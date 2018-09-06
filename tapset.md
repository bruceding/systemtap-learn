## Tapsets

Tapsets 类似于函数库，里面预先提供了很多 probe 及很多有用的函数，这些脚本安装在特定的目录下，SystemTap 脚本可以直接拿来使用。 你可以实现 tapset，供 SystemTap 脚本重用，或者提供给其他人使用。

SystemTap 脚本运行时，SystemTap 会根据 tapset 库 检查 probe 事件和 handler 程序。在翻译成 C 语言之前，SystemTap 会加载使用 tapset 相关的 probe 和函数。

Tapsets 也使用文件扩展名 `.stp`, 但不能直接的运行 。公共的 Tapsets 默认安装在 /usr/share/systemtap/tapset/ 下面。 Tapset 提供了一个抽象层，提供了大量的事件别名，方便用户定义 probe 事件和函数。这样用户也不用记住特定的内核函数，只要记住别名就行了，这样也方便脚本的移植。

如果 tapset 没有安装在默认路径，或者需要自定义的 tapset， 可以使用 `-I DIR` 选项指定搜索路径。



