# Lab: Xv6 and Unix utilities

本实验将使您熟悉 xv6 及其系统调用。

## Boot xv6 ([easy](https://pdos.csail.mit.edu/6.S081/2021/labs/guidance.html))

您可以在 Athena 机器或您自己的计算机（建议）上进行这些实验。如果您使用自己的计算机，请查看[lab tools page](./tools)以获取设置提示。

获取lab的 xv6 源代码并查看 util 分支：

```shell
$ git clone git://g.csail.mit.edu/xv6-labs-2021
Cloning into 'xv6-labs-2021'...
...
$ cd xv6-labs-2021
$ git checkout util
Branch 'util' set up to track remote branch 'util' from 'origin'.
Switched to a new branch 'util'
```

xv6-labs-2021 存储库与本书的 xv6-riscv 略有不同；它主要添加一些文件。如果您好奇，请查看 git 日志：

```shell
$ git log
```

您将使用 [Git](http://www.git-scm.com/) 版本控制系统分发這些你需要的文件和后续 lab 作业。上面你切换到了一个分支（git checkout util），其中包含为这个 lab 量身定制的 xv6 版本。要了解有关 Git 的更多信息，请查看 [Git user's manual](http://www.kernel.org/pub/software/scm/git/docs/user-manual.html)，或者你可能会发现 [CS-oriented overview of Git](http://eagain.net/articles/git-for-computer-scientists/) 很有用。Git 允许您跟踪您对代码所做的更改。例如，如果您完成了一项练习，并且想要检查您的进度，您可以通过运行以下命令git来提交您的更改：

```shell
$ git commit -am 'my solution for util lab exercise 1'
Created commit 60d2135: my solution for util lab exercise 1
 1 files changed, 1 insertions(+), 0 deletions(-)
```

您可以使用 `git diff` 命令跟踪您的更改。运行 `git diff` 将显示自上次提交以来对您的代码所做的更改，而 `git diff origin/util` 将显示相对于初始 xv6-labs-2021 代码的更改。在这里，`origin/xv6-labs-2021` 是 git 分支的名称，其中包含您为该类下载的初始代码。

```shell
$ make qemu
riscv64-unknown-elf-gcc    -c -o kernel/entry.o kernel/entry.S
riscv64-unknown-elf-gcc -Wall -Werror -O -fno-omit-frame-pointer -ggdb -DSOL_UTIL -MD -mcmodel=medany -ffreestanding -fno-common -nostdlib -mno-relax -I. -fno-stack-protector -fno-pie -no-pie   -c -o kernel/start.o kernel/start.c
...  
riscv64-unknown-elf-ld -z max-page-size=4096 -N -e main -Ttext 0 -o user/_zombie user/zombie.o user/ulib.o user/usys.o user/printf.o user/umalloc.o
riscv64-unknown-elf-objdump -S user/_zombie > user/zombie.asm
riscv64-unknown-elf-objdump -t user/_zombie | sed '1,/SYMBOL TABLE/d; s/ .* / /; /^$/d' > user/zombie.sym
mkfs/mkfs fs.img README  user/xargstest.sh user/_cat user/_echo user/_forktest user/_grep user/_init user/_kill user/_ln user/_ls user/_mkdir user/_rm user/_sh user/_stressfs user/_usertests user/_grind user/_wc user/_zombie 
nmeta 46 (boot, super, log blocks 30 inode blocks 13, bitmap blocks 1) blocks 954 total 1000
balloc: first 591 blocks have been allocated
balloc: write bitmap block at sector 45
qemu-system-riscv64 -machine virt -bios none -kernel kernel/kernel -m 128M -smp 3 -nographic -drive file=fs.img,if=none,format=raw,id=x0 -device virtio-blk-device,drive=x0,bus=virtio-mmio-bus.0

xv6 kernel is booting

hart 2 starting
hart 1 starting
init: starting sh
```

如果您在提示符下键入 ls，您应该会看到类似于以下内容的输出：

```
$ ls
.              1 1 1024
..             1 1 1024
README         2 2 2059
xargstest.sh   2 3 93
cat            2 4 24256
echo           2 5 23080
forktest       2 6 13272
grep           2 7 27560
init           2 8 23816
kill           2 9 23024
ln             2 10 22880
ls             2 11 26448
mkdir          2 12 23176
rm             2 13 23160
sh             2 14 41976
stressfs       2 15 24016
usertests      2 16 148456
grind          2 17 38144
wc             2 18 25344
zombie         2 19 22408
console        3 20 0
```

这些是 `mkfs` 包含在初始文件系统中的文件；大多数是您可以运行的程序。你只运行了其中一个：`ls`。

xv6 没有 `ps` 命令，但是，如果您键入 `Ctrl-p`，内核将打印有关每个进程的信息。如果您现在尝试，您会看到两行：一行用于 `init`，另一行用于 `sh`。

要退出 qemu，请输入：`Ctrl-a x`。

## Grading and hand-in procedure

您可以运行 `make Grade` 以使用评分程序测试您的解决方案。

lab 代码带有 GNU Make 规则，使提交更容易。将您的最终更改提交到lab后，键入 `make handin` 以提交您的lab。有关如何提交的详细说明，请参见[below](https://pdos.csail.mit.edu/6.S081/2021/labs/util.html#submit)。

## sleep ([easy](https://pdos.csail.mit.edu/6.S081/2021/labs/guidance.html))

> 为 xv6 实现 UNIX 程序 sleep；您的`sleep`应该暂停用户指定的ticks。tick是 xv6 内核定义的时间概念，即来自定时器芯片的两次中断之间的时间。您的解决方案应该在文件 `user/sleep.c` 中。

Some hints:

- 再开始实验之前，请先阅读[xv6 book](https://pdos.csail.mit.edu/6.S081/2021/xv6/book-riscv-rev2.pdf)的Chapter 1。

- 查看 `user/` 中的其他一些程序（例如 `user/echo.c`、`user/grep.c` 和 `user/rm.c`），了解如何获取传递给程序的命令行参数。

- 如果用户忘记传递参数，`sleep` 应该打印一条错误消息。

- 命令行参数作为字符串传递；您可以使用 `atoi` 将其转换为整数（请参阅 `user/ulib.c`）。

- 使用系统调用`sleep`。

- 有关实现系统调用`sleep`（查找 `sys_sleep`）的 xv6 内核代码，请参见 `kernel/sysproc.c`， `user/user.h`为从用户程序调用的`sleep`的 C 定义， `user/usys.S`为`sleep`从用户代码跳转到内核运行的汇编代码。

- 确保 `main` 调用 `exit()` 以退出程序。

- 在 Makefile 中将你的`sleep`程序添加到 `UPROGS`；完成后，`make qemu` 将编译您的程序，您将能够从 xv6 shell 运行它。

- 可以查看 Kernighan 和 Ritchie 的书 The C programming language (second edition) (K&R) 以了解 C。

从 xv6 shell 运行程序：

```shell
$ make qemu
...
init: starting sh
$ sleep 10
(nothing happens for a little while)
$
```

如果您的程序在运行时暂停，则您的解决方案是正确的，如上所示。运行 `make grade` 以查看您是否确实通过了sleep 测试。

请注意，`make grade` 会运行所有测试，包括以下作业的测试。如果您想为一项作业运行成绩测试，请输入：

```shell
$ ./grade-lab-util sleep
```

这将运行匹配“sleep”的等级测试。或者，您可以键入：

```shell
$ make GRADEFLAGS=sleep grade
```

都是一样的效果。

## pingpong ([easy](https://pdos.csail.mit.edu/6.S081/2021/labs/guidance.html))

> 编写一个程序，使用 UNIX 系统调用通过一对管道在两个进程之间“乒乓”一个字节，仅使用一个管道读写。父进程向子进程发送一个字节，子进程应该打印："\<pid>: received ping"，其中 \<pid> 是进程 ID，然后将管道上的字节写回给父进程，并退出；父进程读取子进程发送的字节，打印“\<pid>: received pong”，然后退出。请实现在文件 `user/pingpong.c` 中。

Some hints:

- 使用 `pipe` 创建管道pipe；
- 使用 `fork` 创建子进程；
- 使用 `read` 读取管道，`write` 写入；
- 使用 `getpid` 获取调用者的进程ID——pid；
- 将程序`pingpong`添加到 Makefile 中的 UPROGS。

- xv6 上的用户程序的库函数非常有限。可以在 user/user.h 中看到列表；源代码（系统调用除外）在 user/ulib.c、user/printf.c 和 user/umalloc.c 中。

从 xv6 shell 运行程序：

```
$ make qemu
...
init: starting sh
$ pingpong
4: received ping
3: received pong
$
```

如果您的程序在两个进程之间交换一个字节并产生如上所示的输出，那么您的解决方案是正确的。

## primes ([moderate](https://pdos.csail.mit.edu/6.S081/2021/labs/guidance.html))/([hard](https://pdos.csail.mit.edu/6.S081/2021/labs/guidance.html))

> 使用管道编写一个并发版本的素数筛选。这个想法归功于 Unix 管道的发明者 Doug McIlroy。[这篇文章](http://swtch.com/~rsc/thread/)中的图片很好的解释了其原理。请实现在`user/primes.c`文件中。





## find ([moderate](https://pdos.csail.mit.edu/6.S081/2021/labs/guidance.html))

## xargs ([moderate](https://pdos.csail.mit.edu/6.S081/2021/labs/guidance.html))

## Reference

1. [Lab: Xv6 and Unix utilities (mit.edu)](https://pdos.csail.mit.edu/6.S081/2021/labs/util.html)