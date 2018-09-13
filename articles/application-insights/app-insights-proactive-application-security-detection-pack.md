---
title: Smart identifiering – hanteringspaket för identifiering av säkerhet med Azure Application Insights | Microsoft Docs
description: Övervaka program med Azure Application Insights för potentiella säkerhetsproblem.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: edfe2956da84347abf3e5da7a7d0fd448361c7f5
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35647258"
---
# <a name="application-security-detection-pack-preview"></a>Application security identifiering pack (förhandsversion)

Application Insights automatiskt telemetri som genereras av programmet och identifierar potentiella säkerhetsproblem. Den här funktionen gör det möjligt för dig att identifiera potentiella säkerhetsproblem och hantera dem genom att åtgärda programmet eller genom att göra de nödvändiga säkerhetsåtgärderna.

Den här funktionen kräver några särskilda inställningar än [konfigurera appen för att skicka telemetri](https://docs.microsoft.com/azure/application-insights/app-insights-usage-overview).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>När ska jag den här typen av meddelande för smart identifiering?
Det finns tre typer av säkerhetsproblem som identifieras:
1. Osäkert URL-åtkomst: en URL i programmet nås via HTTP och HTTPS. En URL som accepterar HTTPS-begäranden ska normalt inte godkänner HTTP-begäranden. Detta kan tyda på problem med en bugg eller säkerhet i ditt program.
2. Osäkert formulär: ett formulär (eller annan ”POST”-begäran) i programmet använder HTTP i stället för HTTPS. Med HTTP kan påverka användarens data som skickas av formuläret.
3. Misstänkt användaraktivitet: programmet som nås från flera länder av samma användare vid ungefär samma tillfälle. Exempelvis kan samma användare komma åt programmet från Spanien och USA inom en och samma timme. Den här identifieringen indikerar ett potentiellt skadliga åtkomstförsök till ditt program.

## <a name="does-my-app-definitely-have-a-security-issue"></a>Min app definitivt finns det ett säkerhetsproblem?
Nej, ett meddelande innebär inte att din app definitivt har ett säkerhetsproblem. En identifiering av någon av ovanstående scenarier kan i många fall kan indikera ett säkerhetsproblem. Men identifieringen kan ha en naturlig affärsjustering och kan ignoreras.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Hur kan jag åtgärda ”osäkert URL-åtkomst”-identifiering?
1. **Prioritering.** Meddelandet innehåller antalet användare som nås osäkert URL: er och den URL som har de flesta påverkas av osäkert åtkomst. Detta kan du tilldela en prioritet till problemet.
2. **Omfattningen.** Vilken procentandel av användarna komma åt osäkert URL: er? Hur många URL: er har påverkas? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera.** Identifieringen innehåller en lista över osäkert önskemål och listan över URL: er och användare som har påverkats för att diagnosticera problemet.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Hur kan jag åtgärda ”osäkert formatet”-identifiering?
1. **Prioritering.** Meddelandet innehåller antalet osäkert formulär och antal användare vars data vara potentiellt komprometterade. Detta kan du tilldela en prioritet till problemet.
2. **Omfattningen.** Vilken form var inblandad i det största antalet osäkert överföringar och vad är distribution av osäkert överföringar över tid? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera.** Identifieringen innehåller listan över osäkert formulär och en detaljerad analys av antalet osäkert överföringar för varje formulär för att diagnosticera problemet.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Hur kan jag åtgärda ”misstänkt användaraktivitet”-identifiering?
1. **Prioritering.** Meddelandet innehåller många olika användare som visas misstänkt beteende. Detta kan du tilldela en prioritet till problemet.
2. **Omfattningen.** Från vilka länder misstänkta begäranden kommer? Vilken användare var de mest misstänkta? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera.** Identifieringen innehåller en lista över misstänkta användare och listan över länder/regioner för varje användare för att diagnosticera problemet.
