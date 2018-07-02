#JPA双向一对多关联关系

双向即，通过主表能查到从表的相关的信息，而在从亦能查到主表的相关信息。

通过在一方的类中添加`@ManyToOne`,在另一方中添加`@OneToMany`来表示双向一对多关系。

一般在`1`的一方需要取消维护关联关系,即多的一方存在外键，而`1`的一方不存在外键，也就不维护外键关系。

`主表实体类代码`

```java
@Table(name="USER")
@Entity
public class User {
	private Integer id;
	private String username;
	private String userpassword;
	private String useraddress;
	
	private List<Order> listOrder;
	
	@OneToMany(cascade=CascadeType.REMOVE,mappedBy="user")
	public List<Order> getListOrder() {
		return listOrder;
	}
	
	public void setListOrder(List<Order> listOrder) {
		this.listOrder = listOrder;
	}
	@Id
	@GeneratedValue(strategy=GenerationType.AUTO)
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	
	@Column(name="USERNAME")
	public String getUsername() {
		return username;
	}
	public void setUsername(String username) {
		this.username = username;
	}
	
	@Column(name="USERPASSWORD")
	public String getUserpassword() {
		return userpassword;
	}
	
	public void setUserpassword(String userpassword) {
		this.userpassword = userpassword;
	}
	
	@Column(name="USERADDRESS")
	public String getUseraddress() {
		return useraddress;
	}
	public void setUseraddress(String useraddress) {
		this.useraddress = useraddress;
	}
}
```

`主表实体类`中因为是`1`的一房，所以需要添加`@OneToMany`，且为了执行效率[默认不添加放弃主键维护的情况下，会重复出现update，SQL语句]需要放弃主键维护，那么在`@OneToMany`注解标注的属性处就不能再添加`@JoinColumn`注解。在`@OneToMany`注解中添加`mappedBy`属性并指定另一方所关联本方的**属性**名称，以代表我方放弃主键维护。

`从表实体类代码`

```java
@Entity
@Table(name="ORDER_USER")
public class Order {
	private Integer id;
	private String orderName;
	private User user;
	
	@Id
	@GeneratedValue
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getOrderName() {
		return orderName;
	}
	public void setOrderName(String orderName) {
		this.orderName = orderName;
	}
	
	@JoinColumn(name="USER_ID")
	@ManyToOne
	public User getUser() {
		return user;
	}
	public void setUser(User user) {
		this.user = user;
	}
}
```

在指向`主表实体类`的属性的get方法处增加`@joinColumn`主键指定外键名称，`@ManyToOne`指定该属性是`多对一`的外键属性。