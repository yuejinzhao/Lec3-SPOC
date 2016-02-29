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

1. 通过调试lab1_ex1了解Linux应用的系统调用执行过程。
- strace可以跟踪到一个进程产生的系统调用,包括参数，返回值，执行消耗的时间
```
	gcc -o lab1-ex1.exe lab1-ex1.c
	strace -c ./lab1-ex1.exe
```

####3.5 ucore 系统调用分析
