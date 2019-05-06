---
title: 'Skrivbordsappen att anrop webb-API: er (anropa en webb-API) - Microsoft identity-plattformen'
description: 'Lär dig att skapa en skrivbordsapp som anropar webb-API: er (anropa en webb-API)'
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fb240b54c3d698ead9d3427f603acca2b53745a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075932"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>Skrivbordsappen som anropar webb-API: er – anropa ett webb-API

Nu när du har en token kan du anropa ett skyddat webb-API.

## <a name="calling-a-web-api-from-net"></a>Anropa ett webb-API från .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>Anropa flera API - inkrementella medgivande och villkorlig åtkomst

Om du vill anropa flera API: er för samma användare, när du har fått en token för den första API kan du bara anropa `AcquireTokenSilent`, och du får en token för API: er tyst de flesta av tiden.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Det är de fall där interaktion krävs när:

- Användaren samtyckt för den första API, men måste godkänna för flera omfång (inkrementell medgivande)
- Den första API kräver inte autentisering med flera faktorer, men nästa har.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-desktop-production.md)
