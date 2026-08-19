---
name: refersion-track-orders
description: Report completed orders to Refersion so commissions are calculated — client-side with refersion.js on the thank-you page, or server-side by posting the order and its cart_id to the inbound tracking webhook.
api: refersion:refersion-order-tracking-api
generated: '2026-08-13'
method: generated
source: https://www.refersion.dev/reference/webhook-tracking and https://www.refersion.dev/reference/javascript-v4-tracking
operations: []
---

# Track orders into Refersion

This flow does **not** use the REST API v2. It is a separate surface: a browser tracking library
plus one inbound webhook endpoint. It has no `operationId` because Refersion publishes no OpenAPI
for it — everything below is verbatim from the developer portal.

If your storefront is Shopify, BigCommerce, WooCommerce or Magento, Refersion's app handles this
and you should not implement it by hand.

## Step 1 (every page) — install the click tracker

Put this in `<head>` on every page affiliate traffic can land on, including the order-confirmation
page. Replace the placeholder with your **public** key only; the secret key is never used in the
browser.

```html
<script>
! function(e, n, t, i, o, c, s, a) {
    e.TrackingSystemObject = "r", (s = n.createElement(t)).async = 1,
    s.src = "https://cdn.refersion.com/refersion.js", s.onload = function() {
        r.pubKey = "YOUR-PUBLIC-KEY";
        r.settings.fp_off = true;
        r.initializeXDLS().then(() => {
            r.launchDefault().then(() => {
                document.dispatchEvent(new Event("refersion-loaded"));
            })
        })
    }, (a = n.getElementsByTagName(t)[0]).parentNode.insertBefore(s, a)
}(window, document, "script");
</script>
```

Always bind your own code to the `refersion-loaded` event rather than calling `r.*` directly — the
library loads async. Note the script URL is **unpinned**: there is no version segment and no
integrity hash, so the build you execute can change without notice.

## Then choose ONE of step 2a or step 2b

### Step 2a — client-side reporting (thank-you page)

Requires v4 tracking to be enabled first under Account > Settings > Tracking.

```js
document.addEventListener("refersion-loaded", function() {
  r.addTrans({ order_id: '3123123', shipping: '12.90', tax: '8.23',
               discount: '4.32', discount_code: 'TESTCOUPON',
               currency_code: 'USD', is_subscription: true, subscription_id: '12345' });
  r.addCustomer({ first_name: 'Nancy', last_name: 'Parker',
                  email: 'parker.nancy@example.com', ip_address: '203.0.113.22' });
  r.addItems({ sku: 'DD23444', quantity: '2', price: '100' });   // one call per line item
  r.sendConversion();
});
```

`order_id` and `currency_code` are required. `price` is the **unit** price, not the extended line
total. This code must run on the **same domain and the same protocol** as the click tracker — if
checkout happens on a different host you must either implement cross-domain tracking or fire the
call before the domain switch.

### Step 2b — server-side reporting (recommended when you control the backend)

**Browser half.** After the cart exists, hand Refersion a `cart_id` you mint yourself:

```js
document.addEventListener("refersion-loaded", function() {
  r.sendCheckoutEvent(
    "YOUR-CART-ID",
    localStorage.getItem("rfsn_v4_id"),
    window.location.href,
    localStorage.getItem("rfsn_v4_aid"),
    localStorage.getItem("rfsn_v4_cs")
  );
});
```

`cart_id` is any string up to 255 characters and **must not be sequential or guessable** — the docs
warn against session IDs and simple encodings. Platform-native values work well: Shopify cart or
checkout token, BigCommerce cart/checkout ID, WooCommerce order key, Stripe customer or
subscription ID, Chargebee subscription ID.

**Server half.** Once the order is paid, POST the order JSON with the same `cart_id`:

```
POST https://inbound-webhooks.refersion.com/tracker/orders/paid
Content-Type: application/json
Refersion-Public-Key: <public key>
Refersion-Secret-Key: <secret key>

{
  "cart_id": "...", "order_id": "20150401102883",
  "shipping": 9.99, "tax": 0.57, "discount": 2.25, "discount_code": "HOLIDAY1",
  "currency_code": "USD",
  "customer": { "first_name": "John", "last_name": "Doe",
                "email": "jdoe@example.com", "ip_address": "203.0.113.75" },
  "items": [ { "price": 5.50, "quantity": 2, "sku": "PROD_A", "name": "Product A" } ]
}
```

Required: `cart_id`, `order_id`, `currency_code`, and `sku`/`quantity`/`price` on each item.
Subscriptions additionally require `subscription_id` and `is_subscription: true` — both are
available **only** in webhook reporting, not in the client-side path. `auto_credit_affiliate_id`
optionally forces the credit to a specific affiliate.

## Step 3 — confirm

Subscribe to the **New Conversion** webhook and match on `order_id`. Refersion publishes no
read-back operation for conversions, so the webhook is the only confirmation channel — see
`asyncapi/refersion-webhooks.yml`. Deliveries carry `Refersion-Topic` and `Refersion-Signature`
headers, but Refersion's signature-verification page is a dead link, so verification cannot be
implemented from public documentation today.
