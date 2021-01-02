# Serverless Lambda Auth0 Authorizer

## Description
A serverless framework ES6-friendly [Auth0](https://auth0.com) Lambda Authorizer, based on the [aws node auth0 custom authorizers](https://github.com/serverless/examples/tree/master/aws-node-auth0-custom-authorizers-api).

Features:
- ES6 friendly
- Allows rapid integration & testing Frontend applications
- Provides private endpoint for testing
- Provides public endpoint for testing
  
## Dependencies

* [serverless-bundle plugin](https://www.npmjs.com/package/serverless-pseudo-parameters): Webpack plugin providing zero configuration for bundling JavaScript including modern ES6/ES7 features.

## Setup

- Created service using [codingly.io](https://github.com/codingly-io/sls-base) as follows:

```shell
$ sls create --name auth-service --template-url https://github.com/codingly-io/serverless-auth0-authorizer
$ cd auth-service
$ npm install
```

- Created `secret.pem` file which contains the Auth0 public certificate used to verify tokens.
 - copied public certificate from `berlin-sls-auction.eu.auth0.com ` to `secret.pem` file in the root folder of this project.

## Deployment
```shell
$ sls deploy -v
```

## Auth service endpoints
- `POST` https://gjitqn0le8.execute-api.eu-west-1.amazonaws.com/dev/public
- `POST` https://gjitqn0le8.execute-api.eu-west-1.amazonaws.com/dev/private

## Testing
Use POSTMAN collection, e.g:

`auth0_domain`: berlin-sls-auction.eu.auth0.com

`POST` https://{{auth0_domain}}/oauth/token

Set the body form params (x-www-form-urlencoded):
- client_secret: `auth0 client secret`
- username: `auth0 email`
- password: `auth0 password`
- scope: `openid`

```json
{
    "access_token": "B-sRL4c-3pqj-0PP_A3fk0WoLM0mG-VM",
    "id_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6ImpxRnR1X3FFWTY3OFBnTUhqVWRIcSJ9.eyJuaWNrbmFtZSI6Im14c3N0cjkzIiwibmFtZSI6Im14c3N0cjkzQGdtYWlsLmNvbSIsInBpY3R1cmUiOiJodHRwczovL3MuZ3JhdmF0YXIuY29tL2F2YXRhci85YWRkZTQ4YzAwZWU0YjQ2NjcwMzU5ODgyMWVlZjY5ZD9zPTQ4MCZyPXBnJmQ9aHR0cHMlM0ElMkYlMkZjZG4uYXV0aDAuY29tJTJGYXZhdGFycyUyRm14LnBuZyIsInVwZGF0ZWRfYXQiOiIyMDIwLTEyLTI3VDIxOjAyOjA5LjE4N1oiLCJlbWFpbCI6Im14c3N0cjkzQGdtYWlsLmNvbSIsImVtYWlsX3ZlcmlmaWVkIjpmYWxzZSwiaXNzIjoiaHR0cHM6Ly9iZXJsaW4tc2xzLWF1Y3Rpb24uZXUuYXV0aDAuY29tLyIsInN1YiI6ImF1dGgwfDVmZTU1MWQ2MTI4ZjlmMDA2OTljOWIxMSIsImF1ZCI6ImtMWnhyeDNrN25NVk5TUGxaeXJ1T0FFWk9BMjB0SUJJIiwiaWF0IjoxNjA5MTAyOTI5LCJleHAiOjE2MDkxMzg5Mjl9.OLI8okd3kn0YtyQs1gXUslwIkb4N-SJz5zyXVwPs8ofvvOC8GFbKvF6PD_6aQFrCovNvKEJBM8_mEN3tr7J5ASzhzkeg8uvp7McV1zqqxyBGVO9gJKP-_vMBCzD2euWRHrprKLja-eHutiwnYKS86zWTAA_VCvMm3TWdtjpvO_A8w6sNxP7rjslo_JqhZPzAE5uIX_0h_4N2ANC_NPqan8K1Bs6RX1G2ODF_P93TCOQb78fYt4z5tylto2eMcWK4DbYxLB4nJO22tjgv0rTB5ZPnrM5xPXzknIFfx06d8-MUf6y7iKslycxVqfBzXfyEgLsSXPa4xCXTKyYrYIAQoA",
    "scope": "openid profile email address phone",
    "expires_in": 86400,
    "token_type": "Bearer"
}
```

Copy the `id_token` from above and use it as variabke in the `Authorization` Header as follows:

```json
"Authorization": "{{AUTH_TOKEN}}"
```

## How use in AWS lambda serverless services

In the IaC definition of Lambdas services, simply add the `authorizer` ARN of the `auth` function, e.g.

```shell
$ sls info
```

Alternatively, you can get the ARN from the AWS Console.

Example:

```yaml
placeBid:
  handler: src/handlers/placeBid.handler
  events:
    - http:
        method: PATCH
        path: /auctions/{id}/bid
        cors: true
        authorizer: ${self:custom.authorizer}
```
