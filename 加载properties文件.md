# 加载properties文件

spring加载properties文件

1. 开启一个 context 命名空间

初始的配置是这样的
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans 
       http://www.springframework.org/schema/beans/spring-beans.xsd ">
</beans>

```

我们开启一个 `context` 命名空间  
把 `xmlns="http://www.springframework.org/schema/beans"` 复制  
改成 `xmlns:context="http://www.springframework.org/schema/context"`  
把 `http://www.springframework.org/schema/beans` 和  
`http://www.springframework.org/schema/beans/spring-beans.xsd` 复制  
把里面的 `beans` 都改成 `context`。  
http://www.springframework.org/schema/context` `http://www.springframework.org/schema/context/spring-context.xsd`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans 
       http://www.springframework.org/schema/beans/spring-beans.xsd 
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">
</beans>
```

这样就开启了 `context` 的命名空间

2. 加载properties文件

假设我们有这样一个 `properties` 文件  
``` 
jdbc.name=jacky
jdbc.age=20
jdbc.address=beijing
jdbc.job=teacher
```

创建了一个userDao类，测试
```java
public class UserDaoImpl implements UserDao {

    private String name;
    private int age;
    private String address;
    private String job;

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    public void setJob(String job) {
        this.job = job;
    }

    @Override
    public void save() {
        System.out.println("UserDao save...");
    }
}
```

然后我们通过 `context` 命名空间加载 `properties` 文件  
用 `${}` 加载数据
```xml
<beans>
<!--....
    system-properties-mode="NEVER"  : 不加载系统属性，因为主机系统属性高于properties文件
    若不设置这一参数当出现相同名字时会使用系统属性-->
    <context:property-placeholder location="application.properties" system-properties-mode="NEVER"/>
    <bean id="UserDao" class="com.quickstart.Dao.impl.UserDaoImpl">
        <property name="address" value="${jdbc.address}"/>
        <property name="age" value="${jdbc.age}"/>
        <property name="job" value="${jdbc.job}"/>
        <property name="name" value="${jdbc.name}"/>
    </bean>
</beans>
```


