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


1.游标cursor
  概念
	游标是映射在结果集中一行数据上的位置指针或实体这阵有了游标，用户就可以访问结果集中任何一行数据。把游标放到某行

数据后，就可以对该行数据进行操作，比如提取这行数据。
  作用
	处理多行结果集
  游标的使用步骤
      声明游标
	在声明区声明游标
	声明游标名以及对应的select语句
	select语句并没有执行（没有产生查询结果），只是把声明信息保存在相应的数据字典中。
	声明游标后，可以使用 游标名%rowtype 声明变量
	--语法
	cursor 游标名 is select 语句；
      打开游标
	执行在声明游标时 指定的select语句，把查询结果集保存在游标对应的工作区，同时游标指向工作区的首部、
	--语法
	open 游标名  //游标一旦打开，无法再次打开，除非先关闭。
      提取数据
	从定义游标的工作区检索一条数据放入指定的变量中。
	每提取一条数据，游标自动向下移动一行（指向下一行数据）
	游标指针只能单向的向下移动，不能返回。
	--语法
	fetch 游标名 into 变量 //用游标名%rowtype
      关闭游标
	当提取和处理完游标结果集的数据后，应该及时关闭游标，以释放占用的系统资源。
	关闭游标后，游标所对应的工作区变为无效，不能在用fetch语句提取其中数据。
	关闭的游标可以再次用open语句打开
	--语法
	close 游标名；
  使用游标操作s_mep中的全部数据
	declare
	--声明游标
	    cursor emp_cursor is select * from s_emp;
	--声明变量
	    var_emp emp_cursor%rowtype;
	begin
	--打开游标
	    open emp_cursor;
	--提取数据
	    fetch emp_cursor into var_emp;
	    dbms_output.put_line(var_emp.id||'.'||var_emp.first_name||'.'||var_emp.salary);
	    fetch emp_cursor into var_emp;
	    dbms_output.put_line(var_emp.id||'.'||var_emp.first_name||'.'||var_emp.salary);
	    fetch emp_cursor into var_emp;
	    dbms_output.put_line(var_emp.id||'.'||var_emp.first_name||'.'||var_emp.salary);	
	--关闭游标
	    close emp_cursor;
	end;
	/
1.Carmen.2500
2.LaDoris.1450
3.Midori.1400
PL/SQL procedure successfully completed.

  游标的属性
	游标名%属性
	found      在提取数据时，如果提取到了数据，返回真（true）；否则返回假（false）；
			     如果没有提取数据，则值为空（NULL）; //fetch 之前为空
			     如果没有打开游标，会产生非法游标异常。//open 之前为非法
	notfound   与found相反。
	isopen     游标是否处于打开状态。//游标打开返回真，游标关闭返回假。 游标的打开与关闭没有重复性
	rowcound   游标偏移量//游标没有打开则，出现非法异常。

  使用简单循环 配合notfound属性遍历游标
	declare
	  cursor emp_cursor is select * from s_emp;
	  var_emp emp_cursor%rowtype;
	begin
	  open emp_cursor;
		loop
	  	  fetch emp_cursor into var_emp;
		  exit when emp_cursor%notfound;
	  	  dbms_output.put_line(var_emp.id||','||var_emp.first_name||','||var_emp.salary);
		end loop;
	  close emp_cursor;
	end;
	/
  使用while循环 配合found
