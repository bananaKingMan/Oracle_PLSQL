# Oracle_PLSQL
for_4day_1day
PLSQL

1.数据库访问相关的技术

1),plsql : procedural 过程化的sql
2),proc: 使用C语言访问Oracle数据库技术
3),odbc/ado: vc中访问数据库
4),oci: Oracle底层提供的链接接口
5),sqlj/jdbc ：Java访问数据库的技术

PL/SQL
  概念 PL/SQL（procedural language/SQL）:
      在标准SQL的基础上增加过程化处理的语言，是Oracle客户端工具访问Oracle服务器的操作语言是对标准SQL的扩充。
  特点：
	结构化模块化编程
	良好的可移植性
	良好的可维护性
	提高系统性能
  缺点
	不便于向异构数据库移植

SQL语言的特点
	只管做什么 不管怎么做
	没有过程和控制语句
	没有算法描述能力
PL/SQL对SQL扩充的内容
	变量和数据类型
	控制结构
	增加过程和函数
	对象类型和方法

PL/SQL的程序结构
	declare
	  /* 声明区 声明变量、定义类型等 */
	--单行注释
	begin
	  /* 执行区 执行SQL、PLSQL 语句 */	
	exception
	  /* 异常处理区  处理异常 */
	end;
	/  --->表示代码结束、必须独立占一行

PL/SQL的开发环境
	SQLplus： 命令提示符下的工具
	plsqldeveloper:图形化的工具

	begin
	  dbms_output.put_line('Hello World!');
	end;
	/
	
	set serveroutput on;
	
标识符
  作用：给变量、数据类型、游标、过程、函数、包、触发器等命名

  使用标识符定义变量
    declare
	变量名 数据类型；
	变量名 数据类型 := 值；
    begin
	变量名 := 值；

  案列
	declare
	  var_id number;
	  var_name varchar2(25) := 'Carmen';
	begin
	  var_id := 1;
	  dbms_output.put_line(var_id||','||var_name);
	end;
	/
变量与数据类型
  数据类型
	标量类型
	  number  binary_integer	数字类型 //相当于number一个子集  
	  char / varchar2		字符串类型
	  date				日期类型
	  Boolean			布尔类型
  符合类型
	  record 类型 类似结构体
	  table  类型 类似于数组
  参考类型
	  ref  cursor
  大类型（文件路劲）
	  BLOB   //大的二进制数据（0~4G）
	  CLOB   //长文本（0~4G）
	  BFILE  //文件
变量修饰符
	  constant --->  变量名 constant 数据类型
	  not null --->  变量名 数据类型 not null
  案列
	declare 
	  var_id constant number;	
	begin
	  var_id := 1;
	  dbms_output.put_line(var_id);
	end;
	/	  
ERROR at line 2:
ORA-06550: line 2, column 4:
PLS-00322: declaration of a constant 'VAR_ID' must contain an initialization
assignment //constant 修饰的变量必须初始化
ORA-06550: line 2, column 11:
PL/SQL: Item ignored
ORA-06550: line 4, column 4:
PLS-00363: expression 'VAR_ID' cannot be used as an assignment target//cosntant 修饰的变量不能被赋值
ORA-06550: line 4, column 4:
PL/SQL: Statement ignored
	declare 
	  var_id constant number := 1;	
	begin
	  dbms_output.put_line(var_id);
	end;
	/
1
PL/SQL procedure successfully completed.

	declare 
	  var_id constant number := 1;
	  name varchar(25) not null;	
	begin
	  dbms_output.put_line(var_id);
	  name := 'hell0';
	  dbms_output.put_line(name);
	end;
	/
ERROR at line 3:
ORA-06550: line 3, column 9:
PLS-00218: a variable declared NOT NULL must have an initialization assignment//用not null 修饰的必须初始化
	declare 
	  var_id constant number := 1;
	  name varchar(25) not null := 'world' ;	
	begin
	  dbms_output.put_line(var_id);
	  name := 'hell0';
	  dbms_output.put_line(name);
	end;
	/
1
hell0
PL/SQL procedure successfully completed.
任何类型的变量在没有赋值之前默认值都为NULL.

使用binary_integer 和Boolean定义变量 true、false、null
  案列
	declare
	  var_id binary_integer := 1;
	  var_f boolean;
	begin
	  var_f := true;
	  if var_f then
		dbms_output.put_line(var_id);
	  end if;
	end;
	/
1
PL/SQL procedure successfully completed.

  练习：定义两个变量，类型分别和s_emp表中id、first_name 类型相同。并且把id = 1的id和first_name赋值给两个变量，输出。
	declare
	  var_id number(7); 
	  var_name varcahr2(25);
	begin
	  var_id := 1;
	  var_name := 'Carmen';
	  dbms_output.put_line(var_id||','||var_name);
	end;
	/
  可以使用 表名.字段%type获取表中字段的数据类型
	s_emp.id%type
	s_emp.first_name%type
  可以使用select ...into ...语句把查询结果赋值给变量
  select 字段列表 into 变量列表from 表名 where 条件；
	select id,first_name into var_id,var_name form s_emp where id = 1;//要求查询语句有且只有一条结果

	declare
	  var_id s_emp.id%type;
	  var_name s_emp.first_name%type;
	begin
	  select id, first_name into var_id,var_name from s_emp where id = 12;
	  dbms_output.put_line(var_id||' ,'||var_name);
	end;
	/
