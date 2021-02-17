from datetime import datetime
###如何产生时间日期对象
    1.构造方法
    dt1 = datetime(2020, 7, 3, 11, 15, 16)
    print(dt1)
    2.通过方法获取时间对象
    dt2 = datetime.now()
    print(dt2)
    3.通过计算机的纪元时间获取
    1970-1-1
    dt3 = datetime.fromtimestamp(0)
    print(dt3)
    4.通过字符串转换得到
    date_str = input("请输入日期(YYYY-mm-dd HH:MM:SS)：")
    try:
        dt4 = datetime.strptime(date_str, "%Y-%m-%d %H:%M:%S")
        print(dt4)
    except:
        print("输入时间无效！")

###通过日期时间对象可以分项获取年月日时分秒
    now = datetime.now()
    year = now.year
    month = now.month
    day = now.day
    hour = now.hour
    minute = now.minute
    second = now.second

    分项设定：无效
    now.year = 2021

###若已有日期时间对象  需要进行格式转换为字符串进行保存

    now = datetime.now()
    数据库需要的格式为：YYYYmmdd HHMMSS
    print(now)
###日期时间格式化方法
    strftime:日期时间->字符串
    strptime:字符串->日期时间

    db_date = now.strftime("%Y%m%d %H%M%S")
    print(db_date)
    strftime不允许中文
    print(now.strftime("%Y年%m月%d日"))
    建议分项格式化
    print(f"{now.year}年{now.month}月{now.day}日")
    print(now.strftime("%Y{}%m{}%d{}").format("年", "月", "日"))

###日期时间的比较
    exp_time = datetime(2020, 5, 31)
    now = datetime.now()
    print(now > exp_time)
    print(now < exp_time)
    print(now == exp_time)
    print(now >= exp_time)
    print(now <= exp_time)
    print(now != exp_time)
###日期时间的+-
    求100天后的时间
    from datetime import timedelta

    print(datetime.now() + timedelta(days=100))
    print(datetime.now() + timedelta(weeks=2))
    请求出五小时之前的时间
    日期的差值
    已经过期了多少天
    datetime - datetime = timedelta
    print(datetime.now() - exp_time)
    print((datetime.now() - exp_time).days)
###差的小数数 分钟数 和秒数
    print((datetime.now() - exp_time).seconds)
    h, m = divmod(((datetime.now() - exp_time).seconds), 3600)
    m, s = divmod(m, 60)
    print(h, m, s)
###知道生日 求年龄
    dt_test = input("请输入生日(YYYYmmdd)：")
    try:
        dt5 = datetime.strptime(dt_test, "%Y%m%d")
        if datetime.now().replace(year=dt5.year) > dt5:
            print(datetime.now().year - dt5.year)
        else:
            print(datetime.now().year - dt5.year - 1)
    except:
        print("输入的日期无效")

