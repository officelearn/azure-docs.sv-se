---
title: 'Webbapp som anropar webb-API: er (anropa en webb-API) - Microsoft identity-plattformen'
description: 'Lär dig att skapa en webbapp som anropar webb-API: er (anropa en webb-API)'
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
ms.openlocfilehash: dd44dda06b2f6fc48538f2fb74c0bf8e04d0362b
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074642"
---
# <a name="web-app-that-calls-web-apis---call-a-web-api"></a>Webbapp som anropar webb-API: er – anropa ett webb-API

Nu när du har en token kan du anropa ett skyddat webb-API.

## <a name="aspnet-core"></a>ASP.NET Core

Här är en förenklad kod åtgärd av den `HomeController`. Den här koden hämtar en token för att anropa Microsoft Graph. Den här tiden koden har lagts till, som visar hur du anropar Microsoft Graph som en REST-API.

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

 // Calls the web API (here the graph)
 HttpClient httpClient = new HttpClient();
 httpClient.DefaultRequestHeaders.Authorization =
     new AuthenticationHeaderValue(Constants.BearerAuthorizationScheme,accessToken);
 var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

 if (response.StatusCode == HttpStatusCode.OK)
 {
  var content = await response.Content.ReadAsStringAsync();

  dynamic me = JsonConvert.DeserializeObject(content);
  return me;
 }

 ViewData["Me"] = me;
 return View();
}
```

> [!NOTE]
> Du kan använda samma princip för att anropa alla webb-API.
>
> De flesta Azure web API: er ger ett SDK som förenklar anropas. Detta gäller även för Microsoft Graph. I nästa artikel lär du dig var du hittar en självstudie som visar dessa aspekter.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-web-app-call-api-production.md)
