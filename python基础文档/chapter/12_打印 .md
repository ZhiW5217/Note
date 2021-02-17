<!--
    作者：华校专
    email: huaxz1986@163.com
**  本文档可用于个人学习目的，不得用于商业目的  **
-->
# 打印 
1.打印`print`类似于文件`.write()`方法，它将默认地把对象打印到`stdout`流中。它会自动添加一些自动化的格式。
> 和文件的`.write()`方法不同的是，`print`不需要将对象转换为字符串

2.在Python3中，`print`是一个内置函数，用关键字参数表示特定模式，其语法格式为：
  `print(obj1,obj2,sep=' ',end='\n',file=sys.stdout)`，返回值为`None`

* 参数意义依次为：
	* 待打印对象作为位置参数依次给出
	* `sep`关键字参数指定分隔符，默认为空格
	* `end`关键字参数指定结尾字符串，默认为换行符
	* `file`指定输出位置，默认为标准输出文件，它必须是一个写打开的文件对象
* 每个被打印的对象依次自动通过内置的`str()`函数取得其文本表示
* 当没有打印对象时，`print()`会把一个换行符（或者由`end`指定的其他字符串）打印到标准输出流中（或者由`file`指定的文件中）
* 关键字参数可以以任何顺序出现，但必须在位置参数之后
* 如果想指定对齐或者位宽，则可以先构建格式化表达式来生成字符串，然后在`print`这个字符串  
![print示例](../imgs/python_12_1.JPG)

  >Python2中，`print`是语句，有自己的特定语法：`print x,y`，如果想指定结尾字符串（默认为换行），则用`print x,y, '\t'` 

3.`print`实际上是向文件对象中写文本字符串，因此对于字符串常量的一对引号实际上是不输出的，它只是输出字符串的内容。而交互式命令中，为了显示指定字符串，输出中带有一对引号。  
![print与交互式显示区别](../imgs/python_12_2.JPG)

4.你也可以用`sys.stdout.write(str(x)+' '+str(y)+'\n')`代替`print(x,y)`  

5.`file`关键字指定的对象可以是文件对象，也可以是拥有`.write()`方法的其他对象

6.指定了默认的`file`关键字参数时，也可以输出重定向，如

```
	temp=sys.stdout #保存旧值
	sys.stdout=open('test.txt','a') #重新对stdout赋值，该文件对象必须写打开
	print(obj1,obj2)
	sys.stdout.close() #此句可以不要，此时文件对象自动回收，文件自动关闭
	sys.stdout=temp #恢复旧值
```
7.在Python3中，可以通过`from __future __import print_function`使用Python3中的`print()`函数

8.Python3的`input()`直接把输入的文本作为一个字符串返回，不会求值
  (<font color='red'>输入什么，字符串中就是什么</font>）。
>Python2中的`input()`会对字符串求职，就像他们是输入到一个脚本的程序代码一样

  ![input函数](../imgs/python_12_3.JPG)



