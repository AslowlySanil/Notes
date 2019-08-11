# springboot_learn 20190630

### Spring Boot项目

~~~markdown
- 如果你的其他包都在使用了@SpringBootApplication注解的main app所在的包及其下级包，则你什么都不用做，SpringBoot会自动帮你把其他包都扫描了
- 如果你有一些bean所在的包，不在main app的包及其下级包，那么你需要手动加上@ComponentScan注解并指定那个bean所在的包
~~~

#### 0.1 项目结构推荐

~~~markdown
（1）代码层的结构
　　根目录：com.springboot
　　　　1.工程启动类(ApplicationServer.java)置于com.springboot.build包下
　　　　2.实体类(domain)置于com.springboot.domain
　　　　3.数据访问层(Dao)置于com.springboot.repository
　　　　4.数据服务层(Service)置于com,springboot.service,数据服务的实现接口(serviceImpl)至于com.springboot.service.impl
　　　　5.前端控制器(Controller)置于com.springboot.controller
　　　　6.工具类(utils)置于com.springboot.utils
　　　　7.常量接口类(constant)置于com.springboot.constant
　　　　8.配置信息类(config)置于com.springboot.config
　　　　9.数据传输类(vo)置于com.springboot.vo
（2）资源文件的结构
　　根目录:src/main/resources
　　　　1.配置文件(.properties/.json等)置于config文件夹下
　　　　2.国际化(i18n))置于i18n文件夹下
　　　　3.spring.xml置于META-INF/spring文件夹下
　　　　4.页面以及js/css/image等置于static文件夹下的各自文件下
~~~

#### 0.2 domain

~~~markdown
domain类不需要提供任何实现，属于供应方的服务接口层。创建在项目模块的xxx.<module>.domain包下
名称与表名称相同，表名中_替换为驼峰命名法，首字母大写，若表中涉及到缩写，需要修改为原单词，如tra_report_header对应实体类TrainingReportHeader
每一个domain类即为一个实体类，对应数据库中的一个具体表.
@TableName("tra_report_header")指定domain对应数据库中表的名称，用在类上
用@Data注解代替getter、setter方法，用在类上
~~~

#### 0.3 dto

~~~markdown
dto不需要提供任何实现，属于供应方的服务接口层。创建在项目模块的xxx.<module>.dto包下
命名规则是domain类命名+DTO结尾 例如 SysInstMailLogDTO
当domain类字段和前端所需字段不一致时，需要创建dto。如：表里存的是申请人id，前端需要显示申请人名称
~~~

#### 0.4 persistence 持久化

~~~markdown
该层存放 *.Mapper接口和 *.Mapper.xml文件。 如果把mapper.xml文件放到java类的包中。需要在pom中增加配置。具体设置见 10.3mybatis Mapper位置章节。
创建在项目模块的xxx.<module>.persistence包下
每一个Mapper接口类封装了对数据库表的操作，每一个Mapper对应一个domain类，所以命名为domain类名 + Mapper。如：TrainingReportHeaderMapper对应表为TrainingReportHeader类
需增加 @Compoment注解
~~~

#### 0.5 service和impl

~~~markdown
impl 需增加 @Service 注解
~~~

#### 0.6 web层

~~~
具体的实现  @RestController @RequestMapping("/请求路径") 
~~~

#### 0.7 启动类

~~~markdown
@SpringBootApplication
@MapperScan("com.richard.springboot.mybatis.**.persistence")
@ComponentScan(value="com.richard.springboot")
public class HelloworldApplication {
    public static void main(String[] args) {
        System.out.println("hello world 2019");
        SpringApplication.run(HelloworldApplication.class, args);
    }
}
~~~



### 1 主配置 自定义配置

```properties
server.port=8010
server.context-path=/api
```

```yml
spring:
  profiles:
    active: dev
```

#### 1.1 application-dev.yml

```yaml
server:
  port: 8010
  context-path: /api
logging:
  level:
    root: error
    com.richard.springboot.helloworld: debug
  file: /logs/mylog.log
```

#### 1.2 配置文件的优先级

~~~markdown
1. file:./config/ (当前项目路径config目录下);
2. file:./ (当前项目路径下);
3. classpath:/config/ (类路径config目录下);
4. classpath:/ (类路径config下).

优先级由高到底，高优先级的配置会覆盖低优先级的配置； SpringBoot会从这四个位置全部加载配置文件并互补配置； 

接下来还是以端口配置为例 

1. 在resources/config目录下配置文件设置端口为8010;
2. 在resources/目录下配置文件设置端口为8020;
3. 在类路径config目录下配置文件设置端口为8030;
4. 在类路径下配置文件设置端口为8040;

~~~

![1564982045337](C:\Users\RICHAR~1\AppData\Local\Temp\1564982045337.png)



最终的运行结果是端口号 8010.

#### 1.4 日志依赖

##### 1.4.1 添加日志依赖

spring-boot-starter其中包含了 spring-boot-starter-logging，该依赖内容就是 Spring Boot 默认的日志框架 logback .实际开发中我们不需要直接添加该依赖.

##### 1.4.2 控制台输出

日志级别从低到高分为TRACE < DEBUG < INFO < WARN < ERROR < FATAL，如果设置为WARN，则低于WARN的信息都不会输出。 Spring Boot中默认配置`ERROR`、`WARN`和`INFO`级别的日志输出到控制台 。

开启调试模式的方式：

- 在运行命令后加入`--debug`标志，如：`$ java -jar springTest.jar --debug`
- 在`application.properties`中配置`debug=true`，该属性置为true的时候，核心Logger（包含嵌入式容器、hibernate、spring）会输出更多内容，但是你自己应用的日志并不会输出为DEBUG级别。

##### 1.4.3 文件输出

默认情况下，Spring Boot将日志输出到控制台，不会写到日志文件。如果要编写除控制台输出之外的日志文件，则需在application.properties中设置logging.file或logging.path属性。 

- logging.file，设置文件，可以是绝对路径，也可以是相对路径。如：`logging.file=my.log`
- logging.path，设置目录，会在该目录下创建spring.log文件，并写入日志内容，如：`logging.path=/var/log`

 如果只配置 logging.file，会在项目的当前路径下生成一个 xxx.log 日志文件。 如果只配置 logging.path，在 /var/log文件夹生成一个日志文件为 spring.log 。二者不能同时使用，如若同时使用，则只有logging.file生效 

##### 1.4.4 自定义日志配置

根据不同的日志系统，你可以按如下规则组织配置文件名，就能被正确加载： 

