## Overview

* [Introduction](#introduction)
* [Authentication](#authentication)

Orders
* [Create an order](#create-order)
* [Retrieve a single order](#retrieve-order)
* [List all orders](#list-all-orders)
* [Update an order](#update-order)

Products
* [Create a product](#create-product)
* [Retrieve a single product](#retrieve-product)
* [List all products](#list-all-product)
* [Update a product](#update-product)

Account
* [Retrieve account details](#retrieve-account)

## Introduction

The Airbrite API is organized around REST. Our API is designed to have predictable, resource-oriented URLs and to use HTTP response codes to indicate API errors.

The base endpoint URL is: `https://api.airbrite.io`

API Endpoints:

* /v2/orders
* /v2/products
* /v2/account

Remember to set the HTTP header on all POST/PUT requests:

    "Content-Type: application/json"

All currency amounts and costs are in CENTS!


## Authentication

All endpoints are authenticated. To authenticate, send an HTTP header with the access token:

    "Authorization: Basic sk_test_xxxxxxxxxxxxxxxxxxxxxxxxx"

## Orders

### Create Order

The order resource contains a metadata field for storing key-value pairs of extra data. Store as many of these key-value pairs as you wish.

Regarding payments, the order can be created with either 1) existing payment data (already charged), or 2) a Stripe card token (new payment upon order creation).

If using Stripe, the payment method should be pre-tokenized with `stripe.js` before the order is created. With Stripe, there are three ways to create orders.

* At order creation, charge the payment card once and only once (single-use)

        {
            ...,
            "payments": [{
                "gateway": "stripe",
                "amount": 1337,    // amount > 0
                "currency": "usd",
                "card_token": "tok_XXXXXXXXXXXXXX",
                "create_stripe_customer": false    // set to false
            }],
            ...
        }

* At order creation, save payment card to be charged at a future date (multi-use)  

        {
            ...,
            "payments": [{
                "gateway": "stripe",
                "amount": 0,    // amount = 0
                "currency": "usd",
                "card_token": "tok_XXXXXXXXXXXXXX",
                "create_stripe_customer": true    // set to true
            }],
            ...
        }

* At order creation, charge payment card now, and save payment card to be charged again at a future date (multi-use)

        {
            ...,
            "payments": [{
                "gateway": "stripe",
                "amount": 1337,    // amount > 0
                "currency": "usd",
                "card_token": "tok_XXXXXXXXXXXXXX",
                "create_stripe_customer": true    // set to true
            }],
            ...
        }


__Endpoint__

    POST https://api.airbrite.io/v2/orders

__Arguments__

    Required: customer
    Optional: line_items, shipping_address, shipping, tax, discount, payments, shipments, metadata

__Body__

    {
        "customer": {
            "name": "Jack Dagnels",
            "email": "jack@dagnels.ru",
            "phone": "4157875227"
        },
        "line_items": [
            {
                "sku": "12345ABC",
                "price": 15000,
                "qty": 1
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
            "country": "US"
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
            "create_stripe_customer": false
        }],
        "shipments": [{
            "courier": "ups",
            "method": "ground",
            "tracking": "1ZXXXXXXXXXXXX",
            "line_items": [{
                "sku": "12345ABC",
                "qty": 1
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

__Endpoint__

    PUT https://api.airbrite.io/v2/orders/{ORDER_ID}

__Arguments__

    Required: order_id
    Optional: 


## Products

### Create product

__Endpoint__

    POST https://api.airbrite.io/v2/products

__Arguments__

    Required: 
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
    Optional: 


## Account

### Retrieve account

__Endpoint__

    GET https://api.airbrite.io/v2/account

__Arguments__

    Required: none
    Optional: none