12 ,Henry
PL/SQL procedure successfully completed.

record 类型 相当于c语言中结构体
  定义record类型的语法
	type 类型名称 is record（字段名 数据类型， ... ...）;
  定义record类型，包括三个成员类型分别和员工信息表id,first_name,salary相同，声明变量，接受id=2的成员相应信息。
	
	declare
	  type new_type is record(id s_emp.id%type, name s_emp.first_name%type, salary number(11,2));
	  var_emp new_type;
	begin
	  select id,first_name,salary into var_emp from s_emp where id =2;
	  dbms_output.put_line(var_emp.id||','||var_emp.name||','||var_emp.salary);
	end;
	/
2,LaDoris,1450
PL/SQL procedure successfully completed.

当查询的字段少于record类型也可以使用。

若要接表中的一整行
  表名%rowtype --record类型 成员是和表中的字段数量和顺序一致

	declare
	  var_emp s_emp%rowtype;
	begin
	  select * into var_emp from s_emp where id = 3;
	  dbms_output.put_line(var_emp.id||','||var_emp.first_name||','||var_emp.salary);
	end;
	/
3,Midori,1400
PL/SQL procedure successfully completed.

table类型 --->类似于数组
  定义table类型的语法：type 类型 is table of 元素的数据类型
	index by binary_integer  
  定义table类型，声明该类型变量，用来保存多个数字。
	declare
	  type arr is table of number   index by binary_integer;
	  var_num arr;
	begin
	  var_num(6) := 100;
	  var_num(1) := 200;
	  var_num(7) := 300;
	  dbms_output.put_line(var_num(1));
	end;
	/
200
PL/SQL procedure successfully completed.

  如果下标连续时，访问table类型的变量
	declare
	--定义table类型
	  type arr is table of number   index by binary_integer;
	--声明变量
	  var_num arr;
	  var_i binary_integer;
	begin
	  var_num(3) := 100;
	  var_num(1) := 200;
	  var_num(2) := 300;
	  var_i:= 1;
	  dbms_output.put_line(var_num(var_i));
	  var_i := var_i + 1; --sql中没有++运算符
	  dbms_output.put_line(var_num(var_i));
	  var_i := var_i + 1;
	  dbms_output.put_line(var_num(var_i));
	  var_i := var_i + 1;
	end;
	/
200
300
100
PL/SQL procedure successfully completed.

  下标不连续时，访问table类型的变量
	first（） : 获取第一个元素的下标
	next（n） : 获取下表为N的元素的下一个元素的下标
	last()    : 获取最后一个元素的下标

	declare
	--定义table类型
	  type arr is table of number   index by binary_integer;
	--声明变量
	  var_num arr;
	  var_i binary_integer;
	begin
	  var_num(3) := 100;
	  var_num(1) := 200;
	  var_num(2) := 300;
	  var_i:= var_num.first();
	  dbms_output.put_line(var_num(var_i));
	  var_i := var_num.next(var_i); --sql中没有++运算符
	  dbms_output.put_line(var_num(var_i));
	  var_i := var_num.next(var_i);
	  dbms_output.put_line(var_num(var_i));
	  var_i := var_num.next(var_i);
	end;
	/
200
300
100
PL/SQL procedure successfully completed.

  定义table类型变量，把员工表中s_emp表中id为1,3,7的员工的信息保存在变量中输出。id，first_name，salary
	declare
	  --定义table类型
	  type emptable is table of s_emp%rowtype	index by binary_integer;
	  --定义变量
	  var_emp emptable;
	  var_i binary_integer;
	begin
	  select * into var_emp(1) from s_emp where id = 1;
	  select * into var_emp(3) from s_emp where id = 3;
	  select * into var_emp(7) from s_emp where id = 7;
	  var_i := var_emp.first();
	  dbms_output.put_line(var_emp(var_i).id||','||var_emp(var_i).first_name||'.'||var_emp(var_i).salary);
	  var_i := var_emp.next(var_i);
	  dbms_output.put_line(var_emp(var_i).id||','||var_emp(var_i).first_name||'.'||var_emp(var_i).salary);
	  var_i := var_emp.next(var_i);
	  dbms_output.put_line(var_emp(var_i).id||','||var_emp(var_i).first_name||'.'||var_emp(var_i).salary);
	  var_i := var_emp.next(var_i);
	end;
	/
1,Carmen.2500
3,Midori.1400
7,Roberta.1250
PL/SQL procedure successfully completed.

