# Airbrite API


## Quick Intro

The Airbrite API is a RESTful ecommerce logic and storage engine designed to be an essential tool for any developer.  




## Getting Started

To get started with the API, it's useful to know some essentials.

* The API's base URL can be found at `https://api.airbrite.io`

* Your access tokens can be found in "My Account" under "Keys"

* There are two environments - a live environment that uses your live keys and a test environment that uses your test keys.  For a payment provider like Stripe, we will also use the Stripe live or test keys for making calls to their API based on which key is used.

* You can connect to Stripe from "My Account" under "Payments"




## Authentication

Tokens are used for authenticating your requests.  There are two sets of two tokens for each environment - live and test, public and secret.  Live tokens will access your live data and use your live keys against other APIs.  Test tokens will use your test data.  Public keys are OK to use client-side in your javascript code and will authenticate for only POST requests to orders (and other limited calls) while the secret keys are what your servers should be using to make requests.    
All endpoints require authentication, which have two methods of authentication.

* The recommended method is to pass in the HTTP header field "Authorization" with the value "{access_token}", where token is "sk_test_xxx..." or "sk_live_xxx".  Also accepted are "Authorization: Basic {base_64_encoded_token}" and "Authorization: Bearer {access_token}".

* Another method is to pass access_token in via query string.  This can be particularly useful in debugging GET requests.  You can simply add "?access_token={access_token}" to any request to authenticate.




## Organization

The API is organized by version of the API and resource (/{version}/{resource}).  The current version of the API is v2. For example, to reach the products end point, you would access /v2/products. To access a resource with a particular ID the route is /{version}/{resource}/{id}, or /v2/products/52323272fa361e040c000001.

These resources have full CRUD support:

* Products (/v2/products)
* Orders
* Customers

These resources have read-only CRUD support:

* Events (/v2/events)
* Logs (/v2/logs)

Because of the inherent complexity of ecommerce, the API also has a number of child resources.  Child resources are accessible via the route "/{version}/{resource}/{resource_id}/{child_resource}" and are related to the parent, and a particular resource can be accessed at "../{child_resource}/{child_resource_id}".  

* Orders
  + Payments (/v2/orders/{order_id}/payments)
  + Shipments
  + Events (/v2/orders/{order_id}/events - read-only)



