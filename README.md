#  JetBit Public API Documents

## Table of Contents
* [Base URL](#base-url)
* [Constructing the request](#constructing-the-request)
* [API Documents](#api-documents)
* [Error Messages](#error-messages)

## Base URL
* The base URL is 

## Constructing the request
### GET/POST request
* GET request require parameters as query string in the URL (e.g. ?limit=10&asset=btc).
* POST request require JSON payload ( application/json )

### Request Headers
Authentication requires `API KEY` and `API SECRET`. Every request to the server must contain the following in the request header.
* Accept : application/json
* Content-type : application/json
* x-jbt-key : `API KEY` 

### Signature ( POST )
Generate the signature from the JSON payload using HMAC `SHA-256` with `base64`. Use the `API SECRET` as the `secret`. Timestamp include milliseconds.

#### Example payload
 ``` js
{ 
    “ts” : "1693290930000" 
}
 ```

#### Example payload with signature
``` js
{
    “ts” : “1693290930000”,
    “signature” : “503380b8cf5f64755308621263df1d4b459cb0e4be3e1e6804676f99a4b28e21"
}
```

### How to create signature
example with javascript
``` js 
const secret = {{ API SECRET }} // Your Secret Key
const body_str = JSON.stringify(req_body) // JSON to Object String

const signature = createHmac( secret , 'sha256' ).update( body_str ).digest( 'base64' )
```

#### Example cURL
``` js
curl -X POST \
{{url}} /api/user/account \
-H 'accept: application/json' \
-H 'content-type: application/json' \
-H 'x-jbt-key: {{ YOUR_API_KEY }}' \
-d '{“ts” : “1693290930000”,“signature” : “503380b8cf5f64755308621263df1d4b459cb0e4be3e1e6804676f99a4b28e21"}'
```

### !! WARNING !!
Please check the `Permissions` before calling the API.

# API Documents

### Endpoints
#### GET
* [GET /v1/status](#get-hostv1status)
* [GET /v1/server-time](#get-hostv1server-time)
* [GET /v1/asset-statuses](#get-hostv1asset-statuses)
* [GET /v1/user-account/info](#get-hostv1user-accountinfo)
* [GET /v1/spot-user-assets](#get-hostv1spot-user-assets)
* [GET /v1/depth/:asset](#get-hostv1depth)
* [GET /v1/data-feed/:asset](#get-hostv1data-feedasset)
* [GET /v1/trade/info/:asset](#get-hostv1tradeinfoasset)
* [GET /v1/spot-order/transaction/asset-list](#get-hostv1spot-ordertransactionasset-list)
* [GET /v1/spot-order/transaction/pending-waiting](#get-hostv1spot-ordertransactionpending-waiting)
* [GET /v1/spot-order/transaction/finished-canceled](#get-hostv1spot-ordertransactionfinished-canceled)
* [GET /v1/spot-order/transaction/trade-history](#get-hostv1spot-ordertransactiontrade-history)

#### POST
* [POST /v1/process-order-market-buy](#post-hostv1process-order-market-buy)
* [POST /v1/process-order-market-sell](#post-hostv1process-order-market-sell)
* [POST /v1/process-order-limit-buy](#post-hostv1process-order-limit-buy)
* [POST /v1/process-order-limit-sell](#post-hostv1process-order-limit-sell)
* [POST /v1/process-order-stop-buy](#post-hostv1process-order-stop-buy)
* [POST /v1/process-order-stop-sell](#post-hostv1process-order-stop-sell)
* [POST /v1/cancel-order](#post-hostv1cancel-order)
### Response form server
Every response have `code` and `message`. And `response` is data response. If code isn't **200**, `response` will not return. Please read `message`.
``` js 
{
    "code": 200,
    "message": "success"
}

{
    "code": 200,
    "message": "success",
    "response": {
        // ...data //
    }
}
```


### GET {host}/v1/status
#### Description
Check server status. When status `code` is `not` **200**, Server is not ready.

#### Response
``` js 
{
    "code": 200,
    "message": "success"
}
```

### GET {host}/v1/server-time
#### Description
Get server timestamp.

#### Response
``` js 
{
    "code": 200,
    "message": "success",
    "response": {
        "timeStamp": 1695194435021
    }
}
```

### GET {host}/v1/asset-statuses
#### Description
Get asset statuses.

#### Response
``` js 
{
    "code": 200,
    "message": "success",
    "response": {
        "assetStatuses": [
            {
                "minimumBuyInFiat": 10,
                "minimumSellInFiat": 10,
                "minimumBuyAmount": 0.25,
                "minimumSellAmount": 0.25,
                "asset": "ada",
                "name": "Cardano",
                "price": 40,
                "open": 0,
                "assetImageUrl": "/ada.png",
                "isMemo": false,
                "high": 44,
                "low": 0,
                "volume": 668.28,
                "change": 0,
                "graph": [
                    19,
                    19,
                    19,
                    19,
                    19,
                    19,
                    19,
                    19,
                    19,
                    19,
                ],
                "pricePrecision": 2
            },
        ]
    }
}
```

### GET {host}/v1/user-account/info
#### Description
Get user account info.

#### Response
``` js 
{
    "code": 200,
    "message": "success",
    "response": {
        "kycFullName": "John Charter",
        "investmentVerifyStatus": "not_verify",
        "userId": "c9d75b58-xxxx-xxxx-xxxx-a64ed49f5ac1",
        "email": "johnCharter@xxx.com",
        "regionCode": "EN",
        "phone": "36***12",
        "phoneCountryCode": "1",
        "firstTimeDeposit": "not_verify",
        "firstTimeCoinDeposit": "not_verify",
        "level": 2,
        "firstTimeOpen2faEmail": true,
        "firstTimeOpen2faPhone": false,
        "firstTimeOpen2faGoogleAuth": true,
        "acceptVerifyPhone": true,
        "acceptVerifyEmail": true,
        "acceptVerifyGoogleAuth": false,
        "maxWithdrawBtc": 3,
        "maxDepositBtc": 100,
        "fee": 0.25,
        "provider": "email",
        "currentTermAndConditionVersion": 1,
        "acceptTermAndConditionVersion": null,
        "isBlock": null,
        "isBlockKyc": false,
        "loginLogs" : []
    }
}
```

### GET {host}/v1/spot-user-assets
#### Description
Get user assets and asset summary.

#### Response
``` js 
{
    "code": 200,
    "message": "success",
    "response": {
        "assetSummary": {
            "totalValueInUsd": 9922.4532,
            "totalValueInBtc": 198.449064,
            "lastDayPnl": 9922.4532,
            "lastDayPnlPercentage": 100
        },
        "assetList": [
            {
                "assetImageUrl": "usdt.png",
                "assetTitle": "Tether (USDT)",
                "assetSymbol": "usdt",
                "assetName": "Tether",
                "total": 10022.68,
                "totalValue": 9922.4532,
                "available": 10022.68,
                "inOrder": 0,
                "inWithdraw": 0
            },
        ]
    }
}
```

### GET {host}/v1/data-feed/:asset
#### Description
Get data feed by asset.

#### Params
Name | Required | Type
:----: | :----:| :----:
asset | yes | STRING 

#### Query
Name | Required | Type
:----: | :----:| :----:
timeFrame | yes | STRING 
unixStartDate | yes | TIMESTAMP 
unixEndDate | yes | TIMESTAMP 
* timeFrame: such as M1, M5, M15, M30, H1, H4, D1, W1, MM1

#### Response
``` js 
{
    "code": 200,
    "status": "success",
    "response": {
        "bids": [
            [
                217,
                17.04
            ],
            [
                216.9,
                28.05
            ],
            [
                216.8,
                27.2
            ],
            [
                216.7,
                22.15
            ]
        ],
        "asks": [
            [
                217.1,
                88.72
            ],
            [
                217.2,
                253.15
            ],
            [
                217.3,
                199.88
            ],
            [
                217.4,
                215.98
            ]
        ]
    }
}
```

### GET {host}/v1/depth/:asset
#### Description
Get depth chart by asset.

#### Params
Name | Required | Type
:----: | :----:| :----:
asset | yes | STRING 

#### Response
``` js 
{
    "code": 200,
    "status": "success",
    "response": {
        "bids": [
            [
                217,
                17.04
            ],
            [
                216.9,
                28.05
            ],
            [
                216.8,
                27.2
            ],
            [
                216.7,
                22.15
            ]
        ],
        "asks": [
            [
                217.1,
                88.72
            ],
            [
                217.2,
                253.15
            ],
            [
                217.3,
                199.88
            ],
            [
                217.4,
                215.98
            ]
        ]
    }
}
```

### GET {host}/v1/trade/info/:asset
#### Description
Get trade info by asset.

#### Params
Name | Required | Type
:----: | :----:| :----:
asset | yes | STRING 

#### Response
``` js 
{
    "code": 200,
    "message": "success",
    "response": {
        "asset": "bnb",
        "assetName": "Binance Coin",
        "currentAssetAmount": 27.95,
        "currentUsdtAmount": 61742.23,
        "currentAssetAmountInUsd": 7316.192,
        "minimumBuy": 500,
        "minimumSell": 500,
        "fee": 0.0025,
        "pricePrecision": 2
    }
}
```

### GET {host}/v1/spot-order/transaction/asset-list
#### Description
Get asset list.

#### Query
Name | Required | Type
:----: | :----:| :----:
isListed | yes | INTEGER

#### Response
``` js 
{
    "code": 200,
    "message": "success",
    "response": [
        {
            "asset": "ada",
            "name": "Cardano"
        },
        {
            "asset": "bch",
            "name": "Bitcoin Cash"
        },
        {
            "asset": "bnb",
            "name": "Binance Coin"
        },
        {
            "asset": "ltc",
            "name": "Litecoin"
        }
    ]
}
```

### GET {host}/v1/spot-order/transaction/pending-waiting
#### Description
Get transaction pending and waiting.

#### Query
Name | Required | Type | Default
:----: | :----:| :----: | :----:
asset | yes | STRING  | - 
limit | no  | INTEGER | 10
page |  no | INTEGER | 1

#### Response
``` js 
{
    "code": 200,
    "message": "success",
    "response": {
        "orderHistory": [
            {
                "orderId": "OD-BUY-MKT-ADA-KSURJ2J63Z",
                "asset": "ada",
                "orderType": "market",
                "triggerCondition": null,
                "baseAssetAmount": 5000, // fiatAmount
                "remainingBaseAssetAmount": 5000, // remainingFiatAmount
                "assetAmount": 0,
                "remainingAssetAmount": 0,
                "orderPrice": null,
                "fee": 0.0025,
                "side": "buy",
                "orderStatus": "0",
                "createdAt": "2023-05-23T06:12:26.000Z",
                "updatedAt": "2023-05-23T06:12:26.000Z",
                "openPrice": 70
            }
        ],
        "pagination": {
            "totalRows": 1,
            "currentPage": 1,
            "totalPages": 1,
            "limit": 1,
        }
    }
}
```

### GET {host}/v1/spot-order/transaction/finished-canceled
#### Description
Get transaction finished and canceled.

#### Query
Name | Required | Type| Default
:----: | :----:| :----:| :----:
asset | yes | STRING | -
limit | no  | INTEGER | 10
page |  no | INTEGER | 1

#### Response
``` js 
{
    "code": 200,
    "message": "success",
    "response": {
        "orderHistory": [
            {
                "orderId": "OD-BUY-STP-BCH-R05CPIDUIB",
                "asset": "bch",
                "orderType": "stop",
                "side": "buy",
                "baseAssetAmount": 10, // usdt ที่ซื้อ
                "remainingAssetAmount": 0,
                "remainingBaseAssetAmount": 10, // usdt ที่กำลัง match
                "assetAmount": 0.08312551,
                "orderPrice": 120,
                "openPrice": 90,
                "triggerCondition": 100,
                "fee": 0.0025,
                "orderStatus": "300",
                "createdAt": "2023-05-23T06:15:26.000Z",
                "updatedAt": "2023-05-23T06:15:26.000Z",
                "matchedFeeAmount": 1.37,
                "matchedBaseAsset": null,
                "matchedAsset": null,
                "averageOrderPrice": null
            },
            {
                "orderId": "OD-BUY-STP-BCH-MU83UG3RCZ",
                "asset": "bch",
                "orderType": "stop",
                "side": "buy",
                "baseAssetAmount": 10,
                "remainingAssetAmount": 0,
                "remainingBaseAssetAmount": 10,
                "assetAmount": 0.08312551,
                "orderPrice": 120,
                "openPrice": 90,
                "triggerCondition": 100,
                "fee": 0.0025,
                "orderStatus": "300",
                "createdAt": "2023-05-23T06:15:25.000Z",
                "updatedAt": "2023-05-23T06:15:25.000Z",
                "matchedFeeAmount": 1.37,
                "matchedBaseAsset": null,
                "matchedAsset": null,
                "averageOrderPrice": null
            }
        ],
        "pagination": {
            "totalRows": 6,
            "currentPage": 1,
            "totalPages": 3,
            "limit": 2,
        }
    }
}
```

### GET {host}/v1/spot-order/transaction/trade-history
#### Description
Get trade history.

#### Query
Name | Required | Type | Default
:----: | :----:| :----: | :----:
asset | yes | STRING  | - 
side | no | STRING | All
limit | no  | INTEGER | 10
page |  no | INTEGER  | 1

#### Response
``` js 
{
    "code": 200,
    "message": "success",
    "response": {
        "orderHistory": [
            {
                "orderId": "OD-BUY-MKT-BTC-4SYRBB6G84",
                "asset": "btc",
                "orderType": "market",
                "side": "buy",
                "baseAssetAmount": 0,
                "remainingAssetAmount": 0,
                "remainingBaseAssetAmount": 500,
                "assetAmount": 0,
                "orderPrice": null,
                "openPrice": 30000,
                "triggerCondition": null,
                "fee": 0.0025,
                "feeAmount": 1.27,
                "orderStatus": "300",
                "createdAt": "2023-05-23T06:12:42.000Z",
                "updatedAt": "2023-05-23T06:12:42.000Z"
            }
        ],
        "pagination": {
            "totalRows": 1,
            "currentPage": 1,
            "totalPages": 1,
            "limit": 2,
        }
    }
}
```

### POST {host}/v1/process-order-market-buy
#### Description
Open order market buy.

#### Body
Name | Required | Type
:----: | :----:| :----:
asset | yes | STRING 
usdtAmount | yes | DOUBLE

#### Response
``` js 
{
    "code": 200,
    "message": "success"
}
```

### POST {host}/v1/process-order-market-sell
#### Description
Open order market sell.

#### Body
Name | Required | Type
:----: | :----:| :----:
asset | yes | STRING 
assetAmount | yes | DOUBLE

#### Response
``` js 
{
    "code": 200,
    "message": "success"
}
```

### POST {host}/v1/process-order-limit-buy
#### Description
Open order limit buy.

#### Body
Name | Required | Type
:----: | :----:| :----:
asset | yes | STRING 
usdtAmount | yes | DOUBLE
orderPrice | yes | DOUBLE

#### Response
``` js 
{
    "code": 200,
    "message": "success"
}
```

### POST {host}/v1/process-order-limit-sell
#### Description
Open order limit sell.

#### Body
Name | Required | Type
:----: | :----:| :----:
asset | yes | STRING 
assetAmount | yes | DOUBLE
orderPrice | yes | DOUBLE

#### Response
``` js 
{
    "code": 200,
    "message": "success"
}
```

### POST {host}/v1/process-order-stop-buy
#### Description
Open order stop limit buy.

#### Body
Name | Required | Type
:----: | :----:| :----:
asset | yes | STRING 
usdtAmount | yes | DOUBLE
orderPrice | yes | DOUBLE
triggerPrice | yes | DOUBLE

#### Response
``` js 
{
    "code": 200,
    "message": "success"
}
```

### POST {host}/v1/process-order-stop-sell
#### Description
Open order stop limit sell.

#### Body
Name | Required | Type
:----: | :----:| :----:
asset | yes | STRING 
assetAmount | yes | DOUBLE
orderPrice | yes | DOUBLE
triggerPrice | yes | DOUBLE

#### Response
``` js 
{
    "code": 200,
    "message": "success"
}
```

### POST {host}/v1/cancel-order
#### Description
cancel order.

#### Body
Name | Required | Type
:----: | :----:| :----:
orderId | yes | STRING 

#### Response
``` js 
{
    "code": 200,
    "message": "success"
}
```

# Error Messages
Every error response must have status code 400.
Message | Description
:----- | :-------
bad_request | Invalid parameter,body or query.
invalid_time_stamp | Invalid time stamp.
invalid_signature | Invalid signature.
invalid_minimum_amount_to_buy | Invalid minimum amount to buy.
invalid_minimum_amount_to_sell | Invalid minimum amount to sell.
usdt_balance_insufficient | USDT balance insufficient.
asset_balance_insufficient | Asset balance insufficient.
order_price_is_more_than_price_impact | Order price is more than price impact.
cannot_cancel_finished_order | Cannot cancel finished order.
exceed_max_limit_order | Exceed max limit order.
exceed_max_limit_asset | Exceed max limit asset.
sell_market_impact | Sell market impact.
buy_market_impact | Buy market impact.
not_allow_to_trade | Not allow to trade.
user_not_found | User not found.
access_token_not_found | Access token(x-jbt-key) not found.
public_api_key_not_found | Public api key not found.
public_api_key_permission_denied | Public api key had no permission.
