## JPA之JPQL基础查询

**`实体类代码`**

```java
@NamedQuery(name="test_jpql",query="SELECT t FROM TestJPQL t")
@Entity
@Table(name="TEST_JPQL")
public class TestJPQL {
	
	private Integer id;
	private String name;
	private String address;
	
	public TestJPQL(String name, String address) {
		super();
		this.name = name;
		this.address = address;
	}

	public TestJPQL() {
		super();
	}

	@Id
	@GeneratedValue
	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getAddress() {
		return address;
	}

	public void setAddress(String address) {
		this.address = address;
	}

	@Override
	public String toString() {
		return "TestJPQL [id=" + id + ", name=" + name + ", address=" + address + "]";
	}
}
```

**`createQuery查询基础`**

```java
Query createQuery = entityManager.createQuery("SELECT t FROM TestJPQL t");

@SuppressWarnings("unchecked")
List<TestJPQL> resultList = createQuery.getResultList();
System.out.println(resultList);
```

代码中通过对象`TestJPQL`查询数据，调用`getResultList`返回一个List结果集

**`createQuery之new对象代码`**

```java
Query createQuery = entityManager.createQuery("SELECT new TestJPQL(t.name,t.address) FROM TestJPQL t");

@SuppressWarnings("unchecked")
List<TestJPQL> resultList = createQuery.getResultList();
System.out.println(resultList);
```

通过在语句中`new`一个对象，将对应元素以对应参数传入查询。`注意: 此时需要对实体类添加对应的有参构造器和和无参构造器`。

**`createNamedQuery代码`**

```java
Query createQuery = entityManager.createNamedQuery("test_jpql");

@SuppressWarnings("unchecked")
List<TestJPQL> resultList = createQuery.getResultList();
System.out.println(resultList);
```

在实体类需要添加`@NamedQuery`注解，`name`为`createNamedQuery`方法传入的参数，`query`为JPQL语句，可以有select部分，也没有没有。

**`createNativeQuery`**

```java
Query createQuery = entityManager.createNativeQuery("SELECT id FROM test_jpql WHERE id = ? ");
createQuery.setParameter(1, 1);
Object singleResult = createQuery.getSingleResult();
System.out.println(singleResult);
```

通过`createNativeQuery`方法，传入SQL查询。且只能返回一个元素[即只能查询指定行的某列数据],
所以通过`getSingleResult`查询出对应列的数据。