## Probe Point

本文详细介绍下 probe point，探测点是如何定义的。下面是一些合法的 probe point 定义

```
kernel.function("foo")
kernel.function("foo").return
module{"ext3"}.function("ext3_*")
kernel.function("no_such_function") ?
syscall.*
end
timer.ms(5000)
```

之前提过， probe 广义上分为同步事件和异步事件。像 timer， end 没有固定的探测点相关，是属于异步事件。如果使用通配符或者别名的话，每个探测点可能会匹配到多个代码位置。一个 probe 可以同时定义多个探测点，可以使用 `,` 分隔。 

使用 `*` 可以进行通配符的匹配，会匹配到多个探测点。

```
 # 匹配 syscall 下面的所有函数
 kernel.syscall.*

 # 匹配 sys_ 开头的内核函数
 kernel.function("sys_*)
```

probe point 后面跟着 `?` 字符，说明这个探测点是可选的，如果这个探测点不存在的话，也不会报错。

```
 kernel.function("no_such_function") ?
```

### 探测点类型

探测点可以通过模块，源文件，行数，函数名称以及这些的组合定位出具体的位置。

```
kernel.function(PATTERN)
kernel.function(PATTERN).call
kernel.function(PATTERN).return
kernel.function(PATTERN).return.maxactive(VALUE)
kernel.function(PATTERN).inline
kernel.function(PATTERN).label(LPATTERN)
module(MPATTERN).function(PATTERN)
module(MPATTERN).function(PATTERN).call
module(MPATTERN).function(PATTERN).return.maxactive(VALUE)
module(MPATTERN).function(PATTERN).inline
kernel.statement(PATTERN)
kernel.statement(ADDRESS).absolute
module(MPATTERN).statement(PATTERN)
```

`.function` 会把探测点放置在函数的开头，这样函数的参数可以作为上下文变量获取到。

`.return` 会把探测点放在函数的返回的时刻，返回的值标识成 `$return`, 作为一个上下文变量。函数的入参也可以获取到，但是可能被探测的函数修改过。后面加上 `.maxactive` 限定同时可跟踪的实例数。一般不会用到，默认值为 KRETACTIVE，如果发现跳过的 probe 数量增多，可以使用 `.maxactive` 把值调大。

`.inline` 放到 `.function` 后面，会进行过滤值包含内联函数，`.call` 修饰符会选择相反的子集。内联函数没有可用的返回点，所以 `.return` 不支持 `.inline` 的 probe 上。

`.statement` 放到更精确的位置上，暴露那些可见的本地变量。

PATTERN 主要由下面的三部分组成：
 1. 函数名称，可用使用通配符匹配多个名称。
 2. 这部分是可选的，后面跟着 `@` 字符。后面是源文件路径，使用相对或者绝对路径都可以。如果是相对路径，是相对于 linux 源码路径的。
 3. 这部分也是可选的，存在文件名称的情况下，这部分才会存在。通过 `:` 或者 `+` 标识出了源文件所在的行号。`:` 后面的数字是绝对行号，`+` 是相对于函数开头的行数。`:*` 匹配源文件中的所有的行。`:x-y` 表示一个范围，从行数 x 到 行数 y 之间的范围。 
