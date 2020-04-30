## 21 Systems airtime API ( Version 1.2)

#### Contents
- [Authentication](#authentication)
- [Making Authenticated Requests](#making-authenticated-requests)
- [Endpoints](#endpoints)
- [Topup endpoint](#post-airtimeapisubscribertopup)
- [Check Balance endpoint](#get-airtimeapisubscriberbalance)

### Authentication
The API is using Oauth 2.0 token for authentication of request. Client will request an access token using Client Credential Grant according to RFC 6749. The token received is a json web token (JWT).

The client crendentials in this case is a username/password pair obtained when signing up for the service.
To request an API account with this MTN airtime API, please send an email to [here](mailto:chol@dmarkmobile.com)

To obtain an access token from your username and password you need to send `POST` request to `/auth/token`

#### Authentication

```Go
curl -v X "Content-Type: application/x-www-form-urlencoded" -d "grant_type=password&username=email@example.com&password=secret" $host/auth/token

//returns
{
  "access_token": "jwt-token-representing-the-user",
  "token_type": "bearer",
  "expires_in": 3600,
  "refresh_token": "a-refresh-token"
}
```
The token expires after the time shown from `expires_in` response param. You can set your application to request another token using `refresh_token` param by sending request as seen below

```Go
curl -v -d "grant_type=refresh_token&refresh_token=my-refresh-token" -X "Content-Type: application/x-www-form-urlencoded" $host/auth/token
```
__Important__: The token must be treated as a credential and kept secret. The party that have access to the token will be authenticated as the user that requested the token.

#### Making Authenticated Requests
After obtaining the access token from the above, you can access protected resources by including the access token in the header.

```Go
curl -v -X "Authorization: Bearer access-token-string" $host/auth/user

//returns
{
  "id": "11111111-2222-3333-4444-5555555555555",
  "email": "email@example.com",
  "confirmation_sent_at": "2016-05-15T20:49:40.882805774-07:00",
  "created_at": "2016-05-15T19:53:12.368652374-07:00",
  "updated_at": "2016-05-15T19:53:12.368652374-07:00"
}
```
### Endpoints
__POST /auth/token__
This endpoint is used to request for an access token.
The request body is just `application/x-www-form-urlencoded` data.

```Go
grant_type=password&username=email@example.com&password=secret
```
The response is json object with access token or error in case of one.
a success response returns

```json
{
"access_token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJqZWRjbyIsImV4cCI6MTU4ODIyNzQ0Miwic3ViIjoiMTY0MzExYWItOWI4Ni00NjliLTk4ZWQtZmU0ZDg2Njg3ZGM0IiwiZW1haWwiOiJjaG9sQGRtYXJrbW9iaWxlLmNvbSIsImFwcF9tZXRhZGF0YSI6e30sInVzZXJfbWV0YWRhdGEiOnt9fQ.ILmO5CRCbqm9ohnq1JTa-Afh5tm8qMoFDZeDw1HAp3k",
"token_type":"bearer",
"expires_in":3600,
"refresh_token":"3hUt3oUTvf1WYDJX5dpj4A"
}
```
while an error response look this

```json
{
"error":"invalid_grant",
"error_description":"Invalid Password"
}
```
you can inspect the `error_description` param to understand the nature of the error. they will be different based on the type of the error.

__POST /auth/logout__
This endpoint just requires you to supply the authorization header with a valid access token.
This will revoke all refresh tokens for the user. Remember that the JWT tokens will still be valid for stateless auth until they expires.

#### POST /airtime/api/subscriber/topup

Call this endpoint to send airtime to valid MTN-South Sudan subscriber. It accepts json object as a request body

example
```Go
{"msisdn":"211965615584","amount":6}' -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJqZWRjbyIsImV4cCI6MTU4ODIyODA4Nywic3ViIjoiMTY0MzExYWItOWI4Ni00NjliLTk4ZWQtZmU0ZDg2Njg3ZGM0IiwiZW1haWwiOiJjaG9sQGRtYXJrbW9iaWxlLmNvbSIsImFwcF9tZXRhZGF0YSI6e30sInVzZXJfbWV0YWRhdGEiOnt9fQ.Jwv1I26shOaOVOY-QQZLGFtqVdReHQLvQ9z-ldf5J5Y" $host/airtime/api/subscriber/topup
```

A success response looks like this
 ```json
 {
 "reference":"2015061114441812901004879",
 "statusCode":11,
 "statusMessage":"SUCCESS",
 "receiver":"211965615584",
 "balance":94
 }
 ```
 While an error response looks like this
 
 ```json
 {
 "Error":true,
 "Message":"Account balance amount less than topup amount"
 }
 ```
 Again, inspect the `Message` param to understand the nature of error.
 
 #### GET /airtime/api/subscriber/balance
  This endpoint provides you with your account information, this includes also the account airtime balance maintain with 21   Systems.
  The only you need to call this endpoint is access token. this endpoint does not require any request body.
  
  It returns the following json object on success
  
  ```json
  {
  "statusCode":0,
  "statusMessage":"",
  "currency":"SSP",
  "balance":94
  }
  ```
  
 
 

