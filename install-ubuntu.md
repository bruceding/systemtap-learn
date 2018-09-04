## ubuntu

简单介绍下 ubuntu 安装过程。使用 `uname -r` 确定好内核版本后，从 [http://ddebs.ubuntu.com/pool/main/l/linux/](http://ddebs.ubuntu.com/pool/main/l/linux/) 下载相应的版本，假设下载的版本为 linux-image-unsigned-4.15.0-32-generic-dbgsym_4.15.0-32.35_amd64.ddeb 。使用 `sudo dpkg -i linux-image-unsigned-4.15.0-32-generic-dbgsym_4.15.0-32.35_amd64.ddeb` 安装。 成功后，运行 `sudo apt-get install systemtap` 安装 systemtap 。
