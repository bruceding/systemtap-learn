
### disktop.stp

监控进程的读写，每隔5s进行打印。

```
Sun Jan 27 23:25:07 2019 , Average: 860Kb/sec, Read:    4231Kb, Write:     71Kb

     UID      PID     PPID                       CMD   DEVICE    T        BYTES
       0    38894     1612                      java      vdb    R      2078485
     576    38928    86355                      java      vdb    R      1480549
     576    14400    13758                      java      vdb    R       378320
       0    38893    38892                  modprobe     vda1    R        54715
       0    38915    38914                  modprobe     vda1    R        54715
       0    38927    38926                  modprobe     vda1    R        54715
     552    80900    84926           realtimestatist      vdb    W        53118
     576    38928    86355                      bash     vda1    R        38063
       0    38923    38922                   ntpdate     vda1    R        34850
       0     1567        1                 YDService      vdb    R        31962
```
T 表示动作，读为 R， W 为写。
### iotime.stp
检测进程的文件读写，把文件的访问信息，读写信息，读写耗时打印出来

```
1042110 129557 (redis-sentinel) access "/proc/129557/stat" read: 348 write: 0
1042175 129557 (redis-sentinel) iotime "/proc/129557/stat" time: 23
1066854 123902 (redis-server) access "/proc/123902/stat" read: 346 write: 0
1066909 123902 (redis-server) iotime "/proc/123902/stat" time: 23
1073521 124755 (redis-server) access "/proc/124755/stat" read: 345 write: 0
1073550 124755 (redis-server) iotime "/proc/124755/stat" time: 8
1093315 100033 (a.out) access "/home/bruceding/data/file.hole" read: 0 write: 20
1093355 100033 (a.out) iotime "/home/bruceding/data/file.hole" time: 9
```
### traceio.stp
打印出进程累积的IO操作
```
 20800            java r:       13 MiB w:        0   B
   87639            java r:       69 KiB w:       46 KiB
   20940        modprobe r:       53 KiB w:        0   B
   87313            java r:       42 KiB w:      960   B
    2919     barad_agent r:        2 KiB w:       15 KiB
   20569          vmstat r:        5 KiB w:       86   B
    2918     barad_agent r:      940   B w:        1 KiB
   81349            java r:        1 KiB w:      232   B
   20940         khelper r:      732   B w:        0   B
   87879            java r:      331   B w:      364   B
```
### ioblktime.stp
打印设备的IO时间
```
 device  rw total (us)      count   avg (us)
       vdc   R  508025137       8899      57087
       vdb   R   73066430       2436      29994
       vdd   R    3477917         66      52695
       vdb   W     109838          5      21967
       vda   W       6487          5       1297
       vdd   W      98615          2      49307
       vdc   W     338257          2     169128
```
