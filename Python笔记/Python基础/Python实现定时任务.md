# Python实现定时任务
 - 工作中可能需要周期性执行一些任务，俗称定时任务。
 - Linux环境下，可以借助于系统自带的crontab完成定时任务。
 - 但是很多时候，开发人员可能并没有权限去操作crontab。
 - 而schedule是Python的轻量级定时任务解决方案，可以满足常定时采集数据，定时执行脚本程序等周期性任务需求。

- 安装
```
pip install schedule
```
- 按照某个时间间隔
每10分钟执行一次job函数

schedule.every(10).minutes.do(test)

 

每10秒执行一次job函数

schedule.every(10).seconds.do(test)

 

每10小时执行一次job函数

schedule.every(10). hours.do(test)

 

每10天执行一次job函数

schedule.every(10). days.do(test)

 

每10周执行一次job函数

schedule.every(10). weeks.do(test)

 

 

当every()没参数时默认是1小时执行一次job函数

schedule.every().hour.do(test)

 

当every()没参数时默认是分钟执行一次job函数

schedule.every().minute.do(test)

 

当every()没参数时默认是1秒执行一次job函数

schedule.every().second.do(test)

 

当every()没参数时默认是1天执行一次job函数

schedule.every().day.do(test)

 

当every()没参数时默认是1周执行一次job函数

schedule.every().week.do(test)

 

案例演示

from datetime import datetime
import time
import schedule


def job1(*args):
    print("I'm working...")
    with open('tasks', 'a') as f:
        f.write('[{}]{}\n'.format(datetime.now().strftime('%Y-%m-%d %H:%M:%S.%f'), 'msg'))


# 每隔5秒执行一次job1
schedule.every(5).seconds.do(job1)


if __name__ == '__main__':
    while True:
        # 启动服务
        schedule.run_pending()
        time.sleep(1)

 

按照一个固定的时间点
表示每天10:30执行任务

schedule.every().day.at("10:30").do(test)

 

每周一执行一次job函数

schedule.every().monday.do(test)

 

每周三13：15执行一次job函数

schedule.every().wednesday.at("13:15").do(test)

 

案例演示

from datetime import datetime

import time

import schedule

 

 

def job1(*args):

    print("I'm working...")

    with open('tasks', 'a') as f:

        f.write('[{}]{}\n'.format(datetime.now().strftime('%Y-%m-%d %H:%M:%S.%f'), 'msg'))

 

# 每天9:30执行任务

schedule.every().day.at('09:30').do(job1)

 

 

if __name__ == '__main__':

    while True:

        # 启动服务

        schedule.run_pending()

        time.sleep(1)

 

任务只执行一次
在任务最后加上这句话就行，return schedule.CancelJob

案例演示

import time

import schedule

 

def job_run_once(*args):

    print('Job run only once')

    return schedule.CancelJob

 

 

# 只执行一次任务

schedule.every(3).seconds.do(job_run_once)

 

 

if __name__ == '__main__':

    while True:

        schedule.run_pending()

        time.sleep(1)

 

多任务串行执行延迟问题
import datetime
import schedule
import time


def job1():
    print("I'm working for job1")
    time.sleep(2)
    print("job1:", datetime.datetime.now())


def job2():
    print("I'm working for job2")
    time.sleep(2)
    print("job2:", datetime.datetime.now())


def run():
    schedule.every(10).seconds.do(job1)
    schedule.every(10).seconds.do(job2)

if __name__ == '__main__':
    run()
    while True:
        schedule.run_pending()
        time.sleep(1)

解决方法
import datetime

import schedule

import threading

import time

 

 

def job1():

    print("I'm working for job1")

    time.sleep(2)

    print("job1:", datetime.datetime.now())

 

 

def job2():

    print("I'm working for job2")

    time.sleep(2)

    print("job2:", datetime.datetime.now())

 

 

def job1_task():

    threading.Thread(target=job1).start()

 

 

def job2_task():

    threading.Thread(target=job2).start()

 

 

def run():

    schedule.every(10).seconds.do(job1_task)

    schedule.every(10).seconds.do(job2_task)

 

if __name__ == '__main__':

    run()

    while True:

        schedule.run_pending()

        time.sleep(1)