## JPA之JPQL子查询

```java
String jpql = "SELECT o FROM Order o WHERE o.user = (SELECT u FROM User u WHERE u.id = ?)";
List resultList = entityManager.createQuery(jpql).setParameter(1, 1).getResultList();
System.out.println(resultList);
```