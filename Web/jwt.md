# JWT

JWT(JSON Web Token) - открытый стандарт для создания токенов доступа. Основан на формате JSON. В основном, используется для аутентификации и авторизации в web-приложениях.

## Структура

JWT состоит из 3 блоков:

- header(заголовок);
- payload(полезная нагрузка/основные данные);
- signature(подпись).

### Header

Заголовок содержит одно обязательное поле:

- `alg` - алгоритм, используемый для подписи/шифрования. В случае неподписанного JWT используется значение = `none` .

Необязательные поля:

- `typ` - тип токена. Используется, когда присутствуют различные виды токенов с `JOSE`-заголовками (например, `JWS` и `JWE` ). По умолчанию имеет значение = `JWT` .
- `cty` -  тип содержимого. Присутствует только если в содержимом токена есть только служебные поля - в таком случае имеет значение =  `JWT` . В случае наличия пользовательских полей - это поле отсутствует в заголовке.

### Payload

Полезная нагрузка содержит пользовательские данные. Также здесь могут присутствовать некоторые служебные поля. Все они являются необязательными:

- `iss` - издатель токена. Уникальный идентификатор стороны, генерирующей токен;
- `sub` - уникальный идентификатор объекта, о котором содержится информация в этом токене;
- `aud` - получатели токена. Список уникальных идентификаторов, являющихся получателями данного токена. Когда принимающая сторона получает токен она должна проверить наличие себя в получателях и при отсутствии - посчитать токен невалидным;
- `exp` - время в формате Unix Time, определяющее момент, когда токен станет невалидным;
- `nbf` - время в формате Unix Time, определяющее момент, когда токен станет валидным;
- `jti` - уникальный идентификатор токена;
- `iat` - время создания токена в формате Unix Time. Это значение может не совпадать со значением поля `nbf` , т.к. токен может быть выпущен в одно время, но станет валидным позже.

Какие пользовательские данные будут включены в `JWT` определяется сервером аутентификации. Туда могут быть включены, например:

- логин пользователя;
- email пользователя;
- AD группа пользователя и т.д.

### Signature

Задача данного блока заключается в подтверждении того, что данные из блока `Payload` не были подменены и являются идентичны изначально переданным.

## Виды токенов

- `access` - токен - это токен, который предоставляет его владельцу доступ к серверу. Обычно выдается на краткий срок;
- `refresh` - токен - это токен, который позволяет клиентам запрашивать новый `access` - токен, когда тот истечет. Обычно выдается на долгий срок.

## Алгоритм работы

- Клиент проходит аутентификацию в приложении;
- В случае успешной аутентификации - сервер отправляет клиенту `access` и `refresh` токены;
- При обращении к серверу клиент использует `access`-токен. Сервер проверяет его на валидность (критерии успешности этих проверок можно устанавливать вручную). В случае успешного прохождения проверок - клиент получает доступ к ресурсам;
- Когда `access`-токен устаревает, клиент отправляет серверу `refresh`-токен и получает в ответ два обновленных токена;
- Если устаревает `refresh`-токен, то клиенту будет нужно пройти заново процесс аутентификации в приложении.