？？？？declare
！！！！  cursor empcur is select * from s_emp;
	  var_cur empcur%rowtype;
	begin
	  open empcur;
	  fetch empcur into var_cur;
	while empcur%found loop //此处empcur不能用var_cur;
	    dbms_output.put_line(var_cur.id||' , '||var_cur.first_name||' , '||var_cur.salary);
	    fetch empcur into var_cur;
	end loop;
	  close empcur;
	end;
	/
  使用for可以自动打开游标、提取数据、关闭游标
	declare
	  cursor empcur is select * from s_emp;
	  --var_cur empcur%rowtpye;//不用声明
	begin
	  for var_cur in empcur loop
	    dbms_output.put_line(var_cur.id||' , '||var_cur.first_name||' , '||var_cur.salary);
	  end loop;
	end;
	/
  带参游标
      参数的数据类型不能有任何长度、精度等修饰，但是可以使用%type
      打开游标是传参
      cursor 游标名（形参列表） is select 语句;
      ...
      open 游标名（实参列表）;

	declare
	  cursor empcur(var_id number) is select * from s_emp where id > var_id;
	  --var_cur empcur%rowtpye;//不用声明
	begin
	  for var_cur in empcur(10) loop
	    dbms_output.put_line(var_cur.id||' , '||var_cur.first_name||' , '||var_cur.salary);
	  end loop;
	end;
	/

  参考游标//动态查询语句返回多行 需要用到动态sql加游标
	sqlstr := 'select * from s_emp';
	使用参考游标步骤
	1）定义一个参考游标数据类型
	type 参考游标类型 is ref cursor;
	2)声明参考游标类型变量（声明游标）
	变量名 参考游标类型；
	3）把游标变量与动态SQL结合（打开游标）
	open 游标名（变量名） for sqlstr;
	4)提取数据
	fetch 游标名 into 变量；
	5）关闭游标；0
	close 游标名；

？？？？declare
	--声明参考游标定义
	  tpye mycursor is ref cursor;
	--声明参考游标类型变量
	  var_empcur mycursor;
	--声明变量 接受每行结果
	  var_emp s_emp%rowtype;
	--声明变量 保存动态SQL语句
	   sqlstr varchar2(100);
	begin
	  sqlstr := 'select * from s_emp';
	--打开游标 把动态sql语句和参考游标关联
	  open empcur for sqlstr;
	--提取数据
	  loop
	    fetch empcur into var_emp;
	    exit when empcur%not found;
	    dbms_output.put_line(var_cur.id||' , '||var_cur.first_name||' , '||var_cur.salary);
	  end loop;
	--关闭游标
	    close empcur;
	end;
	/


PLSQL中的异常
  系统预定于异常
  Oracle系统为用户提供的，可以在plsql中使用的预定义异常，以便检查用户代码失败的一般原因。
  系统预定义异常有系统定义和引发，用户只需要根据异常的名字捕获和处理。
  exception
	when 异常名 then 
	异常处理
  ...
zero_divide		除数为零
	
  案列
	declare
	  var_id number := 100;
	  var_name varchar2(25);
	begin
	  select first_name into var_name from s_emp where id < var_id;
	  dbms_output.put_line(var_name);
	exception 
	  when  no_data_found  then 
	    dbms_output.put_line('no emp');
	  when others then
	    --dbms_output.put_line('Others');
	    dbms_output.put_line(sqlcode||'...........'||sqlerrm);
	end;
	/


  常用异常：
	cursor_already_open
	invalid_cursor
	invalid_number		不能讲字符串准环卫字符串成数字
	no_date_found		select ..into 结果集为空
	to_many_rows		结果集操作一行
 自定义异常
	步骤：
	1）定义异常
	异常名 exception；
	2）更具条件引发异常
	if 引发条件 then 
	    raise 异常名；
	end if;
	3）捕获
	4）处理
	
  案列：更新执行编号的员工信息，员工不存在时提示异常
	emp_new_1为例

	declare
	  no_emp exception;
	  var_id number := 100;
	begin
	  update emp_new_1 set salary = 9999 where id = var_id;
	  if sql%notfound then 
	    raise no_emp;
	  end if;	  
	  commit;
	exception 
	  when no_emp then 
	    dbms_output.put_line('no emp');
	end;
	/
隐式游标：在执行一个sql语句时，Oracle会自动创建一个隐式游标。这个游标是内存中处理该条语句的工作区。
  隐式游标主要是处理dml语句的结果,当然特殊情况下也可以处理select语句。
  由于隐式游标也有属性，使用属性是，需要游标的名字，所以Oracle提供了隐式游标的名字--sql.
  