- Logback：`logback-spring.xml`, `logback-spring.groovy`, `logback.xml`, `logback.groovy`
- Log4j：`log4j-spring.properties`, `log4j-spring.xml`, `log4j.properties`, `log4j.xml`
- Log4j2：`log4j2-spring.xml`, `log4j2.xml`
- JDK (Java Util Logging)：`logging.properties`
- 

### 2 自定义配置  

#### 2.1 myproperties.properties

```properties
--resources 下定义 myproperties.properties
myconfig.name=richard_11
myconfig.age=1811
```

```java
@Component
/*自定义配置文件的路径*/
@PropertySource(value = "classpath:myconfig.properties")
@ConfigurationProperties(prefix = "myconfig")
public class MyConfig {
 private String name;
 private Integer age;
}
--第二种方式
1 在application.properties中写自定义属性值
2 然后直接在要使用的地方通过注解@Value(value=”${config.name}”)就可以绑定到你想要的属性上面
	@Value("${myconfig.name}")
    private String name;
    @Value("${myconfig.age}")
    private Integer age;

    @GetMapping
    public String hello(){
        return "hello ,我的姓名 "+name+" ,我的年龄 "+age;
    }
--参数间引用--
    在application.properties中的各个参数之间也可以直接引用来使用，就像下面的设置,这样我们就可以只是用yearhope这个属性就好
    
com.dudu.name="嘟嘟MD"
com.dudu.want="祝大家鸡年大吉吧"
com.dudu.yearhope=${com.dudu.name}在此${com.dudu.want}

```

#### 2.2 生成随机值 

配置文件中${random} 可以用来生成各种不同类型的随机值，从而简化了代码生成的麻烦，例如 生成 int 值、long 值或者 string 字符串。 

```properties
myconfig.secret=${random.value}
myconfig.number=${random.int}
myconfig.bignumber=${random.long}
myconfig.uuid=${random.uuid}
myconfig.number.less.than.ten=${random.int(10)}
myconfig.number.in.range=${random.int[1024,65536]}
```



### 3 lombok的用法

```java
@AllArgsConstructor
@NoArgsConstructor
@Getter
@Setter
@Log4j
@Builder
/*lombok的builder的用法*/
MyConfig myConfig= MyConfig.builder().age(20).name("456").build();
```

### 4 springboot 单元测试

```java
/*2019 0803  使用springboot test  不能再引用junit的jar包*/
@SpringBootTest(classes = BootApplication.class)
@RunWith(SpringRunner.class)
```

### 5 springboot filter 过滤器

#### 5.1 第一种

```java
@WebFilter(filterName = "myFilter",urlPatterns = "/*")
public class MyFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) {
    }

    @Override
    public void destroy() {
    }
}

@SpringBootApplication
@EnableAutoConfiguration
@EnableWebMvc
@ServletComponentScan(basePackages = "com.fanyin.eghm")
public class EghmApplication {

    public static void main(String[] args) {
        SpringApplication.run(EghmApplication.class, args);
    }
}
```

- `@ServletComponentScan` 所扫描的包路径必须包含该Filter

#### 5.2 第二种

```java
@Configuration
public class FilterConfig {
    @Bean
    public FilterRegistrationBean filterRegistrationBean(){
        FilterRegistrationBean bean = new FilterRegistrationBean();
        bean.setFilter(new MyFilter2());
        bean.addUrlPatterns("/*");
        return bean;
    }
}

```

#### 5.3 第三种

```java
@Bean("proxyFilter")
    public Filter filter (){
        return new Filter() {
            @Override
            public void init(javax.servlet.FilterConfig filterConfig) throws ServletException {
            }

            @Override
            public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) {
            }

            @Override
            public void destroy() {
            }
        };
    }

    @Bean
    public DelegatingFilterProxyRegistrationBean delegatingFilterProxyRegistrationBean(){
        DelegatingFilterProxyRegistrationBean bean = new DelegatingFilterProxyRegistrationBean("proxyFilter");
        bean.addUrlPatterns("/*");
        return bean;
    }
```

#### 5.4 我的例子

```java


public class FilterController implements Filter {
	@Override
	public void init(FilterConfig filterConfig) throws ServletException {
		System.out.println("----------------------->过滤器被创建");
	}
	
	@Override
	public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
		HttpServletRequest req = (HttpServletRequest) servletRequest;
		String requestURI = req.getRequestURI();
		System.out.println("--------------------->过滤器：请求地址"+requestURI);
		/*if(!requestURI.contains("info")){
			servletRequest.getRequestDispatcher("/failed").forward(servletRequest, servletResponse);
		}else{
			filterChain.doFilter(servletRequest, servletResponse);
		}*/
		filterChain.doFilter(servletRequest, servletResponse);
	}
	
	@Override
	public void destroy() {
		System.out.println("----------------------->过滤器被销毁");
	}
}
```

```java
@Configuration
public class FilterConfig {
 @Bean
 public FilterRegistrationBean filterRegistrationBean(){
  FilterRegistrationBean bean=new FilterRegistrationBean();
  FilterController filterController=new FilterController();
  bean.setFilter(filterController);
  /*过滤所有的url请求*/
  bean.addUrlPatterns("/*");
  return  bean;
 }
}
```

```java
@RestController
@RequestMapping(value = "/filter")
public class helloController {
 
 @GetMapping(value = "/success/info")
 public User UserInfo(){
  User user= User.builder().name("哈哈").age(18).build();
  return  user;
 }
 
 @GetMapping(value = "/failed")
 public Map<String,String>requestfailed(){
  Map<String,String>map=new HashMap<>();
  map.put("code","-1");
  map.put("message","请求错误");
  return map;
 }
 
 @GetMapping
 public String filter(String name){
  return "hello filter "+name;
 }
}
```

### 6 springboot 异步线程池

#### 6.1 java自带的线程池

```java
@RestController
@RequestMapping("/executor")
public class helloController {
 ExecutorService executorService= Executors.newFixedThreadPool(10);
 @GetMapping
 public String executor(){
  /*执行异步的任务*/
  executorService.execute(new Runnable() {
   @Override
   public void run() {
    try {
     TimeUnit.SECONDS.sleep(3);
    }catch (Exception e){
     e.printStackTrace();
    }
    /*3秒后再执行的任务。。*/
    System.out.println("3秒后醒了。。。");
   }
  });
  return "hello executor";
 }
}
```

#### 6.2 taskexecutor

