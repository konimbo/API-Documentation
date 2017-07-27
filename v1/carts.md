# Konimbo API Docs

## הזמנות
### תוכן עניינים
0. [חזור לדף הראשי](https://github.com/konimboltd/api-documentation)
1. [הקדמה](#user-content-הקדמה)
2. [EndPoints](#user-content-endpoints)
3. [פירוט השדות](#user-content-פירוט-השדות)
4. [עגלה](#user-content-עגלה)
6. [עריכת עגלה](#user-content-עריכת-עגלה)

### הקדמה

לכל הזמנה יש עגלת קניות
לכל עגלה יש:
* המוצרים שנרכשו
* השדרוגים שנרכשו
* ההנחות שהתקבלו
* פרטי תשלומים

### EndPoints

* [GET /{apiVersion}/carts/{id}](#user-content-עגלה)
* [PUT /{apiVersion}/carts/{id}](#user-content-עריכת-עגלה)

### פירוט השדות

שם השדה | הסבר | סכמה | דוגמא
:---|---:|:---|:---
id | מספר העגלה במערכת קונימבו | String | `1013564` `965487`
upgrades | פירוט השדרוגים | Object | {<br>price: `100.00`,<br>title: `שדרוג ל32 ג'יגה'`,<br>topic_title: `שדרוג כרטיס הזיכרון`<br>quantity: `1`<br>}
discounts | פירוט ההנחות | Object | 
payments | מצב ההזמנה | String | `שולם` `אשראי - מלא`
name | שם הלקוח | String | `ישראל ישראלי`
email | אימייל הלקוח | String | `mail@domain.com`
phone | טלפון הלקוח | String | `055-5555555`
address | כתובת הלקוח | String | `שם הרחוב 3, שם העיר`
note | הערת הלקוח | String | `בבקשה להתקשר לפני שמוציאים את המשלוח`
total_price | סך הכל לתשלום | String | `2680.5`
status_option_title | מצב ההזמנה | String | `אשראי - מלא`
created_at | תאריך יצירת ההזמנה | Time (ISO-8601) | `2017-01-01T17:10:37Z`
items | פירוט המוצרים | Object | {<br>id: `1305555`,<br>title: `טלפון סלולארי`,<br>code: `SKU#123`,<br>quantity: `2`,<br>price: `2780.50`<br>}

discounts | פירוט ההנחות | Object | {<br>price: `200`,<br>title: `הנחה לחברים`,<br>quantity: `1`<br>}
shipping | פרטי המשלוח | Object | {<br>id: `502135`,<br>title: `משלוח חינם בקנייה מעל 1000 שקל`,<br>price: `0`<br>}
payments | פרטי התשלום | Object | {<br>number_of_payments: `12`,<br>special_first_payment: `237.5`,<br>single_payment: `233.0`<br>}

### עגלה
#### תיאור
בעזרת השירות הזה, ניתן להחזיר מידע על עגלה ספציפית.


#### EndPoint
```
GET /{apiVersion}/carts/{id}?token={yourToken}
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
GET /v1/carts/5552478?token=794fc87483d6b5d519a98300e7bfcffa7a7aba008160c61cbf9b50fb59ed35cf HTTP/1.1
Host: api.konimbo.co.il

RESPONSE:
{
    "id": "5552478",
    "items": [
        {
            "id": 1404903,
            "title": "ckvckv",
            "code": "",
            "quantity": 1,
            "price": null
        }
    ],
    "upgrades": [],
    "shipping": {
        "id": 4520,
        "title": null,
        "price": null
    },
    "payments": {
        "number_of_payments": 1,
        "special_first_payment": null,
        "single_payment": null
    },
    "total_price": "",
    "discounts": [
        {
            "price": "15.0",
            "title": "ziv test4",
            "quantity": 1,
            "line_item_id": null,
            "item_id": null,
            "discount_value": "12.0",
            "discount_type": "₪",
            "lock_me": false,
            "type_name": null
        }
    ]
}
```

הקריאה הבאה תחזיר את שדות הid, items לעגלה 5552478

```
GET /v1/carts/5552478?token=794fc87483d6b5d519a98300e7bfcffa7a7aba008160c61cbf9b50fb59ed35cf&attributes=id,items
HTTP/1.1
Host: api.konimbo.co.il

RESPONSE:
{
    "id": "5552478",
    "items": [
        {
            "id": 1404903,
            "title": "ckvckv",
            "code": "",
            "quantity": 1,
            "price": null
        }
    ]
}
```



### עריכת עגלה
#### תיאור
בעזרת השירות הזה, ניתן לעדכן מידע לעגלה ספציפית.
העגלה חייבת להיות פתוחה לעריכה ולא נעולה על מנת שיהיה ניתן לעדכן אותה.
#### EndPoint
```
GET /v1/carts/5552478?token=794fc87483d6b5d519a98300e7bfcffa7a7aba008160c61cbf9b50fb59ed35cf&attributes=id,total_price HTTP/1.1
Host: api.konimbo.co.il

RESPONSE:
{
    "id": "5552478",
    "items": [
        {
            "id": 1404903,
            "title": "ckvckv",
            "code": "",
            "quantity": 1,
            "price": null
        }
    ]
}
```
#### שדות שניתן לעדכן לעגלה

##### discounts
ניתן להוסיף הנחה חדשה לעגלה

שם השדה | סכמה | חובה | הסבר
:---|:---|---:|---:
title | String | כותרת ההנחה  | כן
quantity | String | כמות ההנחה  | כן
discount_value | String |   | כן
discount_type | String |   | כן
unit_price | String | ערך ההנחה  | כן
type_name | String | לא | הערה על הסטטוס (טקסט חופשי)
line_item_id | Integer | לא | מזהה השורה בעגלה שאליה נרצה לשייך את ההנחה)

דוגמאות:

הוספת הנחה לעגלה

```
PUT /v1/cartsa/5552478?token=794fc87483d6b5d519a98300e7bfcffa7a7aba008160c61cbf9b50fb59ed35cf&attributes=id,total_price HTTP/1.1
Host: api.konimbo.co.il

BODY:
{
    "token": "794fc87483d6b5d519a98300e7bfcffa7a7aba008160c61cbf9b50fb59ed35cf",
    "cart": {
        "discounts": {
            "title": "הנחת מקורבים",
            "quantity": 1,
            "discount_value": 12,
            "unit_price": 15,
            "discount_type": "₪"
        }
    }
}
```

#### Responses
* 200 - The updated order
* 400 - Bad Request
* 401 - Unauthorized
* 404 - No results found
* 422 - Unprocessable Entity
* 500 - Internal error
