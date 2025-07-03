# Smartcheckout API Documentation for Agora

## Authentication

All API requests must be authenticated. Send your API key as a Bearer token in the `Authorization` header.

`Authorization: Bearer YOUR_API_KEY`

## API Endpoints

We have enabled two primary endpoints for you to get started.

### 1. Create a Checkout

This endpoint initiates a new purchase. You need to provide the product `url` and a `user_data` JSON object containing the necessary information for the purchase (e.g., email, shipping details, etc). There is no need to include any Credit Card information. We will use our own Virtual Credit Cards to complete the payments. We also support capturing and using your end user's CC directly so you do not need to deal with refunds, be the merchant on record, etc...

-   **URL:** `https://api.smartcheckout.dev/v0/checkout`
-   **Method:** `POST`

#### Example Request (`curl`)

You can create a checkout by providing a product URL and user data. The `user_data` field is a flexible JSON object that should contain all necessary information for the purchase, like email, shipping address, and any specific prompts (e.g., size, color).

```bash
# Set your API Key
export API_KEY="YOUR_API_KEY_HERE"

# Create a new checkout
curl -X POST "https://api.smartcheckout.dev/v0/checkout" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $API_KEY" \
  -d '{"url": "https://example-store.com/product/item", "user_data": {"email": "test@example.com", "prompt": "Size Large, Color Blue"}}'
```

#### Response

The API will respond with a `checkout_id` and the initial `status` (`processing`).

```json
{
  "checkout_id": "a8043a0d-bb94-4d26-aa9f-8ca02ad146fd",
  "status": "processing"
}
```

### 2. Get Checkout Details

This endpoint retrieves the current status and other details of a previously created checkout.

-   **URL:** `https://api.smartcheckout.dev/v0/checkout/:checkout_id`
-   **Method:** `GET`

#### Example Request (`curl`)

Use the `checkout_id` from the creation step to poll for the status.

```bash
# Set the checkout_id from the previous response
export CHECKOUT_ID="a8043a0d-bb94-4d26-aa9f-8ca02ad146fd"

# Get checkout status
curl -s "https://api.smartcheckout.dev/v0/checkout/$CHECKOUT_ID" \
  -H "Authorization: Bearer $API_KEY"
```

#### Response

The response includes the checkout `status`, final `amount` and `currency` if completed, and `notes` with details, especially in case of failure.

**Possible Statuses:**
*   `processing`: The purchase is in progress.
*   `completed`: The purchase was successful.
*   `failed`: The purchase could not be completed.

```json
{
  "id": "a8043a0d-bb94-4d26-aa9f-8ca02ad146fd",
  "status": "completed",
  "amount_str": "49.99",
  "currency": "USD",
  "notes": ""
}
```

## Testing

The simplest way to test the full checkout flow without using your account credit is with a Stripe test payment link. You can easily create payment links for test products from your Stripe dashboard. Our service can then use this URL to perform a test purchase.


## What's Next?

We are actively developing a dashboard for account management, API key rotation, and more detailed analytics. We will be standardizing the `user_data` format soon. Until then our feedback is be crucial in guiding us on what to build!