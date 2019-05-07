---
title: 'Webb-API som anropar webb-API: er (anropa API: er) - Microsoft identity-plattformen'
description: 'Lär dig hur du skapar ett webb-API som anrop underordnat webb API: er (anropa en webb-API).'
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
ms.openlocfilehash: 1cd093cc68a21558dc326221eeaa8c034c24f1c2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074732"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>Webb-API som anropar webb-API: er – anropa ett API

När du har en token kan anropa du ett skyddat webb-API. Detta görs från styrenheten för din ASP.NET/ASP.NET Core webb-API.

## <a name="controller-code"></a>Kontrollantkoden

Här är fortsättningen på kodexemplet i [skyddade webb-API-anrop webb-API: er – en token hämtades](scenario-web-api-call-api-acquire-token.md), kallas i åtgärderna för API-styrenheter, anropa en underordnad API (med namnet todolist).

När du har köpt token, använda en ägartoken för att anropa API: et som underordnade.

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

// Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-web-api-call-api-production.md)
