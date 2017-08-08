
# Konimbo API Docs
## מוצרים
### תוכן עניינים
0. [חזור לדף הראשי](https://github.com/heimanmorad/konimbo-api-docs)
1. [הקדמה](#user-content-הקדמה)
2. [EndPoints](#user-content-endpoints)
3. [אמזון](#user-content-אמזון)

### הקדמה
בעזרת נקודת קצה זו ניתן לקרוא קבצים מ - Amazon s3
### EndPoints
* [POST /{apiVersion}/amazons](#user-content-קריאת-קובץ)
* [POST /{apiVersion}/amazons/write_file](#user-content-כתיבת-קובץ)



### קריאת-קובץ
#### תיאור
בעזרת השירות הזה, ניתן להחזיר מידע של קובץ XML או JSON
את המידע ניתן להחזיר בפורמט XML או JSON, השליטה על כך מתבצעת דרך משתמש ה API
ישנה מגבלה על גודל הקובץ - לא יעלה על 6 מגהבייט
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

### כתיבת-קובץ
#### תיאור
בעזרת שירות זה ניתן לכתוב קובץ לאמזון
ניתן לשלוט בשם הקובץ, סוג הקובץ, הבאקט שאליו נרצה לכתוב ועוד
ידה ולא ישלח שם הבאקט שאליו נרצה לכתוב, הקובץ יכתב לבקאט הדיפולטי של קונימבו konimbo-integration-files-bucket
השירות מחזיר לינק לקובץ

#### EndPoint
```
POST /{apiVersion}/amazons/write_file
Host: api.konimbo.co.il
```

#### Parameters
הפרמטרים הנדרשים הם פרטי התחברות ל amazon s3 ואת ה konimbo api token
1. token = "konimbo_api_token"
2. data - אובייקט שיכיל פרמטרים של קוניפוגרציה
21. file_name - שם הקובץ שנרצה, לדוגמא myfile.json
22. file_type - על פי שדה זה מבצעים המרה לתוכן שאותו נרצה לכתוב, האופציות הן: json/xml/string/query
23. store_unique_token - מזהה חד חד ערכי שנשלח פר חנות, שימנע מחנויות שונות לגשת לקבצים לא שלהם
24. credentials - אובייקט שיכיל את פרטי הבאקט שאליו נרצה לכתוב את הקובץ כולל פרטי גישה
24.1 - region - הריג'ן של הבאקט
2.4.2 - access_key_id - אקסס קי במידה ויש
2.4.3 - secret_access_key - סיקרט אקסס קי במידה ויש
2.4.5 - bucket_name -  konimbo-integration-files-bucket שם הבאקט שאליו נרצה לכתוב, אם לא ישלח יכתב לבאקט הדיפולטי
3. content - תוכן הקובץ המבוקש. יכול להישלח כסטרינג או כאובייקט

#### Responses
* 200 - Link to the requested file
* 401 - Unauthorized
* 404 - No results found
* 500 - Internal error

#### דוגמאות
הקריאה הבאה תכתוב קובץ מסוג ג'ייסון עם תוכן של מוצר מסויים

```
POST /{apiVersion}/amazons/write_file HTTP/1.1
Host: api.konimbo.co.il
Content-Type: application/json

{
	"token": "794fc87483d6b5d519a98300e7bfcffa7a7aba008160c61cbf9b50fb59ed35cf",
	"data": {
	 "file_name": "zivfile3.json",
	 "file_type": "json",
	 "store_unique_token": "abcd",
	 "credentials": {
		  "region": "us-east-1",
		  "access_key_id": null,
		  "secret_access_key": null,
		  "bucket_name": "konimbo-integration-files"
		},
	 "content": {
    "id": "1358279",
    "created_at": "2017-07-02T12:36:56Z",
    "updated_at": "2017-07-31T11:10:46Z",
    "title": "34234undefinedziv",
    "store_category_title": "סדרות על הטיח",
    "price": "26.7",
    "origin_price": "",
    "code": "50025101",
    "second_code": "",
    "desc": "קופסת הסתעפות בדרגת אטימות IP55. מיוצרות מפוליקרבונט, חומר כבה מאליו, העמיד בבדיקת תיל להט בטמפרטורה של 850?. אפשרות לכניסת צינור תקשורת.",
    "visible": "true",
    "model_title": "",
    "brand": "",
    "warranty": "",
    "position": "999",
    "quantity": "",
    "discount_prices": {
        "field_1828": "",
        "field_1829": "",
        "field_1210": "",
        "field_1211": ""
    },
    "related_items": [],
    "inventory": [],
    "images": [
        {
            "id": 2079264,
            "url": "https://s3-eu-west-1.amazonaws.com/devkonimboimages/system/photos/2079264/original/aa0448e22384d5e5a2b79f9b366419f5.jpg",
            "position": 1,
            "alt": "image1_alt"
        }
    ],
    "filters": [
        {
            "id": 1295220,
            "group_title": "סדרה",
            "group_value_title": "TITAN"
        },
        {
            "id": 1295221,
            "group_title": "אופן התקנה",
            "group_value_title": "על הטיח"
        },
        {
            "id": 1295222,
            "group_title": "צבע בסיס",
            "group_value_title": "שחור"
        },
        {
            "id": 1295223,
            "group_title": "מתח",
            "group_value_title": "220"
        },
        {
            "id": 1295224,
            "group_title": "יצרן",
            "group_value_title": "NISKO"
        }
    ]
}
	}
}
```

