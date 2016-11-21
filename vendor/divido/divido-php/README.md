Divido API
=======

This is the documentation for the Divido API.

Sign up for an account to get instant access to our sandbox environment.

*Current version: v1.2*


Getting started
---------------

There are several distinct parts of a complete integration with the Divido API:

 * Deal Calculator
 * Finances
 * Credit Request
 * Fulfillment
 * Cancellation


### Deal Calculator

Calculate APR, monthly repayments, duration etc

### Finances

List Rate Card and all financial products available for a specific merchant.

### Credit Request

Initialite a new credit proposal

### Fulfillment

Mark application as fulfilled

### Cancellation

Mark application as cancelled

Change log
------------

#### 2016-06-28

- Added cancellation method
- Checkout URL
- Bug fixes
- Added more community libraries

#### 2016-01-28

- Added fulfillment method

#### 2015-06-21

- Added product to credit request
- Campaigns changed to Finances


Offical Libraries
---------------

We strongly encourage use of our official libraries for accessing the Divido API. Our official libraries are listed below, as well as community supported libraries.

#### PHP

Install from source

  `git clone https://github.com/DividoFinancialServices/divido-api-php.git`


Community libraries
---------------

Please submit a pull request to this page to add any missing libraries that should be featured here. Please note that Divido does not offer any support for community libraries.

