# Creating an Order

This tutorial helps you create your first order with Airbrite. If you need help, refer to our API documentation or email support@airbriteinc.com.

__Step 1: Create your Product__

To get started, let's make sure you have at least one product in Airbrite. Make the following request (and don't forget to use your `Test Secret Key` which can be found in Account Settings):

    curl https://api.airbrite.io/v2/products \
        -u {TEST SECRET KEY}: \
        -d sku=first-product \
        -d price=995 \
        -d "description=My First Product"