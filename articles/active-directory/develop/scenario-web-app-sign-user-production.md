---
title: Webbapp som loggar in användare (flytta till produktion) – Microsoft Identity Platform
description: Lär dig hur du skapar en webbapp som loggar in användare (flytta till produktion)
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: efcc7ed4f80d0e3e9750d19ff95d010052a08b87
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481876"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Webbapp som loggar in användare: flytta till produktion

Nu när du vet hur du kan hämta en token för att anropa webb-API: er, lär dig hur du flyttar den till produktionen.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Nästa steg

### <a name="scenario-for-calling-web-apis"></a>Scenario för att anropa webb-API: er

När din webbapp loggar in användare kan den anropa webb-API: er åt de inloggade användarna. Att anropa webb-API: er från webbappen är objektet i följande scenario:

> [!div class="nextstepaction"]
> [Webbapp som anropar webb-API:er](scenario-web-app-call-api-overview.md)

### <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Djupgående övning: ASP.NET Core Web Apps-självstudie

Lär dig mer om andra sätt att logga in användare med den här ASP.NET Core själv studie kursen: 

> [!div class="nextstepaction"]
> [Aktivera dina webbappar för att logga in användare och anropa API: er med Microsoft Identity Platform för utvecklare](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Den här Progressive-kursen har produktions klart kod för en webbapp, inklusive hur du lägger till inloggning med konton i:

- Din organisation
- Flera organisationer
- Arbets-eller skol konton eller personliga Microsoft-konton
- [Azure AD B2C](https://aka.ms/aadb2c)
- Nationella moln

### <a name="sample-code-java-web-app"></a>Exempel kod: Java-webbapp

Läs mer om Java-webbappen från det här exemplet på GitHub: 

> [!div class="nextstepaction"]
> [Ett Java-webbprogram som loggar in användare med Microsoft Identity Platform och anropar Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
