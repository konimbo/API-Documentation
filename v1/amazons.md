
# Konimbo API Docs
## מוצרים
### תוכן עניינים
0. [חזור לדף הראשי](https://github.com/heimanmorad/konimbo-api-docs)
1. [הקדמה](#user-content-הקדמה)
2. [EndPoints](#user-content-endpoints)
3. [פירוט השדות](#user-content-פירוט-השדות)
4. [אמזון](#user-content-אמזון)
5. [מוצרים](#user-content-מוצרים)
6. [יצירת מוצר](#user-content-יצירת-מוצר)
7. [עריכת מוצר](#user-content-עריכת-מוצר)
8. [מחיקת מוצר](#user-content-מחיקת-מוצר)

### הקדמה
בעזרת נקודת קצה זו ניתן לקרוא קבצים מ - Amazon s3
### EndPoints
* [POST /{apiVersion}/amazons](#user-content-קריאת-קובץ)

### פירוט השדות
#### שדות פשוטים:
שם השדה | סכמה | ניתן לעדכון | הסבר | דוגמא
:--|:---|---:|---:|---:
id                     | Integer         | לא | מספר המוצר במערכת קונימבו | `1203546`
title                  | String          | כן | כותרת המוצר | `טלפון סלולארי`
store_category_title   | String          | כן | הקטגוריה שאליה המוצר שייך | `טלפונים`
price                  | Numeric         | כן | מחיר המוצר | `55.50`, `999`
origin_price           | Numeric         | כן | מחיר מקורי (לפני מבצע) | `1499`, `1999.99`
code                   | String          | כן | מק"ט המוצר | `SKU#123`, `49841687468`
second_code            | String          | כן | מקט משני (לשימוש פנימי) | `6418351816`
desc                   | String          | כן | תיאור קצר על המוצר | `טלפון מתקדם מיצרן בינלאומי, מספר אחד בתחום הטלפונים`
visible                | Boolean         | כן | האם המוצר מופיע בחנות או מוסתר | `true`, `false`
model_title            | String          | כן | שם הספק | `MyProvider`
created_at             | Time (ISO-8601) | לא | זמן יצירת המוצר | `2016-01-01T09:00:00Z`
updated_at             | Time (ISO-8601) | לא | זמן העדכון האחרון של המוצר | `2017-01-01T09:00:00Z`
related_items          | Array           | כן | רשימת הids של המוצרים הנלווים | `[1253456, 654879]`

### אמזון
#### תיאור
בעזרת השירות הזה, ניתן להחזיר מידע של קובץ XML או JSON
את המידע ניתן להחזיר בפורמט XML או JSON, השליטה על כך מתבצעת דרך משתמש ה API
#### EndPoint
```
POST /{apiVersion}/amazons/
Host: api.konimbo.co.il
```

#### Parameters
הפרמטרים הנדרשים הם פרטי התחברות ל amazon s3 ואת ה konimbo api token
1. token = "konimbo_api_token"
2. Amazon file name - שם הקובץ לקיריאה
3. Amazon bucket name - שם הבאקט שבו נמצא הקובץ
4. Amazon region - הריג'ן שבו הבאקט נמצא
5. Amazon file format - פורמט הקובץ - XML / JSON
6. Access key id - amazon access key 
7. Secret access key - amazon secret access key
8. trigger id - מזהה הטריגר במערכת קונימבו - אופציונאלי


#### Responses
* 200 - The requested file's content
* 401 - Unauthorized
* 404 - No results found
* 500 - Internal error

#### דוגמאות
הקריאה הבאה תחזיר את תוכן הקובץ (XML\JSON)
פורמט קבלת הקובץ ניתן לשליטה ע"י עריכת משתמש ה API.

```
POST /{apiVersion}/amazons HTTP/1.1
Host: api.konimbo.co.il
Content-Type: application/json

{
  "token": "{yourToken}",
  "amazon": {
    "region": "value",
    "access_key_id": "value",
    "secret_access_key": "value",
    "bucket_name": "value",
    "file_name": "value",
    "file_format": "value"
  }
}
```

