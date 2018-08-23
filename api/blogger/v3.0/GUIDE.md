# Быстрый старт

Blogger API v3 позволяет:
- создавать новые посты;
- редактировать посты;
- удалять посты;
- запрашивать посты соответствующие заданным критериям.

Когда приложение запрашивает приватные данные,
запрос должен быть авторизован аутентифицированным пользователем
у которого есть доступ к этим данным.

Когда приложение запрашивает публичные данные, авторизация не нужна,
но запрос должен сопровождаться идентификатором - API ключом.

Основные концепции Blogger:
- блоги:
 - посты;
 - комментарии;
 - страницы;
- пользователи.

Идентификатор **self** ссылается на самого пользователя.

## Операции Blogger API

| Операция	 | Описание   	                                      | REST HTTP mappings                                                |
| ---------- | -------------------------------------------------- | ----------------------------------------------------------------- |
| list	     | Lists all resources within a collection.	          | **GET** on a collection URI.                                      |
| get	       | Gets a specific resource.	                        | **GET** on a resource URI.                                        |
| getByUrl	 | Gets a resource, looking it up by URL.	            | **GET** with the URL passed in as a parameter.                    |
| getByPath  | Gets a resource by looking it up by its path.	    | **GET** with the Path passed in as a parameter.                   |
| listByUser | Lists resources owned by a User.	                  | **GET** on a user owned collection.                               |
| search	   | Search for resources, based on a query parameter.	| **GET** on a Search URL, with the query passed in as a parameter. |
| insert	   | Create a resource in a collection.	                | **POST** on a collection URI.                                     |
| delete	   | Deletes a resource.	                              | **DELETE** on a resource URI.                                     |
| patch	     | Update a resource, using Patch semantics.	        | **PATCH** on a resource URI.                                      |
| update	   | Update a resource.	                                | **PUT** on a resource URI.                                        |

Все list- и get-операции над приватными блогами требуют аутентификации.

Поддержка методов различными ресурсами:

| Resource Type | Supported Methods |     |          |           |            |        |        |        |       |        |
| ------------- | ----------------- | --- | -------- | --------- | ---------- | ------ | ------ | ------ | ----- | ------ |
|               | list	            | get	| getByUrl | getByPath | listByUser | search | insert | delete | patch | update |
| Blogs	        | no	              | yes	| yes	     | no	       | yes	      | no	   | no	    | no	   | no	   | no     |
| Posts	        | yes	              | yes	| no	     | yes	     | no	        | yes	   | yes	  | yes	   | yes	 | yes    |
| Comments	    | yes	              | yes	| no     	 | no	       | no	        | no	   | no	    | no	   | no	   | no     |
| Pages	        | yes	              | yes	| no	     | no	       | no	        | no	   | no	    | no	   | no	   | no     |
| Users	        | no	              | yes	| no	     | no	       | no	        | no	   | no	    | no	   | no	   | no     |

## REST в Blogger API

Шаблон ресурса:

https://www.googleapis.com/apiName/apiVersion/resourcePath?parameters

Ресурсы Blogger API:

- https://www.googleapis.com/blogger/v3/users/userId
- https://www.googleapis.com/blogger/v3/users/self
- https://www.googleapis.com/blogger/v3/users/userId/blogs
- https://www.googleapis.com/blogger/v3/users/self/blogs
- https://www.googleapis.com/blogger/v3/blogs/blogId
- https://www.googleapis.com/blogger/v3/blogs/byurl
- https://www.googleapis.com/blogger/v3/blogs/blogId/posts
- https://www.googleapis.com/blogger/v3/blogs/blogId/posts/bypath
- https://www.googleapis.com/blogger/v3/blogs/blogId/posts/search
- https://www.googleapis.com/blogger/v3/blogs/blogId/posts/postId
- https://www.googleapis.com/blogger/v3/blogs/blogId/posts/postId/comments
- https://www.googleapis.com/blogger/v3/blogs/blogId/posts/postId/comments/commentId
- https://www.googleapis.com/blogger/v3/blogs/blogId/pages
- https://www.googleapis.com/blogger/v3/blogs/blogId/pages/pageId

Пример получения списка блогов аутентифицированным пользователем:

```
GET https://www.googleapis.com/blogger/v3/users/self/blogs?key=YOUR-API-KEY
```

Пример получения списка постов блога code.blogger.com с ID 3213900:

```
GET https://www.googleapis.com/blogger/v3/blogs/3213900?key=YOUR-API-KEY
```

