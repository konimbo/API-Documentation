
# Konimbo Integration Doc
## מוצרים
### תוכן עניינים
0. [חזור לדף הראשי](https://github.com/heimanmorad/konimbo-api-docs)
1. [הקדמה](#user-content-הקדמה)
2. [Getting Started](#user-content-getting-started)
3. [Integration](#user-content-Integration)

### הקדמה
The integration system will allow us to right simple integrations with 3rd party system without needed a server-side developer (hopefully)
The integration itself is written in the Admin-control-panel as described below.
For example - an integration that will load items from a 3rd party system into Konimbo. that sort of integration can be written in the integration system.

### Getting Started
In order to start developing an intergration, we need to enable some settings in the store.
1. On הגדרות מתקדמות לחנות, enable the "Api" and "אינטגרציות" checkboxes.
2. An Api user is needed. in order to create one, go to "משתמשי API" under "משתמשים - אדמין"
   choose a name of the user, make it "Active" (checkbox "פעיל"), and update the "הגבלת קריאות" and "הגבלת רשומות" for the
   maxmimum possible (so u wont get blocked for multiple requests)
   Add the users permissions according to the wanted ENDPOINTS this user needs to be permitted to.
3. Under "קונימבו - אדמין" a new link will be avialble - "אינטגרציות",
   Create a new integration, make it active (checkbox "פעיל"),
   Choose a name for the integration and save it
   
### Integration
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

