# unMineable - Public API reference
We provide the following public API endpoints for querying accounts stats and information, useful for developers looking to create reporting websites or apps on top of our platform.

### Base URL: https://api.unminable.com/v4

Note that **HTTPS** is required, you can use "api.unmineable.com" or "api.unminable.com" (without the "e") both will work. All responses are in _JSON_ format (**Content-type: application/json** is required). All numbers are returned in string format, with the exception of timestamp values.

### Limits

There is a 500 requests per minute per IP limit. Also every request is cached by 1 second, so there is no need to make more than 1 request per second per endpoint, as the data won't update until a second after.

### Available endpoints

* **GET**  /address/:address?coin=:coin
* **GET**  /account/:uuid
* **GET**  /account/:uuid/stats
* **GET**  /account/:uuid/workers
* **GET**  /account/:uuid/payments
* **GET**  /account/:uuid/referrals

## Reference

### GET  /address/:address?coin=:coin

Returns general stats (balance related) for a specific **address** and **coin**. This endpoint returns the **uuid** field which is used for every following endpoint.

Input example: 

```
GET https://api.unminable.com/v4/address/A_VALID_TRX_ADDRESS?coin=TRX
```

Output example:

```
{
 "success": true,
 "msg": "Ok",
 "data": {
     "balance": "10.26909",  →  Current total pending balance.
     "balance_payable": "10.26909",  →  Total pending payable balance (some coins like NEO doesn't have decimals, thus this value is different (rounded) from the "balance").
     "payment_threshold": "15",  →  Current payment threshold.
     "mining_fee": "0.75",   →  Can also be: "1", if no referral code is being used.
     "auto": false,  →  Indicates whether auto payment is on (true) or off (false) for the address.
     "enabled": true, →  Indicates whether payments are enabled (true) or under maintenance (false).
     "uuid": "22d53b0b-24a2-4c1d-9f56-175d3a2e", →  Required for following requests.
     "fresh": false →  Indicates if an address is new (true if haven't mined before, false if  it has had an online worker at some point)
    }
}
```

### GET  /account/:uuid

Returns general stats (balance related) for a specific **uuid**, you must obtain the **uuid** from the first request (to _/address/:address?coin=:coin_). The output is the same as the first request, except for the "fresh" field, which is not returned. _Using this endpoint for subsequential balance requests is recommended, as it is faster._

Input example:

```
GET https://api.unminable.com/v4/account/A_VALID_UUID
```

Output example:

```
{
 "success": true,
 "msg": "Ok",
 "data": {
     "balance": "10.26909",  →  Current total pending balance.
     "balance_payable": "10.26909",  →  Total pending payable balance (some coins like NEO doesn't have decimals, thus this value is different (rounded) from the "balance").
     "payment_threshold": "15",  →  Current payment threshold.
     "mining_fee": "0.75",   →  Can also be: "1", if no referral code is being used.
     "auto": false,  →  Indicates whether auto payment is on (true) or off (false) for the uuid.
     "enabled": true, →  Indicates whether payments are enabled (true) or under maintenance (false).
     "uuid": "22d53b0b-24a2-4c1d-9f56-175d3a2e"
    }
}
```

### GET  /account/:uuid/stats

Returns the stats for a specific **uuid**.

Input example:

```
GET https://api.unminable.com/v4/account/A_VALID_UUID/stats
```

Output example:

```
{
    "success": true,
    "msg": "Ok",
    "data": {
        "balance_mining": "8.68065594", →  Current pending balance obtained by mining.
        "balance_referral": "0", →  Current pending balance obtained by referrals.
        "balance": "8.68065594", →  Current total pending balance.
        "rewarded": {
            "past_24h": "0.24640626", →  Past 24h rewarded coins.
            "past_7d": "1.5948849", →  Past 7 days rewarded coins.
            "past_30d": "1.5948849" →  Past 30 days rewarded coins.
        },
        "paid": "0", →  Total paid until current time, the amount is cached by every 15 minutes.
        "last_payment": null, →  Timestamp for the last payment.
        "payment_threshold": "30", →  Current payment threshold.
        "mining_fee": "0.75" →  Can also be: "1", if no referral code is being used in any worker.
    }
}
```

