# Before retrying a timed-out order

An empty response does not tell you whether an order was saved. In this lab, the server can commit the order and then deliberately drop the response. Repeating the purchase with a new key creates another order.

## Keep one purchase attempt identifiable

When a customer reports a timeout, collect the order reference if available, the request key, the requested SKU, the request time and the response or connection error. Keep sensitive purchase records in your internal support system. Use synthetic values in a public issue.

Before offering a retry button, decide where the application retains the attempt key. The retry should reuse that key and the same purchase details. A new purchase needs a new key.

## Use the existing lab to review the behavior

Run `python3 -B -m unittest -v` from the repository directory. Then follow the README's lost-response experiment using a fresh key. The retry omits `X-Demo-Drop-Response` and should return HTTP 200 with the order already saved. The test also checks that this experiment left one order in the database.

| Question for a handover | Evidence in this lab |
| --- | --- |
| Does a retry return the original order? | `test_retry_reuses_order` |
| What happens if the response disappears after commit? | `test_lost_response_after_commit` |
| Can the same key silently buy a different SKU? | `test_same_key_different_purchase_is_conflict` expects 409 |
| Can concurrent calls create extra orders? | `test_twelve_concurrent_calls_create_one_order` uses twelve calls |
| Does a restart forget the key? | `test_restart_retains_idempotency` reuses the same database |

This lab checks order creation. A real checkout also needs separate evidence for payment verification and fulfillment. Reusing an order does not demonstrate that a payment was captured or a download delivered.

## If a result differs, report a reproducible case

Copy this into your notes or a public issue with synthetic data:

```text
Python version and operating system
Repository version or commit
Database path: fresh or reused across restart?
Request sequence (synthetic SKU and keys)
Expected status and order ID behavior
Actual status and order ID behavior
Relevant error output with credentials removed
```

For an order to retry safely across a restart, it must use the same stored database. If you deliberately change the database path, record that change before comparing results.

The [walkthrough](https://fone.cc/insights/python-sqlite-idempotent-order-api/?utm_source=github&utm_medium=referral&utm_campaign=gh_retry_handoff_20261116) shows the requests and records. For payment-to-delivery diagnosis, use the [merchant troubleshooting guide](https://fone.cc/insights/stablecoin-payment-confirmed-order-not-delivered/?utm_source=github&utm_medium=referral&utm_campaign=gh_retry_handoff_20261116).
