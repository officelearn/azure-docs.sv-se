---
title: Program med en sida (anropa ett webb-API) – Microsoft Identity Platform
description: Lär dig hur du skapar ett program med en sida (anropa ett webb-API)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4170a6642d35802581b5d1ff28eb802a6eb3482b
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766165"
---
# <a name="single-page-application-call-a-web-api"></a>Program med en sida: anropa ett webb-API

Vi rekommenderar att du anropar `acquireTokenSilent`-metoden för att inhämta eller förnya en åtkomsttoken innan du anropar ett webb-API. När du har en token kan du anropa ett skyddat webb-API.

## <a name="call-a-web-api"></a>Anropa en webb-API

### <a name="javascript"></a>JavaScript

Använd den hämtade åtkomsttoken som en innehavare i en HTTP-begäran för att anropa alla webb-API: er, till exempel Microsoft Graph-API. Exempel:

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

### <a name="angular"></a>Angular

MSAL vinkel omslutningen drar nytta av HTTP-spärren för att automatiskt hämta åtkomsttoken och koppla dem till HTTP-begäranden till API: er. Mer information finns i [Hämta en token för att anropa ett API](scenario-spa-acquire-token.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-spa-production.md)