存储过程 procedure
  匿名块  有名块
  匿名块： 匿名块不保存在数据中，每次使用都需要进行编译，不能再其他块中调用
  有名块： 可以存储在数据库中，并且可以在任何需要的地方调用
	存储过程（procedure） 函数（function） 包（package） 触发器(trigger)

创建存储过程的语法

	create [or replace] procedure[(参数列表)]
	  {as|is}
		--临时变量
	begin
	exception
	end;
	/
  无参的存储过程
  案列： 创建一个无参存储过程，输出两个书中的较大值。
	--创建
	create or replace procedure getmax_1
	is
	  var_x number;
	  var_y number;
	begin
	  var_x := 10;
	  var_y := 200;
	if var_x > var_y then
	  dbms_output.put_line(var_x);
	else
	  dbms_output.put_line(var_y);
	end if;
	end;
	/
  如果创建的过程存在编译错误，默认只有警告，没有具体的错误信息，查看错误信息，可以使用命令：show errors
  调用
	begin
	  getmax;
	end;
	/
  查看存储过程：user_source
  select text from user_source where name = 'GETMAX'; <----名字//查看存储过程

  带参的存储过程
    注意事项
	1）参数的数据类型不能带有长度或精度的修饰
	2）参数的语法：
		参数名{[in]|out|in out} 数据类型 {:= 值|default 值}
		参数的模式：
		  in  		 输入参数 负责向过程传入数据 默认方式  //实参要求是值，或初始化的变量
		  out 		 输出参数 负责从过程传出数据  //实参必须是变量，不需要初始化
		  in  out 	 输入输出参数 既负责传入数据，有负责传出数据 //必须是初始化的变量
	默认值：
		模式是in 的才可以默认值
  创建带参的存储过程
	create or replace procedure getmax_1(
	  var_x in number, var_y number := 100)
	is
	begin
	  if var_x > var_y then
	    dbms_output.put_line(var_x);
	  else 
	    dbms_output.put_line(var_y);
	end if;
	end;
	/
  参数赋值方式
	declare
	  var_a number := 100;
	  var_b number := 300;
	begin
	  getmax_1(var_a, var_b);
	  getmax_1(3,100);
	  getmax_1(203);
	end;
	/
  按照名字赋值
	参数名=>值
	declare
	  var_a number := 100;
	  var_b number := 300;
	begin
	  getmax_1(var_a, var_b);
	  getmax_1(3,100);
	  getmax_1(var_x=>10, var_y=>20);
	end;
	/

  --练习 ：创建一个存储过程，输出两个较大值，同时把数字之和存到第二个参数
？？？？create or replace procedure max(
	  var_x in number := 1, var_y in out number:= 2)
	is
	begin
	  if var_x > var_y then
	    dbms_output.put_line(var_x);
	    
	    dbms_output.put_line(var_z);
	  else 
	    dbms_output.put_line(var_y);
	    
	    dbms_output.put_line(var_z);
	end if;
	end;
	/ 
  函数 function	
	函数和存储过过程的区别
	1）关键字不同 函数是function，存储过程是procedure
	2) 存储过程没有返回类型和返回值，函数有
	3）调用方式不同：
	存储过程在plsql中是直接调用
	函数在plsql中调用时要组成表达式调用
	  a.变量:=函数（参数）
	  b.把函数的调用作为其他的函数存储过程的参数
  语法
	create[or replace] function 函数名[（参数列表）]
	    return 返回值的数据类型
	{is|as}
	  -- 临时变量
	begin
	  -- 必须有return语句
	exception
	end;
	/

  案列：
	create or replace function getmin_1(
	  var_x number, var_y number) return number
	is
	begin
	  if var_x > var_y then
	    dbms_output.put_line(var_y);
	    return var_x;
	  else
	    dbms_output.put_line(var_x);
	    return var_x;
	  end if;
	end;
	/

	declare
	  var_min number;
	begin
	  var_min := getmin_1(12,123);
	end;
	/
  练习：创建一个函数，返回两个数的较小值，同时把两个之和存到第二个参数。
	
	create or replace function getmin_1(
	  var_x in out number , var_y in out number ) return number
	is 
	begin
	  if var_x > var_y then
	    var_x := var_x + var_y;
	    dbms_output.put_line('Total:='||var_x);
	    return var_y;
	  else 
	    var_y := var_x + var_y;
	    dbms_output.put_line('Total:='||var_y);
	    return var_x;
	  end if;
	end;
	/

	declare
	  var_min number;
	  var_a number := 12;
	  var_b number := 14;
	begin
	  var_min := getmin_1(var_a,var_b);
	  dbms_output.put_line('Min:='||var_min);

	  var_min := getmin_1(12,23); //expression '12' cannot be used as an assignment target
	  dbms_output.put_line('Min:='||var_min);
	end;
	/

  包 package
  概念
	把一组逻辑上相关的函数、过程、类型、变量等组织到一起的一种逻辑结构
  系统提供的包
