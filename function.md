## 函数

函数的一般格式为：

```
 function <name>[:<type>] ( <arg1>[:<type>], ... ) { <stmts> }
```

函数可以接受任意多个参数，但返回值只有一个。函数中的变量一般是数字或者字符串。

```
function thisfn (arg1, arg2) {
    return arg1 + arg2
}
```

类型一般不需要显式的声明，在脚本翻译时会自动推断。当然，可以显式的声明函数的返回类型及函数类型。举例如下

```
function thatfn:string(arg1:long, arg2) {
    return sprintf("%d%s", arg1, arg2)
}
```

函数的返回类型定义为 string 类型。参数 arg1 声明为 long 类型， arg2 会自动推断出是 string 类型。

函数可以相互调用，也可以递归地调用自己，但是调用层次受资源的限制。
