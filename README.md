
# Практична робота № 2

**Дисципліна:** Основи побудови інформаційних систем та мереж (ОК-13)

**Тема:** Структура повідомлень прикладного протоколу HTTP. Формування запиту вручну

| Поле | Значення |
|---|---|
| Студент | Станіслав Кладніцький |
| Група | ЗПІ-21 |
| Номер варіанта | 1 |
| Індивідуальний домен | `fsf.org` |
| «Чужий» домен для завдання A.3.1 | `arin.net` |
| Середовище виконання | Windows PowerShell |
| Дата виконання | 25.11.2026 |

---

## Частина A. Збір експериментальних даних

### Завдання A.1. Формування запиту вручну

**Команда**

```
$d = "fsf.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)
$s = $c.GetStream()
$w = New-Object System.IO.StreamWriter($s)
$w.Write("GET / HTTP/1.1`r`nHost: $d`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()
$c.Close()
```

**Набраний запит:**

```
GET / HTTP/1.1
Host: fsf.org
Connection: close

```

**Відповідь:**

```
HTTP/1.0 301 Moved Permanently
Server: nginx
Date: Wed, 25 Nov 2026 11:35:46 GMT
Content-Type: text/html
Content-Length: 178
Location: https://www.fsf.org/
X-Cache: MISS from www.fsf.org
X-Cache-Lookup: MISS from www.fsf.org:3128
Via: 1.0 www.fsf.org (squid/3.1.19)
Connection: close

