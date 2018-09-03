## 变量

变量分为本地变量和全局变量，本地变量只能在当前的 handler 中使用，在多个 handler 中使用的变量称为全局变量，定义时加上 global 标识。

变量定义时不需要指定类型，SystemTap 会自动识别出类型。

```
global count_jiffies, count_ms
probe timer.jiffies(100) { count_jiffies ++ }
probe timer.ms(100) { count_ms ++ }
probe timer.ms(12345)
{
  hz=(1000*count_jiffies) / count_ms
  printf ("jiffies:ms ratio %d:%d => CONFIG_HZ=%d\n",
    count_jiffies, count_ms, hz)
  exit ()
}
```

hz 是本地变量，类型为 integer , SystemTap 会自动识别，在打印时，指定了 %d 格式。count_jiffies, count_ms 是全局变量，在各自的 probe 中自增统计，在 timer.ms 中汇总输出。

### Target Variables 

在执行 probe 时，可以访问被探测代码的上下文变量，称之为 target variable 。包括函数的入参，返回值，局部变量，设置包括全局变量等。

之前提过，可以使用 `-L` 选项列出目标函数的 target variable 。列出内核函数 vfs_read 的变量

```
 sudo stap -L 'kernel.function("vfs_read")'
```

输出如下： 

```
 kernel.function("vfs_read@fs/read_write.c:339") $file:struct file* $buf:char* $count:size_t $pos:loff_t*
```

每个 target variable 名称前都加了 `$`, `:` 之后是类型。vfs_read 函数包含了以下变量：$file 指向了描述文件的结构，$buf 指向用户空间的内存地址，用于存储读取的数据，$count 表示读取数据的大小，$pos 表示从文件的偏移量进行读取。可以使用 `->` 访问 struct 的具体的字段。




