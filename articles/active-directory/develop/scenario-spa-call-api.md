---
title: Bygg en app med en sida som anropar ett webb-API – Microsoft Identity Platform | Azure
description: Lär dig hur du skapar ett program med en enda sida som anropar ett webb-API
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
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160006"
---
# <a name="single-page-application-call-a-web-api"></a>Program med en sida: anropa ett webb-API

Vi rekommenderar att du anropar `acquireTokenSilent`-metoden för att inhämta eller förnya en åtkomsttoken innan du anropar ett webb-API. När du har en token kan du anropa ett skyddat webb-API.

## <a name="call-a-web-api"></a>Anropa en webb-API

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Använd den hämtade åtkomsttoken som en innehavare i en HTTP-begäran för att anropa alla webb-API: er, till exempel Microsoft Graph-API. Några exempel:

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

# <a name="angulartabangular"></a>[Angular](#tab/angular)

MSAL vinkel omslutningen drar nytta av HTTP-spärren för att automatiskt hämta åtkomsttoken och koppla dem till HTTP-begäranden till API: er. Mer information finns i [Hämta en token för att anropa ett API](scenario-spa-acquire-token.md).

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-spa-production.md)
