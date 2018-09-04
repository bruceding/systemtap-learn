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

如果变量的指针指向的是基本类型，可以通过下面的函数获取到内核数据

* kernel_char(address) 把指针地址解析为 char 类型
* kernel_short(address) 解析为 short 类型
* kernel_int(address) 解析为 int 类型
* kernel_long(address) 解析为 long 类型
* kernel_string(address) 解析为 string 类型
* kernel_string_n(address, n) 解析为 string 类型，只把前 n 个字节返回

### 打印变量

有时候，我们只想打印上下文的 target variable, systemtap 也提供了一系列操作。

* $$vars   

类似于 `sprintf("parm1=%x ... parmN=%x var1=%x ... varN=%x", parm1, ..., parmN, var1, ..., varN)`, 会打印 probe 范围内的所有变量。如果运行时的变量地址找不到的话，会打印 `=?` 。

* $$locals  

是 $$vars 的子集，只会包含本地变量

* $$parms

是 $$vars 的子集，只会包含函数参数变量

* $$return

只在有 `return` 标识的 probe 中有意义。类似于 `sprintf("return=%x",  $return)`, 如果 probe 有返回值的话，则打印，否则是空字符串。

我们来看个例子

```
 stap -e 'probe kernel.function("vfs_read") {printf("%s\n", $$parms); exit(); }'
```

前面提到 vfs_read  有四个参数：file, buf,  count, and pos 。$$parms 根据这些参数会生成一个字符串，除了 count 参数，其余都是指针类型。下面是可能的输出

```
 file=0xffff8805b071fd40 buf=0x7fffe5b64b60 count=0x2004 pos=0xffff88010db17f48
```

只是打印地址的话，并没有太大作用。可以添加 `$` 后缀，可以更详细的输出数据结构。

```
 sudo stap -e 'probe kernel.function("vfs_read") {printf("%s\n", $$parms$); exit(); }'
```
那么就会输出

```
 file={.f_u={...}, .f_path={...}, .f_op=0xffffffffa0426ac0, .f_lock={...}, .f_count={...}, .f_flags=34818, .f_mode=31, .f_pos=0, .f_owner={...}, .f_cred=0xffff880353f46540, .f_ra={...}, .f_version=0, .f_security=0x0, .private_data=0x0, .f_ep_links={...}, .f_mapping=0xffff88022f18f3f8} buf="" count=8196 pos=-131925246861496
```

使用 `$` 还不能显示嵌套的结构体。使用 `$$` 可以打印嵌套的数据结构。

```
 sudo stap -e 'probe kernel.function("vfs_read") {printf("%s\n", $$parms$$); exit(); }'
```

`$$` 生成的字符串也受最长字符的限制，嵌套太多，输出并不完整。输出如下

```
 file={.f_u={.fu_list={.next=0xffff880117252740, .prev=0xffff880037949c00}, .fu_rcuhead={.next=0xffff880117252740, .func=0xffff880037949c00}}, .f_path={.mnt=0xffff880c142213c0, .dentry=0xffff88034e7f3d40}, .f_op=0xffffffffa01eb3c0, .f_lock={.raw_lock={.slock=0}}, .f_count={.counter=2}, .f_flags=34818, .f_mode=31, .f_pos=0, .f_owner={.lock={.raw_lock={.lock=16777216}}, .pid=0x0, .pid_type=0, .uid=0, .euid=0, .signum=0}, .f_cred=0xffff88010eba8480, .f_ra={.start=0, .size=0, .async_size=0, .ra_pages=32, .mmap_
```
可以使用 `-D` 选项把资源限制调大，后面的输出就会完整了。

```
 sudo stap -e 'probe kernel.function("vfs_read") {printf("%s\n", $$parms$$); exit(); }' -DMAXSTRINGLEN=10240

 file={.f_u={.fu_list={.next=0xffff880414a74f00, .prev=0xffff88036adc7980}, .fu_rcuhead={.next=0xffff880414a74f00, .func=0xffff88036adc7980}}, .f_path={.mnt=0xffff880c142213c0, .dentry=0xffff8801fdbab800}, .f_op=0xffffffffa020d3c0, .f_lock={.raw_lock={.slock=0}}, .f_count={.counter=2}, .f_flags=34818, .f_mode=31, .f_pos=0, .f_owner={.lock={.raw_lock={.lock=16777216}}, .pid=0x0, .pid_type=0, .uid=0, .euid=0, .signum=0}, .f_cred=0xffff880414bb10c0, .f_ra={.start=0, .size=0, .async_size=0, .ra_pages=32, .mmap_miss=0, .prev_pos=-1}, .f_version=0, .f_security=0x0, .private_data=0x0, .f_ep_links={.next=0xffff88054b3b7ca8, .prev=0xffff88054b3b7ca8}, .f_mapping=0xffff88040032b9d8} buf="" count=8196 pos=-131924015866040
```


