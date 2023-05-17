# Menu

- [HTTP Protocol API](#http-protocol-api)
  - [HTTP Authorization](#http-authorization)
  - [Return Codes of Errors](#return-codes-of-errors)
  - [Public Data Methods](#public-methods)
  - [Private Data Methods](#private-data-methods)
- [WebSoket Protocol API](#websoket-protocol-api)
  - [Web-Soket Authorization](#web-soket-authorization)
  - [Basic WS structure](#basic-ws-structure)
  - [PING-PONG Method](#ping-pong-method)
  - [System Time Method](#system-time-method)

## HTTP Protocol API
<details open>
  <summary>
  </summary>

  ### Detailed information
  <details open>
  <summary> </summary>

  **Public Methods:**

  This method provides information via `GET`. The response will return all the information that was posted by the platform. To obtain private information, use the same methods via `POST` as an authorized user using api keys.

  - [List of Public Pairs](#list-of-public-pairs) - returns the history of trades for all public pairs - used for tracking to compare prices in the market, control positions for many markets at once.
  - [List of Public Tickers](#list-of-public-tickers) - That method returns the trading history for a all pair - used to track a specific pair and track its key characteristics.
  - [Specific Public Ticker Data](#specific-public-ticker-data) - returns the trading history for a specific selected pair - used to track a specific pair and track its key characteristics.
  - [List of Order Book](#list-of-order-book) - Returns all positions for a specific market for all orders of the order book in the selected direction with pagination - used for full monitoring of the order book status, its changes, evaluation of its placed orders and their priority.
  - [Market History](#market-history) - That method returns all positions for a specific market for all orders of the order book in the selected direction with pagination.
  - [Market History Data](#market-history-data) - returns the history of trading on the market - used to track your own or someone else's executed orders, trading dynamics, control over buying / selling.
  - [Public Products List](#public-products-list) - returns all public pairs - used to track new pairs, monitor pairs for MM.
  - [Public Symbols List](#public-products-list) - returns all public pairs - used to track new pairs, monitor pairs for MM.
  - [Depth List](#depth-list) - returns data on the order book with pagination for a specific pair - used to track the depth of the order book, control placed orders, monitor the market in light mode.
  - [List of Graphic Data KLine](#list-of-graphic-data-kline) - returns data on charts - is used to create personal charts, control market trends.

  **Public Methods Via `POST`:**

  First of all to use `POST` methods check how to made [HTTP Authorization](#http-authorization)
  Use next methods via `POST` and obtain full information:

  - [List of Public Pairs](#list-of-public-pairs)
  - [Specific Public Ticker Data](#specific-public-ticker-data)
  - [List of Order Book](#list-of-order-book)
  - [Market History](#market-history)
  - [Market History Data](#market-history-data)
  - [Public Products List](#public-products-list)
  - [Public Symbols List](#public-symbols-list)
  - [Depth List](#depth-list)
  - [List of Graphic Data KLine](#list-of-graphic-data-kline)

  **Private Methods:**

  All of this methods can be use only with `POST`. Before using check [HTTP Authorization](#http-authorization)

  - [Create new Limit Order](#create-new-limit-order) - used to create a Limit Order with the specified parameters, if successful, returns information about the placed order.
  - [Cancel Order](#cancel-order) - used to close the placed Limit Order with the ID of the placed order, if successful, returns information about the closed order.
  - [My Active Orders](#my-active-orders) - used to obtain information on the user's active orders, if successful, it returns a list of active orders.
  - [My All Trade Balances](#my-all-trade-balances) - returns the entire list of user balances - used to control all balances.
  - [My Specific Trade Balance](#my-specific-trade-balance) - returns the balance for the specified coin - used to control a specific coin, as a faster alternative to the first method.
  - [Get My Order Info](#get-my-order-info) - returns information about a specific order of a specific user - used to track the status of an order.
  - [My Order History](#my-order-history)- returns the history of orders, it is used to generate reports, control orders and executed orders.

  </details>

  ### HTTP Authorization
  <details open>
  <summary> </summary>
  Follow 5 simple steps to use private methods:

  * Before authorization, you need to log in to https://pointpay.io/login or register if the user has not yet been created https://pointpay.io/register.
  * After authorization, you need to go to https://pointpay.io/user/settings/2fa and activate 2-factor authentication.
  * After enabling 2-factor authentication, you need to go to the API manager to create API keys that will be required for authorization in the API https://pointpay.io/user/api.
  * To create keys, click Add Key and the 1st package of keys for connecting the API will be created. Key packages can be up to 5 pieces.
  * Before using the apkey, they must be activated - for this, on the created package, click on the switch which will switch them to the "Active" state.


  Each package will contain 3 keys:

  - ***apiKey*** - public key

  - ***apiSecret*** - private key

  - ***weKey*** - key for websockets


  You need to fill in the header parameters, see examples.

  ```
  Important!
  nonce - used as a parameter to protect against DDoS attacks and an excessive number of API requests. This parameter is most often used through a timestamp with a minimum step per second. Each next request must be greater than the next one in the 'nonce' parameters.
  ```

  Let's look at a few examples of authorization in different development languages:

  #### PHP
  <details  open>
    <summary> </summary>

  ```javascript
  use GuzzleHttp\Client;

  public function callApi()
  {
      $apiKey = '';
      $apiSecret = '';
  		$request = ''; // /api/v1/order/new
      $baseUrl = 'https://api.exchange.pointpay.io//';

      $data = [
          'request' => $request,
          'nonce' => (string)\Carbon\Carbon::now()->timestamp,
      ];

      $completeUrl = $baseUrl . $request;
      $dataJsonStr = json_encode($data, JSON_UNESCAPED_SLASHES);
      $payload = base64_encode($dataJsonStr);
      $signature = hash_hmac('sha512', $payload, $apiSecret);

      $client = new Client();
      try {
          $res = $client->post($completeUrl, [
              'headers' => [
                  'Content-type' => 'application/json',
                  'X-TXC-APIKEY' => $apiKey,
                  'X-TXC-PAYLOAD' => $payload,
                  'X-TXC-SIGNATURE' => $signature
              ],
              'body' => json_encode($data, JSON_UNESCAPED_SLASHES)
          ]);
      } catch (\Exception $e) {
          return response()->json(['error' => $e->getMessage()]);
      }

      return response()->json(['result' => json_decode($res->getBody()->getContents())]);
  }
  ```
  </details>
	  
	  
</details>


### Return Codes of Errors
  <details open>
    <summary> </summary>

  #### Return Codes

  ```java
   HTTP `200` return code is used when the Succesful responce;
   HTTP `401` return code is used when the request made by Unauthorized user;
   HTTP `403` return code is used when the request made to Forbidden data from user without necessary permissions;
   HTTP `404` return code is used when the request made with undefined data and service can Not Found data for response.
  ```
  #### Errors messages:
  **"Invalid payload"** - this error occurs when the data that was provided in the body of the request doesn't match the base64-decoded payload.
  ```java
  {
       "message": [
           [
               "Invalid payload."
           ]
       ],
       "result": [],
       "success": false
  }
  ```
  **"Nonce not provided."** - this error occurs if your request is missing "nonce" in the request body.
  ```java
  {
      "message": [
          [
              "Nonce not provided."
          ]
      ],
      "result": [],
      "success": false
  }
  ```
  **"Request not provided."** - this error occurs if your request is missing "request" path in the request body.
  ```java
  {
      "message": [
          [
              "Request not provided."
          ]
      ],
      "result": [],
      "success": false
  }
  ```
  **"Unauthorized request."** - this error occurs if the request was signed incorrectly.
  ```java
  {
      "message": [
          [
              "Unauthorized request."
          ]
      ],
      "result": [],
      "success": false
  }
  ```
  **"This action is unauthorized. Enable your key in API settings"** - this error occurs when you are using disabled API key.
  ```java
  {
      "message": [
          [
              "This action is unauthorized. Enable your key in API settings"
          ]
      ],
      "result": [],
      "success": false
  }
  ```
  **"Invalid payload"** - this error occurs when the data that was provided in the body of the request doesn't match the base64-decoded payload.
  ```java
  {
       "message": [
           [
               "Invalid payload."
           ]
       ],
       "result": [],
       "success": false
  }
  ```
  </details>

---

### Public Methods
<details open>
  <summary>
These methods provide information via GET.
The response will return all the information that was posted by the platform.
To obtain private information, use the same methods via POST as an authorized user using api keys.
  </summary>

#### List of Public Pairs
  <details open>
  <summary>
  This method provides information via GET
  </summary>

  ```java
  GET /api/v1/public/markets
  ```

  ```java
  curl -X GET "https://api.exchange.pointpay.io/api/v1/public/markets" -H "accept: application/json"
  ```

**Response Parameters:**

Name | Type |
------------ | ------------
name | STRING |
moneyPrec | NUMERIC |
stock | STRING | NO |
money | STRING |
stockPrec | NUMERIC |
feePrec | NUMERIC |
minAmount | STRING |

**Response samples:**
```javascript
{
  "code": 0,
  "message": {},
  "result":
  [
    {
      "name":"BCH_BTC",
      "moneyPrec":8,
      "stock":"BCH",
      "money":"BTC",
      "stockPrec":8,
      "feePrec":8,
      "minAmount":"0.001"
    },
    {
      ...
    },
  ],
}
```
  </details>

#### List of Public Tickers
<details open>
  <summary>
  This method provides information via GET
  </summary>

```java
GET /api/v1/public/tickers
```

```java
curl -X GET "https://api.exchange.pointpay.io/api/v1/public/tickers" -H "accept: application/json"
```

**Response Parameters:**

Name | Type |
---- | ---- |
name | STRING |
bid | STRING |
ask | STRING |
open | STRING |
high | STRING |
low | STRING |
last | STRING |
volume | STRING |
deal | STRING |
change | STRING |

**Response samples:**

```javascript
{
    "code": 200,
    "success": true,
    "message": "",
    "result": {
        "ETH_UAH": {
            "at": 1650542464,
            "ticker": {
                "name": "ETH_UAH",
                "bid": "101275.50569145",
                "ask": "101564.13835842",
                "open": "100285.81312762",
                "high": "101579.95341111",
                "low": "97265.91569436",
                "last": "101335.47031808",
                "vol": "13.78367282",
                "deal": "1365777.629861316184",
                "change": "1"
            }
        }
    }
}
```

</details>


#### Specific Public Ticker Data
  <details open>
  <summary>
  This method provides information via GET
  </summary>

```java
GET /api/v1/public/ticker
```
```java
curl -X GET "https://api.exchange.pointpay.io/api/v1/public/ticker?market=BTC_USDT" -H "accept: application/json"
```

**Request Parameters:**

Name | Type | Mandatory | Description |
------------ | ------------ | ------------ | ------------ |
market | STRING | YES | Any Public Market

**Response Parameters:**

Name | Type |
---- | ---- |
name | STRING |
bid | STRING |
ask | STRING |
open | STRING |
high | STRING |
low | STRING |
last | STRING |
volume | STRING |
deal | STRING |
change | STRING |

**Response samples:**
```javascript
{
  "code": 0,
  "message": {},
  "result":
  [
    {
      "name":"ETH_BTC",
      "bid":"0.05075249",
      "ask":"0.05075451",
      "open":"0.04799996",
      "high":"0.05086171",
      "low":"0.047891",
      "last":"0.05068996",
      "volume":"71.7808779340174791",
      "deal":"1446.73007651",
      "change":"5"
    },
  ],
}
```
  </details>


#### List of Order Book
<details open>
  <summary>
  This method provides information via GET and POST
  </summary>

```java
GET /api/v1/public/book
```
```java
curl -X GET "https://api.exchange.pointpay.io/api/v1/public/book?market=BTC_USDT&side=sell&offset=0&limit=1" -H "accept: application/json"
```

**Request Parameters:**

Name | Type | Mandatory | Description |
------------ | ------------ | ------------ | ------------ |
market | STRING | YES | Any Public Market
side | STRING | YES |  buy / sell
offset | NUMERIC | NO | Default 0
limit | NUMERIC | NO | Default 50; min 1; max 1000


**Request in body POST method:**

```java
{
  "market": "ETH_BTC"
  "side": "sell"
  "offset": 0
  "limit": 100
  "request": "/api/v1/public/book",
  "nonce": 1636733702330
}
```

**Response Parameters:**

Name | Type |
------------ | ------------
id | NUMERIC |
market | STRING |
price | STRING |
amount | STRING |
left | STRING |
type | STRING |
side | STRING |
timestamp | NUMERIC |
dealFee | STRING |
takerFee | STRING |
dealStock | STRING |
dealMoney | STRING |

**Response samples:**
```javascript
{
"code":200,
"success":true,
"message":"",
"result":
   {
    "offset":0,
    "limit":1,
    "total":337,
    "orders":
    [
      {
        "id":324101293,
        "market":"ETH_BTC",
        "price":"0.05065051",
        "amount":"1.462",
        "left":"1.462",
        "type":"limit",
        "side":"sell",
        "timestamp":1619691495.549000,
        "dealFee":"0",
        "takerFee":"0",
        "makerFee":"0",
        "dealStock":"0",
        "dealMoney":"0"
      },
      {
        ...
      },
    ],
  },
}
```
</details>

#### Market History
<details open>
  <summary>
  This method provides information via GET and POST
  </summary>

```java
GET /api/v1/public/history
```
```java
curl -X GET "https://api.exchange.pointpay.io/api/v1/public/history?market=BTC_USDT&lastId=1&limit=10'" -H "accept: application/json"
```

**Request Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
market | STRING | YES | Any Public Market
lastId | NUMERIC | YES |  last Id
limit | NUMERIC | NO | Default 50; min 1; max 1000


**Request in body POST method:**
```javascript
{
  "market":"BTC_USDT",
  "lastId":1,
  "limit":10,
  "request":"/api/v1/public/history",
  "nonce":1650541053626
}
```

**Response samples:**
```javascript
    "response": {
        "success": true,
        "message": "",
        "result": [
            {
                "id": 265,
                "time": 1533292963.202649,
                "price": "2",
                "type": "sell",
                "amount": "1"
            },
            {
                "id": 263,
                "time": 1533292880.088096,
                "price": "2",
                "type": "sell",
                "amount": "1"
            },
						{...}
    ]
}
```

</details>

#### Market History Data
<details open>
  <summary>
  This method provides information via GET and POST
  </summary>

```java
GET /api/v1/public/history/result
```
```java
curl -X GET 'https://api.exchange.pointpay.io/api/v1/public/history/result?market=BTC_USDT&since=1&limit=20' -H "accept: application/json"
```

**Request Parameters:**
Name | Type | Mandatory | Description |
------------ | ------------ | ------------ | ------------ |
market | STRING | YES | Any Public Market
since | NUMERIC | YES |  Min 1; Market History Since Selected `tid`
limit | NUMERIC | NO | Default 50; min 1; max 1000


**Request in body POST method:**
```java
{
  "market":"LTC_ETH",
  "since":1,
  "limit":20,
  "request":"/api/v1/public/history/result",
  "nonce":1650541233074
}
```

**Response Parameters:**
Name | Type |
------------ | ------------ |
tid | NUMERIC |
date | NUMERIC |
price | STRING |
type | STRING |
amount | STRING |
total | STRING |

**Response:**
```javascript
[
  {
    "tid":23914004,
    "date":1619693164,
    "price":"0.05065949",
    "type":"buy",
    "amount":"0.01854886",
    "total":"0.00093968"
  },
  {
   ...
  },
]
```

</details>

#### Public Products List
<details open>
  <summary>
  This method provides information via GET
  </summary>

```java
GET /api/v1/public/products
```
```java
curl -X GET "https://api.exchange.pointpay.io/api/v1/public/products" -H "accept: application/json"
```

**Response:**
```javascript
response: {
  success: true,
  message: "",
  result: [
			  {
          "id": "ETH_BTC",
          "fromSymbol": "ETH",
          "toSymbol": "BTC"
        },
        {
           "id": "OCC_BTC",
           "fromSymbol": "OCC",
           "toSymbol": "BTC"
        },
				{...}
	   ]
 }
```
</details>

#### Public Symbols List
<details open>
  <summary>
  This method provides information via GET and POST
  </summary>

```java
GET /api/v1/public/symbols
```
```java
curl -X GET "https://api.exchange.pointpay.io/api/v1/public/symbols" -H "accept: application/json"
```

**Response Parameters:**
Name | Type |
----- | ----- |
Pair | STRING |

Request in body POST method:
```java
{
  "request":"/api/v1/public/symbols",
  "nonce":1650541438785
}
```

**Response:**
```javascript
{
"code":200,
"success":true,
"message":"",
"result":
    [
      "AAVE_BTC",
      "BCH_BTC",
      "BCH_USD",
      "BNB_BTC",
      "BNB_ETH",
      "BNB_USD",
      "BNB_USDT",
    ]
}
```
</details>

#### Depth List
<details open>
  <summary>
  This method provides information via GET and POST
  </summary>

```java
GET /api/v1/public/depth/result
```
```java
curl -X GET "https://api.exchange.pointpay.io/api/v1/public/depth/result?market=BTC_USDT&limit=5" -H "accept: application/json"
```

**Request Parameters:**

Name | Type | Mandatory | Description |
------------ | ------------ | ------------ | ------------ |
market | STRING | YES | Any Public Market
limit | NUMERIC | NO | Default 50; min 1; max 1000


**Request in body POST method:**
```JSON
{
  "market": "ETH_BTC",
  "limit": 100,
  "request": "/api/v1/public/depth/result",
  "nonce": 1650541438785
}
```

**Response Parameters:**

Name | Type | Description
------------ | ------------ | ------------
type | STRING | asks (sell) / bids (buy)
price | STRING | 1st parametr - in left side currency
amount | STRING |2st parametr - in left side currency

**Response:**
```java
{
  "asks":
    [
      [
        "0.05084851", //price
        "21.437"      //amount
      ],
      [
        "0.05084951", //price
        "0.453"       //amount
      ]
    ],
  "bids":
    [
      [
        "0.05084649", //price
        "3.99"        //amount
      ],
      [
        "0.05084549", //price
        "18.15"       //amount
      ]
    ]
}
```

</details>

#### List of Graphic Data KLine
<details open>
  <summary>
  This method provides information via GET and POST
  </summary>

```java
GET /api/v1/public/kline
```
```java
curl -X GET "https://api.exchange.pointpay.io/api/v1/public/kline?market=BTC_USDT&start=1650210446&end=1650390446&interval=60" -H "accept: application/json"
```

**Request Parameters:**
Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
market | STRING | YES | Any Public Market
start | NUMERIC | YES |  unixtime from
end | NUMERIC | YES | unixtime to
interval | NUMERIC | NO | Default 60; min 60; max 604800 sec

**Request in body POST method:**

```javascript
{
  "market": "ETH_BTC",
  "start": 1601359005,
  "end": 1601389005,
  "interval": 60,
  "request": "/api/v1/public/kline",
  "nonce": 1650541438785
}
```

**Response Parameters:**

Name | Type |
------------ | ------------
time | NUMERIC |
open | STRING |
close | STRING |
highest | STRING |
lowest | STRING |
volume | STRING |
amount | STRING |
market | STRING |

**Response:**
```javascript
{
"code":200,
"success":true,
"message":"",
"result":
  {
    "market":"ETH_BTC",
    "start":1619695680,
    "end":1619699280,
    "interval":60,
    "kline":
      [
        {
          "time":1619695680,
          "open":"0.05068385",
          "close":"0.05068196",
          "highest":"0.05069765",
          "lowest":"0.0506626",
          "volume":"0.45754085",
          "amount":"0.0231873341977513",
          "market":"ETH_BTC"
        },
        {
          ...
        },
     ]
  }
}
```
</details>

</details>

---

### Private Data Methods
<details open>
<summary>
First of all to use POST methods check how to made HTTP Authorization
Use next methods via POST and obtain full information
All of this methods can be use only with POST.
</summary>

#### 1. Market API
<details open>
<summary> </summary>

  #### Create new Limit Order
  <details open>
  <summary>
  This method provides information via POST
  </summary>

  ```java
  POST /api/v1/order/new
  ```
  ```java
  curl --location --request POST 'https://api.exchange.pointpay.io/api/v1/order/new' \
--header 'Content-Type': 'application/json' \
--header 'X-TXC-APIKEY': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'X-TXC-PAYLOAD': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'X-TXC-SIGNATURE': '0000000000XXXXXXXXXXXXXXXXX' \
--data-raw '{"market":"BTC_USDT","lastId":1,"limit":10,"request":"/api/v1/public/history","nonce":1650473825200}'
```

**Request Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
request | STRING | YES | A request path without the domain name
market | STRING | YES | Any Market
side | STRING | YES |  sell or buy
amount | STRING | YES | Order amount in 1st Ticker of Pair
price | STRING | YES | Order price in 2st Ticker of Pair
nonce | STRING | YES | A number that is always greater than the previous request’s nonce number

**Request:**

```javascript
{
  "request": "/api/v1/order/new",
  "market": "ETH_BTC",
  "side" : "sell",
  "amount" : "0.1",
  "price" : "0.1",
  "nonce": 1636733702330
}
```

**Response Parameters:**

Name | Type  | Description
------------ | ------------ | ------------
orderId | STRING  | ID of placed order
market | STRING  |  Market of placed order
price | STRING  | Price of placed order
side | STRING  | Side of placed order
type | STRING  | Type of placed order
timestamp | NUMERIC  | Time of placed order
dealMoney | STRING  | Order Amount in 2st Ticker of Pair
dealStock | STRING  | Order Amount in 1st Ticker of Pair
amount | STRING  | Order Amount in 1st Ticker of Pair
takerFee | STRING  | Order Taker Fee
makerFee | STRING  | Order Maker Fee
left | STRING  | Order left amount - shows how much left in order; if = 0 - order finished
dealFee | STRING  | Deal Fee of Order


**Response:**
```javascript
{
    "success": true,
    "message": "",
    "result": [
        "orderId": 25749,
        "market": "ETH_BTC",
        "price": "0.1",
        "side": "sell",
        "type": "limit",
        "timestamp": 1537535284.828868,
        "dealMoney": "0",
        "dealStock": "0",
        "amount": "0.1",
        "takerFee": "0.002",
        "makerFee": "0.002",
        "left": "0.1",
        "dealFee": "0"
    ]
}
```

</details>

#### Cancel Order
<details open>
  <summary>
  This method provides information via POST
  </summary>

```java
POST /api/v1/order/cancel
```
```java
curl --location --request POST 'https://api.exchange.pointpay.io/api/v1/order/cancel' \
--header 'Content-Type': 'application/json' \
--header 'X-TXC-APIKEY': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'X-TXC-PAYLOAD': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'X-TXC-SIGNATURE': '0000000000XXXXXXXXXXXXXXXXX' \
--data-raw '{"market":"BTC_USDT","lastId":1,"limit":10,"request":"/api/v1/public/history","nonce":1650473825200}'
```

**Request Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
request | STRING | YES | A request path without the domain name
market | STRING | YES | Any Market
orderId | STRING | YES |  Order ID for canceling
nonce | STRING | YES | A number that is always greater than the previous request’s nonce number

**Request:**

```javascript
{
  "request": "/api/v1/order/cancel",
  "market": "ETH_BTC",
  "orderId": 25749,
  "nonce": 1636733702330
}
```

**Response Parameters:**

Name | Type  | Description
------------ | ------------ | ------------
orderId | STRING  | ID of placed order
market | STRING  |  Market of placed order
price | STRING  | Price of placed order
side | STRING  | Side of placed order
type | STRING  | Type of placed order
timestamp | NUMERIC  | Time of placed order
dealMoney | STRING  | Order Amount in 2st Ticker of Pair
dealStock | STRING  | Order Amount in 1st Ticker of Pair
amount | STRING  | Order Amount in 1st Ticker of Pair
takerFee | STRING  | Order Taker Fee
makerFee | STRING  | Order Maker Fee
left | STRING  | Order left amount - shows how much left in order; if = 0 - order finished
dealFee | STRING  | Deal Fee of Order


**Response:**
```javascript
{
    "success": true,
    "message": "",
    "result": [
        "orderId": 25749,
        "market": "ETH_BTC",
        "price": "0.1",
        "side": "sell",
        "type": "limit",
        "timestamp": 1537535284.828868,
        "dealMoney": "0",
        "dealStock": "0",
        "amount": "0.1",
        "takerFee": "0.002",
        "makerFee": "0.002",
        "left": "0.1",
        "dealFee": "0"
    ]
}
```

</details>

#### My Active Orders
  <details open>
  <summary>
  This method provides information via POST
  </summary>

```java
POST /api/v1/orders
```
```java
curl --location --request POST 'https://api.exchange.pointpay.io/api/v1/orders' \
--header 'X-TXC-APIKEY': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'X-TXC-PAYLOAD': '0000000000XXXXXXXXXXXXXXXXX==' \
--header 'X-TXC-SIGNATURE': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'Content-Type': 'application/json' \
--data-raw '{"market":"BTC_USDT","lastId":1,"limit":10,"request":"/api/v1/public/history","nonce":1650473825200}'
```

**Request Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
request | STRING | YES | A request path without the domain name
market | STRING | YES | Any Market
offset | STRING | NO |  Default: 0
limit | NUMERIC | NO |  Default: 50
nonce | STRING | YES | A number that is always greater than the previous request’s nonce number

**Request:**

```javascript
{
  "request": "/api/v1/orders",
  "market": "ETH_BTC",
  "offset": 10,
  "limit": 100,
  "nonce": 1636733702330
}
```

**Response Parameters:**

Name | Type  | Description
------------ | ------------ | ------------
orderId | STRING  | ID of placed order
market | STRING  |  Market of placed order
price | STRING  | Price of placed order
side | STRING  | Side of placed order
type | STRING  | Type of placed order
timestamp | NUMERIC  | Time of placed order
dealMoney | STRING  | Order Amount in 2st Ticker of Pair
dealStock | STRING  | Order Amount in 1st Ticker of Pair
amount | STRING  | Order Amount in 1st Ticker of Pair
takerFee | STRING  | Order Taker Fee
makerFee | STRING  | Order Maker Fee
left | STRING  | Order left amount - shows how much left in order; if = 0 - order finished
dealFee | STRING  | Deal Fee of Order

**Response:**
```javascript
{
    "success": true,
    "message": "",
    "result":
      {
          "limit": 100,
          "offset": 10,
          "total": 17,
          "result":
            [
	       {
                 "id": 9472,
                 "left": "1",
                 "market": "ETH_BTC",
                 "amount": "1",
                 "type": "limit" | "market",
                 "price": "0.01",
                 "timestamp": 1533561772.211871,
                 "side": "sell" | "buy",
                 "dealFee": "0",
                 "takerFee": "0",
                 "makerFee": "0",
                 "dealStock": "0",
                 "dealMoney": "0"
               },
               {
	         ...
	       },
            ]
      }
}
```
</details>

</details>

#### 2. Account API
<details open>
<summary>
</summary>

#### My All Trade Balances
<details open>
  <summary>
  This method provides information via POST
  </summary>

```java
POST /api/v1/account/balances
```
```java
curl --location --request POST 'https://api.exchange.pointpay.io/api/v1/account/balances' \
--header 'X-TXC-APIKEY': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'X-TXC-PAYLOAD': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'X-TXC-SIGNATURE': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'Content-Type': 'application/json' \
--data-raw '{"market":"BTC_USDT","lastId":1,"limit":10,"request":"/api/v1/public/history","nonce":1650473825200}'
```

**Request Parameters:**
Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
request | STRING | YES | A request path without the domain name
nonce | STRING | YES | A number that is always greater than the previous request’s nonce number

**Request:**
```javascript
{
  "request": "/api/v1/account/balances",
  "nonce": 1636733702330
}
```

**Response Parameters:**

Name | Type  | Description
------------ | ------------ | ------------
available | NUMERIC | Amount without active orders
freeze | STRING | active orders amount

**Response:**
```javascript
{
    "success": true,
    "message": "",
    "result":
       {
            "ATB":
	    	{
                   "available": "0",
                   "freeze": "0"
            	},
            "USD":
	    	{
                   "available": "8990",
                   "freeze": "0"
            	},
            	{
	           ...
	    	},
       }
}
```
</details>

#### My Specific Trade Balance
<details open>
  <summary>
  This method provides information via POST
  </summary>

```java
POST /api/v1/account/balance
```
```java
curl --location --request POST 'https://api.exchange.pointpay.io/api/v1/account/balance' \
--header 'X-TXC-APIKEY': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'X-TXC-PAYLOAD': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'X-TXC-SIGNATURE': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'Content-Type': 'application/json' \
--data-raw '{"market":"BTC_USDT","lastId":1,"limit":10,"request":"/api/v1/public/history","nonce":1650473825200}'
```

**Request Parameters:**
Name | Type  |
------------ | ------------
request | STRING | YES | A request path without the domain name
currency | STRING
nonce | STRING | YES | A number that is always greater than the previous request’s nonce number

**Request:**
```javascript
{
  "request": "/api/v1/account/balance",
  "currency": "ETH",
  "nonce": 1636733702330
}
```
**Response Parameters:**
Name | Type  | Description
------------ | ------------ | ------------
available | NUMERIC | Amount without active orders
freeze | STRING | active orders amount

**Response:**
```javascript
{
    "success": true,
    "message": "",
    "result":
    	{
            "available": "8990",
            "freeze": "0"
        }
}
```
</details>

#### Get My Order Info
<details open>
  <summary>
  This method provides information via POST
  </summary>

```java
POST /api/v1/account/order
```
```java
curl --location --request POST 'https://api.exchange.pointpay.io/api/v1/account/order' \
--header 'X-TXC-APIKEY': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'X-TXC-PAYLOAD': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'X-TXC-SIGNATURE': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'Content-Type': 'application/json' \
--data-raw '{"market":"BTC_USDT","lastId":1,"limit":10,"request":"/api/v1/public/history","nonce":1650473825200}'
```

**Request Parameters:**
Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
request | STRING | YES | A request path without the domain name
market | STRING | YES | Any Market
offset | STRING | NO |  Default: 0
limit | NUMERIC | NO |  Default: 50
nonce | STRING | YES | A number that is always greater than the previous request’s nonce number

**Request:**

```javascript
{
  "request": "/api/v1/account/order",
  "market": "ETH_BTC",
  "offset": 10,
  "limit": 100,
  "nonce": 1636733702330
}
```

**Response Parameters:**

Name | Type  | Description
------------ | ------------ | ------------
time | NUMERIC  | Trade Time
fee | STRING  | Trade Fee
price | STRING  | Trade Price
amount | STRING  | Trade amount in 1st Ticker of Pair
Id | NUMERIC  | User ID
dealOrderId | NUMERIC  | Trade ID
role | NUMERIC  | Trade Role: Taker or Maker
deal | STRING  | Trade amount in 2st Ticker of Pair.


**Response:**
```javascript
{
    "success": true,
    "message": "",
    "result":
        {
          "offset": 0,
          "limit": 50,
          "records":
            {
              "time": 1533310924.935978,
              "fee": "0",
              "price": "80.22761599",
              "amount": "2.12687945",
              "id": 548,
              "dealOrderId": 1237,
              "role": 1,
              "deal": "170.6344677716224055"
            }
        }
}
```
</details>

#### Get My Trades Info
<details open>
  <summary>
  This method provides information via POST
  </summary>

```java
POST /api/v1/account/trades
```
```java
curl --location --request POST 'https://api.exchange.pointpay.io/api/v1/account/trades' \
--header 'X-TXC-APIKEY': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'X-TXC-PAYLOAD': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'X-TXC-SIGNATURE': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'Content-Type': 'application/json' \
--data-raw '{"market":"BTC_USDT","lastId":1,"limit":10,"request":"/api/v1/public/history","nonce":1650473825200}'
```

**Request Parameters:**
Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
request | STRING | YES | A request path without the domain name
market | STRING | YES | Any Market
offset | STRING | NO |  Default: 0
limit | NUMERIC | NO |  Default: 50
nonce | STRING | YES | A number that is always greater than the previous request’s nonce number

**Request:**
```javascript
{
  "request": "/api/v1/account/order",
  "market": "ETH_BTC",
  "offset": 10,
  "limit": 100,
  "nonce": 1636733702330
}
```

**Response Parameters:**

Name | Type  | Description
------------ | ------------ | ------------
time | NUMERIC  | Trade Time
fee | STRING  | Trade Fee
price | STRING  | Trade Price
amount | STRING  | Trade amount in 1st Ticker of Pair
Id | NUMERIC  | User ID
dealOrderId | NUMERIC  | Trade ID
role | NUMERIC  | Trade Role: Taker or Maker
deal | STRING  | Trade amount in 2st Ticker of Pair.


**Response:**
```javascript
{
    "success": true,
    "message": "",
    "result":
        {
          "offset": 0,
          "limit": 50,
          "records":
            {
              "time": 1533310924.935978,
              "fee": "0",
              "price": "80.22761599",
              "amount": "2.12687945",
              "id": 548,
              "dealOrderId": 1237,
              "role": 1,
              "deal": "170.6344677716224055"
            }
        }
}
```
</details>

#### My Order History
<details open>
  <summary>
  This method provides information via POST
  </summary>

```java
POST /api/v1/account/order_history_list
```
```java
curl --location --request POST 'https://api.exchange.pointpay.io/api/v1/account/order_history' \
--header 'X-TXC-APIKEY': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'X-TXC-PAYLOAD': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'X-TXC-SIGNATURE': '0000000000XXXXXXXXXXXXXXXXX' \
--header 'Content-Type': 'application/json' \
--header 'Cookie': 'JSESSIONID=280523DF04A70E6B5' \
--data-raw '{"request":"/api/v1/account/order_history","offset":0,"limit":100,"nonce":1650536118818}'
```

**Request Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
request | STRING | YES | A request path without the domain name
market | STRING | YES | Any Market
offset | STRING | NO |  Default: 0
limit | NUMERIC | NO |  Default: 50
nonce | STRING | YES | A number that is always greater than the previous request’s nonce number

**Request:**

```javascript
{
  "request": "/api/v1/account/order_history_list",
  "market": "ETH_BTC",
  "offset": 10,
  "limit": 100,
  "nonce": 1636733702330
}
```

**Response Parameters:**

Name | Type  | Description
------------ | ------------ | ------------
amount | STRING  | Order Amount in 1st Ticker of Pair
price | STRING  | Price of placed order
type | STRING  | Type of placed order
Id | STRING  | ID of placed order
side | STRING  | Side of placed order
ctime | NUMERIC  | Time of create order
takerFee | STRING  | Order Taker Fee
ftime | NUMERIC  | Time of finishing order
market | STRING  |  Market of placed order
makerFee | STRING  | Order Maker Fee
dealFee | STRING  | Deal Fee of Order
dealStock | STRING  | Order Amount in 1st Ticker of Pair
dealMoney | STRING  | Order Amount in 2st Ticker of Pair
marketName | STRING  |  Market of placed order

**Response:**
```javascript
{
  {
    "success": true,
    "message": "",
    "result":
      {
        "records":
	  [
            {
                "amount": "1",
                "price": "0.01",
                "type": "limit"
                "id": 9740,
                "side": "sell"
                "ctime": 1533568890.583023,
                "takerFee": "0.002",
                "ftime": 1533630652.62185,
                "market": "ETH_BTC",
                "makerFee": "0.002",
                "dealFee": "0.002",
                "dealStock": "1",
                "dealMoney": "0.01",
                "marketName": "ETH_BTC"
            },
            {
	      ...
	    }
	  ]
  }
}
```

</details>
</details>

</details>
</details>

---

## WebSoket Protocol API
<details open>
  <summary>
  The API is based on JSON RPC of Websocket protocol.
  Repeated subscription will be cancelled for the same data type.
  </summary>

### Detailed information
<details open>
  <summary>  </summary>  

**System Methods**

* [PING-PONG Method](#ping-pong-method) - used to test the health of sockets
* [System Time Method](#system-time-method) - used to synchronize with socket service times
* [Web-Soket Authentication](#web-soket-authentication) - used to subscribe on private methods

**Public Methods**

* [KLine methods for Graph](#kline-methods-for-graph) - used to get data on charts (the change comes after a trade and chart data change)
* [Market Price Methods](#market-price-methods) - used to get information about the market price for a specific pair (the change comes after a trade and a change in the market price)
* [Market Price Methods](#market-price-methods) - used to get information about market activity for a specific pair (the change comes after a trade and a change in market parameters)
* [Deals Methods](#deals-methods) - used to get information about the last trade in the market (the change comes after the execution of a trade)
* [Depth Methods](#depth-methods) - used to get information on order books and monitor their changes in the market (the change comes after placing, canceling, executing, changing an order)

**Private Methods**

* [Trade Users Balances Methods](#trade-user-balances-methods) - used to get the balances of the user from which the request is made (the change comes after updating the value of any balance)
* [Active Orders and Orders History Methods](#active-orders-and-orders-history-methods) - used to get active orders and their history for a specific user from which the request is made (the change comes after placing, canceling, executing, changing an order)

**Private Methods:**

This method provides information via `POST`. The response will return all the information that was posted by the platform. To obtain private information, use the same methods via `POST` as an authorized user using api keys.

- [List of Public Pairs](#list-of-public-pairs) - returns the history of trades for all public pairs - used for tracking to compare prices in the market, control positions for many markets at once.
- [Specific Public Ticker Data](#specific-public-ticker-data) - returns the trading history for a specific selected pair - used to track a specific pair and track its key characteristics.
- [List of Order Book](#list-of-order-book) - Returns all positions for a specific market for all orders of the order book in the selected direction with pagination - used for full monitoring of the order book status, its changes, evaluation of its placed orders and their priority.
- [Market History Data](#market-history-data) - returns the history of trading on the market - used to track your own or someone else is executed orders, trading dynamics, control over buying / selling.
- [Public Pair List](#public-pair-list) - returns all public pairs - used to track new pairs, monitor pairs for MM.
- [Depth List](#depth-list) - returns data on the order book with pagination for a specific pair - used to track the depth of the order book, control placed orders, monitor the market in light mode.
- [List of Graphic Data KLine](#list-of-graphic-data-kline) - returns data on charts - is used to create personal charts, control market trends.

</details>

---

### Common methods
<details open>
<summary> </summary>

#### Web-Soket Authorization
  <details open>
  <summary> </summary>

**Request:**

**Method**
```java
server.auth
```

**Request Parameters:**
Name | Type | Description |
------------ | ------------ | ------------
Token | STRING | Personal user token ( check  [HTTP Authorization](#http-authorization) )
Source | STRING | Defoult: web; Max: 30 bytes; Custom configuration for understendong source of web-soket info

**Request**
```javascript
{
  "method":"server.auth",
  "params":
    [
      "$2y$10$J3gXRK...ZOH2yGXTlOcp9iBUq"
      "web"
    ],
  "id":0
}
```

**Response Parameters:**

Name | Type | Description |
------------ | ------------ | ------------
Status | STRING | Success - auth compleated

**Response:**
```javascript
{
  id: 2,
  params:
    [
    ],
  result:
    {
      status: "success"
    },
  error: null
}
```
</details>

#### Basic WS structure
<details open>
<summary> </summary>

**Request**

* Endpoint
```java
wss://pointpay.io/ws
```

Name | Type |
------------ | ------------
method | STRING |
params | ARRAY |
id | INTEGER |

**Response**

Name | Type | Success | Error
------------ | ------------ | ------------ | ------------
result | JSON Object | Response | Failure
Error | JSON Object | NULL | Failure
id | Integer | Integer | Integer

**Notify**

Name | Type |
------------ | ------------
method | STRING |
params | ARRAY |
id | NULL |

**General error code:**

* invalid argument
* internal error
* service unavailable
* method not found
* service timeout
* require authentication

</details>

#### PING-PONG Method
  <details open>
  <summary>
  </summary>

**Method**
```
server.ping
```

**Request**
```javascript
{
  "method":"server.ping",
  "params":[],
  "id":1000}
}
```

**Response Parameters:**

Name | Type |
------------ | ------------
result | STRING |
ID | NUMERIC |
Error | NULL |

**Response:**
```javascript
{
  "error": null,
  "result": "pong",
  "id": 1000
}
```
</details>

#### System Time Method
  <details open>
  <summary>
  </summary>

**Request:**

**Method**
```
server.time
```

**Request**
```javascript
{
  "method":"server.time",
  "params":[],
  "id":0
}
```

**Response Parameters:**

Name | Type |
------------ | ------------
result | TimeStamp, INTEGER |
ID | NUMERIC |
Error | NULL |

**Response:**
```javascript
{
  "error": null,
  "result": 1493285895,
  "id": 1000
}
```
</details>
</details>


### KLine methods for Graph
<details open>
<summary>
</summary>

#### KLine Query Method
  <details open>
  <summary>
  </summary>

**Method**
```
kline.query
```

**Request Parameters:**

Name | Type |
------------ | ------------
market | STRING |
interval | NUMERIC |

**Request:**
```javascript
{
  "method":"kline.query",
  "params":
    [
      "ETH_BTC",
      3600
    ],
  "id":1000
}
```

**Response Parameters:**

Name | Type |
------------ | ------------
time | TimeStamp, INTEGER |
open | STRING |
close | STRING |
highest | STRING |
lowest | STRING |
volume | STRING |
amount | STRING |
market | STRING |

**Response:**
```javascript
{
  "method": "kline.query",
  "params":
    [
      [
        1568548260, //time
        "0.018302", //open
        "0.018302", //close
        "0.018302", //highest
        "0.018302", //lowest
        "500",      //volume
        "15",       //amount
        "ETH_BTC"   //market
      ]
    ],
  "id": 1000
}
```
</details>

#### KLine Subscribe Method
  <details open>
  <summary>
  </summary>

**Method**
```
kline.subscribe
```

**Request Parameters:**

Name | Type |
------------ | ------------
market | STRING |
interval | NUMERIC |

**Request:**
```javascript
{
  "method":"kline.subscribe",
  "params":
    [
      "ETH_BTC",
      3600
    ],
  "id":1000
}
```

**Response Parameters:**

```
kline.update
```

Name | Type |
------------ | ------------
time | TimeStamp, INTEGER |
open | STRING |
close | STRING |
highest | STRING |
lowest | STRING |
volume | STRING |
amount | STRING |
market | STRING |

**Response:**

```javascript
{
  "method": "kline.update",
  "params":
    [
      [
        1568548260, //time
        "0.018302", //open
        "0.018302", //close
        "0.018302", //highest
        "0.018302", //lowest
        "500",      //volume
        "15",       //amount
        "ETH_BTC"   //market
      ]
    ],
  "id": null
}
```
</details>

#### KLine Unsubscribe Method
  <details open>
  <summary>
  </summary>

**Method**
```
kline.unsubscribe
```

**Request**
```javascript
{
  "method":"kline.unsubscribe",
  "params":[],
  "id":16
}
```
</details>
</details>

### Market Price Methods
<details open>
<summary>
</summary>

#### Market Price Query Method
  <details open>
  <summary>
  </summary>

**Method**
```
price.query
```

**Request Parameters:**

Name | Type | Descriprion |
------------ | ------------ | ------------
market | STRING | Can be few markets per request

**Request:**
```javascript
{
  "method":"price.query",
  "params":
    [
      "ETH_BTC",
    ],
  "id":111
}
```

**Response Parameters:**
Name | Type |
------------ | ------------
market | STRING |
price | STRING |

**Response:**
```javascript
{
  "method":"price.query",
  "params":
    [
      "ETH_BTC",
      "0.01860351"
    ],
  "id":111
}
```
</details>

#### Market Price Subscribe Method
  <details open>
  <summary>
  </summary>

**Method**
```
price.subscribe
```

**Request Parameters:**
Name | Type | Description |
------------ | ------------ | ------------
market | STRING | Can be few markets per request

**Request:**
```javascript
{
  "method":"price.subscribe",
  "params":
    [
      "ETH_BTC",
    ],
  "id":111
}
```

**Response Parameters:**
```
price.update
```

Name | Type |
------------ | ------------
market | STRING |
price | STRING |

**Response:**
```javascript
{
  "method":"price.update",
  "params":
    [
      "ETH_BTC",
      "0.01860351"
    ],
  "id":null
}
```
</details>

#### Market Price Unsubscribe Method
  <details open>
  <summary>
  </summary>

**Method**
```
price.unsubscribe
```

**Request**
```javascript
{
  "method":"price.unsubscribe",
  "params":[],
  "id":16
}
```
</details>
</details>

### Market Status Methods
<details open>
<summary>
</summary>

#### Market status Query Method
  <details open>
  <summary>
  </summary>

**Method**
```
state.query
```

**Request Parameters:**
Name | Type | Description |
------------ | ------------ | ------------
market | STRING | Any exchange market
period | NUMERIC | Default: 86400

**Request:**
```javascript
{
  "method":"state.query",
  "params":
    [
      "ETH_BTC",
      86400
    ],
  "id":111
}
```

**Response Parameters:**
Name | Type |
------------ | ------------
period | NUMERIC |
volume | STRING |
last | STRING |
open | STRING |
low | STRING |
close | STRING |
high | STRING |
deal | STRING |

**Response:**
```javascript
{
  "result":
    {
      "period": 86400,
      "volume": "192238.62392908",
      "last": "0.018295",
      "open": "0.017526",
      "low": "0.0174",
      "close": "0.018295",
      "high": "0.02",
      "deal": "3479.25570915213257"
    },
  "error": null,
  "id": 111
}
```
</details>

#### Market Status Subscribe Method
  <details open>
  <summary>
  </summary>

**Method**
```
state.subscribe
```

**Request Parameters:**
Name | Type | Description |
------------ | ------------ | ------------
market | STRING | Any exchange market
period | NUMERIC | Default: 86400

**Request:**
```javascript
{
  "method":"state.subscribe",
  "params":
    [
      "ETH_BTC",
      86400
    ],
  "id":111
}
```

**Response Parameters:**
```
state.update
```

**Response Parameters:**
Name | Type |
------------ | ------------
period | NUMERIC |
volume | STRING |
last | STRING |
open | STRING |
low | STRING |
close | STRING |
high | STRING |
deal | STRING |

**Response:**
```javascript
{
  "result":
    {
      "period": 86400,
      "volume": "192238.62392908",
      "last": "0.018295",
      "open": "0.017526",
      "low": "0.0174",
      "close": "0.018295",
      "high": "0.02",
      "deal": "3479.25570915213257"
    },
  "error": null,
  "id": null
}
```
</details>

#### Market Status Unsubscribe Method
  <details open>
  <summary>
  </summary>

**Method**
```
state.unsubscribe
```

**Request**
```javascript
{
  "method":"state.unsubscribe",
  "params":[],
  "id":16
}
```
</details>
</details>


### Deals Methods
<details open>
<summary>
</summary>

#### Deals Query Method
  <details open>
  <summary>
  </summary>


**Method**
```
deals.query
```

**Request Parameters:**
Name | Type | Description |
------------ | ------------ | ------------
market | STRING | Can be few markets per request

**Request:**
```javascript
{
  "method":"deals.query",
  "params":
    [
      "ETH_BTC",
      "BTC_USD"
    ],
  "id":111
}
```

**Response Parameters:**
Name | Type |
------------ | ------------
type | NUMERIC |
time | STRING |
id | NUMERIC |
amount | STRING |
price | STRING |


**Response:**
```javascript
{
  "method": "deals.query",
  "params":
    [
      "ETH_BTC",
        [
          {
            "type": "sell",
            "time": 1568556382.1329091,
            "id": 5478754,
            "amount": "4.9193309",
            "price": "10365.40703518"
          }
        ],
      "BTC_USD",
        [
          {
            ...
          }
        ],
    ],
  "id": 111
}
```
</details>

#### Deals Subscribe Method
  <details open>
  <summary>
  </summary>

**Method**
```
deals.subscribe
```

**Request Parameters:**
Name | Type | Description |
------------ | ------------ | ------------
market | STRING | Can be few markets per request

**Request:**
```javascript
{
  "method":"deals.subscribe",
  "params":
    [
      "ETH_BTC",
      "BTC_USD"
    ],
  "id":111
}
```

**Response Parameters:**
```
deals.update
```

**Response Parameters:**
Name | Type |
------------ | ------------
type | NUMERIC |
time | STRING |
id | NUMERIC |
amount | STRING |
price | STRING |

**Response:**
```javascript
{
  "method": "deals.update",
  "params":
    [
      "ETH_BTC",
        [
          {
            "type": "sell",
            "time": 1568556382.1329091,
            "id": 5478754,
            "amount": "4.9193309",
            "price": "10365.40703518"
          }
        ],
      "BTC_USD",
        [
          {
            ...
          }
        ],
    ],
  "id": null
}
```
</details>


#### Deals Unsubscribe Method
  <details open>
  <summary>
  </summary>

**Method**
```
deals.unsubscribe
```

**Request**
```javascript
{
  "method":"deals.unsubscribe",
  "params":[],
  "id":16
}
```
</details>
</details>

### Depth Methods
<details open>
<summary>
</summary>

#### Depth Query Method
  <details open>
  <summary>
  </summary>

**Method**
```
depth.query
```

**Request Parameters:**
Name | Type | Description |
------------ | ------------ | ------------
market | STRING | Any market pair
limit | NUMERIC | Limit of order quantity
interval | STRING | Defoult: 1, No interval: 0, Step: 1

**Request:**
```javascript
{
  "method":"depth.query",
  "params":
    [
      "ETH_BTC",  //market
      1,          //limit
      "0"         //interval
    ],
  "id":111
}
```

**Response Parameters:**
Name | Type |
------------ | ------------
type | STRING |
id | NUMERIC |
amount | STRING |
price | STRING |


**Response:**
```javascript
}
  "method":"depth.query",
  "result":
   {
      "asks":
       [
          [
            "8000.00", //price
            "9.6250"   //amount
          ]
       ],
      "bids":
       [
         [
           "7000.00",  //price
           "0.1000"    //amount
         ]
       ]
   "id": 111,
}
```
</details>

#### Depth Subscribe Method
  <details open>
  <summary>
  </summary>

**Method**
```
depth.subscribe
```

**Request Parameters:**
Name | Type | Description |
------------ | ------------ | ------------
market | STRING | Any market pair
limit | NUMERIC | Limit of order quantity
interval | STRING | Defoult: 1, No interval: 0, Step: 1

**Request:**
```javascript
{
  "method":"depth.subscribe",
  "params":
    [
      "ETH_BTC",    //market
      1,            //limit
      "0"           //interval
    ],
  "id":111
}
```

**Response Parameters:**
```
depth.update
```

**Response Parameters:**
Name | Type | Description |
------------ | ------------ | ------------
clean | BOOLEAN | FALSE: returned latest result, TRUE - no updates
limit | NUMERIC | Return update from last result with limit
market | STRING | Subscribed market
id | NUMERIC | Request ID
Type | String | Order type
Amount | String | order amount in 1st Ticker
Price | String | order price in 1st Ticker

**Response:**
```javascript
{
  "method": "depth.update",
  "params":
    [
      true,
        {
          "asks":
            [
              [
                "0.018519", //price
                "120.6"     //amount
              ]
            ],
          "bids":
            [
              [
                "0.01806",    //price
                "90.31637262" //amount
              ]
            ]
        },
      "ETH_BTC"
    ],
  "id": null
}
```
</details>

#### Depth Unsubscribe Method
  <details open>
  <summary>
  </summary>

**Method**
```
depth.unsubscribe
```

**Request**
```javascript
{
  "method":"depth.unsubscribe",
  "params":[],
  "id":16
}
```
</details>
</details>

### Trade User Balances Methods
<details open>
<summary>
Need to authorize first
</summary>

#### Asset Query Method
  <details open>
  <summary>
  </summary>

**Method**
```
asset.query
```

**Request Parameters:**
Name | Type | Description |
------------ | ------------ | ------------
asset list | STRING | Null: for all; Asset List: for choosed

**Request:**
```javascript
{
  "method":"asset.query",
  "params":
    [
      "BTC"
    ],
  "id":111
}
```

**Response Parameters:**
Name | Type | Description |
------------ | ------------ | ------------
market | STRING | Asset name
available | NUMERIC | Amount without active orders
freeze | STRING | active orders amount

**Response:**
```javascript
{
  "BTC":
    {
      "available": "1.10000000",
      "freeze": "9.90000000"
    }
}

```
</details>

#### Asset Subscribe Method
  <details open>
  <summary>
  </summary>

**Method**
```
asset.subscribe
```

**Request Parameters:**
Name | Type | Description |
------------ | ------------ | ------------
asset list | STRING | Null: for all; Asset List: for choosed

**Request:**
```javascript
{
  "method":"asset.subscribe",
  "params":
    [
      "BTC",
      "CNY"
    ],
  "id":111
}
```

**Response Parameters:**
```
asset.update
```

**Response Parameters:**

Name | Type | Description |
------------ | ------------ | ------------
market | STRING | Asset name
available | NUMERIC | Amount without active orders
freeze | STRING | active orders amount

**Response:**
```javascript
[
  {
    "BTC:
      {
        "available": "1.10000000",
        "freeze": "9.90000000"
      },
    "CNY":
      {
      }
  }
]

```
</details>

#### Asset Unsubscribe Method
  <details open>
  <summary>
  </summary>

**Method**
```
asset.unsubscribe
```

**Request**
```javascript
{
  "method":"asset.unsubscribe",
  "params":[],
  "id":111
}
```
</details>
</details>
---

### Active Orders and Orders History Methods
<details open>
<summary>
Need to authorize first
</summary>

#### Order Query Method
<details open>
<summary> </summary>  

**Method**
```
order.query
```

**Request Parameters:**
Name | Type | Description |
------------ | ------------ | ------------
market | STRING | For all pairs: ""; Special: Choosed Pair
offset | INTEGER | NO | Default 0
limit | INTEGER | Defoult: 1, No interval: 0, Step: 1

**Request:**
```javascript
{
  "method":"order.query",
  "params":
    [
      "",      //market
      1,          //offset
      30           //interval
    ],
  "id":6
}
```

**Response Parameters:**
Name | Type | Description |
------------ | ------------ | ------------
algorithm | INTEGER | Spot: 0; Margin: 1
amount | NUMERIC | Order amount in first ticker of Pair (Asset)
ctime | TIMESTAMP | Time of order placed
deal_fee | STRING | user fee coefficient
deal_money | STRING | Order amount in second ticker of Pair (Market)
deal_stock | STRING | Order amount in first ticker of Pair (Asset)
id | INTEGER | Order ID
left | STRING | Order left (if not traded = amount = deal_stock; if 0 - order finished)
maker_fee | STRING | Fee of order placer
market | STRING | Order Pair
mtime | INTEGER | Matching time ( time of finishing)
platform | INTEGER | 0 - Limit / Market; 1 - Stop-limit; 2 - OCO
price | STRING | Order Price
side | INTEGER | 1 - Sell (Ask) 2- Buy (Bid)
source | STRING | Custom Parametr
taker_fee | STRING |  Fee of order tacker
type | INTEGER | 1 - Limit; 2 - Market
user | INTEGER | - User ID

**Response:**
```javascript
{
  id: 6,
  params:
    [
      6,
      "all",
      0,
      30
    ],
  result:
    {
      limit: 30
      market_name: "all"
      offset: 0
      records:
        {
          algorithm: 0
          amount: "1"
          ctime: 1620561060.783
          deal_fee: "0"
          deal_money: "0"
          deal_stock: "0"
          id: 224280948
          left: "1"
          maker_fee: "0.002"
          market: "ETH_EUR"
          mtime: 0
          platform: 0
          price: "5000"
          side: 1
          source: ""
          taker_fee: "0.002"
          type: 1
          user: 6
        }
      total: 1,
    }
}

```
</details>

#### Order History Method
  <details open>
  <summary>
  </summary>


**Method**
```
order.history
```

**Request Parameters:**

Name | Type | Description |
------------ | ------------ | ------------
market | STRING | Any market pair
start_time | INTEGER | Unlimited: 0; Timestamp: custom
end_time | INTEGER | Unlimited: 0; Timestamp: custom
offset | INTEGER | NO | Default 0
limit | NUMERIC | Limit of order quantity
side | STRING | Unlimited: 0, Sell: 1, Buy: 2


**Request:**
```javascript
{
  method: "order.history",
  params:
    [
      "",
      0,
      0,
      0,
      30
    ],
  id: 10
}

```



**Response Parameters:**

Name | Type | Description |
------------ | ------------ | ------------
algorithm | INTEGER | Spot: 0; Margin: 1
amount | NUMERIC | Order amount in first ticker of Pair (Asset)
ctime | TIMESTAMP | Time of order placed
deal_fee | STRING | user fee coefficient
deal_money | STRING | Order amount in second ticker of Pair (Market)
deal_stock | STRING | Order amount in first ticker of Pair (Asset)
id | INTEGER | Order ID
left | STRING | Order left (if not traded = amount = deal_stock; if 0 - order finished)
maker_fee | STRING | Fee of order placer
market | STRING | Order Pair
mtime | INTEGER | Matching time ( time of finishing)
platform | INTEGER | 0 - Limit / Market; 1 - Stop-limit; 2 - OCO
price | STRING | Order Price
side | INTEGER | 1 - Sell (Ask) 2- Buy (Bid)
source | STRING | Custom Parametr
taker_fee | STRING |  Fee of order tacker
type | INTEGER | 1 - Limit; 2 - Market
user | INTEGER | - User ID

**Response:**
```javascript

{
  id: 6,
  params:
    [
      "",
      0,
      0,
      0,
      30
    ],
  result:
    {
      limit: 30
      start_time: 0
      end_time: 0
      market_name: ""
      side: 0
      offset: 0
      records:
        {
          algorithm: 0
          amount: "1"
          ctime: 1620561060.783
          deal_fee: "0"
          deal_money: "0"
          deal_stock: "0"
          id: 224280948
          left: "1"
          maker_fee: "0.002"
          market: "ETH_EUR"
          mtime: 0
          platform: 0
          price: "5000"
          side: 1
          source: ""
          taker_fee: "0.002"
          type: 1
          user: 6
        }
    }
}
```
</details>

#### Order Subscribe Method
<details open>
  <summary>
  </summary>

**Method**
```
order.subscribe
```

**Request Parameters:**
Name | Type | Description |
------------ | ------------ | ------------
market | STRING | For all pairs: ""; Special: Choosed Pair
offset | INTEGER | NO | Default 0
limit | INTEGER | Defoult: 1, No interval: 0, Step: 1

**Request:**
```javascript
{
  "method":"order.subscribe",
  "params":
    [
      "BTC_USDT",      //market
    ],
  "id":6
}
```
```
order.update
```

**Response Parameters:**
Name | Type | Description |
------------ | ------------ | ------------
algorithm | INTEGER | Spot: 0; Margin: 1
amount | NUMERIC | Order amount in first ticker of Pair (Asset)
ctime | TIMESTAMP | Time of order placed
deal_fee | STRING | user fee coefficient
deal_money | STRING | Order amount in second ticker of Pair (Market)
deal_stock | STRING | Order amount in first ticker of Pair (Asset)
id | INTEGER | Order ID
left | STRING | Order left (if not traded = amount = deal_stock; if 0 - order finished)
maker_fee | STRING | Fee of order placer
market | STRING | Order Pair
mtime | INTEGER | Matching time ( time of finishing)
platform | INTEGER | 0 - Limit / Market; 1 - Stop-limit; 2 - OCO
price | STRING | Order Price
side | INTEGER | 1 - Sell (Ask) 2- Buy (Bid)
source | STRING | Custom Parametr
taker_fee | STRING |  Fee of order tacker
type | INTEGER | 1 - Limit; 2 - Market
user | INTEGER | - User ID

**Response:**
```javascript
result:
    {
        {
          algorithm: 0
          amount: "1"
          ctime: 1620561060.783
          deal_fee: "0"
          deal_money: "0"
          deal_stock: "0"
          id: 224280948
          left: "1"
          maker_fee: "0.002"
          market: "ETH_EUR"
          mtime: 0
          platform: 0
          price: "5000"
          side: 1
          source: ""
          taker_fee: "0.002"
          type: 1
          user: 6
        }
    }

```
</details>

#### Order Unsubscribe Method
  <details open>
  <summary>
  </summary>

**Method**
```
order.unsubscribe
```

**Request**
```javascript
{
  "method":"order.unsubscribe",
  "params":[],
  "id":16
}
```
</details>
</details>
