---
title: HTTP аутентификация
slug: Web/HTTP/Guides/Authentication
---

HTTP предоставляет набор инструментов для контроля доступа к ресурсам и аутентификации. Самой
распространённой схемой HTTP аутентификации является "Базовая" (Basic) аутентификация. Данное руководство описывает
основные возможности HTTP аутентификации и показывает способы ограничения доступа к вашему серверу с её
использованием.

## Общий механизм HTTP аутентификации

{{RFC("7235")}} определяет средства HTTP аутентификации, которые может использовать сервер
для {{glossary("challenge","запроса")}} у клиента аутентификационной информации. Сценарий запрос-ответ подразумевает,
что вначале сервер отвечает клиенту со статусом {{HTTPStatus("401")}} (Unauthorized) и предоставляет информацию о
порядке авторизации через заголовок {{HTTPHeader("WWW-Authenticate")}}, содержащий хотя бы один метод авторизации.
Клиент, который хочет аутентифицироваться, может сделать это, включив в следующий запрос заголовок
{{HTTPHeader("Authorization")}} с требуемыми данными. Обычно, клиент отображает пользователю запрос (prompt) пароля, и
после получения ответа отправляет запрос (request) с пользовательскими данными в заголовке `Authorization`.

![](httpauth.png)

В случае базовой авторизации как на иллюстрации выше, обмен **должен** вестись через HTTPS (TLS) соединение, чтобы обеспечить защищённость.

### Прокси-аутентификация

Этот же механизм запроса и ответа может быть использован для _прокси-аутентификации_. В таком случае ответ
посылает промежуточный прокси-сервер, который требует авторизации. Поскольку обе формы аутентификации могут
сосуществовать, для них используются разные заголовки и коды статуса ответа. В случае с _прокси_, статус-код
запроса {{HTTPStatus("407")}} (Proxy Authentication Required) и заголовок {{HTTPHeader("Proxy-Authenticate")}},
который содержит хотя бы один запрос, относящийся к прокси, а для передачи учётных данных прокси-серверу
используется заголовок {{HTTPHeader("Proxy-Authorization")}}.

### Доступ запрещён

Если (прокси) сервер получает корректные учётные данные, но они не подходят для доступа к данному ресурсу, сервер
должен отправить ответ со статус кодом {{HTTPStatus("403")}} `Forbidden`. В отличии от статус
кода {{HTTPStatus("401")}} `Unauthorized` или {{HTTPStatus("407")}}
`Proxy Authentication Required`, аутентификация для этого пользователя не возможна.

Во всех случаях сервер может предпочесть возвращать код состояния {{HTTPStatus("404")}} `Not Found`, чтобы
скрыть существование страницы для пользователя без соответствующих привилегий или не прошедшего правильную
аутентификацию.

### Аутентификация cross-origin изображений

