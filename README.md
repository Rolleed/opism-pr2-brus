# opism-pr2-brus

# Практична робота № 2

**Дисципліна:** Основи побудови інформаційних систем та мереж (ОК-13)

**Тема:** Структура повідомлень прикладного протоколу HTTP. Формування запиту вручну

| Поле | Значення |
|---|---|
| Студент | Брус Микита Олександрович |
| Група | ІПЗ-2.01 |
| Номер варіанта | 3 |
| Індивідуальний домен | haproxy.org |
| «Чужий» домен для завдання A.3.1 (варіант - 18) | ietf.org |
| Середовище виконання | PowerShell |
| Дата виконання | 06.10.2026 |

> Бланк заповнюють, не змінюючи структури розділів. Порожні заготовки блоків коду замінюють власними виводами. Позначки-підказки в кутових дужках вилучають.

---

## Частина A. Збір експериментальних даних

### Завдання A.1. Формування запиту вручну

**Команда:**

```
$d = "haproxy.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)$s = $c.GetStream()$w = New-Object System.IO.StreamWriter($s)$w.Write("GET / HTTP/1.1`r`nHost: $d`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()$c.Close()
```

**Набраний запит:**

```
GET / HTTP/1.1
Host: haproxy.org
Connection: close
```

**Відповідь:**

```
HTTP/1.1 301 Moved Permanently
content-length: 0
location: [http://www.haproxy.org/](http://www.haproxy.org/)
alt-svc: h2=":443"; ma=3600
alt-svc: h3=":443"; ma=3600
set-cookie: served=1:TCP:IPv4
connection: close

```

---

### Завдання A.2. Запит без поля `Host` у версії 1.1

**Команда:**

```
$d = "haproxy.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)$s = $c.GetStream()$w = New-Object System.IO.StreamWriter($s)$w.Write("GET / HTTP/1.1`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()$c.Close()
```

**Вивід:**

```
HTTP/1.1 403 Forbidden
content-length: 93
cache-control: no-cache
content-type: text/html
set-cookie: served=1:TCP:IPv4
connection: close

<html><body><h1>403 Forbidden</h1>
Request forbidden by administrative rules.
</body></html>

```

---

### Завдання A.3. Вплив поля `Host` на відповідь сервера

#### A.3.1. Чуже доменне ім'я в полі `Host`

**Команда:**

```
$d = "haproxy.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)$s = $c.GetStream()$w = New-Object System.IO.StreamWriter($s)$w.Write("GET / HTTP/1.1`r`nHost: ietf.org`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()$c.Close()
```

**Вивід:**

```
HTTP/1.1 404 Not Found
date: Tue, 06 Oct 2026 00:10:05 GMT
server: Apache
content-length: 198
content-type: text/html; charset=iso-8859-1
set-cookie: served=1:TCP:IPv4
connection: close

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>404 Not Found</title>
</head><body>
<h1>Not Found</h1>
<p>The requested URL / was not found on this server.</p>
</body></html>
```

#### A.3.2. Неіснуюче ім'я в полі `Host`

**Команда:**

```
$d = "haproxy.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)$s = $c.GetStream()$w = New-Object System.IO.StreamWriter($s)$w.Write("GET / HTTP/1.1`r`nHost: opism-pr02.invalid`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()$c.Close()
```

**Вивід:**

```
HTTP/1.1 404 Not Found
date: Tue, 06 Oct 2026 00:13:03 GMT
server: Apache
content-length: 198
content-type: text/html; charset=iso-8859-1
set-cookie: served=1:TCP:IPv4
connection: close

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>404 Not Found</title>
</head><body>
<h1>Not Found</h1>
<p>The requested URL / was not found on this server.</p>
</body></html>

```

#### A.3.3. Запит без поля `Host` у версії 1.0

**Команда:**

```
$d = "haproxy.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)$s = $c.GetStream()$w = New-Object System.IO.StreamWriter($s)$w.Write("GET / HTTP/1.0`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()$c.Close()
```

**Вивід:**

```
HTTP/1.1 404 Not Found
date: Tue, 06 Oct 2026 00:14:25 GMT
server: Apache
content-length: 198
keep-alive: timeout=15, max=100
content-type: text/html; charset=iso-8859-1
set-cookie: served=1:TCP:IPv4
connection: close

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>404 Not Found</title>
</head><body>
<h1>Not Found</h1>
<p>The requested URL / was not found on this server.</p>
</body></html>

```

Зведення результатів наведено в **Додатку Д**.

---

### Завдання A.4. Два запити в одному з'єднанні

**Команда:**

```
$d = "haproxy.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)$s = $c.GetStream()$w = New-Object System.IO.StreamWriter($s)$w.Write("GET /opism-pr02-12345 HTTP/1.1`r`nHost: $d`r`n`r`nGET / HTTP/1.1`r`nHost: $d`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()$c.Close()
```

**Вивід:**

```
HTTP/1.1 301 Moved Permanently
content-length: 0
location: [http://www.haproxy.org/opism-pr02-12345](http://www.haproxy.org/opism-pr02-12345)
alt-svc: h2=":443"; ma=3600
alt-svc: h3=":443"; ma=3600
set-cookie: served=1:TCP:IPv4

HTTP/1.1 301 Moved Permanently
content-length: 0
location: [http://www.haproxy.org/](http://www.haproxy.org/)
alt-svc: h2=":443"; ma=3600
alt-svc: h3=":443"; ma=3600
set-cookie: served=1:TCP:IPv4
connection: close

```

**Кількість отриманих відповідей: 2**

**Коди стану отриманих відповідей: 301, 301**

---

### Завдання A.5. Запит за допомогою клієнтської програми

**Команда:**

```
curl.exe -v --http1.1 http://haproxy.org/ -o NUL
```

**Вивід:**

```
* Host haproxy.org:80 was resolved.
* IPv6: (none)
* IPv4: 51.15.8.218
*   Trying 51.15.8.218:80...
* Established connection to haproxy.org (51.15.8.218 port 80) from 192.168.31.39 port 53947
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0* using HTTP/1.x
> GET / HTTP/1.1
> Host: haproxy.org
> User-Agent: curl/8.21.0
> Accept: */*
>
* Request completely sent off
< HTTP/1.1 301 Moved Permanently
< content-length: 0
< location: [http://www.haproxy.org/](http://www.haproxy.org/)
< alt-svc: h2=":443"; ma=3600
< alt-svc: h3=":443"; ma=3600
< set-cookie: served=1:TCP:IPv4
<
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
* Connection #0 to host haproxy.org:80 left intact
```

---

### Завдання A.6. Запит через захищене з'єднання

**Ресурс, на якому виконано завдання:** <`haproxy.org`>

**Підстава для використання резервного ресурсу (заповнюють за потреби): резервний ресурс не знадобився, з'єднання з haproxy.org:443 успішно встановлено**

**Команда:**

```
"GET / HTTP/1.1`r`nHost: www.haproxy.org`r`nConnection: close`r`n`r`n" | openssl.exe s_client -connect haproxy.org:443 -servername www.haproxy.org -quiet
```

**Набраний запит:**

```
GET / HTTP/1.1
Host: www.haproxy.org
Connection: close
```

**Вивід:**

```
Connecting to 51.15.8.218
depth=2 C=US, O=SSL Corporation, CN=SSL.com TLS RSA Root CA 2022
verify error:num=20:unable to get local issuer certificate
verify return:1
depth=1 C=US, O=SSL Corporation, CN=SSL.com TLS Issuing RSA CA R1
verify return:1
depth=0 CN=*.haproxy.org
verify return:1
HTTP/1.1 200 OK
date: Tue, 06 Oct 2026 00:21:38 GMT
server: Apache
last-modified: Mon, 05 Oct 2026 20:17:36 GMT
etag: "500b0b-14d4b-65d1d92ab7df0"
accept-ranges: bytes
content-length: 85323
content-type: text/html
age: 38
alt-svc: h2=":443"; ma=3600
alt-svc: h3=":443"; ma=3600
set-cookie: served=1:TLSv1.3+TCP:IPv4
connection: close

<html>
  <head>
    <title>HAProxy - The Reliable, High Perf. TCP/HTTP Load Balancer</title>

```

---

## Частина B. Розбір полів заголовка

Розбирається відповідь, отримана в завданні A.1.

**Загальна кількість полів заголовка у відповіді:**

| № | Поле заголовка | Значення | Призначення (власне формулювання) | Походження: сервер / проміжний вузол / не визначено | Обґрунтування |
|---|---|---|---|---|---|
| 1 | content-length | 0 | Показує довжину тіла відповіді в байтах | Проміжний вузол | Балансирувальник (HAProxy) сам видав редирект 301, у якого взагалі немає тіла |
| 2 | location | http://www.haproxy.org/ | Куди перенаправити клієнта далі | Проміжний вузол | Проксі сам робить редирект із домену без ввв на www.haproxy.org |
| 3 | alt-svc | h2=":443"; ma=3600 | Каже, що можна підключитися по HTTP/2 на 443 порт | Проміжний вузол | Це налаштування самого фронтенд-проксі для переходу на новіший протокол |
| 4 | alt-svc | h3=":443"; ma=3600 | Каже, що сервер підтримує HTTP/3 через QUIC на 443 порту | Проміжний вузол | Проксі на вході мережі повідомляє клієнту про підтримку протоколу QUIC |
| 5 | set-cookie | served=1:TCP:IPv4 | Технічна кука з інформацією про те, як пройшов сеанс | Проміжний вузол | Це суто службова мітка проксі для скриптів (видно параметри TCP та IPv4), а не кука від застосунку |
| 6 | connection | close | Попереджає, що з'єднання закриється після відповіді | Проміжний вузол | Вузол підтвердив закриття сесії, бо ми самі передали такий заголовок у запиті |

> Рядок наводять на кожне поле, яке реально надійшло. Зайві рядки вилучають, за потреби додають нові. Поле, походження якого встановити не вдалося, зазначають із позначкою «не визначено» та поясненням утруднення.

---

## Частина D. Висновки

Обсяг — 150–300 слів. Висновки спираються на власні спостереження.

**D.1.** Що з поведінки сервера виявилося неочевидним або несподіваним. Конкретно, з посиланням на рядок виводу.

У завданні A.2 Здивувало те, що коли відправив запит без поля Host, сервер не видав звичну помилку 400 Bad Request, а повернув рядок HTTP/1.1 403 Forbidden із текстом Request forbidden by administrative rules. То есть, на вході стоїть зворотний проксі (HAProxy), у якого прямо в конфігу забито правило безпеки: якщо хост не вказали, запит тупо блокується правилом доступу, а не просто падає. Ще було незвично, що в A.1 тіло відповіді було абсолютно пустим (content-length: 0), хоча зазвичай вебсервери пхають туди хоч якийсь мінімальний HTML із посиланням на редирект.

**D.2.** Яке з полів заголовка викликало найбільше утруднення при визначенні походження (частина B) та з якої причини.

Найбільше питань викликало поле set-cookie: served=1:TCP:IPv4. Зазвичай куки ставить бекенд або якась адмінка сайту для авторизації, тому спершу здається, що це відповідь кінцевого сервера. Але якщо подивитись на саме значення, то там записано 1:TCP:IPv4 це лог того, як він прийняв пакет. Вдобавок у відповіді A.1 не було заголовків Server та Date, то есть відповідь склав саме проміжний вузол.

**D.3.** Яке питання залишилося без відповіді після виконання роботи.

Чому в пробах A.3.1–A.3.3 кінцевий вебсервер Apache на наш запит по старому протоколу HTTP/1.0 все одно відповів рядком HTTP/1.1 404 Not Found і додав keep-alive, хоча для версії 1.0 це зовсім не стандартна поведінка за замовчуванням?

---

## Контрольні питання

**1.** У завданні A.1 сервер не надсилав відповіді, доки не було введено порожній рядок. Чим це зумовлено?

За стандартом HTTP заголовок запиту складається з рядків, кожен з яких закінчується на CRLF (\r\n). А щоб сервер зрозумів, що всі заголовки закінчилися і більше ніяких полів не буде, клієнт має відправити ще один перевід рядка, тобто порожній рядок (\r\n\r\n). Поки цього порожнього рядка немає, сервер просто чекає продовження запиту.

**2.** Порівняйте результати завдань A.1, A.2 та A.3.1–A.3.3 (таблиця Додатка Д). За яких значень поля `Host` і за якої версії протоколу сервер обслуговує запит, а за яких — ні? Яку задачу розв'язує поле `Host`? Відповідь має посилатися на конкретні рядки ваших виводів.

Нормально запит обслуговується лише в A.1 з рідним Host: haproxy.org на версії 1.1 сервер видає HTTP/1.1 301 Moved Permanently і шле на location: [http://www.haproxy.org/](http://www.haproxy.org/). Якщо поле прибрати взагалі (A.2), проксі блокує запит: HTTP/1.1 403 Forbidden (Request forbidden by administrative rules.). З чужим доменом ietf.org (A.3.1), неіснуючим opism-pr02.invalid (A.3.2) або без хоста на HTTP/1.0 (A.3.3) запит летить на дефолтний бекенд server: Apache, який віддає HTTP/1.1 404 Not Found. Поле Host якраз і потрібне для віртуального хостингу щоб вебсервер на одній IP знав, який саме сайт віддати.

**3.** Скільки відповідей надійшло у завданні A.4 і з якими кодами стану? Чи залежить відповідь сервера на порту 80 від запитаного шляху — і що це говорить про роль цього сервера? Якщо надійшла одна відповідь, знайдіть у ній поле заголовка, яке це пояснює, або зазначте, що такого поля немає. Якщо надійшло дві — що це означає для клієнтської програми, яка завантажує сторінку з великою кількістю вкладених ресурсів?

Прийшло 2 відповіді, обидві з кодом 301. Від шляху відповідь залежить: на /opism-pr02-12345 прийшов редирект на такий самий шлях, а на корінь / - на головну. Тобто роль сервера на 80 порту це просто маршрутизатор, який робить перенаправлення зі збереженням URI. А те, що прийшло дві відповіді в одному з'єднанні (keep-alive/pipelining), означає, що клієнту не треба на кожен файл відкривати новий TCP-сеанс, що відчутно прискорює завантаження сайту

**4.** Які поля заголовка програма `curl` додала самостійно (завдання A.5)? Ці поля не є обов'язковими — сервер відповів і без них у завданні A.1. З якою метою їх додано?

curl сам додав два рядки (позначені >):   User-Agent: curl/8.21.0 - повідомляє серверу назву та версію клієнта
Accept: */* - показує, що клієнт готовий прийняти відповідь будь-якого формату.

**5.** За якими ознаками у вашому виводі виявляється присутність проміжного вузла? Якщо таких ознак не виявлено, поясніть, що з цього випливає.

У A.1 проксі відповів сам, додавши свої технічні поля alt-svc і мітку сесії set-cookie: served=1:TCP:IPv4.
У A.2 проксі видав відмову 403 за своїми внутрішніми правилами (administrative rules).
У A.3.1–A.3.3 та A.6, коли проксі переслав запит далі, у відповіді раптом виліз кінцевий сервер server: Apache та поле age: 38.

**6.** Три рядки, про які не йшлося на лекції, наведено в **Додатку В**.

---

## Додаток В. Відповіді на питання 6

| № | Рядок виводу | Джерело (номер завдання) |
|---|---|---|
| 1 | alt-svc: h3=":443"; ma=3600 | А.1 |
| 2 | set-cookie: served=1:TCP:IPv4 | А.1 |
| 3 | etag: "500b0b-14d4b-65d1d92ab7df0" | А.6 |

---

## Додаток Д. Зведення результатів завдання A.3

**Вузол, з яким установлювалося з'єднання (у всіх пробах однаковий):haproxy.org (порт 80)**

| Проба | Значення поля `Host` | Версія | Код стану | Обсяг тіла відповіді | Збігається з A.1 (так / ні) |
|---|---|---|---|---|---|
| A.1 (вихідна) | haproxy.org | 1.1 | 301 | 0 байт | — |
| A.2 | поле відсутнє | 1.1 | 403 | 93 байт | ні |
| A.3.1 | ietf.org | 1.1 | 404 | 198 байт | ні |
| A.3.2 | `opism-pr02.invalid` | 1.1 | 404 | 198 байт | ні |
| A.3.3 | поле відсутнє | 1.0 | 404 | 198 байт | ні |

**Висновок за таблицею (2–4 речення):** що саме змінювалося у запиті від проби до проби і як на це реагував сервер.

Від проби до проби змінювався заголовок Host та версія самого протоколу. На правильний хост сервер одразу видає редирект 301, а без хоста на версії 1.1 проксі відсікає запит помилкою 403. Якщо підставити ліве чи несуществующе ім'я, або зробити запит по HTTP/1.0, балансирувальник перенаправляє запит на стандартний бекенд Apache, який видає 404 помилку.

---

## Декларування використання технологій штучного інтелекту

Для цієї роботи встановлено **рівень Р3 — ШІ як співвиконавець**.

Виводи команд частини A не можуть бути згенеровані та мають бути отримані внаслідок фактичного виконання команд.

**Чи використовувалися технології ШІ під час виконання роботи:** так

Якщо так, заповнюють таблицю. Якщо ні, таблицю вилучають.

| № | Інструмент (назва, версія) | Етап роботи | Дослівний текст запиту (промпту) | Як використано результат |
|---|---|---|---|---|
| 1 | gemini flash 3.8 | Частина B | Как установить openssl для powershell | Ввів команду winget install --id ShiningLight.OpenSSL.Light --accept-source-agreements --accept-package-agreements для встановлення Win64OpenSSL_Light-4_0_3.msi |
| 2 | gemini flash 3.8 | Частина А | Почему при передаче ряда через пайплайн в openssl s_client выдаёт 400 Bad request: Your browser sent an invalid request | З'ясовано, що утиліта отримує зайві символи через ключ -crlf при наявності rn у рядку; ключ було прибрано для коректного надсилання запиту |

**Підтвердження:** усі виводи команд, наведені в частині A, отримано внаслідок фактичного виконання команд на зазначеному індивідуальному домені.

---

*ОПІСМ (ОК-13) · Практична робота № 2 · бланк звіту*
