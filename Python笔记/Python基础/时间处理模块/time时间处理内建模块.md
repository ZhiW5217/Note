###处理日期时间
###获取日期在某一年的第几天
import time

###距离纪元时间的秒数
    print(time.localtime(time.mktime((2020, 7, 3, 14, 15, 16, 1, 0, 0))).tm_yday)

    1.暂停程序执行 程序的休眠  自动属性
    for i in range(10,0,-1):
        # sleep中设置休眠的秒数：可以为小数
        time.sleep(5)
        print(i)

###统计程序的运行时间
###获取本行执行的系统时间
    s = time.time()
    for i in range(10000000):
        # time.sleep(0.01)
        # print(i)
        print(i, end="")
    print(end="\n")
    print(time.time() - s)