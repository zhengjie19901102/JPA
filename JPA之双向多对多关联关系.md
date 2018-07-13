##JPA双向多对多关联关系

`主表实体类代码`

```java
@Entity
@Table(name = "TEACHER")
public class Teacher {
	private Integer id;
	private String teacherName;
	private Integer teacherAge;
	private List<ClassName> classNameList;

	@Id
	@GeneratedValue
	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	@Column(name = "TEACHER_NAME")
	public String getTeacherName() {
		return teacherName;
	}

	public void setTeacherName(String teacherName) {
		this.teacherName = teacherName;
	}


	public Integer getTeacherAge() {
		return teacherAge;
	}

	public void setTeacherAge(Integer teacherAge) {
		this.teacherAge = teacherAge;
	}

	@JoinTable(name="TEACHER_CLASS",
			joinColumns= {@JoinColumn(name="TEACHER_ID",referencedColumnName="ID")},
			inverseJoinColumns= {@JoinColumn(name="CLASS_ID",referencedColumnName="ID")})
	@ManyToMany
	public List<ClassName> getClassNameList() {
		return classNameList;
	}

	public void setClassNameList(List<ClassName> classNameList) {
		this.classNameList = classNameList;
	}

}
```

`从表实体类代码`

```java
@Entity
@Table(name="CLASSNAME")
public class ClassName {
private Integer id;
private String classNumber;
private List<Teacher> teacherList;

@Id
@GeneratedValue
public Integer getId() {
	return id;
}
public void setId(Integer id) {
	this.id = id;
}

@Column(name="CLASS_NUMBER")
public String getClassNumber() {
	return classNumber;
}
public void setClassNumber(String classNumber) {
	this.classNumber = classNumber;
}

@ManyToMany(mappedBy="classNameList")
public List<Teacher> getTeacherList() {
	return teacherList;
}
public void setTeacherList(List<Teacher> teacherList) {
	this.teacherList = teacherList;
}
}
```

从表维护外键表，通过`@ManyToMany`注解所在属性上标注`@JoinTable`注解来指要生成哪个外键中间表。

`name="TEACHER_CLASS"` -> 指定生成中间表的表名。
`joinColumns= {@JoinColumn(name="TEACHER_ID",referencedColumnName="ID")}` -> 指定中间表哪列关联当前类，并指定该列的列名和指向的主键属性名。
`inverseJoinColumns= {@JoinColumn(name="CLASS_ID",referencedColumnName="ID")}` -> 指定中间表那列关联另一个类，并指定该列的列名和指向的主键名。
