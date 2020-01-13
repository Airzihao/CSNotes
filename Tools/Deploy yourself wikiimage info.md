## 概述：
- task：搭建wiki镜像
#### Requirement: 
1. mediawiki (基于php的开源软件，用于搭建wiki站点)
2. wiki转储（wikidumps的数据）

#### 数据集：
wiki转储的官方网站：
https://dumps.wikimedia.org/

建议通过镜像网站下载（不限速）：https://ftp.acc.umu.se/mirror/wikimedia.org/

**wiki转储一般半个月发布一次，注意下载的时候看一下当前最新版本是否已转储完成。**

**另：转储上有提供每次更新的增量文件，值得研究一下。**

#### 关于语言版本：
英文版wikipedia的转储数据：enwiki

中文版Wikipedia的转储：zhwiki（文件大小只有enwiki的十分之一左右）

#### 有用的文件：
```
//记录行号
enwiki-20190520-pages-articles-multistream-index.txt.bz2

//主文件
enwiki-20190520-pages-articles-multistream.xml.bz2

//metadata（这个文件是最大的，不知道里边具体有啥）
enwiki-20190520-pages-meta-current.xml.bz2

//另有每次转储的更新文件，可以研究。本次做完整镜像，因此不做那些考虑。
```




## 搭建流程：
http://www.mamicode.com/info-detail-2445563.html
### 1. 安装并启动httpd和MariaDB：
```
yum install httpd mariadb-server mariadb

//修改httpd的配置文件，把DocumentDirectory指向/home/mediawiki（与4中对应即可）

systemctl enable mariadb
systemctl enable httpd
systemctl start httpd
systemctl start mariadb
```

### 2.在MariaDB中建库并配置
```
CREATE USER 'wiki'@'localhost' IDENTIFIED BY '123';
CREATE DATABASE wikidb;
GRANT ALL PRIVILEGES ON wikidb.* TO 'wiki'@'localhost';
FLUSH PRIVILEGES;
show databases;
SHOW GRANTS FOR 'wiki'@'localhost';
```

### 3. 安装php：
**注：部门虚拟机上php版本好像不适用，建议卸载重装**

##### 完整卸载php：
```
//查看版本
rpm -qa|grep php

//将下边的版本号换成rpm -qa得到的版本号（下边指令可能未列全）
//因为存在依赖关系，下边的几条指令需按顺序执行
rpm -e php-mysql-5.1.6-27.el5_5.3 
rpm -e php-pdo-5.1.6-27.el5_5.3 
rpm -e php-xml-5.1.6-27.el5_5.3 
rpm -e php-cli-5.1.6-27.el5_5.3 
rpm -e php-gd-5.1.6-27.el5_5.3 
rpm -e php-common-5.1.6-27.el5_5.3 
// rpm -e掉 rpm -qa 中的全部包
```

##### 安装php：
```
yum install epel-release
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
yum install -y php71w-fpm php71w-opcache php71w-cli php71w-gd php71w-imap php71w-mysqlnd php71w-mbstring php71w-mcrypt php71w-pdo php71w-pecl-apcu php71w-pecl-mongodb php71w-pecl-redis php71w-pgsql php71w-xml php71w-xmlrpc php71w-devel mod_php71w
```

## 4.安装mediawiki：
```
cd /home
wget https://releases.wikimedia.org/mediawiki/1.32/mediawiki-1.32.1.tar.gz
cd /data //最好把mediawiki解压到/data中，空间充足
tar xvf /home/mediawiki-1.32.1.tar.gz
ln -s mediawiki-1.32.1/ /home/mediawiki
chown -R apache:apache /data/mediawiki-1.32.1 
systemctl restart httpd
```

## 5. 配置mediawiki
浏览器打开：http://IP/mediawiki/index.php
下一步即可
1. 数据库选MySQL。
2. 数据库主机填 ip:3306 (端口注意对应)。
3. 数据库名称、用户名、密码按照前边的设置填写，配置工具会检测合法性。
4. 数据库引擎选InnoDB，编码选二进制。
5. 设置全局名称和添加管理员。
6. 会生成一个LocalSetting.php，把这个配置文件放在index.php的同级目录，重启httpd即可。



## 导入数据
### 1. 数据准备
1. 下载 enwiki-20190520-pages-articles-multistream.xml.bz2
2. 解压。**一定要bzip2 -kd xxx.bz2，否则会把原始包删除。**

### 2. 导入
参考链接:
https://www.mediawiki.org/wiki/Manual:Importing_XML_dumps
#### 方法一
shell登陆，进入mediapedia下边的maintaince路径，执行：
```
cd /data/wiki/mediawiki-1.32.1/maintenance/
php importDump.php --conf ../LocalSettings.php /path_to/dumpfile.xml.gz
```
此方法只适用于小批量导入

#### 方法二
使用mwdumper
```
git clone https://phabricator.wikimedia.org/diffusion/MWDU/mwdumper.git
cd mwdumper
mvn compile
mvn package
```
```
  java -jar mwdumper.jar --format=sql:1.25 pages_full.xml.bz2 |
    mysql -u <username> -p <databasename>
```
此方法适用于大量数据导入，但需要先清空库:
```
In SQL: USE wikidb; TRUNCATE TABLE page; TRUNCATE TABLE text; TRUNCATE TABLE revision; TRUNCATE TABLE redirect;
In maintenance directory: php rebuildall.php
```




java -server -classpath /data/wiki/tools/mariadb-java-client-2.4.1-sources.jar:/data/wiki/test/mwdumper-1.25.jar \ org.mediawiki.dumper.Dumper --output=mysql://127.0.0.1/testwiki?user=wiki\&password=wiki \ --format=sql:1.25 /data/wiki/pediaDataset/0520_base/zhwiki-20190520-pages-articles-multistream.xml.bz2

## Trick：


###LocalSetting 

##### debug:
$wgShowExceptionDetails = true;
$wgDevelopmentWarnings = true; error_reporting( -1 ); ini_set( 'display_errors', 1);


##### extensions:
```
wfLoadExtension( 'Scribunto' );
wfLoadExtension('Cite');
wfLoadExtension('ParserFunctions');
$wgScribuntoDefaultEngine = 'luastandalone';
```

##### upgrade:
$wgMultiContentRevisionSchemaMigrationStage=SCHEMA_COMPAT_WRITE_BOTH | SCHEMA_COMPAT_READ_OLD;



