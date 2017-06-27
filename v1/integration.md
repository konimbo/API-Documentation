
# Konimbo Integration Doc
## מוצרים
### תוכן עניינים
0. [חזור לדף הראשי](https://github.com/heimanmorad/konimbo-api-docs)
1. [Background](#user-content-הקדמה)
2. [Getting Started](#user-content-getting-started)
3. [Integration](#user-content-Integration)
4. [Trigger](#user-content-Trigger)

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
In this section we define the integration's variables.
Format: Array of JSONs. each JSON represents a single variable.

```
[{
 "type": "password",
 "label": "Konimbo API Token",
 "key": "konimbo_api_token",
 "class": "ltr"
}, {
 "type": "password",
 "label": "username",
 "key": "username",
 "class": "ltr"
}
```
each varaible's JSON needs to contain the following keys:
* "type" - "password" will cause the data be "encrypted" with * to encrypt sensitive data.
* "label" - thats the title of the field that will be seen at the Trigger creation/edit page in the Admin control panel
* "key" - the name of the field. we can reach the data by using this name we set here (example: vars["konimbo_api_token"]
* "class" - irrelevnt, always initialize with "ltr"

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


* array

```
function array(response, vars) {
 return response["body"]["items"]["item"];
}
```
This function needs to return the object (or array of objects) we want the integration to be executed on.

For example: if we want to load items into Konimbo system, the inital script will make an HTTP request to a third party system that will return a response containing the items, and in this function we will return the actual items array out of the response as seen in the example above.

* hasAnotherPage

need to complete

```
function hasAnotherPage(response, vars) {
 // there is no use with this function at this integration
 return false;
}
```

#### Actions
The actions are the "logic" behind the integration.

each action is built from a script.

the goal of the actions is to do manipulations on the data we recieved from the first script,
and write it to a third party system (or our own system)

Similar to the first script, each action's MUST contain 3 functions:

* data - define the data object

```
function data(response, vars, current_obj) {

    var dummy_var = "Hello World";
    
    return {
        "store_category_title": current_obj["store_category_title"],
        "spec": current_obj["spec"],
        "title": current_obj["title"],
        "filter": current_obj["filter"],
        "code": current_obj["code"],
        "price": current_obj["price"],
        "desc": current_obj["desc"],
        "images": current_obj["images"],
        "visible": current_obj["visible"],
        "quantity": current_obj["quantity"],
        "related_items": current_obj["related_items"]
    }
}
```
This function used to define a data object that saves whatever data we want, to use later in the rest of the script.

the data function MUST return a json object. (an empty object is acceptable)

The data functions recieves 3 parameters: response, vars, current_obj

vars = contains the integration's variables - things like usernames and passwords
current_obj = contains the object we want the "work" on in this Action. (wheter it is part of an array of objects or not)

For example: If the integration's goal is to read an item from another system and write it to Konimbo, we need to make sure that current_obj will contain the data of the wanted order.


the returned object can later be access as follows:
data[ACTION_NUMBER]["FIELD_NAME"]
ACTION_NUMBER = represented the position of the Action. if we are at the first Action the position will be 1, and so on.

```
data[1]["images"]
```

you can write Javascript code to make manipulations on the data (Loops, conversions, etc.)


* filter -  based on what we know, do we want to continue the integration?

```
function filter(data, vars) {
    return data[1]["price"] > 0;
}

```

The filter function is ment to decide wether or not we want to stop, based on the data object.

the function MUST return true or false


* request - make an HTTP request to whoever you want.

```
function request(data, vars) {
    return {
        "url": "http://localhost:3002/v1/items/",
        "method": "post",
        "internal_data": {
            "log_type": "items"
        },
        "body": {
            "token": vars["konimbo_api_token"],
            "item": {
                "title": data[1]["title"],
                "store_category_title": data[1]["store_category_title"],
                "spec": data[1]["spec"],
                "filters": data[1]["filter"],
                "code": data[1]["code"],
                "price": data[1]["price"],
                "desc": data[1]["desc"],
                "images": data[1]["images"],
                "visible": data[1]["visible"],
                "quantity": data[1]["quantity"],
                "related_items": data[1]["related_items"]
            }
        }
    }
  }
```

The request function is ment to make an HTTP request to an API.

This function MUST return a JSON object with the following keys: url, method, body, headers:
* url = String - the url that to make the HTTP request to.
* method = String - the HTTP method the request should be (GET/POST/PUT)
* body = JSON - a JSON object the represents the body of the request we want to send.
* headers = JSON - a JSON object the represents the headers of the request we want to send.

in order to put together the HTTP request we can use the data object and the vars object.


### Trigger

create a trigger:
1. press "טריגרים" link under "קונימבו - אדמין" in the Admin control panel
2. choose the integration to which we want to create a trigger for (will show only existing integrations within the store0
3. create the trigger
4. Make the trigger active (checkbox "פעיל")
5. Incase we dont want an extra layer of security - remove the checkbox "שכבת אבטחה נוספת".
6. under "הגבלת פעילות" increate to maximum value - so you want get blocked while testing your integrations
7. The integration variables we created in the Config part will be presented at the bottom of the page, and we can set data    in them.
 
 after creating the Trigger, we will get unique url for it.
 in order to activate the integration we need to make an GET HTTP request to that url (you can use Postman to do so)
 
