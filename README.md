# PyDNSServer
一个Python实现的简单的可配置的DNS服务程序。

支持域名完全匹配（相等）或正则匹配（re.match,re.sub）。比如***www.baidu.com***对应***www.baidu.com***，***\S+\.baidu\.com***对应任意的baidu.com二级域名，比如***img.baidu.com***、***cloud.baidu.com***等

支持指定ip、允许、禁止策略：
* 指定ip: 直接返回该指定的ip地址
* 支持正则替换: 根据正则替换生成ip地址
* 允许: 返回该域名对应的正真ip地址
* 禁止: 不对该域名进行解析，即返回解析失败


dnf.cfg文件内容如下：

```sh
# 一行一个配置
# 主机名（域名）支持正在方式配置
# 策略支持ip地址或者允许（allow）或者禁止（deny）
# 对于同时符合多个正则的域名，优先级从上到下

# 格式:
#  主机名或正则模式   ip或allow或deny
# 

# more info : https://github.com/sintrb/PyDNSServer

#  hostname|pattern   ip|allow|deny

# allow www.baidu.com
www.baidu.com allow

# deny xxx.360.com
\S+\.360\.com deny

# set www.qq.com with ip 192.168.0.100
www.qq.com 192.168.0.100

# set 10.1.1.1.xxx.com with ip 10.1.1.1
(\d+\.\d+\.\d+\.\d+)\..* \1 

# deny other hostname
.* deny
```

使用
首先，运行服务(使用DNS的53端口，需要管理员权限)：
> \# python RunMain.py

直接在本机测试：
> \> nslookup www.baidu.com 127.0.0.1

> \> nslookup www.360.com 127.0.0.1

> \> nslookup 10.2.0.215.xxx.com 127.0.0.1

### 正则替换应用场景：

在阿里云内选择“将子域名指定其他DNS服务器解析”，将 *.ip.haikor.top解析到 nds.haikor.top(本服务)

这样在任何地方都能够通过 10.2.0.215.ip.haikor.top 的方式访问 10.2.0.215。

假设同一个开发电脑里配置了N个web项目，只要按照规范配置IIS或者Nginx绑定不同的域名，就可以通过域名内指定ip及项目名的方式访问不同电脑的不同项目，如：
* 10.2.0.215.proj1.ip.haikor.top
*  10.2.0.215.proj2.ip.haikor.top
* 10.2.0.200.proj1.ip.haikor.top
* 10.2.0.200.proj2.ip.haikor.top
