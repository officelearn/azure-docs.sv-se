---
title: Samla in övervakningsdata i Azure | Microsoft Docs
description: Översikt över övervakningsdata som samlas in från program och tjänster i Azure och verktyg för att analysera den.
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
ms.openlocfilehash: d3ebd512f8244de74c009ac8a2936ed8e817dad9
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991510"
---
# <a name="collecting-monitoring-data-in-azure"></a>Samla in övervakningsdata i Azure
Den här artikeln innehåller en översikt över övervakningsdata som samlas in från program och tjänster i Azure och verktyg som används för att analysera den. 

## <a name="types-of-monitoring-data"></a>Typer av övervakningsdata
Alla övervakningsdata passar in i två grundläggande typer, mått eller loggar. Varje typ har olika egenskaper och passar bäst för specifika scenarier som beskrivs nedan.

### <a name="metrics"></a>Mått
Mått är numeriska värden som beskriver någon aspekt av ett system vid en viss tidpunkt. De innehåller distinkta data, inklusive själva värdet tiden värdet samlades in, typen av mått värdet representerar och den resurs som värdet som är associerad med. Mätvärden samlas in med jämna mellanrum oavsett värdet ändras. Du kan till exempel samla in processorbelastning från en virtuell dator varje minut eller antalet användare som loggat in på ditt program var tionde minut.

Mått är lätt och kan stödja scenarier i nästan realtid. De är särskilt användbart för avisering eftersom mått kan samlas in ofta och en avisering kan vara drar igång snabbt med relativt enkla logik. Du kan till exempel utlöses en avisering när ett mått överskrider ett tröskelvärde eller utlöses en avisering när skillnaden mellan två mått når ett visst värde.

Enskilda mått ger vanligtvis lite information på egen hand. De ger ett enstaka värde utan kontext än jämförelse med ett enkelt tröskelvärde. De är dock värdefulla när de kombineras med andra mått för att identifiera mönster och trender eller i kombination med loggar som fungerar som kontext runt specifika värden. Till exempel kan ett visst antal användare på ditt program vid en given tidpunkt visa lite om hälsotillståndet för programmet. En plötslig försämring användare dock identifieras med flera värden med samma mått kan tyda på ett problem. Långa undantag som utlösts av programmet och anges med ett separat mått kan identifiera ett problem med programmet i listrutan. Händelser som skapas av programmet identifierar fel i synnerhet komponenter i programmet kan hjälpa dig att identifiera orsaken.

Aviseringar baserat på loggar är inte lika dynamiska som aviseringar baserat på mått, men de kan innehålla mer komplex logik. Du kan skapa en avisering baserat på resultatet av en fråga som utför komplexa analyser på data från flera källor.

### <a name="logs"></a>Logs
Loggar innehålla olika typer av data ordnas i poster med olika uppsättningar med egenskaper för varje typ. Loggar kan innehålla numeriska värden, till exempel mått, men vanligtvis innehåller textdata med detaljerade beskrivningar. De ytterligare skiljer sig från mått genom att de kan variera i deras struktur och ofta inte har samlats in med jämna mellanrum.

En vanlig typ av loggpost är en händelse. Händelser som samlas in sporadiskt som har skapats av ett program eller tjänst och inkluderar vanligtvis tillräcklig information för att tillhandahålla slutförd kontexten på egen hand.  En händelse kan till exempel ange att en viss resurs skapades eller ändrades, en ny värd startas som svar på ökad trafik eller ett fel upptäcktes i ett program.

Loggarna är särskilt användbart för att kombinera data från olika källor för komplexa analyser och trender över tid. Eftersom formatet för data kan variera kan program skapa anpassade loggar med hjälp av strukturen som de behöver. Mått kan även replikeras i loggarna för att kombinera den med andra övervakningsdata för trender och andra dataanalys.


## <a name="monitoring-tools-in-azure"></a>Övervakningsverktyg i Azure
Övervakning av data i Azure som samlas in och analyseras med hjälp av flera källor som beskrivs i följande avsnitt.

