## Probe

### 定义

来看下 probe 的正式语法定义

```
 probe PROBEPOINT [, PROBEPOINT] { [STMT ...] }
```

probe points(探测点) 指的就是所定义的事件，当 probe points 事件发生时，SMTM 语句就会执行。 有大量预先定义好的 probe points, 而且 tapset 脚本以别名的形式的也定义了很多。可以使用 `stap -l` 列出可用的探测点。

### 别名

定义别名的形式如下：

```
 probe <alias> = <probepoint> { <prologue_stmts> }
 probe <alias> += <probepoint> { <epilogue_stmts> }
```

可以用别名的形式定义新的探测点。别名的定义很类似 probe 的定义，但是别名并不激活事件，也就是说不主动运行，只是现存的 probe 的一个别名。新的 probe 别名可以指向一个或者多个已存在的 probe 别名。多个别名也可以指向同一个探测点。

```
 probe socket.sendmsg = kernel.function ("sock_sendmsg") { ... }
 probe socket.do_write = kernel.function ("do_sock_write") { ... }
 probe socket.send = socket.sendmsg, socket.do_write { ... }
```

有两种类型的我别名，序言样式和结尾样式，分别由 `=` 和 `+=` 标识。

序言样式定义的别名，里面的语句块会隐式的加到别名定义的事件之前。 来看个例子

```
probe syscall.read = kernel.function("sys_read") {
    fildes = $fd
}

probe syscall.read {
    printf("reading fd=%d\n", fildes)
}
```

使用序言样式定义了别名 syscall.read , 里面对变量进行了赋值。在使用 syscall.read 定义事时 ，可以直接使用别名定义中的变量。在实际的加载中，优先加载序言样式中的语句块，这样在事件定义的 handler 中，可以直接使用这些变量。

结尾样式的语句会隐式地加到 probe 定义的尾部。结尾样式的语句里，基本上不会定义变量，因为别的语句无法使用，但是可以对已定义好的变量做操作。看下面的例子：

```
probe syscall.read += kernel.function("sys_read") {
    if (traceme) println ("tracing me")
}
```



