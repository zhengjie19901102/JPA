##JPA之二级缓存
`在persistence配置文件中添加缓存相关配置`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<persistence version="2.1" xmlns="http://xmlns.jcp.org/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_1.xsd">
	<persistence-unit name="jpa" transaction-type="RESOURCE_LOCAL">
	
		<!-- 配置什么ORM产品，来作为JPA的实现 
			 实际上是配置 javax.persistence.spi.PersistenceProvider接口的实现类
			 若JPA项目中只有一个JPA的实现产品，则可以不用配置该节点。
		-->
		<provider>org.hibernate.ejb.HibernatePersistence</provider>
		<class>com.heiketu.entity.User</class>
		<class>com.heiketu.entity.Order</class>
		<class>com.heiketu.entity.WindowsPhone</class>
		<class>com.heiketu.entity.Windows</class>
		<class>com.heiketu.entity.Teacher</class>
		<class>com.heiketu.entity.ClassName</class>


		<!--此配置有位置要求-->
		<!-- 
		配置二级缓存的策略 
		ALL：所有的实体类都被缓存
		NONE：所有的实体类都不被缓存. 
		ENABLE_SELECTIVE：标识 @Cacheable(true) 注解的实体类将被缓存
		DISABLE_SELECTIVE：缓存除标识 @Cacheable(false) 以外的所有实体类
		UNSPECIFIED：默认值，JPA 产品默认值将被使用
		-->
		<shared-cache-mode>ENABLE_SELECTIVE</shared-cache-mode>
		
		<properties>
			<!-- 数据库相关连接设置 -->
			<property name="javax.persistence.jdbc.driver" value="com.mysql.cj.jdbc.Driver"/>
			<property name="javax.persistence.jdbc.url" value="jdbc:mysql:///test?serverTimezone=GMT%2B8"/>
			<property name="javax.persistence.jdbc.user" value="root"/>
			<property name="javax.persistence.jdbc.password" value="admin"/>
			
			<!-- 配置JPA实现产品的基本属性: hibernate基本配置 -->
			<property name="hibernate.format_sql" value="true"/>
			<property name="hibernate.show_sql" value="true"/>
			<property name="hibernate.hbm2ddl.auto" value="update"/>
			<!-- <property name="hibernate.dialect" value="org.hibernate.dialect.MySQLDialect"/> -->
			
			
			<!-- 二级缓存相关 -->
			<!-- 开启二级缓存 -->
			<property name="hibernate.cache.use_second_level_cache" value="true"/>
			<!--使用EhCache-->
			<property name="hibernate.cache.region.factory_class" value="org.hibernate.cache.ehcache.EhCacheRegionFactory"/>
			<!--使用查询缓存-->
			<property name="hibernate.cache.use_query_cache" value="true"/>
		</properties>
	</persistence-unit>
</persistence>
```

在classpath下加入**`ehcache.xml`**ehcache缓存相关配置文件。

配置文件内容:

```xml
<ehcache>

    <!-- Sets the path to the directory where cache .data files are created.

         If the path is a Java System Property it is replaced by
         its value in the running VM.

         The following properties are translated:
         user.home - User's home directory
         user.dir - User's current working directory
         java.io.tmpdir - Default temp file path -->
    <diskStore path="java.io.tmpdir"/>


    <!--Default Cache configuration. These will applied to caches programmatically created through
        the CacheManager.

        The following attributes are required for defaultCache:

        maxInMemory       - Sets the maximum number of objects that will be created in memory
        eternal           - Sets whether elements are eternal. If eternal,  timeouts are ignored and the element
                            is never expired.
        timeToIdleSeconds - Sets the time to idle for an element before it expires. Is only used
                            if the element is not eternal. Idle time is now - last accessed time
        timeToLiveSeconds - Sets the time to live for an element before it expires. Is only used
                            if the element is not eternal. TTL is now - creation time
        overflowToDisk    - Sets whether elements can overflow to disk when the in-memory cache
                            has reached the maxInMemory limit.

        -->
    <defaultCache
        maxElementsInMemory="10000"
        eternal="false"
        timeToIdleSeconds="120"
        timeToLiveSeconds="120"
        overflowToDisk="true"
        />

    <!--Predefined caches.  Add your cache configuration settings here.
        If you do not have a configuration for your cache a WARNING will be issued when the
        CacheManager starts

        The following attributes are required for defaultCache:

        name              - Sets the name of the cache. This is used to identify the cache. It must be unique.
        maxInMemory       - Sets the maximum number of objects that will be created in memory
        eternal           - Sets whether elements are eternal. If eternal,  timeouts are ignored and the element
                            is never expired.
        timeToIdleSeconds - Sets the time to idle for an element before it expires. Is only used
                            if the element is not eternal. Idle time is now - last accessed time
        timeToLiveSeconds - Sets the time to live for an element before it expires. Is only used
                            if the element is not eternal. TTL is now - creation time
        overflowToDisk    - Sets whether elements can overflow to disk when the in-memory cache
                            has reached the maxInMemory limit.

        -->

    <!-- Sample cache named sampleCache1
        This cache contains a maximum in memory of 10000 elements, and will expire
        an element if it is idle for more than 5 minutes and lives for more than
        10 minutes.

        If there are more than 10000 elements it will overflow to the
        disk cache, which in this configuration will go to wherever java.io.tmp is
        defined on your system. On a standard Linux system this will be /tmp"
        -->
    <cache name="sampleCache1"
        maxElementsInMemory="10000"
        eternal="false"
        timeToIdleSeconds="300"
        timeToLiveSeconds="600"
        overflowToDisk="true"
        />

    <!-- Sample cache named sampleCache2
        This cache contains 1000 elements. Elements will always be held in memory.
        They are not expired. -->
    <cache name="sampleCache2"
        maxElementsInMemory="1000"
        eternal="true"
        timeToIdleSeconds="0"
        timeToLiveSeconds="0"
        overflowToDisk="false"
        /> -->

    <!-- Place configuration for your caches following -->

</ehcache>

```

导入`ehcache`相关jar包:

	|---ehcache-core-2.4.3.jar
	|---hibernate-ehcache-4.3.11.Final.jar
	|---slf4j-api-1.6.1.jar

在需要缓存的类上增加`@Cacheable(true)`或`@Cacheable(false)`相关标注，与`persistence`配置文件中的`<shared-cache-mode>`标签的值相呼应。


**`默认JPA开启了一级缓存`**