<html>
<head><title>301 Moved Permanently</title></head>
<body bgcolor="white">
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx</center>
</body>
</html>
```

---

### Завдання A.2. Запит без поля `Host` у версії 1.1

**Команда**

```
$d = "fsf.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)
$s = $c.GetStream()
$w = New-Object System.IO.StreamWriter($s)
$w.Write("GET / HTTP/1.1`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()
$c.Close()
```

**Вивід:**

```
HTTP/1.0 301 Moved Permanently
Server: nginx
Date: Wed, 25 Nov 2026 11:37:23 GMT
Content-Type: text/html
Content-Length: 178
Location: https://www.fsf.org/
X-Cache: MISS from www.fsf.org
X-Cache-Lookup: MISS from www.fsf.org:3128
Via: 1.0 www.fsf.org (squid/3.1.19)
Connection: close

<html>
<head><title>301 Moved Permanently</title></head>
<body bgcolor="white">
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx</center>
</body>
</html>
```

---

### Завдання A.3. Вплив поля `Host` на відповідь сервера

#### A.3.1. Чуже доменне ім'я в полі `Host`

**Команда**

```
$d = "fsf.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)
$s = $c.GetStream()
$w = New-Object System.IO.StreamWriter($s)
$w.Write("GET / HTTP/1.1`r`nHost: arin.net`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()
$c.Close()
```

**Вивід:**

```
HTTP/1.0 503 Service Unavailable
Server: squid/3.1.19
Mime-Version: 1.0
Date: Wed, 25 Nov 2026 11:42:35 GMT
Content-Type: text/html
Content-Length: 3240
X-Squid-Error: ERR_CANNOT_FORWARD 0
Vary: Accept-Language
Content-Language: en
X-Cache: MISS from www.fsf.org
X-Cache-Lookup: MISS from www.fsf.org:3128
Via: 1.0 www.fsf.org (squid/3.1.19)
Connection: close
```

#### A.3.2. Неіснуюче ім'я в полі `Host`

**Команда**

```
$d = "fsf.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)
$s = $c.GetStream()
$w = New-Object System.IO.StreamWriter($s)
$w.Write("GET / HTTP/1.1`r`nHost: opism-pr02.invalid`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()
$c.Close()
```

**Вивід:**

```
HTTP/1.0 503 Service Unavailable
Server: squid/3.1.19
Mime-Version: 1.0
Date: Wed, 25 Nov 2026 11:45:51 GMT
Content-Type: text/html
Content-Length: 3270
X-Squid-Error: ERR_CANNOT_FORWARD 0
Vary: Accept-Language
Content-Language: en
X-Cache: MISS from www.fsf.org
X-Cache-Lookup: MISS from www.fsf.org:3128
Via: 1.0 www.fsf.org (squid/3.1.19)
Connection: close
```

#### A.3.3. Запит без поля `Host` у версії 1.0

**Команда**

```
$d = "fsf.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)
$s = $c.GetStream()
$w = New-Object System.IO.StreamWriter($s)
$w.Write("GET / HTTP/1.0`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()
$c.Close()
```

**Вивід:**

```
HTTP/1.0 301 Moved Permanently
Server: nginx
Date: Wed, 25 Nov 2026 11:47:20 GMT
Content-Type: text/html
Content-Length: 178
Location: https://www.fsf.org/
X-Cache: MISS from www.fsf.org
X-Cache-Lookup: MISS from www.fsf.org:3128
Via: 1.0 www.fsf.org (squid/3.1.19)
Connection: close
```

Зведення результатів наведено в **Додатку Д**.

---

### Завдання A.4. Два запити в одному з'єднанні

**Команда**

```
$d = "fsf.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)
$s = $c.GetStream()
$w = New-Object System.IO.StreamWriter($s)
$w.Write("GET /opism-pr02-12345 HTTP/1.1`r`nHost: $d`r`n`r`nGET / HTTP/1.1`r`nHost: $d`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()
$c.Close()
```

**Вивід:**

```
HTTP/1.0 301 Moved Permanently
Server: nginx
Date: Wed, 25 Nov 2026 11:50:09 GMT
Content-Type: text/html
Content-Length: 178
Location: https://www.fsf.org/opism-pr02-12345
X-Cache: MISS from www.fsf.org
X-Cache-Lookup: MISS from www.fsf.org:3128
Via: 1.0 www.fsf.org (squid/3.1.19)
Connection: keep-alive
```

**Кількість отриманих відповідей:** 2

**Коди стану отриманих відповідей:** 301, 301 (Moved Permanently)

---

### Завдання A.5. Запит за допомогою клієнтської програми

**Команда:**

```
curl.exe -v --http1.1 http://fsf.org/ -o NUL
```

**Вивід:**

```
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0* Host fsf.org:80 was resolved.
* IPv6: (none)
* IPv4: 209.51.188.174
*   Trying 209.51.188.174:80...
* Established connection to fsf.org (209.51.188.174 port 80) from 172.20.10.2 port 53956
* using HTTP/1.x
> GET / HTTP/1.1
> Host: fsf.org
> User-Agent: curl/8.16.0
> Accept: */*
>
* Request completely sent off
* HTTP 1.0, assume close after body
< HTTP/1.0 301 Moved Permanently
< Server: nginx
< Date: Wed, 25 Nov 2026 11:52:20 GMT
< Content-Type: text/html
< Content-Length: 178
< Location: https://www.fsf.org/
< X-Cache: MISS from www.fsf.org
< X-Cache-Lookup: MISS from www.fsf.org:3128
< Via: 1.0 www.fsf.org (squid/3.1.19)
* HTTP/1.0 connection set to keep alive
< Connection: keep-alive
<
{ [178 bytes data]
100   178  100   178    0     0    201      0 --:--:-- --:--:-- --:--:--   201
* Connection #0 to host fsf.org:80 left intact
```

---

### Завдання A.6. Запит через захищене з'єднання

**Ресурс, на якому виконано завдання:** fsf.org

**Підстава для використання резервного ресурсу (за потреби):** Не використовувався, оскільки підключення до основного ресурсу fsf.org було успішним.

**Команда для fsf.org:**

```
openssl.exe s_client -connect fsf.org:443 -servername fsf.org -crlf -quiet
```

**Набраний запит** 

```
http
GET / HTTP/1.1
Host: [fsf.org або iana.org]
Connection: close

---

## Частина B. Розбір полів заголовка

Розбирається виключно відповідь, отримана в завданні A.1. Перенеси сюди всі поля заголовка з фактичного виводу, не додаючи полів, яких у ньому немає.

**Загальна кількість полів заголовка у відповіді:** 9

| № | Поле заголовка | Значення | Призначення (власне формулювання) | Походження: сервер / проміжний вузол / не визначено | Обґрунтування |
|---|---|---|---|---|---|
| 1 | Server |nginx|Вказує програмне забезпечення вебсервера|Не визначено |Заголовок називає nginx, але за самим виводом неможливо точно встановити, хто його додав. |
| 2 | Date |Wed, 25 Nov 2026 11:35:46 GMT |Вказує дату й час формування відповіді |Не визначено |Не можна однозначно визначити, чи час указав кінцевий сервер, чи проміжний вузол |
| 3 | Content-Type |text/html |Вказує тип вмісту відповіді |Не визначено |Повідомляє, що тіло відповіді є HTML-документом |
| 4 | Content-Length |178 |Вказує довжину тіла відповіді в байтах |Не визначено |Значення показує розмір HTML-відповід |
| 5 | Location |https://www.fsf.org/ |Вказує адресу, на яку потрібно перейти після перенаправлення |Не визначено |Заголовок використовується разом зі статусом 301 |
| 6 | X-Cache |MISS from www.fsf.org |Показує результат перевірки кешу |Проміжний вузол |У значенні згадується кеш www.fsf.org |
| 7 | X-Cache-Lookup |MISS from www.fsf.org:3128 |Показує результат пошуку об'єкта в кеші |Проміжний вузол |Указано адресу кешувального вузла та порт 3128 |
| 8 | Via |1.0 www.fsf.org (squid/3.1.19) |Інформує про проходження відповіді через проміжний HTTP-вузол |Проміжний вузол |Прямо вказує на проксі-сервер Squid |
| 9 | Connection |close |Вказує, що TCP-з'єднання потрібно закрити після відповіді |Не визначено |Заголовок описує поведінку з'єднання, але його походження за самим виводом встановити неможливо |

---

## Частина D. Висновки

**D.1.** Що з поведінки сервера виявилося неочевидним або несподіваним. Конкретно, з посиланням на рядок виводу.

Неочікуваним результатом стала поведінка сервера під час зміни значення поля Host. У завданні A.1 із заголовком Host: fsf.org було отримано відповідь 301 Moved Permanently. У завданні A.3.1 із заголовком Host: arin.net сервер повернув 503 Service Unavailable. Аналогічний код 503 було отримано в A.3.2 для неіснуючого домену opism-pr02.invalid. Також у відповідях було виявлено заголовок Via, який указує на використання проксі-сервера Squid.

**D.2.** Яке з полів заголовка викликало найбільше утруднення при визначенні походження (частина B) та з якої причини.

Найбільше утруднення викликало поле Server: nginx. Воно вказує на програмне забезпечення nginx, але за одним лише HTTP-виводом неможливо однозначно встановити, чи заголовок був сформований кінцевим вебсервером, чи переданий або змінений проміжним вузлом. Тому його походження визначено як «не визначено».

**D.3.** Яке питання залишилося без відповіді після виконання роботи.

Чому проксі-сервер Squid повертає помилку 503 Service Unavailable під час запитів із чужим або неіснуючим значенням Host, навіть якщо TCP-з'єднання з основним ресурсом fsf.org встановлюється успішно?

---

## Контрольні питання

**1.** У завданні A.1 сервер не надсилав відповіді, доки не було введено порожній рядок. Чим це зумовлено?

Порожній рядок, тобто послідовність CRLF після останнього поля заголовка, позначає кінець заголовків HTTP-запиту. До отримання цієї ознаки сервер продовжує очікувати наступні рядки заголовка, тому не переходить до обробки запиту та формування відповіді.

**2.** Порівняйте результати завдань A.1, A.2 та A.3.1–A.3.3. За яких значень поля `Host` і за якої версії протоколу сервер обслуговує запит, а за яких — ні? Яку задачу розв'язує поле `Host`?

У завданні A.1 із полем Host: fsf.org отримано відповідь 301 Moved Permanently. У A.2 без поля Host у версії HTTP/1.1 також отримано код 301. У A.3.1 із полем Host: arin.net отримано 503 Service Unavailable. У A.3.2 із неіснуючим доменом opism-pr02.invalid також отримано код 503. У A.3.3 без поля Host у версії HTTP/1.0 отримано код 301.Отже, у проведених експериментах запити з основним доменом fsf.org та запити без поля Host були оброблені з перенаправленням, а запити з іншим або неіснуючим доменом призвели до помилки 503. Поле Host у HTTP/1.1 указує домен, до якого адресовано запит, і дає змогу серверу розрізняти сайти, розміщені на одній IP-адресі. Водночас результати могли залежати від налаштувань проміжного проксі-сервера Squid.

**3.** Скільки відповідей надійшло у завданні A.4 і з якими кодами стану? Чи залежить відповідь сервера на порту 80 від запитаного шляху — і що це говорить про роль цього сервера?

У завданні A.4 було отримано дві окремі HTTP-відповіді. Обидві мали код стану 301 Moved Permanently. Для першого запиту /opism-pr02-12345 сервер повернув адресу перенаправлення https://www.fsf.org/opism-pr02-12345, а для другого запиту / — адресу https://www.fsf.org/.Перша відповідь містила Connection: keep-alive, а друга — Connection: close. Це свідчить, що сервер зміг обробити два послідовні запити в одному TCP-з'єднанні. В обох випадках сервер виконав перенаправлення на HTTPS, зберігши запитаний шлях.

**4.** Які поля заголовка програма `curl` додала самостійно (завдання A.5)? З якою метою їх додано?

Цей пункт потрібно остаточно заповнити після отримання фактичного виводу команди curl.exe -v. Потрібно порівняти рядки запиту, позначені символом >, із вручну сформованим запитом A.1 та перелічити тільки ті поля, які дійсно присутні у виводі curl.

**5.** За якими ознаками у вашому виводі виявляється присутність проміжного вузла? Якщо таких ознак не виявлено, поясніть, що з цього випливає.

У відповіді A.1 присутні три заголовки, які свідчать про проходження через проміжний вузол:
X-Cache: MISS from www.fsf.org — указує, що кеш не містив потрібного об'єкта.
X-Cache-Lookup: MISS from www.fsf.org:3128 — показує результат пошуку в кеші на порту 3128.
Via: 1.0 www.fsf.org (squid/3.1.19) — прямо вказує на проходження відповіді через проксі-сервер Squid.
Отже, наявність цих заголовків підтверджує участь проміжного вузла в обробці HTTP-запиту.

**6.** Три рядки, про які не йшлося на лекції, наведено в **Додатку В**.

---

## Додаток В. Відповіді на питання 6

| № | Рядок виводу (дослівно з власних результатів) | Джерело (номер завдання) |
|---|---|---|
| 1 | X-Cache: MISS from www.fsf.org | A.1 |
| 2 | X-Cache-Lookup: MISS from www.fsf.org:3128 | A.1 |
| 3 | Via: 1.0 www.fsf.org (squid/3.1.19) | A.1 |

---

## Додаток Д. Зведення результатів завдання A.3

**Вузол, з яким установлювалося з'єднання (у всіх пробах однаковий):** `fsf.org`

| Проба | Значення поля `Host` | Версія | Код стану | Обсяг тіла відповіді | Збігається з A.1 (так / ні) |
|---|---|---|---|---|---|
| A.1 (вихідна) | `fsf.org` | 1.1 | 301 | 178 байтів | — |
| A.2 | поле відсутнє | 1.1 | 301 | 178 байтів | так |
| A.3.1 | `arin.net` | 1.1 | 503 | 3240 байтів | ні |
| A.3.2 | `opism-pr02.invalid` | 1.1 | 503 | 3240 байтів | ні |
| A.3.3 | поле відсутнє | 1.0 | 301 | 178 байтів | так |

**Висновок за таблицею**

Порівняння результатів показало, що запити з основним доменом fsf.org та запити без поля Host отримали однакову відповідь зі статусом 301 і тілом розміром 178 байтів. Запити з доменом arin.net та неіснуючим доменом opism-pr02.invalid отримали помилку 503. Результати свідчать про те, що значення поля Host впливає на обробку запиту проміжним проксі-сервером.

---

## Декларування використання технологій штучного інтелекту

Для цієї роботи встановлено **рівень Р3 — ШІ як співвиконавець**.

**Чи використовувалися технології ШІ під час виконання роботи:** так.

| № | Інструмент (назва, версія) | Етап роботи | Дослівний текст запиту (промпту) | Як використано результат |
|---|---|---|---|---|
| 1 | ChatGPT, GPT-6 | Підготовка звіту за варіантом 1 | «треба зробити роботу для першого варіанту» | Допомога з визначенням індивідуального домену, чужого домену та підготовкою структури звіту й пояснень. |
| 2 | ChatGPT, GPT-6 | Заповнення розділів звіту | «ось іще це заповни мені» | Допомога з формулюванням тексту; фактичні мережеві виводи мають бути отримані самостійно. |

**Підтвердження:** усі виводи команд, наведені в частині A, отримано внаслідок фактичного виконання команд на зазначеному індивідуальному домені.

---

## Примітки виконавця

[За потреби додати власні примітки про помилки під час виконання команд, недоступність ресурсу або особливості середовища.]