变量的作用域
  plsql快的嵌套
	declare
	  --全局
	  var_n number := 100;
	begin
	  declare
	  --局部
	    var_m number := 10;
	  begin
	    dbms_output.put_line(var_m);
	    dbms_output.put_line(var_n);
	  end;
	    dbms_output.put_line(var_n);
	    --dbms_output.put_line(var_m);//全局不能使用局部变量
	end;
	/
  如果局部变量和全局变量重名，局部变量会覆盖同名的全局变量
  使用标签可以使用 <<标签名>> 用标签名.变量的访问方式。
	<<abc>>
	declare
	  --全局
	  var_n number := 100;
	begin
	  declare
	  --局部
	    var_m number := 10;
	  begin
	    dbms_output.put_line('a:'||var_n); -- var_n = 10
	    dbms_output.put_line('g:'||abc.var_n); -- abc.var_n = 100
	  end;
	    dbms_output.put_line(var_n);
	    --dbms_output.put_line(var_m);//全局不能使用局部变量
	end;
	/

控制语句(分支循环跳转）
  分支语句;
  if 简单语句
	if  条件 then --条件可以使用() 一般不用
	  操作
	end if;
  if ...else 语句
	if  条件 then 
	  条件成立执行的操作
	else
	  条件不成立时执行的操作
	end if;
  多分支if 
	if 条件 then
	  操作1
	elseif 条件2 then
	  操作
	... ...
	[elseif 条件N then
	  操作]
	end if;
  --定义3个数字类型的变量并赋值，输出最大值。
Ways: one
	declare
	  num1 number(3);
	  num2 number(3);
	  num3 number(3);
	begin
	  num1 := 2;
	  num2 := 9;
	  num3 := 6;
	  if num1 > num2 then
	     num2 := num1;
	   end if;
	  if num2 > num3 then
	     num3 := num2;
	   end if;
	  dbms_output.put_line('Max Values :'||num3);
	end;
	/
Max Values :9
PL/SQL procedure successfully completed.
Way two:
	declare
	  num1 number(3) := &a;
	  num2 number(3) := &b;
	  num3 number(3) := &c;
	begin
	 --num1 := 2;
	 --num2 := 9;
	 --num3 := 6;
	if num1>num2 then
	  if num1>num3 then
	  dbms_output.put_line('MaxValues:'||num1);
	  else
	  dbms_output.put_line('MaxValues:'||num3);
	  end if;
	else
	   if num2>num3 then 
	   dbms_output.put_line('MaxValues:'||num2);
	   else
	   dbms_output.put_line('MaxValues:'||num3);
	   end if;
	end if;
	end;
	/

  null值运算不用用来比较，只能用来判断。

循环语句
  循环变量初始化 循环条件 循环变量的更新   循环操作
    int i = 1     i < 10       i++        dosomething

  1)简单循环
	语法： 
	loop
	  -- 循环操作
	end loop;
    结束循环语法
	-- 第一种
	exit when 退出循环条件；
	-- 第二种
	if 退出循环条件 then 
	  exit；
	end if;
  案列：循环输出1~10
	declare
	  var_i number := 1;
	begin
	   loop
	     dbms_output.put_line(var_i);
	     exit when var_i = 10; //此处var_i := 10出错
	     var_i := var_i + 1;
	   end loop;
	end;
	/
  2）while 循环
  -- 语法
	while 循环条件 loop
	  循环操作
	end loop;
	

  案例 输出10；
	declare
	  var_i binary_integer := &integer_number;
	begin
	  if var_i > 10 then 
	    while var_i >= 10 loop
	    dbms_output.put_line(var_i);
	    var_i := var_i - 1;
	    end loop;
	  else
	    while var_i <= 10 loop
	    dbms_output.put_line(var_i);
	    var_i := var_i + 1;
	    end loop;
	  end if;
	end;
	/

	declare
	  var_i number := 1;
	  var_num number := 0;
	begin
	  while var_num <= 2000 loop //循环条件中用= 
	  var_i := var_i + 1;
	  var_num := var_num + var_i;//循环操作中用:=
	  end loop;
	  dbms_output.put_line(var_num);
	  dbms_output.put_line(var_i);
	end;
	/
  3)for循环  -- 智能循环
   -- 语法
	for 变量 in 区间 loop
	  -- 循环操作
	end loop;
  -- 案列： 使用for 循环输出1~10
	begin 
	  for  var_i in 1..10 loop
	    if var_i = 10 then
	      dbms_output.put_line(var_i);
	    end if;
	  end loop;
	end;
	/
	1)循环变量不需要声明
	2)循环变量的赋值和条件的判断由for循环自动完成
	begin 
	  for  var_i in 1..10 loop
	    if var_i = 10 then
	      dbms_output.put_line(var_i);
	      var_i := var_i + 1;
	    end if;
	  end loop;
	end;
	/
