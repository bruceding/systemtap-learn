## 条件编译

SystemTap 脚本解析时有预处理阶段，此阶段支持类似三元运算符的条件解析。通用的形式如下

```
 %( CONDITION %? TRUE-TOKENS %)
 %( CONDITION %? TRUE-TOKENS %: FALSE-TOKENS %)
```
通用的语法形式如下

```
 %( <condition> %? <code> [ %: <code> ] %)
```

举个例子说明下

```
%( $# > 1 %?
probe timer.sec($2) {
    %:
probe timer.sec(5) {
    %)
        exit();
}
```

条件表达式是 `$# > 1`, 当命令行参数大于1时，执行 `probe timer.sec($2)` 把第二个参数当成 timer 的秒数，否则使用默认值5。

`@defined` 可以用来测试 targe 变量是否可用。

```
 probe foo { if (@defined($bar)) log ("$bar is available here") }
```
