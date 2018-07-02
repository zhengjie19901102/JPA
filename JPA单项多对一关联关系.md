## JPA单项多对一关联关系

多对一即n多1关系。
在从表中与主表有外键关系的列的get方法上添加**`@ManyToOne`**注解后，该属性则表示为外键，该外键关系为其属性类型对应的类型。用**`JoinColumn`**注解设置外键的列名。

示例代码如下:

`主表实体代码:`

```java
@Table(name="USER")
@Entity
public class User {
	private Integer id;
	private String username;
	private String userpassword;
	private String useraddress;
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
主表**`User`**通过`@Table`注解指定表名为`USER`,通过`@Column`注解指定列名。
该User对象主键生成策略通过第三方表来实现自增长。

`从表实体代码:`

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
	@ManyToOne(fetch=FetchType.LAZY)
	public User getUser() {
		return user;
	}
	public void setUser(User user) {
		this.user = user;
	}
}
```

从表**`Order`**表名为`ORDER_USER`[`重点: 取表名不能与数据库的关键字重名，否则无法生成表但JPA却不一定会报错提示。`]。该表采用默认自增策略[MYSQL即为`auto_increment`],`@JoinColum`注解表示该属性为外键,name属性指定外键列名。`@ManyToOne`表示外键为多对一关系。
`@ManyToOne`中`fetch`属性表示是否采用懒加载或正常加载,其值为枚举类型。有两种选择:`FetchType.LAZY`->表示该外键使用懒加载,`FetchType.EAGER`->表示该外键使用正常的积极加载模式。

采用外键后,修改、删除、添加都需要注意主从顺序进行操作,否则会操作失败。