# Airbrite API

* [Introduction](#introduction)
* [Getting Started](#getting-started)
* [Authentication](#authentication)
* [Errors](#errors)

Products
* [Create a product](#create-product)
* [Retrieve a single product](#retrieve-product)
* [List all products](#list-all-products)
* [Update a product](#update-product)

Orders
* [Create an order](#create-order)
* [Retrieve a single order](#retrieve-order)
* [List all orders](#list-all-orders)
* [Update an order](#update-order)

Payments
* [Create a payment](#create-payment)
* [Retrieve a payment](#retrieve-payment)
* [List all payments](#list-all-payments)
* [Capture a payment](#capture-payment)
* [Refund a payment](#refund-payment)

Shipments
* [Create a shipment](#create-shipment)
* [Retrieve a shipment](#retrieve-shipment)
* [List all shipments](#list-all-shipments)

Taxes
* [Retrieve sales tax](#retrieve-sales-tax)

Account
* [Retrieve account details](#retrieve-account)

Events
* [Retrieve an event](#retrieve-event)
* [List all events](#list-all-events)
* [Types of events](#types-of-events)

## Introduction

The Airbrite API is an ecommerce logic and storage engine designed to be an essential tool for any developer.  
Our API is organized around REST and designed to have predictable, resource-oriented URLs, and to use HTTP response codes to indicate API errors. We support cross-origin resource sharing to allow you to interact securely with our API from a client-side web application (though you should remember that you should never expose your secret API key in any public website's client-side code). JSON will be returned in all responses from the API, including errors.

To make the Airbrite API as explorable as possible, accounts have test API keys as well as live API keys. These keys can be active at the same time. Data created with test credentials will never access live money.


## Getting Started

* The base endpoint URL is: `https://api.airbrite.io`
* Your access tokens can be found in "My Account"
* To process payments, you must connect to Stripe, which can be done in "My Account"
* There are two environments: live and test
* Currency amounts and costs are in cents


## Authentication

Tokens are used to authenticate your requests. There are two sets of tokens (public and secret) for each environment (live and test). Public keys are used on the client-side and will authenticate for only POST requests to Orders. Secret keys are used by your servers to make requests to the Airbrite API.

All endpoints require authentication. To authenticate with HTTP header, there are 3 methods you can your header, where {ACCESS_TOKEN} is "sk_test_xxx" or "sk_live_xxx":

* Authorization: {ACCESS_TOKEN}
* Authorization: Basic {BASE64_ENCODED_ACCESS_TOKEN}
* Authorization: Bearer {ACCESS_TOKEN}

Alternatively, you can authenticate via query string by simply adding `?access_token={ACCESS_TOKEN}` to any request.


## Organization

The API is organized by version of the API and resource (/{version}/{resource}). The current version of the API is v2. For example, to reach the products end point, you would access /v2/products. To access a resource with a particular ID the route is /{version}/{resource}/{id}, or /v2/products/52323272fa361e040c000001.

These resources have full CRUD support:

* Products (/v2/products)
* Orders
* Customers

These resources have read-only CRUD support:

* Events
* Logs

Because of the inherent complexity of ecommerce, the API also has a number of child resources (or subcollections). Child resources are accessible via the route "/{version}/{resource}/{resource_id}/{child_resource}" and are related to the parent, and a particular resource can be accessed at "../{child_resource}/{child_resource_id}". 

* Products
  + Events
* Orders
  + Payments (/v2/orders/{order_id}/payments)
  + Shipments
  + Events (/v2/orders/{order_id}/events - read-only)
* Customers
  + Events
  + Orders
* Events
  + Webhooks (/v2/events/{event_id}/webhooks)


## List of Routes

* Customers
  * /v2/customers
  * /v2/customers/{CUSTOMER_ID}

* Orders
  * /v2/orders
  * /v2/orders/{ORDER_ID}
  * /v2/orders/{ORDER_ID}/payments
  * /v2/orders/{ORDER_ID}/payments/{PAYMENT_ID}
  * /v2/orders/{ORDER_ID}/payments/{PAYMENT_ID}/capture
  * /v2/orders/{ORDER_ID}/payments/{PAYMENT_ID}/refund
  * /v2/orders/{ORDER_ID}/shipments
  * /v2/orders/{ORDER_ID}/shipments/{SHIPMENT_ID}

* Products
  * /v2/products
  * /v2/products/{PRODUCT_ID}

* Taxes
  * /v2/tax

* Events
  * /v2/events
  * /v2/events/{EVENT_ID}

* Logs
  * /v2/logs
  * /v2/logs/{LOG_ID}


## Errors

Airbrite uses conventional HTTP response codes to indicate success or failure of an API request. In general, codes in the 2xx range indicate success, codes in the 4xx range indicate an error that resulted from the provided information (e.g. a required parameter was missing, a charge failed, etc.), and codes in the 5xx range indicate an error with Airbrite's servers.

Our errors have the format: 

    {
        "meta": {
            "code": 400,
            "error_message": "User with email already exists.",
            "error_type": "client_error"
    },
        "data": "User with email already exists."
    }



## Resource Methods


## Products

### Create product

__Endpoint__

    POST https://api.airbrite.io/v2/products

__Arguments__

    Required: sku, price
    Optional: name


### Retrieve product

__Endpoint__

    GET https://api.airbrite.io/v2/products/{PRODUCT_ID}

__Arguments__

    Required: product_id
    Optional: none


### List all products

__Endpoint__

    GET https://api.airbrite.io/v2/products

__Arguments__

    Required: none
    Optional: since/from, until/to, sort, order


### Update product

__Endpoint__

    PUT https://api.airbrite.io/v2/products/{PRODUCT_ID}

__Arguments__

    Required: product_id
    Optional: none




## Orders

### Create Order

The order resource contains a metadata field for storing key-value pairs of extra data. Store as many of these key-value pairs as you wish.

Regarding line_items, your SKU should be already created.

Regarding payments, the order can be created with either:
1) no prior payment data
2) existing payment data (already charged)
3) a Stripe card token (new payment upon order creation)

If using Stripe, the payment card should be tokenized with `stripe.js` before the order is created. With Stripe, there are three ways to create payments for orders.

* At order creation, capture funds immediately

        {
            ...,
            "payments": [{
                "gateway": "stripe",
                "amount": 1337,
                "currency": "usd",
                "card_token": "tok_XXXXXXXXXXXXXX",
                "capture": "charge"
            }],
            ...
        }

* At order creation, place an authorization/hold on the card (will drop off after ~7 days unless captured)

        {
            ...,
            "payments": [{
                "gateway": "stripe",
                "amount": 1337,
                "currency": "usd",
                "card_token": "tok_XXXXXXXXXXXXXX",
                "capture": "auth"
            }],
            ...
        }

* At order creation, save payment card to be charged at a future date

        {
            ...,
            "payments": [{
                "gateway": "stripe",
                "amount": 1337,
                "currency": "usd",
                "card_token": "tok_XXXXXXXXXXXXXX",
                "capture": "hold"
            }],
            ...
        }


__Endpoint__

    POST https://api.airbrite.io/v2/orders

__Arguments__

    Required: customer (email address)
    Optional: line_items, shipping_address, shipping, tax, discount, payments, shipments, metadata

__Body__

    {
        "customer": {
            "name": "Jack Dagnels",
            "email": "jack@dagnels.ru"
        },
        "line_items": [
            {
                "sku": "12345ABC",
                "quantity": 1
            }
        ],
        "shipping_address": {
            "name": "Jack Dagnels",
            "phone": "4151234567",
            "line1": "123 Main St",
            "line2": "Unit 3A",
            "city": "San Francisco",
            "state": "CA",
            "zip": "94105",
            "country": "US",  // two-letter ISO code
        },
        "tax": {
            "cost": 89
        },
        "shipping": {
            "cost": 595
        },
        "discount": {
            "cost": 500,
            "code": "5DOLLARSOFF"
        },
        "payments": [{
            "gateway": "stripe",
            "amount": 1337,
            "currency": "usd",
            "card_token": "tok_XXXXXXXXXXXXXX"
            "capture": "charge"
        }],
        "shipments": [{
            "courier": "ups",
            "method": "ground",
            "tracking": "1ZXXXXXXXXXXXX",
            "line_items": [{
                "sku": "12345ABC",
                "quantity": 1
            }]
        }],
        "metadata": {
            "ref": "facebook"
        }
    }

### Retrieve order

__Endpoint__

    GET https://api.airbrite.io/v2/orders/{ORDER_ID}

__Arguments__

    Required: order_id
    Optional: none


### List all orders

__Endpoint__

    GET https://api.airbrite.io/v2/orders

__Arguments__

    Required: none
    Optional: since/from, until/to, sort, order


### Update order

To update an order (with the exception of customer, payments, and shipping), you can make a PUT request to the endpoint in this section. To create or update customer, payments, or shipments, please use the following endpoints:

* https://api.airbrite.io/v2/orders/{ORDER_ID}/customers
* https://api.airbrite.io/v2/orders/{ORDER_ID}/payments
* https://api.airbrite.io/v2/orders/{ORDER_ID}/shipments

__Endpoint__

    PUT https://api.airbrite.io/v2/orders/{ORDER_ID}

__Arguments__

    Required: order_id
    Optional: line_items, shipping_address, shipping, tax, discount, metadata


## Payments

### Create payment

__Endpoint__

    POST https://api.airbrite.io/v2/orders/{ORDER_ID}/payments

__Arguments__

    Required: order_id, gateway, currency, amount
    Optional: (card_token and capture OR charge_token), metadata


### Retrieve payment

__Endpoint__

    GET https://api.airbrite.io/v2/orders/{ORDER_ID}/payments/{PAYMENT_ID}

__Arguments__

    Required: order_id, payment_id
    Optional: none


### List all payments

__Endpoint__

    GET https://api.airbrite.io/v2/orders/{ORDER_ID}/payments

__Arguments__

    Required: order_id
    Optional: since/from, until/to, sort, order


### Capture payment

__Endpoint__

    PUT https://api.airbrite.io/v2/orders/{ORDER_ID}/payments/{PAYMENT_ID}/capture

__Arguments__

    Required: order_id, payment_id
    Optional: none



### Refund payment

__Endpoint__

    PUT https://api.airbrite.io/v2/orders/{ORDER_ID}/payments/{PAYMENT_ID}/refund

__Arguments__

    Required: order_id, payment_id
    Optional: amount


## Shipments

### Create shipment

__Endpoint__

    POST https://api.airbrite.io/v2/orders/{ORDER_ID}/shipments

__Arguments__

    Required: order_id, line_items
    Optional: courier, shipping_address, tracking, method, metadata


### Retrieve shipment

__Endpoint__

    GET https://api.airbrite.io/v2/orders/{ORDER_ID}/shipments/{SHIPMENT_ID}

__Arguments__

    Required: order_id, shipment_id
    Optional: none


### List all shipments

__Endpoint__

    GET https://api.airbrite.io/v2/orders/{ORDER_ID}/shipments

__Arguments__

    Required: order_id
    Optional: since/from, until/to, sort, order


## Taxes

### Retrieve sales tax

__Endpoint__

    GET https://api.airbrite.io/v2/tax

__Arguments__

    Required: zip, nexus_zips (comma separated)
    Optional: amount

__Body__

    {
        "meta": {
            "code": 200,
        },
        "data": {
            "amount": "1000",
            "tax_amount": "88",
            "tax_rate": "0.0875",
            "state_rate": "0.065",
            "county_rate": "0.01",
            "city_rate": "0",
            "special_rate": "0.0125"
        }
    }


## Account

### Retrieve account

__Endpoint__

    GET https://api.airbrite.io/v2/account

__Arguments__

    Required: none
    Optional: none


## Events

### Retrieve an event

__Endpoint__

    GET https://api.airbrite.io/v2/events/{EVENT_ID}

__Arguments__

    Required: event_id
    Optional: none


### List all events

__Endpoint__

    GET https://api.airbrite.io/v2/events

__Arguments__

    Required: none
    Optional: since/from, until/to, sort, order


### Types of events

This is a list of all the types of events we currently send. We may add more at any time, so you shouldn't rely on only these types existing in your code.

You'll notice that these events follow a pattern: resource.event. Our goal is to design a consistent system that makes things easier to anticipate and code against.

__Events__

* order.created
* order.updated
* order.payment.succeeded
* order.payment.authorized
* order.payment.captured
* order.payment.refunded