ERROR at line 5:
ORA-06550: line 5, column 8:
PLS-00363: expression 'VAR_I' cannot be used as an assignment target //其中for循环中的变量不允许对其赋值
ORA-06550: line 5, column 8:
PL/SQL: Statement ignored

	begin 
	  for  var_i in 1..10 loop
	      dbms_output.put_line(11 - var_i);
	  end loop;
	end;
	/

	begin 
	  for  var_i in reverse 1..10 loop //默认区间是从大到小，如果希望从大到小，需要使用reverse反转区间
	      dbms_output.put_line(var_i);
	  end loop;
	end;
	/
  4）go to 语句
  -- 语法：
	 << 标签名 >>
	 ... ...   --标签后面必须有语句
	goto 标签名；

	begin 
	  for var_i in 1..3 loop
	    for var_j in 1..5 loop
		dbms_output.put_line(var_j);
		if var_j = 3 then
		  goto abc;
		end if;
	    end loop;
	  end loop;
	  << abc >>	  
	  NULL; --空语句
	end;
	/

	begin 
	  << abc >>
	  for var_i in 1..3 loop
	    for var_j in 1..5 loop
		dbms_output.put_line(var_j);
		if var_j = 3 then
		  exit abc;
		end if;
	    end loop;
	  end loop;
	  --<< abc >>	  
	  --NULL; --空语句
	end;
	/

PLSQL中如何使用SQL语句
  1)select语句
	select 配合 into 使用
	select id,first_name,salary into var_id,var_name,var_sal from s_emp shere id = 1;
  2)DML语句（insert update delete）
  	TCL语句（commit roolback savepoint）
  3)DDL语句(drop create alter)
	不能直接在plsql中使用，若要使用需要使用动态sql

动态SQL
  概念：把一条字符串对应的SQL语句当成真正的SQL语句去执行。
	begin
	  create table testsql (id number); //ddl语句不能直接在plsql语句中使用看，所以错误。若要用，则用动态sql
	end;
	/
ERROR at line 2:
ORA-06550: line 2, column 4:
PLS-00103: Encountered the symbol "CREATE" when expecting one of the following:
begin case declare exit for goto if loop mod null pragma
raise return select update while with <an identifier>
<a double-quoted delimited-identifier> <a bind variable> <<
close current delete fetch lock insert open rollback
savepoint set sql execute commit forall merge pipe
	
	declare
	  sqlstr varchar2(100);
	begin
	  sqlstr := 'create table testsql (id number)';
	  execute immediate sqlstr;
	end;
	/
PL/SQL procedure successfully completed.
SQL> desc testsql;
 Name					   Null?    Type
 ----------------------------------------- -------- ----------------------------
 ID						    NUMBER

？？？？
	declare
	  sqlstr varchar2(100);
	begin
	  sqlstr := 'create table testsql (id number)';
	  sqlstr := substr(sqlstr,1,length(sqlstr) -1 )||',name varchar2(25))';
	  --sqlstr := substr(sqlstr,-1);
	  dbms_output.put_line(sqlstr);
	  execute immediate sqlstr;
	end;
	/

  dml语句的动态sql 
  1)直接在plsql中使用dml语句
	begin
	  insert into testdsql values(1,'test');
	  commit;
	end;
	/
  2)常规字符串拼接
	declare
	  sqlstr varchar2(100);	
	begin
	  sqlstr := 'insert into testsql values(2, ''test'')';
	  dbms_output.put_line(sqlstr);
	  execute immediate sqlstr;
	  commit;
	end;

  3)带有变量的字符串的拼接
	declare
	  var_id number := 3;
	  var_name varchar2(25) := 'test';
	  sqlstr varchar2(100);
	begin
	  sqlstr := var_id||','|| var_name
	end;
	/
  4)使用占位符 和using 关键字 解决字符串的拼接问题
  占位符：	：名字
  execute immediate sqlstr suing 变量列表；

	declare
	  var_id number := 4;
	  var_name varchar2(25) := 'test';
	  sqlstr varchar2(100);
	begin
	  sqlstr := 'insert into testsql values(:bo, :b1)';
	  execute immediate sqlstr using var_id, var_name;
	  commit;
	end;
	/

select语句的动态sql
  必须是普通色select语句（不能带into）select语句又只有一行结果

	declare
	  sqlstr varchar2(100);
	  var_id number;
	  var_name varchar2(25);
	begin
	  sqlstr := 'select id,first_name from s_emp where id = 1';
	  execute immedita sqlstr into var_id, var_name;
	end;
	/

--练习
	1）使用table类型变量接受s_dept表中id为31,42,50的部门信息并输出
	2）使用动态sql更新testsql表中的数据，指定id的name.

1.什么是pro程序

    1）概念
	通过在过程化的编程语言中嵌入SQL语句而开发的应用成语，称为pro程序。
	其中，过程化的变成语言称为宿主语言
	嵌入的SQL语句称为嵌入式SQL、
    2）proc/c++
	在c/c++中嵌入SQL语句而开发的应用程序，称为proc/proc++程序
	目的：使用c/c++这种高效的编程语言称为访问Oracle数据库工具

2.proc中和数据库相关的语句
	
	#include <...>
	... ...
	/*包含一个教唆sqlca的结构*/
	exec sql include sqlca;
	
	声明变量
	声明函数

	int main（）
	{
		/*链接数据库*/
		exec sql connect：用户名/密码
		/*查询操作*/
		exec sql select 字段列表 into 变量列表 from 表名 where 条件；
		/*断开链接*/
		exec sql commit work release；
		exec sql rollbanck work release;

		return 0;
	}
