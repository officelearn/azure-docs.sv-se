---
title: Anropa ett webb-API från en demonapp - Microsoft identity platform | Azure
description: 'Lär dig hur du skapar en daemon app som anropar webb-API: er'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e440628526dada7655cc71f63fc9fff006cc5ef5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885454"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Daemon app som anropar webb-API : er - anropa ett webb-API från appen

.NET daemon apps kan anropa ett webb-API. .NET daemon apps kan också anropa flera förgodkända webb-API: er.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Anropa ett webb-API från ett daemonprogram

Så här använder du token för att anropa ett API:

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="java"></a>[Java](#tab/java)

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

## <a name="calling-several-apis"></a>Anropa flera API:er

För daemon-appar måste de webb-API:er som du anropar godkännas i förväg. Det finns inget inkrementellt samtycke med daemon-appar. (Det finns ingen användarinteraktion.) Klientadministratören måste ge medgivande i förväg för programmet och alla API-behörigheter. Om du vill anropa flera API:er måste du skaffa en `AcquireTokenForClient`token för varje resurs, varje gång du ringer . MSAL använder programtokens cacheminnet för att undvika onödiga serviceanrop.

## <a name="next-steps"></a>Nästa steg

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon app - flytta till produktion](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon app - flytta till produktion](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon app - flytta till produktion](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
