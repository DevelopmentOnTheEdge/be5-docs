# Проект

## Создание проекта

### Создание кода проекта из шаблона
Для создание нового проекта для be5 используется [maven archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html):

https://mvnrepository.com/artifact/com.developmentontheedge.be5/be5-app-template

Для его запуска наберите:  
`mvn archetype:generate  -DarchetypeGroupId=com.developmentontheedge.be5 -DarchetypeArtifactId=be5-app-template`

При создании проекта будут заданы вопросы, чтобы указать свойства:
* groupId
* artifactId
* version (значение по умолчанию - 1.0-SNAPSHOT)
* package (значение по умолчанию - groupId)

Эти параметры также можно указать в строке запуска.

После запуска генерируется заготовка для проекта на основе этого шаблона:   
https://github.com/DevelopmentOnTheEdge/testBe5app

### Проект из репозитория
Если вы только что выкачали be5 проект из git репозитория, то могут потребоваться 
описанные ниже действия по его настройке.

## Настройка проекта

### Создание базы данных
Создайте в используемой вами СУБД:
* пользователя, от имени которого be5 приложение будет соединяться с базой данных;
* настройте ему права доступа (для PostgeSQL в привилегиях включите "Can login");
* создайте базу данных от этого пользователя.

### Настройка соединения с базой данных
В директории проекта ./src создайте файл connectionProfiles.local.yaml

Ниже приведен пример такого файла
```
connectionProfiles:  
  profiles:  
    test_local:   
      connectionUrl: jdbc:postgresql://localhost:5434/registry   
      username: registry   
      password: registry
```

Более подробно: [[Connection profile|Connection profile]]

В директории проекта ./src создайте файл profile.local и в нем укажите имя соединения из файла connectionProfiles.local.yaml
Ниже приведен пример такого файла
```
test_local
```

### Быстрое обновление метаданных при запущенном проекте
Для быстрого обновления meta данных при разработке (yaml файлов проекта, groovy операций) создайте файл **dev.yaml** в корне проекта с содержимым:
```
paths:
  your_app: ./
```

Be5 будет следить за изменениями и перезагружать проект.

### Создание таблиц базы данных
Для этого в директории проекта выполните команду:  
`mvn be5:create-db`

### Установка npm пакетов для фронтенда (be5-react)
Из корня проекта выполните команду:  
`npm install`

## Запуск проекта
Для удобства разработки удобнее запустить 2 приложения:
* npm start - запускает npm сервер, что позволяет без перезапуска сервера обновлять код react компонентов
* mvn jetty:run -Djetty.http.port=8200 - запускает jetty сервер, без перезагрузки можно обновлять метаданные и groovy код операций.

Страничка в браузере: http://localhost:8888

Из шаблона приложение создан пользователь Administrator с паролем 12345.