3.c程序开发步骤
    ... ...

4.proc程序的开发步骤
    1）编译源程序
	vi xxx.pc
    2）预编译 把.pc文件翻译成.c文件
    	proc  xxx.c
    3）编译、链接
	gcc xxx.c -lclntsh      --linux
	gcc xxx.x -lorasql10	--windows
    4）执行
	a.out
  案例---first.pc     
    
5.宿主变量

    概念 C语言是宿主语言，在宿主语言中定义，用于和数据库交互的变量称为宿主变量。
    特点：变量可以在数据库与C语言中使用。
    
    宿主变量的数据类型
	char 
	short
	int		整型
	long 		
	float
	double		浮点
	char arr[]	定长字符串
	varchar arr[] //proc 中可以使用 为变长字符串
    定长字符串，与变长字符串
	定长字符串
	    数据长度不足his，用空格补齐，‘\0’结尾
    案例：second.pc
#include <stdio.h>

exec sql include sqlca;

int main ()
{
	char password[30] = "system/open123";
	varchar name[25];
	exec sql connect: password;
	exec sql select first_name into name from s_emp where id = 1;
	exec sql commit work release;
	printf("The name is : %s hello\n", name);
	return 0;
}
proc secend.pc
gcc secend.c -lclntsh
./a.out

解决：The name is : CarmenP�؎����� hello
1） varchar  name[25] = {0};
2)  name.arr[name.len] = '\0';
解决后：The name is : Carmen hello

    在预编译是，varchar 类型的数组会被翻译成同名的c语言的街结构体
	struct{
	  unsigned short len;
	  unsigned cahr arr[25];
		} name;
    在C语言中使用时，需要按照结构体的使用方法使用，也就是说使用name.arr获取字符创值，

只用name.len、获取字符创的长度。
    在sql语句中，varchar类型和char类型的使用方式相同
    在使用时varchar，可能产生乱码，解决方案：
	a:数据初始化
	  varchar name[25] = {0};
	b:在字符串的下一个元素添加‘\0’
 	  name.arr[name.len] = '\0';
3)使用proc的预编译选项
    proc fitst.pc oname=xx.c/cpp
    char_map=charz :把字符串处理成定长，不足的空格补齐，‘\0’结尾。
	     =varchar2|charf :把字符串处理为定长，不足的一空格补齐，不一‘\0’结尾
	     =string ：把字符串处理成变长，以‘\0’结尾
	proc second.pc char_map=charz oname varchar.c

4）宿主变量的使用注意事项
    1.宿主变量在sql语句中使用时，最好前边加默冒号：宿主变量
	int  id = 1;
	exec sql select frist_name into name from s_emp where id=id (X:error);
	exec sql select frist_name into :name from s_emp where id=:id (X:error);
    2.DDL语句中不能只用宿主变量
	案例：删除一张表 droptable.pc
    3.宿主变量可以使用指针，但是不推荐使用
    4.宿主变量的定义，强烈建议放入声明区
	（C++语言、Windows平台 要求宿主变脸必须在声明区定义）
	exec sql begin declare section;
	exec sql end declare section;

	exec sql begin declare section;
		char userpasswd[] = "system/open123";
		char name[25]; 
	exec sql end declare section;

6.指示变量
    指示变量的概念及作用
	当数据库中的字段值，赋值给宿主变量时，可以只用指示变量获取赋值状态。
	指示变量的值：
	    0		代表正常赋值
	   -1		代表数据库中字段值为NULl
	   >0		代表阶段赋值
    指示变量的语法
	指示变量的数据必须是short类型；
	short indid;
	short indname;
	:宿主变量 [indicator] :指示变量
	exec sql select id, first_name, id int :id:indid, :name:indname from s_emp 

where id = 1;

    ----案列：使用指示变量来指定id,first_name, manager_id的赋值状态
	indvar.pc

7.数组变量
    数组变量使用的注意事项
    1）除了字符类型以外，其他的数据类型的数组只能是一维的
    int ids[50]
    double sals[50]
    char names[50][25]
    2）不支持数组指针
    3）数组最大元素32767
    4）在select语句中使用数组变量时，只能使用数组名，不能使用下标
    5）如果需要是你要哪个多个变量的赋值状态，可以使用指示变量数组
    
    把s_emp表中所有员工id,first_name查询出来，使用相应的数组接收查询结果，并输出

8.sqlca通信区
    通信区
	为了取得每个sql语句之心后的相关状态说明，以便进行错误的后续操作以及跟踪。
	Oracle中有两个通信区：
	  sql通信区	：sqlca
	  Oracle 通信区 ：oraca
	sqlca通信区
	  sqlca是Oracle和应用程序的一个接口，主要用于错误的诊断和时间处理。
	  执行proc程序时，Oracle把每一个嵌入的sql状态信息存入sqlca中，主要包括错误代码

、警告标志设置、诊断文本、影响的行数等。
	  sqlca本质上是c语言的一个结构体。程序中没执一条sql语句就会把sqlca中所有成员的

