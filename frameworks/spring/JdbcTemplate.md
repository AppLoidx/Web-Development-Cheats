# JdbcTemplate

Шаблон **JdbcTemplate** - одна из реализаций Spring-экосистемы шаблонных образцов. Он предоставляет удобные и полезные методы,делающие общение с `JDBC` простым и обыденным. Он управляет инициализацией и получение ресурсов, уничтожением, обработкой исключений и др., позволяя сконцентрироваться на самой сути решаемой задачи.

### Пример
```java
public Collection<Customer> findAll() {
  List<Customer> customerList = new ArrayList<>();
  try {
   try (Connection c = dataSource.getConnection()) {
    Statement statement = c.createStatement();
    try (ResultSet rs = statement.executeQuery("select * from CUSTOMERS")) {
     while (rs.next()) {
      customerList.add(new Customer(rs.getLong("ID"), rs.getString("EMAIL")));
     }
    }
   }
  }
  catch (SQLException e) {
   throw new RuntimeException(e);
  }
  return customerList;
 }
```

**C JdbcTemplate**
```java
private final JdbcTemplate = jdbcTemplate;

public Collection<Customer> findAll() {
  RowMapper<Customer> rowMapper = ... ;
  return this.jdbcTemplate.query( "select * from CUSTOMERS ", rowMapper);
 }
```

_Использовано "Внедрение Зависимостей" и IoC_

JavaDoc : [docs.spring.io](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/jdbc/core/JdbcTemplate.html)
<br>
Статья на русском : [Alexander Kosarev](https://alexkosarev.name/2016/06/13/spring-framework-jdbctemplate/)
<br>
Еще одна статья на русском : [Spring по-русски](http://spring-projects.ru/guides/relational-data-access/)
<br>
Статья на Baeldung: [Spring JDBC](https://www.baeldung.com/spring-jdbc-jdbctemplate)
<br>
<hr>

**Листинг примеров**<br>
[Cloud Native Java - bootcamp, javaconfig](https://github.com/cloud-native-java/bootcamp/tree/master/spring-configuration/src/main/java/com/example/javaconfig)

**Литература**<br>
Cloud Native Java - Josh Long