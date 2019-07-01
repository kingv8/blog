# JPA 实体管理器使用小结
### 一、声明实体

@Entity : 标识这个实体类是一个JPA实体，告诉JPA在程序运行的时候记得生成这个实体类所对应的表~
@Table : 声明此对象映射到数据库的数据表，通过它可以为实体指定表(talbe),目录(Catalog)和schema的名字。该注释不是必须的，如果没有则系统使用默认值(实体的短类名)。
@Version : 该注释可用于在实体Bean中添加乐观锁支持。

### 二、声明主键
@Id : 声明此属性为主键。该属性值可以通过应该自身创建，但是Hibernate推荐通过Hibernate生成
@GeneratedValue : 设置主键的生成策略，这种方式依赖于具体的数据库，如果数据库不支持自增主键，那么这个类型是没法用的。有如下四个值
TABLE：使用表保存id值
IDENTITY：identitycolumn
SEQUENCR ：sequence
AUTO：根据数据库的不同使用上面三个

### 三、声明普通属性
@Column : 声明该属性与数据库字段的映射关系。
@Column（name = "自定义字段名"，length = "自定义长度"，nullable = "是否可以空"，unique = "是否唯一"，columnDefinition = "自定义该字段的类型和长度"）
```
@Column(nam=”category_name” length=20)
Public void getCategoryName(){
	Return this.categoryName;
}  
```
@Transitent : 表示该属性并非一个到数据库表的字段的映射,ORM框架将忽略该属性. 如果一个属性并非数据库表的字段映射,就务必将其标示为 @Transient ,否则,ORM框架默认其注解为 @Basic

@JsonIgnore : json序列化时将java bean中的一些属性忽略掉，序列化和反序列化都受影响

@Temporal
当我们使用到java.util包中的时间日期类型，则需要@Temporal注释来说明转化成java.util包中的类型。其中包含三种转化类型分别是：
java.sql.Date日期型，精确到年月日，例如“2008-08-08”
java.sql.Time时间型，精确到时分秒，例如“20:00:00”
java.sql.Timestamp时间戳，精确到纳秒，例如“2008-08-08 20:00:00.000000001”
```
@Temporal(TemporalType.DATE)\\@Temporal(TemporalType.TIME)   @Temporal(TemporalType.TIMESTAMP)
private Date enableTime;
```
### 四、声明关联关系
 一对多关联关系
 ```
 @OneToMany(cascade = CascadeType.REFRESH, fetch = FetchType.EAGER)
 @JoinColumn(name = "buildingId", foreignKey = @ForeignKey(name = "none", value = ConstraintMode.NO_CONSTRAINT), insertable = false, updatable = false)
 private List<Floor> childrens;
 ```
 多对一关联关系
 ```
 @ManyToOne(cascade = CascadeType.REFRESH, fetch = FetchType.EAGER)
 @JoinColumn(name = "personGroupCategoryId", updatable = false, insertable = false)
 @NotFound(action = NotFoundAction.IGNORE)
 private PersonGroupCategory personGroupCategory;
```
 一对一关联关系
 ```
 @JsonIgnore
 @OneToOne(cascade = CascadeType.REFRESH, fetch = FetchType.EAGER)
 @JoinColumn(name = "PersonId", insertable = false, updatable = false)
 @NotFound(action = NotFoundAction.IGNORE)
 private Person person;
 ```
 多对多关联关系
 ```
 @ManyToMany(cascade = CascadeType.REFRESH, fetch = FetchType.EAGER)
 @JoinTable(name = "PersonDutyMap", joinColumns = @JoinColumn(name = "PersonId"), inverseJoinColumns = @JoinColumn(name = "DutyId"))
 private Set<Duty> dutyList;
```

@NotFound(action=NotFoundAction.IGNORE)，意思是找不到引用的外键数据时忽略，NotFound默认是exception 
使用hibernate 注解配置实体类的关联关系，在many-to-one,one-to-one等关联中，一边引用自另一边的属性，如果属性值为某某的数据在数据库不存在了，hibernate默认会抛出异常。解决此问题，加上此注解就可以了


### JPA 实体管理器文档:https://www.w3cschool.cn/java/jpa-entitymanager.html
           

 

        

 

           

 

               

 

              

 

               

 

               

 

        

 

       

 

        

 

           