值重新赋值一遍。所以需要好获取一条sql语句的执行结果的信息的话，需要立即获取，否则就会

被下一条sql语句结果覆盖掉。
	常用结构体成员：
	  sqlca.sqlcode: sql与执行的状态
			0  正常
			>0 执行出错 一般是出现异常
			<0 系统错误 比如数据库系统或网络错误
	  sqlca.sqlerrm.sqlerrmc: sql出错的错误信息（字符串）

	  sqlca.sqlerrd[2]: 保存sql语句影响的行数

#include <stdio.h>
exec sql include sqlca;
int main ()
{
	exec sql begin declare section;
		char pw[] = "system/open123";
		int ids[50] = {0};
		char names[50][25] = {0};
		short indmids[50] = {-2};
	exec sql end declare section;
	exec sql connect:pw;
	if (sqlca.sqlcode)
	{
		printf("%s\n", sqlca.sqlerrm.sqlerrmc);
		return -1;
	}
	exec sql select id,first_name into :ids:indmids,:names from s_emp;

	exec sql commit work release;
	int i = 0;
	for (; i<sqlca.sqlerrd[2]; i++)
		printf("ids:%d  names:%s indmid%hd\n", ids[i], names[i], indmids[i]);
	return 0;
}

ubuntu@linux:~/桌面/Prc/proc_day01$ proc sqlca.pc 
Pro*C/C++: Release 10.2.0.1.0 - Production on Fri Dec 15 14:45:40 2017
Copyright (c) 1982, 2005, Oracle.  All rights reserved.
System default option values taken from: /opt/ora10/precomp/admin/pcscfg.cfg
ubuntu@linux:~/桌面/Prc/proc_day01$ gcc sqlca.c -lclntsh
ubuntu@linux:~/桌面/Prc/proc_day01$ ./a.out --->>
--->>ORA-01017: invalid username/password; logon denied

#include <stdio.h>
exec sql include sqlca;
int main ()
{
	exec sql begin declare section;
		char pw[] = "system/open123";
		int ids[50] = {0};
		char names[50][25] = {0};
		short indmids[50] = {-2};
	exec sql end declare section;
	exec sql connect:pw;
	if (sqlca.sqlcode)
	{
		printf("%s\n", sqlca.sqlerrm.sqlerrmc);
		return -1;
	}
	exec sql select id,first_name into :ids:indmids,:names from s_emp where id = 

100;
	if (sqlca.sqlcode)
	{
		printf("%s\n", sqlca.sqlerrm.sqlerrmc);
		return -1;
	}

	exec sql commit work release;
	int i = 0;
	for (; i<sqlca.sqlerrd[2]; i++)
		printf("ids:%d  names:%s indmid%hd\n", ids[i], names[i], indmids[i]);
	return 0;
}

ubuntu@linux:~/桌面/Prc/proc_day01$ proc sqlca.pc 
Pro*C/C++: Release 10.2.0.1.0 - Production on Fri Dec 15 14:48:50 2017
Copyright (c) 1982, 2005, Oracle.  All rights reserved.
System default option values taken from: /opt/ora10/precomp/admin/pcscfg.cfg
ubuntu@linux:~/桌面/Prc/proc_day01$ gcc sqlca.c -lclntsh
ubuntu@linux:~/桌面/Prc/proc_day01$ ./a.out --->>
--->>ORA-01403: no data found

    oraca通信区
	一个类型与sqlca的数据结构，可以作为sqlca通信区的辅助，如果需要更加详细的状态信

息，就可以时你也能oraca。
	对sqlca的一个补充。
	可以获取执行的sql语句的文本。
	oraca的使用步骤：
	1）包含oraca
	exec sql include oraca;
	2）打开oraca选项
	exec oracle option(oraca=yes);
	3）设置sql的保存标准
	oraca.orastxtf (save text file)
		0	不保存sql文本 默认值
		1	sql语句出错时保存
		2 	sql出现警告或错误时保存
		3	始终保存
	4）获取sql文本
	oraca.orastxt.orastxtc

	案例：oraca.pc
#include <stdio.h>
exec sql include sqlca;
exec sql include oraca;
exec oracle option(oraca=yes);
int main ()
{
	exec sql begin declare section;
		char userpasswd[] = "system/open123";
		char name[25]; 
		int id = 1;
	exec sql end declare section;
	exec sql connect: userpasswd;
	oraca.orastxtf = 1;
	exec sql select first_name into name from s_emp where id = :id;
	exec sql commit work release;
	printf("oraca.orastxt.orastxtc: %s\n", oraca.orastxt.orastxtc);
	printf("Table s_emp first_name: %s\n", name);
	return 0;
}
ubuntu@linux:~/桌面/Prc/proc_day01$ proc oraca.pc 
Pro*C/C++: Release 10.2.0.1.0 - Production on Fri Dec 15 15:27:15 2017
Copyright (c) 1982, 2005, Oracle.  All rights reserved.
System default option values taken from: /opt/ora10/precomp/admin/pcscfg.cfg
ubuntu@linux:~/桌面/Prc/proc_day01$ gcc oraca.c -lclntsh
ubuntu@linux:~/桌面/Prc/proc_day01$ ./a.out 
oraca.orastxt.orastxtc: 
Table s_emp first_name: Carmen

