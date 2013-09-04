## Overview

* [Introduction](#introduction)
* [Authentication](#authentication)

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

The Airbrite API is organized around REST. Our API is designed to have predictable, resource-oriented URLs and to use HTTP response codes to indicate API errors.

The base endpoint URL is: `https://api.airbrite.io`

API Endpoints:

* /v2/customers
* /v2/customers/{CUSTOMER_ID}
* /v2/orders
* /v2/orders/{ORDER_ID}
* /v2/orders/{ORDER_ID}/payments
* /v2/orders/{ORDER_ID}/payments/{PAYMENT_ID}
* /v2/orders/{ORDER_ID}/payments/{PAYMENT_ID}/refund
* /v2/orders/{ORDER_ID}/shipments
* /v2/orders/{ORDER_ID}/shipments/{SHIPMENT_ID}
* /v2/products
* /v2/products/{PRODUCT_ID}
* /v2/tax
* /v2/account
* /v2/events

Remember to set the HTTP header on all POST/PUT requests:

    "Content-Type: application/json"

All currency amounts and costs are in CENTS!


## Authentication

All endpoints are authenticated. To authenticate, send an HTTP header with the access token:

    "Authorization: Bearer sk_test_xxxxxxxxxxxxxxxxxxxxxxxxx"


## Products

### Create product

__Endpoint__

    POST https://api.airbrite.io/v2/products

__Arguments__

    Required: sku, price, name
    Optional: none


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
    Optional: since/from, until/to


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
            "country": "US",
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
    Optional: since/from, until/to


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

    Required: gateway, currency, amount
    Optional: (card_token and capture OR charge_token), metadata


### Retrieve payment

__Endpoint__

    GET https://api.airbrite.io/v2/orders/{ORDER_ID}/payments/{PAYMENT_ID}

__Arguments__

    Required: payment_id
    Optional: none


### List all payments

__Endpoint__

    GET https://api.airbrite.io/v2/orders/{ORDER_ID}/payments

__Arguments__

    Required: none
    Optional: since/from, until/to


### Refund payment

__Endpoint__

    PUT https://api.airbrite.io/v2/orders/{ORDER_ID}/payments/{PAYMENT_ID}/refund

__Arguments__

    Required: payment_id
    Optional: 


## Shipments

### Create shipment

__Endpoint__

    POST https://api.airbrite.io/v2/orders/{ORDER_ID}/shipments

__Arguments__

    Required: line_items
    Optional: courier, shipping_address, tracking, method, metadata


### Retrieve shipment

__Endpoint__

    GET https://api.airbrite.io/v2/orders/{ORDER_ID}/shipments/{SHIPMENT_ID}

__Arguments__

    Required: shipment_id
    Optional: none


### List all shipments

__Endpoint__

    GET https://api.airbrite.io/v2/orders/{ORDER_ID}/shipments

__Arguments__

    Required: none
    Optional: since/from, until/to


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

    Required: none
    Optional: none


### List all events

__Endpoint__

    GET https://api.airbrite.io/v2/events

__Arguments__

    Required: none
    Optional: since/from, until/to


### Types of events

This is a list of all the types of events we currently send. We may add more at any time, so you shouldn't rely on only these types existing in your code.

You'll notice that these events follow a pattern: resource.event. Our goal is to design a consistent system that makes things easier to anticipate and code against.

__Events__

* order.created
* order.updated
* order.payment.suceeded
* order.payment.authed
* order.payment.captured
* order.payment.refunded