## REST из JavaScript

Пример получения списка постов блога code.blogger.com с ID 3213900
через JavaScript:

```html
<html>
  <head>
    <title>Blogger API Example</title>
  </head>
  <body>
    <div id="content"></div>
    <script>
      function handleResponse(response) {
        document.getElementById("content").innerHTML += "<h1>" + response.title + "</h1>" + response.content;
      }
    </script>
    <script
    src="https://www.googleapis.com/blogger/v3/blogs/3213900/posts/8398240586497962757?callback=handleResponse&key=YOUR-API-KEY"></script>
  </body>
</html>
```

# Авторизация запросов и идентификация приложения

Каждый запрос к Blogger API должен быть идентифицирован как приложение.
Есть два способа идентификации приложения:
- ипользуя OAuth 2.0 токен, который также авторизует запрос;
- ипользуя API ключ.

Если запрос требует авторизации (для доступа к приватным данным),
то приложение должно сопроводить запрос OAuth 2.0 токеном.

Если запрос не требует авторизации (для доступа к публичным данным)
тогда приложение должно сопроводить запрос API ключом
или OAuth 2.0 токеном или обоими сразу.

Приложение должно использовать протокол [OAuth 2.0](https://developers.google.com/identity/protocols/OAuth2) для авторизации.
Если приложение использует [Google Sign-In](https://developers.google.com/identity/#google-sign-in),
то некоторые аспекты авторизации делаются за вас.

## Авторизация запросов с помощью OAuth 2.0

Чтобы авторизовать запрос с помощью OAuth 2.0 нужно получить
OAuth client ID на [Credentials page](https://console.developers.google.com/apis/credentials).

Процесс или "поток" OAuth 2.0 авторизации:
1. Приложение запрашивает у Google доступ к конкретной области.
2. Google запрашивает в окне согласие у пользователя.
3. Если пользователь соглашается, то Google выдает приложению кратковременный access token.
4. Приложение запрашивает данные пользователя сопровождая запрос access токеном.
5. Если всё валидно, то Google выдет запрошенные данные.

В некоторых случаях делается обновление токенов.
О различных потоках авторизации можно почитать в [документации](https://developers.google.com/identity/protocols/OAuth2).

OAuth 2.0 scope information для Blogger API:

```
https://www.googleapis.com/auth/blogger
```

Чтобы получить доступ с помощью OAuth 2.0 приложению нужны:
- scope information;
- client ID и client secret (выдаются Google при регистрации приложения).

[Google APIs client libraries](https://developers.google.com/apis/blogger/docs/3.0/libraries.html) могут делать авторизацию за вас.

## Использование API ключа

Запрос к публичным данным должен сопровождаться идентификатором в виде
[API ключа](https://developers.google.com/console/help/generating-dev-keys)
или [access токена](https://developers.google.com/accounts/docs/OAuth2).

При наличии API ключа его надо добавлять в параметры запроса как **key=yourAPIKey**.
Встраивание API ключа в URL-ы безопасно и не требует дополнительного кодирования.

## Стандартные параметры запроса

1. С каждым запросом должен идти API-ключ или OAuth 2.0 token.
2. Если запрос требует OAuth scope, то с ним должен посылаться authorization token.
3. OAuth 2.0 token может посылаться так:
 - Используя параметр **access_token**, например: **?access_token=oauth2-token**
 - Используя заголовок **HTTP Authorization**, например: **Authorization: Bearer oauth2-token**

| Параметр	   | Значение                                                           | Примечание
| ------------ | ------------------------------------------------------------------ | ----------
| access_token | OAuth 2.0 token for the current user.                              | [One possible way](https://developers.google.com/identity/protocols/OAuth2WebServer#callinganapi) to provide an OAuth 2.0 token.
| callback	   | Callback function.                                                 | Name of the JavaScript callback function that handles the response. Used in JavaScript JSON-P requests.
| fields	     | Selector specifying a subset of fields to include in the response. | For more information, see the [partial response](https://developers.google.com/blogger/docs/3.0/performance.html#partial) section in the Performance Tips document.
| key	         | API key. (REQUIRED*)                                               | *Required unless you provide an OAuth 2.0 token. Your [API key](https://developers.google.com/console/help/using-keys) identifies your project and provides you with API access, quota, and reports. Obtain your project's API key from the [Google API Console](https://console.developers.google.com/).
| prettyPrint	 | Returns response with indentations and line breaks.                | Default value: true. When this is false, it can reduce the response payload size, which might lead to better performance in some environments. You can choose any arbitrary string that uniquely identifies a user, but it is limited to 40 characters. Overrides userIp if both are provided.
| quotaUser	   | Alternative to userIp.                                             | Lets you enforce per-user quotas from a server-side application even in cases when the user's IP address is unknown. This can occur, for example, with applications that run cron jobs on App Engine on a user's behalf. Learn more about [Capping API usage](https://support.google.com/cloud/answer/7035610).
| userIp	     | IP address of the end user for whom the API call is being made.    | Lets you enforce per-user quotas when calling the API from a server-side application. Learn more about [Capping API usage](https://support.google.com/cloud/answer/7035610).

# Быстродействие

## gzip

HTTP-заголовки для включения gzip-сжатия:

```
Accept-Encoding: gzip
User-Agent: my program (gzip)
```

## Частичные запросы

Есть два вида частичных запросов:

- Запросы в которых указаны поля для включения в ответ (параметр **fields**).
- PATCH-запрос содержащий только те поля, которые надо обновить.

### Параметр fields

HTTP GET запрос возвращающий полный ресурс:

```
https://www.googleapis.com/demo/v1?key=YOUR-API-KEY
```

Запрос для частичного ответа:

```
https://www.googleapis.com/demo/v1?key=YOUR-API-KEY&fields=kind,items(title,characteristics/length)
```

Формат параметра **fields** основан на синтаксисе XPath.
Значение параметра **fields** должно быть URL закодированным.

Некоторые API поддерживают параметры для пагинации данных:
**maxResults*** и **nextPageToken**.

### Метод PATCH

PATCH-запрос отличается от PUT-запроса тем, что PUT-запрос выполняется неудачно,
если ему не предоставить требуемые параметры, а также он очищает предыдущие
данные, если ему не предоставить дополнительные параметры. PATCH-запрос более
безопасен потому что изменяются только указанные данные, а пропущенные поля
не очищаются. Исключение составляют только массивы: если их пропускать, то они
не изменяются, но если предоставить любой элемент, то он заменяет всё. Поэтому,
если нужно добавить элемент в массив, то надо сначала получить существующие
элементы массива, чтобы не потерять их.

Простой patch-запрос:

```
PATCH https://www.googleapis.com/demo/v1/324
Authorization: Bearer your_auth_token
Content-Type: application/json

{
  "title": "New title"
}
```

В ответ на patch-запрос сервер возвращает полное представление обновлённого ресурса.
Но если использовать параметр **fields** с patch-запросом, то можно сократить ответ.

Если файрвол не пропускает HTTP PATCH запросы, то можно сделать так:

```
POST https://www.googleapis.com/...
X-HTTP-Method-Override: PATCH
...
```

### Пример read-modify-write цикла:

```
GET https://www.googleapis.com/demo/v1/324?fields=etag,title,comment,characteristics
Authorization: Bearer your_auth_token
```

```
200 OK

{
  "etag": "ETagString"
  "title": "New title"
  "comment": "First comment.",
  "characteristics": {
    "length": "short",
    "level": "5",
    "followers": ["Jo", "Will"],
  }
}
```

```
PATCH https://www.googleapis.com/demo/v1/324?fields=etag,title,comment,characteristics
Authorization: Bearer your_auth_token
Content-Type: application/json
If-Match: "ETagString"

{
  "etag": "ETagString"
  "title": "",                  /* Clear the value of the title by setting it to the empty string. */
  "comment": null,              /* Delete the comment by replacing its value with null. */
  "characteristics": {
    "length": "short",
    "level": "10",              /* Modify the level value. */
    "followers": ["Jo", "Liz"], /* Replace the followers array to delete Will and add Liz. */
    "accuracy": "high"          /* Add a new characteristic. */
  },
}
```

```
200 OK

{
  "etag": "newETagString"
  "title": "",                 /* Title is cleared; deleted comment field is missing. */
  "characteristics": {
    "length": "short",
    "level": "10",             /* Value is updated.*/
    "followers": ["Jo" "Liz"], /* New follower Liz is present; deleted Will is missing. */
    "accuracy": "high"         /* New characteristic is present. */
  }
}
```

### Прямое изменение ресурса:

```
PATCH https://www.googleapis.com/demo/v1/324?fields=comment,characteristics
Authorization: Bearer your_auth_token
Content-Type: application/json

{
  "comment": "A new comment",
  "characteristics": {
    "volume": "loud",
    "accuracy": null
  }
}
```
