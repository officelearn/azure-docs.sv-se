---
title: "Identifiering - säkerhet identifiering Pack med Azure Application Insights för smartkort | Microsoft Docs"
description: "Övervaka program med Azure Application Insights för potentiella säkerhetsproblem."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 837525d70f28c0710b8a8b86d411a7854402207f
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="application-security-detection-pack-preview"></a>Programmet säkerhet identifiering pack (förhandsgranskning)

Application Insights automatiskt analyserar telemetri som genereras av programmet och identifierar potentiella säkerhetsproblem. Den här funktionen kan du identifiera potentiella säkerhetsproblem och hantera dem genom att åtgärda programmet eller genom att göra nödvändiga skyddsåtgärder.

Den här funktionen kräver några särskilda inställningar än [konfigurera appen för att skicka telemetri](https://docs.microsoft.com/azure/application-insights/app-insights-usage-overview).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>När ska jag den här typen av meddelande för smart identifiering?
Det finns tre typer av säkerhetsproblem som identifieras:
1. Osäker åtkomst till URL: en URL i programmet används via både HTTP och HTTPS. En URL som accepterar HTTPS-begäranden ska normalt inte acceptera HTTP-begäranden. Detta kan tyda på ett fel eller säkerhet problem i programmet.
2. Osäker formulär: ett formulär (eller annan begäran ”POST”) i programmet använder HTTP i stället för HTTPS. Med HTTP kan äventyra informationen som skickas av formuläret.
3. Misstänkt användaraktivitet: programmet används från flera länder av samma användare vid ungefär samma tillfälle. Till exempel samma användare komma åt programmet från Spanien och USA inom samma timme. Denna identifiering anger ett potentiellt skadliga åtkomstförsök till ditt program.

## <a name="does-my-app-definitely-have-a-security-issue"></a>Har min app definitivt ett säkerhetsproblem?
Nej, ett meddelande innebär inte att appen verkligen har ett säkerhetsproblem. I många fall kan en identifiering av någon av ovanstående scenarier indikera ett säkerhetsproblem. Men identifieringen kan ha en naturlig affärsjustering och kan ignoreras.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Hur åtgärdar ”osäker URL åtkomst” identifiering?
1. **Prioritering.** Meddelandet innehåller antalet användare som kan komma åt osäker URL: er och den URL som har de flesta påverkas av osäker åtkomst. Detta kan hjälpa dig att tilldela en prioritet till problemet.
2. **Omfattningen.** Vilken procentandel av användarna komma åt osäker URL: er? Hur många webbadresser påverkades? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera.** Identifieringen innehåller listan över oskyddade begäranden och listor över URL: er och användare som har påverkats som hjälper dig att diagnosticera problemet.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Hur åtgärdar ”osäker formuläret” identifiering?
1. **Prioritering.** Meddelandet innehåller antalet osäker formulär och antalet användare vars data vara potentiellt komprometterade. Detta kan hjälpa dig att tilldela en prioritet till problemet.
2. **Omfattningen.** Vilken form var inblandad i det största antalet osäker överföringar och vad är distribution av osäker överföringar över tid? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera.** Identifieringen innehåller listan över oskyddade formulär och en uppdelning av antalet osäker överföringar för varje formulär som hjälper dig att diagnosticera problemet.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Hur åtgärdar ”misstänkta användaraktivitet” identifiering?
1. **Prioritering.** Meddelandet innehåller många olika användare som visas misstänkt beteende. Detta kan hjälpa dig att tilldela en prioritet till problemet.
2. **Omfattningen.** Vilka länder misstänkt begäranden härstammar från? Som användare har mest misstänkta? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera.** Identifieringen innehåller en lista över misstänkta användare och lista över länder för varje användare som hjälper dig att diagnosticera problemet.
