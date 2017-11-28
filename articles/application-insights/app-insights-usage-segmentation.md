---
title: "Användare, session och händelsen analys i Azure Application Insights | Microsoft docs"
description: "Demografisk analys av användare av ditt webbprogram."
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: 04efb82addd0f307c68c73e28e46b602e5bc194a
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2017
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Användare, sessioner och händelser för analys i Application Insights

Ta reda om personer använder ditt webbprogram, vilka sidor som de är mest intresserad av, där användarna finns, vilka webbläsare och operativsystem som de använder. Analysera affärs- och användningsdata telemetri med hjälp av [Azure Application Insights](app-insights-overview.md).

## <a name="get-started"></a>Kom igång

Om du ännu inte ser data i användare, sessioner eller händelser blad i Application Insights-portalen [Lär dig att komma igång med verktyg för användning](app-insights-usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>Verktyget användare, sessioner och händelser segmentering

Tre av användning blad använder samma verktyg rapportanvändarna telemetri från ditt program från tre perspektiv. Genom filtrering och dela data, kan du få insikter om relativa användningen av olika sidor och funktioner.

* **Användare verktyget**: hur många som används för appen och dess funktioner.  Användare ska räknas med anonyma ID: N som lagras i cookies i webbläsaren. En enda person som använder olika webbläsare eller datorer som ska räknas som flera användare.
* **Sessioner verktyget**: hur många sessioner för användaraktiviteten finns det vissa sidor och funktioner i din app. En session räknas efter en halvtimme användaren inaktivitet, eller efter kontinuerlig 24 timmar för användning.
* **Händelser verktyget**: hur ofta vissa sidor och funktioner i din app används. Vyn sida räknas när en webbläsare läser en sida från din app, förutsatt att du har [instrumenterats den](app-insights-javascript.md). 

    En anpassad händelse representerar en förekomst av något händer i din app, ofta användarinteraktion som klickar på en knapp eller slutförandet av en uppgift. Du infogar kod i din app [generera anpassade händelser](app-insights-api-custom-events-metrics.md#trackevent).

![Användning-verktyget](./media/app-insights-usage-segmentation/users.png)

## <a name="querying-for-certain-users"></a>Frågor för vissa användare 

Utforska olika grupper av användare genom att justera frågealternativ överst i verktyget användare: 

* Som används: Välj anpassade händelser och sidvisningar. 
* Under: Välja ett tidsintervall. 
* Av: Välj hur du bucket data, antingen genom en viss tidsperiod eller en annan egenskap, till exempel webbläsare eller ort. 
* Delar av: Välj en egenskap som att dela eller segment data. 
* Lägg till filter: Begränsa frågan till vissa användare, sessioner och händelser utifrån deras egenskaper, till exempel webbläsare eller ort. 
 
## <a name="saving-and-sharing-reports"></a>Spara och dela rapporter 
Du kan spara användare rapporter, antingen privat ska du i avsnittet Mina rapporter eller delad med alla andra med åtkomst till denna Application Insights-resurs i den delade rapporter.  
 
När du sparar en rapport eller redigerar egenskaper, väljer du ”aktuellt relativt tidsintervall” att spara en rapport kommer kontinuerligt uppdateras data, gå tillbaka vissa fast tidsperiod.  
 
Välj ”aktuellt absolut tidsintervall” spara en rapport med en fast mängd data. Tänk på att data i Application Insights bara lagras under 90 dagarna, så om du har gått mer än 90 dagar sedan en rapport med ett absolut tidsintervall sparades, rapporten visas tom. 
 
## <a name="example-instances"></a>Exempel instanser

Exempel instanser avsnittet visar information om en handfull enskilda användare, sessioner och händelser som täcks av den aktuella frågan. Med tanke på och utforska beteenden för enskilda användare, förutom aggregeringar, kan ge insikter om hur personer faktiskt använder din app. 
 
## <a name="insights"></a>Insikter 

Insikter sidopanelen visar stora kluster med användare som har gemensamma egenskaper. Dessa kluster kan avslöja konstigt trender i hur personer använder din app. Om exempelvis 40% för alla användningen av din app kommer från personer som använder en enda funktion.  


## <a name="next-steps"></a>Nästa steg
- Om du vill aktivera användning upplevelser börja skicka [anpassade händelser](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) eller [sidvisningar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Om du skickar redan anpassade händelser eller sidvisningar, utforska verktygen användning om du vill veta hur användarna använder din tjänst.
    - [Trattar](usage-funnels.md)
    - [Kvarhållning](app-insights-usage-retention.md)
    - [Användarflöden](app-insights-usage-flows.md)
    - [Arbetsböcker](app-insights-usage-workbooks.md)
    - [Lägg till användarkontext](app-insights-usage-send-user-context.md)

