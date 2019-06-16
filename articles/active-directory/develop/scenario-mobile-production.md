---
title: 'Mobilapp att anrop web API: er (flytta till produktion) - Microsoft identity-plattformen'
description: 'Lär dig att skapa en mobilapp att anrop webb-API: er (flytta till produktion)'
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
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8b6a5c2a29228de806088ea93e197d42bf1ab47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65962351"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Mobila appar som anropar webb-API: er – flytta till produktion

Den här artikeln innehåller information om hur du förbättrar kvaliteten och tillförlitligheten för din app innan du flyttar det till produktion.

## <a name="handling-errors-in-mobile-applications"></a>Hantera fel i mobila program

Ett antal fel kan inträffa i din app nu. De huvudsakliga scenarierna för att hantera är tyst fel och återgångarna tar interaktion. Andra villkor som du bör tänka på för produktion är Nej nätverk situationer, tjänstavbrott, krav för administratörens godkännande och andra scenariospecifika fall.

Varje MSAL biblioteket har kod och wiki exemplen som beskriver hur du hanterar dessa villkor:

- [MSAL Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Minimera och undersöka problem

För att diagnostisera problem i din app, det hjälper dig att samla in data. Information om vilka typer av data kan du samla in, se MSAL plattform wikis.

- Användare kan be om hjälp när det uppstår några problem. Ett bra tips är att samla in och tillfälligt lagra loggar och ge en plats där användarna kan ladda upp dem. MSAL tillhandahåller loggning tillägg för att samla in detaljerad information om autentisering.
- Om den är tillgänglig, aktivera telemetri via MSAL för att samla in data om hur användare loggar in på din app.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
