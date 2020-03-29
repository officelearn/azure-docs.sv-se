---
title: Azure AD B2C (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om specifika överväganden när du använder Azure AD B2C med Microsoft Authentication Library for Android (MSAL. Android)
services: active-directory
author: brianmel
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.openlocfilehash: 0998bb04b0dfc69db4696f2e390cfe259eba6718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696529"
---
# <a name="use-msal-for-android-with-b2c"></a>Använda MSAL för Android med B2C

Med MSAL (Microsoft Authentication Library) kan programutvecklare autentisera användare med sociala och lokala identiteter med hjälp av [Azure Active Directory B2C (Azure AD B2C).](https://docs.microsoft.com/azure/active-directory-b2c/) Azure AD B2C är en identitetshanteringstjänst. Använd den för att anpassa och styra hur kunder registrerar sig, loggar in och hanterar sina profiler när de använder dina program.

## <a name="configure-known-authorities-and-redirect-uri"></a>Konfigurera kända myndigheter och omdirigera URI

I MSAL för Android konfigureras B2C-principer (användarresor) som enskilda myndigheter.

Med tanke på ett B2C-program som har två principer:
- Anmälan / Inloggning
    * Kallas`B2C_1_SISOPolicy`
- Redigera profil
    * Kallas`B2C_1_EditProfile`

Konfigurationsfilen för appen deklarerar två `authorities`. En för varje policy. Varje `type` myndighets egendom `B2C`är .

### `app/src/main/res/raw/msal_config.json`
```json
{
    "client_id": "<your_client_id_here>",
    "redirect_uri": "<your_redirect_uri_here>",
    "authorities": [{
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/",
            "default": true
        },
        {
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_EditProfile/"
        }
    ]
}
```

Måste `redirect_uri` registreras i appkonfigurationen och `AndroidManifest.xml` även för att stödja omdirigering under [tilldelningskodsbidragsflödet](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code).

## <a name="initialize-ipublicclientapplication"></a>Initiera IPublicClientApplication

`IPublicClientApplication`konstrueras med en fabriksmetod så att programkonfigurationen kan tolkas asynkront.

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(
    context, // Your application Context
    R.raw.msal_config, // Id of app JSON config
    new IPublicClientApplication.ApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication pca) {
            // Application has been initialized.
        }

        @Override
        public void onError(MsalException exception) {
            // Application could not be created.
            // Check Exception message for details.
        }
    }
);
```

## <a name="interactively-acquire-a-token"></a>Skaffa en token interaktivt

Om du vill hämta en token `AcquireTokenParameters` interaktivt med MSAL skapar du en instans och tillhandahåller den `acquireToken` till metoden. Tokenbegäran nedan använder `default` myndigheten.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }

        @Override
        public void onCancel() {
            // The user cancelled the flow
        }
    }).build();

pca.acquireToken(parameters);
```

## <a name="silently-renew-a-token"></a>Förnya en token tyst

Om du vill hämta en token `AcquireTokenSilentParameters` tyst med MSAL `acquireTokenSilentAsync` skapar du en instans och tillhandahåller den till metoden. Till `acquireToken` skillnad från `authority` metoden måste det anges för att hämta en token tyst.

```java
IMultilpeAccountPublicClientApplication pca = ...; // Initialization not shown
AcquireTokenSilentParameters parameters = new AcquireTokenSilentParameters.Builder()
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .forAccount(account)
    // Select a configured authority (policy), mandatory for silent auth requests
    .fromAuthority("https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/")
    .callback(new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccesful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>Ange en princip

Eftersom principer i B2C representeras som separata myndigheter uppnås en annan princip `fromAuthority` än standardinställningen genom att ange en sats när du `acquireToken` skapar eller `acquireTokenSilent` parametrar.  Ett exempel:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Hantera principer för ändring av lösenord

Det lokala kontouppskrivnings- eller inloggningsanvändarflödet visar ett "**Glömt lösenord?**" Länk. Om du klickar på den här länken utlöses inte ett användarflöde för återställning av lösenord automatiskt.

I stället returneras felkoden `AADB2C90118` till din app. Appen bör hantera den här felkoden genom att köra ett visst användarflöde som återställer lösenordet.

För att fånga upp en felkod för återställning `AuthenticationCallback`av lösenord kan följande implementering användas i din:

```java
new AuthenticationCallback() {

    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // ..
    }

    @Override
    public void onError(MsalException exception) {
        final String B2C_PASSWORD_CHANGE = "AADB2C90118";

        if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
            // invoke password reset flow
        }
    }

    @Override
    public void onCancel() {
        // ..
    }
}
```

## <a name="use-iauthenticationresult"></a>Använd IAuthenticationResult

En lyckad tokenförvärv resulterar i ett `IAuthenticationResult` objekt. Den innehåller åtkomsttoken, användaranspråk och metadata.

### <a name="get-the-access-token-and-related-properties"></a>Hämta åtkomsttoken och relaterade egenskaper

```java
// Get the raw bearer token
String accessToken = authenticationResult.getAccessToken();

// Get the scopes included in the access token
String[] accessTokenScopes = authenticationResult.getScope();

// Gets the access token's expiry
Date expiry = authenticationResult.getExpiresOn();

// Get the tenant for which this access token was issued
String tenantId = authenticationResult.getTenantId();
```

### <a name="get-the-authorized-account"></a>Skaffa det auktoriserade kontot

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>IdToken-anspråk

Anspråk som returneras i IdToken fylls i av Security Token Service (STS), inte av MSAL. Beroende på vilken identitetsleverantör (IdP) som används kan vissa anspråk vara frånvarande. Vissa IdPs tillhandahåller för `preferred_username` närvarande inte anspråket. Eftersom det här anspråket används av MSAL för `MISSING FROM THE TOKEN RESPONSE`cachelagring används ett platshållarvärde, i stället. Mer information om B2C IdToken-anspråk finns [i Översikt över token i Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims).

## <a name="managing-accounts-and-policies"></a>Hantera konton och principer

B2C behandlar varje policy som en separat myndighet. Åtkomsttoken, uppdateringstoken och ID-token som returneras från varje princip är därför inte utbytbara. Det innebär att varje `IAccount` princip returnerar ett separat objekt vars token inte kan användas för att anropa andra principer.

Varje princip `IAccount` lägger till en i cacheminnet för varje användare. Om en användare loggar in på ett program och anropar två principer har de två `IAccount`s. Om du vill ta bort den `removeAccount()` här användaren från cacheminnet måste du anropa för varje princip.

När du förnyar token `acquireTokenSilent`för en `IAccount` princip med anger du samma som returnerades `AcquireTokenSilentParameters`från tidigare anrop av principen till . Om du tillhandahåller ett konto som returneras av en annan policy kommer det att resultera i ett fel.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Active Directory B2C (Azure AD B2C) på [Vad är Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
