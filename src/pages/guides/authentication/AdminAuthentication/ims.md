# Enterprise Web App Credential API Reference 

Following is an API reference for Adobe Identity Management Services (IMS) APIs.

## Consent URL

### Base URL

```text
https://id.adobe.com/consent
```

### Query parameters

| Query Parameter | Required | Description                                                                                                                                                                               | Example                      |
|-----------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| client_id       | Yes      | The Client ID of the partner app from the Enterprise Web App credential overview page.                                                                                                    | abcd1234                     |
| scope           | Yes      | Comma-separated list of scopes you are requesting. Must be a subset of scopes listed on the Enterprise Web App credential overview page.                                                  | openid,AdobeId               |
| state           | Yes      | Cryptographically secure random string generated by the partner app for protection against CSRF attacks.                                                                                  | xyz987                       |
| nonce           | Yes      | Cryptographically secure random string generated by the partner app for protection against replay attacks.                                                                                | nonce123                     |
| redirect_uri    | Optional | The URL to which Adobe should redirect the admin after the consent workflow ends. This URL must match one of the redirect URL parameters configured in the Enterprise Web App credential. | https://example.com/redirect |


## Redirect URL 

### Query paramters

| Query Parameter | Always present | Description                                                                                                                                                        | Possible values                                     |
|-----------------|----------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------|
| admin_consent   | No             | Denotes whether the admin consented to the partner app or not. This parameter is not present in the redirect if there was an error during the consent workflow     | true, false                                         |
| error           | No             | Denotes the error which occurred during the consent workflow. This parameter is only present if there was an error in the redirect workflow.                        | See table below for all possible values.            |
| state           | Yes            | Adobe echoes back the value of the state parameter you supplied                                                                                                     | Same as the value you supplied in the consent URL.  |
| id_token        | No             | Adobe provides an id token to enable the partner app to generate access tokens. This parameter is only present if the admin provided consent to your application.  | A well formed JSON web token.                       |

### Error codes


| Error Code                |  Description                                                                                                                                              |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| invalid_scopes            |  Returned when the partner app requests one or more scopes that are not available to it.                                                                  |
| invalid_redirect_uri      |  Returned when the redirect URI provided in the consent URL does not match the redirect URL pattern configured on the Enterprise Web App credential.      |
| insufficient_privilege    |  Returned when the logged in user is not an admin and does not have the appropriate role to grant consent to the partner app.                             |
| incompatible_account_type |  Returned when the logged in user account is not a company or school account. Note: only company or school accounts can provide consent to a partner app. |
| missing_state_param       |  Returned when the partner app did not provide a state query parameter in the consent URL.                                                                 |
| error                     |  Returned when an unknown error occurred.


## Generate Access Token Request

### cURL Request 

```cURL
curl -X POST 'https://ims-na1.adobelogin.com/ims/token/v3'
     -H 'Content-Type: application/x-www-form-urlencoded'
     -d 'grant_type=client_credentials'
     -d 'client_id=<YOUR_CLIENT_ID>'
     -d 'client_secret=<YOUR_CLIENT_SECRET>'
     -d 'scope=<COMMA_SEPARATED_SCOPES>'
     -d 'org_id=<ORG_ID_EXTRACTED_FROM_A_VERIFIED_ID_TOKEN>'
```

### Sample response
```JSON
{
    "access_token": "ey.....",
    "expires_in": 3599
}
``` 