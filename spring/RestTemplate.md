# RestTemplate

По сути своей, он похож на JdbcTemplate и JmsTemplate, но он предназначен для HTTP.

|HTTP|	RESTTEMPLATE|
|----|----|
|DELETE|	**delete**(String, String...)|
|GET|	**getForObject**(String, Class, String...)|
|HEAD|	**headForHeaders**(String, String...)|
|OPTIONS|	**optionsForAllow**(String, String...)|
|POST|	**postForLocation**(String, Object, String...)|
|PUT|	**put**(String, Object, String...)|

## Примеры
В методе необходимо передавать метку типа (`Class<T>`) как при использовании Gson.

```java
String result = 
  restTemplate.getForObject("http://example.com/hotels/{hotel}/bookings/{booking}", String.class, "42", "21");
```

Также можно задать аргументы через Map:
```java
Map<String, String> vars = new HashMap<String, String>();
vars.put("hotel", "42");
vars.put("booking", "21");
String result = restTemplate.getForObject("http://example.com/hotels/{hotel}/bookings/{booking}", String.class, vars);
```

Или задать параметры:
```java
Map<String, String> vars = new HashMap<>();
vars.put("login", login);
vars.put("password", password);
Token token = restTemplate.getForObject(ExternalSourcesConfig.heliosApiUri + "auth?login={login}&password={password}",Token.class, vars);
```
