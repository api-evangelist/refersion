# Refersion GraphQL API

## Description

Refersion provides a GraphQL API that supplements its REST API, enabling flexible, ad-hoc queries and data manipulation for affiliate marketing programs. The GraphQL API allows clients to request exactly the data they need in a single call rather than making multiple REST requests, and uses a strongly-typed schema that governs all client-server communication.

Refersion's GraphQL API covers affiliate management, conversion tracking, commission configuration, offer management, and performance reporting — the same domain as the REST API but with the flexibility and efficiency of GraphQL query composition.

## Endpoint

```
POST https://api.refersion.com/graphql
```

## Authentication

Requests require Refersion API credentials passed as HTTP headers:

- `Refersion-Public-Key`: Your account public API key
- `Refersion-Secret-Key`: Your account secret API key

## Documentation

- GraphQL Overview: https://www.refersion.dev/reference/graphql-overview
- Full API Reference: https://www.refersion.dev/reference/welcome-to-refersion
- GraphQL Explorer: Available within the Refersion developer portal

## Schema Source

Refersion's GraphQL endpoint (`https://api.refersion.com/graphql`) requires authentication and does not expose unauthenticated introspection. The SDL in `refersion-schema.graphql` is a conceptual schema derived from the confirmed REST API types, field names, enumerations, and relationships documented at https://www.refersion.dev and in the Refersion OpenAPI specification. All type names, field names, and enum values are grounded in the REST API surface.

## Key Types

| Type | Description |
|------|-------------|
| `Affiliate` | An affiliate account with profile, status, offer assignment, and tracking link |
| `ConversionTrigger` | A coupon code, SKU, or email address that fires a conversion attribution |
| `Conversion` | A tracked purchase or credit event attributed to an affiliate |
| `Offer` | A commission program defining rates and rules for a set of affiliates |
| `SkuCommission` | A product-level commission rate configuration within an offer |
| `ConversionTotals` | Aggregated commission and order statistics across filtered conversions |
| `Report` | A saved performance report with a time-limited download link |
| `CustomField` | A label-value pair attached to an affiliate for merchant-defined data |
| `OrderItem` | A line-item (SKU, price, quantity) within an order cancellation event |
