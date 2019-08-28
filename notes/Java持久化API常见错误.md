### API 本身，定义在 javax.persistence 包内

问题1:

代码:
```
@OneToMany(cascade = CascadeType.REFRESH, fetch = FetchType.EAGER, orphanRemoval = true)
	@JoinColumns({
			@JoinColumn(name ="equipmentTemplateId", referencedColumnName="equipmentTemplateId", insertable =false, updatable = false),
			@JoinColumn(name ="signalId", referencedColumnName="signalId", insertable =false, updatable = false)
	})
	@NotFound(action = NotFoundAction.IGNORE)
	private List<SignalMeanings> signalMeanings;
```
根据主键id查询时,如果查询的对象signalMeanings没有数据时,会报错
```
{
    "message": "Internal server error",
    "exception": "Error accessing field [private java.lang.Integer com.phoenix.platform.monitoring.domain.siteweb.Signal.equipmentTemplateId] by reflection for persistent property [com.phoenix.platform.monitoring.domain.siteweb.Signal#equipmentTemplateId] : 325; nested exception is org.hibernate.property.access.spi.PropertyAccessException: Error accessing field [private java.lang.Integer com.phoenix.platform.monitoring.domain.siteweb.Signal.equipmentTemplateId] by reflection for persistent property [com.phoenix.platform.monitoring.domain.siteweb.Signal#equipmentTemplateId] : 325"
}
```

当fetch = FetchType.EAGER时, 如果signalMeanings为空,会报错

解决方法: 去掉fetch = FetchType.EAGER


JoinColumn中, name代表的是本实体类的字段, referencedColumnName代表关联表对应的字段

问题2:

代码:
```
@OneToMany(cascade = CascadeType.REFRESH, fetch = FetchType.EAGER, orphanRemoval = true)
	@JoinColumns({
			@JoinColumn(name ="equipmentTemplateId", referencedColumnName="equipmentTemplateId", insertable =false, updatable = false),
			@JoinColumn(name ="signalId", referencedColumnName="signalId", insertable =false, updatable = false)
	})
	@NotFound(action = NotFoundAction.IGNORE)
	private List<SignalMeanings> signalMeanings;
```
保存时出错

```
{
    "message": "Internal server error",
    "exception": "A collection with cascade=\"all-delete-orphan\" was no longer referenced by the owning entity instance: com.phoenix.platform.monitoring.domain.siteweb.Signal.signalMeanings; nested exception is org.hibernate.HibernateException: A collection with cascade=\"all-delete-orphan\" was no longer referenced by the owning entity instance: com.phoenix.platform.monitoring.domain.siteweb.Signal.signalMeanings"
}
```
原因:signalMeanings有可能为空,而OneToMany的设置中orphanRemoval = true,orphanRemoval是一个完全ORM特定的东西。当signalMeanings有可能为空为空时,出现上面错误,

解决方法,去掉orphanRemoval = false

问题3

代码:
```
@Override
    public void deleteById(Integer tblSignalId) {
        Signal signal = tblSignalRepository.findOne(tblSignalId);
        tblSignalRepository.delete(tblSignalId);
        if(signal.getSignalMeanings()!=null){
            for(SignalMeanings signalMeanings : signal.getSignalMeanings()){
                signalMeaningsRepository.delete(signalMeanings.getId());
            }
        }
    }
```

报错:Unknown collection role: null
猜测: tblSignalRepository.findOne(tblSignalId); 和 tblSignalRepository.delete(tblSignalId);异步执行,后者比前者执行的快

因为debug的时候不会报这个错,直接运行就会
