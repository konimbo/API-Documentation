
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

* data
```
function data(request, vars) {
 return {
   "order_id": request["params"]["order"]["id"]
 };
}
```
this function must return a JSON object.
it is used to define a data object that saves whatever data we want, to use later in the rest of the script.
in the example above we return a JSON object that contains a key name "order_id" that contains request["params"]["order"]["id"] value in it.
(this request data comes from a WEBHOOK but we will explain what that is later)

* request
```
function request(data, vars) {
 return {
   "url": "https://api.konimbo.co.il/v1/orders/" + data[0]["order_id"] + "?token=" + vars["konimbo_api_token"] ,
   "method": "get",
   "body": {},
   "headers": {}
 }
}
```
This function MUST return a JSON object with the following keys: url, method, body, headers:
* url = String - the url that to make the HTTP request to.
* method = String - the HTTP method the request should be (GET/POST/PUT)
* body = JSON - a JSON object the represents the body of the request we want to send.
* headers = JSON - a JSON object the represents the headers of the request we want to send.

* filter
```
function filter(data, vars) {
 return true;
}
```
this function must return TRUE or FALSE.
it is used to decide wheter or not we want to continue the integration, based on the response we got from the previous function's HTTP request.
for example: if the request was to get an order from Konimbo, and we want the integration to work only on orders that has  GETT shipping type, we will write on condition in the filter function that checks wheter or not the order has that shipping or not. if it wont - the filter function will return false, and the integration will stop running.


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