？？？？dbms_output ： 输出包
	dbms_random :  产生随机数

  自定义包
    步骤
	1）定义一个头部 类似于C语言中.h文件
	create [or replace] package 报名
	is
	-- 声明函数、过程、定义类型、声明变量。
	end [报名];

	2) 定义一个包的主体 类似C语言中.c文件
	create [or replace] package body 包名
	is 
	--实现函数、过程等
	end [包名];

  创建包的头部
	create or replace package PKG
	is
	--声明过程
	procedure getmax(x number, y number);
	--声明函数
	function getmin (x number, y number) return number;
	end;
	/

  创建包的主体
	create or replace package body PKG
	is
	  procedure getmax(x number, y number)
	  is 
	  begin
		if x > y then
		  dbms_output.put_line(x);
		else
		  dbms_output.put_line(y);
	 	end if;
	  end;
？？？？  function getmin(x number, y number)
	  is
	  begin
		if x > y then 
		  return y;
		else
		  return x;
		end if;
	  end;
	end;
	/

  使用包中数据
	declare
	  var_a number := 1;
	  var_b number := 2;
	  var_c number;
	begin
	PKG.getmax(var_a, var_b);
	var_c := PKG.getmix(var_a, var_b);
	dbms_output.put_line(var_c);
	end;
	/

触发器 trigger
  概念
	触发器可以看成是一种的特殊的存储过程，他定义了一些和数据库相关事件。（insert delete update create等）发生时应

该执行的功能代码块。通常用于去管理一些复杂性约束、监控对表达修改。通过其他的程序，实现对数据的审计功能。
  语法 DML触发器
	create [or replace] trigger 触发器名
	{befor|after} {insert|update|delete}
	on 表名[for each row]
	declare
	begin
	exception
	end;
	/

  语句及触发器
	以emp_new_1为例创建一个触发器，监控对表的更新
	create trigger update_emp_new_1
	after update on emp_new_1
	declare
	begin
	dbms_output.put_line('table update');
	end;
	/
	
	update emp_new_1 set salary = salary + 50 where id = 1;
	
  行级触发器
	create trigger update_emp_new_3
	after update on emp_new_1 for each row
	declare
	begin
	--dbms_output.put_line('table update');
	dbms_output.put_line('old:'||:old.id||','||:old.salary);
	dbms_output.put_line('new:'||:new.id||','||:new.salary);
	end;
	/
	update emp_new_1 set salary = salary + 50 where id > 1;
	列标识符
	    原值标识符 := old 更新前的数据行的值
	    新值标识符 := new 更新后的数据行的值
	类型： 表名%rowtype类型

  使用触发器产生主键的值：
	--创建表
	create table trigger_4(id number primary key, name varchar(25));
	--创建序列
	create sequence trigger_4_s;	
	--创建触发器
	create trigger trigger_insert
	before insert on trigger_4 for each row
	begin
	  select trigger_4_s.nextval into :new.id from dual;
	  dbms_output.put_line(:new.id);
	end;
	/
	--插入语句
？？？？	insert into trigger_4 values('Ben');
	/

---->练习
	创建一个存储过程，传入一个大于1的整数，把1..n的累加和放入到第二个参数，并测试。
