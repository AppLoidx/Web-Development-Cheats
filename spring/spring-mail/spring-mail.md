# Отправка электронных писем через Spring Boot

Репозиторий с исходниками: https://github.com/AppLoidx/spring-mail-example

Рассмотрим пример использования с gmail

Сначала необоходимо настроить `application.properties`:

```properties
spring.mail.host=smtp.gmail.com
spring.mail.port=465
spring.mail.username=your_email
spring.mail.password=password
spring.mail.properties.mail.smtp.auth=true

# If you need TLS in some ports
# spring.mail.properties.mail.smtp.starttls.enable=true

# SSL, post 465
spring.mail.properties.mail.smtp.socketFactory.port=465
spring.mail.properties.mail.smtp.socketFactory.class=javax.net.ssl.SSLSocketFactory
```

Вы можете использовать два вида соединения: TLS/STARTTLS или SSL.

Здесь мы сконфигурируем SSL с портом 465.

Напишем компонент, который будет всем этим заниматься:

```java
@Component
public class EmailServiceImpl {

    public final JavaMailSender emailSender;

    public EmailServiceImpl(JavaMailSender emailSender) {
        this.emailSender = emailSender;
    }

    public void sendSimpleMessage(String to, String subject, String text) {

        SimpleMailMessage message = new SimpleMailMessage();
        message.setTo(to);
        message.setSubject(subject);
        message.setText(text);
        emailSender.send(message);

    }

    void sendEmailWithAttachment(String to, String subject) throws MessagingException {

        MimeMessage msg = emailSender.createMimeMessage();

        // true = multipart message
        MimeMessageHelper helper = new MimeMessageHelper(msg, true);

        helper.setTo(to);

        helper.setSubject(subject);

        // default = text/plain
        //helper.setText("Check attachment for image!");

        // true = text/html
        helper.setText("<h1>Hey!</h1>" +
                "<img src=\"https://i.pinimg.com/564x/31/df/14/31df1484768d55b36fc62b30e935b95c.jpg\" />", true);

        emailSender.send(msg);

    }
}
```

И создадим контроллер, через который будем говорить, что надо отправить это сообщение:

```java
@RestController
@RequestMapping("/start")
public class StartTestController {

    private final EmailServiceImpl emailService;

    public StartTestController(EmailServiceImpl emailService) {
        this.emailService = emailService;
    }

    @GetMapping("/simple")
    public void simpleMessage() {
        emailService.sendSimpleMessage("to_email@gmail.com", "Arthur", "Hello!");
    }

    @GetMapping("attachment")
    public void withAttachment() throws IOException, MessagingException {
        emailService.sendEmailWithAttachment("to_email@gmail.com", "Arthur");
    }
}
```

Перейдем к ендпоинту, который вызовет операцию отправки письма:
```
localhost:8080/start/simple
```
![](https://i.imgur.com/L3ZiJbV.png)

И письмо с приложением:
```
localhost:8080/start/attachment
```

![](https://i.imgur.com/qeMkeg5.png)

Также вы можете посмотреть реализацию без окружения Spring : [JavaMail-Example](https://github.com/AppLoidx/JavaMail-Example)
