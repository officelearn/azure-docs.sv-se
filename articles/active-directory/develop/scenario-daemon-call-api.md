---
title: Anropa ett webb-API från en daemon-app – Microsoft Identity Platform | Azure
description: 'Lär dig hur du skapar en daemon-app som anropar webb-API: er'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5a234e0b5d3a88b722257aa80249f4d80182d12f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702240"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Daemon-app som anropar webb-API: er – anropa ett webb-API från appen

En daemon-app kan anropa ett webb-API från ett .NET daemon-program eller anropa flera förauktoriserade webb-API: er.

## <a name="calling-a-web-api-daemon-application"></a>Anropa ett daemon-program för webb-API

Så här använder du token för att anropa ett API:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

---

## <a name="calling-several-apis"></a>Anropa flera API: er

Webb-API: er som du anropar måste vara för hands godkända för daemon-appar. Det får inget stegvist godkännande med daemon-appar (det finns inga användar åtgärder). Klient organisationens administratör måste förauktorisera programmet och alla API-behörigheter. Om du vill anropa flera API: er måste du hämta en token för varje resurs, varje tid som anropar `AcquireTokenForClient`. MSAL kommer att använda Application token-cache för att undvika onödiga tjänst anrop.

## <a name="next-steps"></a>Nästa steg

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon-app – flytta till produktion](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon-app – flytta till produktion](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon-app – flytta till produktion](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