10.proc中如何使用sql语句

    1）select语句
	在语句前加exec sql，配合into 使用
	exec sql select 字段列表 into 宿主变量（数组变量） from 表名 where 条件；
    2） dml语句（insert、update、delete）
	ddL语句（drop、create、alter）
	tcl语句（commit、rollback、savepoint）
	直接在语句前加exec sql
	注意：ddl语句中不能使用宿主变量
	
	综合案例：迷你学生管理系统
	实现功能
	    1）创建学生信息表
	    2）添加学生
	    3）根据学号删除学生信息
	    4）根据学号修改学生信息
	    5）显示学生列表
	    0.退出
	案例名称sql.pc
	1.实现循环菜单的功能
	    while（1）
		{
		    switch （option）{
			    case 1：
			    ... ...
			    breaker；	
			}
		}
定长字符串和变长字符串
  char  name[n] = {0} 
  varchar name[n] = {0};
  struct 
  {
	unsigned short len;
	unsigned char arr[n];
  } name;

  预编译选项：
    char_map = charz
		varchar2|charf
		string

  数组变量使用的注意事项：
    1）在SQL语句中使用宿主变量 最好加 ：
    2）ddl不能使用宿主变量
    3）可以使用指针，单是不推荐
    4）宿主变量的声明最好放入声明区

1.1使用plsql语法
    exec sql execute
	begin
	  /* 相当于plsql的匿名块*/
	end;
    end-exec;

    在预编译是，需要添加连个预编译选项：
	sqlcheck=semantics
	userid=用户名/密码
    在预编译时，需要连接数据库，检查调用的存储过程、函数等是否存在以及可用。

1.2在proc中调用纯粹过程，传入两个数字，然后把两个数之和存入第二个参数。

    create procedure getsum_c(x number, y in out number) as 
    begin
	y := x + y;
    end;
    /

    测试：
	declare
	   var_x number := 3;
	   var_y number := 4;
	begin
	   dbms_output.put_line(var_x);
	   getsum_c(var_x, var_y);
	   dbms_output.put_line(var_y);
	end;
	/

#include <stdio.h>

exec sql include sqlca;

int main ()
{
	exec sql begin declare section;
		char pw[] = "system/open123";
		int a = 10;
		int b = 12;
	exec sql end declare section;
	exec sql connect:pw;
	exec sql execute 
		begin 
			getsum_c(:a, :b);
		end;
	end-exec;
	exec sql commit work release;
	printf("%d\n", b);
	return 0;
}

proc one.pc SQLCHECK=SEMANTICS userid=system/open123 
gcc one.c -lclntsh

create or replace function fun_aaaa (x number, y number, z in out number )  return number as
begin
  if x > y then
  	z := x;
	return z;
  else
  	z := y;
	return z;
  end if;
end;
/

declare
  x number := 10;
  y number := 20;
  z number := 15;
begin
  z:= fun_aaaa(x, y, z);
  dbms_output.put_line(z);
  dbms_output.put_line('hello');
end;
/

