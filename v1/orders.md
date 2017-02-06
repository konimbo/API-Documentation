# Konimbo API Docs

## הזמנות
### תוכן עניינים
0. [חזור לדף הראשי](https://github.com/heimanmorad/konimbo-api-docs)
1. [הקדמה](#user-content-הקדמה)
2. [EndPoints](#user-content-endpoints)
3. [פירוט השדות](#user-content-פירוט-השדות)
4. [הזמנה](#user-content-הזמנה)
5. [רשימת הזמנות](#user-content-רשימת-הזמנות)
6. [עריכת הזמנה](#user-content-עריכת-הזמנה)

### הקדמה
לכל חנות יש הזמנות,

לכל הזמנה יש:
* פרטי הלקוח
* עגלת הקניות
 * המוצרים שנרכשו
 * השדרוגים שנרכשו
 * ההנחות שהתקבלו
* פרטי המשלוח
* פרטי התשלום
* סטטוטים להזמנה (נערכים על ידי בעל החנות)

### EndPoints

* [GET /{apiVersion}/orders](#user-content-רשימת-הזמנות)
* [GET /{apiVersion}/order/{id}](#user-content-הזמנה)
* [PUT /{apiVersion}/order/{id}](#user-content-עריכת-הזמנה)

### פירוט השדות

שם השדה | הסבר | סכמה | דוגמא
:---|---:|:---|:---
id | מספר ההזמנה במערכת קונימבו | String | `1013564` `965487`
payment_status | מצב ההזמנה | String | `שולם` `אשראי - מלא`
name | שם הלקוח | String | `ישראל ישראלי`
email | אימייל הלקוח | String | `mail@domain.com`
phone | טלפון הלקוח | String | `055-5555555`
address | כתובת הלקוח | String | `שם הרחוב 3, שם העיר`
note | הערת הלקוח | String | `בבקשה להתקשר לפני שמוציאים את המשלוח`
total_price | סך הכל לתשלום | String | `2680.5`
status_option_title | מצב ההזמנה | String | `אשראי - מלא`
created_at | תאריך יצירת ההזמנה | Time (ISO-8601) | `2017-01-01T17:10:37Z`
items | פירוט המוצרים | Object | {<br>id: `1305555`,<br>title: `טלפון סלולארי`,<br>code: `SKU#123`,<br>quantity: `2`,<br>price: `2780.50`<br>}
upgrades | פירוט השדרוגים | Object | {<br>price: `100.00`,<br>title: `שדרוג ל32 ג'יגה'`,<br>topic_title: `שדרוג כרטיס הזיכרון`<br>quantity: `1`<br>}
discounts | פירוט ההנחות | Object | {<br>price: `200`,<br>title: `הנחה לחברים`,<br>quantity: `1`<br>}
shipping | פרטי המשלוח | Object | {<br>id: `502135`,<br>title: `משלוח חינם בקנייה מעל 1000 שקל`,<br>price: `0`<br>}
payments | פרטי התשלום | Object | {<br>number_of_payments: `12`,<br>special_first_payment: `237.5`,<br>single_payment: `233.0`<br>}

### הזמנה
#### תיאור
בעזרת השירות הזה, ניתן להחזיר מידע על הזמנה ספציפית.

ניתן לבקש מהשרת רק את השדות שתרצו

#### EndPoint
```
GET /{apiVersion}/order/{id}?token={yourToken}
```
#### Parameters
##### בקשת שדות מסויימים
כדי שהשרת יידע איזה שדות להחזיר יש לציין בפרמטר `attributes` את השדות המבוקשים מופרדים בפסיקים. 

[לרשימת השדות](#user-content-פירוט-השדות)

לדוגמא: `attributes=id,payment_status,total_price`

במידה ולא נשלח פרמטר זה, יוחזרו כל השדות המורשים למשתמש זה.

#### Responses
* 200 - The requested order
* 401 - Unauthorized
* 404 - No results found
* 500 - Internal error

#### דוגמאות
הקריאה הבאה תחזיר את כל הפרמטרים המורשים של הזמנה זו

```
GET /v1/orders/123456789?token=e012a2944be024a85062c2bbd27b9f61284bf2439fd87992069b84b6a4ef93ca HTTP/1.1
Host: api.konimbo.co.il

RESPONSE:
{
  "id": "123456789",
  "payment_status": "אשראי - מלא",
  "name": "ישראל ישראלי",
  "email": "israel@israeli.co.il",
  "phone": "0555555555",
  "address": "רחוב 3 עיר",
  "note": "בבקשה לעטוף כמתנה",
  "items": [
    {
      "id": 655206,
      "title": "מכשיר סלולארי",
      "code": "SKU#123",
      "quantity": 1,
      "price": "799.0"
    }
  ],
  "upgrades": [],
  "discounts": [],
  "shipping": {
    "id": 1465,
    "title": "משלוח חינם  - דואר רשום (זמן משלוח של עד 7 ימי עסקים).",
    "price": "0.0"
  },
  "payments": {
    "number_of_payments": 1,
    "special_first_payment": "799.0",
    "single_payment": "799.0"
  },
  "total_price": "799.0",
  "status_option_title": "הזמנה חדשה",
  "created_at": "2016-11-21T12:51:22Z",
  "statuses": [
    {
      "id": 2119938,
      "status_option_title": "הזמנה חדשה",
      "username": "some api application",
      "comment": "דיברתי עם הלקוח, הוא חייב לעטוף כמתנה",
      "updated_at": "2016-11-21T12:52:38.000Z",
      "created_at": "2016-11-21T12:52:38.000Z"
    }
  ]
}
```

הקריאה הבאה תחזיר את שדות הid, total_price להזמנה 123456789

```
GET /v1/orders/123456789?token=e012a2944be024a85062c2bbd27b9f61284bf2439fd87992069b84b6a4ef93ca&attributes=id,total_price HTTP/1.1
Host: api.konimbo.co.il

RESPONSE:
{
  "id": "123456789",
  "total_price": "799.0"
}
```

### רשימת הזמנות
#### תיאור
בעזרת השירות הזה, ניתן להחזיר מידע על מספר הזמנות.

ניתן לסנן ולמיין את התוצאות, ובנוסף ניתן לבקש מהשרת שיחזיר רק את השדות שתרצו.

#### EndPoint
```
GET /{apiVersion}/orders?token={yourToken}
```

#### Parameters
את כל הפרמטרים ניתן לשלב יחד.
##### סינון

פרמטר | הסבר | דוגמא
:---|---:|:---
created_at_min | מחזיר את כל ההזמנות שנוצרו אחרי הזמן הנתון | `created_at_min=2017-01-01T09:00:00Z`
created_at_max | מחזיר את כל ההזמנות שנוצרו לפני הזמן הנתון | `created_at_max=2016-01-01T09:00:00Z`
payment_status | מחזיר רק הזמנות עם מצב התשלום הנתון | `payment_status=שולם`

##### מיון

פרמטר | הסבר | דוגמא
:---|---:|:---
sort_by | מיון סדר התוצאות לפי פרמטר מסוים | `sort_by=payment_status`, `sort_by=id`
sort_order | מיון סדר התוצאות בסדר עולה או יורד, פרמטר זה מתפקד רק ביחד עם `sort_by`| `sort_order=desc`, `sort_order=asce`

##### בקשת שדות מסויימים
כדי שהשרת יידע איזה שדות להחזיר יש לציין בפרמטר `attributes` את השדות המבוקשים מופרדים בפסיקים. 

[לרשימת השדות](#user-content-פירוט-השדות)

לדוגמא: `attributes=id,payment_status,total_price`

במידה ולא נשלח פרמטר זה, יוחזרו כל השדות המורשים למשתמש זה.
#### Responses

* 200 - Array of Orders
* 401 - Unauthorized
* 404 - No results found
* 500 - Internal error

#### דוגמאות

הקריאה הבאה תחזיר את הסכום סה"כ של כל אחת ההזמנות במצב שולם בשנת 2016

```
GET /v1/orders?
token=e012a2944be024a85062c2bbd27b9f61284bf2439fd87992069b84b6a4ef93ca&created_at_min=2016-01-01T00:00:00Z&created_at_max=2017-01-01T00:00:00Z&payment_status=שולם&attributes=total_price HTTP/1.1
Host: api.konimbo.co.il

RESPONSE:

[
  {
    "total_price": "1775.2"
  },
  {
    "total_price": "1493.0"
  },
  {
    "total_price": "1422.0"
  },
  ...
]
```

### עריכת הזמנה
#### תיאור
בעזרת השירות הזה, ניתן לעדכן מידע להזמנה ספציפית.
#### EndPoint
```
PUT /{apiVersion}/orders/{id} HTTP/1.1
Host: api.konimbo.co.il
Content-Type: application/json

{
  "token": {yourToken},
  "order": {
    "attribute1": "value",
    "attribute2": "value"
  }
}
```
#### שדות שניתן לעדכן להזמנה

##### Statuses
לכל הזמנה יש סטטוטים, דרך הAPI ניתן להוסיף סטטוסים חדשים בלבד.

בכל קריאה, ניתן להוסיף עד 5 סטטוסים להזמנה.

שם השדה | סכמה | חובה | הסבר
:---|:---|---:|---:
status_option_title | String | כן | כותרת הסטטוס.<br>אם הערך שניתן קיים בממשק תחת `הזמנות-מצבי הזמנה` אז מצב ההזמנה ישתנה גם לכותרת הסטטוס שניתנה. במידה והכותרת לא קיימת, מצב ההזמנה לא ישתנה.
username | String | לא|  שם המשתמש אשר יזם את הסטטוס (טקסט חופשי)
comment | String | לא | הערה על הסטטוס (טקסט חופשי)

דוגמאות:

נניח ואני רוצה לעדכן הזמנה מסוימת כחסרה במלאי, אני ארצה שגם מצב ההזמנה ישתנה.

ולכן, בממשק החנות ניגש ל`הזמנות` > `מצבי הזמנה`, ונוודא שאכן קיים הערך `חסר במלאי`

![StatusOptions](https://s3-eu-west-1.amazonaws.com/files.konimbo.co.il/api/docs/v1/orders/status_options.png)

ונשלח את הקוד הבא בתוכן הקריאה
```
"order": {
  "statuses": [{
    "status_option_title": "חסר במלאי",
    "username": "ERP Integration",
    "comment": "חלק מהמוצרים בהזמנה לא קיימים במלאי, הסחורה הוזמנה מהספק."
  }]
}
```

עוד דוגמא, נניח ואנחנו רוצים ליצור קשר עם לקוח בקשר להזמנה, והוא לא עונה, אני ארצה שיהיה מתועד שהתקשרנו ללקוח, אבל אין לי סיבה לשנות את מצב ההזמנה.

במצב כזה, נוודא שכותרת הסטטוס **לא** קיימת תחת `הזמנות > מצבי הזמנה`
ונשלח את הקוד הבא בתוכן הקריאה

```
"order": {
  "statuses": [{
    "status_option_title": "הלקוח לא עונה",
    "username": "צוות טלפונים - מיכל"
  }]
}
```


#### Responses
* 200 - The updated order
* 400 - Bad Request
* 401 - Unauthorized
* 404 - No results found
* 422 - Unprocessable Entity
* 500 - Internal error
