---
title: Respondent Portal API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Respondent Portal API! 

The RP API is a RESTful API for Curious Cat client applications.  




This page was created with [Slate](https://github.com/lord/slate)

# Setup

## Host

```shell
# staging
host="https://respondent-portal-i.odbot.co.uk"

# production
host="https://pawoints.com"
```

## api versioning

```shell
defaultApiVersion="2"
taskApiVersion="5"
taskCompleteApiVersion="4"
flaggedTaskApiVersion="3"
balanceApiVersion="3"
cashoutProductApiVersion="3"
verifyEmailApiVersion="3"
```

## App Version
 
Min supported client app version = 0.29.1

```shell
# for development
appVersion="_dev_"

# for production 
appVersion="0.33.4"
```
 
// TODO: Error 409, apiUpgradeNeeded
 
 
## UserAgent

A valid userAgent string is required.

```shell
# android 
userAgent="Curious Cat/$appVersion; (Android 6.0.1, API 23)"

# ios: 
# NB. the device must support telephony, so no iPad, iPod etc.

userAgent="Curious Cat/$appVersion; (iPhone 10.2.1)"
```


// TODO: Error 409, invalidUserAgent


# Authentication

Passwordless authentication, achieved with 3 steps, 2 of which are http requests. 

## 1. request OTP 

```shell
curl -A "$userAgent" -X POST $host/api/2/verify/generate \
-H "Content-type: application/json" \
--data '{username:"+447534405065", countryCode:"GB", locale:"en_GB", deviceId:"2f6480bd-1b36-40c1-ad12-49b2f5899de0", os:"android", doNotTrack:"false"}'

# optionally accepts install referrer:
curl -A "$userAgent" -X POST $host/api/2/verify/generate \
-H "Content-type: application/json" \
--data '{username:"+447534405065", countryCode:"GB", locale:"en_GB", deviceId:"7703AEB1-6AAD-483B-9999-D1116C02227B", os:"android", doNotTrack:"false", installReferrer:"utm_source%3Dpawoints_com%26utm_medium%3Dinstall_ctrl%26utm_content%3D302%26utm_campaign%3Dic1"}' 

>
{
    "meta":{"respondentPk":"N/A","uri":"/api/2/verify"},
    "status":"200",
    "message":"The request was successful and the response data contains the representation requested.",
    "data":{"requestId":"18b9b212b5444711a5f730cce7bf5633"}
}
```

- username: Phone number in e164 format.
- countryCode: ISO country code
- os: android | ios 
- deviceId: aaid | idfa 
- doNotTrack: limitAdTracking
 
NB. requestId is required as input in the second request:


// TODO: Errors: 400, 409 



## 2. OTP is sent to phone number via SMS (or voice)

```shell
requestId="18b9b212b5444711a5f730cce7bf5633"
otp="8272"
```

Retrieve OTP


## 3. check OTP, requestId

```shell
curl -A "$userAgent" $host/api/2/verify/check/$requestId/$otp
>
{
    "username":"+447534405065",
    "roles":["ROLE_USER"],
    "respondentPk":"b06011cf-e2ff-4d03-8388-47a3e5ab9e6e",
    "token_type":"Bearer",
    "access_token":"rg4p2omcerpgc5mu9d8j23tv6n02qnuk"
}
```
Responds with status 200 or 201 (login or register)

// TODO: Errors: 400, 409

## Auth token

```shell
token="rg4p2omcerpgc5mu9d8j23tv6n02qnuk"
```
Store the auth token


# Task

## List

```shell
curl -A "$userAgent" -i -X GET $host/api/5/task \
-H "Content-type: application/json" \
-H "Authorization: Bearer $token"
```

List all available tasks matched to the current user's profile.

## List (deprecated)
```shell
curl -A "$userAgent" -i -X GET "$host/api/5/task?os=android&osVersion=4.1.1&deviceId=e799d78a16c74877aab267058d9a4e3e&limitAdTracking=false&locale=en_GB" \
-H "Content-type: application/json" \
-H "Authorization: Bearer $token"
``` 

Inc. device profile query params (deprecated)

## Item

```shell
curl -A "$userAgent" -i -X GET $host/api/5/task/$taskPk \
-H "Content-type: application/json" \
-H "Authorization: Bearer $token"
```

// TODO: Error: 404, 410
 

# Task link

```shell
$host/link/$taskPk/$respondentPk?[aaid=<android_aaid>|idfa=<apple_idfa>]
```

Task link endpoint will return an appropriate http response in order to allow e.g. start a survey or a download in a web browser.

Client applications are expected to generate a link of the form:


### important!
 
(deprecated) If task.custom is not null, its contents should be serialized to a query string and appended to the task link.

Where task.custom is `@Nullable Map<String,String>`


# TaskComplete

## list

```shell
curl -A "$userAgent" -i -X GET $host/api/4/task/complete \
-H "Content-type: application/json" \
-H "Authorization: Bearer $token"
```

## update

```shell
curl -A "$userAgent" -i -X POST $host/api/4/task/complete \
-H "Content-type: application/json" \
-H "Authorization: Bearer $token" \
--data '{taskPk:"b3b57392-5335-4565-8408-ec5a6bdc854f", status:"started"}'
```   

# FlaggedTask

## list

```shell
curl -A "$userAgent" -i -X GET $host/api/3/task/flagged \
-H "Content-type: application/json" \
-H "Authorization: Bearer $token"
```

## update

```shell
# item 
curl -A "$userAgent" -i -X POST $host/api/3/task/flagged \
-H "Content-type: application/json" \
-H "Authorization: Bearer $token" \
--data '[{"reason":"other","respondentPk":"d1adada0-3afe-11e3-8934-d3a2b39befda","taskPk":"781318b4-7ef3-4491-a7bd-dc027bc7d876","text":"BISCUITS HEY"}]' 

# list     
curl -A "$userAgent" -i -X POST $host/api/3/task/flagged \ 
-H "Content-type: application/json" \ 
 -H "Authorization: Bearer $token" \
--data '[{"reason":"other","respondentPk":"d1adada0-3afe-11e3-8934-d3a2b39befda","taskPk":"781318b4-7ef3-4491-a7bd-dc027bc7d876","text":"BISCUITS HEY"},{"reason":"an_error","respondentPk":"d1adada0-3afe-11e3-8934-d3a2b39befda","taskPk":"19efce98-2161-4ed9-9b89-fca14ec3de13","text":null, isDeleted:true}]'
```

# Device profile

```shell
curl -A "$userAgent" -i -X POST $host/api/2/device \
-H "Content-type: application/json" \
-H "Authorization: Bearer $token" \
--data '{deviceId:"2f6480bd-1b36-40c1-ad12-49b2f5899de0", os:"android", doNotTrack:"false"}' 

# optional locale added in version 0.12
curl -A "$userAgent" -i -X POST $host/api/2/device \
-H "Content-type: application/json" \
-H "Authorization: Bearer $token" \
--data '{deviceId:"2f6480bd-1b36-40c1-ad12-49b2f5899de0", os:"android", doNotTrack:"false", locale:"en_GB"}'
```

Submit device profile.

- deviceId (String): aaid on Android, idfa on iOS.
- os (String): "android" or "ios"


# Register for push notifications

```shell
curl -A "$userAgent" -i -X POST $host/api/2/gcm \
-H "Content-type: application/json" \
-H "Authorization: Bearer $token" \
--data '{tokenValue:"dZqqpNgY5P4xAPA91bEM8C6ZtxG81dku46N"}'
```

# Balance

```shell
curl -A "$userAgent" -i -X GET $host/api/3/balance \
-H "Content-type: application/json" \
-H "Authorization: Bearer $token"
```

# Cashout

## Cashout products


## Cashout products V3 (Deprecated)


```shell
curl -A "$userAgent" -i -X GET $host/api/3/cashout/product \
-H "Content-type: application/json" \
-H "Authorization: Bearer $token"
```


```json
{
  "meta": {
    "respondentPk": "0e1c1de4-f899-4eeb-9459-1fd3d4e1cf5d",
    "uri": "/api/3/cashout/product"
  },
  "status": "200",
  "message": "Ok",
  "data": {
    "list": [
      {
        "productId": null,
        "currencyCode": "GBP",
        "points": 100,
        "amount": "1.00",
        "exchangeRate": "1.0",
        "paymentType": "PayPal",
        "name": "maximum amount with PayPal"
      }
    ],
    "emailCashoutEnabled": "yes",
    "balance": {
      "balance": 100,
      "canCashout": true
    }
  }
}
```

## Cashout products V4


```shell
curl -A "$userAgent" -i -X GET $host/api/3/cashout/product \
-H "Content-type: application/json" \
-H "Authorization: Bearer $token"
```

curl -A "$userAgent" -i -X GET $host/api/4/cashout/product \
-H "Content-type: application/json" \
-H "Authorization: Bearer $token"
```


```json
{
    "meta": {
        "respondentPk": "0e1c1de4-f899-4eeb-9459-1fd3d4e1cf5d",
        "uri": "/api/4/cashout/product"
    },
    "status": "200",
    "message": "Ok",
    "data": {
        "list": [
            {
                "productId": null,
                "currencyCode": "GBP",
                "points": 100,
                "amount": "1.00",
                "exchangeRate": "1.0",
                "paymentType": "PayPal",
                "name": "maximum amount with PayPal",
                "enabled": false,
                "disabledStatus": "locked",
                "description": "Cashout available in 3 days",
                "lockExpires": "2021-05-25T00:00:00Z"
            }
        ],
        "emailCashoutEnabled": "yes",
        "balance": {
            "balance": 100
        }
    }
}
```

### Disabled Status

Value      | Description
----------- | -----------
locked      | Exceeding the weekly cashout limit
insufficient_balance   | Not earnt enough points
manual_verification_required   | Exceeding max cashout limit


## Cashout

// with setup (paymentType, amount, point, exchangeRate, name?:"", userDetail?:"")

```shell
# signature 
md5 -s "PayPal1.351351.0maximum amount with PayPaltim@ansr.io"

# request
curl -A "$userAgent" -X POST $host/api/2/cashout \
-H "Content-type: application/json" \
-H "Authorization: Bearer $token" \
--data '{"userDetail":"tim@ansr.io", "name":"maximum amount with PayPal",
"paymentType":"PayPal","points":135,\"currencyCode":"GBP","amount":1.35,
"exchangeRate":1.0,"signature":"5930df7c16ccd020954e6376209c5590"}'        
```

// with setup (paymentType, amount, point, exchangeRate, name?:"", userDetail?:"")


## Cashout transaction history

```shell
curl -A "$userAgent" -i -X GET $host/api/2/cashout/history \
-H "Content-type: application/json" \
-H "Authorization: Bearer $token"
```

# Preferences

Released in version 0.17

The field "legacy" indicates that preferences have not yet been written i.e. that the user's client application is pre 0.17.x

## Read

```shell
curl -A "$userAgent" -i -X GET $host/api/2/preferences \
-H "Content-type: application/json" \
-H "Authorization: Bearer $token"

