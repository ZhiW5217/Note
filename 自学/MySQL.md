### 简介
- 1. MySQL 成本低 性能高 简单 可信赖

- 2. 用户 主机名 端口 口令
	mysql -u ben -p -h myserver -P 9999
	
- 3. SQL语句及种类
	
	- DDL (Data Definition Language, 数据定义语言)用来创建或者删除存储数据
		create : 创建数据库和表等对象
		drop   : 删除数据库和表等对象
		alter  : 修改数据库和表等对象的结构
	
	- DML (Data Manipulation Language, 数据库操纵语言)查询或变更表中记录
		insert : 向表中插入新数据
		update : 更新表中数据
		delete : 删除表中数据
	
	- DQL (Data Query Language, 数据查询语言)查询数据
		select : 查询表中的数据
		
	- DCL (Dta Control Language, 数据控制语言)用来确认或者取消对数据库中的数据进行的变更
		commit : 确认对数据库中的数据进行变更
		rollback: 取消对数据库中的数据进行变更
		grant  : 赋予用户操作权限
		revoke : 取消用户的操作权限
		
	ps: 实际使用中的SQL语句中有90%属于DML,DDL 应用于数据库和表
		DML,DQL 应用于表中数据的操作
```
单词小记
1. Definition		n. 定义；[物] 清晰度；解说
2. Manipulation		n. 操纵；操作；处理；篡改
3. Control			n. 控制；管理；抑制；操纵装置	vt. 控制；管理；抑制					
4. drop				n. 滴；落下；空投；微量；滴剂	v. 推动；帮助；宣扬；下降；终止
5. alter			vt. 改变，更改	vi. 改变；修改
6. grant			n.（政府）拨款，补助金；授予，给予；合法转让，正式授予		v. （合法地）授予，允许；（勉强）承认，同意
7. revoke			vt. 撤回，取消；废除

```
### SQL的基本书写规则
 - 1. SQL语句要以(;)结尾
 - 2. 关键字不区分大小写
 - 3. 字符串和日期常数需要使用单引号(')括起来,数据及常数直接书写即可
 - 4. 单词之间需要使用半角空格或者换行符进行分隔

### 表的创建
```sql
	CREATE TABLE <表名>
		（<列名1> <数据类型> <该列所需约束>，
		<列名2> <数据类型> <该列所需约束>，
		<列名3> <数据类型> <该列所需约束>，
		<列名4> <数据类型> <该列所需约束>，
		.
		.
		.
		<该表的约束1>， <该表的约束2>，……）；
```
### 命名规则
 - 1. 数据库名称,表名和列名等可以使用(英文字母, 数字, 下划线)
 - 2. 名称必须以半角英文字母为开头
 - 3. 名称不能重复

### 表的删除
```sql
	DROP TABLE <表名>;
```
 - 删除了的表是无法恢复的

### 表结构操作
 - 表定义变更之后无法恢复

 - 添加列的ALTER TABLE语句
```sql
	ALTER TABLE <表名> ADD COLUMN <列的定义>；
```
 - 删除列的ALTER TABLE语句
 ```sql
	ALTER TABLE <表名> DROP COLUMN <列名>；
 ```
 
### 查询
 - 在SELECT语句中使用DISTINCT可以删除重复行
```sql
	SELECT DISTINCT <列名> FROM <表名>;
	在使用 `distict` 时, null 也被视为一类数据
```
ps: DISTINCT 关键字只能用在第一个列名之前
 - 关系查询
` >   >=   <    <=  !=(不等于)   = (等于)    <>(不等于) `  



START TRANSACTION;

### 集合查询
 - count : 计算表中的记录数(行数)
 - sum	 : 计算表中数值列中数据的合计值
 - avg	 : 计算表中数值列中数据的平均值
 - min	 : 计算表中数值列中数据的最小值
 - max	 : 计算表中数值列中数据的最大值


### 分组查询
 - HAVING : 将GROUP BY后的结果进行筛选
```sql
	SELECT <列名1>, <列名2>, <列名3>, ……
	FROM <表名>
	GROUP BY <列名1>, <列名2>, <列名3>, ……
	HAVING <分组结果对应的条件>
```
	- 使用 HAVING 子句时 SELECT 语句的顺序
		SELECT → FROM → WHERE → GROUP BY → HAVING
		
	- WHERE 子句 = 指定行所对应的条件
	- HAVING 子句 = 指定组所对应的条件
### 排序
 - ORDER BY子句
 ```sql
 SELECT <列名1>, <列名2>, <列名3>, ……
 FROM <表名>
 ORDER BY <排序基准列1>, <排序基准列2>, …
 ```
ps: (ORDER BY子句通常写在SELECT语句的末尾

	未指定ORDER BY子句中排列顺序时会默认使用升序进行排列
	
	在结尾使用 `DESC` 关键字,指定为降序排列
	
	排序键中包含NULL时，会在开头或末尾进行汇总)
	