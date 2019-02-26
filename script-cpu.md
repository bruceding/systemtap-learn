
### thread-times.stp 

分用户态和系统态列出耗cpu的信息
```
 comm   tid   %user %kernel (of 89639 ticks)
            java  2491   0.05%   0.03%
            java  2614   0.04%   0.03%
            java  2573   0.04%   0.04%
            java  2745   0.44%   0.16%
            java  2602   0.03%   0.04%
            java  2613   0.04%   0.03%
            java  2740   0.52%   0.13%
            java  2760   0.46%   0.09%
            java  2616   0.05%   0.04%
            java  2752   0.50%   0.16%
       rcu_sched     9   0.00%   0.10%
            java  2743   0.50%   0.14%
            java  2741   0.55%   0.13%
            java  2746   0.49%   0.09%
            java  2750   0.57%   0.12%
            java  2758   0.04%   0.01%
            java  2757   0.51%   0.12%
            java  2747   0.49%   0.16%
            java  2759   0.59%   0.07%
            java  2756   0.45%   0.11%

```
### timeout.stp

跟踪以下系统调用，没有发生事件而超时的次数
 * poll
 * select 
 * epoll
 * itimer
 * futex
 * nanosleep
 * signal
```
 pid |   poll  select   epoll  itimer   futex nanosle  signal| process
 2268 |      0       0    7153       2       0       0       0| java
29292 |      0       0       0       0       0     282       0| YDService
  729 |      0       0      29       0       0       0       0| nscd
    0 |      0       0       0      17       0       0       0| swapper/6
 9852 |      0      15       0       0       0       0       0| sshd
 9749 |      0       0       0       0       0      11       0| YDLive
  790 |      0       0       0       0       0       8       0| zabbix_agentd
  721 |      0       8       0       0       0       0       0| tuned
  794 |      0       0       0       0       0       8       0| zabbix_agentd
 9699 |      0       8       0       0       0       0       0| barad_agent
  699 |      0       0       7       0       0       0       0| auditd
 9698 |      0       7       0       0       0       0       0| barad_agent
  767 |      0       1       0       0       0       4       0| crond
  792 |      0       5       0       0       0       0       0| zabbix_agentd
  728 |      0       0       4       0       0       0       0| dbus-daemon
  793 |      0       4       0       0       0       0       0| zabbix_agentd
14747 |      2       0       0       0       0       0       0| ntpdate
  791 |      0       1       0       0       0       0       0| zabbix_agentd
 9697 |      0       1       0       0       0       0       0| barad_agent
```
### futuxes.stp

列出锁的争用情况

```
java[2268] lock 0x7fcb940ce354 contended 26 times, 4313 avg us
java[2268] lock 0x7fcb78008754 contended 5 times, 359373 avg us
java[2268] lock 0x7fca0c00ac54 contended 5 times, 359100 avg us
java[2268] lock 0x7fd51c036554 contended 404 times, 78462 avg us
java[2268] lock 0x7fca5c003054 contended 52 times, 11004 avg us
java[2268] lock 0x7fcb90061654 contended 5 times, 319993 avg us
java[2268] lock 0x7fcadc02aa28 contended 4 times, 11 avg us
java[2268] lock 0x7fcb940bf188 contended 5 times, 14 avg us
java[2268] lock 0x7fcb94028424 contended 983 times, 28268 avg us
java[2268] lock 0x7fca300194e4 contended 10 times, 1022507 avg us
```
