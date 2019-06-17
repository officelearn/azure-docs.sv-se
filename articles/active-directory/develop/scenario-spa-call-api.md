---
title: Ensidesapplikation (anropa ett webb-API) - Microsoft identity-plattformen
description: Lär dig hur du skapar ett enkelsidigt program (anropa ett webb-API)
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
ms.openlocfilehash: 77a4ed01ac55a1153a62c672b33056a543b912ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545601"
---
# <a name="single-page-application---call-a-web-api"></a>Ensidesprogram – anropa en webb-API

Vi rekommenderar att du anropar den `acquireTokenSilent` sättet att förvärva eller förnya en åtkomst-token innan du anropar ett webb-API. När du har en token kan anropa du ett skyddat webb-API.

## <a name="call-a-web-api"></a>Anropa en webb-API

### <a name="javascript"></a>JavaScript

Använda förvärvade åtkomsttoken som en innehavare i en HTTP-begäran för att anropa alla webb-API, till exempel Microsoft Graph API. Exempel:

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

Som vi nämnde i den [hämtar tokenavsnittet](scenario-spa-acquire-token.md), MSAL Angular omslutningen utnyttjar HTTP-standardstack automatiskt hämta åtkomsttoken tyst och koppla dem till HTTP-begäranden till API: er.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-spa-production.md)