# response
{
  "notifyTaskPayout": true,
  "notifyNewTask": true,
  "legacy": true
}
```

## Update

```shell
curl -A "$userAgent" -i -X PUT $host/api/2/preferences \
-H "Content-type: application/json" \
-H "Authorization: Bearer $token" \ 
--data '{"notifyNewTask": true, "notifyTaskPayout": false}'

# response: status 200
```

NB. when update is first called 'legacy' is set to false


# Error Types

```java
public enum PublicApiError {
    exists,
    save,
    invalidUserAgent,
    apiUpgradeNeeded,
    fraudLock;
}
```

Ref https://github.com/Ansr-io/ccat-plugins/blob/master/ccat-constants/src/main/java/io/ansr/ccat/PublicApiError.java


## Invalid User Agent

```shell
{
  "meta": {
    "respondentPk": "N/A",
    "uri": "/api/3/balance"
  },
  "status": "403",
  "message": "Forbidden",
  "data": {
    "errorType": "invalidUserAgent"
  }
}
```

The API will return a '403 invalidUserAgent' response if an invalid userAgent header is detected.

Example:


## Force upgrade

```shell
{
  "meta": {
    "respondentPk": "N/A",
    "uri": "/api/3/balance"
  },
  "status": "409",
  "message": "Conflict",
  "data": {
    "errorType": "apiUpgradeNeeded"
  }
}
```

The API will return a '409 apiUpgradeNeeded' response if app version < required minimum version.

App version is obtained by parsing the userAgent header.

Example:


## Fraud Lock

```shell
{
  "meta": {
    "respondentPk": "N/A",
    "uri": "/api/3/balance"
  },
  "status": "409",
  "message": "Conflict",
  "data": {
    "errorType": "fraudLock"
  }
}
```

The API will return a '409 fraudLock' response if user account is locked.

    `sec_user.fraud_lock = 1;`

Example:
