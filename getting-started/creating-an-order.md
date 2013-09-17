# Creating an Order

This tutorial helps you create your first order with Airbrite. If you need help, refer to our API documentation or email support@airbriteinc.com.

__Step 1: Make sure you've created a Product__

To get started, let's make double check that you have at least one product in Airbrite. To help, a sample test API key has been provided, so you can test right away. Make the following request:

    curl https://api.airbrite.io/v2/products \
        -u sk_test_a805be8b2add854f09976b3b5c0f5bd06c14617c:

curl uses the -u flag to pass basic auth credentials (adding a colon after your API key will prevent it from asking you for a password). 

From the response, we have a product called "My First Product" with the SKU "first-product". We'll be creating orders with "first-product".


__Step 2: Create your Order__

Next, we'll attempt to order 1 item of "first-product".

curl https://api.airbrite.io/v2/orders \
    -u sk_test_a805be8b2add854f09976b3b5c0f5bd06c14617c: \
    -d "line_items[0][sku]=first-product"\
    -d "line_items[0][quantity]=1"

If successful, you should receive a response similar to:

    {
        "meta": {
            "code": 200,
            "env": "test"
        },
        "data": {
            "user_id": "5237a347429acf0400000013",
            "_id": "5237a8e7a1b644040000000d",
            "line_items": [
                {
                    "sku": "first-product",
                    "quantity": "1",
                    "price": "995",
                    "description": "My First Product",
                    "name": null,
                    "weight": null,
                    "inventory": null,
                    "metadata": {}
                }
            ],
            "customer_id": null,
            "shipping_address": null,
            "shipping": {},
            "tax": {},
            "discount": {},
            "currency": "usd",
            "status": null,
            "description": null,
            "metadata": {},
            "created": 1379379432,
            "created_date": "2013-09-17T00:57:12.007Z",
            "updated": 1379379432,
            "updated_date": "2013-09-17T00:57:12.008Z",
            "order_number": 1001,
            "payments": [],
            "shipments": []
        }
    }


_Creating an order with a one-time payment_

