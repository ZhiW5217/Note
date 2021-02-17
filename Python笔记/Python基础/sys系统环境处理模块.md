import sys

###模块的导入的搜索路径
    print(sys.path)
###获取当前环境所处的操作系统
    print(sys.platform)

###获取内建的名字
    print(sys.builtin_module_names)

###直接终止程序
    sys.exit(0)

    if input("结束？y-结束 其他—结束") == "y":
        sys.exit(1)
    print("程序继续！")
    用于命令行参数
    在程序启动的瞬间注入参数值
    print(sys.argv)
###A B C 三套测试方案
    if sys.argv[1] == "A":
        print("执行方案A")
    elif sys.argv[1] == "B":
        print("执行方案B")
    else:
        print("执行方案C")