```java
--spring的异步线程池 taskexecutor
@Configuration
public class TaskExecutorConfig {
 @Bean
 public org.springframework.core.task.TaskExecutor mytaskExecutor(){
  ThreadPoolTaskExecutor taskExecutor=new ThreadPoolTaskExecutor();
  taskExecutor.setCorePoolSize(10);
  taskExecutor.setMaxPoolSize(20);
  return taskExecutor;
 }
}
//调用实例
@Autowired
	private TaskExecutorConfig taskExecutorConfig;
	@GetMapping("/executor2")
	public String test02(){
		taskExecutorConfig.mytaskExecutor().execute(new Runnable(){
			@Override
			public void run() {
				try {
					TimeUnit.SECONDS.sleep(5);
				}catch (Exception e){
					e.printStackTrace();
				}
				/*3秒后再执行的任务。。*/
				System.out.println("5秒后醒了。。。");
			}
		});
		System.out.println("hello executor2");
		return "hello executor2 ";
	}
```

#### 6.3 My实例

```java
--Service
@Service
@Async
public class AsyncService {
 public static Random random=new Random();
 

 public void doTaskOne() throws Exception {
  System.out.println("开始做任务一");
  long start = System.currentTimeMillis();
  Thread.sleep(random.nextInt(10000));
  long end = System.currentTimeMillis();
  //System.out.println(stringRedisTemplate.randomKey());
  System.out.println("完成任务一，耗时：" + (end - start) + "毫秒");
 }
 public void doTaskThree() throws Exception {
  System.out.println("开始做任务三");
  long start = System.currentTimeMillis();
  Thread.sleep(random.nextInt(10000));
  long end = System.currentTimeMillis();
  System.out.println("完成任务三，耗时：" + (end - start) + "毫秒");
 }

 public void doTaskTwo() throws Exception {
  System.out.println("开始做任务二");
  long start = System.currentTimeMillis();
  Thread.sleep(random.nextInt(10000));
  long end = System.currentTimeMillis();
  System.out.println("完成任务二，耗时：" + (end - start) + "毫秒");
 }
}
--Utils
@EnableAsync
@Configuration
public class TaskExecutorConfig {
	@Bean
	public org.springframework.core.task.TaskExecutor mytaskExecutor(){
		ThreadPoolTaskExecutor taskExecutor=new ThreadPoolTaskExecutor();
		taskExecutor.setCorePoolSize(10);
		taskExecutor.setMaxPoolSize(20);
		return taskExecutor;
	}
}

--Controller
@Autowired
	private AsyncService asyncService;
	@GetMapping("/executor3")
	public String test03(){
		try {
			asyncService.doTaskOne();
			asyncService.doTaskTwo();
			asyncService.doTaskThree();
		} catch (Exception e) {
			e.printStackTrace();
		}
		System.out.println("hello executor3");
		return "hello executor3";
	}

```

### 7 spring aop

### 8 spirng 全局异常处理

```java
--utils


@RestControllerAdvice
public class Exception {
	
	/*对于空指针异常的情况*/
	/*@ExceptionHandler(value = {NullPointerException.class})
	public ResCode execption(){
		ResCode resCode= ResCode.builder().code("001").msg("空指针异常").build();
		return resCode;
	}*/
	
	@ExceptionHandler(value ={NullPointerException.class} )
	public ModelAndView modelAndView(){
		/*返回哪个页面 自定义*/
		return  new ModelAndView("");
	}
	/*统一的异常处理*/
	@ExceptionHandler(value = {Throwable.class})
	public ResCode executor(Throwable throwable)
	{
		ResCode resCode= ResCode.builder().code("500").msg("未知错误").build();
		return resCode;
	}
	
	
	@Getter
	@Setter
	@Builder
	public static class ResCode implements Serializable{
		private String code;
		private String msg;
	}
}

--测试controller

@RestController
@RequestMapping("/execption")
public class ExecptionController {
	
	@GetMapping
	public String test01(String name){
		System.out.println(name.length());
		System.out.println(1/0);
		return "hello execption";
	}
}
```

### 9 springboot 打包部署

```xml
--maven 打包，先clean  再package
<build>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
    </plugin>
  </plugins>
</build>
--可以以下命令修改tomcat端口号--
修改端口后的方法  java -jar ***.jar --server.port=8080
```

### 10 springboot 数据源配置

#### 10.1 liquibase 应用

~~~xml
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>org.liquibase</groupId>
            <artifactId>liquibase-core</artifactId>
        </dependency>
		<dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>${alibaba-druid.version}</version>
        </dependency>
~~~



~~~properties
# liquibase 配置
spring.liquibase.enabled=true
spring.liquibase.change-log=classpath:/db/changelog/db.changelog-master.xml
~~~

~~~xml
<?xml version="1.0" encoding="UTF-8"?>

<!--
  ~ Copyright (c) 2017. Shanghai Zhenhui Information Technology Co,. ltd.
  ~ All rights are reserved.
  -->
<databaseChangeLog
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
        xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-3.1.xsd">
    <include file="classpath:db/changelog/db.changelog-master.xml" relativeToChangelogFile="false"/>
</databaseChangeLog>
~~~

~~~xml
<?xml version="1.0" encoding="utf-8"?>

