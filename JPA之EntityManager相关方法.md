## JPA之EntityManager相关方法

```java
public class MainTest {

	EntityManagerFactory entityManagerFactory = null;
	EntityManager entityManager = null;
	EntityTransaction transaction = null;
	
	@After
	public void commitEntityManager() {
		transaction.commit();
		entityManager.close();
		entityManagerFactory.close();
	}
	
	@Before
	public void beginEntiyManager() {
		entityManagerFactory = Persistence.createEntityManagerFactory("jpa");
		entityManager = entityManagerFactory.createEntityManager();
		transaction = entityManager.getTransaction();
		transaction.begin();
	}
	
	/**
	 * 测试EntityManager:
	 * pesist方法:与Hibernate的save效果相同
	 * 把瞬时态的对象持久化到数据库中
	 */
	@Test
	public void persistenceTest() {
		Customer customer = new Customer();
		customer.setName("APPLE");
		customer.setAddress("加利福尼亚");
		System.out.println(entityManager);
		//persist操作
		entityManager.persist(customer);
		//测试此时customer是否已经在entityManager管理下[经过测试当persist持久化之后，customer被entityManager管理中]
		customer.setAddress("美国硅谷");
	}
	
	/**
	 * 测试EntityManager:
	 * find方法:与Hibernate的get效果相同
	 * 从数据库中查询实体类到内存中[查询数据]
	 */
	@Test
	public void findTest() {
		//find操作
		Customer customer = entityManager.find(Customer.class, 3);
		System.out.println(customer);
		//因为此时已经是在entityManager管理中，所以修改数据后执行事物的commit操作后会被提交到数据库中[update操作]
		customer.setName("Microsoft");
	}
	
	
	/**
	 * 测试EntityManager:
	 * find方法:与Hibernate的load效果相同
	 * 从数据库中查询实体类到内存中[查询数据,是懒加载模式。当需要用到该实体对象时entityManager才会从数据库中
	 * 正式查询数据出来]
	 */
	@Test
	public void loadTest() {
		//getReference操作
		Customer customer = entityManager.getReference(Customer.class, 1);
		System.out.println("---------这条打印可以查看到其效果----------");
		//只要使用到了entityManager管理下的customer时,entityManager就会去发sql请求查询数据
		//System.out.println(customer);
		//entityManager.remove(customer);
	}
	
	/**
	 * 测试EntityManager:
	 * remove方法:与Hibernate的delete效果相同[hibernate中直接删除一个游离态对象是不会报错的]
	 * 从数据库中删除数据
	 */
	@Test
	public void removeTest() {
		Customer customer = new Customer();
		customer.setName("Microsoft");
		customer.setAddress("美国硅谷");
		customer.setId(3);
		//remove操作[此时删除的是一个游离态的customer对象,按照个人理解:JPA是一个ORM框架的规范，那么任何
		//对象都必须与数据库中的表对应。那么ORM框架必须通过该对象来映射数据表中每个记录的变化。所以要删除
		//对应的数据，那么实际上是删除ORM管理下的映射对象。所以直接通过remove去删除没有映射到的对象JPA会报异常]
		//entityManager.remove(customer);
		
		//所以此时需要从数据库中查询到对象后交给entityManager管理，删除entityManager管理中的对象，
		//当commit操作后entityManager对象会自动映射对数据库中的操作
		Customer find = entityManager.find(Customer.class, 3);
		entityManager.remove(find);
	}
	
	/**
	 * 测试EntityManager:
	 * merge方法:与Hibernate的saveOrUpdate效果相同
	 * 从数据库中保存或更新数据:返回更新或保存后的对象。
	 * 所以手动创建的对象和返回的对象不是同一个对象。这点从返回对象的Id值可以看出[返回的对象的id是自增的Id值]
	 */
	@Test
	public void mergeTest() {
		Customer customer = new Customer();
		customer.setName("Microsoft");
		customer.setAddress("美国硅谷");
		customer.setId(4);
		//通过实体类的主键ID来判断是更新还是保存
		//entityManager先通过实体类的主键ID去数据库中查询是否有该数据
		//如果查询到有该数据，然后查询数据到entityManager管理下，然后复制实体类数据
		//到entityManager管理下对应的对象中，最后更新该管理的对象到数据库中，返回更新后的对象
		Customer customer2 = entityManager.merge(customer);
		System.out.println(customer2);
		
		
		System.out.println("------------ 完美分割线 ------------");
		Customer google = new Customer();
		google.setName("google");
		google.setAddress("美国硅谷2区");
		google.setId(5);
		//通过实体类的主键ID来判断是更新还是保存
		//entityManager先通过实体类的主键ID去数据库中查询是否有该数据
		//如果查询没有该数据，则会将该实体对象放入entityManager管理下，然后持久到数据库中，返回持久化后管理的对象
		Customer google2 = entityManager.merge(google);
		System.out.println("google2#id->"+google2);
		System.out.println("google#id->"+google);
		
	}
	
	
	
	/**
	 * --------------------  entityManager 对象其他方法  --------------------
	 */
	
	@Test
	public void flushTest() {
		//将entityManager管理下的对象刷新至数据库中[修改或未修改的]
		//此时还未commit，所以数据库中的数据是不会有什么变化的
		entityManager.flush();
	}

	
	@Test
	public void refreshTest() {
		Customer customer = new Customer();
		customer.setAddress("美国硅谷2区");
		customer.setId(8);
		customer.setName("google");
		// 将customer对象关联至entityManager中，交给entityManager缓存
		customer = entityManager.getReference(Customer.class, customer.getId());
		//此时如果数据库有变化，我们可以通过refresh方法，将数据库中的变化刷新到该对象中
		entityManager.refresh(customer);
	}
	
	@Test
	public void containsTest() {
		Customer customer = entityManager.getReference(Customer.class,8);
		//查看实体对象是否在entityManager管理下
		boolean isContains = entityManager.contains(customer);
		System.out.println("entityManager管理下是否存在customer对应的映射对象:"+isContains);
	}
}
```