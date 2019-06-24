#JDBC-day01

- JDBC java数据库连接
- JDBC是JAVA官司方提供的一套接口,用于连接并操作数据库不同的数据库厂商都提供了一套JDBC的实现类,并以JAR包形式发布,用于使我们的程序可以通过这套实现类来操作其数据库,这套jar称为"驱动".
 

- 我们无需记住这些数据库提供商提供的JDBC实现类的名字,只要使用JDBC接口看待它们即可,以多态形式调用它们来操作数据库 

##1.JDBC接口介绍 ##

- 1): DriverManager
 	- DriverManager是实现类,用于加载数据库驱动,并与数据库连接,成功建立后产生Connection

- 2): Connection接口
	- 表示与数据库的连接,负责管理事务,创建Statement实例
 
- 3):Statement接口
	- 负责执行SQL语句
  
- 4):ResultSet接口
 	- 表示查询结果集

##2.数据库连接步骤 ###
- 1):加载类库(驱动jar包)到JVM
- 2):通过DriverManager建立连接这里会加载jar包中JDBC实现类来与数据库建立连接
- 3):通过Connection创建Statement对象
- 4):通过Statement执行SQL语句
- 5):若执行的是DQL语句,则会得到查询结果集ResultSet,遍历该结果
 集得到查询内容
- 6):关闭连接

### 2.1)Class.forName(String className )加载驱动包 ###
- 不同的数据库,驱动包路径不同,下面代码容易出现:ClassNotFoundException原因一般有两个:
	- 1:类路径写错了
	- 2:项目没有导入驱动jar包(ojdbc)

			Class.forName("oracle.jdbc.driver.OracleDriver");

### 2.2)Connection DriverManager.getConnection 加载驱动建立连接###

- 使用DriverManger的静态方法getConnection该方法要求传入三个参数:

	- 1)数据库地址(不同数据库格式不同):地址:端口:SID

	- 2)登录数据库时的用户名

	- 3:密码

- oracle的格式: jdbc:oracle:thin:@url:port:sid

		Connection conn = DriverManager.getConnection(
					"jdbc:oracle:thin:@172.86.30.150:1521:orcl",
					"openlab","open123");


### 2.3)Statement createStatement()  创建Statement ###

		Statement state= conn.createStatement();

### 2.4)执行SQL语句 ###
- 执行不同种类的SQL语句有相应的方法：
 
#### 2.4.1)int executeUpdate(String sql) 执行DML语句
- 专门用来执行DML语句的方法，返回值是一个整数用来表示执行了该DML语句后影响了
表中多少条数据。


		String sql = "INSERT INTO userinfo_hjx "
						+ "(id,username,password,email,nickname,account) "
						+ "VALUES "
						+ "(seq_userinfo_id_hjx.NEXTVAL, "
						+ "'"+ username+"', "
						+ "'"+password +"', "
						+ "'"+email+"', "
						+ "'"+nickname+"','"+account+"')";
			
		int i = state.executeUpdate(sql);  --执行语句


#### 2.4.2)ResultSet executeQuery（String sql) 执行DQL语句
- 专门用来指定DQL语句的方法，返回值为查询结果集


- ResultSet 表示查询的结果集 ,遍历结果集的步骤为:

	- 1:先判断结果集是否还有下一条记录

	- 2:若有,则可以获取该条记录各字段的值

- 如此反复直到所有记录都被遍历完毕,

- ResultSetMetaData getMetaData() 获取结果集中的元数据
		ResultSetMetaData rsmd = rs.getMetaData();

- getColumnCount();获取结果集包含的字段数
		int i = rsmd.getColumnCount(); 
 
- 遍历的相关方法:
	- 1) boolean next()该方法会让指针指向结果集下一条记录并且ResultSet表示该条记录,若返回值为false则表示没有记录了


	- 2)一组getXXX(String colName)方法:
	 
		- 获取字符串类型字段值用getString
		- 获取整数用getInt
		- 获取小数用getDouble等 

				ResultSet rs = state.executeQuery(sql);   --执行语句

				while(rs.next()){						--遍历结果集
					//获取ID
					int id = rs.getInt("id");
					String username = rs.getString("username");
					String password = rs.getString("password");
					String email = rs.getString("email");
					String nickname = rs.getString("nickname");
					double account = rs.getDouble("account");

					System.out.println(id+","+username+","+password+","+email+","+nickname+","+account);
				}


