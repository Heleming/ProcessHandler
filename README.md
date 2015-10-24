# 项目名:
`ProcessHandler`

##用途:
简单理解为这是一个` Master Worker `框架. 可以说跟nginx的进程管理模式相似的.

设计思想跟代码实现(包括代码片段) 及其认真的参考了gunicorn框架[详细介绍](http://gunicorn.org/) . 可能有些朋友在纳闷、疑惑. 怀疑我为毛又在造轮子,但我想说的是gunicorn代码理解起来不简单,有不少的UNIX设计艺术在里面. 回归重点, gunicorn的代码质量很高,实现的prefork也很是优雅,但是他更多是为web frame打造的. 我如果只是想做个Master Worker这样的进程管理,以及基于这进程框架封装的RPC或Restful Api服务.

![master worker frame](static/master_worker.png)

----

##文档说明

配置文件说明 config.py:
```
[DEFAULT]
#当收到kill信号后,几秒后干掉worker
graceful_timeout        = 3

#应用的环境变量
base_path               = . 

#日志根目录
log_path                = .

#是否支持多实例
single_instance         = false

[jobexecute]
#是否需要扔到后端
daemonize               = true

#进程名字
proc_name               = jobexecute

#Master主进程PID
pidfile                 = %(base_path)s/master.pid

#日志位置
log_file                = %(log_path)s/master.log

#最大的请求数,也可以理解为是调用测试
max_requests            = 10000

#启动的进程数目,每个进程都是一个实例
number_workers          = 2

```
----

##简单的用法

```
# coding=utf-8

import time
import logging
import traceback

from ProcessHandler.lib.log import setup_file_logging
from ProcessHandler.lib.workers.sync import SyncWorker

logger = setup_file_logging('jobexecute','master.log')
class JobExecute(SyncWorker):

    LOGGER_NAME = "jobexecute"

    def __init__(self, cfg, file_logger=None, ppid=None, sockets=None):
        SyncWorker.__init__(self, cfg, file_logger, ppid)
#        setup_file_logging(self.LOGGER_NAME, self.cfg.log_file)
#        self.logger = logging.getLogger(self.LOGGER_NAME)

    def setup(self):
        super(JobExecute, self).setup()

    def init_process(self):
        super(JobExecute, self).init_process()

    def stop(self):
        super(JobExecute, self).stop()

    def handle_request(self):
        while 1:
            print 'go....'
            logger.info('go...')
            time.sleep(1.5)

if __name__ == '__main__':
    pass
```