<databaseChangeLog
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
        xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
                      http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-3.4.xsd">
    <property name="now" value="now()" dbms="mysql,h2"/>
    <property name="now" value="sysdate" dbms="oracle"/>
    <property name="autoIncrement" value="true" dbms="mysql,h2,postgresql,oracle"/>
    <property name="amount" value="decimal(20,2)"/>

    <changeSet id="201907201637001" author="richard.cui">
        <createTable tableName="sys_inst_mail" remarks="新费控系统邮件表">
            <column name="ID" remarks="主键id" type="bigint" autoIncrement="true" >
                <constraints nullable="false" primaryKey="true"  primaryKeyName="pk_sys_inst_mail"  />
            </column>
            <column name="MAIL_SYSID" remarks="应用系统ID" type="varchar(30)"></column>
            <column name="MAIL_UID" remarks="业务系统ID" type="varchar(30)"></column>
            <column name="MAIL_SERVICENAME" remarks="服务名" type="varchar(30)"></column>
            <column name="MAIL_SUBJECT" remarks="邮件主题" type="varchar(512)">
                <constraints nullable="false"/>
            </column>
            <column name="MAIL_CONTENT" remarks="邮件正文" type="longtext"></column>
            <column name="MAIL_FROM" remarks="发件人" type="varchar(50)">
                <constraints nullable="false"/>
            </column>
            <column name="MAIL_TO" remarks="收件人" type="varchar(512)">
                <constraints nullable="false"/>
            </column>
            <column name="MAIL_CC" remarks="抄送地址" type="varchar(512)"></column>
            <column name="MAIL_BCC" remarks="密送地址" type="varchar(512)"></column>
            <column name="MAIL_FILEPATH" remarks="附件路径" type="varchar(512)"></column>
            <column name="MAIL_FILENAME" remarks="附件名称" type="varchar(512)"></column>
            <column name="MAIL_MEMO" remarks="备注信息" type="varchar(512)"></column>
            <column name="MAIL_IMMEDIATE" remarks="是否立即发送(1立即发送)" type="varchar(20)" defaultValue="1">
                <constraints nullable="false"/>
            </column>
            <column name="MAIL_TM_CREATE" remarks="邮件创建时间" type="datetime"></column>
        </createTable>
    </changeSet>

    <changeSet id="201907221318002" author="richard.cui">
        <addColumn tableName="sys_inst_mail">
            <column name="MAIL_HAS_RESENT_NUM" remarks="重复发送次数" type="int" defaultValue="0"></column>
        </addColumn>
    </changeSet>
    <changeSet id="201907221318003" author="richard.cui">
        <createTable tableName="sys_inst_mail_log" remarks="新费控系统邮件日志表">
            <column name="ID" remarks="主键id" type="bigint" autoIncrement="true">
                <constraints nullable="false" primaryKey="true"  primaryKeyName="pk_sys_inst_mail_log"  />
            </column>
            <column name="SYS_INST_MAIL_ID" remarks="邮件表的ID" type="bigint"></column>
            <column name="MAIL_NO" remarks="邮件编号" type="varchar(30)"></column>
            <column name="MAIL_SYSID" remarks="应用系统ID" type="varchar(30)"></column>
            <column name="MAIL_UID" remarks="业务系统ID" type="varchar(30)"></column>
            <column name="MAIL_SERVICENAME" remarks="服务名" type="varchar(30)"></column>
            <column name="MAIL_SUBJECT" remarks="邮件主题" type="varchar(512)">
                <constraints nullable="false"/>
            </column>
            <column name="MAIL_CONTENT" remarks="邮件正文" type="longtext"></column>
            <column name="MAIL_FROM" remarks="发件人" type="varchar(50)">
                <constraints nullable="false"/>
            </column>
            <column name="MAIL_TO" remarks="收件人" type="varchar(512)">
                <constraints nullable="false"/>
            </column>
            <column name="MAIL_CC" remarks="抄送地址" type="varchar(512)"></column>
            <column name="MAIL_BCC" remarks="密送地址" type="varchar(512)"></column>
            <column name="MAIL_FILEPATH" remarks="附件路径" type="varchar(512)"></column>
            <column name="MAIL_FILENAME" remarks="附件名称" type="varchar(512)"></column>
            <column name="MAIL_MEMO" remarks="备注信息" type="varchar(512)"></column>
            <column name="MAIL_TM_CREATE" remarks="邮件创建时间" type="datetime"></column>
            <column name="MAIL_IMMEDIATE" remarks="是否立即发送(1立即发送)" type="varchar(20)" defaultValue="1">
                <constraints nullable="false"/>
            </column>
            <!--邮件平台返回的消息记录-->
            <column name="MAIL_STATUS" remarks="处理标识 1正常|2异常|0系统异常" type="varchar(10)"></column>
            <column name="MAIL_ERRORMASSAGE" remarks="错误信息正常（无）异常（有）" type="varchar(512)"></column>
            <column name="MAIL_RESULTCODE" remarks="处理结果SN01 邮件发送成功|FN01 邮件发送失败|B01 收件人被拉黑名单, 本次不作发送处理|E01 邮件发送服务异常|OTHER 未知错误" type="varchar(20)"></column>
            <column name="MAIL_RESULTDESC" remarks="处理结果 DESC" type="varchar(200)"></column>
            <column name="MAILSERVICE_TM_RETURN" remarks="邮件服务返回结果时间" type="datetime"></column>
        </createTable>
    </changeSet>

    <changeSet id="201907231522001" author="richard.cui">
        <addColumn tableName="sys_inst_mail">
            <column name="MAIL_HAS_ALARM_NUM" type="int" remarks="已经发送报警邮件的次数" defaultValue="0"></column>
        </addColumn>
    </changeSet>
</databaseChangeLog>
~~~



#### 10.2 application.properties

~~~properties
# DataSource settings
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.url = jdbc:mysql://localhost:3306/richard?useUnicode=true&serverTimezone=GMT&characterEncoding=utf8&useSSL=true&useAffectedRows=true
spring.datasource.username = root
spring.datasource.password = Aegon_54321
spring.datasource.driverClassName = com.mysql.jdbc.Driver

#连接池的配置信息
spring.datasource.initialSize=5
spring.datasource.minIdle=5
spring.datasource.maxActive=20

# 配置获取连接等待超时的时间
spring.datasource.maxWait=60000

# 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
spring.datasource.timeBetweenEvictionRunsMillis=60000

# 配置一个连接在池中最小生存的时间，单位是毫秒
spring.datasource.minEvictableIdleTimeMillis=300000
spring.datasource.validationQuery=SELECT 1 FROM DUAL
spring.datasource.testWhileIdle=true
spring.datasource.testOnBorrow=false
spring.datasource.testOnReturn=false

# 打开PSCache，并且指定每个连接上PSCache的大小
spring.datasource.poolPreparedStatements=true
spring.datasource.maxPoolPreparedStatementPerConnectionSize=20

# 配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙
spring.datasource.filters=stat,wall,log4j

# 通过connectProperties属性来打开mergeSql功能；慢SQL记录
spring.datasource.connectionProperties=druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000
~~~

#### 10.3 mybatis Mapper位置

