---
title: 'Webb-API att anrop andra web API: er (hämta en token för appen) – Microsoft identity-plattformen'
description: 'Lär dig hur du skapar ett webb-API att anrop andra webb-API: er (skaffa en token som appen).'
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
ms.openlocfilehash: 986e2e0f8a481d61dc870af2548290658b44d2d3
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231111"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>Webb-API som anropar webb-API: er – hämta en token för appen

När du har byggt ett klientprogram objekt, använda den för att hämta en token som du kan använda för att anropa ett webb-API.

## <a name="code-in-the-controller"></a>Koda i kontrollanten

Här är ett exempel på kod som anropas i åtgärderna för API-styrenheter och anropa en underordnad API (med namnet todolist).

```CSharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...
}
```

`BuildConfidentialClient()` liknar vad du har sett i artikeln [webb-API som anropar webb-API: er – appkonfiguration](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` skapar en instans av `IConfidentialClientApplication` med ett cacheminne som innehåller endast information för ett konto. Konton som tillhandahålls av den `GetAccountIdentifier` metoden.

Den `GetAccountIdentifier` metoden använder de anspråk som är associerade med identiteten för användaren som fick webb-API i JWT:

```CSharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Anropa ett webb-API](scenario-web-api-call-api-call-api.md)
