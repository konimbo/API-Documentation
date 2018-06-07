
# Konimbo Integration Doc
## מוצרים
### תוכן עניינים
0. [חזור לדף הראשי](https://github.com/heimanmorad/konimbo-api-docs)
1. [Background](#user-content-הקדמה)
2. [Getting Started](#user-content-getting-started)
3. [Integration](#user-content-integration)
4. [Trigger](#user-content-trigger)
4. [Webhook](#user-content-webhook)


### Background
The integration system will allow us to write simple integrations with third party systems - without needing a server-side developer.

The integration itself is written in the Admin-control-panel as described below.
For example: - an integration that will load items from a third party system into Konimbo. that sort of integration can be written in the integration system.

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
   
# Integration
Each integration is built from 3 sections:
* Config
* Initial Script
* Actions

#### Config
In this section we define the integration's variables.
this variables are initialized in the Trigger, and can be used throughout the intire Integration.

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

##### Special integration varaibles

there are a few special integration variables that you can add to your integration.
1. Trigger ID - used mainly for /amazons ENDPOINT. Initalize this field with the trigger's id and konimbox will automatically fetch the integrations variables behind the scenes. notice the exact key name you need to set:

```
[{
 "type": "...",
 "label": "...",
 "key": "...",
 "class": "..."
}, {
 "type": "password",
 "label": "Trigger ID",
 "key": "trigger_id",
 "class": "ltr"
},
```
2. Log emails - emails (seperated with , (comma)) that will get the integration's log file (incase we have set the integration to create one)

3. Log file format - the format we want the output of the log file to be presented in. (XML/JSON)

notice the exact key names of these variables:

```
...
, {
 "type": "password",
 "label": "log_emails (להפריד עם פסיקים):",
 "key": "log_emails",
 "class": "ltr"
}, {
 "type": "password",
 "label": "log file format (XML/JSON):",
 "key": "log_file_format",
 "class": "ltr"
}]
```


#### Initial Script
The first script section is usually used to make an initial HTTP request, that the entire integration will work based on the request's response.
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
it is used to define a data object that saves whatever data we want, to use later in the rest of the script (and integration)

in the example above we return a JSON object that contains a key named "order_id" that contains request["params"]["order"]["id"] value in it.
(this request data comes from a WEBHOOK but we will explain what that is later)

* request
```
function request(data, vars) {
 return {
   "url": "https://api.konimbo.co.il/v1/orders/" + data[0]["order_id"] + "?token=" + vars["konimbo_api_token"] ,
   "method": "get",
   "body": {},
   "expectedContentType": "string",
   "headers": {}
 }
}
```
This function MUST return a JSON object with the following keys: url, method, body, headers:
* url = String - the url that to make the HTTP request to.
* method = String - the HTTP method the request should be (GET/POST/PUT)
* body = JSON - a JSON object the represents the body of the request we want to send.
* headers = JSON - a JSON object the represents the headers of the request we want to send.
* expectedContentType = STRING\JSON\XML\QUERY - אם רוצים לשנות האופן בו מתקבלת התשובה מהשרת לסוג ספציפי ולא לקבל אוטומטי

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

# Actions
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
        "url": "https://api.konimbo.co.il/v1/items/",
        "method": "post",
        "internal_data": {
            "log_type": "items",
	         "authentication": {
			  "authentication_type": "basic_auth", 
			  "username": vars["priority_user_name"],
			  "password": vars["priority_password"],
   		  }
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
* internal_data = String - Optional - a JSON of data for internal use. NOTICE: the internal_data can only be used in the Actions scripts (and not in the Initial script).
there are 2 available keys currently supported:
1. "log_type": "items" - set the log type we want the integration to output. right only "items" is supported. 
2. "authentication": used to set an authentication to the wanted HTTP request. currently we support basic authentication. see example above.
3. "content_type": used to set the header "content_type"-'s value. its default is 'application/json'. u can set it to "blank" and it wont be sent at all.

* method = String - the HTTP method the request should be (GET/POST/PUT)
* body = JSON - a JSON object the represents the body of the request we want to send.
* headers = JSON - a JSON object the represents the headers of the request we want to send.

in order to put together the HTTP request we can use the data object and the vars object.


# Trigger

create a trigger:
1. press "טריגרים" link under "קונימבו - אדמין" in the Admin control panel
2. choose the integration to which we want to create a trigger for (will show only existing integrations within the store0
3. create the trigger
4. Make the trigger active (checkbox "פעיל")
5. Incase we dont want an extra layer of security - remove the checkbox "שכבת אבטחה נוספת".
6. under "הגבלת פעילות" increate to maximum value - so you want get blocked while testing your integrations
7. The integration variables we created in the Config part will be presented at the bottom of the page, and we can set data    in them.
8. To get cross domain add to vars variable with key named "Access-Control-Allow-Origin", and in the integrtion enter the domain as value.
9. To get the integrtion to run without delay (like orders) add to vars variable with key named "execute_now", and in the integrtion enter "YES" as value.

 
 after creating the Trigger, we will get unique url for it.
 in order to activate the integration we need to make an GET HTTP request to that url (you can use Postman to do so)
 
 
 # Webhook
 
 A Webhook is a way to let other system know that a specific event happend in Konimbo automatically.
 
 we have 2 events:
 1. order_created
 2. item_inventory_updateditem's inventory update
 
 A webhook is built from:
 1. callback_url - the url we want the webhook to activate in case the webhook event occured.
 2. event - the event on which the webhook is activated.
 
 * Creating a webhook
 ```
POST /{apiVersion}/webhooks HTTP/1.1
Host: api.konimbo.co.il
Content-Type: application/json

{
	"token": "{yourToken}",
	"webhook": {
		"callback_url": "{the url the webhook will make an HTTP POST request to}",
		"event": "order_created"
	}
}

```
Example:
 
Lets say we want to send each konimbo order that is created (in a specific store)
to a third party system.
these are the steps to do so:

1. Create a webhook and set the callback_url to the wanted Trigger's URL (that will activate a specific integration eventually)
 
```
POST https://api.konimbo.co.il/v1/webhooks

{
	"token": "1231123",
	"webhook": {
		"callback_url": "https://api.konimbo.co.il/hooks/OTU2MSDTlfMTQ5NzI2NDcwNQ",
		"event": "order_created"
	}
}

```

2. After an order will be created, the webhook will make an HTTP POST request to the trigger's url,
sending the order id of the order that was just created.

3. In the integration itself, we can get this order id in the following method:
   In the Initial Script section, on the data function:
   
   ```
   function data(request, vars) {
     return {
      "order_id": request["params"]["order"]["id"]
     };
   }

   ```
 4. We can use this order id to fetch the order's detail and then send it to wherever we want.
 5. see the complete example in digitalilelad store in the priority_orders integration
 
 * Updating a webhook
 ```
PUT /{apiVersion}/webhooks/{webhook_id} HTTP/1.1
Host: api.konimbo.co.il
Content-Type: application/json

{
	"token": "{yourToken}",
	"webhook": {
		"callback_url": "https://api.konimbo.co.il/hooks/Mzk5MDlfMTQ5NTk1ODY5OQ",
		"event": "order_created"
	}
}

```

 * Get a single webhook
 ```
GET /{apiVersion}/webhooks/{webhook_id}?token={yourToken} HTTP/1.1
Host: api.konimbo.co.il
```

 * Get all of the store's webhooks
 ```
GET /{apiVersion}/webhooks/?token={yourToken} HTTP/1.1
Host: api.konimbo.co.il
```

## Activating a webhook manually
In somecases we will want to activate a webhook manually from cart's page(and automatically through a specific event that occured on the store)

To do that, we need to create a form in cart page. The form's action should route to '/webhooks/trigger'
with the following fields:

1. id -  the id of the Cart - <input id="id" name="id" type="hidden" value="5315865">
2. event - the event of the webhook. this will be used to find the webhook to activate - <input id="event" name="event" type="hidden" value="order_created"> 
3. model - cart/order - the model we want to send it's payload with the webhook to the integration - <input id="model" name="model" type="hidden" value="cart">
4. trigger_data[WHATEVER_FIELD_WE_WANT] - used for extra data we want to send with the webhook to the integration that isnt necessarily connected to a model's payload. u can send this field multiple times. 

Example for such form
```
<form action="/webhooks/trigger" method="post"><div style="margin:0;padding:0;display:inline"><input name="authenticity_token" type="hidden" value="NinOp55uzgP1LoKm4TyDTHsjB23efsmtvQkPxd/7liM="></div>     <div class="input">
       <input id="id" name="id" type="hidden" value="5315865">
       <input id="event" name="event" type="hidden" value="order_created">
       <input id="model" name="model" type="hidden" value="cart">
       <input id="trigger_data_full_name" name="trigger_data[full_name]" type="hidden" value="זיו אריכא">
       <input id="trigger_data_email" name="trigger_data[email]" type="hidden" value="ziv@aricha.co.il">
       <input id="trigger_data_mobile_phone" name="trigger_data[mobile_phone]" type="hidden" value="0525594854">
       <input id="trigger_data_full_address" name="trigger_data[full_address]" type="hidden" value="weisburg netanya">
       <input id="trigger_data_note" name="trigger_data[note]" type="hidden" value="anat al tohli gvina">
       <input id="store_subdomain" name="store_subdomain" type="hidden" value="macbubi">
    </div>
     <div class="button">
     <input type="submit" value="בדוק זכאות למבצעים">
     </div>
</form>
```
