## 21 Systems airtime API ( Version 1.2)

#### Contents
[Authentication]()
[Making Authenticated Requests]()
[Endpoints](_
[Topup endpoint]()
[Check Balance endpoint]()

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
