## Enterprise Java Beans

> или же EJB



> **EJB** — технология разработки серверных компонентов, реализующих бизнес-логику 

**Особенности EJB**: 

* Возможность локального и удалённого доступа (с одного JVM на другой, например)

* Возможность доступа через JNDI или Dependency Injection (как и большинство бинов, бтв)

* Поддержка распределённых транзакций (с помощью JTA).
  

Условно `BEGIN TRANSACTION` > `some actions` > `COMMIT`

* Поддержка событий (Message-Driven Beans, какая-то херь с JMS связанная)

* Жизненным циклом управляет EJB-контейнер (в составе сервера приложений)
  
  * Вот это вот особенность! Прям "уникальный" бин : )



**Виды** **EJB**

![](https://i.imgur.com/whl7niA.png)

На самом деле, есть еще Entity Bean, но там все сложно, да и в презентации его не было.



## Proxy and Business Interfaces

Как я понял, мы не обращаемся к EJB напрямую, а взаимодействуем с прокси-объектами.

![](https://i.imgur.com/raA2mzb.png)



Не знаю как это реализовано в здесь, но вот пример динамического создания прокси на [хабре]( https://habr.com/ru/post/348906/ )

![](https://i.pinimg.com/564x/f9/6d/04/f96d04a3861159b9d18eb2afd64b0cb6.jpg)

Для чего нужен этот прокси? Если работать только с одним JVM, то он вроде нахрен не нужен (но это не точно, далее). Проблема возникает тогда, когда мы работаем с несколькими JVM, так как EJB по сути Location Transparent.

Но прокси нужны не только для этого. Например, они нужны для открытия (завершения) транщакций (вроде AOP в спринге)



Если вы забыли, то вот освежить память про **Location Transparency**

![](https://i.imgur.com/3niZXjD.png)



## Session Beans

* Предназначены для синхронной обработки вызовов.
* Вызываются посредством обращения через API.
* Могут вызываться локально или удалённо
* Могут быть endpoint'ами для веб-сервисов.
* **CDI — аннотация `@EJB`.**
* Не обладают свойством персистентности
* Можно формировать пулы бинов (за исключением `@Singleton`).  

### Stateless

* Не сохраняют состояние между последовательными обращениями клиента.

* Нет привязки к конкретному клиенту.
* Хорошо масштабируются.
* **Объявление — аннотация `@Stateless`.**  

 ![img](https://i.imgur.com/402eRmC.png) 

Тут важно заметить, что нет необходимости создавать для каждого клиента отдельный EJB-объект и в пуле находится три объекта `AuctionManagerBean`.

Пример:

```java
package converter.ejb;

import java.math.BigDecimal;
import javax.ejb.*;

@Stateless
public class ConverterBean {
     private BigDecimal yenRate = new BigDecimal("83.0602");
     private BigDecimal euroRate = new BigDecimal("0.0093016");
    
     public BigDecimal dollarToYen(BigDecimal dollars) {
         BigDecimal result = dollars.multiply(yenRate);
         return result.setScale(2, BigDecimal.ROUND_UP);
     }
    
     public BigDecimal yenToEuro(BigDecimal yen) {
         BigDecimal result = yen.multiply(euroRate);
         return result.setScale(2, BigDecimal.ROUND_UP);
     }
}

```

### Stateful

* «Привязываются» к конкретному клиенту.
* Можно сохранять контекст в полях класса (юхуууу!)
* Масштабируются хуже, чем @Stateless (как я понял, накладных расходов выше)
* **Объявление — аннотация @Stateful.** 

![](https://i.imgur.com/6EhwDYT.png)

Ну, тут очевидно, в отличие от Stateless нам нужны разные бины, чтобы сохранять статус Stateful

Страшна вырубай! :

```java
@Stateful
@StatefulTimeout(unit = TimeUnit.MINUTES, value = 20)
public class CartBean implements Cart {
    
    @PersistenceContext(unitName = "pu", type = PersistenceContextType.EXTENDED)
    private EntityManager entityManager;
    private List products;
    
    @PostConstruct
    private void initializeBean(){
        products = new ArrayList<>();
    }
    
    @Override
    public void addProductToCart(Product product) {
        products.add(product);
    }
    
    @Override
    @TransactionAttribute(TransactionAttributeType.REQUIRED)
    public void checkOut() {
        for(Product product : products){
        entityManager.persist(product);
        }
        products.clear();
    }
}
```

### Singleton



![](https://wine-shopper.ru/image/cache/catalog/0-%D0%90%D0%90/Singleton%20of%20Dufftown%2015-600x600.jpg)

* Контейнер гарантирует существование строго одного экземпляра такого бина.
* **Объявление — аннотация @Singleton**. 

![](https://i.imgur.com/SgGekh3.png)

Картинка почти идентична первому, но если посмотреть на пул, то есть отличия. В первых двух, у нас в пулах были по три бина, а здесь только один.

##  Разработка Session Beans 

> Та за шо?!

Три легких шага:

1.  Создание бизнес-интерфейса компонента (**Business Interface**) 
2.  Создание класса компонента, реализующего бизнес-интерфейс 
3.  Конфигурация компонента с помощью аннотаций и/или дескриптора развёртывания. 

### **Business Interface**

 **Сцена 1**

Бизнес-интерфейс содержит заголовки всех методов, реализующих бизнес-логику компонента. Может быть локальным `@Local` и удалённым `@Remote`.  

Ну, тут хоть пример попроще

```java
import javax.ejb.*;

@Remote
public interface Hello {
	public String sayHello();
}
```



**Сцена 2** **и 3**

 Компонент должен реализовывать все методы бизнесинтерфейса. 

Может содержать методы, реагирующие на события жизненного цикла компонента. 

```java
import javax.ejb.*;

// вот бы всегда было так легко :)

@Stateless	// Конфигурация
public class HelloBean implements Hello {	// реализация
    public String sayHello() {
        return "Hello World!";
    }
}
```

Требования:

* Должен быть public классом верхнего уровня.
* Не должен быть final и/или abstract.
* Должен иметь public конструктор без параметров.
* Не должен переопределять метод finalize().
* Должен реализовывать все методы бизнес интерфейса(-ов).  



## Жизненные циклы

#### Stateless

![](https://i.imgur.com/DqwwGCv.png)

#### Stateful

![](https://i.imgur.com/nK3Y5ut.png)

А синглтон походу выпили...



## Обращение к Session Bean

```java
import javax.ejb.*;

public class HelloClient {
     @EJB
     private static Hello hello;
    
     public static void main (String args[]) {
         System.out.println(hello.sayHello())
     }
}

```