### GET  /account/:uuid/workers

Returns the workers for a specific **uuid**. With their current stats and chart data (per algorithm).

Input example:

```
GET https://api.unminable.com/v4/account/A_VALID_UUID/workers
```

Output example:

```
{
    "success": true,
    "msg": "Ok",
    "data": {
        "ethash": {
            "workers": [
                {
                    "online": true, →  Online status (if "last" is older than 15 minutes, a worker is considered as offline.)
                    "name": "Worker_001", →  Given worker name.
                    "last": 1626042536000, →  Last activity timestamp.
                    "rhr": 326.6909, →  Reported hashrate
                    "chr": 340.2421, →  Calculated hashrate
                    "referral": "bbbb-aaaa" →  Used referral code (can be null if not referral code is used)
                }
            ],
            "chart": {
                "reported": {
                    "data": [
                      "326.69", →  Reported hr per timestamp (for the same "timestamps" array index)
                      "326.69",
                      "326.69",
                      "326.69",
                      "326.69"
                    ],
                    "timestamps": [
                      1625856766000, →  Timestamp for the same "data" array index.
                      1625856830000,
                      1625856894000,
                      1625856959000,
                      1625857024000
                    ]
                },
                "calculated": {
                    "data": [
                      "340.24",
                      "340.24",
                      "340.24",
                      "340.24",
                      "340.24",
                    ],
                    "timestamps": [
                      1625856766000,
                      1625856830000,
                      1625856894000,
                      1625856959000,
                      1625857024000
                    ]
                }
            }
        },
        "etchash": {
            "workers": [],
            "chart": {
                "reported": {
                    "data": [],
                    "timestamps": []
                },
                "calculated": {
                    "data": [],
                    "timestamps": []
                }
            }
        },
        "randomX": {
            "workers": [],
            "chart": {
                "reported": {
                    "data": [],
                    "timestamps": []
                },
                "calculated": {
                    "data": [],
                    "timestamps": []
                }
            }
        },
        "kawpow": {
            "workers": [],
            "chart": {
                "reported": {
                    "data": [],
                    "timestamps": []
                },
                "calculated": {
                    "data": [],
                    "timestamps": []
                }
            }
        }
    }
}
```

### GET  /account/:uuid/payments?page=:page_number

Returns the payment list for a specific **uuid**. The list is paginated (10 rows per page), from the most recent to the oldest payout. Note that the current page logic must be implement on client side (should be incremented or decreased depending the current page changes).

Input example:

```
GET https://api.unminable.com/v4/account/A_VALID_UUID/payments?page=1
```

Output example:

```
{
    "success": true,
    "message": "Ok",
    "data": {
        "explorer": "https://tronscan.org/#/transaction/", →  Default explorer used on the website, it can be used to append the "tx" field and create a valid link to the tx.
        "coin": "TRX",  → Payment coin
        "list": [
            {
                "tx": "5ee7e976322950653ad38a67f60078ff479291107cce91222c5713c067",  →  Transaction id (on the blockchain) for the payout, can be null if the payment is in pending or error status.
                "amount": "16.020691", → Payment amount
                "timestamp": 1610035918000, → Timestamp of the payment request.
                "status": "success", → Payment status: "pending", "processing", "success" and "error"
                "id": 379743, → Internal id
                "reason": "INVALID_ADDRESS" → Error in payment reason (only available when the payment is in "error" status.)
            },
            (...up to 10 rows)
        ],
        "has_next": true, → Indicates whether a next page button should be visible (on client side).
        "has_prev": false, → Indicates whether a previous page button should be visible (on client side).
        "last_page": 8  → Last page available (you can run a request to "(...)?page=:last_page" to implement a last page (or oldest payments) button.
    }
}
```

### GET  /account/:uuid/referrals

Returns the referral code for a specific **uuid**.

Input example:

```
GET https://api.unminable.com/v4/account/A_VALID_UUID/referrals
```

Output example:

```
{
    "success": true,
    "message": "Ok",
    "data": {
        "code": "aaa-bbb" →  The referral code.
    }
}
```
