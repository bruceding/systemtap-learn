## 打印数据

我们再来看下之前的 hello world 例子

```
probe begin
{
  printf ("hello world\n")
  exit ()
}
```

上面定义了 begin 事件，在脚本运行时，开始执行，事件触发后，使用 printf 打印 `hello world` , 随后执行 exit 函数退出脚本。

SystemTap 脚本会一直运行直到遇到 exit 函数，如果手动退出，可以执行 `Ctrl+C` 。 

printf 与 C 语言的 printf 函数类似，不仅可以打印数据，也可以格式化打印数据。格式如下

```
printf ("format string\n", arguments)
```

SystemTap 提供了一系列函数获取上下文信息供 printf 打印，包括：

* tid() 当前线程的id
* pid() 当前进程的id
* uid() 当前用户的id
* cpu() 当前 CPU 号
* execname() 当前进程的名称
* gettimeofday_s() 当前时间戳
* ctime() 把当前的时间戳转化成时间
* pp() 当前执行事件的描述
* ppfunc() 事件监听的函数名称
* name 系统函数调用的名称
* target() 如果指定脚本运行参数，进程id 或者命令，在脚本中可以使用 target() 引用这个标识。示例如下
```
probe syscall.* {
  if (pid() == target())
    printf("%s\n", name)
}
```
运行此事件时，当前的进程id 与传入的参数一致才会进行打印。用形式 `stap script -x process id or stap script -c command` 运行脚本。
