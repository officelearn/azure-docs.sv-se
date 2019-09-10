---
title: 'Webbapp som anropar webb-API: er (hämtar en token för appen) – Microsoft Identity Platform'
description: 'Lär dig hur du skapar en webbapp som anropar webb-API: er (hämtar en token för appen)'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3aa144c76fb0a8e479658efdb5d43361fbbc085c
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860618"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Webbapp som anropar webb-API: er – hämta en token för appen

Nu när du har skapat ett klient program objekt använder du det för att hämta en token för att anropa ett webb-API. Anropa ett webb-API i ASP.NET eller ASP.NET Core och sedan utföras i kontrollanten. Det är ungefär:

- Hämta en token för webb-API: et med hjälp av token cache. Om du vill hämta den här token anropar `AcquireTokenSilent`du.
- Anropar det skyddade API: t med åtkomsttoken.

## <a name="aspnet-core"></a>ASP.NET Core

Styrenhets metoderna skyddas av ett `[Authorize]` attribut som tvingar användare att autentiseras att använda webbappen. Här är den kod som anropar Microsoft Graph.

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

Här är en förenklad kod för åtgärden för HomeController, som hämtar en token för att anropa Microsoft Graph.

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;
 ...
 // use the access token to call a web API
}
```

Information om den kod som krävs för det här scenariot finns i steg 2 ([2-1 – webapps-anrop Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) i själv studie kursen [MS-Identity-aspnetcore-webapp – självstudie](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

Det finns många ytterligare komplicerade faktorer, till exempel:

- Implementera en token-cache för webbappen (självstudien visar flera implementeringar)
- Ta bort kontot från cachen när användaren loggar ut
- Anropa flera API: er, inklusive ett stegvist godkännande

## <a name="aspnet"></a>ASP.NET

Saker liknar varandra i ASP.NET:

- En styrenhets åtgärd som skyddas av ett [auktorisera]-attribut extraherar klient-ID och `ClaimsPrincipal` användar-ID för kontrollantens medlem. (ASP.NET använder `HttpContext.User`.)
- Därifrån skapar den en MSAL.NET `IConfidentialClientApplication`.
- Slutligen anropar `AcquireTokenSilent` den metoden för det konfidentiella klient programmet.

Koden liknar den kod som visas för ASP.NET Core.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Anropa en webb-API](scenario-web-app-call-api-call-api.md)
