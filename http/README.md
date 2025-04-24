 # HTTP

## Содержание
1. [Основы протокола](#основы-протокола)
2. [Методы HTTP](#методы-http)
3. [REST API](#rest-api)
4. [Коды состояния](#коды-состояния)
5. [Заголовки](#заголовки)
6. [Безопасность](#безопасность)
7. [Оптимизация](#оптимизация)

## Основы протокола

### Структура запроса
```
GET /index.html HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0
Accept: text/html
```

### Структура ответа
```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1234

<!DOCTYPE html>
...
```

## Методы HTTP

### GET
- Получение ресурса
- Идемпотентный
- Кэшируемый
- Не имеет тела запроса

### POST
- Создание ресурса
- Не идемпотентный
- Имеет тело запроса
- Не кэшируемый

### PUT
- Обновление ресурса
- Идемпотентный
- Имеет тело запроса

### DELETE
- Удаление ресурса
- Идемпотентный
- Не имеет тела запроса

### PATCH
- Частичное обновление
- Не идемпотентный
- Имеет тело запроса

## REST API

### Принципы REST
1. **Stateless**: сервер не хранит состояние клиента
2. **Единообразие интерфейса**:
   - Ресурсы идентифицируются URI
   - Использование стандартных методов HTTP
   - Представление ресурсов
   - Гипермедиа как движок состояния приложения
3. **Кэширование**
4. **Многоуровневая система**
5. **Код по требованию**

### Пример RESTful API
```javascript
// Получение списка пользователей
GET /api/users

// Получение конкретного пользователя
GET /api/users/1

// Создание пользователя
POST /api/users
{
    "name": "John",
    "email": "john@example.com"
}

// Обновление пользователя
PUT /api/users/1
{
    "name": "John Doe"
}

// Удаление пользователя
DELETE /api/users/1
```

## Коды состояния

### 2xx - Успех
- 200 OK
- 201 Created
- 204 No Content

### 3xx - Перенаправление
- 301 Moved Permanently
- 302 Found
- 304 Not Modified

### 4xx - Ошибка клиента
- 400 Bad Request
- 401 Unauthorized
- 403 Forbidden
- 404 Not Found
- 429 Too Many Requests

### 5xx - Ошибка сервера
- 500 Internal Server Error
- 502 Bad Gateway
- 503 Service Unavailable

## Заголовки

### Общие заголовки
```
Cache-Control: no-cache
Connection: keep-alive
Date: Wed, 21 Oct 2015 07:28:00 GMT
```

### Заголовки запроса
```
Accept: text/html
Accept-Encoding: gzip, deflate
Authorization: Bearer token
User-Agent: Mozilla/5.0
```

### Заголовки ответа
```
Content-Type: application/json
Content-Length: 1234
Set-Cookie: session=abc123
```

## Безопасность

### HTTPS
- Шифрование данных
- Аутентификация сервера
- Защита от MITM-атак

### CORS
```javascript
// Настройка CORS в Express
const cors = require('cors');

app.use(cors({
    origin: 'https://example.com',
    methods: ['GET', 'POST'],
    allowedHeaders: ['Content-Type', 'Authorization']
}));
```

### JWT
```javascript
// Создание токена
const jwt = require('jsonwebtoken');
const token = jwt.sign({ userId: 1 }, 'secret', { expiresIn: '1h' });

// Верификация
jwt.verify(token, 'secret', (err, decoded) => {
    if (err) throw err;
    console.log(decoded);
});
```

## Оптимизация

### Кэширование
```javascript
// Настройка кэширования в Express
const cache = require('express-cache-headers');

app.use(cache({
    maxAge: 3600 // 1 час
}));
```

### Сжатие
```javascript
const compression = require('compression');
app.use(compression());
```

### HTTP/2
- Мультиплексирование
- Сжатие заголовков
- Server Push
- Приоритизация запросов

### Оптимизация изображений
```javascript
// Использование WebP
app.use(express.static('public', {
    setHeaders: (res, path) => {
        if (path.endsWith('.webp')) {
            res.setHeader('Content-Type', 'image/webp');
        }
    }
}));
```