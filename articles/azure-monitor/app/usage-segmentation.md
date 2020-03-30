---
title: Användar-, sessions- och händelseanalys i Azure Application Insights
description: Demografisk analys av användare av din webbapp.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 56059304026b060e2215ce73e0e94e3200573a14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670992"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Användare, sessioner och händelseanalys i Application Insights

Ta reda på när användarna använder din webbapp, vilka sidor de är mest intresserade av, var användarna finns och vilka webbläsare och operativsystem de använder. Analysera företag och användning telemetri med hjälp av [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

![Skärmbild av användare av programinsikter](./media/usage-segmentation/0001-users.png)

## <a name="get-started"></a>Komma igång

Om du ännu inte ser data i användar-, sessions- eller händelsebladen i application insights-portalen [kan du läsa om hur du kommer igång med användningsverktygen](usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>Segmenteringsverktyget Användare, Sessioner och Händelser

Tre av användningsbladen använder samma verktyg för att skära och tärna telemetri från din webbapp från tre perspektiv. Genom att filtrera och dela upp data kan du upptäcka insikter om den relativa användningen av olika sidor och funktioner.

* **Verktyg för användare**: Hur många personer som använde din app och dess funktioner.  Användare räknas med hjälp av anonyma ID:er som lagras i webbläsarcookies. En enskild person som använder olika webbläsare eller maskiner räknas som mer än en användare.
* **Verktyg för sessioner**: Hur många sessioner med användaraktivitet som har inkluderat vissa sidor och funktioner i din app. En session räknas efter en halvtimmes inaktivitet från användaren eller efter 24 timmars kontinuerlig användning.
* **Händelseverktyget**: Hur ofta vissa sidor och funktioner i din app används. En sidvy räknas när en webbläsare läser in en sida från din app, förutsatt att du har [instrumenterat den](../../azure-monitor/app/javascript.md). 

    En anpassad händelse representerar en förekomst av något som händer i din app, ofta en användarinteraktion som ett knappklick eller slutförandet av en aktivitet. Du infogar kod i appen för att [generera anpassade händelser](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Fråga för vissa användare

Utforska olika grupper av användare genom att justera frågealternativen högst upp i användarverktyget:

* Visa: Välj en kohort av användare att analysera.
* Vem som används: Välj anpassade händelser och sidvisningar.
* Under: Välj ett tidsintervall.
* Av: Välj hur du ska bucketa data, antingen med en tidsperiod eller av en annan egenskap som webbläsare eller ort.
* Dela efter: Välj en egenskap som data ska delas efter. 
* Lägg till filter: Begränsa frågan till vissa användare, sessioner eller händelser baserat på deras egenskaper, till exempel webbläsare eller ort. 
 
## <a name="saving-and-sharing-reports"></a>Spara och dela rapporter 
Du kan spara användarrapporter, antingen privata bara för dig i avsnittet Mina rapporter, eller delas med alla andra med åtkomst till den här application insights-resursen i avsnittet Delade rapporter.

Så här delar du en länk till rapporten Användare, Sessioner eller Händelser. Klicka på **Dela** i verktygsfältet och kopiera länken.

Så här delar du en kopia av data i rapporten Användare, Sessioner eller Händelser. Klicka på **Dela** i verktygsfältet och sedan på **Word-ikonen** för att skapa ett Word-dokument med data. Du kan också klicka på **Word-ikonen** ovanför huvuddiagrammet.

## <a name="meet-your-users"></a>Träffa dina användare

Avsnittet **Möt användarna** visar information om fem exempelanvändare som matchas av den aktuella frågan. Om du överväger och utforskar enskilda personers beteenden kan du förutom aggregat ge insikter om hur personer faktiskt använder din app.

## <a name="next-steps"></a>Nästa steg

- Om du vill aktivera användningsupplevelser börjar du skicka [anpassade händelser](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) eller [sidvisningar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Om du redan har skickat anpassade händelser eller sidvisningar kan du utforska användningsverktygen för att ta reda på hur användarna använder tjänsten.
    - [Trattar](usage-funnels.md)
    - [Kvarhållning](usage-retention.md)
    - [Användarflöden](usage-flows.md)
    - [Arbetsböcker](../../azure-monitor/app/usage-workbooks.md)
    - [Lägga till användarkontext](usage-send-user-context.md)