~~~markdown
SpringBoot在支持MyBaties时需要将XML文件放在resources路径下，发现SpringBoot在打包的时候默认在Java目录下的文件只打包 .java的文件，所以把xml文件或者其他类型文件放在这个目录下，自然就会在项目运行的时候找不到了，所以需要做一些处理操作，让SpringBoot知道需要打包那些类型的文件 
Maven Build标签内添加配置：
<resources>
    <resource>
        <directory>src/main/java</directory>
        <includes>
            <include>**/*.xml</include>
        </includes>
    </resource>
</resources>
然后是配置文件中MyBaties指定XML文件路径的修改
#Mybatis Mapper映射文件地址
mybatis.mapper-locations=classpath*:com/richard/sys_inst_mail/mail/persistence/*.xml
mybatis.type-aliases-package=com.richard.sys_inst_mail.**.domain
~~~

#### 10.4 mybatis-plus

### 11 spring banner 

~~~markdown
--生成banner.txt 地址  然后放到resources路径下即可
https://www.bootschool.net/ascii
~~~

### 12 redis

#### 12.1 redisTemplate 

#### 12.2 stringRedisTemplate

~~~markdown
1. 两者的关系是StringRedisTemplate继承RedisTemplate。
2. 两者的数据是不共通的；也就是说StringRedisTemplate只能管理StringRedisTemplate里面的数据，RedisTemplate只能管理RedisTemplate中的数据。
3. SDR默认采用的序列化策略有两种，一种是String的序列化策略，一种是JDK的序列化策略。
StringRedisTemplate默认采用的是String的序列化策略，保存的key和value都是采用此策略序列化保存的。
RedisTemplate默认采用的是JDK的序列化策略，保存的key和value都是采用此策略序列化保存的。
~~~



~~~java

@RestController
@RequestMapping("/redis")
public class RedisController {

    @Autowired
    private RedisTemplate<Object,Object> redisTemplate;

    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    @GetMapping
    public String SetRedis(){

        redisTemplate.opsForValue().set("a","a1");
        stringRedisTemplate.opsForValue().set("aa","aa1");
        return "hello redis";
    }

    @GetMapping(value ="/map")
    public Map<String, String> map(){
         Map<String,String> map=new HashMap<>();
         map.put("a",redisTemplate.opsForValue().get("a").toString());
         map.put("aa",stringRedisTemplate.opsForValue().get("aa"));
         return map;
    }
}
~~~

#### 12.3 My redisTemplate

~~~java
原因是我在该实体类中添加了一个为了方便实例化该类用的构造函数，导致JVM不会添加默认的无参构造函数，而jackson的反序列化需要无参构造函数，因此报错。
Response实体类同理。解决：在实体类中补上一个无参构造器即可。

@NoArgsConstructor
@AllArgsConstructor
@Getter
@Setter
@Builder
public class AdminUser implements Serializable {
	--serialVersionUID  序列化插件
    private static final long serialVersionUID = 2718819894407743864L;
    private String username;
    private Integer age;
}


@Configuration
public class MyReidsTemplate {
    @Bean
    public RedisTemplate redisTemplate(RedisConnectionFactory redisConnectionFactory){
        RedisTemplate redisTemplate =new RedisTemplate<>();
        redisTemplate.setConnectionFactory(redisConnectionFactory);

        //序列化的方式 序列化成json
        GenericJackson2JsonRedisSerializer genericJackson2JsonRedisSerializer=new GenericJackson2JsonRedisSerializer();
        //序列化的方式 序列化成string
        StringRedisSerializer stringRedisSerializer=new StringRedisSerializer();

        //序列化处理 key value; haskey hakvalue
        redisTemplate.setKeySerializer(stringRedisSerializer);
        redisTemplate.setValueSerializer(genericJackson2JsonRedisSerializer);
        redisTemplate.setHashKeySerializer(stringRedisSerializer);
        redisTemplate.setHashValueSerializer(genericJackson2JsonRedisSerializer);
        //返回重写的类
        return  redisTemplate;
    }
}
--重写ReidsTemplate controller中直接使用即可。

@RestController
@RequestMapping("/redis")
public class RedisController {

    @Autowired
    private RedisTemplate<Object,AdminUser> redisTemplate;

    @Autowired
    private StringRedisTemplate stringRedisTemplate;


    @GetMapping
    public String SetRedis(){
        AdminUser adminUser= AdminUser.builder().age(18).username("richard").build();
        redisTemplate.opsForValue().set("aaa",adminUser);

        return "hello redis";
    }

    @GetMapping(value ="/map")
    public Map<String, Object> map(){
         Map<String,Object> map=new HashMap<>();
         map.put("aaa",redisTemplate.opsForValue().get("aaa"));
         return map;
    }
}
~~~

### 13  redisQueue

#### 13.1 redis生产者

~~~java

@RestController
@RequestMapping("/redis")
public class RedisController {
	
	@Autowired
	private RedisTemplate<Object, AdminUser> redisTemplate;
	
	@Autowired
	private StringRedisTemplate stringRedisTemplate;
	
	private static AtomicInteger atomicInteger=new AtomicInteger(1);
	
	@GetMapping
	public String SetRedis(){
		for(int i=0;i<10;i++){
			AdminUser adminUser= AdminUser.builder().age(atomicInteger.getAndIncrement()).username(UUID.randomUUID().toString()).build();
			//redisTemplate.opsForValue().set("aaa",adminUser);
			redisTemplate.opsForList().rightPush("my:list",adminUser);
		}
		return "hello redis";
	}
}
~~~

#### 13.2 redis消费者

~~~java
注意点：
1）消费者随着系统启动的时候就开始执行，为了防止阻塞系统启动，将其放入到一个线程中，不影响系统的正常启动。
2）在处理while(true)循环的时候，一定要考虑异常情况的发生，如果出现了异常，那么while循环就死掉了。增加try catch 块，则会防止循环死掉。
3）catch 块中，也增加sleep()的处理。
@Slf4j
@Service
public class RedisConsumer implements InitializingBean {
	@Autowired
	private RedisTemplate<Object, AdminUser> redisTemplate;
	
	//@PostConstruct //系统启动的时候就运行的注解
	public void myconsumer1(){
		AdminUser adminUser= redisTemplate.opsForList().leftPop("my:list");
		System.out.println(adminUser);
	}
	
	/*实现系统启动的时候就开始执行*/
	@Override
	public void afterPropertiesSet() throws Exception {
	new Thread(
			()->{
				while (true) {
					try {
						AdminUser adminUser = redisTemplate.opsForList().leftPop("my:list",10, TimeUnit.SECONDS);
						//System.out.println(adminUser);
						if(adminUser!=null){
							//todo 处理业务
							log.info("{}",adminUser);
						}
					}catch (Exception e){
						e.printStackTrace();
						try {
							TimeUnit.SECONDS.sleep(10);
						} catch (InterruptedException ex) {
							ex.printStackTrace();
						}
					}
				}
			}
	).start();
	}
}
~~~

### 14 springboot 缓存注解

#### 14.1 cache-redis

~~~java

@EnableCaching
@Configuration
public class MyRedisTemplate {
	
