### SystemTap 脚本

SystemTap 脚本由两部分组成，事件和处理器。一个事件和相应的处理器组成为一个探针(probe)。脚本可以包含多个探针(probes)。

脚本文件使用后缀 .stp ，probe 格式定义如下
```
 probe event {statements}
```

probe 可以对应多个事件，每个事件以 `,` 分隔。存在多个事件的情况下，只要有一个事件发生，则会执行相应的 handler 处理器程序。

SystemTap 脚本也可以定义函数，函数可以在多个 probe 中使用，格式如下
```
 function function_name(arguments) {statements}
 probe event {function_name(arguments)}
```

### 事件(event)

广义上讲，事件分为两类，同步事件和异步事件。

#### 同步事件

当程序执行到特定指令的时候，同步事件发生，同步事件可以获取到相关的上下文信息。同步事件包括下面几类：

* syscall.system_call

进行调用系统函数 `system_call` 时发生，如果希望在函数退出时发生，在事件后面加 `.return` 即可。比如在系统函数 `close` 调用或者退出时执行相应的事件，可以分别这样指定 `syscall.close`, `syscall.close.return` 。 

* kernel.function("function")

调用内核函数 `function` 发生。比如，`kernel.function("sys_open")` 代表系统任何线程调用函数 `sys_open` 时发生的事件。如果跟踪退出的事件，可以这样表示 `kernel.function("sys_open").return` 。

可以用通配符 `*` 定义多个事件。内核源码文件中的函数都可以来跟踪。看下下面的例子：

```
 probe kernel.function("*@net/socket.c") { }
 probe kernel.function("*@net/socket.c").return { }
```
第一个 probe 指定了在内核文件 `net/socket.c` 中所有函数调用时发生的事件。第二个表示所有函数退出时发生的事件。

全部的内核函数可以这样列出
```
 sudo stap -L 'kernel.function("*")'
```

* module("module").function("function")

也可以定义 module 中的函数，可以参考如下

```
 probe module("ext3").function("*") { }
 probe module("ext3").function("*").return { }
```
内核的 modules 可以在这里 `/lib/modules/kernel_version` 找到，`kernel_version` 指的是内核的版本号，modules 使用的文件后缀 `.ko` 。

#### 异步事件

