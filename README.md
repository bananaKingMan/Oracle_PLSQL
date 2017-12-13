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
