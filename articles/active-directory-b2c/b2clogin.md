---
title: 'Ange omdirigerings-URL: er till b2clogin.com-Azure Active Directory B2C'
description: 'Läs om hur du använder b2clogin.com i dina omdirigerings-URL: er för Azure Active Directory B2C.'
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: dbc366daac89f44d4b084081590124f81ff9cc9c
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533742"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Ange omdirigerings-URL: er till b2clogin.com för Azure Active Directory B2C

När du konfigurerar en identitets leverantör för registrering och inloggning i ditt Azure Active Directory B2C (Azure AD B2C) måste du ange en omdirigerings-URL. Du bör inte längre referera till *login.microsoftonline.com* i dina program och API: er. Använd i stället *b2clogin.com* för alla nya program och migrera befintliga program från *login.microsoftonline.com* till *b2clogin.com*.

## <a name="benefits-of-b2clogincom"></a>Fördelar med b2clogin.com

När du använder *b2clogin.com* som omdirigerings-URL:

* Förbrukat utrymme i cookie-huvudet av Microsoft-tjänster minskas.
* Dina omdirigerings-URL: er behöver inte längre innehålla en referens till Microsoft.
* Java Script-kod på klient sidan stöds (för närvarande i för [hands version](user-flow-javascript-overview.md)) på anpassade sidor. På grund av säkerhets begränsningar tas JavaScript-kod och HTML-formulär element bort från anpassade sidor om du använder *login.microsoftonline.com*.

## <a name="overview-of-required-changes"></a>Översikt över nödvändiga ändringar

Det finns flera ändringar som du kan behöva göra för att migrera dina program till *b2clogin.com*:

* Ändra omdirigerings-URL: en i din Identitetsproviders program till Reference *b2clogin.com*.
* Uppdatera dina Azure AD B2C program så att de använder *b2clogin.com* i sina användar flöden och token slut punkts referenser.
* Uppdatera eventuella **tillåtna ursprung** som du har DEFINIERAT i CORS-inställningarna för [anpassning av användar gränssnitt](active-directory-b2c-ui-customization-custom-dynamic.md).

## <a name="change-identity-provider-redirect-urls"></a>Ändra omdirigerings-URL: er för identitetsprovider

På varje identitets leverantörs webbplats där du har skapat ett program, ändra alla betrodda URL: er som ska `your-tenant-name.b2clogin.com` omdirigeras till i stället för *login.microsoftonline.com*.

Det finns två format som du kan använda för dina b2clogin.com-omdirigerings-URL: er. Det första ger fördelen att du inte har "Microsoft" visas var som helst i URL: en genom att använda klient-ID: t (en GUID) i stället för domän namnet för din klient organisation:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

Det andra alternativet använder klient domän namnet i form av `your-tenant-name.onmicrosoft.com`. Exempel:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

För båda formaten:

* Ersätt `{your-tenant-name}` med namnet på din Azure AD B2C-klient.
* Ta `/te` bort om den finns i URL: en.

## <a name="update-your-applications-and-apis"></a>Uppdatera dina program och API: er

Koden i dina Azure AD B2C-aktiverade program och API: er kan referera `login.microsoftonline.com` till på flera platser. Din kod kan till exempel innehålla referenser till användar flöden och token-slutpunkter. Uppdatera följande referens `your-tenant-name.b2clogin.com`till i stället:

* Auktoriseringsslutpunkt
* Tokenslutpunkt
* Tokenutfärdare

Till exempel skulle auktoritets slut punkten för Contosos registrerings-/inloggnings princip bli:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="validateauthority-property"></a>ValidateAuthority-egenskap

Om du använder [MSAL.net][msal-dotnet] v2 eller tidigare anger du egenskapen **ValidateAuthority** till `false` på klientens instansiering för att tillåta omdirigering till *b2clogin.com*. Den här inställningen krävs inte för MSAL.NET v3 eller senare.

```CSharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

Om du använder [MSAL för Java Script][msal-js]:

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

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md