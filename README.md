# m1925-tickets2-api
api.1925.me/t2 description

## Common values

`[token]` - Токен для підпису важливих подій (покласти місце в кошик, зняти з резерву, оплата, тощо)
Формуєьтся функцією, де $data це масив параметрів, що передаються в рядку URL

```php
function signRequest(array $data, string $key): string {
    // Сортуємо масив для стабільності
    ksort($data);
    // Перетворюємо масив у рядок формату key1=value1&key2=value2...
    $query = http_build_query($data, '', '&', PHP_QUERY_RFC3986);
    // Генеруємо HMAC-підпис за допомогою SHA256
    $signature = hash_hmac('sha256', $query, $key);
    return $signature;
}
```

Параметри що надаються клубом:

`pid` - Унікальний ідентифікатор партнера (varchar(6)) ('/^[A-Za-z0-9]+$/') 

`key` - Унікальний секретний ключ для підпису запитів (varchar(36)) ('/^[0-9a-f]{8}-[0-9a-f]{4}-[1-5][0-9a-f]{3}-[89ab][0-9a-f]{3}-[0-9a-f]{12}$/i')

Загальні:

`invoice` - Рахунок на оплату, ключ UUID (varchar(36)) ('/^[0-9a-f]{8}-[0-9a-f]{4}-[1-5][0-9a-f]{3}-[89ab][0-9a-f]{3}-[0-9a-f]{12}$/i')

`price` - Ціна квитка (float(15,2))

`event` - Подія (матч), ключ UUID (varchar(36)) ('/^[0-9a-f]{8}-[0-9a-f]{4}-[1-5][0-9a-f]{3}-[89ab][0-9a-f]{3}-[0-9a-f]{12}$/i')

`sector` - Сектор в події, ключ UUID (varchar(36)) ('/^[0-9a-f]{8}-[0-9a-f]{4}-[1-5][0-9a-f]{3}-[89ab][0-9a-f]{3}-[0-9a-f]{12}$/i')

`reserve` - Унікальний номер бронювання конкретного місця, до оплати, ключ UUID (varchar(36)) ('/^[0-9a-f]{8}-[0-9a-f]{4}-[1-5][0-9a-f]{3}-[89ab][0-9a-f]{3}-[0-9a-f]{12}$/i')

`row` - Ряд в секторі, tinyint(1)

`seat` - Місце в секторі, tinyint(1)

`sid` - Ідентифікатор сесії для події бронювання та оплати, уникальний в рамках одного кошика покупця, varchar(50) ('/^[A-Za-z0-9\-]+$/') 

