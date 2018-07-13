## JPA之联合主键[复合主键]

`方法1:通过@IdClass注解指定复合主键对象`

```java
//复合主键对象:
public class IdClass implements Serializable{
	private Integer a_id;
	private Integer b_id;
	public Integer getA_id() {
		return a_id;
	}
	public void setA_id(Integer a_id) {
		this.a_id = a_id;
	}
	public Integer getB_id() {
		return b_id;
	}
	public void setB_id(Integer b_id) {
		this.b_id = b_id;
	}
}



//实体类对象
@Entity
@Table(name="A")
@IdClass(com.ieb.bean.IdClass.class)
public class A implements Serializable {
	private Integer a_id;
	private String name;
	private Integer b_id;
	
	@Id
	public Integer getB_id() {
		return b_id;
	}
	public void setB_id(Integer b_id) {
		this.b_id = b_id;
	}
	@Id
	public Integer getA_id() {
		return a_id;
	}
	public void setA_id(Integer a_id) {
		this.a_id = a_id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((a_id == null) ? 0 : a_id.hashCode());
		result = prime * result + ((b_id == null) ? 0 : b_id.hashCode());
		result = prime * result + ((name == null) ? 0 : name.hashCode());
		return result;
	}
	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		A other = (A) obj;
		if (a_id == null) {
			if (other.a_id != null)
				return false;
		} else if (!a_id.equals(other.a_id))
			return false;
		if (b_id == null) {
			if (other.b_id != null)
				return false;
		} else if (!b_id.equals(other.b_id))
			return false;
		if (name == null) {
			if (other.name != null)
				return false;
		} else if (!name.equals(other.name))
			return false;
		return true;
	}
}
```

**`复合主键对象必须要序列化，且对象需要有一个空构造器[默认存在，如果加了有参构造则需要手动添加无参构造器]。实体类对象可以不需要重写hashCode和Equals方法，也可以不序列化[最好序列化]。@IdClass注解指定对应复合主键类[.class]`**

`方法2:通过通过复合主键对象 + @Embeddable 和实体对象 + @EmbeddedId注解：`

```java
//主键类
@Embeddable
public class PrimaryKey implements Serializable{
	private Integer a_id;
	private Integer b_id;
	public Integer getA_id() {
		return a_id;
	}
	public void setA_id(Integer a_id) {
		this.a_id = a_id;
	}
	public Integer getB_id() {
		return b_id;
	}
	public void setB_id(Integer b_id) {
		this.b_id = b_id;
	}
}

//实体类
@Entity
@Table(name="A")
public class A{
	@EmbeddedId
	private PrimaryKey idCLass;
	
	public PrimaryKey getIdCLass() {
		return idCLass;
	}
	public void setIdCLass(PrimaryKey idCLass) {
		this.idCLass = idCLass;
	}
	private String name;
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
}
```