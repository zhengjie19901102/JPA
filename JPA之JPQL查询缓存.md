## JPA之JPQL查询缓存

`原始代码`

```java
String sql = "SELECT t FROM TestJPQL t WHERE id = ?";
Query setHint = entityManager.createQuery(sql);
setHint.setParameter(1, 1);
List resultList = setHint.getResultList();
System.out.println(resultList);

Query setHint2 = entityManager.createQuery(sql);
setHint2.setParameter(1, 1);
List resultList2 = setHint2.getResultList();
System.out.println(resultList2);
```

测试结果会发送两条SQL语句。

此时为了执行效率，需要除去重复发送的SQL，需要添加查询缓存。

在`persistence.xml`配置文件中`<properties>`节点中添加:

```xml
...
<properties>
	...
	<!--开启查询缓存-->
	<property name="hibernate.cache.use_query_cache" value="true"/>
</properties>
...
```

**`此时查询代码如下`**

```java
String sql = "SELECT t FROM TestJPQL t WHERE id = ?";
Query setHint = entityManager.createQuery(sql).setHint(QueryHints.HINT_CACHEABLE, true);
setHint.setParameter(1, 1);
List resultList = setHint.getResultList();
System.out.println(resultList);

Query setHint2 = entityManager.createQuery(sql).setHint(QueryHints.HINT_CACHEABLE, true);
setHint2.setParameter(1, 1);
List resultList2 = setHint2.getResultList();
System.out.println(resultList2);
```

在需要使用查询缓存处的Query对象上调用`setHint方法`，并传入要使用查询缓存的俩个参数[`QueryHints.HINT_CACHEABLE, true`]。

`[注] QueryHints类以及被标注为deprecated`