	@Bean
	public RedisTemplate redisTemplate(RedisConnectionFactory redisConnectionFactory){
		RedisTemplate redisTemplate=new RedisTemplate<>();
		redisTemplate.setConnectionFactory(redisConnectionFactory);
		//序列化的方式 序列化成json
		GenericJackson2JsonRedisSerializer genericJackson2JsonRedisSerializer=new GenericJackson2JsonRedisSerializer();
		//序列化的方式 序列化成string
		StringRedisSerializer stringRedisSerializer=new StringRedisSerializer();
		
		//序列化处理 key value; haskey hakvalue
		redisTemplate.setKeySerializer(stringRedisSerializer);
		redisTemplate.setValueSerializer(genericJackson2JsonRedisSerializer);
		redisTemplate.setHashKeySerializer(stringRedisSerializer);
		redisTemplate.setHashValueSerializer(genericJackson2JsonRedisSerializer);
		//返回重写的类
		return  redisTemplate;
	}
    --重写缓存类
	@Bean
	public CacheManager cacheManager(RedisTemplate redisTemplate){
		RedisCacheManager redisCacheManager=new RedisCacheManager(redisTemplate);
		/*设置一个默认的过期时间 单位是s */
		redisCacheManager.setDefaultExpiration(10*60);
		return  redisCacheManager;
	}
}
~~~

写一个servcie

~~~java
--缓存的存放 刷新 删除；

@Service
public class CacheService {
	/*value 是指业务名称  */
	@Cacheable(value = "names",key = "'names:'+#name")
	public AdminUser adminUser(String name){
		/*实际的业务场景应该是从数据库或别的地方将数据取到  然后再放到缓存中  select  * from db; */
		AdminUser adminUser= AdminUser.builder().username(name).build();
		/*如果缓存中有的话，会直接从缓存中取值，如果缓存中没有，则先取值，再给缓存赋值*/
		System.out.println("我被执行了。。"+name);
		return adminUser;
	}
	/*cacheput  强制刷新缓存*/
	@CachePut(value = "names",key = "'names:'+#name")
	public AdminUser update(String name){
		AdminUser adminUser= AdminUser.builder().username(name+" update").build();
		System.out.println("我被执行了 update name:"+name);
		return adminUser;
	}
	
	/*@CacheEvict 缓存删除*/
	@CacheEvict(value = "names",key = "'names:'+#name")
	public AdminUser delete(String name){
		AdminUser adminUser= AdminUser.builder().username(name+" delete").build();
		System.out.println("我被执行了 delete name:"+name);
		return adminUser;
	}
}
~~~

写一个controller 业务调用

~~~java

@RestController
@RequestMapping(value = "/cache")
public class CacheController {
	
	@Autowired
	private CacheService cacheService;
	
	@GetMapping
	public AdminUser adminUser(String name){
		return cacheService.adminUser(name);
	}
	
	@GetMapping(value = "/hellocache")
	public String hellocache(){
		return "hello cache";
	}
	
	@GetMapping(value = "/cacheput")
	public AdminUser update(String name){
		return cacheService.update(name);
	}
	
	@GetMapping(value = "/Cacheevict")
	public AdminUser delete(String name){
		return cacheService.delete(name);
	}
}
~~~

2019年8月6日23:46:31

#### 14.2 redis-session

##### 14.2.1 引入依赖

~~~xml
 <!-- 共享session设置 -->
        <dependency>
            <groupId>org.springframework.session</groupId>
            <artifactId>spring-session-data-redis</artifactId>
        </dependency>
~~~

##### 14.2.2 session配置

~~~java
@Configuration
@EnableRedisHttpSession(maxInactiveIntervalInSeconds=86400*30) //设置session失效时间
public class SessionConfig {

}
~~~

##### 14.2.3 session测试

~~~java
@RequestMapping(value = "/sessionId")
    public String sessionId(HttpSession session) {
        //session操作
        String userName = (String) session.getAttribute("userName");
        if (userName == null) {
            userName = "richard";
        }
        session.setAttribute("userName", userName);
        return session.getId();
    }
~~~



### 15 路由网关 zuul

![1565233934346](D:\Work\OA_SourceCode\OA_Note\learn_note\springboot_learn.assets\1565233934346.png)



#### 15.1 1 网关产生

~~~markdown
在微服务应用中，客户端的一个请求可能需要多个微服务来提供数据。
1.  客户端多次请求不同的微服务，增加客户端的复杂性，
2. 认证复杂，每个服务都要进行认证；
3. http请求增加，效率不高；
4. 存在跨域请求，比较复杂。
所以，在客户端和服务端增加了API网关。所有的外部请求都先经过这个微服务网关，只需和微服务网关通信，而由网关进行多个微服务的调用。
~~~

#### 15.1.2 网关优点

~~~markdown
1. 减少客户端与微服务之间的调用次数，提高效率；
2. 便于监控，可在网关中监控数据，可以做统一切面任务处理；
3. 便于认证，只需要在网关进行认证即可，无需每个微服务都进行认证；
4. 降低客户端与服务端的耦合度。

~~~

#### 15.1.3 网关功能

~~~markdown
总结一下，服务网关大概就是四个功能：统一接入、流量管控、协议适配、安全维护，如图：
~~~

![1565231300801](D:\Work\OA_SourceCode\OA_Note\learn_note\springboot_learn.assets\1565231300801.png)

#### 15.1.4 **服务网关（Zuul）**

~~~markdown
在目前的网关解决方案里，有Nginx+ Lua、Kong、Spring Cloud Zuul等等，这里以Zuul为例进行说明，它是Netflix公司开源的一个API网关组件，Spring Cloud对其进行封装做到开箱即用，同时，Zuul还可以与Eureka、Ribbon、Hystrix等组件配合使用。简单说来，Zuul实现了两个功能：路由转发、过滤器：
~~~

##### 15.1.4.1 路由转发：接受请求，转发到后端服务

![1565231371805](D:\Work\OA_SourceCode\OA_Note\learn_note\springboot_learn.assets\1565231371805.png)

##### 15.1.4.2 过滤器：提供一系列过滤器完成权限、日志、限流等切面任务

#### 15.1.5 zuul 配置

##### 15.1.5.1 添加Zuul依赖，非常简单

![1565231470005](D:\Work\OA_SourceCode\OA_Note\learn_note\springboot_learn.assets\1565231470005.png)

~~~xml

~~~

##### 15.1.5.2 启动类

~~~java
--在启动类添加@EnableZuulProxy注解，开启路由功能 
@EnableZuulProxy
@EnableEurekaClient
@SpringBootApplication
public class ZuulApplication {
	public static void main(String[] args) {
		SpringApplication.run(ZuulApplication.class, args);
	}
}
~~~

