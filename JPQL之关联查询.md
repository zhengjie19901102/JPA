## JPQL之关联查询

```java
//通过LEFT OUTER JOIN FETCH o.user左外关联，与Hibernate类似
//建议加上FETCH，获取的结果集好处理
String jpql = "SELECT o FROM Order o LEFT OUTER JOIN FETCH o.user";
List<Order> singleResult = entityManager.createQuery(jpql).getResultList();
System.out.println(singleResult);
```