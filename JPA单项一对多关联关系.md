##JPA一对多外键关联
**`一对多`**即一个对象中包含又另外一个对象的集合。
`User主表代码`

```java
@Table(name="USER")
@Entity
public class User {
	private Integer id;
	private String username;
	private String userpassword;
	private String useraddress;
	
	private List<Order> listOrder;
	
	@JoinColumn(name="LIST_ORDER")
	@OneToMany(fetch=FetchType.LAZY,cascade=CascadeType.REMOVE)
	public List<Order> getListOrder() {
		return listOrder;
	}
	public void setListOrder(List<Order> listOrder) {
		this.listOrder = listOrder;
	}
	@Id
	@TableGenerator(name="PK_PRIMARY",
	table="order_user_sque",
	pkColumnName="pk_column_name",
	pkColumnValue="pk_column",
	valueColumnName="pk_column_value",
	allocationSize=1)
	@GeneratedValue(strategy=GenerationType.TABLE,generator="PK_PRIMARY")
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

代码中:`@JoinColumn(name="LIST_ORDER")` -> 表示将外键名设置为`LIST_ORDER`
`@OneToMany(fetch=FetchType.LAZY,cascade=CascadeType.REMOVE)` -> `OneToMany`中fetch表示查询时的加载模式[懒加载还是积极加载],cascade属性表示要级联操作的模式，此处为删除主表后从表一并删除。

`Order从表代码`

```java
@Entity
@Table(name="ORDER_USER")
public class Order {
	private Integer id;
	private String orderName;
	//private User user;
	
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
}
```