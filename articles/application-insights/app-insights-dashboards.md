---
title: Instrumentpaneler och navigering i Azure Application Insights | Microsoft Docs
description: Skapa vyer av din nyckel APM diagram och frågor.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 39b0701b-2fec-4683-842a-8a19424f67bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 72859f68fc1bb76a6c71bbd7b98cd713f1f0fe02
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296408"
---
# <a name="navigation-and-dashboards-in-the-application-insights-portal"></a>Navigering och instrumentpaneler i Application Insights-portalen
När du har [konfigurera Application Insights i ditt projekt](app-insights-overview.md), telemetridata om prestanda och användning av din app visas i ditt projekt Application Insights-resurs i den [Azure-portalen](https://portal.azure.com).

## <a name="find-your-telemetry"></a>Hitta din telemetri
Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till Application Insights-resursen som du skapade för din app.

![Klicka på Bläddra, Välj Application Insights och sedan din app.](./media/app-insights-dashboards/00-start.png)

Översikt över bladet för din app (sidan) visas en sammanfattning av diagnostiska nyckelvärden för din app och är en gateway för andra funktioner i portalen.

![Större vägar för att visa din telemetri](./media/app-insights-dashboards/010-oview.png)

Du kan anpassa någon av de diagram och rutnät och fästa dem på en instrumentpanel. På så sätt kan du kan hämta nyckeln telemetri från olika appar på en central instrumentpanel.

## <a name="dashboards"></a>Instrumentpaneler
Första du se när du loggar in på den [Microsoft Azure-portalen](https://portal.azure.com) är en instrumentpanel. Här kan du sammanfoga diagram som är viktigast för dig i alla dina Azure-resurser, inklusive telemetri från [Azure Application Insights](app-insights-overview.md).

![En anpassad instrumentpanel.](./media/app-insights-dashboards/31.png)

1. **Navigera till specifika resurser** som din app i Application Insights: använda fältet till vänster.
2. **Tillbaka till den aktuella instrumentpanelen**, eller växla till andra vyer för senaste: Använd den nedrullningsbara menyn på upp till vänster.
3. **Växla instrumentpaneler**: Använd den nedrullningsbara menyn på instrumentpanelen rubriken
4. **Skapa, redigera och dela instrumentpaneler** i verktygsfältet instrumentpanelen.
5. **Redigera instrumentpanelen**: hovra över en panel och använda dess översta raden för att flytta, anpassa eller ta bort den.

## <a name="add-to-a-dashboard"></a>Lägg till en instrumentpanel
När du tittar på ett blad eller uppsättning diagram som är särskilt intressanta, kan du fästa en kopia av den på instrumentpanelen. Ser du den nästa gång du kommer tillbaka det.

![Hovra över den och klicka på ”...” i rubriken för att fästa ett diagram.](./media/app-insights-dashboards/33.png)

1. PIN-kod diagram på instrumentpanelen. En kopia av diagrammet visas på instrumentpanelen.
2. Fäst hela bladet på instrumentpanelen, visas den på instrumentpanelen som en panel som du kan klicka på via.
3. Klicka på det övre vänstra hörnet för att återgå till den aktuella instrumentpanelen. Du kan sedan använda den nedrullningsbara menyn för att återgå till den aktuella vyn.

Observera att diagram grupperas i panelerna: en panel kan innehålla fler än ett schema. Du kan fästa panelen hela på instrumentpanelen.

Diagrammet uppdateras automatiskt med en frekvens som är beroende av diagrammets tidsintervall:

* Tid vara upp till 1 timme: uppdatera var femte minut
* Tidsintervallet 1-24 timmar: uppdatera var 15: e minut
* Tidsintervallet ovan 24 timmar: (tidsintervallet) / 60.

### <a name="pin-any-query-in-analytics"></a>Fästa en fråga i Analytics
Du kan också [fästa Analytics](app-insights-analytics-using.md#pin-to-dashboard) diagram till en [delade](#share-dashboards-with-your-team) instrumentpanelen. På så sätt kan du lägga till diagram av en skadlig fråga tillsammans med mätvärdena som är standard. 

Resultaten beräknas automatiskt varje timme. Klicka på ikonen Uppdatera i diagrammet för att beräkna om omedelbart. (Uppdatera webbläsaren omberäknar inte.)

## <a name="adjust-a-tile-on-the-dashboard"></a>Justera en panel på instrumentpanelen
Du kan justera när en panel på instrumentpanelen.

![Hovra över ett diagram om du vill redigera.](./media/app-insights-dashboards/36.png)

1. Lägga till ett diagram i panelen.
2. Ange mått, gruppera efter dimension och format (tabell, diagram) i ett diagram.
3. Dra över diagrammet för att zooma in den. Klicka på knappen Ångra om du vill återställa timespan; Ange filteregenskaper för diagram på panelen.
4. Ange panelrubriken.

Paneler fästs från mått explorer blad har fler redigera alternativ än paneler fästs från en översikt över bladet.

Den ursprungliga panelen som du har fäst påverkas inte av ändringarna.

## <a name="switch-between-dashboards"></a>Växla mellan instrumentpaneler
Du kan spara mer än en instrumentpanel och växla mellan dem. När du fäster ett diagram eller bladet, läggs de till den aktuella instrumentpanelen.

![Växla mellan instrumentpaneler, klicka på instrumentpanelen och välj en sparad instrumentpanel. Klicka på ny om du vill skapa och spara en ny instrumentpanel. Klicka på Redigera.](./media/app-insights-dashboards/32.png)

Du kan till exempel ha en instrumentpanel om du vill visa hela skärmen i rummet team och en annan för allmänna utvecklingen.

Ett blad visas som en panel på instrumentpanelen: genom att klicka på Gå till bladet. Ett diagram replikerar diagram på den ursprungliga platsen.

![Klicka på en panel för att öppna bladet representerar](./media/app-insights-dashboards/35.png)

## <a name="share-dashboards"></a>Dela instrumentpaneler
När du har skapat en instrumentpanel kan dela du den med andra användare.

![Klicka på resurs i rubriken instrumentpanelen](./media/app-insights-dashboards/41.png)

Lär dig mer om [roller och åtkomstkontroll](app-insights-resources-roles-access-control.md).

## <a name="create-dashboards-programmatically"></a>Skapa instrumentpaneler programmässigt
Du kan automatisera instrumentpanelen skapas med hjälp av [Azure Resource Manager](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-create-programmatically) och en enkel JSON-redigerare.

## <a name="app-navigation"></a>App-navigering
Översikt över bladet är gateway till mer information om din app.

* **Alla diagram eller panelen** – Klicka på panelen eller diagram om du vill se mer information om vad som visas.

### <a name="overview-blade-buttons"></a>Översikt över bladet knappar
![Översikt över bladet övre navigeringsfält](./media/app-insights-dashboards/app-overview-top-nav.png)

* [**Metrics Explorer** ](app-insights-metrics-explorer.md) -skapa egna diagram av prestanda och användning.
* [**Sök** ](app-insights-diagnostic-search.md) – undersöka specifika instanser av händelser, till exempel begäranden, undantag, eller logga in spårningar.
* [**Analytics** ](app-insights-analytics.md) -kraftfulla frågor via din telemetri.
* **Tidsintervallet** -Justera intervall som visas av alla diagram på bladet.
* **Ta bort** -ta bort Application Insights-resurs för den här appen. Du bör också ta bort Application Insights-paket från din Appkod, eller redigera den [instrumentation nyckeln](app-insights-create-new-resource.md#copy-the-instrumentation-key) i din app att dirigera telemetri till en annan Application Insights-resurs.

### <a name="essentials-tab"></a>Fliken Essentials
* [Instrumentation nyckeln](app-insights-create-new-resource.md#copy-the-instrumentation-key) -identifierar den här appen resurs.

### <a name="app-navigation-bar"></a>Navigeringsfältet i appen
![Vänstra navigeringsfältet](./media/app-insights-dashboards/app-left-nav-bar.png)

* **Översikt över** -gå tillbaka till bladet Översikt.
* **Aktivitetsloggen** -aviseringar och Azure administrativa händelser.
* [**Åtkomstkontroll** ](app-insights-resources-roles-access-control.md) -ge åtkomst till teammedlemmar och andra.
* [**Taggar** ](../azure-resource-manager/resource-group-using-tags.md) -använda taggar för att gruppera din app med andra.

UNDERSÖK

* [**Programavbildningen** ](app-insights-app-map.md) -aktiv karta med komponenter för programmet, som härletts från beroendeinformationen.
* [**Identifiering för smartkort** ](app-insights-proactive-diagnostics.md) -granska de senaste prestanda aviseringarna.
* [**Direktsänd dataström** ](app-insights-live-stream.md) – en fast uppsättning nästan omedelbar statistik, användbart när du distribuerar en ny version eller felsökning.
* [**Tillgänglighet / Webbtester** ](app-insights-monitor-web-app-availability.md) -skicka regelbundna begäranden till ditt webbprogram runt world.*
* [**Fel, prestanda** ](app-insights-web-monitor-performance.md) -undantag, fel och svarstider för förfrågningar från din app på förfrågningar till din app och [beroenden](app-insights-asp-net-dependencies.md).
* [**Prestanda** ](app-insights-web-monitor-performance.md) -svarstid, beroende svarstider.
* [Servrar](app-insights-web-monitor-performance.md) -prestandaräknare. Tillgänglig om du [installera statusövervakaren](app-insights-monitor-performance-live-website-now.md).
* **Webbläsaren** -vy och AJAX-prestanda. Tillgänglig om du [instrumentera webbsidorna](app-insights-javascript.md).
* **Användning** -sidan Visa, användare och session räknas. Tillgänglig om du [instrumentera webbsidorna](app-insights-javascript.md).

KONFIGURERA

* **Komma igång** -infogade kursen.
* **Egenskaper för** -instrumentation nyckel, prenumeration och resurs-id.
* [Aviseringar](app-insights-alerts.md) -mått varningskonfigurationen.
* [Löpande export](app-insights-export-telemetry.md) -konfigurera export av telemetri till Azure-lagring.
* [Prestandatestning](app-insights-monitor-web-app-availability.md#performance-tests) -Ställ in en syntetisk belastningen på din webbplats.
* [Kvoten och prissättning](app-insights-pricing.md) och [införandet provtagning](app-insights-sampling.md).
* **API-åtkomst** -skapa [släpper anteckningar](app-insights-annotations.md) och Data Access-API: t.
* [**Arbetsobjekt som** ](app-insights-diagnostic-search.md#create-work-item) -ansluta till ett system för uppföljning så att du kan skapa buggar vid undersökning av telemetri arbete.

INSTÄLLNINGAR

* [**Låser** ](../azure-resource-manager/resource-group-lock-resources.md) -låsa Azure-resurser
* [**Automatiseringsskriptet** ](app-insights-powershell.md) -exportera en definition av Azure-resurs så att du kan använda den som en mall för att skapa nya resurser.


## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nästa steg

|  |  |
| --- | --- |
| [Metrics explorer](app-insights-metrics-explorer.md)<br/>Filter och segment mått |![Sök-exempel](./media/app-insights-dashboards/64.png) |
| [Diagnostiska sökning](app-insights-diagnostic-search.md)<br/>Söka efter och granska händelser, relaterade händelser och skapa programfel |![Sök-exempel](./media/app-insights-dashboards/61.png) |
| [Analys](app-insights-analytics.md)<br/>Kraftfulla frågespråket |![Sök-exempel](./media/app-insights-dashboards/63.png) |
