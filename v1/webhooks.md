# Konimbo Webhooks
## Webhooks
### תוכן עניינים
0. [חזור לדף הראשי](https://github.com/heimanmorad/konimbo-api-docs)

### <u><b>Background:</b></u>
A webhook is a way for an app or a system to provide other applications with real-time information. A webhook delivers data to other applications as it happens, meaning you get data immediately. Unlike typical APIs where you would need to poll for data very frequently in order to get it real-time. This makes webhooks much more efficient for both provider and consumer.

  <b>Konimbo provides webhooks for the following events:</b>
- order_created - When an order is created
- order_created_future - 10 minutes after an order is created
- order_after_payment - After an order is paid
- order_created_bulk - When a orders were created in bulk
- order_after_payment_api - After an order's payment is triggered by the API
- order_after_payment_paypal - After an order's payment is triggered by PayPal
- order_status_created - When an order status is created
- credit_guard_xml_created - When a credit guard XML response is received
- item_inventory_updated - When an item's inventory is updated
- customer_created - When a customer is created
- customer_logged_in - When a customer logs in to the store
- ticket_created - When a ticket is created

### <u><b>How to use:</b></u>
* <b>Creating a webhook:</b> <br>
 URL: https://api.konimbo.co.il/v1/webhooks <br>
 Method: POST <br>
 Content-Type: application/json <br>
 Request Body:
    ```json
    {
        "token": "your_token",
        "webhook":{
            "callback_url": "https://your-callback-url.com",
            "event": "order_created"
        }
    }
    ```
    ---


* <b>Updating a webhook:</b> <br>
 URL: https://api.konimbo.co.il/v1/webhooks/{webhook_id} <br>
 Method: PUT <br>
 Content-Type: application/json <br>
 Request Body:
    ```json
    {
        "token": "your_token",
        "webhook":{
            "callback_url": "https://your-callback-url.com",
            "event": "order_created"
        }
    }
    ```
    ---

* <b>Get all active webhooks:</b> <br>
    URL: https://api.konimbo.co.il/v1/webhooks?token={your_token} <br>
    Method: GET <br>
        ---

* <b>Get a specific webhook:</b> <br>
    URL: https://api.konimbo.co.il/v1/webhooks/{webhook_id}?token={your_token} <br>
    Method: GET <br>
        ---

### Examples of some of the webhook events:
* <b>order_created:</b> <br>
    ```json
        {
            "order": {
                "data_record_var": {},
                "customer_orders_size": null,
                "store_id": 1234,
                "phone": "0501234567",
                "status_option_title": "הזמנה חדשה",
                "email": "example@konimbo.co.il",
                "event": "order_created",
                "payment_status": "טלפוני",
                "id": 15872091,
                "name": "ישראל ישראלי",
                "cart": null,
                "affillate_field": "",
                "additional_inputs": null,
                "full_url": "https://shop-konimbo.co.il",
                "customer_id": 12462267,
                "newsletter": false
            }
        }
    ```

* <b>inventory_updated:</b> <br>
    ```json
        {
            "item": {
                "id": 5762508
            }
        }
    ```

* <b>ticket_created:</b> <br>
    ```json
       {
        "ticket": {
            "created_at": "2024-06-26T09:29:17Z",
            "updated_at": "2024-06-26T09:29:17Z",
            "coupon": null,
            "updated_at_date": "26/06/2024",
            "email_content": null,
            "store_id": 1234,
            "customer_id": 12462267,
            "customer_phone": "0501234567",
            "created_at_date": "26/06/2024",
            "ip_address": "11.111.1.111",
            "full_address": null,
            "username": "אורח",
            "newsletter": true,
            "created_at_time": "12:29",
            "code": null,
            "item_title": null,
            "customer_name": "ישראל ישראלי",
            "content": "תוכן הפניה יופיע כאן",
            "title": "נשלח מהאתר",
            "status": "חדשה",
            "customer_email": "example@konimbo.co.il",
            "id": 5171155,
            "vars": null,
            "updated_at_time": "12:29",
            "item_id": null
            }
        }
    ```