#### Magento 1.*

  - [Divido for Magento 1.*](https://github.com/DividoFinancialServices/divido-magento)

#### Magento 2

  - [Divido for Magento 2](https://github.com/DividoFinancialServices/magento2-module)

#### WooCommerce

  - [Divido for WooCommerce](https://wordpress.org/plugins/divido-for-woocommerce/)

#### OpenCart

 - [Divido for OpenCart](https://github.com/DividoFinancialServices/divido-opencart)


Using the API
===========

API endpoints
---------------
To use the Divido API to query data, you will need to send a request to the correct endpoint. Request endpoints should depend on whether you wish to query the live or sandbox environment:

 - Sandbox: `https://secure.sandbox.divido.com`
 - Live: `https://secure.divido.com`

HTTP response codes
---------------
You may encounter the following response codes. Any unsuccessful response codes will contain more information to help you identify the cause of the problem.

 - `200` The request has succeeded.

 - `201` The request has been fulfilled and resulted in a new resource being created. The newly create resource can be referenced by the URI(s) returned in the entity of the response, with the most specific URI for the resource given by a Location header field.

 - `404` Not Found. The requested resource was not found. The response body will explain which resource was not found.

 - `500` Internal Server Error. The server encountered an error while processing your request and failed. Please report this to the Divido support team.



Webhooks
===========

To alert you of any changes in the status of your resources, Divido provides webhooks. These are POST requests to your server that are sent as soon as a resource changes status. The body of the request contains details of the change.

Request
---------------
The API server will send a POST request to the `response_url` associated with the application or `webhook_url` set by Divido support team.


#### Parameters


`application`   Application ID

`reference`   Third party reference (if supplied as part of the application) 

`status`   New status

`live`   False if webhook is sent from sandbox enviroment

`metadata`   Metadata (if supplied as part of the application)


#### Example Request

JSON example

``` json
{
    "application": 'C84047A6D-89B2-FECF-D2B4-168444F5178C',
    "reference": 100024,
    "status": 'SIGNED',
    "live": true,
    "metadata":  {
       "Invoice Number":"844001",
       "Order Number":"100019"
    }
}
```

Possible statuses
---------------

  - `ACCEPTED` - Application accepted by Underwriter

  - `DEFERRED` - Application deferred by Underwriter, waiting for new status

  - `DECLINED` - Applicaiton declined by Underwriter

  - `DEPOSIT-PAID` - Deposit paid by customer

  - `SIGNED` - Customer have signed all contracts

  - `FULFILLED` - Application fulfilled by Underwriter

  - `CANCELLED` - Application Cancelled

  - `COMPLETED` - Application Completed



Resources
===========

Finances
------------------

Returns an array with all finance options available for merchant

#### Example Request
   `GET` https://secure.divido.com/v1/finances?merchant={MERCHANT}&country={COUNTRY} `HTTP/1.1`

#### Example Response

JSON example

``` json
{
     "status":"ok",
     "finances":[
         {
            "id":"FAAC243AC-499A-84AF-DBBA-F58B9F7E798C",
            "country":"GB",
            "text":"RF - BNPL 12 Months",
            "interest_rate":0,
            "min_amount":0,
            "min_deposit":1,
            "max_deposit":30,
            "agreement_duration":48,
            "deferral_period":0
         },
         {
            "id":"FAAC243AC-499A-84AF-DBBA-F58B9F7E798C",
            "country":"GB",
            "text":"RF - BNPL 09 Months",
            "interest_rate":0,
            "min_amount":0,
            "min_deposit":1,
            "max_deposit":30,
            "agreement_duration":48,
            "deferral_period":9
         }
     ]
}
```


#### Parameters

`merchant` 
    -  Your unique account identifier (*Required, String*)
  
```
Example `demo_abc1234567890`
```

`country` - The country code (*Required, String*)

``` html
Example `GB`
```
  


Deal Calculator
------------------

The deal calculator calculates the payment terms for various terms and deposits.

#### Example Request
   `GET` https://secure.divido.com/v1/dealcalculator?merchant={MERCHANT}&amount={AMOUNT}&deposit={deposit}&country={country}&finance={FINANCE} `HTTP/1.1`

#### Example Response

JSON example

``` json
{  
    "status": 'ok',  
    "purchase_price": 2000,  
    "deposit_amount": 200,  
    "credit_amount": 1800,  
    "monthly_payment_amount": 150,  
    "total_repayable_amount": 1800,  
    "agreement_duration": 12,  
    "interest_rate": 0.0,  
    "interest_type": "APR"
 }  
```


#### Parameters

`merchant` 
    -  Your unique account identifier (*Required, String*)
  
```
Example `demo_abc1234567890`
```

`amount`
  - The total value of the order (*Required, Float*)

``` html
Example `2000.00`
```

  `deposit` - The value of the deposit. (*Required, Float*)

``` html
Example `200`
```

  `country` - The country code (*Required, String*)

``` html
Example `GB`
```

  `finance` - The finance code (*Required, String*)

``` html
Example `FA48EC74D-D95D-73A9-EC99-004FBE14A027`
```


Credit Request
------------------

The deal calculator calculates the payment terms for various terms and deposits.

#### Example Request
   `POST` https://secure.divido.com/v1/creditrequest `HTTP/1.1`


``` javascript
curl https://secure.divido.com/v1/creditrequest \
-d merchant=demo_abc1234567890 \
-d deposit=100 \
-d finance=FAAC243AC-499A-84AF-DBBA-F58B9F7E798C \
-d country=GB \
-d language=EN \
-d currency=GBP \
-d "customer[title]=Mr" \
-d "customer[first_name]=John" \
-d "customer[last_name]=Doe" \
-d "customer[country]=GB" \
-d "customer[postcode]=EC2A 4BX" \
-d "customer[email]=john.doe@domain.com" \
-d "customer[phone_number]=+44201234567" \
-d "customer[mobile_number]=+44701234567" \
-d "metadata[Invoice Number]=844001" \
-d "metadata[Order Number]=100019" \
-d "products[1][type]=product" \
-d "products[1][text]=Gibson Les Paul Studio Raw Guitar" \
-d "products[1][quantity]=1" \
-d "products[1][value]=1153.00" \
-d "products[2][type]=service" \
-d "products[2][text]=Restring Upgrade" \
-d "products[2][quantity]=1" \
-d "products[2][value]=100" \
-d "response_url=http://www.webshop.com/response.php" \
-d "checkout_url=http://www.webshop.com/checkout" \
-d "redirect_url=http://www.webshop.com/success.html"
```


#### Example Response

JSON example

``` json
{
    "status": 'ok',
    "id": 128412,
    "url": "https://secure.divido.com/token/da2e7559ac104cf3f21003e5fde4e9f1",
    "token": "da2e7559ac104cf3f21003e5fde4e9f1"
}
```


#### Parameters

`merchant` 
    -  Your unique account identifier (*Required, String*)
  
```
Example `demo_abc1234567890`
```

`deposit` - The value of the deposit. (*Required, Float*)
``` html
Example `200`
```

`finance` - The finance code (*Required, String*)
``` html
Example `FA48EC74D-D95D-73A9-EC99-004FBE14A027`
```

`country` - The country code (*Required, String*)
``` html
Example `GB`
```

`language` - The country code (*Required, String*)
``` html
Example `en`
```

`currency` - The currency code (*Required, String*)
``` html
Example `GBP`
```

`customer['title']` - Customer title (*Optional, String*)
``` html
Example `Mrs`
```

`customer['firstName']` - Customer first name (*Optional, String*)
``` html
Example `Jane`
```

`customer['lastName']` - Customer last name (*Optional, String*)
``` html
Example `Doe`
```

`customer['country']` - Customer country (*Optional, String*)
``` html
Example `GB`
```

`customer['postcode']` - Customer postcode (*Optional, String*)
``` html
Example `EC2A 4BX`
```

`customer['email']` - Customer postcode (*Optional, String*)
``` html
Example `jane.doe@email.com`
```

`customer['phone_number']` - Customer phone number (*Optional, String*)
``` html
Example `0201234567`
```

`customer['mobile_number']` - Customer mobile number (*Optional, String*)
``` html
Example `0701234567`
```

`metadata['key']` - metadata key (*Optional, String*)
``` html
Example `Invoice Number`
```

`metadata['value']` - metadata value (*Optional, String*)
``` html
Example `844001`
```

`products['1']['type']` - Product type, can be `product` or `service` (*Optional, String*)
``` html
Example `product`
```

`products['1']['text']` - Product name/description (*Optional, String*)
``` html
Example `Gibson Les Paul Studio Raw Guitar`
```

`products['1']['quantity']` - Product quantity (*Optional, String*)
``` html
Example `1`
```

`products['1']['value']` - Product value in same currency as proposal (*Optional, String*)
``` html
Example `1153.00`
```

`products['2']['type']` - Product type, can be `product` or `service` (*Optional, String*)
``` html
Example `service`
```

`products['2']['text']` - Product name/description (*Optional, String*)
``` html
Example `Restring Upgrade`
```

`products['2']['quantity']` - Product quantity (*Optional, String*)
``` html
Example `1`
```

`response_url` - The URL where we send notification about the payment (*Optional, String*)
``` html
Example `http://www.webshop.com/response.php`
```

`checkout_url` - A URL to which Divido redirects the customer if they get declined or wishes to cancel their application (*Optional, String*)
``` html
Example `http://www.webshop.com/checkout`
```

`redirect_url` - The URL the customer will get redirected to after a successful application (*Optional, String*)
``` html
Example `http://www.webshop.com/success.html`
```

Fulfillment
------------------

Mark an application as fulfilled and initialise a payout from the underwriter.

#### Example Request
   `POST` https://secure.divido.com/v1/fulfillment `HTTP/1.1`


``` javascript
curl https://secure.divido.com/v1/fulfillment \
-d merchant="demo_abc1234567890" \
-d application="CAAC243AC-499A-84AF-DBBA-F58B9F7E798C" \
-d deliveryMethod="delivery" \
-d trackingNumber="DHL291824419F" \
-d comments="Order was delivered to the customer by DHL" \
```


#### Example Response

JSON example

``` json
{
    "status": 'ok',
    "application": "CAAC243AC-499A-84AF-DBBA-F58B9F7E798C",
    "newStatus": "ACTION-LENDER"
}
```


#### Parameters

`merchant` 
    -  Your unique account identifier (*Required, String*)
  
```
Example `demo_abc1234567890`
```

`application` - The application or proposal identifier. (*Required, String*)
``` html
Example `CAAC243AC-499A-84AF-DBBA-F58B9F7E798C`
```

`deliveryMethod` - How the goods was delivered, can be either "store" or "delivery" (*Required, String*)
``` html
Example `FA48EC74D-D95D-73A9-EC99-004FBE14A027`
```

`trackingNumber` - If the deliveryMethod is delivery and you have a tracking number (*Optional, String*)
``` html
Example `DHL291824419F`
```

`comment` - Comment to the underwriter, can be order number or other information (*Optional, String*)
``` html
Example `Order was delivered to the customer by DHL`
```

Cancellation
------------------

Mark an application as cancelled and notify the underwriter.

#### Example Request
   `POST` https://secure.divido.com/v1/cancellation `HTTP/1.1`


``` javascript
curl https://secure.divido.com/v1/cancellation \
-d merchant="demo_abc1234567890" \
-d application="CAAC243AC-499A-84AF-DBBA-F58B9F7E798C" \
-d comments="Customer wished to cancelled the order" \
```


#### Example Response

JSON example

``` json
{
    "status": 'ok',
    "application": "CAAC243AC-499A-84AF-DBBA-F58B9F7E798C",
    "newStatus": "CANCELLED"
}
```


#### Parameters

`merchant` 
    -  Your unique account identifier (*Required, String*)
  
```
Example `demo_abc1234567890`
```

`application` - The application or proposal identifier. (*Required, String*)
``` html
Example `CAAC243AC-499A-84AF-DBBA-F58B9F7E798C`
```

`comment` - Comment to the underwriter, can be order number or other information (*Optional, String*)
``` html
Example `Customer wished to cancelled the order`
```

