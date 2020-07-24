---
title: 'Webb-API som anropar webb-API: er – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du skapar ett webb-API som anropar webb-API: er.'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b756d7df03bd3c06b703617dbf84a194d716f1e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026381"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Ett webb-API som anropar webb-API: er: anropa ett API

När du har en token kan du anropa ett skyddat webb-API. Du gör detta från kontrollanten för ditt webb-API.

## <a name="controller-code"></a>Kod för styrenhet

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Följande kod fortsätter med exempel koden som visas i [ett webb-API som anropar webb-API: er: Hämta en token för appen](scenario-web-api-call-api-acquire-token.md). Koden anropas i API-styrenhetens åtgärder. Den anropar ett underordnat API med namnet *ToDoList*.

När du har skaffat token ska du använda den som en Bearer-token för att anropa det underordnade API: et.

```csharp
private async Task CallTodoListService(string accessToken)
{

// After the token has been returned by Microsoft Identity Web, add it to the HTTP authorization header before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

# <a name="java"></a>[Java](#tab/java)

Följande kod fortsätter med exempel koden som visas i [ett webb-API som anropar webb-API: er: Hämta en token för appen](scenario-web-api-call-api-acquire-token.md). Koden anropas i API-styrenhetens åtgärder. Den anropar det underordnade API MS-diagrammet.

När du har skaffat token ska du använda den som en Bearer-token för att anropa det underordnade API: et.

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
Ett exempel som demonstrerar det här flödet med MSAL python är ännu inte tillgängligt.

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ett webb-API som anropar webb-API: er: flytta till produktion](scenario-web-api-call-api-production.md)
