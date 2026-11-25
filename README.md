
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
| 1 | Server | | | | |
| 2 | Date | | | | |
| 3 | Content-Type | | | | |
| 4 | Content-Length | | | | |
| 5 | Location | | | | |
| 6 | X-Cache | | | | |
| 7 | X-Cache-Lookup | | | | |
| 8 | Via | | | | |
| 9 | Connection | | | | |

> Якщо полів більше восьми, додай рядки. Якщо походження конкретного поля неможливо встановити за самим виводом, напиши «не визначено» та поясни чому.

---

## Частина D. Висновки

**D.1.** Що з поведінки сервера виявилося неочевидним або несподіваним. Конкретно, з посиланням на рядок виводу.

[Заповнити після перегляду власних результатів A.1–A.5. Наприклад, описати фактичну реакцію на відсутній або змінений Host, якщо вона відрізнялася від очікуваної.]

**D.2.** Яке з полів заголовка викликало найбільше утруднення при визначенні походження (частина B) та з якої причини.

[Назвати конкретне поле з власної таблиці B. Пояснити, чому за одним лише HTTP-виводом складно визначити, чи його сформував кінцевий сервер, чи проміжний вузол.]

**D.3.** Яке питання залишилося без відповіді після виконання роботи.

[Сформулювати одне конкретне питання, що виникло під час аналізу власних результатів.]

---

## Контрольні питання

**1.** У завданні A.1 сервер не надсилав відповіді, доки не було введено порожній рядок. Чим це зумовлено?

Порожній рядок, тобто послідовність `CRLF` після останнього поля заголовка, позначає кінець заголовка HTTP-запиту. До отримання цієї ознаки сервер продовжує очікувати наступні рядки заголовка, тому ще не переходить до обробки запиту та формування відповіді.

**2.** Порівняйте результати завдань A.1, A.2 та A.3.1–A.3.3. За яких значень поля `Host` і за якої версії протоколу сервер обслуговує запит, а за яких — ні? Яку задачу розв'язує поле `Host`?

Остаточну відповідь потрібно сформулювати за фактичними кодами стану з власних виводів. Поле `Host` у HTTP/1.1 указує, до якого доменного вузла адресовано запит, і дає серверу змогу вибрати відповідний сайт, коли на одній IP-адресі розміщено кілька доменів. Порівняй відповідь A.1 (`Host: fsf.org`) з A.2 (поле відсутнє), A.3.1 (`Host: arin.net`), A.3.2 (`Host: opism-pr02.invalid`) та A.3.3 (HTTP/1.0 без `Host`). У звіт додай конкретні коди стану та коротко опиши різницю між відповідями.

**3.** Скільки відповідей надійшло у завданні A.4 і з якими кодами стану? Чи залежить відповідь сервера на порту 80 від запитаного шляху — і що це говорить про роль цього сервера?

Порахуй у фактичному виводі кількість рядків статусу HTTP. Для кожного запиту в одному TCP-з'єднанні сервер може надіслати окрему HTTP-відповідь. Якщо отримано дві відповіді, наведи обидва коди та порівняй реакцію на шлях `/opism-pr02-12345` і `/`. Якщо отримано одну — перевір, чи є у відповіді поле `Connection` або інша ознака, що пояснює закриття з'єднання; не роби висновку без перевірки виводу.

**4.** Які поля заголовка програма `curl` додала самостійно (завдання A.5)? З якою метою їх додано?

Порівняй рядки запиту, позначені `>` у виводі `curl.exe -v`, із вручну сформованим запитом A.1. Саме додаткові рядки, які наявні у запиті curl, але відсутні в A.1, потрібно перелічити. Їх призначення поясни за фактичними назвами полів; не додавай поля, яких немає у твоєму виводі.

**5.** За якими ознаками у вашому виводі виявляється присутність проміжного вузла? Якщо таких ознак не виявлено, поясніть, що з цього випливає.

Перевір заголовки A.1 на поля, які можуть указувати на кешування або посередника, наприклад `Via`, `Age`, `X-Cache`, `CF-Ray`, `Server` та інші подібні поля. Наведи тільки ті, які справді є у твоїй відповіді. Наявність таких ознак може свідчити про проміжний вузол або інфраструктуру доставки, але відсутність явних ознак не доводить, що проміжних вузлів у мережевому тракті немає.

**6.** Три рядки, про які не йшлося на лекції, наведено в **Додатку В**.

---

## Додаток В. Відповіді на питання 6

| № | Рядок виводу (дослівно з власних результатів) | Джерело (номер завдання) |
|---|---|---|
| 1 | [рядок із власного виводу] | A.__ |
| 2 | [рядок із власного виводу] | A.__ |
| 3 | [рядок із власного виводу] | A.__ |

---

## Додаток Д. Зведення результатів завдання A.3

**Вузол, з яким установлювалося з'єднання (у всіх пробах однаковий):** `fsf.org`

| Проба | Значення поля `Host` | Версія | Код стану | Обсяг тіла відповіді | Збігається з A.1 (так / ні) |
|---|---|---|---|---|---|
| A.1 (вихідна) | `fsf.org` | 1.1 | [з виводу] | [байти/символи — однаково виміряний спосіб] | — |
| A.2 | поле відсутнє | 1.1 | [з виводу] | [значення] | [так/ні] |
| A.3.1 | `arin.net` | 1.1 | [з виводу] | [значення] | [так/ні] |
| A.3.2 | `opism-pr02.invalid` | 1.1 | [з виводу] | [значення] | [так/ні] |
| A.3.3 | поле відсутнє | 1.0 | [з виводу] | [значення] | [так/ні] |

**Висновок за таблицею (2–4 речення):**

[Після заповнення таблиці опиши, як зміна лише поля Host або версії HTTP вплинула на код стану та тіло відповіді. Не підставляй очікувані результати замість отриманих.]

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
