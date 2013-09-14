# Airbrite API

* [Introduction](#introduction)
* [Getting Started](#getting-started)
* [Authentication](#authentication)
* [Organization](#organization)
* [Response Format](#response-format)
* [Errors](#errors)
* [Resource Methods](#resource-methods)
    + [Products](#products)
        + [Create a product](#create-product)
        + [Retrieve a single product](#retrieve-product)
        + [List all products](#list-all-products)
        + [Update a product](#update-product)
    + [Orders](#orders)
        + [Create an order](#create-order)
        + [Retrieve a single order](#retrieve-order)
        + [List all orders](#list-all-orders)
        + [Update an order](#update-order)
    + [Payments](#payments)
        + [Create a payment](#create-payment)
        + [Retrieve a payment](#retrieve-payment)
        + [List all payments](#list-all-payments)
        + [Capture a payment](#capture-payment)
        + [Refund a payment](#refund-payment)
    + [Shipments](#shipments)
        + [Create a shipment](#create-shipment)
        + [Retrieve a shipment](#retrieve-shipment)
        + [List all shipments](#list-all-shipments)
    + [Customers](#customers)
        + [Create a customer](#create-customer)
        + [Retrieve a single customer](#retrieve-customer)
        + [List all customers](#list-all-customers)
        + [Update a customer](#update-customer)
    + [Taxes](#taxes)
        + [Retrieve sales tax](#retrieve-sales-tax)
    + [Account](#account)
        + [Retrieve account details](#retrieve-account)
    + [Events](#events)
        + [Retrieve an event](#retrieve-event)
        + [List all events](#list-all-events)
        + [Types of events](#types-of-events)


## Introduction

The Airbrite API is an ecommerce logic and storage engine designed to be an essential tool for any developer.

Our API is organized around REST and designed to have predictable, resource-oriented URLs, and to use HTTP response codes to indicate API errors. We support [cross-origin resource sharing](http://en.wikipedia.org/wiki/Cross-origin_resource_sharing) to allow you to interact securely with our API from a client-side web application (though you should remember that you should never expose your secret API key in any public website's client-side code). JSON will be returned in all responses from the API, including errors.

To make the Airbrite API as explorable as possible, accounts have test API keys as well as live API keys. These keys can be active at the same time. Data created with test credentials do not access live money.


## Getting Started

* The base endpoint URL is: `https://api.airbrite.io`
* All requests must be made over SSL
* To create new orders, you should setup your product first
* To process payments, you must connect to Stripe, which can be done in your account settings


## Authentication

Tokens are used to authenticate your requests. There are two sets of tokens (public and secret) for each environment (live and test). Public keys are used only on the client-side and will authenticate for only POST requests for Orders. Secret keys are used by your servers to make requests to the Airbrite API.

All endpoints require authentication. To authenticate with HTTP header, there are 3 ways you can set your header, where {ACCESS_TOKEN} is `sk_test_xxxxxx` or `sk_live_xxxxxx`.

1. Authorization: {ACCESS_TOKEN}
2. Authorization: Basic {BASE64_ENCODED_ACCESS_TOKEN}
3. Authorization: Bearer {ACCESS_TOKEN}

Alternatively, you can authenticate via query string by simply adding `?access_token={ACCESS_TOKEN}` to any request.


## Organization

The API is organized by version of the API and resource: `/{VERSION}/{RESOURCE}`. The current version of the API is `v2`. For example, to reach the Products endpoint, you would access `/v2/products`. To access a resource with a specific ID, the route is `/{VERSION}/{RESOURCE}/{ID}`.

These resources have full [CRUD](http://en.wikipedia.org/wiki/Create,_read,_update_and_delete) support:

* Products
* Orders
* Customers

These resources have read-only CRUD support:

* Events

The API also has a number of child resources (or subcollections). Child resources are accessible via the route `/{VERSION}/{RESOURCE}/{RESOURCE_ID}/{CHILD_RESOURCE}`. A specific resource can be accessed at `../{CHILD_RESOURCE}/{CHILD_RESOURCE_ID}`. 

Below are the parent and their respective child resources.

* Products
    + Events
* Orders
    + Payments
    + Shipments
    + Events
* Customers
    + Orders
    + Events
* Account
* Events
    + Webhooks


## Response Format

All responses return with a similar structure. Collections returns an array and single objects return an object. Here's an example:

__Response__

    {
      "meta": {
        "code": 200,
        "env": "test"
      },
      "paging": {
        "total": 7,
        "count": 1,
        "offset": 5,
        "limit": 1,
        "has_more": true
      },
      "data": [
        {
          "user_id": "522a72380ac3590000000001",
          "_id": "522e4eeccf84bd0000000007",
          "name": "Awesome Product",                    
          "sku": "awesome",
          "price": 1000,
          "created": 1378766572,
          "created_date": "2013-09-09T22:42:52.781Z",
          "updated": 1378766572,
          "updated_date": "2013-09-09T22:42:52.781Z",
          "description": null,
          "metadata": null
        }
      ]
    }

All collections accept pagination parameters and filters will respond with paging information about the collection. These are read from the passed in query string and can be mixed and matched as necessary.

__Endpoint__

    GET https://api.airbrite.io/v2/products?limit=10&skip=5&sort=sku&order=asc

__Arguments__

    limit:  optional
            Maximum number of objects to return. Defaults to 100.
    skip:   optional
            Number of objects to skip over before returning results
    sort:   optional
            Which field to sort by
    order:  optional
            Which way to sort (1 or -1 and asc or desc are the same, respectively)
    since:  optional
            Matches all items updated after unix timestamp
    until:  optional
            Matches all items updated before unix timestamp

__Response__

    { 
        ...,
        "paging": {
            "total": 45,
            "count": 10,
            "offset": 5,
            "limit": 10,
            "has_more": true
        }
        "data": {
          ...  
        }
    }


## Errors

Airbrite uses conventional HTTP response codes to indicate success or failure of an API request. In general, codes in the 2xx range indicate success, codes in the 4xx range indicate an error that resulted from the provided information (e.g. a required parameter was missing, a charge failed, etc.), and codes in the 5xx range indicate an error with Airbrite's servers.

Our error responses have the format: 

    {
        "meta": {
            "code": 400,
            "error_message": "User with email already exists.",
            "error_type": "client_error"
        },
        "data": "User with email already exists."
    }



## Resource Methods

### List of Routes

* Products
    + /v2/products
    + /v2/products/{PRODUCT_ID}
* Orders
    + /v2/orders
    + /v2/orders/{ORDER_ID}
    + /v2/orders/{ORDER_ID}/payments
    + /v2/orders/{ORDER_ID}/payments/{PAYMENT_ID}
    + /v2/orders/{ORDER_ID}/payments/{PAYMENT_ID}/capture
    + /v2/orders/{ORDER_ID}/payments/{PAYMENT_ID}/refund
    + /v2/orders/{ORDER_ID}/shipments
    + /v2/orders/{ORDER_ID}/shipments/{SHIPMENT_ID}
* Customers
    + /v2/customers
    + /v2/customers/{CUSTOMER_ID}
* Taxes
    + /v2/tax
* Events
    + /v2/events
    + /v2/events/{EVENT_ID}
    + /v2/events/{EVENT_ID}/webhooks


## Products

__Arguments__

    _id:          string
    user_id:      string
    created:      timestamp (Unix)
    created_date: timestamp (ISO_8601)
    updated:      timestamp (Unix)
    updated_date: timestamp (ISO_8601)
    sku:          string
                  Unique identifier for your product
    name:         string
    price:        positive integer or zero
                  Amount in cents
    description:  string
    metadata:     object
                  Store as many key-value pairs of extra data as you wish


### Create product

__Endpoint__

    POST https://api.airbrite.io/v2/products

__Arguments__

    Required: sku, price
    Optional: name, description, metadata


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
    Optional: limit, skip, sort, order, since, until


### Update product

__Endpoint__

    PUT https://api.airbrite.io/v2/products/{PRODUCT_ID}

__Arguments__

    Required: product_id
    Optional: none


## Orders

__Arguments__

    _id:              string
    user_id:          string
    created:          timestamp (Unix)
    created_date:     timestamp (ISO_8601)
    updated:          timestamp (Unix)
    updated_date:     timestamp (ISO_8601)
    currency:         string
                      3-letter ISO currency code
    customer_id:      string
    discount:         object
    line_items:       array
    order_number:     integer
                      Automatically designated by Airbrite
    shipping:         object
                      Contains cost (integer)
    shipping_address: object
                      Contains name, line1, line2, city, state, zip, country, phone
    status:           string
    tax:              object
                      Contains cost (integer)
    customer:         object
    payments:         array
                      Contains gateway, amount, charge_token
    shipments:        array
    description:      string
    metadata:         object
                      Store as many key-value pairs of extra data as you wish


### Create Order

If you'd like to add line_items, your product should be already created.

Regarding payments, the order can be created with either:
1) no existing payment data
2) existing payment data (already charged)
3) a Stripe card token (new payment upon order creation)

If using Stripe, the payment card should be tokenized with `stripe.js` before the order is created. With Stripe, there are two ways to manage payments. You can make a single charge at order creation or save the payment card for recurring/multiple charges.

To make a single charge, you have the option of capturing funds immediately or placing an authorization on the payment card. If capture is not specified, it will default to capturing funds immediately.

1) At order creation, capture funds immediately

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

2) At order creation, place an authorization/hold on the card (will drop off after ~7 days unless captured)

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

To save the payment card, you must pass the card_token in the customer object (rather than the payments array). This will create a Stripe customer object, which allows you to perform recurring charges and track multiple charges that are associated with the same customer.

        {
            ...,
            "customer": {
                "card_token": "tok_XXXXXXXXXXXXXX"
            },
            ...
        }

If you would like to save the payment card and make a charge immediately at order creation, you would pass the card token in the customer object and payment details in the payments array.

        {
            ...,
            "customer": {
                "card_token": "tok_XXXXXXXXXXXXXX"
            },
            "payments": [{
                "gateway": "stripe",
                "amount": 1337,
                "currency": "usd"
            }],
            ...
        }


__Endpoint__

    POST https://api.airbrite.io/v2/orders

__Arguments__

    Required: none
    Optional: customer_id, customer, line_items, shipping_address, shipping, tax, discount, payments, shipments, status, description, metadata

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
            "country": "US"  // two-letter ISO code
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
            "card_token": "tok_XXXXXXXXXXXXXX",
            "capture": "charge"
        }],
        "status": "open",
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
    Optional: limit, skip, sort, order, since, until


### Update order

To update an order (with the exception of customer, payments, and shipping), make a PUT request to the endpoint in this section. To create or update customer, payments, or shipments, use the following endpoints:

* https://api.airbrite.io/v2/customers/{CUSTOMER_ID}
* https://api.airbrite.io/v2/orders/{ORDER_ID}/payments/{PAYMENT_ID}
* https://api.airbrite.io/v2/orders/{ORDER_ID}/shipments/{SHIPMENT_ID}

__Endpoint__

    PUT https://api.airbrite.io/v2/orders/{ORDER_ID}

__Arguments__

    Required: order_id
    Optional: line_items, shipping_address, shipping, tax, discount, status, description, metadata


## Payments

__Arguments__

    _id:                  string
    user_id:              string
    order_id:             string
    created:              timestamp (Unix)
    created_date:         timestamp (ISO_8601)
    updated:              timestamp (Unix)
    updated_date:         timestamp (ISO_8601)
    currency:             string (3-letter ISO currency code)
    gateway:              string
    charge_token:         string
    amount:               integer
    customer_token:       string
    customer_card_token:  string
    capture:              string
    paid:                 boolean
    refunded:             boolean
    captured:             boolean
    charge_created:       timestamp (Unix)
    processed:            boolean
    card:                 object
                          Contains last4, type, exp_month, exp_year
    billing_address:      object
    description:          string
    metadata:             object
                          Store as many key-value pairs of extra data as you wish


### Create payment

1) If you're creating a new charge, pass in the card_token.

__Endpoint__

    POST https://api.airbrite.io/v2/orders/{ORDER_ID}/payments

__Arguments__

    Required: order_id, gateway, currency, amount, card_token
    Optional: capture, metadata

2) If you're adding an existing charge, pass in the charge_token.

__Endpoint__

    POST https://api.airbrite.io/v2/orders/{ORDER_ID}/payments

__Arguments__

    Required: order_id, gateway, charge_token
    Optional: metadata


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
    Optional: limit, skip, sort, order, since, until


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
    Optional: limit, skip, sort, order, since, until


## Customers

__Arguments__

    _id:              string
    user_id:          string
    created:          timestamp (Unix)
    created_date:     timestamp (ISO_8601)
    updated:          timestamp (Unix)
    updated_date:     timestamp (ISO_8601)
    name:             string
    email:            string
    addresses:        array
    default_address:  object
                      Contains name, line1, line2, city, state, zip, country, phone
    card_token:       string
    stripe:           object
    description:      string
    metadata:         object
                      Store as many key-value pairs of extra data as you wish


### Create customer

__Endpoint__

    POST https://api.airbrite.io/v2/customers

__Arguments__

    Required: 
    Optional: 


### Retrieve customer

__Endpoint__

    GET https://api.airbrite.io/v2/customers/{CUSTOMER_ID}

__Arguments__

    Required: customer_id
    Optional: none


### List all customers

__Endpoint__

GET https://api.airbrite.io/v2/customers

__Arguments__

    Required: none
    Optional: limit, skip, sort, order, since, until


### Update customer

__Endpoint__

    PUT https://api.airbrite.io/v2/customers/{CUSTOMER_ID}

__Arguments__

    Required: customer_id
    Optional: 


## Tax

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

Events are our way of letting you know about something interesting that has just happened in your account. When an interesting event occurs, we create a new event object. For example, when a payment is successfully charged, we create a order.payment.succeeded event. Note that many API requests may cause multiple events to be created.

Like our other API resources, you can retrieve an individual event or a list of events from the API. We also have a system for sending the events directly to your server, called webhooks. Webhooks are managed in your account settings.


### Retrieve event

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
    Optional: limit, skip, sort, order, since, until


### Types of events

This is a list of all the types of events we currently send. We may add more at any time, so you shouldn't rely on only these types existing in your code.

You'll notice that these events follow a pattern: resource.event. Our goal is to design a consistent system that makes things easier to anticipate and code against.

__Events__

* customer.created
* customer.updated
* order.created
* order.updated
* order.payment.succeeded
* order.payment.authorized
* order.payment.captured
* order.payment.refunded
* product.created
* product.updated
