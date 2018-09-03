## 安装

要安装 SystemTap ，内核需要安装 `-devel`, `-debuginfo`, `-debuginfo-common` 这几个包。如果系统有多个内核版本的话，针对每个内核都要安装以上三个包。 

### linux

```
 uname -r
```

确定系统内核版本。

```
 rpm -qa | grep  kernel
```

确定系统是否安装了以上三个包，如果没有安装则继续，如果有安装，确定与系统内核的版本是否一直，如果不一致，可以通过 `yum erase` 卸载。

首先安装 `-devel`, `-debuginfo`, `-debuginfo-common` 这三个包，以 centos 系统的安装举例。

contos 6 版本可以通过 [http://debuginfo.centos.org/6/x86_64/](http://debuginfo.centos.org/6/x86_64/) 下载。
contos 7 版本可以通过 [http://debuginfo.centos.org/7/x86_64/](http://debuginfo.centos.org/7/x86_64/) 下载。

下载完成后，通过 rpm 安装
```
 # sudo rpm -ivh kernel-debuginfo-*
   Preparing...                ########################################### [100%]
      1:kernel-debuginfo-common########################################### [ 50%]
      2:kernel-debuginfo       ########################################### [100%]
 # sudo rpm -ivh kernel-devel-*
   Preparing...                ########################################### [100%]
      1:kernel-devel           ########################################### [100%]

```

安装 systemtap 

```
 #  sudo yum install systemtap
```

安装完成后，运行

```
 # sudo stap -v -e 'probe begin {print("hello world\n");exit()}'
```
看到输出 hello world， 至此安装成功。
