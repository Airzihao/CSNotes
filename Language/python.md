### anaconda
装包可以去anacond.org查一下
### 获取路径
```
import  os
print os.getcwd()  #获取当前工作目录路径
print os.path.abspath('.')  #获取当前工作目录路径
print os.path.abspath('test.txt') #获取当前目录文件下的工作目录路径
print os.path.abspath('..') #获取当前工作的父目录 ！注意是父目录路径
print os.path.abspath(os.curdir) #获取当前工作目录路径
os.path.exists(test_file.txt) #判断路径是否存在
```
上边记录的都是执行命令的目录，而不是脚本所在位置。若需获取脚本所在位置，则命令如下：
```
os.path.abspath(__file__)
```
```__file__``` 是内部变量意指当前文件。


### 文件相关操作
```
os.mknod("test.txt")    #创建空文件
```

### 用Python搭建简单的HTTP服务器
```nohup python -m SimpleHTTPServer 8000 &```

在命令的开头加一个nohup，忽略所有的挂断信号，如果当前bash关闭，则当前进程会挂载到init进程下，成为其子进程，这样即使退出当前用户，其8000端口也可以使用。


