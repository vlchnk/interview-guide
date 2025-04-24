 # Node.js

## Содержание
1. [Основы](#основы)
2. [Асинхронное программирование](#асинхронное-программирование)
3. [Event Loop](#event-loop)
4. [Express.js](#expressjs)
5. [Работа с файлами](#работа-с-файлами)
6. [Тестирование](#тестирование)
7. [Безопасность](#безопасность)

## Основы

### Модульная система
```javascript
// module.js
exports.hello = function() {
    return 'Hello World';
};

// main.js
const module = require('./module');
console.log(module.hello());
```

### Глобальные объекты
```javascript
// process
process.env.NODE_ENV = 'development';
console.log(process.argv);

// __dirname и __filename
console.log(__dirname);
console.log(__filename);
```

## Асинхронное программирование

### Callbacks
```javascript
const fs = require('fs');

fs.readFile('file.txt', 'utf8', (err, data) => {
    if (err) throw err;
    console.log(data);
});
```

### Promises
```javascript
const fs = require('fs').promises;

fs.readFile('file.txt', 'utf8')
    .then(data => console.log(data))
    .catch(err => console.error(err));
```

### Async/Await
```javascript
async function readFile() {
    try {
        const data = await fs.readFile('file.txt', 'utf8');
        console.log(data);
    } catch (err) {
        console.error(err);
    }
}
```

## Event Loop

### Фазы Event Loop
1. **Timers**: выполнение setTimeout и setInterval
2. **Pending callbacks**: системные операции
3. **Idle, prepare**: внутренние операции
4. **Poll**: получение новых I/O событий
5. **Check**: выполнение setImmediate
6. **Close callbacks**: закрытие соединений

### Примеры
```javascript
// Microtasks vs Macrotasks
console.log('1');
setTimeout(() => console.log('2'), 0);
Promise.resolve().then(() => console.log('3'));
console.log('4');
// Output: 1, 4, 3, 2
```

## Express.js

### Базовое приложение
```javascript
const express = require('express');
const app = express();

app.get('/', (req, res) => {
    res.send('Hello World!');
});

app.listen(3000, () => {
    console.log('Server is running on port 3000');
});
```

### Middleware
```javascript
// Логирование
const logger = (req, res, next) => {
    console.log(`${req.method} ${req.url}`);
    next();
};

// Обработка ошибок
const errorHandler = (err, req, res, next) => {
    console.error(err.stack);
    res.status(500).send('Something broke!');
};

app.use(logger);
app.use(errorHandler);
```

### Маршрутизация
```javascript
const router = express.Router();

router.get('/users', (req, res) => {
    res.json({ users: [] });
});

router.post('/users', (req, res) => {
    // Создание пользователя
    res.status(201).json({ id: 1 });
});

app.use('/api', router);
```

## Работа с файлами

### Streams
```javascript
const fs = require('fs');

// Чтение файла потоком
const readStream = fs.createReadStream('input.txt');
const writeStream = fs.createWriteStream('output.txt');

readStream.pipe(writeStream);
```

### Buffer
```javascript
const buffer = Buffer.from('Hello World');
console.log(buffer.toString('utf8'));
```

## Тестирование

### Jest
```javascript
// sum.js
function sum(a, b) {
    return a + b;
}

// sum.test.js
test('adds 1 + 2 to equal 3', () => {
    expect(sum(1, 2)).toBe(3);
});
```

### Supertest
```javascript
const request = require('supertest');
const app = require('./app');

test('GET /', async () => {
    const response = await request(app).get('/');
    expect(response.statusCode).toBe(200);
    expect(response.text).toBe('Hello World!');
});
```

## Безопасность

### Helmet
```javascript
const helmet = require('helmet');
app.use(helmet());
```

### Rate Limiting
```javascript
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
    windowMs: 15 * 60 * 1000, // 15 минут
    max: 100 // лимит запросов
});

app.use(limiter);
```

### CORS
```javascript
const cors = require('cors');
app.use(cors({
    origin: 'https://example.com',
    methods: ['GET', 'POST']
}));
```

### JWT
```javascript
const jwt = require('jsonwebtoken');

// Создание токена
const token = jwt.sign({ userId: 1 }, 'secret', { expiresIn: '1h' });

// Верификация
jwt.verify(token, 'secret', (err, decoded) => {
    if (err) throw err;
    console.log(decoded);
});
```