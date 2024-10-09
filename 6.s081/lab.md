# lab

## lab2

### Using gdb

首先在一个终端中输入: `make qemu-gdb` (打开qemu的gdb调试模式)

然后打开另一个终端,输入 `gdb-multiarch -x .gdbinit` (运行`.gdbinit`中的命令,设置`arch`架构为`riscv64`,并打开远程调试功能)

````bash

(gdb) b syscall              #在函数syscall处打上断点
Breakpoint 1 at 0x800020a2: file kernel/syscall.c, line 133.
(gdb) c                      #运行到断点处
Continuing.

Thread 1 hit Breakpoint 1, syscall () at kernel/syscall.c:133
133     {
(gdb) layout src             #打开源代码窗口
(gdb) backtrace              #打印堆栈回溯
````

![image-20241003173028673](../../../AppData/Roaming/Typora/typora-user-images/image-20241003173028673.png)

第一个问题: ```   Looking at the backtrace output, which function called `syscall`?  ```

由堆栈回溯得出,`usertrap()`函数调用了`syscall()`;

### 0x2

```bash
(gdb) n
(gdb) n
(gdb) p /x *p
```

![image-20241003175359608](../../../AppData/Roaming/Typora/typora-user-images/image-20241003175359608.png)

第二个问题: `What is the value of p->trapframe->a7 and what does that value represent? (Hint: look user/initcode.S, the first user program xv6 starts.)`

![image-20241003180312842](../../../AppData/Roaming/Typora/typora-user-images/image-20241003180312842.png)

a7的值为7,由`initcode.S`中代码可知a7存储的是 `SYS_exec` , 在`syscall.h`文件中可知`SYS_exec`系统调用编号为7

### 0x3

```bash
(gdb)p /x $sstatus      # p 命令用于打印表达式的值; /x 是格式说明符,表示以十六进制格式显示结果; $sstatus 是寄存器                           的名称
```

![image-20241003181516743](../../../AppData/Roaming/Typora/typora-user-images/image-20241003181516743.png)

![image-20241003182333279](../../../AppData/Roaming/Typora/typora-user-images/image-20241003182333279.png)

当SPP位为0时来自用户模式,为1时不是.

问题3:`What was the previous mode that the CPU was in?`

由上面的十六进制展开为二进制代码后,可知第八位为0,所以之前的模式是用户模式.

### 0x4

800020b6:	00002683          	lw	a3,0(zero) # 0 <_entry-0x80000000>

