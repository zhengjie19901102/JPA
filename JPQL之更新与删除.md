## JPQL之更新与删除

**`更新操作`**

```java
String jpql = "Update Order o SET o.orderName = ? WHERE id = ?";
int resultList = entityManager.createQuery(jpql).setParameter(1, "京东").setParameter(2, 1).executeUpdate();
System.out.println(resultList);
```

**`删除操作`**

```java
String jpql = "DELETE FROM Order o WHERE o.id = ?";
int executeUpdate = entityManager.createQuery(jpql).setParameter(1, 2).executeUpdate();
System.out.println(executeUpdate);
```