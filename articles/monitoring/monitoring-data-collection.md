---
title: Samla in övervakningsdata i Azure | Microsoft Docs
description: Översikt över övervakningsdata som samlas in från program och tjänster i Azure och de verktyg som används för att analysera den.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: bwren
ms.openlocfilehash: 35580d71aa2592fa94f42cfdbad3c192acc303c5
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39363993"
---
# <a name="collect-monitoring-data-in-azure"></a>Samla in övervakningsdata i Azure
Den här artikeln innehåller en översikt över övervakningsdata som samlas in från program och tjänster i Azure. Här beskrivs också de verktyg som du kan använda för att analysera data. 

## <a name="types-of-monitoring-data"></a>Typer av övervakningsdata
Alla övervakningsdata passar in i två grundläggande typer, mått eller loggar. Varje typ har olika egenskaper och passar bäst för särskilda scenarier.

### <a name="metrics"></a>Mått
Mått är numeriska värden som beskriver någon aspekt av ett system vid en given tidpunkt. Dessa är:

* Distinkta data, inklusive själva värdet.
* Den tid som värdet samlades in.
* Typ av mått som representerar värdet.
* Resursen som värdet som är associerad med. 

Mätvärden samlas in med jämna mellanrum oavsett värdet ändras. Exempel: du kan samla in processorbelastning från en virtuell dator på varje minut eller antalet användare som loggat in på ditt program var tionde minut.

Mått är lätt och kan stödja scenarier i nästan realtid. Det är användbart för avisering eftersom mått kan samlas in ofta och en avisering kan vara drar igång snabbt med relativt enkla logik. Du kan till exempel utlöses en avisering när ett mått överskrider ett tröskelvärde. Eller du kan utlösa en avisering när skillnaden mellan två mått når ett visst värde.

Enskilda mått ger vanligtvis lite information på egen hand. De ger ett enstaka värde utan kontext än jämförelse med ett enkelt tröskelvärde. Det är värdefullt när de kombineras med andra mått för att identifiera mönster och trender eller i kombination med loggar som fungerar som kontext runt specifika värden. 

Till exempel kan ett visst antal användare på ditt program vid en given tidpunkt visa lite om hälsotillståndet för programmet. Men en plötslig försämring användare, som anges med flera värden med samma mått kan indikera ett problem. Långa undantag som utlösts av programmet och anges med ett separat mått kan identifiera ett problem med programmet som orsakar i listrutan. Händelser som programmet skapar för att identifiera fel i dess komponenter kan hjälpa dig att identifiera orsaken.

Aviseringar baserat på loggar är inte lika dynamiska som aviseringar baserat på mått, men de kan innehålla mer komplex logik. Du kan skapa en avisering baserat på resultatet av en fråga som utför komplexa analyser på data från flera källor.

### <a name="logs"></a>Logs
Loggar innehålla olika typer av data ordnas i poster med olika uppsättningar med egenskaper för varje typ. Loggar kan innehålla numeriska värden, till exempel mått, men vanligtvis innehåller textdata med detaljerade beskrivningar. De ytterligare skiljer sig från mått genom att de kan variera i deras struktur och ofta inte har samlats in med jämna mellanrum.

En vanlig typ av loggpost är en händelse. Händelser har samlats in sporadiskt. De skapas av programmet eller tjänsten och inkluderar vanligtvis tillräcklig information för att tillhandahålla slutförd kontexten på egen hand. En händelse kan till exempel ange att en viss resurs har skapats eller ändrats, en ny värd igång som svar på ökad trafik, eller ett fel upptäcktes i ett program.

Loggarna är särskilt användbart för att kombinera data från olika källor för komplexa analyser och trender över tid. Eftersom formatet för data kan variera kan program skapa anpassade loggar med hjälp av strukturen som de behöver. Mått kan även replikeras i loggarna kan kombinera dem med andra övervakningsdata för trender och andra dataanalys.


## <a name="monitoring-tools-in-azure"></a>Övervakningsverktyg i Azure
Övervakning av data i Azure samlas in och analyseras via följande källor.

### <a name="azure-monitor"></a>Azure Monitor
Mått från Azure-resurser och program samlas in till Azure Monitor. Måttdata är integrerad i sidorna i Azure-portalen för Azure-resurser. För virtuella datorer visas diagram över mått som CPU- och nätverksanvändning för den valda datorn. 

