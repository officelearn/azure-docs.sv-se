---
title: Skapa ensidig app som anropar ett webb-API – Microsoft identity platform | Azure
description: Lär dig hur du skapar ett ensidigt program som anropar ett webb-API
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: ccece6c840033913ec6d96b446dcb98c4befb32f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160006"
---
# <a name="single-page-application-call-a-web-api"></a>Ensidigt program: Anropa ett webb-API

Vi rekommenderar att `acquireTokenSilent` du anropar metoden för att hämta eller förnya en åtkomsttoken innan du anropar ett webb-API. När du har en token kan du anropa ett skyddat webb-API.

## <a name="call-a-web-api"></a>Anropa en webb-API

# <a name="javascript"></a>[Javascript](#tab/javascript)

Använd den förvärvade åtkomsttoken som bärare i en HTTP-begäran för att anropa webb-API, till exempel Microsoft Graph API. Ett exempel:

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

# <a name="angular"></a>[Angular](#tab/angular)

MSAL-vinkelomslaget utnyttjar HTTP-interceptor för att automatiskt hämta åtkomsttoken tyst och bifoga dem till HTTP-begäranden till API:er. Mer information finns i [Hämta en token för att anropa ett API](scenario-spa-acquire-token.md).

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-spa-production.md)
