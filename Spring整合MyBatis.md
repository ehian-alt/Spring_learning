# Spring整合MyBatis

### 依赖坐标

```xml
    <dependencys>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.13</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>3.0.2</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.28</version>
        </dependency>
    </dependencys>
```

### 创建Mybatis配置类

```java
public class MybatisConfig {
    // Mybatis配置类都只需要做一次
    @Bean
    public SqlSessionFactoryBean sqlSessionFactoryBean(DataSource dataSource){
        SqlSessionFactoryBean ssfb = new SqlSessionFactoryBean();
        ssfb.setDataSource(dataSource);
        ssfb.setTypeAliasesPackage("com.scase.domain");
        return ssfb;
    }

    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer(){
        MapperScannerConfigurer msc = new MapperScannerConfigurer();
        msc.setBasePackage("com.scase.dao");
        return msc;
    }
}
```

### 在Spring配置类 `SpringConfig` 中ImportBean

```java
@Configuration
@ComponentScan("com.scase")
@PropertySource("classpath:jdbc.properties")
@Import({MybatisConfig.class, JdbcConfig.class})
@EnableAspectJAutoProxy
public class SpringConfig {
}
```

数据层接口
```java
@Repository
public interface AccDao {
    @Insert("insert into user_table(id,name,age) values(#{id},#{name},#{age});")
    void save(Acc acc);

    @Delete("delete from user_table where id=#{id};")
    void delete(Integer id);

    @Update("update user_table set id=#{id},name=#{name},age=#{age};")
    void update(Acc acc);

    @Select("select * from user_table;")
    List<Acc> findAll();

    @Select("select * from user_table where id=#{id};")
    Acc findById(Integer id);
}
```







