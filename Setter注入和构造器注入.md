# Setter注入和构造器注入

## setter 引用类型的注入
对于这段代码
```java
public class BookServiceImpl implements BookService {
    private BookDao bookDao = new BookDaoImpl();

    @Override
    public void save() {
        System.out.println("book Service save..");
        bookDao.save();
    }
}
```

由于存在new 导致的耦合度，那么根据IoC的思想，我们只需要对引用类型创建一个入口，创建对象的事情交给IoC容器  
这里我们可以采用Setter注入，先把new的方式删除，并给BookDao创建Setter方法。
```
    private BookDaoImpl bookDao;

    public void setBookDao(BookDaoImpl bookDao) {
        this.bookDao = bookDao;
    }
```

那么在Spring中，如何通过这个Setter方法创建对应的bean呢。  
还是通过xml配置

```xml
<beans>
    <bean id="BookDao" class="org.quick.Dao.Impl.BookDaoImpl"/>

    <bean id="ServiceDao" class="org.quick.Service.Impl.BookServiceImpl">
<!--        配置server和dao的关系-->
<!--        property表示配置当前bean的属性-->
<!--        name属性表示配置具体哪一个的属性    这个值必须要和setter方法的驼峰命名相对应-->
<!--        ref表示参照哪一个bean-->
        <property name="bookDao" ref="BookDao"/>
    </bean>
</beans>
```

## 简单类型的setter注入

为简单类型写set方法
```java
public class BookDaoImpl implements BookDao {
    private int actionNum;
    private String databaseName;

    // 提供入口，set方法
    public void setActionNum(int actionNum) {
        this.actionNum = actionNum;
    }
    public void setDatabaseName(String databaseName) {
        this.databaseName = databaseName;
    }
}
```
配置bean

```xml
    <bean id="BookDao"  class="com.quickstart.Dao.impl.BookDaoImpl">
        <property name="actionNum" value="10"/>
        <property name="databaseName" value="mysql"/>
    </bean>
```

## 构造器注入

既然是构造器注入那肯定要先定义构造方法
```
    public BookServerImpl(BookDaoImpl bookDao, UserDaoImpl userDao) {
        this.bookDao = bookDao;
        this.userDao = userDao;
    }
```

配置bean
```xml
    <bean id="BookServer" class="com.quickstart.server.impl.BookServerImpl">
<!--    跟setter注入都差不多，就是把property换成了constructor-arg-->
        <constructor-arg name="userDao" ref="UserDao"/>
        <constructor-arg name="bookDao" ref="BookDao"/>
    </bean>
```

构造方法简单类型的注入
```java
public class BookDaoImpl implements BookDao {
    private int actionNum;
    private String databaseName;

    public BookDaoImpl(int actionNum, String databaseName) {
        this.actionNum = actionNum;
        this.databaseName = databaseName;
    }
}
```
配置bean
```xml
<!--标准书写-->
    <bean id="BookDao" class="com.quickstart.Dao.impl.BookDaoImpl">
        <constructor-arg name="actionNum" value="10"/>
        <constructor-arg name="databaseName" value="mysql"/>
    </bean>
```

但是这样的话，name跟构造方法的参数名又耦合了, 于是又有了下面两种方法  
* 不使用name，改为使用类型type, 解决了形参名的问题，但是构造函数有两个相同类型就不行了
```xml
    <bean id="BookDao" class="com.quickstart.Dao.impl.BookDaoImpl">
    <constructor-arg type="java.lang.Integer" value="10"/>
    <constructor-arg name="java.lang.String" value="mysql"/>
</bean>
```
* 使用index，
```xml
    <bean id="BookDao" class="com.quickstart.Dao.impl.BookDaoImpl">
    <constructor-arg index="0" value="10"/>
    <constructor-arg index="1" value="mysql"/>
</bean>
```

***强制依赖使用构造注入，可选依赖使用setter注入***

## 集合注入

以setter注入为例，先为各集合创建set方法  
分别创建了有数组，List、Set、Map、Properties
```java
public class BookDaoImpl implements BookDao {
    // 集合的注入
    private int[] array;
    private List<String> ls;
    private Set<String> ss;
    private Map<String, String> map;
    private Properties properties;

    // setter
    public void setArray(int[] array) {
        this.array = array;
    }

    public void setLs(List<String> ls) {
        this.ls = ls;
    }

    public void setSs(Set<String> ss) {
        this.ss = ss;
    }

    public void setMapSS(Map<String, String> map) {
        this.map = map;
    }

    public void setProperties(Properties properties) {
        this.properties = properties;
    }

    public void save(){
        System.out.println("遍历数组: " + Arrays.toString(array));
        System.out.println("遍历List: " + ls);
        System.out.println("遍历Set: " + ss);
        System.out.println("遍历Map: " + map);
        System.out.println("遍历Properties: " + properties);
    }
}
```

配置bean
```xml
    <bean id="BookDao"  class="com.quickstart.Dao.impl.BookDaoImpl">
<!--        集合的注入-->
        <property name="array">
            <array>
                <value>100</value>
                <value>200</value>
                <value>300</value>
            </array>
        </property>
        <property name="ls">
            <list>
                <value>spring</value>
                <value>test</value>
                <value>service</value>
            </list>
        </property>
        <property name="ss">
            <set>
                <value>service</value>
                <value>spring</value>
                <value>hhhh</value>
                <value>hhhh</value>
            </set>
        </property>
        <property name="mapSS">
            <map>
                <entry key="country" value="China"/>
                <entry key="province" value="henan"/>
                <entry key="city" value="zhengzhou"/>
            </map>
        </property>
        <property name="properties">
            <props>
                <prop key="country">China</prop>
                <prop key="province">henan</prop>
                <prop key="city">zhengzhou</prop>
            </props>
        </property>
    </bean>
```

但是这些都是自己定义的，在实现的时候有些数据来自外部，我们应该如何获取呢？  
加载properties文件