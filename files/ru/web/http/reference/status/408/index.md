---
title: 408 Request Timeout
slug: Web/HTTP/Reference/Status/408
---

HTTP **`408 Request Timeout`** означает, что сервер хотел бы отключить это неиспользуемое соединение. Он отправляется на незанятое соединение некоторыми серверами, _даже без какого-либо предыдущего запроса клиентом_

Сервер должен отправить заголовок {{HTTPHeader("Connection")}} со значением «close» в ответ, поскольку **408** подразумевает, что сервер решил закрыть соединение, а не продолжать ждать.

Этот ответ используется гораздо больше, поскольку некоторые браузеры, такие как Chrome, Firefox 27+ или IE9, используют механизмы предварительного подключения HTTP для ускорения сёрфинга. Также обратите внимание, что некоторые серверы просто закрывают соединение без отправки этого сообщения.

## Статус

```
408 Request Timeout
```

## Спецификации

| Спецификация                                     | Титул                                                         |
| ------------------------------------------------ | ------------------------------------------------------------- |
| {{RFC("7231", "408 Request Timeout" , "6.5.7")}} | Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content |

## Смотрите также

- {{HTTPHeader("Connection")}}
- {{HTTPHeader("X-DNS-Prefetch-Control")}}
- [408 Request Timeout](https://www.exai.com/blog/408-request-timeout-error)