### <a name="azure-metrics"></a>Azure-mått
Mått från Azure-resurser och program samlas in till Azure-mått. Måttdata är integrerad i sidorna i Azure-portalen för specifika Azure-resurser som virtuella datorer, bland annat diagram över mått som CPU- och nätverksanvändning för den valda datorn. Det kan även analyseras med den [Metrics Explorer](../monitoring-and-diagnostics/monitoring-metric-charts.md) som kan skapa diagram över värdena för flera mått med tiden.  Du kan visa diagrammen interaktivt eller fästa dem på en instrumentpanel för att visa dem med andra visualiseringar. Du kan också hämta mått med den [Azure monitoring REST API](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

Du kan hämta information om måttdata som samlas in av olika typer av Azure-resurser på [källor för övervakningsdata i Azure](monitoring-data-sources.md). 

![Metrics Explorer](media/monitoring-data-collection/metrics-explorer.png)


### <a name="azure-activity-log"></a>Azure-aktivitetsloggen 
Den [Azure-aktivitetsloggen](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) lagrar loggar om konfiguration och hälsotillståndet för Azure-tjänster. Du kan använda aktiviteten Log Explorer för att visa dessa loggar i Azure-portalen, men de är ofta [kopieras till Log Analytics](../log-analytics/log-analytics-activity.md) analyseras med andra loggdata.

Du kan använda aktiviteten Log Explorer för att visa aktivitetsloggen filtreras så att den matchar vissa kriterier.  De flesta resurser har också en aktivitetslogg alternativet sina-menyn i Azure-portalen som visar aktivitet Log Explorer filtreras för den resursen. Du kan också hämta aktivitetsloggar med den [Azure monitoring REST API](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

![Aktivitet Log Explorer](media/monitoring-data-collection/activity-log-explorer.png)


### <a name="log-analytics"></a>Log Analytics
Log Analytics tillhandahåller en gemensam dataplattform för hantering i Azure. Det är den primära tjänsten som används för lagring och analys av loggar i Azure, samla in data från olika källor, inklusive agenter på virtuella datorer och lösningar för hantering av olika Azure-resurser. Data från andra källor, inklusive mått och aktivitetsloggen kan kopieras till Log Analytics för att skapa en fullständig centrallager för övervakningsdata.

Log Analytics har ett funktionsrikt frågespråk för att analysera data som samlas in.  Du kan använda [loggsökningsportaler](../log-analytics/log-analytics-log-search-portals.md) för interaktivt skriva och testa frågor och analysera resultaten. Du kan också [skapa vyer](../log-analytics/log-analytics-view-designer.md) att visualisera resultat i loggsökningar eller klistra in resultatet av en fråga direkt till en Azure-instrumentpanel.  I hanteringslösningarna ingår loggsökningar och vyer i Log Analytics för att analysera den information som samlas in. Andra tjänster som Application Insights lagrar data i Log Analytics och innehåller ytterligare verktyg för analys.  

![Logs](media/monitoring-data-collection/logs.png)

### <a name="application-insights"></a>Application Insights
Application Insights samlar in telemetri för webbprogram som är installerad på en rad olika plattformar. Den lagras data i Azure-mått och Log Analytics och ger en omfattande uppsättning omfattande verktyg, utöver de befintliga verktyg för att analysera data, för att analysera och visualisera dess data. På så sätt kan du utnyttja en gemensam uppsättning tjänster, till exempel aviseringar, loggsökningar och instrumentpaneler som du använder för andra övervakning.


![App Insights](media/monitoring-data-collection/app-insights.png)

### <a name="service-map"></a>Tjänstkarta
Tjänstkartan tillhandahåller en visuell representation av virtuella datorer med sina processer och beroenden. De flesta av dessa data lagras i Log Analytics så att du kan analysera dem med andra management-data. Konsolen för Tjänstkarta hämtar också data från Log Analytics för att presentera dem i kontexten för den virtuella datorn som analyseras.

![Tjänstkarta](media/monitoring-data-collection/service-map.png)


## <a name="transferring-monitoring-data"></a>Överföring av övervakningsdata

### <a name="metrics-to-logs"></a>Mått till loggar
Mått kan också replikeras till Log Analytics för att utföra avancerade analyser med andra datatyper med hjälp av dess funktionsrikt frågespråk. Du kan även spara loggdata under längre perioder än mätvärden, där du kan utföra resursanvändningstrender. När mått eller några andra prestandadata lagras i Log Analytics, som data fungerar som en logg. Använda mått för att stödja nästan i realtid analys och varning när du använder loggar för trender och göra analyser med andra data.

Du kan få vägledning för att samla in mått från Azure-resurser på [samla in Azure-tjänsteloggar och mått för användning i Log Analytics](../log-analytics/log-analytics-azure-storage.md). Få vägledning för att samla in resurser mått från Azure PaaS-resurser på [konfigurera insamling av mätvärden för Azure PaaS-resurs med Log Analytics](../log-analytics/log-analytics-collect-azurepass-posh.md).

### <a name="logs-to-metrics"></a>Loggar till mått
Mått är snabbare än loggar så att du kan skapa aviseringar med kortare svarstider och till en lägre kostnad enligt beskrivningen ovan. Log Analytics samlar in en betydande mängd numeriska data som skulle vara lämplig för mått, men inte lagras i Azure-mått. Ett vanligt exempel är prestandadata som samlats in från agenter och lösningar för hantering. Vissa av dessa värden kan kopieras till Azure-mått när de är tillgängliga för aviseringar och analys med Metrics Explorer.

Förklaring av den här funktionen är tillgänglig på [snabbare mått aviseringar för loggar nu i en begränsad offentlig förhandsversion](https://azure.microsoft.com/blog/faster-metric-alerts-for-logs-now-in-limited-public-preview/). Listan över värden support finns på [mått och skapa metoder som stöds för nya måttaviseringar](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md).

### <a name="event-hub"></a>Händelsehubb
Förutom att använda verktygen i Azure för att analysera övervakningsdata, kanske du vill vidarebefordra den till något externt verktyg sådana säkerhetsinformation och event management (SIEM)-produkt. Detta görs normalt med hjälp av [Azure Event Hub](https://docs.microsoft.com/azure/event-hubs/). 

Du kan få vägledning för de olika typerna av övervakningsdata på [Stream Azure-övervakningsdata till en händelsehubb för användning av något externt verktyg](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om den [övervakningsdata är tillgängliga](monitoring-data-sources.md) för olika resurser i Azure. 