Du kan också analysera data med hjälp av [Metrics Explorer](../monitoring-and-diagnostics/monitoring-metric-charts.md), som diagram värdena för flera mått med tiden. Du kan visa diagrammen interaktivt eller fästa dem på en instrumentpanel för att visa dem med andra visualiseringar. Du kan också hämta mått med hjälp av den [Azure monitoring REST API](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

Mer information om måttdata som samlar in olika typer av Azure-resurser finns i [källor för övervakningsdata i Azure](monitoring-data-sources.md). 

![Metrics Explorer](media/monitoring-data-collection/metrics-explorer.png)


### <a name="activity-log"></a>Aktivitetslogg 
Den [Azure-aktivitetsloggen](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) lagrar loggar om konfiguration och hälsotillståndet för Azure-tjänster. Du kan använda aktiviteten Log Explorer för att visa loggarna i Azure-portalen, men de är ofta [kopieras till Azure Log Analytics](../log-analytics/log-analytics-activity.md) analyseras med andra loggdata.

Du kan använda aktiviteten Log Explorer för att visa aktivitetsloggen filtreras så att den matchar vissa kriterier. De flesta resurser har också en **aktivitetsloggen** alternativ på menyn i Azure-portalen. Den visar aktivitet Log Explorer filtreras för den resursen. Du kan också hämta aktivitetsloggar med hjälp av den [Azure Monitoring REST API](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

![Aktivitet Log Explorer](media/monitoring-data-collection/activity-log-explorer.png)


### <a name="log-analytics"></a>Log Analytics
Log Analytics tillhandahåller en gemensam dataplattform för hantering i Azure. Det är den primära tjänsten som används för lagring och analys av loggar i Azure. Den samlar in data från olika källor, inklusive agenter på virtuella datorer och lösningar för hantering av Azure-resurser. Du kan kopiera data från andra källor, inklusive mått och aktivitetsloggen, att skapa en fullständig central lagringsplats för övervakningsdata.

Log Analytics har ett funktionsrikt frågespråk för att analysera data som det samlar in. Du kan använda [loggsökningsportaler](../log-analytics/log-analytics-log-search-portals.md) för interaktivt skriva och testa frågor och analysera resultaten. Du kan också [skapa vyer](../log-analytics/log-analytics-view-designer.md) att visualisera resultat i loggsökningar eller klistra in resultatet av en fråga direkt till en Azure-instrumentpanel.  

I hanteringslösningarna ingår loggsökningar och vyer i Log Analytics för att analysera data som de samlar in. Andra tjänster som Azure Application Insights kan lagra data i Log Analytics och innehåller ytterligare verktyg för analys.  

![Logs](media/monitoring-data-collection/logs.png)

### <a name="application-insights"></a>Application Insights
Application Insights samlar in telemetri för webbprogram som är installerad på en rad olika plattformar. Data lagras i Azure Monitor och Log Analytics. Och den tillhandahåller en omfattande uppsättning verktyg för att analysera och visualisera dess data. Dessa funktioner kan du använda en gemensam uppsättning tjänster, till exempel aviseringar, loggsökningar och instrumentpaneler som du använder för andra övervakning.


![Application Insights](media/monitoring-data-collection/app-insights.png)

### <a name="service-map"></a>Tjänstkarta
Tjänstkartan tillhandahåller en visuell representation av virtuella datorer med sina processer och beroenden. De flesta av dessa data lagras i Log Analytics så att du kan analysera dem med andra management-data. Konsolen för Tjänstkarta hämtar också data från Log Analytics för att presentera dem i kontexten för den virtuella datorn som analyseras.

![Tjänstkarta](media/monitoring-data-collection/service-map.png)


## <a name="transferring-monitoring-data"></a>Överföring av övervakningsdata

### <a name="metrics-to-logs"></a>Mått till loggar
Du kan replikera mått i Log Analytics för att utföra komplexa analyser med andra datatyper med hjälp av dess funktionsrikt frågespråk. Du kan även spara loggdata under längre perioder än mätvärden, där du kan utföra resursanvändningstrender. När mått eller några andra prestandadata lagras i Log Analytics, som data fungerar som en logg. Använda mått för att stödja nästan i realtid analys och varning när du använder loggar för trender och göra analyser med andra data.

Du kan få vägledning för att samla in mått från Azure-resurser på [samla in Azure-tjänsteloggar och mått för användning i Log Analytics](../log-analytics/log-analytics-azure-storage.md). Få vägledning för att samla in resurser mått från Azure PaaS-resurser på [konfigurera insamling av mätvärden för Azure PaaS-resurs med Log Analytics](../log-analytics/log-analytics-collect-azurepass-posh.md).

### <a name="logs-to-metrics"></a>Loggar till mått
Enligt beskrivningen ovan, är mått snabbare än loggar, så du kan skapa aviseringar med kortare svarstider och till en lägre kostnad. Log Analytics samlar in en betydande mängd numeriska data som skulle vara lämplig för mått, men inte lagras i Azure Monitor. 

Ett vanligt exempel är prestandadata som samlats in från agenter och lösningar för hantering. Vissa av dessa värden kan kopieras till Azure Monitor, när de är tillgängliga för aviseringar och analys med Metrics Explorer.

Förklaring av den här funktionen är tillgänglig på [snabbare mått aviseringar för loggar nu i en begränsad offentlig förhandsversion](https://azure.microsoft.com/blog/faster-metric-alerts-for-logs-now-in-limited-public-preview/). Listan över värden support finns på [mått och skapa metoder som stöds för nya måttaviseringar](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md).

### <a name="event-hubs"></a>Event Hubs
Förutom att använda verktygen i Azure för att analysera övervakningsdata, kanske du vill vidarebefordra dem till ett externt verktyg, till exempel en säkerhetsinformations- och event management (SIEM)-produkt. Den här vidarebefordran görs normalt via [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/). 

Du kan få vägledning för de olika typerna av övervakningsdata på [Stream Azure-övervakningsdata till en händelsehubb för användning av något externt verktyg](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om den [övervakningsdata är tillgängliga](monitoring-data-sources.md) för olika resurser i Azure. 