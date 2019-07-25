# BUILD A REST API WITH SPRING 4 AND JAVA CONFIG

## Overview
This section shows how to **set up REST in Spring** – the Controller and HTTP response codes,
configuration of payload marshalling and content negotiation.

## The Java Configuration
```java
@Configuration
@EnableWebMvc
public class WebConfig{
 //
}
```

The new `@EnableWebMvc` annotation does a number of useful things – specifically, in
the case of REST, it detect the existence of `Jackson` and `JAXB 2` on the classpath and
automatically creates and registers default **JSON and XML converters**. The functionality of
the annotation is equivalent to the XML version:
```
<mvc:annotation-driven />
```

<hr>
This is a shortcut, and though it may be useful in many situations, it’s not perfect.
When more complex configuration is needed, remove the annotation and extend
WebMvcConfigurationSupport directly.
<hr>

## Controller
The @Controller is the central artifact in the entire Web Tier of the RESTful API. For the
purpose of the following examples, the controller is modeling a simple REST resource – Foo:

```java
@Controller
@RequestMapping( value = “/foos” )
class FooController{
 @Autowired
 IFooService service;
 @RequestMapping( method = RequestMethod.GET )
 @ResponseBody
 public List< Foo > findAll(){
 return service.findAll();
 }
 @RequestMapping( value = “/{id}”, method = RequestMethod.GET )
 @ResponseBody
 public Foo findOne( @PathVariable( “id” ) Long id ){
 return RestPreconditions.checkFound( service.findOne( id ) );
 }
 @RequestMapping( method = RequestMethod.POST )
 @ResponseStatus( HttpStatus.CREATED )
 @ResponseBody
 public Long create( @RequestBody Foo resource ){
 Preconditions.checkNotNull( resource );
 return service.create( resource );
 }
 @RequestMapping( value = “/{id}”, method = RequestMethod.PUT )
 @ResponseStatus( HttpStatus.OK )
 public void update( @PathVariable( “id” ) Long id, @RequestBody Foo resource ){
 Preconditions.checkNotNull( resource );
 RestPreconditions.checkNotNull( service.getById( resource.getId() ) );
 service.update( resource );
 }
 @RequestMapping( value = “/{id}”, method = RequestMethod.DELETE )
 @ResponseStatus( HttpStatus.OK )
 public void delete( @PathVariable( “id” ) Long id ){
 service.deleteById( id );
 }
}
```

The Controller implementation is **non-public** – this is because it doesn’t need to be. Usually
the controller is the last in the chain of dependencies – it receives HTTP requests from the
Spring front controller (the DispathcerServlet) and simply delegate them forward to a service
layer. If there is no use case where the controller has to be injected or manipulated through a
direct reference, then I prefer not to declare it as public.

**The request mappings** are straightforward – as with any controller, the actual value of the
mapping as well as the HTTP method are used to determine the target method for the
request. @RequestBody will bind the parameters of the method to the body of the HTTP
request, whereas @ResponseBody does the same for the response and return type. They
also ensure that the resource will be marshalled and unmarshalled using the correct HTTP
converter. **Content negotiation** will take place to choose which one of the active converters
will be used, based mostly on the Accept header, although other HTTP headers may be used
to determine the representation as well.

## Mapping the HTTP response codes
The status codes of the HTTP response are one of the most important parts of the REST
service, and the subject can quickly become very complex. Getting these right can be what
makes or breaks the service.

### Unmapped Requests
If Spring MVC receives a request which doesn’t have a mapping, it considers the request
not to be allowed and returns a **405 METHOD NOT ALLOWED** back to the client. It is also good practice to include the **Allow HTTP header** when returning a 405 to the client, in order
to specify which operations **are** allowed. This is the standard behavior of Spring MVC and does
not require any additional configuration.

### Valid, Mapped Requests
For any request that does have a mapping, Spring MVC considers the request valid and
responds with **200 OK** if no other status code is specified otherwise. It is because of this that
controller declares different `@ResponseStatus` for the create, update and delete actions but
not for get, which should indeed return the default 200 OK.

### Client Error
In case of a client error, custom exceptions are defined and mapped to the appropriate error
codes. Simply throwing these exceptions from any of the layers of the web tier will ensure
Spring maps the corresponding status code on the HTTP response.

```java
@ResponseStatus( value = HttpStatus.BAD_REQUEST )
public class BadRequestException extends RuntimeException{
 //
}
@ResponseStatus( value = HttpStatus.NOT_FOUND )
public class ResourceNotFoundException extends RuntimeException{
 //
}
```

These exceptions are part of the REST API and, as such, should only be used in the appropriate
layers corresponding to REST; if for instance a DAO/DAL layer exist, it should not use the
exceptions directly. Note also that these are not **checked exceptions** but **runtime exceptions** –
in line with Spring practices and idioms.

### Using @ExceptionHandler
Another option to map custom exceptions on specific status codes is to use the
`@ExceptionHandler` annotation in the controller. The problem with that approach is that
the annotation only applies to the controller in which it is defined, not to the entire Spring Container, which means that it needs to be declared in each controller individually. This quickly
becomes cumbersome, especially in more complex applications which many controllers. 

## Additional Dependencies 
In addition to the spring-webmvc dependency [required for the standard web application](https://www.baeldung.com/spring-with-maven#mvc), we’ll
need to set up content marshalling and unmarshalling for the REST API:

```xml
<dependencies>
 <dependency>
 <groupId>com.fasterxml.jackson.core</groupId>
 <artifactId>jackson-databind</artifactId>
 <version>${jackson.version}</version>
 </dependency>
 <dependency>
 <groupId>javax.xml.bind</groupId>
 <artifactId>jaxb-api</artifactId>
 <version>${jaxb-api.version}</version>
 <scope>runtime</scope>
 </dependency>
</dependencies>
<properties>
 <jackson.version>2.4.0</jackson.version>
 <jaxb-api.version>2.2.11</jaxb-api.version>
</properties>
```
These are the libraries used to convert the representation of the REST resource to either JSON
or XML.

<br><br>
<hr>

## Source
Building a REST API with Spring 4