`firstname` - Ім'я глядача, varchar(60) ('/^[A-Za-zА-Яа-яЁёІіЇїЄє\'\s\-]{2,60}$/u) 

`lastname` - Прізвище глядача, varchar(60) ('/^[A-Za-zА-Яа-яЁёІіЇїЄє\'\s\-]{2,60}$/u) 

`ticketid` - Ідентифікатор квитка, varchar(18), формат для баркоду Code128 або текст для QR, які скануються в нашій системі (Формат 4 латинські символи + 14 цифр)

`result` - Ознака успішності виконання запиту (0/1)

інші ключі що використовуються будуть описані додатково під конкретними запитами


## Request for information


## Запит що вертає список подій

**GET** : `https://api.1925.me/t2/g/getevents`

**Success Response**

**Code** : `200`

```json
{
    "result": 1,
    "events": [
        {
            "event": "be198bf5-4a86-11f0-96f1-00505630580c",
            "start": "2025-07-25 10:00:00",
            "stop": "2025-08-04 19:45:00",
            "date": "2025-08-04 18:00:00",
            "th": "Металіст 1925",
            "thlogo": "https://res1.1925.me/logo/teams-48abdf97518baf37513ecb3dbc75cd75.png",
            "tg": "Оболонь",
            "tglogo": "https://res1.1925.me/logo/teams-7a843b1aa1063bc56e6ea368077c5310.png",
            "stadium": "Центральний міський стадіон \"Полісся\" (Житомир)",
            "stadiumaddr": "м. Житомир, вул. Фещенка-Чопівського, 18",
            "location": "https://maps.app.goo.gl/bY8JoKy8DXA1Btri8",
            "basedate": 0
        }
    ]
}
```

`start` - час старту публічного продажу

`stop` - час коли продаж зупиняється

`date` - дата та час початку події

`th` - Домашня команда

`thlogo` - Логотип домашньої команди

`tg` - Гостьова команда

`tglogo` - Логотип гостьової команди

`stadium` - Назва стадіону

`stadiumaddr` - Адреса стадіону

`location` - Посилання на навігацію до стадіону

`basedate` - Ознака "Базова дата" (час мату не встановлений, дата матчу може змінитись)


## Запит що вертає список секторів в події

**GET** : `https://api.1925.me/t2/g/getsectors?event=53345751-cd8f-11ee-935f-00505630580c`

**Success Response**

**Code** : `200`

```json
{
    "result": 1,
    "sectors": [
        {
            "sector": "9d925921-6701-11f0-97a8-00505630580c",
            "name": 10,
            "bcolor": "#A5529A",
            "btcolor": "#ffffff",
            "ord": 10,
            "price": 100,
            "qty": 483,
            "remains": 473
        },
        {
            "sector": "15b9a5e8-6701-11f0-97a8-00505630580c",
            "name": "VIP-SILVER",
            "bcolor": "#D3D3D3",
            "btcolor": "#000000",
            "ord": 13,
            "price": 300,
            "qty": 128,
            "remains": 98
        }
    ]
}
```

`name` - назва сектору

`bcolor` - Колір фону сектора

`btcolor` - Колір тексту на фоні сектора

`ord` - порядок сотрування в списку

`price` - ціна квитка. Зверніть увагу: в день матчу ціна квитка автоматично змінюється в 00:00

`qty` - загальна кількість місць в секторі

`remains` - доступна для продажу кількість (на момент запиту)


## Запит що вертає список список всіх місць в секторі

**GET** : `https://api.1925.me/t2/g/getseats?sector=53345751-cd8f-11ee-935f-00505630580c`

**Success Response**

**Code** : `200`

```json
{
    "result": 1,
    "seats": [
        {
            "row": 9,
            "seat": 1,
            "available": 1,
            "take": 0
        },
        {
            "row": 9,
            "seat": 2,
            "available": 1,
            "take": 0
        },
        {
            "row": 9,
            "seat": 3,
            "available": 1,
            "take": 0
        }
  ]
}
```

`available` - ознака доступності місця (0 - недоступне взагалі/1)

`take` - ознака що місце зайняте (0 - вільне для бронбвання/1)


## Запит що вертає список список вільних місць в секторі

**GET** : `https://api.1925.me/t2/g/getfreeseats?sector=53345751-cd8f-11ee-935f-00505630580c`

**Success Response**

**Code** : `200`

```json
{
    "result": 1,
    "seats": [
        {
            "row": 9,
            "seat": 1
        },
        {
            "row": 9,
            "seat": 2
        },
        {
            "row": 9,
            "seat": 3
        }
    ]
}
```


## Запит що кладе місце в кошик

**GET** : `https://api.1925.me/t2/g/putseat?sector=53345751-cd8f-11ee-935f-00505630580c&row=1&seat=1&sid=02c62aa968d811f0994e00505630580c&token=[token]&pid=AB1234`

**Success Response**

**Code** : `200`

```json
{
    "result": 1,
    "uuid": "691495fa-6a6a-11f0-994e-00505630580c",
    "starttimer": "2025-07-27 00:49:31"
}
```
або помилка
```json
{
  "response":0,
  "error":"Місце вже заброньоване або продане"
}
```

`uuid` - унікальний ідентифікатор бронювання конкретного місця

`starttimer` - таймер відліку 15 хвилин для здійснення оплати. Через 15 хвилин бронь буде анульована. Якщо це друге та настпуні місця в цій сесії, таймер буде рахуватись від першого місця


## Запит що звілюнює місце з кошика

**GET** : `https://api.1925.me/tickets2/g/unputseat?reserve=53345751-cd8f-11ee-935f-00505630580c&token=[token]&pid=AB1234`

або

**GET** : `https://api.1925.me/tickets2/g/unputseat?sector=53345751-cd8f-11ee-935f-00505630580c&row=1&seat&=1&sid=02c62aa968d811f0994e00505630580c&token=[token]&pid=AB1234`

**Success Response**

**Code** : `200`

Відповідь по запиту з reserve
```json
{
    "result": 1,
    "text": "The transaction is completed, but it is not known whether the reserve existed"
}
```
Відповідь по запиту з даними сектора, ряду та місця
```json
{
    "result": 1
}
```


## Запит що встановлює ім'я та прізвище глядача для конкретного місця

**GET** : `https://api.1925.me/t2/g/setname?reserve=53345751-cd8f-11ee-935f-00505630580c&firstname=Ivan&lastname=Ivanov&token=[token]&pid=AB1234`

або

**GET** : `https://api.1925.me/t2/g/setname?sector=53345751-cd8f-11ee-935f-00505630580c&row=1&seat&=1&sid=02c62aa968d811f0994e00505630580c&firstname=Ivan&lastname=Ivanov&token=[token]&pid=AB1234`

**Success Response**

**Code** : `200`

Відповідь по запиту з reserve
```json
{
    "result": 1,
    "text": "The transaction is completed, but it is not known whether the reserve existed"
}
```
Відповідь по запиту з даними сектора, ряду та місця
```json
{
    "result": 1
}
```


## Запит що формує інвойс для оплати на стороні партнера

В одному інфвойсі можуть бути тільки місця з одного сектору
В цьому випадку: у відповідь на повідомлення про оплату будуть видані коди квитків

**GET** : `https://api.1925.me/t2/c/addinvoice?seactor=15b9a5e8-6701-11f0-97a8-00505630580c&tel=380671234567&email=email@email.com&sid=02c62aa968d811f0994e00505630580c&token=[token]&pid=AB1234`

`tel` - Телефон покупця (не обов'язкове поле) (/^[0-9]{8,15}$/)

`email` - Email покупця (обов'язкове)

**Success Response**

**Code** : `200`

```json
{
  "response":1,
  "code":"TKHB000009",
  "invoice":"15b9a5e8-6701-11f0-97a8-00505630580c",
  "total":1000
}
```
або помилка
```json
{
  "response":0,
  "error":"Якась помилка"
}
```

`code` - унікальний код інвойсу (публічний)

`total` - сума інвойсу


## Запит що формує інвойс для оплати на стороні клубу
В одному інфвойсі можуть бути тільки місця з одного сектору
В цьому випадку далі весь процес проходить на боці клубу (оплата, формування та відправка квитків). Клієнт для оплати перенаправляється на сервіс WayForPay

**GET** : `https://api.1925.me/t2/c/payinvoice?seactor=15b9a5e8-6701-11f0-97a8-00505630580c&tel=380671234567&email=email@email.com&sid=02c62aa968d811f0994e00505630580c&token=[token]&pid=AB1234`

`tel` - Телефон покупця (не обов'язкове поле) (/^[0-9]{8,15}$/)

`email` - Email покупця (обов'язкове)

**Success Response**

**Code** : `200`

```json
{
  "response":1,
  "payurl":"https://pay.1925.me/TKHB000009"
}
```
або помилка
```json
{
  "response":0,
  "error":"Якась помилка"
}
```

`payurl` - адреса на яку необхідно перенаправити покупця

## Запит що підтверджує факт успішної оплати інвойсу на стороні партнера

**GET** : `https://api.1925.me/t2/c/setpaid?invoice=53345751-cd8f-11ee-935f-00505630580c&token=[token]&pid=AB1234`

**Success Response**

**Code** : `200`

```json
{
    "result": 1,
    "tickets": [
        {
            "code": "TKDV00003001706307",
            "sector": "054f9127-cd96-11ee-935f-00505630580c",
            "row": 0,
            "seat": 0,
            "firstname": "",
            "lastname": ""
        },
        {
            "code": "TKDV00003002393390",
            "sector": "054f9127-cd96-11ee-935f-00505630580c",
            "row": 0,
            "seat": 0,
            "firstname": "",
            "lastname": ""
        }
    ]
}
```
або помилка
```json
{
  "response":0,
  "text":"Якась помилка"
}
```

`code` - унікальний код квитка (який друкується на квитку у вигляді Code128 або QR з текстом, для сканування в нашій системі)

