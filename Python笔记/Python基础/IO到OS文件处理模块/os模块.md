####获取当前操作系统
    print(os.name)

####获取所有的环境变量
    print(os.environ)

####获取环境变量的某一项
    print(os.environ.get("path"))

    和文件及目录相关
    辨识文件的权限
    文件存在与否
    文件是否可读 可写 可执行
    print(os.access(r"sort_test2.py", os.F_OK))
    print(os.access(r"sort_test.py", os.W_OK))
    print(os.access(r"sort_test.py", os.R_OK))
    print(os.access(r"sort_test.py", os.X_OK))

    print(os.access("..\\main.py", os.F_OK))

####辨别当前的文件的工作目录
    print(os.getcwd())

####修改当前的工作目录：只在当前行后有效！！！
    os.chdir("..")
    print(os.getc wd())
    print(os.access("main.py", os.F_OK))

###创建目录:
    # 创建单级的目录
    # 若目录已存在：报异常！！！ 需要提前预判目录是否存在！
    if not os.access("day22", os.F_OK):
        os.mkdir("day22")
    
    os.mkdir("day22\\a\\b")


####创建多级的目录
    若目录已存在：
    if not os.access("day22\\a\\b\\c\\d", os.F_OK):
        os.makedirs("day22\\a\\b\\c\\d")

####删除单级的空目录:非空目录无法删除
    os.rmdir("day22\\a\\b\\c")

####所有路径的对应的空目录全部删除:非空无法删除！
    os.removedirs("day22\\a\\b")


###文件操作：
####创建文件
    os.open可以创建文件 但是保留对文件操作的IO连接
    单创建文件后应该关闭对文件的io连接
    os.close(os.open("test1.py", os.O_CREAT))

####删除文件
    os.remove("test1.py")

####移动+重命名:不支持跨盘符
    rename:可以将某个文件进行重新命名
#####移动文件位置?
    os.rename("choice.py", "choice2.py")
    os.rename("choice2.py", "day21\\choice.py")
    若移动到的父目录不存在? 会出异常！！！
    os.rename("day21\\choice.py", "day23\\choice.py")

#####renames：无父目录 创建父目录 再移动+重命名
    os.renames("day21\\choice.py", "day23\\choice.py")
    能否跨盘符移动！！
    os.renames(r"D:\1.mp4", r"E:\2.mp4")

####查看目录中的所有的文件和子目录
    print(os.listdir(r"D:\py1908"))
