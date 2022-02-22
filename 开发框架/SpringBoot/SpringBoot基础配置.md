# SpringBoot基础配置

## 配置文件分类

`SpringBoot`通过配置文件`application.properties`就可以修改默认的配置，也提供了另外两种配置文件格式：`yml`和`yaml`。并且，`SpringBoot`将配置信息集中在一个文件中写。

1. properties格式（默认）

   ```properties
   # application.properties
   # 书写格式：key=value。如：修改服务器默认端口
   server.port=80
   ```

2. yml格式（主流格式）

   ```properties
   # application.yml
   server:
   	port: 80
   ```

3. yaml格式

   ```properties
   # application.yaml
   server:
   	port: 80
   ```

注：yml格式和yaml格式除了文件名后缀不一样，格式完全一样。但是，yml格式是主流，用的机会是最多的。

**配置文件优先级**：如果三个文件同时存在，`SpringBoot`会先读取那个文件的配置呢？（不过在实际开发中，一般只会有一个配置文件）

```properties
# 优先级
properties > yml > yaml
# 文件中 相同的配置按优先级读取，不同的配置都会读取。
```

## yaml文件

`YAML`(`YAML Ain't a Markup Language`的缩写)，是一种数据序列化格式。具有**容易阅读**、容易与脚本语言交互、**以数据为核心**、**重数据轻格式**的特点。主要扩展名有`**yml(主流)**`和`yaml`。

### yaml语法格式

- 大小写敏感
- 属性**层级关系**使用多行描述，**每行结尾使用冒号结束**
- 使用缩进表示层级关系，同层级左侧对齐，**只允许使用空格**（不允许使用Tab键）
- **属性值前面添加空格**
- “#”表示注释

```yaml
# 常用数据格式
boolean: TRUE      # TRUE,true,True,FALSE,false，False均可
float: 3.14        # 6.8523015e+5，支持科学计数法
int: 123           # 0b1010_0111_0100_1010_1110，支持二进制、八进制、十六进制
null: ~            # 使用~表示null
string: HelloWorld     # 字符串可以直接书写
string2: "Hello World" # 可以使用双引号包裹特殊字符
date: 2018-02-17       # 日期必须使用yyyy-MM-dd格式
datetime: 2018-02-17T15:02:31+08:00 # 时间和日期之间使用T连接，最后使用+代表时区
lesson: "Spring\tboot\nlesson"      # 如果要用到转义字符，必须用双引号包裹
```

`yaml`格式中也可以表示数组和对象

```yaml
# 数组
subject:       # 使用减号"-"作为数据开始，减号与数据间空格分隔
	- Java
	- 前端
	- 大数据
likes: [Java,前端] # 缩略格式

# 对象
object:
	name: kk
	age: 16

# 对象数组
users:  #格式一
	- name: Tom
	  age: 4
	- name: Jerry
	  age: 5
users1: #格式二
	-
	  name: Tom
	  age: 4
	-
	  name: Jerry
	  age: 5
users2: [ { name:Tom , age:4 } , { name:Jerry , age:5 } ] #缩略格式
```

### yaml数据读取

1. 读取单一数据

   ```java
   @RestController
   @RequestMapping("/books")
   public class BookController {
   
       // 读取单一数据
       // 1.常量   date: 2018-02-17
       @Value("${date}")
       private String str;
       
       /* 2.对象   user1:
        *            name: kk
        */
       @Value("${user1.name}")
       private String name;
       
       // 3.数组   subject: [Java,前端]
       @Value("${subject[0]}")
       private String arr0;
       @Value("${subject}")
       private String[] arr;
       
   }
   ```

   

2. 读取全部数据

   ```java
   @Autowired
   //@Resource
   private Environment env;
   
   System.out.println(env.getProperty("user1.name")); 
   ```

   

3. 变量引用

   ```yaml
   baseDir: /usr/local/fire
   tmpDir: ${baseDir}/tmp
   ```

   

4. 读取数据并封装到对象里

   ```java
   //com.ljk.pojo
   @Component
   //指定加载的数据
   @ConfigurationProperties("user1")
   public class User {
       //变量名必须和配置文件中的属性名一致
       private String name;
       private String age;
       //get、set、toString
   }
   
   //com.ljk.controller
   @Autowired
   private User user1;
   ```

   





