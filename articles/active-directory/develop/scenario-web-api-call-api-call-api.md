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
ms.openlocfilehash: c7c54c7c4718cea479a812574e961ef2338a6be6
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701764"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Ett webb-API som anropar webb-API: er: anropa ett API

När du har en token kan du anropa ett skyddat webb-API. Du gör detta från styrenheten för din ASP.NET eller ASP.NET Core webb-API.

## <a name="controller-code"></a>Kod för styrenhet

Följande kod fortsätter med exempel koden som visas i [ett webb-API som anropar webb-API: er: Hämta en token för appen](scenario-web-api-call-api-acquire-token.md). Koden anropas i API-styrenhetens åtgärder. Den anropar ett underordnat API med namnet *ToDoList*.

När du har skaffat token ska du använda den som en Bearer-token för att anropa det underordnade API: et.

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

// After the token has been returned by Microsoft Authentication Library (MSAL), add it to the HTTP authorization header before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ett webb-API som anropar webb-API: er: flytta till produktion](scenario-web-api-call-api-production.md)
