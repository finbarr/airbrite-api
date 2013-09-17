# Creating an Order

This tutorial helps you create your first order with Airbrite. If you need help, refer to our API documentation or email support@airbriteinc.com.

__Step 1: Create your Product__

To get started, let's make sure you have at least one product in Airbrite. Make the following request:

    curl https://api.airbrite.io/v2/products \
        -u sk_test_a805be8b2add854f09976b3b5c0f5bd06c14617c: \
        -d sku=first-product \
        -d price=995 \
        -d "description=My First Product"

        curl uses the -u flag to pass basic auth credentials (adding a colon after your API key will prevent it from asking you for a password).

        A sample test API key has been provided, so you can test out any example right away.

If successful, the API will respond with the Product object:


__Step 2: Create your Order__

Next, we'll make an order to purchase 1 item of "first-product". 

curl https://api.airbrite.io/v2/orders \
    -u sk_test_a805be8b2add854f09976b3b5c0f5bd06c14617c: \
    -d "line_items[0][sku]=first-product"\
    -d "line_items[0][quantity]=1"

Congratulations! You've created your first order.

