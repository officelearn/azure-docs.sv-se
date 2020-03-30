---
title: Migrera program och API:er till b2clogin.com
titleSuffix: Azure AD B2C
description: Lär dig mer om hur du använder b2clogin.com i omdirigeringsadresser för Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 64b440054795670b99a22e37dec7188f3e1cd74c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189998"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Ange omdirigeringsadresser till b2clogin.com för Azure Active Directory B2C

När du konfigurerar en identitetsprovider för registrering och inloggning i ditt Azure Active Directory B2C-program (Azure AD B2C) måste du ange en omdirigerings-URL. Du bör inte längre referera *login.microsoftonline.com* i dina program och API:er. Använd i stället *b2clogin.com* för alla nya program och migrera befintliga program från *login.microsoftonline.com* till *b2clogin.com*.

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Utfasning av login.microsoftonline.com

Den 4 december 2019 meddelade vi att supporten för login.microsoftonline.com i Azure AD B2C ska gå i pension den **4 december 2020:**

[Azure Active Directory B2C är inaktuell login.microsoftonline.com](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

Utfasningen av login.microsoftonline.com träder i kraft för alla Azure AD B2C-klienter den 4 december 2020, vilket ger befintliga klienter ett (1) år att migrera till b2clogin.com. Nya klienter som skapats efter den 4 december 2019 tar inte emot förfrågningar från login.microsoftonline.com. Alla funktioner förblir desamma på b2clogin.com slutpunkten.

Utfasningen av login.microsoftonline.com påverkar inte Azure Active Directory-klienter. Endast Azure Active Directory B2C-klienter påverkas av den här ändringen.

## <a name="benefits-of-b2clogincom"></a>Fördelar med b2clogin.com

När du använder *b2clogin.com* som omdirigerings-URL:

* Utrymmet som förbrukas i cookie-huvudet av Microsoft-tjänster minskas.
* Dina omdirigeringsadresser behöver inte längre innehålla en referens till Microsoft.
* JavaScript-kod stöds (för närvarande i [förhandsversion)](user-flow-javascript-overview.md)på anpassade sidor. På grund av säkerhetsbegränsningar tas JavaScript-kod och HTML-formulärelement bort från anpassade sidor om du använder *login.microsoftonline.com*.

## <a name="overview-of-required-changes"></a>Översikt över nödvändiga ändringar

Det finns flera ändringar som du kan behöva göra för att migrera dina program till *b2clogin.com:*

* Ändra omdirigeringsadressen i identitetsleverantörens program för att *referera*b2clogin.com .
* Uppdatera dina Azure AD B2C-program för att använda *b2clogin.com* i sina användarflödes- och tokenslutpunktsreferenser.
* Uppdatera alla **tillåtna origin-enheter** som du har definierat i CORS-inställningarna för [anpassning av användargränssnittet](custom-policy-ui-customization.md).

## <a name="change-identity-provider-redirect-urls"></a>Ändra url:er för omdirigering av identitetsprovider

På varje identitetsproviders webbplats där du har skapat ett program `your-tenant-name.b2clogin.com` ändrar du alla betrodda webbadresser så att de omdirigeras till i stället för *login.microsoftonline.com*.

Det finns två format som du kan använda för dina b2clogin.com omdirigera webbadresser. Den första ger fördelen av att inte ha "Microsoft" visas någonstans i WEBBADRESSEN med hjälp av klient-ID (ett GUID) i stället för din klient domännamn:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

Det andra alternativet använder ditt klientdomännamn i form av `your-tenant-name.onmicrosoft.com`. Ett exempel:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

För båda formaten:

* Ersätt `{your-tenant-name}` med namnet på din Azure AD B2C-klient.
* Ta `/te` bort om den finns i webbadressen.

## <a name="update-your-applications-and-apis"></a>Uppdatera dina program och API:er

Koden i dina Azure AD B2C-aktiverade program `login.microsoftonline.com` och API:er kan referera till på flera ställen. Koden kan till exempel ha referenser till användarflöden och tokenslutpunkter. Uppdatera följande för `your-tenant-name.b2clogin.com`att istället referera:

* Slutpunkt för auktorisering
* Tokenslutpunkt
* Tokenutfärdare

Till exempel skulle auktoritetsslutpunkten för Contosos registrerings-/inloggningsprincip nu vara:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

Information om hur du migrerar OWIN-baserade webbprogram till b2clogin.com finns i [Migrera ett OWIN-baserat webb-API till b2clogin.com](multiple-token-endpoints.md).

För migrera Azure API Management API:er som skyddas av Azure AD B2C, se [migrera till b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) avsnittet i [Secure an Azure API Management API med Azure AD B2C](secure-api-management.md).

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="validateauthority-property"></a>Egenskapen ValidateAuthority

Om du använder [MSAL.NET][msal-dotnet] v2 eller tidigare ställer du in egenskapen `false` **ValidateAuthority** på klient instantiation så att omdirigeringar kan *b2clogin.com*. Den här inställningen krävs inte för MSAL.NET v3 och högre.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

Om du använder [MSAL för JavaScript:][msal-js]

```JavaScript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

## <a name="next-steps"></a>Nästa steg

Information om hur du migrerar OWIN-baserade webbprogram till b2clogin.com finns i [Migrera ett OWIN-baserat webb-API till b2clogin.com](multiple-token-endpoints.md).

För migrera Azure API Management API:er som skyddas av Azure AD B2C, se [migrera till b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) avsnittet i [Secure an Azure API Management API med Azure AD B2C](secure-api-management.md).

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
