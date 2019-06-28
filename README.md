# StudentManager_SSM

 该版本为之前 [Servlet + JSP](https://www.jianshu.com/p/553fc76bb8eb) 版本上重构的版本，实现的功能非常简单

-  使用 MySQL 关系数据库来保存学生数据
-  能对学生信息进行增删改查的功能

---

## 开发环境
- IDEA-2019.1
- JDK-1.8
- mysql-8.0.15

## 代码资料
原文地址：https://www.jianshu.com/p/6a594fbea51d



## 关联更新
Java学习.md/JavaEE/SSM/





## 学生管理系统（SSM简易版）总结

https://www.jianshu.com/p/6a594fbea51d

`测试后的项目地址：https://github.com/cn-zhao/StudentManager-SSM`

**`个人总结`**

> 关于《学生管理系统（SSM简易版）总结》项目的总结
>
> 一、导入项目
>
> 项目原文的下载地址：https://github.com/wmyskxz/StudentManager-SSM
>
> 项目下载后为一个压缩文件：StudentManager-SSM-master.zip
>
> 将其解压到D:\DevTests\IdeaProjects目录下，解压后的目录为D:\DevTests\IdeaProjects\StudentManager-SSM然后用IDEA导入解压后的这个Maven项目。
>
> 
>
> 二、配置Tomcat Server
>
> 点击Edit Configurations/Add New Configuration，关键点：
>
> ```java
> - Tomcat Server选择Local
> - Application server选择Tomcat 9.0.21
> - 将Server页签中的URL设置为http://localhost:8080/listStudent
> - 将Deployment页签中的Deploy at the server startup设置为StudentManagerSSM:war exploded
> - 将Deployment页签中的Application context的值设置为/，
> - 其他默认即可
> - 注意：如果不按上面进行设置，在http://localhost:8080/StudentManagerSSM/listStudent
> 在这个页里，我增加学生，一点击提交按钮，地址就变成了http://localhost:8080/addStudent
> 而不是http://localhost:8080/StudentManagerSSM/addStudent
> ```
>
> 
>
> 三、项目源码修改
>
> 由于我本机的环境：IntelliJ IDEA 2019.1 x64、mysql-8.0.15、jdk-1.8、tomcat9.0.21，而作者项目中源码里使用的mysql5.1.41，导致数据库无法连接，一直报错。需要将项目源码中的mysql驱动相关内容修改为和我本机的一致：
>
> - 修改pom.xml文件
>
> 修改前：
>
> ```java
> <!-- 数据库 -->
> <dependency>
> <groupId>mysql</groupId>
> <artifactId>mysql-connector-java</artifactId>
> <version>5.1.41</version>
> <scope>runtime</scope>
> </dependency>
> ```
>
> 修改后：将<version>5.1.41</version>中的版本号替换为<version>8.0.15</version>
>
> - 修改jdbc.properties
>
> 修改前：
>
> ```java
> #数据库驱动
> jdbc.driver=com.mysql.jdbc.Driver
> #数据库地址
> jdbc.url=jdbc:mysql://localhost:3306/student?useUnicode=true&characterEncoding=utf8
> ```
>
> 修改后：
>
> ```java
> #数据库驱动
> jdbc.driver=com.mysql.cj.jdbc.Driver
> #数据库地址
> jdbc.url=jdbc:mysql://localhost:3306/student?useUnicode=true&characterEncoding=utf8&useSSL=true&serverTimezone=GMT
> ```
>
> 
>
> 四、启动数据库和web项目
>
> 最后注意，启动web项目前，先启动mysql数据库。否则将报错：
> `### Error querying database.  Cause: org.springframework.jdbc.CannotGetJdbcConnectionException: Could not get JDBC Connection; nested exception is java.sql.SQLException: Connections could not be acquired from the underlying database!`
>
> ```mysql
> 打开数据库
> net start mysql
> mysql -uroot -p
> 输入密码root
> show databases;
> use student;
> show tables;
> select * from student;
> ```
>
> 最后启动web项目，项目地址：http://localhost:8080/listStudent
>
> 
>
> 五、项目启动过程中遇到的问题
>
> 1. 找不到数据库，错误提示Connections could not be acquired from the underlying database!
>
> 原因分析：mysql数据库未启动、或者驱动版本不对。mysql8相较于mysql5变动较大，驱动描述中增加了cj，url描述中增加了时区。
>
> 解决办法：详见前面的修改。
>
> 2. 不能解析jdbc，错误提示Could not resolve placeholder 'jdbc.driver' in string value "${jdbc.driver}"
>
> 原因分析：
>
> jdbc.properties这个文件的编码格式是ANSI格式的，在IDEA中打开后中文注释部分显示为乱码。为了解决乱码，我在记事本中打开这个文件，另存为UTF-8格式后，在IDEA中的中文可以正常显示了。但是就会报上面的这个错误，可能是字符集转换的过程中某些字符mysql驱动不能识别造成的。
>
> 解决办法：把这个文件重新另存为ANSI格式后，就不报这个错误了。或者删除原来的jdbc.properties文件，然后在IDEA中重新创建一个新的jdbc.properties文件，把原文件中的内容复制到这个新文件中。
>
> 3. 项目运行后，地址栏中的地址有问题。在http://localhost:8080/StudentManagerSSM/listStudent
>    在这个页里，我增加学生，一点击提交按钮，地址就变成了http://localhost:8080/addStudent
>    而不是http://localhost:8080/StudentManagerSSM/addStudent。
>
> 原因分析：在配置Tomcat Server时，软件默认将Server页签中的URL设置为http://localhost:8080/StudentManagerSSM_war_exploded/，将Deployment页签中的Application context的值设置为/StudentManagerSSM_war_exploded。
>
> 解决办法：在配置Tomcat Server时，将Deployment页签中的Application context的值设置为/，然后在地址栏中通过http://localhost:8080/listStudent访问这个项目。
>
> 
>
> 六、SSM框架的各模块调用流程
>
> UI(jsp/css/jquery/js) -> controller -> service -> dao -> mapper.xml -> entity -> table
>
> StudentDao接口和mapper文件(StudentDao.xml)的关系：
>
> 1. 接口名StudentDao是mapper文件(StudentDao.xml)的namespace名
> 2. 接口中的方法名是mapper文件中的SQL标签的id值，方法的参数类型是mapper文件中的SQL标签的parameterType值，方法的返回值类型是mapper文件中的SQL标签的resultType值
>
> entity和table的关系：
>
> 1. Student类名是数据库中的表名
> 2. Student类中的属性名是数据库中表名的列名，属性名的类型与列名的类型对应
>
> 
>
> 七、参考资料
>
> 关于mysql驱动的问题，参考：
>
> https://www.cnblogs.com/alsf/p/9346921.html
>
> https://blog.csdn.net/qq_41139830/article/details/85234012
>
> 关于那个时区，也可以直接修改mysql的my.ini，给时区加个8，弄到中国来就可以不用在url里面加时区了。
>
> 关于IDEA部署web项目，参考：
>
> https://www.cnblogs.com/deng-cc/p/6416332.html
>
> 





