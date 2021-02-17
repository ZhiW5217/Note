import calendar

###打印年
    print(calendar.calendar(2020, m=2))
    calendar.prcal(2020,m=12)
###打印月历
    calendar.prmonth(2020, 7)
###打印周历
    获取阅历数据
    print(calendar.monthcalendar(2020,7))

###便捷的判断方法
    判断闰年
    print(calendar.isleap(2020))
    True
###获取年份间的闰年数
    不包含右边界
    print(calendar.leapdays(2000, 2021))
    5
###获取星期的最大范围
    第一个获取的结果数是本月的星期
    第二个结果是本月的最大天数
    print(calendar.monthrange(2020, 7))
    (2, 31)
###获取某日期的星期
    print(calendar.weekday(1996, 10, 13) + 1)
