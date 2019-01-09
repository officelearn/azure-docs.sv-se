---
title: Instrumentpaneler och navigering i Azure Application Insights | Microsoft Docs
description: Skapa vyer för dina viktiga APM-diagram och frågor.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 39b0701b-2fec-4683-842a-8a19424f67bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 1b0c991c09d1235215dc9b930c529a219d492a8c
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117085"
---
# <a name="navigation-and-dashboards-in-the-application-insights-portal"></a>Navigering och instrumentpaneler i Application Insights-portalen
När du har [ställa in Application Insights i projektet](../../azure-monitor/app/app-insights-overview.md), telemetridata om appens prestanda och användning visas i Application Insights-resurs i ditt projekt i den [Azure-portalen](https://portal.azure.com).

## <a name="find-your-telemetry"></a>Hitta din telemetri
Logga in på den [Azure-portalen](https://portal.azure.com) och gå till Application Insights-resursen som du skapade för din app.

![Klicka på Bläddra och välj Application Insights och sedan din app.](./media/app-insights-dashboards/00-start.png)

Översiktsbladet (sidan) för din app visar en sammanfattning av viktiga diagnostiska mått för din app och är en gateway till andra funktioner i portalen.

![Större vägar för att visa telemetrin](./media/app-insights-dashboards/010-oview.png)

Du kan anpassa någon av de diagram och rutnät och fästa dem på en instrumentpanel. På så sätt kan du kan samla ihop viktig telemetri från olika appar på en central instrumentpanel.

## <a name="dashboards"></a>Instrumentpaneler
Första du ser när du loggar in på den [Microsoft Azure-portalen](https://portal.azure.com) är en instrumentpanel. Här kan du samla diagrammen som är viktigast för dig i alla dina Azure-resurser, inklusive telemetri från [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

![Anpassad instrumentpanel.](./media/app-insights-dashboards/31.png)

1. **Gå till specifika resurser** , till exempel din app i Application Insights: Använd fältet till vänster.
2. **Gå tillbaka till den aktuella instrumentpanelen**, eller växla till andra vyer för senaste: Använd den nedrullningsbara menyn längst upp till vänster.
3. **Växla instrumentpaneler**: Använd den nedrullningsbara menyn på panelen
4. **Skapa, redigera och dela instrumentpaneler** i verktygsfältet för instrumentpanelen.
5. **Redigera instrumentpanelen**: Hovra över en panel och använda dess översta fältet för att flytta, anpassa eller ta bort den.

## <a name="add-to-a-dashboard"></a>Lägg till på en instrumentpanel
När du tittar på ett blad eller uppsättning diagram som är särskilt intressant, kan du fästa en kopia av den på instrumentpanelen. Du ser den nästa gång du kommer tillbaka det.

![Om du vill fästa ett diagram, hovrar över den och klicka sedan på ”...” i rubriken.](./media/app-insights-dashboards/33.png)

1. Fäst diagram på instrumentpanelen. En kopia av diagrammet visas på instrumentpanelen.
2. Fästa hela bladet på instrumentpanelen – den visas på instrumentpanelen som en panel som du kan klicka igenom.
3. Klicka på det övre vänstra hörnet att gå tillbaka till den aktuella instrumentpanelen. Du kan sedan använda den nedrullningsbara menyn för att återgå till den aktuella vyn.

Observera att diagram är grupperade i paneler: en panel kan innehålla flera diagram. Du kan fästa hela panelen på instrumentpanelen.

Diagrammet uppdateras automatiskt med en frekvens som är beroende av diagrammets tidsintervall:

* Tidsintervall upp till 1 timme: Uppdatera var femte minut
* Tidsintervall 1 – 24 timmar: Uppdatera var femtonde minut
* Tidsintervall över 24 timmar: (Tidsintervall) / 60.

### <a name="pin-any-query-in-analytics"></a>Fästa en fråga i Analytics
Du kan också [fästa Analytics](../../azure-monitor/log-query/get-started-portal.md) diagram till en [delade](#share-dashboards-with-your-team) instrumentpanelen. På så sätt kan du lägga till diagram av en godtyckligt fråga tillsammans med mått som standard. 

Resultat räknas automatiskt varje timme. Klicka på Uppdatera-ikonen i diagrammet för att beräkna om omedelbart. (Uppdatera webbläsaren inte beräkna om.)

## <a name="adjust-a-tile-on-the-dashboard"></a>Justera en panel på instrumentpanelen
När det är en panel på instrumentpanelen kan justera du den.

![Hovra över ett diagram för att kunna redigera den.](./media/app-insights-dashboards/36.png)

1. Lägg till ett diagram i panelen.
2. Ange mått, gruppera efter dimension och format (tabell, graph) i ett diagram.
3. Dra över diagrammet för att zooma in den. Klicka på knappen Ångra om du vill återställa timespan; Ange filteregenskaper om diagrammet på panelen.
4. Ange panelrubriken.

Paneler som fästs från metric explorer-blad har fler alternativ än paneler som fästs från en översiktsbladet för redigering.

Den ursprungliga panel som du har fäst påverkas inte av dina ändringar.

## <a name="switch-between-dashboards"></a>Växla mellan instrumentpaneler
Du kan spara mer än en instrumentpanel och växla mellan dem. När du har fäst ett diagram eller bladet läggs de till den aktuella instrumentpanelen.

![Om du vill växla mellan instrumentpaneler, på instrumentpanelen och välj en sparad instrumentpanel. Om du vill skapa och spara en ny instrumentpanel, klickar du på nytt. Klicka på Redigera.](./media/app-insights-dashboards/32.png)

Du kan till exempel ha en enda instrumentpanel för att visa helskärm i teamutrymmet och en annan för allmänna utvecklingen.

På instrumentpanelen visas ett blad som en panel: Klicka på att det ska gå till bladet. Ett diagram replikerar diagram på den ursprungliga platsen.

![Klicka på en panel för att öppna bladet representerar](./media/app-insights-dashboards/35.png)

## <a name="share-dashboards"></a>Dela instrumentpaneler
När du har skapat en instrumentpanel kan dela du den med andra användare.

![I rubriken instrumentpanelen klickar du på resursen](./media/app-insights-dashboards/41.png)

Lär dig mer om [roller och åtkomstkontroll](../../azure-monitor/app/resources-roles-access-control.md).

## <a name="create-dashboards-programmatically"></a>Skapa instrumentpaneler programmässigt
Du kan automatisera instrumentpanel skapas med hjälp av [Azure Resource Manager](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-create-programmatically) och en enkel JSON-redigerare.

## <a name="app-navigation"></a>App-navigering
Översiktsbladet är nyckeln till mer information om din app.

* **Alla diagram eller panel** – Klicka på valfri panel eller diagram om du vill se mer information om vad som visas.

### <a name="overview-blade-buttons"></a>Översikt över bladet knappar
![Översikt över bladet övre navigeringsfältet](./media/app-insights-dashboards/app-overview-top-nav.png)

* [**Metrics Explorer** ](../../azure-monitor/app/metrics-explorer.md) -skapa egna diagram av prestanda och användning.
* [**Sök** ](../../azure-monitor/app/diagnostic-search.md) – Undersök specifika instanser av händelser som begäranden, undantag, eller loggspårningar.
* [**Analytics** ](../../azure-monitor/app/analytics.md) -kraftfulla frågor via din telemetri.
* **Tidsintervall** -Justera intervall som visas av alla diagram på bladet.
* **Ta bort** -ta bort Application Insights-resurs för den här appen. Du bör också antingen ta bort Application Insights-paket från din kod, eller redigera den [instrumenteringsnyckeln](../../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) i din app att dirigera telemetri till en annan Application Insights-resurs.

### <a name="essentials-tab"></a>Fliken Essentials
* [Instrumenteringsnyckeln](../../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) – identifierar den här appresursen.

### <a name="app-navigation-bar"></a>App navigeringsfältet
![Vänster navigeringsfält](./media/app-insights-dashboards/app-left-nav-bar.png)

* **Översikt över** -gå tillbaka till översiktsbladet.
* **Aktivitetsloggen** -aviseringar och Azure administrativa händelser.
* [**Åtkomstkontroll** ](../../azure-monitor/app/resources-roles-access-control.md) – ger åtkomst till gruppmedlemmar och andra.
* [**Taggar** ](../../azure-resource-manager/resource-group-using-tags.md) -använda taggar för att gruppera din app med andra.

UNDERSÖK

* [**Programkartan** ](app-map.md) -Active karta med komponenterna i ditt program, som härletts från beroendeinformationen.
* [**Smart identifiering** ](../../azure-monitor/app/proactive-diagnostics.md) -granska de senaste aviseringarna i prestanda.
* [**Live Stream** ](../../azure-monitor/app/live-stream.md) – en fast uppsättning nästan omedelbar mått, användbart när du distribuerar en ny version eller felsökning.
* [**Tillgänglighet / Webbtester** ](../../azure-monitor/app/monitor-web-app-availability.md) -skickar regelbundna begäranden till webbappen från runt world.*
* [**Fel, prestanda** ](../../azure-monitor/app/web-monitor-performance.md) -undantag, Felfrekvens och svarstider för förfrågningar från din app på förfrågningar till din app och [beroenden](../../azure-monitor/app/asp-net-dependencies.md).
* [**Prestanda** ](../../azure-monitor/app/web-monitor-performance.md) -svarstid, beroende svarstider.
* [Servrar](../../azure-monitor/app/web-monitor-performance.md) -prestandaräknare. Tillgänglig om du [installera Status Monitor](../../azure-monitor/app/monitor-performance-live-website-now.md).
* **Webbläsaren** -sidan Översikt och AJAX-prestanda. Tillgänglig om du [instrumentera dina webbsidor](../../azure-monitor/app/javascript.md).
* **Användning** -sidan antal vy, användare och sessioner. Tillgänglig om du [instrumentera dina webbsidor](../../azure-monitor/app/javascript.md).

KONFIGURERA

* **Komma igång** -infogade självstudien.
* **Egenskaper för** -instrumenteringsnyckeln, prenumeration och resurs-id.
* [Aviseringar](../../azure-monitor/app/alerts.md) -konfiguration av metrisk varning.
* [Löpande export](../../azure-monitor/app/export-telemetry.md) -konfigurera export av telemetri till Azure storage.
* [Prestandatestning](../../azure-monitor/app/monitor-web-app-availability.md#performance-tests) – konfigurera en syntetisk belastningen på din webbplats.
* [Kvoter och priser](../../azure-monitor/app/pricing.md) och [inmatningssampling](../../azure-monitor/app/sampling.md).
* **API-åtkomst** -skapa [Versionsanteckningar](annotations.md) och för Data Access-API: et.
* [**Arbetsobjekt** ](../../azure-monitor/app/diagnostic-search.md#create-work-item) -ansluta till en spårningssystem så att du kan skapa buggar samtidigt Kontrollera telemetri.

INSTÄLLNINGAR

* [**Låser** ](../../azure-resource-manager/resource-group-lock-resources.md) -låsa Azure-resurser
* [**Automationsskript** ](../../azure-monitor/app/powershell.md) -exportdefinition Azure-resursen så att du kan använda den som en mall för att skapa nya resurser.


## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nästa steg

|  |  |
| --- | --- |
| [Måttutforskaren](../../azure-monitor/app/metrics-explorer.md)<br/>Filtrera och segmentera mått |![Sök efter exempel](./media/app-insights-dashboards/64.png) |
| [Diagnostiksökning](../../azure-monitor/app/diagnostic-search.md)<br/>Söka efter och granska händelser, relaterade händelser och skapa buggar |![Sök efter exempel](./media/app-insights-dashboards/61.png) |
| [Analys](../../azure-monitor/app/analytics.md)<br/>Kraftfullt frågespråk |![Sök efter exempel](./media/app-insights-dashboards/63.png) |
