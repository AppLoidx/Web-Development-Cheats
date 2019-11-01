# Гайд как задеплоить 2 лабу на helios, используя WildFly
1.  Собираем war. Используем 8 версию java. [Как собрать можно посмотреть тут]( https://github.com/AppLoidx/Web-Development-Cheats/blob/master/itmo-university/%D0%94%D0%B5%D0%BF%D0%BB%D0%BE%D0%B8%D0%BC%20%D0%BB%D0%B0%D0%B1%D1%83%20%D0%BD%D0%B0%20%D1%85%D0%B5%D0%BB%D0%B8%D0%BE%D1%81%20%D1%87%D0%B5%D1%80%D0%B5%D0%B7%20%D0%BF%D1%83%D1%82%D1%82%D0%B8.pdf)
2.  Качаем zip с официального сайта [WildFly](https://www.wildfly.org/downloads/)
3.  Распаковываем и кидаем на helios. Я это сдедал с помощью [WinSCP](https://winscp.net/eng/download.php)
4.  Далее заходим в `$WILDFLY_HOME/bin` и запускаем файл в зависимости от того, что нужно. 
* **standalone**. В нём каждый инстанс сервера является независимым приложением, живущим в отдельном процессе, со своими конфигурационными файлами, контекстом деплоя.
* **domain** - нововведение 10ки, по сути, это централизованное средство администрирования  экземпляров сервера, которое живет в отдельном процессе. Например можно одновременно отправить на  деплой приложение на все инстансы. 
5.  В моем случае это standalone, поэтому я запускаю следующий файл `./standlone.sh`
6.  Вылезет следующая ошибка. 
> Unrecognized VM option 'MetaspaceSize=96M'
Could not create the Java virtual machine.
7. Чтобы это исправить, нужно прописать путь к java 8. Так как WildFly требует 8 версию
`export JAVA_HOME=/usr/jdk/jdk1.8.0`
8. Далее пробрасываем порты, как в [этой инструкции]( https://github.com/AppLoidx/Web-Development-Cheats/blob/master/itmo-university/%D0%94%D0%B5%D0%BF%D0%BB%D0%BE%D0%B8%D0%BC%20%D0%BB%D0%B0%D0%B1%D1%83%20%D0%BD%D0%B0%20%D1%85%D0%B5%D0%BB%D0%B8%D0%BE%D1%81%20%D1%87%D0%B5%D1%80%D0%B5%D0%B7%20%D0%BF%D1%83%D1%82%D1%82%D0%B8.pdf), однако вместо `helios.se.ifmo.ru` пишем `127.0.0.1` в destination
9. Возможно, что стандартные порты уже используются, тогда надо поменять конфигурационный файл, который находится по адресу 
`$WILDFLY_HOME/standalone/configuration/standalone.xml`
1. Листаем вниз до конца и находим следующие строчки. И обычно меняем следующие порты для следующих компонентов
`http, https и management-http` на порты, которые свободные ~~, однако может быть, что придется поменять все стандартные порты~~
```xml
    <socket-binding-group name="standard-sockets" default-interface="public" port-offset="${jboss.socket.binding.port-offset:0}">
        <socket-binding name="ajp" port="${jboss.ajp.port:8009}"/>
        <socket-binding name="http" port="${jboss.http.port:8080}"/>
        <socket-binding name="https" port="${jboss.https.port:8443}"/>
        <socket-binding name="management-http" interface="management" port="${jboss.management.http.port:9990}"/>
        <socket-binding name="management-https" interface="management" port="${jboss.management.https.port:9993}"/>
        <socket-binding name="txn-recovery-environment" port="4712"/>
        <socket-binding name="txn-status-manager" port="4713"/>
        <outbound-socket-binding name="mail-smtp">
            <remote-destination host="localhost" port="25"/>
        </outbound-socket-binding>
    </socket-binding-group>
```
11. Чтобы проверить, что все работает корректно - запускаем `./standalone` из пункта 5

12. Пробраcываем порт на `http`и видим приветственную страничку
13. Затем запускаем скрипт `./add-user.sh`, который находится в той директории, где и `standalone.sh`. Добавляем админа.
13. Пробраcываем порт на `management-http` и вводим пароль и логин из прошлого пункта
14. Заходим в `Deployment`. Затем плюсик и выбераем файл из 1 пункта. Пропускаем следующие шаги.Копируем часть url напротив Context Root
15. Готово! Заходим на `http` порт и добавляем часть url из прошлого пункта

Если что-то не понятно, то можно почитать [статью](https://www.tune-it.ru/web/bleizard/blog/-/blogs/%D1%83%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0-wildfly-10?_com_liferay_blogs_web_portlet_BlogsPortlet_redirect=https%3A%2F%2Fwww.tune-it.ru%2Fweb%2Fbleizard%2Fblog%3Fp_p_id%3Dcom_liferay_blogs_web_portlet_BlogsPortlet%26p_p_lifecycle%3D0%26p_p_state%3Dnormal%26p_p_mode%3Dview%26p_r_p_tag%3Dwildfly%26_com_liferay_blogs_web_portlet_BlogsPortlet_cur%3D1%26_com_liferay_blogs_web_portlet_BlogsPortlet_delta%3D10) великого человека