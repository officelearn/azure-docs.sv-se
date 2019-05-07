---
title: 'Webbapp som anropar webb-API: er (hämta en token för appen) – Microsoft identity-plattformen'
description: 'Lär dig att skapa en webbapp som anropar webb-API: er (skaffa en token som appen)'
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 653db995000308bb3ef78a9183696cd9d8ed1056
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074807"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Webbapp som anropar webb-API: er – hämta en token för appen

Nu du har skapat klienten programobjektet kan använder du den för att hämta en token som du sedan använder för att anropa ett webb-API: er. Anropa ett webb-API: et görs sedan i controller i ASP.NET eller ASP.NET Core. Det handlar om:

- Få en token för webb-API med hjälp av token-cache. För att detta måste anropa du `AcquireTokenSilent`
- Anropa skyddade API med åtkomsttoken

## <a name="aspnet-core"></a>ASP.NET Core

Kontrollantmetoder skyddas av en `[Authorize]` attribut som tvingar de användare som också autentiseras för att använda Web-App. Här är den kod som anropar Microsoft Graph

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

Här är en förenklad åtgärden av HomeController som hämtar en token för att anropa Microsoft Graph-kod.

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

Om du vill förstå i informationen om summan av den kod som krävs för det här scenariot finns i fas 2 [2-1-Web App anropar Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph) steg i den [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) självstudien

Det finns ett antal ytterligare komplexitet som:

- implementera en token-cache för Webbappen (självstudien presentera flera implementeringar)
- Ta bort kontot från cachen när användaren loggar ut
- Anropa flera API: er, inklusive utan inkrementella samtycke

## <a name="aspnet"></a>ASP.NET

Saker liknar varandra i ASP.NET:

- En åtgärd för domänkontrollant som skyddas av attributet [auktorisera] extraherar klient-ID och användar-ID för den `ClaimsPrincipal` medlem kontrollanten (ASP.NET använder `HttpContext.User`)
- därifrån bygger den en MSAL.NET `IConfidentialClientApplication`
- IT-anrop i `AcquireTokenSilent` -metoden för konfidentiell klientprogrammet 

koden liknar koden som visas för ASP.NET Core

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Anropa en webb-API](scenario-web-app-call-api-call-api.md)
