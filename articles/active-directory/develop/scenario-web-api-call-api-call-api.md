---
title: 'Webb-API som anropar webb-API: er - Microsoft identity platform | Azure'
description: Lär dig hur du skapar ett webb-API som anropar webb-API:er.
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
ms.openlocfilehash: d66a08d4e84a3771d6c3fa46b96c975869435452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76833387"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Ett webb-API som anropar webb-API:er: Anropa ett API

När du har en token kan du anropa ett skyddat webb-API. Du gör detta från styrenheten för webb-API: et.

## <a name="controller-code"></a>Controller-kod

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Följande kod fortsätter den exempelkod som visas i [ett webb-API som anropar webb-API:er: Hämta en token för appen](scenario-web-api-call-api-acquire-token.md). Koden anropas i åtgärderna för API-styrenheterna. Den anropar ett nedströms-API med namnet *todolist*.

När du har skaffat token använder du den som en innehavartoken för att anropa api:et nedströms.

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

# <a name="java"></a>[Java](#tab/java)

Följande kod fortsätter den exempelkod som visas i [ett webb-API som anropar webb-API:er: Hämta en token för appen](scenario-web-api-call-api-acquire-token.md). Koden anropas i åtgärderna för API-styrenheterna. Den anropar API MS Graph nedströms.

När du har skaffat token använder du den som en innehavartoken för att anropa api:et nedströms.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
Ett exempel som visar det här flödet med MSAL Python är ännu inte tillgängligt.

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ett webb-API som anropar webb-API:er: Flytta till produktion](scenario-web-api-call-api-production.md)
