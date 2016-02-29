####2-1 SPOC
@(操作系统)[SPOC]
###启动、中断、异常和系统调用-思考题
####3.4 Linux系统调用分析
1. 通过分析lab1_ex0了解Linux应用的系统调用编写和含义。
- objdump 以一种可阅读的格式让你更多地了解二进制文件可能带有的附加信息。objdump -S *.exe
- nm 用来列出目标文件的符号清单 nm -o *.exe
- file 确定文件类型 file *.exe
	> lab1-ex0.exe: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=60a0c242041e3b120fa228089726025963011671, not stripped

- 系统调用指程序向内核请求需要更高权限运行的服务．系统调用机制为用户进程提供一个获得操作系统服务的统一接口层, Linux 的系统调用通过 int 0x80 实现

- 此程序使用了SYS_write系统调用，使用int 0x80命令实现系统调用

1. 通过调试lab1_ex1了解Linux应用的系统调用执行过程。
- strace可以跟踪到一个进程产生的系统调用,包括调用次数，出错次数，执行消耗的时间
```
	gcc -o lab1-ex1.exe lab1-ex1.c
	strace -C ./lab1-ex1.exe
```
执行strace -C之后, 输出了程序运行过程中的系统调用的相关信息。

```
execve("./lab1-ex1.exe", ["./lab1-ex1.exe"], [/* 62 vars */]) = 0
brk(0)                                  = 0x1c81000
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f1817fb4000
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
open("/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=155959, ...}) = 0
mmap(NULL, 155959, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f1817f8d000
close(3)                                = 0
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0`\v\2\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0755, st_size=1869392, ...}) = 0
mmap(NULL, 3972864, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f18179c9000
mprotect(0x7f1817b89000, 2097152, PROT_NONE) = 0
mmap(0x7f1817d89000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1c0000) = 0x7f1817d89000
mmap(0x7f1817d8f000, 16128, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f1817d8f000
close(3)                                = 0
mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f1817f8c000
mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f1817f8b000
mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f1817f8a000
arch_prctl(ARCH_SET_FS, 0x7f1817f8b700) = 0
mprotect(0x7f1817d89000, 16384, PROT_READ) = 0
mprotect(0x600000, 4096, PROT_READ)     = 0
mprotect(0x7f1817fb6000, 4096, PROT_READ) = 0
munmap(0x7f1817f8d000, 155959)          = 0
fstat(1, {st_mode=S_IFCHR|0620, st_rdev=makedev(136, 9), ...}) = 0
mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f1817fb3000
write(1, "hello world\n", 12hello world
)           = 12
exit_group(12)                          = ?
+++ exited with 12 +++
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
  0.00    0.000000           0         1           read
  0.00    0.000000           0         1           write
  0.00    0.000000           0         2           open
  0.00    0.000000           0         2           close
  0.00    0.000000           0         3           fstat
  0.00    0.000000           0         9           mmap
  0.00    0.000000           0         4           mprotect
  0.00    0.000000           0         1           munmap
  0.00    0.000000           0         1           brk
  0.00    0.000000           0         3         3 access
  0.00    0.000000           0         1           execve
  0.00    0.000000           0         1           arch_prctl
------ ----------- ----------- --------- --------- -------
100.00    0.000000                    29         3 total
```
可以看到strace可用来跟踪系统调用的情况信息

- 包含的系统调用有
	- write 输出写
	- read 读取文件
	- evecve 运行程序
	- mprotect 调用设置内存段的权限

####3.5 ucore 系统调用分析
