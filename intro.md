## 简介
systemtap 是一个 linux 跟踪器，主要用于调试内核.

systemtap 定义了一些列事件，并且有相应的 handler 处理。当事件发生时，linux 内核会运行相应的 handler 程序。事件包含进入或者退出某个函数，定时器到期, systemtap 脚本的开始、结束等。

handler 程序主要工作是从事件上下文中解析数据(入参或者本地变量等)，存储到内部结构或者打印结果。

systemtap 会把脚本翻译成 C 语言，运行 C 编译器生成内核模块。当模块加载时，会把事件链接到内核中，当事件发生时，handler 进行处理。当脚本结束，内核模块也会被移除。stap 是 systemtap 的命令行工具。来看一个简单的例子。

```
# cat hello-world.stp
probe begin
{
    print("hello world\n")
    exit()
}
# stap hello-world.stp
hello world
```
begin 是执行 systemtap 生命周期开始的事件，只能定义一次，print 打印数据，exit 退出脚本执行。

