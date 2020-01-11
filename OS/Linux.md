> 记录一些Linux相关的知识和注意事项

**!!!!!! 千万别删错文件，没得恢复！！！！**


```
ssh user@ip #用user的身份登陆到地址为ip的机器上（注意用户权限问题）
```

#### 一些常用的Linux命令

##### 进程相关
```
ps aux | grep "info" #筛选进程并显示信息
kill -9 pid #杀死进程
nohup cmd & #后台执行cmd并忽略挂断信号，输出导向为nohup.out
scp username@ip:file_abstract_path local_path #将远程的file拷贝到本地
```

##### 文件读写
```
ls #查看当前路径下的文件，不同颜色有不同意义
wc -l $file #查看文件行数
head/tail -20 $file #查看文件的前/后20行
cat $file #查看文件内容
vi $file #进入文件编辑模式
```

##### Git用法
```
git clone repo_address #将远程仓库拷贝到本地
git pull #拉取远程仓库，适用于本地与远程仓库的同步
git add . #在仓库根目录下执行，将全部文件添加到提交区（可以把. 换成具体的文件）
git commit -m "message" #提交
git push #把当前待提交区的内容推到远程仓库上去
```


##### CentOS上Tomcat启动慢
```
yum install rng-tools # 安装rngd服务（熵服务，增大熵池）
systemctl start rngd  # 启动服务
```

# Ubuntu 专用

```
sudo apt-get remove --purge softwarename #彻底卸载软件
```