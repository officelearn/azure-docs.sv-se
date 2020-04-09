---
title: Analysera lagring av webbappanvändare med Azure Application Insights
description: Hur många användare återvänder till din app?
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 05/03/2017
ms.reviewer: mbullwin
ms.openlocfilehash: e6d9be6bc6d7f90153e2fb58aa404e281568dbdd
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892418"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Analys av användarkvarhållning för webbprogram med Application Insights

Lagringsfunktionen i [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) hjälper dig att analysera hur många användare som återvänder till din app och hur ofta de utför vissa uppgifter eller uppnår mål. Om du till exempel kör en spelwebbplats kan du jämföra antalet användare som återvänder till webbplatsen efter att ha förlorat ett spel med numret som återvänder efter att ha vunnit. Denna kunskap kan hjälpa dig att förbättra både din användarupplevelse och din affärsstrategi.

## <a name="get-started"></a>Kom igång

Om du ännu inte ser data i lagringsverktyget i application insights-portalen [kan du läsa om hur du kommer igång med användningsverktygen](usage-overview.md).

## <a name="the-retention-tool"></a>Lagringsverktyget

![Kvarhållningsverktyg](./media/usage-retention/retention.png)

1. Verktygsfältet tillåter användare att skapa nya lagringsrapporter, öppna befintliga lagringsrapporter, spara aktuell kvarhållningsrapport eller spara som, återställa ändringar som gjorts i sparade rapporter, uppdatera data i rapporten, dela rapport via e-post eller direktlänk och komma åt dokumentationssidan. 
2. Som standard visar kvarhållningen alla användare som gjorde något och sedan kom tillbaka och gjorde något annat under en period. Du kan välja olika kombinationer av händelser för att begränsa fokus på specifika användaraktiviteter.
3. Lägg till ett eller flera filter på egenskaper. Du kan till exempel fokusera på användare i ett visst land eller en viss region. Klicka på **Uppdatera** när du har ställt in filtren. 
4. Det övergripande kvarhållningsdiagrammet visar en sammanfattning av användarens kvarhållning under den valda tidsperioden. 
5. Rutnätet visar antalet användare som behålls enligt frågeverktyget i #2. Varje rad representerar en kohort av användare som utförde en händelse under den tidsperiod som visas. Varje cell i raden visar hur många av kohorten som returnerades minst en gång i en senare period. Vissa användare kan återvända inom mer än en period. 
6. Insights-korten visar de fem viktigaste inledande händelserna och de fem bästa returnerade händelserna för att ge användarna en bättre förståelse för sin kvarhållningsrapport. 

![Håll mushållarkhållar](./media/usage-retention/hover.png)

Användare kan hovra över celler på kvarhållningsverktyget för att komma åt analysknappen och verktygstipsen som förklarar vad cellen betyder. Med analytics-knappen visas användare till Analysverktyget med en förifylld fråga för att generera användare från cellen. 

## <a name="use-business-events-to-track-retention"></a>Använda affärshändelser för att spåra kvarhållning

För att få den mest användbara lagringsanalysen mäter du händelser som representerar betydande affärsaktiviteter. 

Många användare kan till exempel öppna en sida i appen utan att spela det spel som visas. Spåra bara sidvisningarna skulle därför ge en felaktig uppskattning av hur många människor återvänder för att spela spelet efter att ha njutit av det tidigare. Om du vill få en tydlig bild av återkommande spelare bör appen skicka en anpassad händelse när en användare faktiskt spelar upp.  

Det är bra att koda anpassade händelser som representerar viktiga affärsåtgärder och använda dessa för din kvarhållningsanalys. För att fånga spelets resultat måste du skriva en kodrad för att skicka en anpassad händelse till Application Insights. Om du skriver det i webbsidans kod eller i Node.JS ser det ut så här:

```JavaScript
    appinsights.trackEvent("won game");
```

Eller i ASP.NET serverkod:

```csharp
   telemetry.TrackEvent("won game");
```

[Läs mer om att skriva anpassade händelser](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Nästa steg
- Om du vill aktivera användningsupplevelser börjar du skicka [anpassade händelser](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) eller [sidvisningar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Om du redan har skickat anpassade händelser eller sidvisningar kan du utforska användningsverktygen för att ta reda på hur användarna använder tjänsten.
    - [Användare, sessioner, händelser](usage-segmentation.md)
    - [Trattar](usage-funnels.md)
    - [Användarflöden](usage-flows.md)
    - [Arbetsböcker](../../azure-monitor/app/usage-workbooks.md)
    - [Lägga till användarkontext](usage-send-user-context.md)


