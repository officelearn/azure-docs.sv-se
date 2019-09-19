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
ms.openlocfilehash: bd6717c132d32d54c16e7678695a09e35181a057
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086554"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>Webbapp som loggar in användare – flytta till produktion

Nu när du vet hur du kan hämta en token för att anropa webb-API: er, lär du dig hur du flyttar den till produktionen.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Nästa steg

### <a name="calling-web-apis-scenario"></a>Anropa webb-API-scenario

När din webbapp loggar in användare kan den anropa webb-API: er för de inloggade användarna. Att anropa webb-API: er från webbappen är objektet i följande scenario:

> [!div class="nextstepaction"]
> [Webbapp som anropar webb-API:er](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---aspnet-core-web-app-tutorial"></a>Djupgående vägledning – ASP.NET Core Web Apps-självstudie

Lär dig mer om andra sätt att logga in användare med självstudierna ASP.NET Core: [MS-Identity-aspnetcore-webapp-självstudie](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). Det här exemplet är en progressiv självstudie med produktions klar kod för en webbapp, inklusive hur du lägger till inloggning med konton i:

- din organisation,
- flera organisationer,
- arbets-eller skol konton eller personliga Microsoft-konto,
- med [Azure AD B2C](https://aka.ms/aadb2c),
- eller i nationella moln.

### <a name="sample-code---java-web-app"></a>Exempel kod – Java-webbapp

Läs mer om Java-webbappen från exemplet på GitHub: [Ett Java-webbprogram som loggar in användare med Microsoft Identity Platform och anropar Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
