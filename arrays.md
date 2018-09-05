## 数组

SystemTap 也支持关联数组，关联数组实现为 hash table 。关联数组包含一组唯一 key 的集合，每个 key 对应一个 value 。

关联数组必需要定义成 global 变量，不管是否在单个或者多个 handler 中使用。如果访问其中的某个元素，语法如下：

>  array_name[index_expression]

index_expression 可以使用 handler 提供的函数。

### 使用样例

```
# Increment the named array slot:
foo [4,"hello"] ++

# Update a statistic:
processusage [uid(),execname()] ++

# Set a timestamp reference point:
times [tid()] = get_cycles()

# Compute a timestamp delta:
delta = get_cycles() - times [tid()]
```

可以看到，可以使用多个元素组成引用表达式，最多允许9个。每个之间使用 `,` 分隔。

### 数组定义

```
 global ARRAY[<size>]
```

数组的大小可以在定义时显式的指定。如果没有指定，最大值限定为宏 MAXMAPENTRIES 的大小。

### 赋值

> array_name[index_expression] = value

多次对同一个 index_expression 进行赋值的话，每个都会使用最新的 value 进行替换。

### 遍历元素

使用 foreach 遍历数组元素，如果索引是单元素，可以使用下面方式遍历。每次循环，count 会更新为当前 key。遍历的顺序是随机的。

> foreach (count in reads) { printf("%d\n", reads[count])}

也可以通过指定索引或者值的顺序进行遍历，`+` 是升序， `-` 是降序。 可以使用 `limit` 限制遍历的数量。

```
 # 按照索引的升序进行遍历 
 foreach (count+ in reads) { printf("%d\n", reads[count])}

 # 按照值的降序进行遍历 
 foreach (count in reads-) { printf("%d\n", reads[count])}

 # 按照值的降序进行遍历, 并且只处理前10的元素 
 foreach (count in reads- limit 10) { printf("%d\n", reads[count])}
```

上面介绍的单元素索引，如果索引是多元组的话，使用方式类似。来看些例子：

```
 # 双元组元素，以随机顺序遍历
 foreach ([a,b] in foo)
    fuss_with(foo[a,b])

 # 以值的升序遍历 
 foreach ([a,b] in foo+) { ... }

 # 以第一个元素的降序遍历
 foreach ([a-,b] in foo) { ... }
 
 # 以值降序遍历，只处理前10的元素。在这里 v 指的就是 value, 否则通过 foo[i,j] 获取
 foreach (v = [i,j] in foo- limit 10)
    printf("foo[%d,%s] = %d\n", i, j, v)

 # 测试索引是否在数组内
 if ([4,"hello"] in foo) { }
```

### 删除元素

使用 delete 删除数组指定的索引或者清空整个数组

```
 # 删除指定的索引，如果索引不存在，也不会报错
 delete ARRAY[INDEX1, INDEX2, ...]

 # 清空数组 
 delete ARRAY
```
