---
title: 'Flytta mobilapp som anropar webb-API: er till produktion – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du skapar en mobilapp som anropar webb-API: er (flytta till produktion)'
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
ms.reviwer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1a82fc7dc1b18fa21657170af29f7de7e84d7c1f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702036"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Mobilapp som anropar webb-API: er – flytta till produktion

Den här artikeln innehåller information om hur du förbättrar appens kvalitet och pålitlighet innan du flyttar den till produktion.

## <a name="handling-errors-in-mobile-applications"></a>Hantera fel i mobila program

Ett antal fel villkor kan uppstå i din app i det här läget. Huvud scenarierna för hantering är tysta haverier och återställningar för interaktion. Andra villkor som du bör tänka på för produktion inkluderar inga nätverks situationer, tjänst avbrott, krav på administrativt godkännande och andra scenario-särskilda fall.

Varje MSAL-bibliotek har exempel kod och wiki-innehåll som beskriver hur du hanterar dessa villkor:

- [MSAL Android-wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS-wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Åtgärda och undersöka problem

För att diagnosticera problem i din app kan du samla in data. Information om vilka typer av data du kan samla in finns i MSAL-plattformens wiki-wiki.

- Användarna kan be om hjälp när de stöter på problem. Ett bra tips är att samla in och lagra loggar tillfälligt och ange en plats där användarna kan ladda upp dem. MSAL tillhandahåller loggnings tillägg för att samla in detaljerad information om autentisering.
- Om det är tillgängligt aktiverar du telemetri via MSAL för att samla in data om hur användarna loggar in i din app.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

Prova ytterligare exempel som är tillgängliga från [exempel | Desktop och mobila offentliga klient program](sample-v2-code.md#desktop-and-mobile-public-client-apps)
