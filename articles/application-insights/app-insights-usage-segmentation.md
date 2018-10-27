---
title: Användaren och session händelse analyser i Azure Application Insights | Microsoft docs
description: Demografiska analys av användare på din webbapp.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 6fd8adab93f5741afe6d3eab0c50ca50a327fbff
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140340"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Användare, sessioner och händelser analys i Application Insights

Ta reda på när personer använder din webbapp, vilka sidor som de är mest intresserad av, där användarna befinner sig, och vilka webbläsare och operativsystem som de använder. Analysera företag och användningstelemetri med hjälp av [Azure Application Insights](app-insights-overview.md).

![Skärmbild av Application Insights-användare](./media/app-insights-usage-segmentation/0001-users.png)

## <a name="get-started"></a>Kom igång

Om du ännu inte visas data i användare, sessioner eller händelser bladen i Application Insights-portalen [Lär dig hur du kommer igång med verktyg för användning](app-insights-usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>Verktyget användare, sessioner och händelser segmentering

Tre av bladen användning använder du samma verktyg rapportanvändarna telemetri från ditt program från tre perspektiv. Genom att filtrera och dela data, kan du få insikter om den relativa användningen av olika sidor och funktioner.

* **Användarverktyget**: hur många som används för din app och dess funktioner.  Användare räknas med hjälp av anonym ID som finns lagrade i cookies. En enskild person som använder olika webbläsare eller datorer räknas som flera användare.
* **Sessionsverktyget**: hur många sessioner av användaraktivitet har inkluderat vissa sidor och funktioner i din app. En session räknas efter en halvtimme av användaren inaktivitet eller efter 24 timmar kontinuerlig användning.
* **Händelseverktyget**: hur ofta vissa sidor och funktioner i din app används. En Sidvisning räknas när en webbläsare läser en sida från din app, förutsatt att du har [instrumenterats den](app-insights-javascript.md). 

    En anpassad händelse representerar en förekomst av något händer i din app, ofta någon interaktion från användaren som en knapp klickar du på eller slutförandet av en uppgift. Du infoga kod i din app till [generera anpassade händelser](app-insights-api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Fråga efter för vissa användare

Utforska olika grupper av användare genom att justera frågealternativ överst i användarverktyget:

* Visa: Välj en kohort för användare att analysera.
* Som används: Välj anpassade händelser och sidvisningar.
* Under: Välj ett tidsintervall.
* Genom: Välj hur du bucket data, antingen av en viss tid eller efter en annan egenskap, till exempel webbläsare eller ort.
* Dela efter: Välj en egenskap som att dela eller segment data. 
* Lägg till filter: Begränsa frågan till vissa användare, sessioner eller händelser utifrån deras egenskaper, till exempel webbläsare eller ort. 
 
## <a name="saving-and-sharing-reports"></a>Spara och dela rapporter 
Du kan spara användare rapporter, antingen privat bara för att du i avsnittet Mina rapporter eller delad med alla andra med åtkomst till den här Application Insights-resursen i avsnittet delade rapporter.

Dela en länk till en användare, sessioner eller händelser-rapporterna. Klicka på **resursen** i verktygsfältet och sedan kopiera länken.

Dela en kopia av data i en användare, sessioner eller händelser; klickar du på **resursen** i verktygsfältet, klicka på den **Word ikonen** att skapa ett Word-dokument med data. Alternativt klickar du på den **Word ikonen** ovanför huvudsakliga diagrammet.

## <a name="meet-your-users"></a>Träffa dina användare

Den **uppfyller användarnas** visar information om fem exempelanvändare som matchas av den aktuella frågan. Överväger och utforska beteenden för enskilda användare, förutom aggregeringar, kan ge insikter om hur personer faktiskt använder din app.

## <a name="next-steps"></a>Nästa steg

- Om du vill aktivera användning upplevelser börja skicka [anpassade händelser](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) eller [sidvisningar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Om du redan skicka anpassade händelser eller sidvisningar, utforska användningsverktygen om du vill veta hur användarna använder din tjänst.
    - [Trattar](usage-funnels.md)
    - [Kvarhållning](app-insights-usage-retention.md)
    - [Användarflöden](app-insights-usage-flows.md)
    - [Arbetsböcker](app-insights-usage-workbooks.md)
    - [Lägg till användarkontext](app-insights-usage-send-user-context.md)