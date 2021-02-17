# 导入:os/os.path
import os


###格式化路径:
#####获取相对路径的绝对路径表示
    from datetime import datetime

    file_path = "io_os_test.py"
    print(os.path.abspath(file_path))
###从全路径中获取文件或目录名称
    path1 = r"C:\a\b\c\111.py"
    path2 = r"d:\a\b\c\test"
    print(os.path.basename(path1))
    print(os.path.basename(path2))
###路径的连接
    # 根盘符：必须带:\\
    dir0 = "C:\\"
    dir1 = "day22"
    dir2 = "sub1"
    file1 = "init.py"
    # print(dir1 + "\\" +file1)


    # print(os.path.join(dir0, dir1, dir2, file1))

####获取最后一个路径分割符前的内容
    路径：dirname + \\ + basename
    print(os.path.dirname(r"c:\aaa\bbb"))
    print(os.path.dirname(r"c:\aaa\bbb\1.txt"))

###路径的标准化
    path3 = r".\a/b\\c//1.py"
    print(os.path.normpath(path3))
    
    路径表示的目录或文件的属性的获取
    文件有多大 文件或目录的创建时间 访问时间 修改时间
    print(os.path.getsize("day21_task.py"))
    可以获取文件大小 无法获取目录的大小！！！
    print(os.path.getsize("."))
    file_path = "day21_task.py"
###以下时间均为纪元时间的偏移量
#####获取文件的创建时间
    c_time = datetime.fromtimestamp(
        os.path.getctime(file_path))
    print(f"{c_time.year}年{c_time.month}月"
          f"{c_time.day}日 "
          f"{c_time.hour}:{c_time.minute}:{c_time.second}")
####获取文件的访问时间

    print(datetime.strftime(datetime.fromtimestamp(os.path.getatime(file_path)), "%X %x"))
####获取文件的修改时间
print(  os.path.getmtime(file_path))

###路径的判断
    判断是否绝对 相对路径
    path4 = "1.txt"
    path5 = "c:\\1.txt"
    isabs:True:绝对路径 False:相对路径
    print(os.path.isabs(path4))
    print(os.path.isabs(path5))
###判断是否存在
    print(os.path.exists(path4))
    print(os.path.exists("__init__.py"))
    print(os.path.exists("M:\\"))
###判断是否为文件或目录
    当路径对应的文件或目录不存在:False
    print(os.path.isfile("M:\\"))
    print(os.path.isdir("M:\\"))

###拆分路径:
    file_path2 = r"C:\Users\qiku\Desktop\2006作业检查表.1.2.xlsx"
    print(os.path.split(file_path2))
    print(os.path.splitext(file_path2))
    请拆出文件路径中的目录 不带后缀名的文件名 后缀名
    print("文件所在目录:")
    print(os.path.split(file_path2)[0])
    print("文件名:")
    print(os.path.split(os.path.splitext(file_path2)[0])[1])
    print("后缀名：")
    print(os.path.splitext(file_path2)[1])

####编写一个函数，传入一个目录路径，将该目录中的文件进行相同后缀名的分组归档：
    比如所有.txt的放入txt目录中，所有.py的放入py目录中等等，注意不处理子目录
    def group_files(dir_file: str):
        # 辨识传入的是否为目录 且是否存在！
        if not os.path.isdir(dir_file):
            print("无法归档！")
            return
        # 获取该目录下的所有内容
        subs = os.listdir(dir_file)
        # 遍历所有的内容
        for file in subs:
            # 辨识是否为文件
            if os.path.isfile(os.path.join(dir_file, file)):
                # 获取文件的后缀名:txt pdf doc
                ext = os.path.splitext(file)[1][1:]
                # 判断ext对应的目录是否存在
                # 连接父目录和后缀名
                # c:\aaa  1.txt 2.exe  c:\aaa\exe
                group_dir = os.path.join(dir_file, ext)
                if not os.path.exists(group_dir):
                    os.mkdir(group_dir)
                # 将文件移动到归档的目录中
                # 1.txt 2.exe  c:\aaa\1.txt
                # c:\aaa\txt\1.txt
                os.rename(os.path.join(dir_file, file), os.path.join(group_dir, file))


    group_files(r"C:\Users\qiku\Desktop\group")