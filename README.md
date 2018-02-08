# task

这里将任务分为三类： 

- swoole的异步工作任务 
- timer的定时任务 
- 类似cronTab的计划任务

## work task

swoole的异步工作任务

> 将 `WorkManager` 绑定到每个task进程上, 再通过它来管理、调度所有的 work task

## timer task

定时任务 

> 最好使用一个独立进程（创建或者指定特定的worker），通过 `TimerManager` 来管理、调度所有的 timer task

## Schedule Task(cronTab)

> 最好使用一个独立进程（创建或者指定特定的worker），通过 `ScheduleManager` 来管理、调度所有的 schedule task

计划任务，类似Linux的cronTab,但是最小精度支持到秒

```text
 0     1    2    3    4    5
 *     *    *    *    *    *
 -     -    -    -    -    -
 |     |    |    |    |    |
 |     |    |    |    |    +----- day of week (0 - 6) (Sunday=0)
 |     |    |    |    +----- month (1 - 12)
 |     |    |    +------- day of month (1 - 31)
 |     |    +--------- hour (0 - 23)
 |     +----------- min (0 - 59)
 +------------- sec (0-59)
```

### crontab 计划任务表达式

crontab 文件中每行的每个字段代表一项设置，它的格式共分为六个字段，前五段是时间设定段，第六段是要执行的命令段，

格式如下： `minute hour day month week command`

其中：
- minute： 表示分钟，可以是从0到59之间的任何整数。
- hour：表示小时，可以是从0到23之间的任何整数。
- day：表示日期，可以是从1到31之间的任何整数。
- month：表示月份，可以是从1到12之间的任何整数。
- week：表示星期几，可以是从0到7之间的任何整数，这里的0或7代表星期日。
- command：要执行的命令，可以是系统命令，也可以是自己编写的脚本文件

格式结构图：

```text
   1    2    3    4    5     6
   *    *    *    *    *    [command]
   -    -    -    -    -     -
   |    |    |    |    |     |
   |    |    |    |    |     +-- Will execute command
   |    |    |    |    +----- day of week (0 - 7) (Sunday=0,7)
   |    |    |    +----- month (1 - 12)
   |    |    +------- day of month (1 - 31)
   |    +--------- hour (0 - 23)
   +----------- min (0 - 59)
```

在以上各个字段中，还可以使用以下特殊字符：

- 星号（`*`）：代表所有可能的值
   - 例如month字段如果是星号`*`，则表示在满足其它字段的制约条件后每月都执行该命令操作。
- 逗号（`,`）：可以用逗号隔开的值指定一个列表范围，例如，`1,2,5,7,8,9`
- 中杠（`-`）：可以用整数之间的中杠表示一个整数范围，例如 `2-6` 表示 `2,3,4,5,6`
- 正斜线（`/`）：可以用正斜线指定时间的间隔频率
   - 例如 `0-23/2` 表示每两小时执行一次。
   - `3/20`表示每隔20分钟执行一次，`3`表示从第3分钟开始执行
   - 同时正斜线可以和星号一起使用，例如`*/10`，如果用在minute字段，表示每十分钟执行一次。

扩展字符：

- `?` ：表示每月的某一天，或第几周的某一天
- `L` ：“6L”表示“每月的最后一个星期五”
- `W`：表示为最近工作日
   - 如“15W”放在每月（day-of-month）字段上表示为“到本月15日最近的工作日” 
- `#`：是用来指定“的”每月第n个工作日
   - eg `6#3` 或者 `FRI#3` : 在每周（day-of-week）中表示“每月第三个星期五”

> 表达式参考链接：https://www.jianshu.com/p/f03b1497122a

## ref

- `"mtdowling/cron-expression": "^1.2"` 一个cronTab表达式解析库