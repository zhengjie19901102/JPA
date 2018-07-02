## JPA双向一对一外键关联关系

`主表实体类代码`

```java
@Id
@GeneratedValue(strategy=GenerationType.AUTO)
public Integer getId() {
	return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	@Column(name = "NAME")
	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	@OneToOne(mappedBy = "win")
	public WindowsPhone getWp() {
		return wp;
	}

	public void setWp(WindowsPhone wp) {
		this.wp = wp;
	}
}
```

`从表实体类代码`

```java
@Entity
@Table(name = "WINDOWS_PHONE")
public class WindowsPhone {
	private Integer id;
	private String name;
	private Windows win;

	@Id
	@GeneratedValue(strategy = GenerationType.AUTO)
	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	@Column(name = "NAME")
	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	@JoinColumn(name = "WIN")
	@OneToOne
	public Windows getWin() {
		return win;
	}

	public void setWin(Windows win) {
		this.win = win;
	}
}

```

维护外键的一方即存在外键列的一方。维护一方需要添加`@JoinColumn`注解指名外键列名。放弃维护一方需要通过`mappedBy`指名将维护权利交给另一方的哪个属性[指外键那列]。