## JPA之JPQL中使用ORDER BY和GROUP BY

```java
String jpqlOrder = "SELECT o FROM OrderByAndGroupBy o ORDER BY o.id DESC";
Query createQuery = entityManager.createQuery(jpqlOrder);
List resultList = createQuery.getResultList();
System.out.println(resultList);

//Group By测试
String jpqlGroup = "SELECT count(*) FROM OrderByAndGroupBy o GROUP BY o.anotherId HAVING count(o.anotherId) > 3";
Query createQuery2 = entityManager.createQuery(jpqlGroup);
List resultList2 = createQuery2.getResultList();
System.out.println(resultList2);
```

使用方式与SQL一致。