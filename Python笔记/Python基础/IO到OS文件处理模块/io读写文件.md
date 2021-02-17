
###非with模式的读写
    io_read = None
    try:
        # 要读写文件 必须建立到文件的io：open
        io_read = open("..\\stus.txt", "r", encoding="utf-8")
    
        # 读文件中的内容
        # 按单字读取
        # 如果未指定读取的数目 默认读完
        # print(io_read.read())
        # 如果给定了读取的数量 按数量的字数进行读取
        print(io_read.read(3))
    except:
        print("io错误！")
    finally:
        # io读取或者写入完毕后 应该关闭io通道
        if io_read is not None:
            io_read.close()

###with模式的写入:自动关闭io通道
    with open("w.txt", "w", encoding="utf-8") as io_write:
        io_write.write("第一行写入的数据！")
    
    io_write.write("ok")

####读取文件操作详细
    with open("..\\stus.txt", "r", encoding="utf-8") as read:
    io的读对象:
    read: 按字符的方式一个一个读取 若没有数目限定 默认读取到文件末尾
    read+限定字符数的读取方式将文件内容全部读完
    while 1:
        content = read.read(50)
        # 读取不到数据的时候 要停止读取
        if not content:
            break
        print(content, end="")
    readline:按行读取
    print(read.readline(),end="")
    print(read.readline(),end="")
    print(read.readline(),end="")
    按行读取所有的行
    while 1:
        line = read.readline()
        if not line:
            break
        print(line, end="")
    readlines:一次性读取所有的行:返回行对应的列表！！！不推荐 文件较小可以使用
    print(read.readlines())

####写入文件的详细操作
    with open("..\\stus.dat", "w", encoding="utf-8") as write:
        # write:按字符串的方式进行写入 要换行 自己追加换行符
        # writelines：按列表写入 将列表的内容进行连接成一个字符串后再写入 需要自己加入换行符
        # write.write(input("请输入要保存的信息："))
        # write.write("\n")
        # write.write(input("请输入要保存的信息："))
    
        write.writelines("\n".join(["123" for i in range(10)]))

####读写一起复制文件
 
    import os
    
    src_file = r"D:\2019随堂\bandicam 2020-07-10 11-48-36-447.mp4"
    dest_file = r"C:\Users\qiku\Desktop"
    
    with open(src_file,"rb") as copy_read:
        with open(os.path.join(dest_file,os.path.basename(src_file)),"wb") as copy_write:
            # 按字符/行
            # 若b模式：按字节读写
            while 1:
                read_str = copy_read.read(100)
                if not read_str:
                    break
                copy_write.write(read_str)


