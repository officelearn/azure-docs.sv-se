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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e267b2f398480da6949a4c85133342aad5c2ba7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919910"
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
