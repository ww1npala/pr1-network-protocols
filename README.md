# Звіт з практичної роботи №1
**Тема:** Дослідження мережевих протоколів засобами інструментів розробника  
**Виконав:** студент групи КН-41 **Тарновський Артур**  
**Репозиторій GitHub:** [ww1npala/pr1-network-protocols](https://github.com/ww1npala/pr1-network-protocols)  

---

## Мета роботи
- Опанувати інструменти дослідження мережевих протоколів HTTP та HTTPS (утиліта `curl`, розширення REST Client у VS Code, панель розробника браузера DevTools).
- Набути практичних навичок формування, передачі та структурного аналізу клієнт-серверних HTTP-запитів із різними методами (GET, POST, PUT, DELETE), статусами відповідей та керуючими заголовками.
- Дослідити механізми захищеного транспорту TLS/HTTPS, життєвий цикл і атрибути безпеки Cookie (`HttpOnly`, `Secure`, `SameSite`), а також функціонування політики безпеки CORS.

---

## Рівень 1. Базовий: дослідження HTTP через DevTools та REST Client

### 1.1 Виконання REST-запитів у файлі `requests.http`

Запити сформовано та виконано за допомогою розширення **REST Client** у VS Code.

#### 1. Отримання даних товару (GET)
* **Запит:** `GET https://dummyjson.com/products/1`
* **Результат:** Код статусу `200 OK`, отримано JSON-об'єкт товару з `id: 1`.
<img width="1920" height="1032" alt="Code_rGOkKzCvrs" src="https://github.com/user-attachments/assets/9358e2cb-d35a-4527-a384-fee1632ad0db" />

#### 2. Створення нового товару (POST)
* **Запит:** `POST https://dummyjson.com/products/add`
* **Тіло:** `{"title": "New Product", "price": 100}`
* **Результат:** Код статусу `201 Created`, сервер повернув створений об'єкт із згенерованим `id: 195`.
<img width="1920" height="1032" alt="Code_MOZJZ84ngu" src="https://github.com/user-attachments/assets/702ed3b8-a51e-4dd6-9e21-765c59fa14b2" />

#### 3. Оновлення товару (PUT)
* **Запит:** `PUT https://dummyjson.com/products/1`
* **Тіло:** `{"title": "Updated Product"}`
* **Результат:** Код статусу `200 OK`, змінено поле `title`.
<img width="1920" height="1032" alt="Code_SLRaQPp0lo" src="https://github.com/user-attachments/assets/41e03039-d177-453f-8428-e4db7a646aa0" />

#### 4. Видалення товару (DELETE)
* **Запит:** `DELETE https://dummyjson.com/products/1`
* **Результат:** Код статусу `200 OK`, у тілі відповіді повернуто прапорець `"isDeleted": true`.
<img width="1920" height="1032" alt="Code_OjOGe0H0Wm" src="https://github.com/user-attachments/assets/1b9127e9-4a9d-474c-bd96-991980492e68" />

---

### 1.2 Аналіз мережевої активності у Chrome DevTools

Здійснено перехід на сторінку `https://dummyjson.com/products` з відкритою вкладкою **Network**.

#### 1. Метадані запиту та заголовки (Headers)
Фіксація ключових параметрів мережевої взаємодії:

| Категорія | Параметр | Значення |
| :--- | :--- | :--- |
| **General** | Request URL | `https://dummyjson.com/products` |
| | Request Method | `GET` |
| | Status Code | `200 OK` |
| | Remote Address | `104.21.49.196:443` |
| **Response Headers** | `content-type` | `application/json; charset=utf-8` |
| | `server` | `cloudflare` |
| | `etag` | `W/"ac3a-Q0j5X7Zb/GG4CpZwhP3POutAwN4"` |
| **Request Headers** | `accept` | `text/html,application/xhtml+xml,...` |
| | `user-agent` | `Mozilla/5.0 (Windows NT 10.0; Win64; x64)...` |
| | `accept-encoding` | `gzip, deflate, br, zstd` |
<img width="1920" height="945" alt="image" src="https://github.com/user-attachments/assets/92db2dd7-6062-4d33-87d8-d4ca534a9be9" />

#### 2. Профілювання затримок (Timing)
Аналіз часових фаз проходження запиту:
- **DNS Lookup:** Час резолвінгу доменного імені в IP-адресу.
- **Initial connection / TLS Handshake:** Час встановлення TCP-з'єднання та захищеного сеансу.
- **Waiting for server response (TTFB):** Час очікування першого байта відповіді від сервера.
- **Content Download:** Час безпосереднього завантаження тіла відповіді.
<img width="1920" height="945" alt="image" src="https://github.com/user-attachments/assets/a1fbf16f-07dd-42fd-a2f8-f56fae1091b1" />

---

## Рівень 2. Консольна діагностика мережевої взаємодії через `curl`
Налаштовано взаємодію через `curl`. Виконано авторизований запит із JWT-токеном. Знято метрики продуктивності з'єднання (DNS, TCP, TLS, TTFB).
#### 1. Отримання списку користувачів (GET із вибіркою полів): <img width="1322" height="44" alt="image" src="https://github.com/user-attachments/assets/d80eee30-46af-47ea-b2e2-4b3735623dee" />

#### 1.1 Створення сутності (POST з передачею JSON): <img width="1161" height="91" alt="image" src="https://github.com/user-attachments/assets/5288c634-ba17-4d09-a21b-c20256436aaa" />

#### 2. Аналіз заголовків та статусів без завантаження тіла: <img width="1532" height="435" alt="image" src="https://github.com/user-attachments/assets/fc1c4e72-8816-40e4-9eac-5592f09a0156" />

#### 2.1 Запити до статусів помилок (404 та 500): 
<img width="523" height="333" alt="image" src="https://github.com/user-attachments/assets/a7feb352-d765-4878-b7fd-3aa572753194" />

#### 2.2 Автентифікація через токен (Bearer Auth):
<img width="1269" height="85" alt="image" src="https://github.com/user-attachments/assets/666aa1b3-c27b-4265-b0ad-e0882948734a" />

#### 2.3 Вимірювання мережевих метрик (Performance profiling):
<img width="1537" height="54" alt="image" src="https://github.com/user-attachments/assets/154c5a9b-b9cf-4add-9b60-ba3314a116a6" />

## Рівень 3. Просунутий: глибинний аналіз HTTPS/TLS, Cookie та CORS
#### 3.1 Аналіз TLS Handshake та редиректів
Детальне трасування TLS-з'єднання (-v / Verbose):
<img width="1533" height="663" alt="image" src="https://github.com/user-attachments/assets/f6e37213-9f87-4ff8-8bd1-cd5f537a5eb7" />

#### 3.2 Дослідження життєвого циклу Cookie (Cookie Jar)
1. Збереження отриманих Cookie у файл cookies.txt (-c): <img width="910" height="120" alt="image" src="https://github.com/user-attachments/assets/9ae064bd-7ff8-4f9e-9437-c73b2d4979c8" />
2. Перевірка вмісту створеного файлу cookies.txt: <img width="560" height="152" alt="image" src="https://github.com/user-attachments/assets/b5fc7815-693e-400b-ad16-7bd47bc579f6" />
3. Передача збережених Cookie у наступному запиті (-b): <img width="600" height="147" alt="image" src="https://github.com/user-attachments/assets/31e5759f-802c-49f1-8724-12c585643510" />

#### 3.3 Аналіз заголовків безпеки Cookie
<img width="1149" height="302" alt="image" src="https://github.com/user-attachments/assets/3968a0c4-13f1-4299-a21b-95974808e5fb" />

#### 3.4 Симуляція CORS Preflight запиту (OPTIONS)
<img width="1558" height="277" alt="image" src="https://github.com/user-attachments/assets/522abfa7-84cf-4d11-8704-31636b968eab" />

## Висновок: У ході виконання практичної роботи опановано інструменти DevTools, REST Client та `curl` для дослідження REST API, профілювання затримок мережевого з'єднання та аналізу механізмів безпеки HTTPS, Cookie й CORS.