2.数据库连接
    本地数据库连接
	应用程序和数据库在同一个台电脑上
	exec sql connect: 用户名/密码；
	exec sql connect：用户名 identified by :密码；
    远程数据库连接
	需要提供一个描述，描述远端的数据库，包括：ip地址、端口号、Oracle数据库的服务id(服务名）
	程序员可以自己写，有固定的格式
	为了编程方便，在$oracle_home/admin/tnsname.ora文件进行了配置可以直接使用，或者按照格式更改。
	$oracle_home:环境变量 oracle的安装目录
	echo $oracle_home
	echo $oracle_sid oracle的服务名
	默认端口号：1521

	定义一个char型数组，保存远程连接的描述字符串，在连接数据库时，使用using引入远程描述字符串：
	exec sql connect ：用户名/密码 using ：描述字符串；

#include <stdio.h>
exec sql include sqlca;
int main ()
{
	exec sql begin declare section;
		char pw[] = "openlab/open123";
		char name[25];
		char rbddesc[20]="DB20"; --远程连接
	exec sql end declare section;
	exec sql connect :pw using :rbddesc;
	exec sql select first_name into :name from s_emp where id = 1;
	exec sql commit work release;
	printf("%s\n", name);
	return 0;
}

多个数据库，可以通过定义标签，然后在使用时用标签标示访问的是哪个数据库。
	at :标签名

#include <stdio.h>
exec sql include sqlca;
int main ()
{
	exec sql begin declare section;
		char pw1[] = "openlab/open123";
		char name1[25];
		/*declare lable*/
		char lable1[10] = "A";
		char rbddesc1[20]="DB20";
	exec sql end declare section;
	exec sql connect :pw1 at: lable1;//using :rbddesc;
	exec sql at: lable1 select first_name into :name1 from s_emp where id = 1;
	exec sql at: lable1 commit work release;
	printf("A:%s\n", name1);

	exec sql begin declare section;
		char pw[] = "openlab/open123";
		char name[25];
		char lable[10] = "B";
		char rbddesc[20]="DB20";
	exec sql end declare section;
	exec sql connect :pw at:lable; //using :rbddesc;
	exec sql at: lable select first_name into :name from s_emp where id = 1;
	exec sql at: lable commit work release;
	printf("B:%s\n", name);
	return 0;
}

proc中错误处理
    局部的错误处理：sqlca.sqlcode来判断sql语句的执行状态。
    全局的错误处理：错误的处理致谢一次，出现同类型的错误时，可以自动查找并执行错误处理方式。
    语法：
	exec sql  whenever 条件 动作；
	条件就只错误的方式，动作就是出现错误后的错误处理方案。
	条件包括三种：
	    not
	    sqlerror --sql语句错误
	    notfound --没有找到数据
 	    sqlwarning --语句出现警告（不常用）
	动作包括：
	    do 错误处理函数（） ---条件发生后，调用错误处理函数。
	    do break;		---条件发生后，退出循环。
	    continue；		---据需运行
	    stop；		---执行停止
	    goto  标签		---跳转到某个位置（）
	proc 中对默认错误处理是忽略的。
	如果代码中sql出现错误（sqlerror、notfound、sqlwarning）向上查找对应的whenever语句，并执行错误处理方案。如果没有找到，继续之心后面的代码。
	如果一点代码中写有多个对同一错误方式的处理方案，每个错误处理方案的作用区域是冲改行代码开始，到下一个同一错误方式之间的代码。
	
4.proc中数据处理
  1）单行单列的查询，使用宿主变量
	exec sql select first_name into:name from s_emp where id = 1;
  2）单行多列的查询，使用多个宿主变量或结构体变量
	exec sql select id,first_name,salary into :id,:name,:salary from s_emp where id = 1;
	使用结构体变量接受单行多列的结果集
#include <stdio.h>
exec sql include sqlca;
struct 
{
	int id;
	char name[25];
	double sal;
} e;
int main ()
{
	exec sql begin declare section;
		char pw[] = "system/open123";
	exec sql end declare section;
	exec sql connect:pw;
	exec sql select id, first_name, salary into :e.id, :e.name, :e.sal from s_emp where id = 1;
	exec sql commit work release;
	printf("%d %s %g\n", e.id, e.name, e.sal);
	return 0;
}
	
1 Carmen                   2500

  3）多行单列的查询，使用数组变量
	exec sql first_name into：names from s_emp;
  4）多行多列的查询，使用多个数组或结构体数组或游标
	结构体数组：
	#include <stdio.h>
exec sql include sqlca;

struct emp 
{
	int id;
	char name[25];
	double sal;
} ;

int main ()
{
	exec sql begin declare section;
		char pw[] = "system/open123";
		struct emp e[50];
	exec sql end declare section;
	exec sql connect:pw;
	exec sql select id, first_name, salary into e from s_emp;
	exec sql commit work release;
	int i = 0;
	for (; i < sqlca.sqlerrd[2]; i++)
	{
		printf("%d %s %g\n", e[i].id, e[i].name, e[i].sal);
	}
	printf("sqlca.sqlerrd:%d\n", sqlca.sqlerrd[2]);
	return 0;
}
	使用游标
	1）声明游标
	  exec sql declare 游标名 cursor for select语句；
	2）打开游标
	  exec sql open 游标名；
	3）提取数据
	  exec sql fetch  游标名 into :宿主变量；
	4）关闭游标
	  exec sql close  游标名；
	游标提取数据用到循环方式，退出方式：
	  exec sql whenever notfound do break;
#include <stdio.h>
exec sql include sqlca;
struct emp 
{
	int id;
	char name[25];
	double sal;
} ;
int main ()
{
	exec sql begin declare section;
	char pw[] = "system/open123";
	struct emp e;
	exec sql end declare section;
	exec sql connect:pw;
	exec sql declare empcursor cursor for select id, first_name, salary from s_emp;
	exec sql open empcursor;
	exec sql whenever notfound do break;
	for (;;)
	{
		exec sql fetch empcursor into :e;
		printf("%d %s %g\n", e.id, e.name, e.sal);
	}
	exec sql close empcursor;
	exec sql commit work release;
	return 0;
}
	有一种特殊的邮包叫做滚动游标，滚动游标可以直接跳到结果集的任何位置。
	滚动游标和顺序游标有两个区别：
	1）声明是，滚动游标的关键字 scroll cursor
	exec sql declare 游标名 scroll cursor for  select 语句；
	2）fetch 是，滚动游标有多种方式：
	fetch first 		---提取第一行
	fetch last		---提取最后一行
	fetch next 		---提取下一行数据
	fetch prior		---提取上一行数据
	fetch absolute n	---提取n行
	fetch relative n 	---提取（若n=N当前行向下数第N行），若n=-N提取当前行向上N行。
	fetch current 		---提取当前行
eg:
	exec sql fetch absolute 5游标名 into :宿主变量；
