---
title: Cachelagra autentiseringstoken
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du cachelagrar autentiseringstoken.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: e652aa29b1c1935fcc4887dbe13ef9b683a8bd05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946170"
---
# <a name="how-to-cache-the-authentication-token"></a>Cachelagra autentiseringstoken

Den här artikeln visar hur du cachelagrar autentiseringstoken för att förbättra programmets prestanda.

## <a name="using-aspnet"></a>Använda ASP.NET

Importera **paketet Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet, som används för att hämta en token. Använd sedan följande kod för `AuthenticationResult`att hämta en med hjälp av de autentiseringsvärden som du fick när du [skapade Immersive Reader-resursen](./how-to-create-immersive-reader.md).

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

Objektet `AuthenticationResult` har `AccessToken` en egenskap som är den faktiska token som du ska använda när du startar Immersive Reader med SDK. Den har `ExpiresOn` också en egenskap som anger när token upphör att gälla. Innan du startar Immersive Reader kan du kontrollera om token har upphört att gälla och endast hämta en ny token om den har upphört att gälla.

## <a name="using-nodejs"></a>Använda nod.JS

Lägg till [**paketet för begäran**](https://www.npmjs.com/package/request) npm i projektet. Använd följande kod för att hämta en token med hjälp av de autentiseringsvärden du fick när du [skapade Immersive Reader-resursen](./how-to-create-immersive-reader.md).

```javascript
router.get('/token', function(req, res) {
    request.post(
        {
            headers: { 'content-type': 'application/x-www-form-urlencoded' },
            url: `https://login.windows.net/${TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: CLIENT_ID,
                client_secret: CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, json) {
            const result = JSON.parse(json);
            return res.send({
                access_token: result.access_token,
                expires_on: result.expires_on
            });
        }
    );
});
```

Egenskapen `expires_on` är det datum och den tid då token upphör att gälla, uttryckt som antalet sekunder sedan den 1 januari 1970 UTC. Använd det här värdet för att avgöra om din token har upphört att gälla innan du försöker skaffa en ny.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>Nästa steg

* Utforska [den uppslukande läsar-SDK-referensen](./reference.md)