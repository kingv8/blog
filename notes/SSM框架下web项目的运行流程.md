# SSM框架下web项目的运行流程
## 1.前言
```
   java程序员一枚，使用ssm框架有一段时间了，在此做个学习记录
```
## 2.SSM中各层的关系和作用
      
### &nbsp;&nbsp;2.1.持久层：Dao层（mapper层）（属于mybaits模块）
- DAO层：主要负责与数据库进行交互设计，原来处理数据的持久化工作。
- DAO层的设计首先是设计DAO的接口，也就是项目中的DAO包。
- 然后在Spring的xml配置文件中定义此接口的实现类，就可在其它的模块中调用此接口来进行数据业务的处理，而不用关心接口的具体实现是那个类，这里通常用到了反射机制，DAO层的jdbc.properties数据源配置，以及有关数据库连接的参数都在Spring的配置文件中进行配置
```
    <!-- spring-dao.xml -->
	<!-- DAO接口所在包名，Spring会自动查找其下的类 -->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
	 	<!--basePackage指定要扫描的包，在此包之下的映射器都会被搜索到。
		 可指定多个包，包与包之间用逗号或分号分隔-->
		<property name="basePackage" value="com.wxc.*.dao" />
		<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"></property>
	</bean>          
```

### &nbsp;&nbsp;2.2.业务层：Service层 （属于spring模块）
- Service层：主要负责业务模块的逻辑应用设计。
- Service层的设计首先是设计Service接口，通常是service+impl形式
- 接着在Spring的xml配置文件中配置其实现的关联，这样就可以在应用中直接调用service接口进行业务处理
- 最后在ServiceImpl类中通过调用DAO层已实现的接口，去实现对应Service具体的实现类
```
    <!--  spring-service.xml -->
    <!--  配置参与事务的类 -->
	 <aop:config>
		 <aop:pointcut id="allServiceMethod" expression="execution(* com.wxc.*.service.*.*(..))"/>
		 <aop:advisor pointcut-ref="allServiceMethod" advice-ref="TxAdvice" />
	 </aop:config>
	
	<!-- 使用声明方式配置事务 -->
	<tx:advice id="TxAdvice" transaction-manager="transactionManager">
	     <tx:attributes>
		       <tx:method name="*" propagation="REQUIRED" rollback-for="java.lang.Exception"/>
	     </tx:attributes>
 	</tx:advice>
```
### &nbsp;&nbsp;2.3.控制层/表现层: Controller层（handler层）（属于springMVC模块）
- Controller层：主要负责具体的业务模块流程控制
- Controller层通过调用Service层的接口来控制业务流程，控制的配置也同样是在Spring的xml中,针对具体的业务，会有不同的控制器。

### &nbsp;&nbsp;2.4.视图层：View层（属于springMVC模块）
- 负责前台jsp页面的显示，此层需要与Controller层结合起来开发
- jsp发送请求，controller接收请求，处理，返回，jsp回显数据

## 3.三层架构的运行机制
<img src="https://img-blog.csdn.net/20181018153647385?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FsYW5raW5ndjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="70%">

## 4.各层之间的联系
- DAO层，Service层这两个层次可以单独开发，耦合性很低。
- Controller层，view层耦合性比较高，因此要结合起来开发，也可以当成两层来开发（需要在开发前约定好，接受的参数名，参数类型，返回的参数格式内容），这样，在层与层之间我们只需要知道接口的定义，调用接口即可完成所需要的逻辑单元应用，项目会显得清晰简单。
- Service层是建立在DAO层之上的，在Controller层之下。因此Service层应该是既可以调DAO层的接口，又提供给Controller层的类来进行调用，它处于一个之间位置，每个模型都有一个service接口，每个接口分别封装各自的业务处理方法。

## 5.SSM框架实现web项目主要使用到以下技术

1.Spring：用到注解和自动装配，就是Spring的IOC(反向控制)和 AOP(面向切面编程).
- IOC:Inversion of Control控制反转，也叫（Dependency Injection）依赖注入，比如dao接口的实现不再是业务逻辑层调用工厂类去获取，而是通过容器（spring）来自动的为我们的业务层设置Dao的实现类，这样整个过程就反过来，以前是我们业务层主动去获取dao，而现在是dao主动被设置到业务逻辑层中来了，这个也就是反转控制的由来。

- spring有三种注入方式：
 &nbsp; &nbsp; 1.根据属性注入也叫set方法注入 
 &nbsp; &nbsp; 2.根据构造方法注入 
 &nbsp; &nbsp; 3.根据注解进行注入（推荐）
 
- Spring依赖注入的实现技术是：动态代理
 
- AOP面向切面编程将程序中的交叉业务逻辑（比如安全，日志，事务），封装成一个切面，然后注入到目标业务逻辑中去。
比如：很多方法都可能会抛异常，你要记录这个异常到日志中去，可以写个拦截器，在这个类中记录日志，在spring.xml中配置一个记录这些日志的方法的拦截器，在这个方法执行后调用这个拦截器，记录日志。这样就不用每次抛异常都要手动记录日志。spring的事务管理用到的就是AOP这样也可以提高程序的内聚性。

- AOP把软件系统分为两个部分：核心关注点和横切关注点。业务处理的主要流程是核心关注点，与之关系不大的部分是横切关注点。横切关注点的一个特点是，他们经常发生在核心关注点的多处，而各处都基本相似。比如权限认证、日志、事务处理

2.SpringMVC：用到了MVC模型，将流程控制代码放到Controller层处理，将业务逻辑代码放到Service层处理。
3.Mybatis：用到了与数据库打交道的层面，dao（mapper）层，放在所有的逻辑之后，处理与数据库的CRUD相关的操作。

比如你开发项目的时候，需要完成一个功能模块：

a.先写实体类entity，定义对象的属性，（可以参照数据库中表的字段来设置，数据库的设计应该在所有编码开始之前）。

b.写Mapper.xml（Mybatis），其中定义你的功能，对应要对数据库进行的那些操作，比如 insert、selectAll、selectByKey、delete、update等。

c.写Mapper.java/Dao.java，将Mapper.xml中的操作按照id映射成Java函数。实际上就是Dao接口，二者选一即可。

d.写Service.java，为控制层提供服务，接受控制层的参数，完成相应的功能，并返回给控制层。

e.写Controller.java，连接页面请求和服务层，获取页面请求的参数，通过自动装配，映射不同的URL到相应的处理函数，并获取参数，对参数进行处理，之后传给服务层。

f.写JSP页面调用，请求哪些参数，需要获取什么数据
