# Konimbo API Docs

## הזמנות
### תוכן עניינים
0. [חזור לדף הראשי](https://github.com/konimboltd/api-documentation)
1. [הקדמה](#user-content-הקדמה)
2. [EndPoints](#user-content-endpoints)
3. [פירוט השדות](#user-content-פירוט-השדות)
4. [לקוח](#user-content-לקוח)
5. [עריכת לקוח](#user-content-עריכת-לקוח)

### הקדמה
לכל חנות יש לקוחות,

לכל לקוח יש:
* פרטי הלקוח
* שדות נוספים

### EndPoints

* [GET /{apiVersion}/customers/{id}?token={yourToken}](#user-content-לקוח-ספציפי)
* [POST /{apiVersion}/customers/](#user-content-יצירת-לקוח)
* [PUT /{apiVersion}/customers/{id}](#user-content-עריכת-לקוח)

### פירוט השדות

שם השדה | הסבר | סכמה | דוגמא
:---|---:|:---|:---
id | מספר הלקוח במערכת קונימבו | String | `1013564` `965487`
full_name | שם הלקוח | String | `ישראל ישראלי`
email | אימייל הלקוח | String | `mail@domain.com`
mobile_phone | טלפון הלקוח | String | `055-5555555`
full_address | כתובת הלקוח | String | `שם הרחוב 3, שם העיר`
created_at | תאריך יצירת הלקוח | Time (ISO-8601) | `2017-01-01T17:10:37Z`
updated_at | תאריך יצירת הלקוח | Time (ISO-8601) | `2017-01-01T17:10:37Z`
newsletter | הרשמה לדיוור | Boolean  | <br>true<br>
discount_title | מועדון לקוחות | String | `פלטינום`
data_record_json | שדות נוספים ללקוח | Json |  { "email":"John1@gmail.com", "Extra_name": "David", "Extra_number": "897561" }


### לקוח
#### תיאור
בעזרת השירות הזה, ניתן להחזיר מידע על לקוח ספציפי.

ניתן לבקש מהשרת רק את השדות שתרצו

#### EndPoint
```
GET /{apiVersion}/customers/{id}?token={yourToken}
```
#### Parameters
##### בקשת שדות מסויימים
כדי שהשרת יידע איזה שדות להחזיר יש לציין בפרמטר `attributes` את השדות המבוקשים מופרדים בפסיקים. 

[לרשימת השדות](#user-content-פירוט-השדות)

לדוגמא: `attributes=id,full_name,email`

במידה ולא נשלח פרמטר זה, יוחזרו כל השדות המורשים למשתמש זה.

#### Responses
* 200 - The requested order
* 401 - Unauthorized
* 404 - No results found
* 500 - Internal error

#### דוגמאות
הקריאה הבאה תחזיר את כל הפרמטרים המורשים של הזמנה זו

```
GET /{apiVersion}/customers/0?token=t48jt4i3jg03j4ig0jg43943949304940923f4332322&email=test@konimbo.co.il
Host: api.konimbo.co.il

RESPONSE:
{
    "id": "1783638",
    "created_at": "2018-01-28T10:42:43Z",
    "updated_at": "2018-02-01T13:14:10Z",
    "email": "itamar@konimbo.co.il",
    "newsletter": "true",
    "full_address": "איתמר בדיקה",
    "mobile_phone": "098866982",
    "full_name": "איתמר בדיקה",
    "discount_title": "מועדון דילרים",
    "data_record_json": ""
}
```

### עריכת לקוח
#### תיאור
בעזרת השירות הזה, ניתן לעדכן מידע ללקוח ספציפי
#### EndPoint
```
PUT /{apiVersion}/customers/0 HTTP/1.1
Host: api.konimbo.co.il
Content-Type: application/json

{
  "token": {yourToken},
  "customer": {
    "attribute1": "value",
    "attribute2": "value"
  }
}
```
#### שדות שניתן לעדכן להזמנה

שם השדה | סכמה | חובה | הסבר
:---|:---|---:|---:
email | String | כן | מייל הלקוח
newsletter | Boolean | לא|  האם הלקוח אישר שליחת דיוור)
full_address | String | לא | כתובת מלאה של הלקוח)
mobile_phone | String | לא | מספר הנייד של הלקוח)
full_name | String | לא | שם מלא של הלקוח)
discount_title | String | לא | שם מדוייק של מועדון הלקוחות אותו יש לשייך ללקוח)
data_record_json | Json | לא | שדות נוספים שהוגדרו במערת מראש)

דוגמאות:

אם נרצה להקים לקוח חדש נוכל לפנות בקריאת POST
הקריאה תקין את הלקוח עם השדות הנוספים
נשלח את הקוד הבא בתוכן הקריאה
```
{
  "token": "ca877786a58453544444405964e01cea91bcae210d912c1d40bf0",
   "customer": {
    "email": "test@konimbo.co.il",
    "newsletter": "true",
    "full_address": "קונימבו בדיקה",
    "mobile_phone": "098866982",
    "full_name": "שם מגניב",
    "data_record_json": {
        "number":"20103",
        "longnumber": "514103910",
        "extra_name": "אירית",
        "extra_mail": "test2@konimbo.co.il"
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