##### 15.1.5.3 路由配置

~~~yaml
server:
  port: 7089
spring:
  application:
    name: zuulclient
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7071/eureka/
zuul:
  routes:
    api-a:
      path: /api-a/**
      serviceid: ribbon-consumer
    api-b:
      path: /api-b/**
      serviceid: feign-consumer
~~~

~~~

~~~

![1565234637962](D:\Work\OA_SourceCode\OA_Note\learn_note\springboot_learn.assets\1565234637962.png)

 ##### 15.1.5.4 测试

~~~markdown

~~~

 

### 16 Kong及Swagger简介

#### 16.1 Kong

~~~markdown

~~~

#### 16.2 Swagger   in  服务提供端

##### 16.2.1 pom.xml

~~~~xml
<dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <!--swagger -->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.9.0</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.9.0</version>
</dependency>
~~~~

##### 16.2.2 yml配置

~~~yaml
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:7071/eureka/
server:
  port: 7081
spring:
  application:
    name: ribbon-provider

# swagger开关
# swagger.enabled=true
swagger:
  enabled: true
~~~

##### 16.2.3 domain

~~~java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
@ApiModel
public class DemoReq {
    @ApiModelProperty(name="code",value="编码",example="oKong")
    String code;

    @ApiModelProperty(name="name",value="名称",example="趔趄的猿")
    String name;

    @ApiModelProperty(name="remark",value="备注",example="blog：blog.lqdev.cn")
    String remark;

}
~~~



##### 16.2.4 增加一个util类

~~~java

@Configuration
@EnableSwagger2
public class SwaggerConfig {
    //是否开启swagger，正式环境一般是需要关闭的，可根据springboot的多环境配置进行设置
    @Value(value = "${swagger.enabled}")
    Boolean swaggerEnabled;

    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2).apiInfo(apiInfo())
                // 是否开启
                .enable(swaggerEnabled).select()
                // 扫描的路径包
                .apis(RequestHandlerSelectors.basePackage("com.cloud.ribbon.ribbon"))
                // 指定路径处理PathSelectors.any()代表所有的路径
                .paths(PathSelectors.any()).build().pathMapping("/");
    }

    //设置api信息
    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("路由网关(Zuul)：利用swagger2聚合API文档-ribbon-provider")
                .description("我的博客 | 小猫吃鱼")
                // 作者信息
                .contact(new Contact("小猫吃鱼", "https://www.cnblogs.com/yachao1120/", "richardcui@aegonthtf.com"))
                .version("1.0.0")
                .build();
    }
}
~~~

##### 16.2.5 controller层

~~~java
@Api(tags = "ribbon_provider服务")
@RestController
@Slf4j
public class HelloController {
    @Autowired
    private HelloService helloService;

    @GetMapping("/hello")
    public String sayHello(String name){
        log.info("{}",name);
        return helloService.sayHello() + " " + name;
    }
    
    @GetMapping("/testfeign")
    public String testfeign(){
        return helloService.testfeign();
    }

    @GetMapping(value = "/request")
    @ApiOperation(value = "request示例")
    public DemoReq request(DemoReq demoReq){
        log.info("DemoReq: {}",demoReq);
        return  DemoReq.builder().code(demoReq.getCode())
                .name(demoReq.getName())
                .remark(demoReq.getRemark()).build();
    }
}
~~~

##### 16.2.6 测试

> 地址  <http://localhost:7081/swagger-ui.html>  

![1565244174742](D:\Work\OA_SourceCode\OA_Note\learn_note\springboot_learn.assets\1565244174742.png)



![1565244196244](D:\Work\OA_SourceCode\OA_Note\learn_note\springboot_learn.assets\1565244196244.png)

#### 16.3 swagger in zuul

##### 16.3.1 pom.xml

~~~xml
<properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>Greenwich.SR2</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <!--swagger -->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.9.0</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.9.0</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
~~~

##### 16.3.2 application.yml

~~~yaml
server:
  port: 7089
spring:
  application:
    name: zuulclient
  main:
    allow-bean-definition-overriding: true
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7071/eureka/

# 启用ip配置 这样在注册中心列表中看见的是以ip+端口呈现的
  instance:
    prefer-ip-address: true

zuul:
  routes:
    api-a:
      path: /api-a/**
      serviceid: ribbon-consumer
    api-b:
      path: /api-b/**
      serviceid: feign-consumer

swagger:
  enabled: true

~~~

##### 16.3.3 类swaggerConfig

~~~java

@EnableSwagger2
@Configuration
@Primary /*多个bean时  此类优先使用*/

public class SwaggerConfig implements SwaggerResourcesProvider {

    //是否开启swagger，正式环境一般是需要关闭的，可根据springboot的多环境配置进行设置
    @Value(value = "${swagger.enabled}")
    Boolean swaggerEnabled;
    @Autowired
    RouteLocator routeLocator;

    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2).apiInfo(apiInfo())
                // 是否开启
                .enable(swaggerEnabled).select()
                // 扫描的路径包
                .apis(RequestHandlerSelectors.basePackage("com.richard.microservice.zuul"))
                // 指定路径处理PathSelectors.any()代表所有的路径
                .paths(PathSelectors.any()).build().pathMapping("/");
    }

    //设置api信息
    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("路由网关(Zuul)：利用swagger2聚合API文档")
                .description("我的博客 | 小猫吃鱼")
                // 作者信息
                .contact(new Contact("小猫吃鱼", "https://www.cnblogs.com/yachao1120/", "richardcui@aegonthtf.com"))
                .version("1.0.0")
                .termsOfServiceUrl("https://www.cnblogs.com/yachao1120/")
                .build();
    }


    @Override
    public List<SwaggerResource> get() {
        //利用routeLocator动态引入微服务
        List<SwaggerResource> resources = new ArrayList<>();
        resources.add(swaggerResource("zuulclient","/v2/api-docs","1.0"));
        //循环 使用Lambda表达式简化代码
        routeLocator.getRoutes().forEach(route ->{
            //动态获取
            resources.add(swaggerResource(route.getId(),route.getFullPath().replace("**", "v2/api-docs"), "1.0"));
        });

//也可以直接 继承 Consumer接口
//		routeLocator.getRoutes().forEach(new Consumer<Route>() {
//
//			@Override
//			public void accept(Route t) {
//				// TODO Auto-generated method stub
//
//			}
//		});

        return resources;
    }

    private SwaggerResource swaggerResource(String name,String location, String version) {
        SwaggerResource swaggerResource = new SwaggerResource();
        swaggerResource.setName(name);
        swaggerResource.setLocation(location);
        swaggerResource.setSwaggerVersion(version);
        return swaggerResource;
    }
}
~~~

