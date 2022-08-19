# Tools Used in 6.S081

对于后续实验，需要几个不同工具的 RISC-V 版本：QEMU 5.1+、GDB 8.3+、GCC 和 Binutils。

## Installing

### Installing on Windows

首先确保您已安装适用于 [Linux 的 Windows 子系统](https://docs.microsoft.com/en-us/windows/wsl/install)。然后添加 [Ubuntu on Windows - Microsoft Store Apps](https://apps.microsoft.com/store/detail/ubuntu-on-windows/9NBLGGH4MSV6?hl=en-us&gl=US)。之后，您应该能够启动 Ubuntu 并与机器进行交互。为了安装所需的所有软件，使用以下指令：

```shell
$ sudo apt-get update && sudo apt-get upgrade
$ sudo apt-get install git build-essential gdb-multiarch qemu-system-misc gcc-riscv64-linux-gnu binutils-riscv64-linux-gnu
```

在 Windows 中，您可以访问"\\\\wsl\$\\"目录下的所有 WSL 文件。例如，Ubuntu 20.04 安装的主目录应位于"\\\\wsl\$\Ubuntu-20.04\home\\\<username>\\"。

### Installing on macOS

首先，安装开发者工具：

```
$ xcode-select --install
```

接下来，安装 [Homebrew](https://brew.sh/)，一个 macOS 的包管理器：

```
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

接下来，安装 [RISC-V 编译器工具链](https://github.com/riscv/homebrew-riscv)：

```
$ brew tap riscv/riscv
$ brew install riscv-tools
```

brew 公式可能不会链接到 `/usr/local`。您需要更新 shell 的 rc 文件（例如 [~/.bashrc](https://www.gnu.org/software/bash/manual/html_node/Bash-Startup-Files.html)）以将适当的目录添加到  [$PATH](http://www.linfo.org/path_env_var.html)。

```
PATH=$PATH:/usr/local/opt/riscv-gnu-toolchain/bin
```

最后，安装 QEMU：

```
brew install qemu
```

### Debian or Ubuntu

```
sudo apt-get install git build-essential gdb-multiarch qemu-system-misc gcc-riscv64-linux-gnu binutils-riscv64-linux-gnu 
```

### Arch Linux

```
sudo pacman -S riscv64-linux-gnu-binutils riscv64-linux-gnu-gcc riscv64-linux-gnu-gdb qemu-arch-extra
```

### Running a Linux VM

如果列出的其他选项不起作用，您还可以尝试使用上面列出的其他操作系统之一运行虚拟机。借助平台虚拟化，Linux 可以与您的正常计算环境一起运行。安装 Linux 虚拟机是一个两步过程。首先，您下载虚拟化平台。

[**VirtualBox**](https://www.virtualbox.org/)（Mac、Linux、Windows 免费）— [Download page](https://www.virtualbox.org/wiki/Downloads)

[VMware Player](http://www.vmware.com/products/player/)（Linux 和 Windows 免费，需要注册） 

[VMware Fusion](http://www.vmware.com/products/fusion/)（可从 IS&T 免费下载）。

VirtualBox 速度稍慢，灵活性较差，但免费！

安装虚拟化平台后，下载您选择的 Linux 发行版的引导磁盘映像。

[Ubuntu Desktop](http://www.ubuntu.com/download/desktop) 是一种选择。

这将下载一个名为 ubuntu-20.04.3-desktop-amd64.iso 的文件。启动您的虚拟化平台并创建一个新的（64 位）虚拟机。使用下载的 Ubuntu 镜像作为启动盘；该过程在虚拟机之间有所不同，但应该不会太难。

### Athena

我们不推荐它，因为它可能有点不方便，但您可以使用 athena.dialup.mit.edu 工作在实验中。如果您使用运行 Linux 的 MIT Athena 机器，那么所有这些工具都位于 6.828 locker。

ssh 进入其中一台 Athena 拨号机器并添加工具：

```shell
$ ssh {your kerberos}@athena.dialup.mit.edu
$ add -f 6.828
```

## Testing your Installation

首先要將xv6的源碼下載下來，然後切換到xv6的文件夾下。

然後测试安装，编译并运行 xv6（退出 `qemu`，键入 `Ctrl-a x`）：

```
# in the xv6 directory
$ make qemu
# ... lots of output ...
init: starting sh
$
```

如果不起作用，您可以仔细检查各个组件。其中包括 QEMU：

```shell
$ qemu-system-riscv64 --version
QEMU emulator version 5.1.0
```

以及至少一个 RISC-V 版本的 GCC：

```shell
$ riscv64-linux-gnu-gcc --version
riscv64-linux-gnu-gcc (Debian 10.3.0-8) 10.3.0
...
$ riscv64-unknown-elf-gcc --version
riscv64-unknown-elf-gcc (GCC) 10.1.0
...
$ riscv64-unknown-linux-gnu-gcc --version
riscv64-unknown-linux-gnu-gcc (GCC) 10.1.0
...
```

## Troubleshooting

### 1 wsl --install 无法解析服务器的名称或地址

### 原因

网络原因

### 解决

#### 方法1

使用VPN

方法2

[(75条消息) WSL: Could not resolve hostname Temporary failure（无法解析域名）_小白小郑的博客-CSDN博客_wsl 域名解析](https://blog.csdn.net/Zhengyangxinn/article/details/123788799)

## Reference

1. [6.S081 / Fall 2021 (mit.edu)](https://pdos.csail.mit.edu/6.S081/2021/tools.html)