Аутентификация с помощью изображений, загружаемых из разных источников, была до недавнего времени потенциальной дырой
в безопасности. Начиная с [Firefox 59](/ru/docs/Mozilla/Firefox/Releases/59), изображения, загружаемые
из разных источников в текущий документ, больше не запускают диалог HTTP-аутентификации [Firefox bug 1423146](https://bugzil.la/1423146), предотвращая
тем самым кражу пользовательских данных (если нарушители смогли встроить это изображение в страницу).

### Кодировка символов HTTP аутентификации

Браузеры используют кодировку `utf-8` для имени пользователя и пароля. Firefox
использовал `ISO-8859-1`, но она была заменена `utf-8` с целью уравнения с другими браузерами, а
также чтобы избежать потенциальных проблем (таких как [Firefox bug 1419658](https://bugzil.la/1419658)).

### заголовки `WWW-Aутентификации` и `Прокси-Аутентификации`

{{HTTPHeader("WWW-Authenticate")}} и {{HTTPHeader("Proxy-Authenticate")}} заголовки ответа которые определяют методы,
что следует использовать для получения доступа к ресурсу. Они должны указывать,
какую схему аутентификации использовать, чтобы клиент, желающий авторизоваться, знал, какие данные предоставить.
Синтаксис для этих заголовков следующий:

```
WWW-Authenticate: <type> realm=<realm>
Proxy-Authenticate: <type> realm=<realm>
```

Здесь, `<type>` - это схема аутентификации ("Basic" (базовая) это наиболее распространённая, [представленная ниже](#Basic_authentication_scheme)). _realm_
используется для описания защищённой области или для индикации области защиты. Это может быть сообщение типа "Access
to the staging site" или подобное, чтобы пользователь знал, к какой области он пытается получить доступ.

### заголовки `Авторизации` и `Прокси-Авторизации`

Заголовки запросов {{HTTPHeader("Authorization")}} и {{HTTPHeader("Proxy-Authorization")}} содержат учётные данные
для аутентификации агента пользователя (User Agent) на (прокси) сервере. Здесь снова требуется
`<type>` за которым следуют учётные данные, которые могут быть закодированы или зашифрованы в
зависимости от того, какая схема аутентификации используется.

```
Authorization: <type> <credentials>
Proxy-Authorization: <type> <credentials>
```

### Схемы Аутентификации

Общая структура HTTP аутентификации является основной для ряда схем аутентификации. Schemes can differ in security
strength and in their availability in client or server software.

IANA поддерживат [список схем аутентификации](https://www.iana.org/assignments/http-authschemes/http-authschemes.xhtml),
однако существуют и другие схемы предлагаемые хост-сервисами, например, Amazon AWS.

Некоторые распространенные схемы аутентификации включают:

- **Basic** (смотреть {{rfc(7617)}}, зашифрованные с помощью base64 учётные данные. Больше информации
  смотреть снизу.),
- **Bearer** (смотреть {{rfc(6750)}}, bearer токены для доступа OAuth 2.0-защищённых ресурсов),
- **Digest** (смотреть {{rfc(7616)}}, Firefox 93 и более поздние версии поддерживают шифрование
  SHA-256. Предыдущие версии поддерживают только хэширование MD5 (не рекомендуется). ),
- **HOBA** (смотреть {{rfc(7486)}}, Секция 3, **H**TTP
  **O**rigin-**B**ound **A**uthentication, digital-signature-based),
- **Mutual** (смотреть [draft-ietf-httpauth-mutual](https://tools.ietf.org/html/draft-ietf-httpauth-mutual-11)),
- **AWS4-HMAC-SHA256** (смотреть [AWS
  документацию](https://docs.aws.amazon.com/AmazonS3/latest/API/sigv4-auth-using-authorization-header.html)).

Схемы могут различаться по степени безопасности и по их доступности в клиентском или серверном программном
обеспечении.

"Базовая" (Basic) схема аутентификации обеспечивает очень низкую безопасность, но широко поддерживается и проста в
настройке. Более подробно она представлена ниже.

## Базовая (Basic) схема аутентификации

"Базовая" схема HTTP-аутентификации определена в {{rfc(7617)}}, которая передаёт учётные данные в виде пар
пользователь ID/пароль (user ID/password), закодированных с использованием base64.

### Безопасность базовой аутентификации

Поскольку идентификатор (ID) пользователя и пароль передаются по сети в виде открытого текста (он кодируется в
base64, однако base64 - это обратимое кодирование), схема базовой аутентификации не является безопасной. При базовой
аутентификации следует использовать HTTPS/TLS. Без этих дополнительных улучшений безопасности, базовая аутентификация
не должна использоваться для защиты конфиденциальной или ценной информации.

### Ограничение доступа с помощью Apache и базовой аутентификации

Чтобы защитить паролем каталог на сервере Apache, вам понадобятся файлы `.htaccess` и `.htpasswd`.

Файл `.htaccess` обычно выглядит так:

```
AuthType Basic
AuthName "Access to the staging site"
AuthUserFile /path/to/.htpasswd
Require valid-user
```

Файл `.htaccess` ссылается на файл `.htpasswd`, в котором каждая строка состоит из имени
пользователя и пароля, разделенных двоеточием (":"). Вы не можете видеть фактические пароли, поскольку они [хешируются](https://httpd.apache.org/docs/2.4/misc/password_encryptions.html) (в данном случае с помощью
md5). Учтите, что вы можете назвать свой файл `.htpasswd` по-другому, если хотите, но помните, что этот
файл не должен быть доступен никому. (Apache обычно настроен на предотвращение доступа к файлам `.ht*`).

```
aladdin:$apr1$ZjTqBB3f$IF9gdYAGlMrs2fuINjHsz.
user2:$apr1$O04r.y2H$/vEkesPhVInBByJUkXitA/
```

### Ограничение доступа с помощью nginx и базовой аутентификации

Для nginx, вам потребуется указать местоположение, которое вы собираетесь защитить и директиву
`auth_basic`, которая задаёт имя защищённой паролем области. Директива `auth_basic_user_file`
затем указывает на файл .htpasswd, содержащий зашифрованные учётные данные пользователя, как и в примере Apache выше.

```
location /status {
    auth_basic           "Access to the staging site";
    auth_basic_user_file /etc/apache2/.htpasswd;
}
```

### Доступ используя учётные данные в URL-адресе

Многие клиенты также позволяют избежать запроса на вход в систему, используя закодированный URL, содержащий имя
пользователя и пароль, как показано ниже:

```plain example-bad
https://username:password@www.example.com/
```

**Использование таких URL-адресов устарело**. В браузере Chrome в URL-адресах часть
`username:password@` даже[вырезана](https://bugs.chromium.org/p/chromium/issues/detail?id=82250#c7) из соображений безопасности. В браузере Firefox, проверяется, действительно ли сайт требует
аутентификации и если нет, тогда Firefox предупредит пользователя запросом (prompt) "You are about to log in to the
site "www\.example.com" with the username "username", but the website does not require authentication. This may be an
attempt to trick you.".

## Смотрите также

- {{HTTPHeader("WWW-Authenticate")}}
- {{HTTPHeader("Authorization")}}
- {{HTTPHeader("Proxy-Authorization")}}
- {{HTTPHeader("Proxy-Authenticate")}}
- {{HTTPStatus("401")}}, {{HTTPStatus("403")}}, {{HTTPStatus("407")}}
