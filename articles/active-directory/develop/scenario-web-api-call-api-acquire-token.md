---
title: 'Hämta en token för ett webb-API som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du skapar ett webb-API som anropar webb-API: er som behöver förvärva en token för appen.'
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
ms.openlocfilehash: 276ff1e5e9f709aa5b38d1efa4055dfe3baf3cc5
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919791"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>Webb-API som anropar webb-API: er – hämta en token för appen

När du har skapat ett klient program objekt kan du använda det för att hämta en token som du kan använda för att anropa ett webb-API.

## <a name="code-in-the-controller"></a>Kod i kontrollanten

Här är ett exempel på kod som kommer att anropas i API-styrenhetens åtgärder, vilket anropar ett underordnat API (med namnet ToDoList).

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

`BuildConfidentialClient()` liknar det du sett i artikeln [webb-API som anropar webb-API: er-app-konfiguration](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` instansierar `IConfidentialClientApplication` med en cache som bara innehåller information för ett konto. Kontot tillhandahålls av metoden `GetAccountIdentifier`.

Metoden `GetAccountIdentifier` använder anspråk som är associerade med identiteten för den användare som webb-API: et tog emot i JWT:

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
> [Anropar ett webb-API](scenario-web-api-call-api-call-api.md)
