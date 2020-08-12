---
title: 'Förbered mobilapp – anropa webb-API: er för produktion | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du skapar en mobilapp som anropar webb-API: er. (Förbered appar för produktion.)'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d1a7407c947c1c5bae1eed00acb0f216722f3d09
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121042"
---
# <a name="prepare-mobile-apps-for-production"></a>Förbered mobila appar för produktion

Den här artikeln innehåller information om hur du förbättrar kvalitet och pålitlighet för mobilappen innan du flyttar den till produktion.

## <a name="handle-errors"></a>Hantera fel

När du förbereder en mobilapp för produktion kan flera fel tillstånd uppstå. De huvudsakliga fall som du hanterar är tysta haverier och återställningar för interaktion. Andra villkor som du bör ta hänsyn till omfattar inga nätverks situationer, tjänst avbrott, krav på administrativt godkännande och andra scenario-särskilda fall.

För varje MSAL-typ (Microsoft Authentication Library) kan du hitta exempel kod och wiki-innehåll som beskriver hur du hanterar fel tillstånd:

- [MSAL Android-wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS-wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>Minimera och undersök problem

För att bättre diagnostisera problem i din app, samla in data. Information om vilka typer av data som du kan samla in finns i [Logga in MSAL-program](./msal-logging.md).

Här följer några förslag på data insamling:

- Användarna kan be om hjälp när de har problem. Ett bra tips är att avbilda och lagra loggar tillfälligt. Ange en plats där användarna kan ladda upp loggarna. MSAL tillhandahåller loggnings tillägg för att samla in detaljerad information om autentisering.

- Om telemetri är tillgängligt aktiverar du det via MSAL för att samla in data om hur användarna loggar in i din app.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

Information om hur du provar ytterligare exempel finns i [Desktop och mobila offentliga klient program](sample-v2-code.md#desktop-and-mobile-public-client-apps).