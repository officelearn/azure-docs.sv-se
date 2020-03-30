---
title: Säkerhetsidentifieringspaket med Azure Application Insights
description: Övervaka program med Azure Application Insights och Smart Detection för potentiella säkerhetsproblem.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: b23ada6fe4596a2eee242cc9145789521caf697f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669734"
---
# <a name="application-security-detection-pack-preview"></a>Programsäkerhetsidentifieringspaket (förhandsgranskning)

Application Insights analyserar automatiskt den telemetri som genereras av ditt program och identifierar potentiella säkerhetsproblem. Med den här funktionen kan du identifiera potentiella säkerhetsproblem och hantera dem genom att åtgärda programmet eller genom att vidta nödvändiga säkerhetsåtgärder.

Den här funktionen kräver ingen särskild konfiguration, förutom [att konfigurera appen för att skicka telemetri](https://docs.microsoft.com/azure/application-insights/app-insights-usage-overview).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>När skulle jag få den här typen av meddelande om smart identifiering?
Det finns tre typer av säkerhetsproblem som har upptäckts:
1. Osäker URL-åtkomst: en URL i programmet används via både HTTP och HTTPS. Vanligtvis bör en URL som accepterar HTTPS-begäranden inte acceptera HTTP-begäranden. Detta kan tyda på ett fel- eller säkerhetsproblem i ditt program.
2. Osäkert formulär: ett formulär (eller en annan "POST"-begäran) i programmet använder HTTP i stället för HTTPS. Om du använder HTTP kan du kompromettera användardata som skickas av formuläret.
3. Misstänkt användaraktivitet: programmet används från flera länder/regioner av samma användare vid ungefär samma tidpunkt. Till exempel kom samma användare åt programmet från Spanien och USA inom samma timme. Den här identifieringen indikerar ett potentiellt skadligt åtkomstförsök till ditt program.

## <a name="does-my-app-definitely-have-a-security-issue"></a>Har min app definitivt ett säkerhetsproblem?
Nej, ett meddelande betyder inte att din app definitivt har ett säkerhetsproblem. En identifiering av något av scenarierna ovan kan i många fall indikera ett säkerhetsproblem. Identifieringen kan dock ha en naturlig affärsmotivering och kan ignoreras.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Hur åtgärdar jag identifieringen av osäker url-åtkomst?
1. **Triage.** Meddelandet innehåller antalet användare som har åtkomst till osäkra webbadresser och den url som påverkades mest av osäker åtkomst. Detta kan hjälpa dig att tilldela en prioritet till problemet.
2. **Omfattning.** Hur stor procentandel av användarna som har åtkomst till osäkra webbadresser? Hur många webbadresser påverkades? Denna information kan erhållas från anmälan.
3. **Diagnostisera.** Identifieringen innehåller en lista över osäkra begäranden och listor över webbadresser och användare som har drabbats, för att hjälpa dig att ytterligare diagnostisera problemet.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Hur åtgärdar jag identifieringen av osäkert formulär?
1. **Triage.** Meddelandet innehåller antalet osäkra formulär och antalet användare vars data potentiellt komprometterades. Detta kan hjälpa dig att tilldela en prioritet till problemet.
2. **Omfattning.** Vilken form var inblandad i det största antalet osäkra sändningar, och vad är fördelningen av osäkra sändningar över tiden? Denna information kan erhållas från anmälan.
3. **Diagnostisera.** Identifieringen innehåller en lista över osäkra formulär och en uppdelning av antalet osäkra överföringar för varje formulär, för att hjälpa dig att ytterligare diagnostisera problemet.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Hur åtgärdar jag identifieringen av "Misstänkt användaraktivitet"?
1. **Triage.** Meddelandet innehåller antalet olika användare som uppvisade det misstänkta beteendet. Detta kan hjälpa dig att tilldela en prioritet till problemet.
2. **Omfattning.** Från vilka länder/regioner har de misstänkta förfrågningarna sitt ursprung? Vilken användare var mest misstänkt? Denna information kan erhållas från anmälan.
3. **Diagnostisera.** Identifieringen innehåller en lista över misstänkta användare och listan över länder/regioner för varje användare, för att hjälpa dig att ytterligare diagnostisera problemet.