##### 16.3.4 启动类

~~~java

@ComponentScan
@EnableZuulProxy
@EnableEurekaClient
@SpringBootApplication
@Slf4j
public class ZuulApplication {

	public static void main(String[] args) {
		SpringApplication.run(ZuulApplication.class, args);
		log.info("zuul 启动。");
	}
}
~~~

##### 16.3.5 demoController

~~~java

@RestController
@RequestMapping("/demo")
@Api(tags="zuul内部rest api")
public class DemoController {

    @GetMapping("/hello")
    @ApiOperation(value="demo示例",notes="demo示例")
    @ApiImplicitParam(name="name",value="名称",example="oKong")
    public String hello(String name) {
        return "hi," + name + ",this is zuul api! ";
    }
}
~~~

##### 16.3.6 测试

> <http://localhost:7089/swagger-ui.html> 

![1565247192173](D:\Work\OA_SourceCode\OA_Note\learn_note\springboot_learn.assets\1565247192173.png)

### 17 方法重试

#### 17.1 pom.xml

~~~xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.springframework.retry/spring-retry -->
        <dependency>
            <groupId>org.springframework.retry</groupId>
            <artifactId>spring-retry</artifactId>
        </dependency>
~~~

#### 17.2 类RetryConfig

~~~java
@Service
public class RetryConfig {
    //重试次数
    @Retryable(maxAttempts = 3)
    public void output(){
        System.out.println("lalala");
        System.out.println(1/0);
    }
}
~~~

#### 17.4 测试类

```java
@RestController
@RequestMapping(value = "/retry")
public class RetryController {

    @Autowired
    private RetryConfig retryConfig;
    @GetMapping
    public String retry(){
        retryConfig.output();
        return "hello retry";
    }
}
--启动类
@SpringBootApplication
@MapperScan("com.richard.springboot.mybatis.**.persistence")
@ComponentScan(value="com.richard.springboot")
@EnableRetry
public class HelloworldApplication {
    public static void main(String[] args) {
        System.out.println("hello world 2019");
        SpringApplication.run(HelloworldApplication.class, args);
    }
}
```

### 18 rabbitMq

#### 18.1 本地配置

```markdown

#------------------------------------SpringBoot 服务名、端口号
spring.application.name=spring-boot-rabbitmq
server.port=8489
#------------------------------------RabbitMQ基本配置
# RabbitMQ的主机地址(默认为:localhost)
spring.rabbitmq.host=localhost
# 指定该用户要连接到的虚拟host端(注:如果不指定,那么默认虚拟host为“/”)
spring.rabbitmq.virtual-host =/
# amqp协议端口号:5672; 集群端口号:25672;http端口号:15672;
spring.rabbitmq.port=5672
# 登录到RabbitMQ的用户名、密码
spring.rabbitmq.username=richardcui
spring.rabbitmq.password=Abcd1234

#------------------------------------RabbitMQ可选配置(注:这里只用到了特别少的几个)
# broker端没有收到消费者的ACK(即:消费者异常时)时,是否再次向消费者投递消息(默认为false)
# 为false时，如果没有收到消费者的ACK，那么会无限投递;设置为true时,默认投递时次数为3此
spring.rabbitmq.listener.simple.retry.enabled=true
# 设置向消费者投递消息的最大次数
spring.rabbitmq.listener.simple.retry.max-attempts=2
# 投递消息的间隔(单位ms)
spring.rabbitmq.listener.simple.retry.initial-interval=2000
```

### 19 springboot restful

#### 19.1 测试类

~~~java

@RestController
@RequestMapping(value = "/restful")
@Slf4j
public class RestfunController {
	//10.72.9.59:8010/restful/get?msg=123,postman
    @GetMapping("/get")
    public String get(String msg){
      log.info("get方式！");
      return msg;
    }

    @PostMapping(value = "/post")
    public String post(@RequestBody String msg){
        log.info("post方式!");
        return msg;
    }

    @PutMapping(value = "/put")
    public String put(@RequestBody String msg){
        log.info("put方式！");
        return msg;
    }

    @DeleteMapping(value = "/delete")
    public String delete(String msg){
        log.info("delete 方式!");
        return "delete "+msg+" success";
    }

    @GetMapping(value = "/status")
    @ResponseStatus(HttpStatus.EXPECTATION_FAILED)
    public String status(String msg){
        log.info("status 方式!");
        return msg;
    }
}
~~~

#### 19.2 postman测试

~~~
get
--10.72.9.59:8010/restful/status?msg=hello,postman

post 
--10.72.9.59:8010/restful/post
--body中的内容
{
	"msg","hello,postman"
}

--put 
--10.72.9.59:8010/restful/put
--body中的内容
{
	"msg","hello,postman"
}
--delete 
--10.72.9.59:8010/restful/delete?msg=hello,postman

此时，可以把各请求保存到Collections中，可以测试下批量执行的功能run.
~~~

![1565334805927](D:\Work\OA_SourceCode\OA_Note\learn_note\springboot_learn.assets\1565334805927.png)

### 20 restTemplate  

```java
RestTemplate restTemplate=new RestTemplate();
@GetMapping(value = "/hello")
public String getHello(){
 String sreturn= restTemplate.getForObject("http://localhost:8010/api/hello",String.class);
 return sreturn;
}
```

```java
/**
 * RestTemplate配置类
 */
@Configuration
public class RestTemplateConfig {

    @Bean
    public RestTemplate restTemplate(ClientHttpRequestFactory factory){
        return new RestTemplate(factory);
    }

    @Bean
    public ClientHttpRequestFactory simpleClientHttpRequestFactory(){
        SimpleClientHttpRequestFactory factory = new SimpleClientHttpRequestFactory();
        factory.setReadTimeout(30000);   //单位为ms
        factory.setConnectTimeout(30000);//单位为ms
        return factory;
    }
}

类中注入RestTemplate
@Autowired
private RestTemplate restTemplate;

```

### 21 springboot 定时任务

```java
@Slf4j
@Service
@EnableScheduling
public class MyJob {
 @Scheduled(cron = "${cron.job1}")
 public void job1(){
  log.info("1");
 }
}
```

```yaml
server:
  port: 8018
  context-path: /api
cron:
  job1: '* * * * * ?' # 秒 时 分 天 周 月
```