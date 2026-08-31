# Microsoft Entra ID Token Claims Sample

This Vanilla JavaScript single-page application signs users in with Microsoft Entra ID by using MSAL Browser. After a successful sign-in, it displays the claims from the signed-in account's ID token in a table.

## What This Sample Does

- Signs in a workforce-tenant user with either a popup or redirect interaction.
- Uses MSAL Browser, which implements the OAuth 2.0 authorization code flow with PKCE for SPAs.
- Processes the authorization response with `handleRedirectPromise()`.
- Stores the signed-in account as the active account.
- Reads `account.idTokenClaims` and renders supported claims, values, and descriptions in the page.
- Signs the active account out with either a popup or redirect interaction.

The app does not use the legacy OAuth implicit flow. MSAL Browser handles the authorization-code and PKCE protocol details; the application consumes the resulting authenticated account and its ID-token claims.

## Prerequisites

- Node.js 18 or later.
- A Microsoft Entra workforce tenant and a user in that tenant.
- A single-page application registration in the tenant.

## Configure Microsoft Entra ID

In the Microsoft Entra admin center, register a **Single-page application** and add this redirect URI:

```text
http://localhost:3000/redirect
```

Update [public/authConfig.js](public/authConfig.js) with the registration values:

```javascript
const msalConfig = {
    auth: {
        clientId: "YOUR_APPLICATION_CLIENT_ID",
        authority: "https://login.microsoftonline.com/YOUR_TENANT_ID",
        redirectUri: "http://localhost:3000/redirect"
    }
};
```

The default `loginRequest` requests `User.Read`. MSAL also adds the OpenID Connect scopes needed to receive an ID token.

## Run the Sample

From this `App` directory, install dependencies and start the local Express server:

```console
npm install
npm start
```

Open `http://localhost:3000`, select **Sign In**, and choose either the popup or redirect experience. On successful authentication, the page shows the signed-in username and an ID-token claim table.

## Where the Flow Lives

| File | Responsibility |
| --- | --- |
| [public/authConfig.js](public/authConfig.js) | MSAL, tenant, redirect URI, cache, logging, and requested-scope configuration. |
| [public/auth.js](public/auth.js) | Creates and initializes MSAL, starts sign-in, processes the authorization response, selects the active account, and signs out. |
| [public/ui.js](public/ui.js) | Displays the signed-in user and passes `account.idTokenClaims` to the table renderer. |
| [public/claimUtils.js](public/claimUtils.js) | Maps known ID-token claims to readable descriptions and formats timestamp claims. |
| [server.js](server.js) | Serves the SPA, the redirect page, and the local MSAL Browser library. |

## Test

Run the Jest test command from this directory:

```console
npm test
```

## Security Notes

- Do not add a client secret to this SPA. Public browser clients cannot keep secrets confidential.
- Treat the displayed claims as client-side UI data. A protected API must validate access tokens independently before authorizing requests.
- Use stable identifiers such as `oid` together with `tid` for tenant-scoped user identity; do not authorize users based on mutable display claims such as `name`, `email`, or `preferred_username`.