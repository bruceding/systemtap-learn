## 用户态 Probe

SystemTap 也支持用户态程序的探测。当然需要 linux 内核的支持。内核版本在3.5之后的， 执行 

```
 grep CONFIG_UPROBES /boot/config-`uname -r`
```

如果返回 `CONFIG_UPROBES=y`, 说明内核支持。3.5版本之前的，执行

```
grep CONFIG_UTRACE /boot/config-`uname -r`
```

如果返回 `CONFIG_UTRACE=y`, 说明内核也支持。

### 用户态事件

用户的事件定义都是以 `process` 开始的。如果是运行的进程，可以指定进程号, 也可以指定进程的具体路径。支持下列事件

* process("PATH").function("function")

进程的函数入口，类似于  kernel.function("function")。函数名称可以使用通配符，也可以添加 `.return` 后缀。也可以是用 `stap -L` 列出探测点。 

* process("PATH").statement("statement")

类似于 kernel.statement("statement")。

* process("PATH").mark("marker")

* process.begin

当用户进程创建发生

* process.thread.begin

当用户线程创建发生

* process.end

当用户进程消亡发生

* process.thread.end

当用户线程销毁发生

* process.syscall

当用户进程执行了系统调用发生。从 $syscall 变量中可以获得系统调用数字。前6个变量可以从 $arg1 到 $arg6 获取到。

### 用户态 Target 变量

下面一些列方法可以用来访问用户空间数据。第一个参数都是指向数据的指针。

* user_char(address)

把指针指向的地址解析成 char 类型

* user_short(address)

把指针指向的地址解析成 short integer 类型

* user_int(address)

把指针指向的地址解析成 int 类型

* user_long(address)

把指针指向的地址解析成 long integer 类型

* user_string(address)

把指针指向的地址解析成 string 类型

* user_string_n(address, n)

把指针指向的地址解析成 string 类型, 并限制到 n 字节
