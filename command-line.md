## 命令行参数

SystemTap 也支持命令行参数。在命令行的数字标记前加上 `$` 或者 `@` 就可以。如果命令行参数是数字类型，使用 `$`, 字符串的话，使用 `@`。

> cat command-line.stp  
```
> probe kernel.function(@1) {   
>    printf("%s\n", $$parms$);  
> }    
> probe timer.ms($2) {     
>    exit();     
> }     
```

可以这样调用

```
 sudo stap -v command-line.stp vfs_read 10
```

第一个参数是函数名称，使用 string 解析，第二个参数为时间，解析为 integer 类型。

