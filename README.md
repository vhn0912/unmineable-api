# unMineable - Public API reference
We provide the following public API endpoints for querying accounts stats and information, useful for developers looking to create reporting websites or apps on top of our platform.

### Base URL: https://api.unminable.com/v4

Note that **HTTPS** is required, you can use "api.unmineable.com" or "api.unminable.com" (without the "e") both will work. All responses are in _JSON_ format (**Content-type: application/json** is required). All numbers are returned in string format, with the exception of timestamp values.

### Limits

There is a 500 requests per minute per IP limit. Also every request is cached by 1 second, so there is no need to make more than 1 request per second per endpoint, as the data won't update until a second after.

### Available endpoints

* **GET**  /pool
* **GET**  /coin
* **GET**  /coin/:coin
* **GET**  /address/:address?coin=:coin
* **GET**  /account/:uuid
* **GET**  /account/:uuid/stats
* **GET**  /account/:uuid/workers
* **GET**  /account/:uuid/payments
* **GET**  /account/:uuid/referrals

## Reference

### GET  /pool

Returns general pool stats.

Input example: 

```
GET https://api.unminable.com/v4/pool
```

Output example:

```
{
    "success": true,
    "msg": "Ok",
    "data": {
        "summary": {
            "total_paid_btc": "245.72662209", →  Total paid by the platform in BTC value
            "past_24hr_miners": "56334", →  Past 24 hour miners (unique addresses) online.
            "past_24hr_workers": "102861" →  Past 24 hour workers online.
        },
        "pools": {
            "ethash": {
                "last_reward": 1626406786, →  Time of the last credited reward, to any miner connected to the ethash pool (for all coins).
                "avg_reward_time": 5 → Average time (in minutes) between rewards, calculated with data from past 24hr.
            },
            "etchash": {
                "last_reward": 1626407003, →  Time of the last credited reward, to any miner connected to the etchash pool (for all coins).
                "avg_reward_time": 4 → Average time (in minutes) between rewards, calculated with data from past 24hr.
            },
            "kawpow": {
                "last_reward": 1626407110, →  Time of the last credited reward, to any miner connected to the kawpow pool (for all coins).
                "avg_reward_time": 4 → Average time (in minutes) between rewards, calculated with data from past 24hr.
            },
            "randomx": {
                "last_reward": 1626406990, →  Time of the last credited reward, to any miner connected to the randomx pool (for all coins).
                "avg_reward_time": 6 → Average time (in minutes) between rewards, calculated with data from past 24hr.
            }
        }
    }
}
```

### GET  /coin

Returns the full list of supported coins.

Input example: 

```
GET https://api.unminable.com/v4/coin
```

Output example:

```
{
    "success": true,
    "msg": "Ok",
    "data": [
        {
            "symbol": "BTT", → Coin symbol
            "network": "TRX", → Coin network (supported network)
            "token_standard": "TRC10", → Token type, if the coin is a token in a network.
            "name": "BitTorrent", → Coin / Token name
            "regex": "^T[1-9A-HJ-NP-Za-km-z]{33}$", → Validation RegExp (for address)
            "regex_memo": null, →  Validation RegExp (for memo / payment tag)
            "top": 1, → Indicates whether a coin should be at the top of the list (1) or not (0).
            "logo": "https://www.unmineable.com/img/logos/BTT.png?v1" → Coin logo (hosted on the website)
        },
        {
            "symbol": "MATIC",
            "network": "ETH",
            "token_standard": "ERC20",
            "name": "MATIC (Polygon)",
            "regex": "^(0x)[0-9A-Fa-f]{40}$",
            "regex_memo": null,
            "top": 1,
            "logo": "https://www.unmineable.com/img/logos/MATIC.png"
        },
        {
            "symbol": "SHIB",
            "network": "ETH",
            "token_standard": "ERC20",
            "name": "SHIBA INU",
            "regex": "^(0x)[0-9A-Fa-f]{40}$",
            "regex_memo": null,
            "top": 1,
            "logo": "https://www.unmineable.com/img/logos/SHIB.png"
        },
        (...)
    ]
}
```

### GET  /coin/:coin

Returns the details of a specific coin.

Input example: 

```
GET https://api.unminable.com/v4/coin/DOGE
```

Output example:

```
{
    "success": true,
    "msg": "Ok",
    "data": {
        "symbol": "DOGE", → Coin symbol
        "network": "DOGE", → Coin network (supported network)
        "token_standard": null, → Token type, if the coin is a token in a network.
        "name": "Dogecoin", → Coin / Token name
        "regex": "^(D|A|9)[a-km-zA-HJ-NP-Z1-9]{33,34}$", → Validation RegExp (for address)
        "regex_memo": null, → Validation RegExp (for memo / payment tag)
        "payment_threshold": 30, → Coin payment threshold
        "payment_enabled": true, → Indicates whether payments are enabled (true) or under maintenance (false)
        "logo": "https://www.unmineable.com/img/logos/DOGE.png"
    }
}
```

### GET  /address/:address?coin=:coin

Returns general stats (balance related) for a specific **address** and **coin**. This endpoint returns the **uuid** field which is used for every following endpoint.
For addresses that require memo / payment tags, the tag must be added after the **address** separated by a **:** symbol, for example: A_VALID_XRP_ADDRESS:PAYMENT_TAG

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
     "precision": "8",  →  Balance decimal positions
     "payment_threshold": "15",  →  Current payment threshold.
     "mining_fee": "0.75",   →  Can also be: "1", if no referral code is being used.
     "auto": false,  →  Indicates whether auto payment is on (true) or off (false) for the address.
     "enabled": true, →  Indicates whether payments are enabled (true) or under maintenance (false).
     "enabled_auto_only": false, →  Indicates whether only auto payments are enabled (true) or not (false), while payments are under maintenance (enabled = false).
     "uuid": "22d53b0b-24a2-4c1d-9f56-175d3a2e", →  Required for following requests.
     "fresh": false, →  Indicates if an address is new (true if haven't mined before, false if  it has had an online worker at some point)
     "address": "A_VALID_TRX_ADDRESS", →  The requested address (exactly as on our database)
     "memo": "A_MEMO_VALUE", →  The requested memo (exactly as on our database)
     "err_flags": {
        "payment_error": false, →  Indicates whether the address has a payment error, usually when the system is unable to pay the funds due to an invalid address error.
        "missing_memo": false, →  Indicates whether the address requires a memo to get paid correctly (usually when mining XRP or similar coins to an exchange).
        "not_activated": false, →  Indicates whether the address isn't activated (for XRP addresses that require activation to get paid).
        "restricted": false →  Indicates whether the address is restricted to use the platform (botnets or similar suspected activities)
     }
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
     "precision": "8",  →  Balance decimal positions
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
