# MyBatis控制台输出sql

#### 在MyBatis配置中的sqlSessionFactory中加上下面一句配置
```
<property name="configLocation" value="classpath:mybatis-config.xml" />
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181120120905170.png)

可通过全局搜索sqlSessionFactory来找到代码位置进行配置
注意：如果项目中没有mybatis-config.xml配置，需要创建，代码如下：
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING" />
    </settings>
</configuration>
```
控制台输出效果如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181120121319436.png)