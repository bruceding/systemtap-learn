## 聚集统计

聚集实例用来收集有关数值的统计信息，可以快速大量的积累新数据。这些操作不需要排它锁，存储在流式统计上。聚合操作只用于 global 变量上。它们独立存储，或者当成数组的元素。

把值添加到聚集统计上，使用操作符 ` <<< value` 。 这个操作符很像 C++ 的流操作符。左值为独立的变量或者是数组的关联元素，右值是数值型表达式。有提取函数可以获得统计信息的值。

```
a <<< delta_timestamp
writes[execname()] <<< count
```

### 提取函数

使用格式 ` @extractor(variable/array index expression)` 可以提取统计信息数据。extractor 有下面几个操作：

* count 统计信息中存储不同值的值的数量。

* sum 统计信息中存储的所有值的和

* min 统计信息中所有值中最小值

* max 统计信息中所有值中最大值

* avg 统计信息中所有值的平均值

可以使用数组多元组存储统计信息，多元组可以统计额外的上下文信息。元组的个数最多为5个。举例如下：

```
    global reads
    probe vfs.read
    {
      reads[execname(),pid()] <<< 1
    }
    probe timer.s(3)
    {
      foreach([var1,var2] in reads)
        printf("%s (%d) : %d \n", var1, var2, @count(reads[var1,var2]))
    }
```

第一个 probe 记录了每个进程执行了多少次 read 操作，不光记录了进程名称，还把进程ID也记录下来。第二个 probe 每隔 3s 进行打印，把进程名称，进程ID，执行 read 次数都打印出来。

