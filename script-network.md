
### nettop.stp

监听网络状况，把当前进程使用最多的网络按照倒序排列出来。每隔5s进行打印。

```
 PID   UID DEV     XMIT_PK RECV_PK XMIT_KB RECV_KB COMMAND
11048   530 eth0       7014    1491    1129     181 sm-alert
10534   576 eth0        149    2993       9     725 java
10543   576 eth0        277    2398      17     478 java
 9356   576 eth0         52    1318       3     256 java
30526   588 eth0         86     763       5     152 java
10849   597 eth0        575       0      55       0 profile-7000
11221   597 eth0        526       3      52       0 profile-7000
 9840   576 eth0        140     141       9      17 java
23635   552 eth0        267       0      44       0 re-voice
 9890   576 eth0        226       0      14       0 java
 ```

 XMIT_PK, RECV_PK, 是发送，接受网络包的数量，XMIT_KB，RECV_KB 是发送，接受网络字节数大小。
 
 netdev.receive  和 netdev.transmit 是两个别名，在路径 /usr/share/systemtap/tapset/linux/networking.stp 里可以找到定义。 

### tcp_connections.stp

监控接受的TCP 请求。

```
   UID              CMD    PID   PORT        IP_SOURCE
   586             java  39385   1924    10.163.25.227
   586             java  39385   1924    10.163.31.115
   586             java  39385   1924    10.163.31.115
   586             java  39385   1924    10.163.31.115
   586             java  39385   1924    10.163.59.223
   586             java  39385   1924    10.163.25.227
   586             java  39385   1924    10.163.81.117

```
inet_get_ip_source 和 inet_get_local_port 都是函数，也可以在 /usr/share/systemtap/tapset/ 目录下找到。
