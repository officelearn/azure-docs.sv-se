---
title: 'Webb-API som anropar webb-API: er – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du skapar ett webb-API som anropar webb-API: er.'
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
ms.openlocfilehash: 5829ca41aaa4bd61f8878657e5eedbf6351b5df4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423584"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>Webb-API som anropar webb-API: er – anropa ett API

När du har en token kan du anropa ett skyddat webb-API. Detta görs från kontrollanten för ditt ASP.NET/ASP.NET Core-webbapi.

## <a name="controller-code"></a>Kod för styrenhet

Här är en fortsättning på den exempel kod som visas i de [skyddade webb-API-anropen webb-API: er som hämtar en token](scenario-web-api-call-api-acquire-token.md)som anropas i API-styrenhetens åtgärder, vilket anropar ett underordnat API (med namnet ToDoList)

När du har köpt token ska du använda den som en Bearer-token för att anropa det underordnade API: et.

```csharp
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
