
# Konimbo Integration Doc
## מוצרים
### תוכן עניינים
0. [חזור לדף הראשי](https://github.com/heimanmorad/konimbo-api-docs)
1. [Background](#user-content-הקדמה)
2. [Getting Started](#user-content-getting-started)
3. [Integration](#user-content-Integration)

### Background
The integration system will allow us to wright simple integrations with third party system without needing a server-side developer (hopefully)
The integration itself is written in the Admin-control-panel as described below.
For example - an integration that will load items from a third party system into Konimbo. that sort of integration can be written in the integration system.

### Getting Started
In order to start developing an intergration, we need to enable some settings in the store:

1. On הגדרות מתקדמות לחנות, enable the "Api" and "אינטגרציות" checkboxes.
2. An Api user is needed. in order to create one, go to "משתמשי API" under "משתמשים - אדמין"
   choose a name of the user, make it "Active" (checkbox "פעיל"), and update the "הגבלת קריאות" and "הגבלת רשומות" for the
   maxmimum possible (so u wont get blocked for multiple requests)
   Add the users permissions according to the wanted ENDPOINTS this user needs to be permitted to.
3. Under "קונימבו - אדמין" a new link will be avialble - "אינטגרציות",
   Create a new integration, make it active (checkbox "פעיל"),
   Choose a name for the integration and save it
   
### Integration
Each integration is built from 3 sections:
* Config
* Initial Script
* Actions

#### Config

#### Initial Script
The first script section is usually used to make an initial HTTP request, that the entire integration will be work based on the request's response.
For example: an integration that is ment to load items into konimbo system. the first script will include the HTTP  request to get the array items to load, and the rest of the integration will work on that array to actually load it.

The script itself is built from 5 different functions that MUST be written if we dont use them:

```
function data(response, vars, current_obj) {
    var username = vars["priority_user_name"];
    var password = vars["priority_password"];

    var items_to_send = [];
    current_obj["items"].forEach(function(entry) {
        items_to_send.push({
            "PARTNAME": entry["code"],
            "QUANT": entry["quantity"],
            "TOTPRICE": parseFloat(entry["price"]),
            // "DUEDATE":  "2017-06-06"
        });
    });

    return {
        "LOADCODE": "001",
        "CUSTDES": current_obj["name"],
        "FULL_ADDRESS": current_obj["address"],
        "PHONE1": current_obj["phone"],
        "EMAIL": current_obj["email"],
        "ID": current_obj["id"],
        "DOCDATE": current_obj["created_at"],
        "PRIT_DOCLINE_SUBFORM": items_to_send,
        "PAYMENTNUM": current_obj["payments"]["number_of_payments"],
        "QPRICE": parseFloat(current_obj["total_price"])
    }
}
```

#### Actions


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