#### 2.4.3)boolean execute(String sql)  用来执行DDL语句

- 本语句什么SQL语句都可以指定行，但由于DML，DQL有专门的方法，所以该方法一般用来执行DDL语句。返回值为true时说明执行后有返回值，但是若执行不是DQL语句，其他类型语句执行后返回值都是false.

		String sql = "CREATE TABLE userinfo_hjx( "
						+ " id NUMBER(6), "
						+ " username VARCHAR2(32), "
						+ " password VARCHAR2(32), "
						+ " email VARCHAR2(50), "
						+ " nickname VARCHAR2(32), "
						+ " account NUMBER(10,2) "
						+ ")";
		boolean b = state.execute(sql);         --执行语句




##3.数据连接管理  ##
- 由于数据库连接操作,经常使用,可以将其定义为一产专门用来进行数据库连接的静态方法
每次用只需调用该静态方法即可 
- 将地址配置信息写到 *.properties文件中(同Map相似:username=openlab)
- 用Properties类加载配置文件,获取初始化信息,
- 配置文件分:XML标签文件,行字符串,键值对,指针读
 
	*.properties文件格式如图:
![](http://i.imgur.com/8gOJpBw.png)
### 3.1)创建对象:Properties prop = new Properties() 用于加载配置文件 ###

### 3.2)void load(InputStream inStream)  读取配置文件 ###
- 该方法要求传入一个输入流

		Properties prop = new Properties()    //创建对象
		prop.load(new FileInputStream("config.properties"));  读取文件


### 3.3)String getProperty(String key) 获取读到的信息 ###

			String driverName = prop.getProperty("drivername");
			String url = prop.getProperty("url");


			Class.forName(driverName);
			Connection conn = DriverManager.getConnection(url,username,password);


			
# JDBC-day02 #

##1.预编译SQL语句 
- java.sql.Statement用来执行SQL语句,但是Statement只适合执行静态SQL语句,即:SQL语句中不含有拼接动态数据的地方.因为拼接SQL语句会带来SQL语句的复杂度提高,并且可能出现SQL注入攻击
- java.sql.PreparedStatement是Statement的子类专门用来解决上述问题PS适合执行含有动态信息的SQL语句.其执行的是预编译SQL语句.

### 1)PreparedStatement prepareStatement(String sql) 创建对象 ###
- 预编译SQL语句,特点:所有动态信息用 "?" 代替,注意只能代替值

- 由于使用PS不会将动态信息拼接到SQL语句中这样就不会出现由于拼接了动态信息可能导致改变语义的情况,所以使用PS不会出现SQL注入攻击的问题.

- 创建PS的同时将预编译SQL语句传入,Connection 就会将该SQL语句发送至数据库来生成执行计划,只是该执行计划还不能执行,因为缺少?对应的值

		String sql = "INSERT INTO userinfo_hjx "     //预编译SQL语句
					+ "(id,username,password) "
					+ "VALUES "
					+ "(seq_userinfo_id_hjx.NEXTVAL,?,'123456')";
						
		PreparedStatement ps = conn.prepareStatement(sql);  //创建对象


- 向userinfo表中插入1000条数据,每次指定?的值以后执行executeUpdate只是将该值发送过去,用同一个执行计划执行1000遍,这样可以减少数据库生成执行计划带来的开销
 
		for(int i = 0; i < 1000; i++){

			ps.setString(1, user);
			ps.executeUpdate();
		}
### 2)void setString(int parameterIndex, String x)  设置?的值 ###
- PreparedStatement 提供了多种重载的方法,根据需要选择
	- void setDouble(int parameterIndex, double x)   
	- void setDate(int parameterIndex, Date x)

## 2.事务控制  ##

- JDBC默认是自动提交事务的,即:每指定一条DML操作事务就会提交 .
 
- 事务是在Connection 中维护的,但实际上调用的还是数据库的事务机制.

### 2.1)void setAutoCommit(boolean autoCommit)  设置事务自动提交开关 ###

- 默认是true,自动提交, 若希望控制事务,那么首先要将自动提交事务关闭,即设置为false

### 2.2)void commit() 提交事务 ###

### 2.3)void rollback() 回滚事务 ###
- 如果使用手动提交事务,在捕获异常中需要设置回滚事务
- 并且在使用完毕后在finally中关闭连接
			try{
				
			} catch (Exception e) {
				e.printStackTrace();
				if(conn!=null){
					try {
						conn.rollback();      //设置回滚事务
					} catch (SQLException e1) {
						e1.printStackTrace();
					}
				}
			}finally{
				if(conn!=null){
					try {
						conn.setAutoCommit(true);  //使用完毕后,设置回默认状态
						conn.close();			//关闭连接
					} catch (SQLException e) {
						e.printStackTrace();
					}
				}
			}
 eg:完成转账操作

## 3.批处理

- 当需要一次执行多条SQL语句时,可以使用批操作.缓存一组SQL语句一次性发送至数据库执行,这样可以减少客户端到数据库之间的网络调用次数,提高SQL传输效率等

- Statement 和 PreparedStatement 都可以使用批操作

### 3.1)void addBatch()   添加到批处理中 ###

### 3.2)int[ ] executeBatch()  执行批处理 ###
### 3.3)void clearBatch() 清空批处理 ###
- 批量更新中，为了防止内存溢出，要及时清空语句列表

## 4.数据库连接池 ##

### 4.1)创建对象:BasicDataSource ds = new BasicDataSource( ) ###

### 4.2)void setDriverClassName(String driverName) 设置驱动jar包路径###
- 同 Class.forName(String className)  用于加载类库驱动jar包


### 4.3)void setUrl(String url) 设置数据库地址 ###
- 同 DriverManager.getConnection()
- 数据库地址(不同数据库格式不同):地址:端口:SID

### 4.4)void setUsername(String username) 设置用户名
- 登录数据库时的用户名

### 4.5)void setPassword(String password) 设置密码 ###
- 登录数据库时的密码

### 4.6)void setMaxActive(int maxActive)    最大连接数 ###

### 4.7)void setMaxWait(long maxWait)  最大等待时间  ###

### 4.8)Connection getConnection() 获取连接 ###

- 连接池提供了获取连接的方法Connection getConnection()
- 该方法会将连接池中的空闲连接反回,若当前连接池中没有可用空闲连接,那么该方法会阻塞,阻塞时间由maxWait指定的值决定.在阻塞期间若有空闲连接可用会立刻返回该连接,若超时后仍没有可用连接, 那么该方法会拋出超时异常 
 
		BasicDataSource ds = new BasicDataSource( ) 
		Connection conn = ds.getConnection()  		// 获取连接




# JDBC-day03
## 1.返回自动主键 ##


- 创建PS时指定第二个参数,该参数是一个字符串数组,数组中每个元素, 应当是一个字段名,标明通过PS执行DML操作后,返回该记录对应字段的值

		PreparedStatement ps = conn.prepareStatement(deptSql,new String[]{"deptno"});

### 1) ResultSet getGeneratedKeys() 获取字段的结果集

		ResultSet rs = dps.getGeneratedKeys();

### 2) int getInt(int columnIndex);  获取指定字段的值###

		ResultSet rs = dps.getGeneratedKeys();

		rs.next();  //指向结果集下一条记录

		//获取第一个字段的值  ,在参数内传入1
		int deptno = rs.getInt(1); 

## 2.DAO数据访问对象 ##

- DAO是一个层次所有类的统称
- DAO是夹在业务逻辑层与数据库之间的层次.作用是将对数据库的所有操作从业务逻辑层中抽离出来,使得业务逻辑层的代码只关注业务,不再关注数据的维护操作.而且DAO也可以让业务逻辑层的代码对数据的操作完全对象化

