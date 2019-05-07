---
title: 'Mobilapp att anrop web API: er (flytta till produktion) - Microsoft identity-plattformen'
description: 'Lär dig att skapa en mobilapp som anropar webb-API: er (flytta till produktion)'
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d37d2de561a6f5841bf17a47fef86ad7639750d5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074957"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Mobila appar som anropar webb-API: er – flytta till produktion

Den här artikeln innehåller information om att förbättra kvaliteten och tillförlitligheten för din app för att flytta den till produktion.

## <a name="handling-errors-in-mobile-applications"></a>Hantera fel i mobila program

I de olika flödena som vi har markerat hittills, finns en mängd olika felvillkor som kan uppstå. Primär scenariot för att hantera är tyst fel och användning av interaktion. Det finns ytterligare villkor som du kan också för produktion som inga nätverk situationer, tjänstavbrott, administratörens godkännande krävs och andra scenariospecifika ärenden.

Varje MSAL biblioteket har kod och wiki exemplen som aktivitetsgruppsrapport djupare i hantering av dessa villkor.

- [MSAL Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Minimera och undersöka problem

Insamling av data kan din app diagnostisera problem. Mer information om vilken typ av data kan du samla in, finns i varje MSAL plattformar wiki.

- Användare kan be om hjälp när den påträffar ett problem. Ett bra tips är att samla in och lagra loggar tillfälligt och Tillåt användare att överföra dem någonstans. MSAL tillhandahåller loggning tillägg för att samla in detaljerad information om autentisering.
- Om det finns aktivera telemetri via MSAL för att samla in data om hur användare loggar in i din app.

## <a name="testing-your-app"></a>Testa din app

Se till att testa din app mot den [integrering checklista](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
