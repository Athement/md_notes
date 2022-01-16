环境：Linux vultr.guest 4.15.0-132-generic #136-Ubuntu SMP Tue Jan 12 14:58:42 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux

## [安装java](https://developer.aliyun.com/article/704959)

**查找合适的openjdk版本:**

```
apt-cache search openjdk
```

**安装**

```
sudo apt-get install openjdk-8-jdk
```

使用apt-get安装时，错误提示`E: Failed to fetch http://...`

原因:apt源未更新,使用命令`sudo apt-get update`更新源后安装成功

 **配置环境变量**

```
vim ~/.bashrc
```

在最后一行加:

```
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```

## 安装Jenkins

[**安装**](https://pkg.jenkins.io/debian/)

*使用官方的安装指南.*

- 将存储库密钥添加到系统

```
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
```

- 将Debian包存储库地址附加到服务器的sources.list

```
sudo sh -c 'echo deb https://pkg.jenkins.io/debian binary/  > /etc/apt/sources.list'
```

- 安装jenkins

```
sudo apt-get update
sudo apt-get install jenkins
```

#### 配置并启动Jenkins

- 查看端口是否占用

```bash
lsof -i:8080
#lsof 即lists openfiles
```

- 启动Jenkins

```bash
systemctl start jenkins
```

- 访问Jenkins的8080端口

