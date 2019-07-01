# SSM�����web��Ŀ����������
## 1.ǰ��
```
   java����Աһö��ʹ��ssm�����һ��ʱ���ˣ��ڴ�����ѧϰ��¼
```
## 2.SSM�и���Ĺ�ϵ������
      
### &nbsp;&nbsp;2.1.�־ò㣺Dao�㣨mapper�㣩������mybaitsģ�飩
- DAO�㣺��Ҫ���������ݿ���н�����ƣ�ԭ���������ݵĳ־û�������
- DAO���������������DAO�Ľӿڣ�Ҳ������Ŀ�е�DAO����
- Ȼ����Spring��xml�����ļ��ж���˽ӿڵ�ʵ���࣬�Ϳ���������ģ���е��ô˽ӿ�����������ҵ��Ĵ��������ù��Ľӿڵľ���ʵ�����Ǹ��࣬����ͨ���õ��˷�����ƣ�DAO���jdbc.properties����Դ���ã��Լ��й����ݿ����ӵĲ�������Spring�������ļ��н�������
```
    <!-- spring-dao.xml -->
	<!-- DAO�ӿ����ڰ�����Spring���Զ��������µ��� -->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
	 	<!--basePackageָ��Ҫɨ��İ����ڴ˰�֮�µ�ӳ�������ᱻ��������
		 ��ָ��������������֮���ö��Ż�ֺŷָ�-->
		<property name="basePackage" value="com.wxc.*.dao" />
		<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"></property>
	</bean>          
```

### &nbsp;&nbsp;2.2.ҵ��㣺Service�� ������springģ�飩
- Service�㣺��Ҫ����ҵ��ģ����߼�Ӧ����ơ�
- Service���������������Service�ӿڣ�ͨ����service+impl��ʽ
- ������Spring��xml�����ļ���������ʵ�ֵĹ����������Ϳ�����Ӧ����ֱ�ӵ���service�ӿڽ���ҵ����
- �����ServiceImpl����ͨ������DAO����ʵ�ֵĽӿڣ�ȥʵ�ֶ�ӦService�����ʵ����
```
    <!--  spring-service.xml -->
    <!--  ���ò���������� -->
	 <aop:config>
		 <aop:pointcut id="allServiceMethod" expression="execution(* com.wxc.*.service.*.*(..))"/>
		 <aop:advisor pointcut-ref="allServiceMethod" advice-ref="TxAdvice" />
	 </aop:config>
	
	<!-- ʹ��������ʽ�������� -->
	<tx:advice id="TxAdvice" transaction-manager="transactionManager">
	     <tx:attributes>
		       <tx:method name="*" propagation="REQUIRED" rollback-for="java.lang.Exception"/>
	     </tx:attributes>
 	</tx:advice>
```
### &nbsp;&nbsp;2.3.���Ʋ�/���ֲ�: Controller�㣨handler�㣩������springMVCģ�飩
- Controller�㣺��Ҫ��������ҵ��ģ�����̿���
- Controller��ͨ������Service��Ľӿ�������ҵ�����̣����Ƶ�����Ҳͬ������Spring��xml��,��Ծ����ҵ�񣬻��в�ͬ�Ŀ�������

### &nbsp;&nbsp;2.4.��ͼ�㣺View�㣨����springMVCģ�飩
- ����ǰ̨jspҳ�����ʾ���˲���Ҫ��Controller������������
- jsp��������controller�������󣬴������أ�jsp��������

## 3.����ܹ������л���
<img src="https://img-blog.csdn.net/20181018153647385?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FsYW5raW5ndjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="70%">

## 4.����֮�����ϵ
- DAO�㣬Service����������ο��Ե�������������Ժܵ͡�
- Controller�㣬view������ԱȽϸߣ����Ҫ�������������Ҳ���Ե�����������������Ҫ�ڿ���ǰԼ���ã����ܵĲ��������������ͣ����صĲ�����ʽ���ݣ����������ڲ����֮������ֻ��Ҫ֪���ӿڵĶ��壬���ýӿڼ����������Ҫ���߼���ԪӦ�ã���Ŀ���Ե������򵥡�
- Service���ǽ�����DAO��֮�ϵģ���Controller��֮�¡����Service��Ӧ���Ǽȿ��Ե�DAO��Ľӿڣ����ṩ��Controller����������е��ã�������һ��֮��λ�ã�ÿ��ģ�Ͷ���һ��service�ӿڣ�ÿ���ӿڷֱ��װ���Ե�ҵ��������

## 5.SSM���ʵ��web��Ŀ��Ҫʹ�õ����¼���

1.Spring���õ�ע����Զ�װ�䣬����Spring��IOC(�������)�� AOP(����������).
- IOC:Inversion of Control���Ʒ�ת��Ҳ�У�Dependency Injection������ע�룬����dao�ӿڵ�ʵ�ֲ�����ҵ���߼�����ù�����ȥ��ȡ������ͨ��������spring�����Զ���Ϊ���ǵ�ҵ�������Dao��ʵ���࣬�����������̾ͷ���������ǰ������ҵ�������ȥ��ȡdao����������dao���������õ�ҵ���߼��������ˣ����Ҳ���Ƿ�ת���Ƶ�������

- spring������ע�뷽ʽ��
 &nbsp; &nbsp; 1.��������ע��Ҳ��set����ע�� 
 &nbsp; &nbsp; 2.���ݹ��췽��ע�� 
 &nbsp; &nbsp; 3.����ע�����ע�루�Ƽ���
 
- Spring����ע���ʵ�ּ����ǣ���̬����
 
- AOP���������̽������еĽ���ҵ���߼������簲ȫ����־�����񣩣���װ��һ�����棬Ȼ��ע�뵽Ŀ��ҵ���߼���ȥ��
���磺�ܶ෽�������ܻ����쳣����Ҫ��¼����쳣����־��ȥ������д������������������м�¼��־����spring.xml������һ����¼��Щ��־�ķ����������������������ִ�к�����������������¼��־�������Ͳ���ÿ�����쳣��Ҫ�ֶ���¼��־��spring����������õ��ľ���AOP����Ҳ������߳�����ھ��ԡ�

- AOP�����ϵͳ��Ϊ�������֣����Ĺ�ע��ͺ��й�ע�㡣ҵ�������Ҫ�����Ǻ��Ĺ�ע�㣬��֮��ϵ����Ĳ����Ǻ��й�ע�㡣���й�ע���һ���ص��ǣ����Ǿ��������ں��Ĺ�ע��Ķദ�����������������ơ�����Ȩ����֤����־��������

2.SpringMVC���õ���MVCģ�ͣ������̿��ƴ���ŵ�Controller�㴦����ҵ���߼�����ŵ�Service�㴦��
3.Mybatis���õ��������ݿ�򽻵��Ĳ��棬dao��mapper���㣬�������е��߼�֮�󣬴��������ݿ��CRUD��صĲ�����

�����㿪����Ŀ��ʱ����Ҫ���һ������ģ�飺

a.��дʵ����entity�������������ԣ������Բ������ݿ��б���ֶ������ã����ݿ�����Ӧ�������б��뿪ʼ֮ǰ����

b.дMapper.xml��Mybatis�������ж�����Ĺ��ܣ���ӦҪ�����ݿ���е���Щ���������� insert��selectAll��selectByKey��delete��update�ȡ�

c.дMapper.java/Dao.java����Mapper.xml�еĲ�������idӳ���Java������ʵ���Ͼ���Dao�ӿڣ�����ѡһ���ɡ�

d.дService.java��Ϊ���Ʋ��ṩ���񣬽��ܿ��Ʋ�Ĳ����������Ӧ�Ĺ��ܣ������ظ����Ʋ㡣

e.дController.java������ҳ������ͷ���㣬��ȡҳ������Ĳ�����ͨ���Զ�װ�䣬ӳ�䲻ͬ��URL����Ӧ�Ĵ�����������ȡ�������Բ������д���֮�󴫸�����㡣

f.дJSPҳ����ã�������Щ��������Ҫ��ȡʲô����
