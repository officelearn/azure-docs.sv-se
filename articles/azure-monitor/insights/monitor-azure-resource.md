---
title: Övervaka Azure-resurser med Azure Monitor | Microsoft-dokument
description: Beskriver hur du samlar in och analyserar övervakningsdata från resurser i Azure med Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 01d188e0e39888297ff8d6a57129a3a17e1654fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249274"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Övervaka Azure-resurser med Azure Monitor
När du har kritiska program och affärsprocesser som förlitar sig på Azure-resurser, vill du övervaka dessa resurser för deras tillgänglighet, prestanda och drift. I den här artikeln beskrivs övervakningsdata som genereras av Azure-resurser och hur du kan använda funktionerna i Azure Monitor för att analysera och avisera om dessa data.

> [!IMPORTANT]
> Den här artikeln gäller alla tjänster i Azure som använder Azure Monitor. Beräkningsresurser, inklusive virtuella datorer och App-tjänst, genererar samma övervakningsdata som beskrivs här men också har ett gästoperativsystem som också kan generera loggar och mått. Mer information om hur du samlar in och analyserar dessa data finns i övervakningsdokumentationen för dessa tjänster.

## <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?
Azure Monitor är en fullständig stackövervakningstjänst i Azure som tillhandahåller en komplett uppsättning funktioner för att övervaka dina Azure-resurser utöver resurser i andra moln och lokalt. [Azure Monitor-dataplattformen](../platform/data-platform.md) samlar in data i [loggar](../platform/data-platform-logs.md) och [mått](../platform/data-platform-metrics.md) där de kan analyseras tillsammans med hjälp av en komplett uppsättning övervakningsverktyg som beskrivs i följande avsnitt.

- [Vad kan du göra med Azure Monitor Metrics?](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [Vad kan du göra med Azure Monitor Logs?](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

Så fort du skapar en Azure-resurs aktiveras Azure Monitor och börjar samla in mått och aktivitetsloggar som du kan [visa och analysera i Azure-portalen](#monitoring-in-the-azure-portal). Med viss konfiguration kan du samla in ytterligare övervakningsdata och aktivera ytterligare funktioner. Se [Övervakningsdata](#monitoring-data) nedan för information om eventuella konfigurationskrav.


## <a name="costs-associated-with-monitoring"></a>Kostnader i samband med övervakning
Det finns ingen kostnad för att analysera övervakningsdata som samlas in som standard. Detta inkluderar följande:

- Samla in plattformsmått och analysera dem med statistikutforskaren.
- Samla aktivitetslogg och analysera den i Azure-portalen.
- Skapa en aktivitetsloggaviseringsregel.

Det finns inga Azure Monitor-kostnader för insamling och export av loggar och mått, men det kan finnas relaterade kostnader som är associerade med målet:

- Kostnader som är associerade med datainmatning och lagring vid insamling av loggar och mått i Log Analytics-arbetsytan. Se [Azure Monitor-priser för Logganalys](https://azure.microsoft.com/pricing/details/monitor/).
- Kostnader som är associerade med datalagring vid insamling av loggar och mått till ett Azure-lagringskonto. Se [Azure Storage-priser för blob-lagring](https://azure.microsoft.com/pricing/details/storage/blobs/).
- Kostnader som är associerade med händelsehubbstreaming vid vidarebefordran av loggar och mått till Azure Event Hubs. Se [priser för Azure Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

Det kan finnas Azure Monitor-kostnader som är associerade med följande. Se [priser för Azure Monitor:](https://azure.microsoft.com/pricing/details/monitor/)

- Köra en loggfråga.
- Skapa en mått- eller loggfrågeaviseringsregel.
- Skickar ett meddelande från en varningsregel.
- Komma åt mått via API.

## <a name="monitoring-data"></a>Övervaka data
Resurser i Azure genererar [loggar](../platform/data-platform-logs.md) och [mått](../platform/data-platform-metrics.md) som visas i följande diagram. Se dokumentationen för varje Azure-tjänst för de specifika data som de genererar och eventuella ytterligare lösningar eller insikter som de tillhandahåller.

![Översikt](media/monitor-azure-resource/logs-metrics.png)



- [Plattformsmått](../platform/data-platform-metrics.md) - Numeriska värden som samlas in automatiskt med jämna mellanrum och beskriver någon aspekt av en resurs vid en viss tidpunkt. 
- [Resursloggar](../platform/platform-logs-overview.md) – Ge insikt i åtgärder som utfördes inom en Azure-resurs (dataplanet), till exempel att få en hemlighet från ett Key Vault eller göra en begäran till en databas. Innehållet och strukturen för resursloggar varierar beroende på Azure-tjänst och resurstyp.
- [Aktivitetslogg](../platform/platform-logs-overview.md) - Ger insikt i åtgärderna på varje Azure-resurs i prenumerationen från utsidan (hanteringsplanet), till exempel skapa en ny resurs eller starta en virtuell dator. Det här är information om vad, vem och när för alla skrivåtgärder (PUT, POST, DELETE) som tas över resurserna i prenumerationen.


## <a name="configuration-requirements"></a>Konfigurationskrav

### <a name="configure-monitoring"></a>Konfigurera övervakning
Vissa övervakningsdata samlas in automatiskt, men du kan behöva utföra vissa konfigurationer beroende på dina krav. Se informationen nedan för specifik information för varje typ av övervakningsdata.

- [Plattformsmått](../platform/data-platform-metrics.md) - Plattformsmått samlas in automatiskt i [Azure Monitor-mått](../platform/data-platform-metrics.md) utan att någon konfiguration krävs. Skapa en diagnostikinställning för att skicka poster till Azure Monitor-loggar eller vidarebefordra dem utanför Azure.
- [Resursloggar](../platform/platform-logs-overview.md) - Resursloggar genereras automatiskt av Azure-resurser men samlas inte in utan en diagnostikinställning.  Skapa en diagnostikinställning för att skicka poster till Azure Monitor-loggar eller vidarebefordra dem utanför Azure.
- [Aktivitetslogg](../platform/platform-logs-overview.md) - Aktivitetsloggen samlas in automatiskt utan att någon konfiguration krävs och kan visas i Azure-portalen. Skapa en diagnostikinställning för att kopiera dem till Azure Monitor-loggar eller vidarebefordra dem utanför Azure.

### <a name="log-analytics-workspace"></a>Log Analytics-arbetsyta
Samla in data i Azure Monitor Logs kräver en Log Analytics-arbetsyta. Du kan snabbt börja övervaka tjänsten genom att skapa en ny arbetsyta, men det kan finnas ett värde i att använda en arbetsyta som samlar in data från andra tjänster. Mer information om hur du skapar [en Log Analytics-arbetsyta i Azure-portalen](../learn/quick-create-workspace.md) finns information om hur du skapar en arbetsyta och [utformar distributionen](../platform/design-logs-deployment.md) av Azure Monitor Logs för att avgöra den bästa arbetsytedesignen för dina behov. Om du använder en befintlig arbetsyta i organisationen behöver du lämpliga behörigheter enligt beskrivningen i [Hantera åtkomst till loggdata och arbetsytor i Azure Monitor](../platform/manage-access.md). 





## <a name="diagnostic-settings"></a>Diagnostikinställningar
Diagnostikinställningar definierar var resursloggar och mått för en viss resurs ska skickas. Möjliga destinationer är:

- [Log Analytics arbetsyta](../platform/resource-logs-collect-workspace.md) som låter dig analysera data med andra övervakningsdata som samlats in av Azure Monitor med hjälp av kraftfulla loggfrågor och även att utnyttja andra Azure Monitor-funktioner som loggaviseringar och visualiseringar. 
- [Händelsehubbar](../platform/resource-logs-stream-event-hubs.md) för att strömma data till externa system som SIEM-tillverkare från tredje part och andra logganalyslösningar. 
- [Azure-lagringskonto](../platform/resource-logs-collect-storage.md) som är användbart för granskning, statisk analys eller säkerhetskopiering.

Följ proceduren i [Skapa diagnostikinställning för att samla in plattformsloggar och mått i Azure](../platform/diagnostic-settings.md) för att skapa och hantera diagnostikinställningar via Azure-portalen. Se [Skapa diagnostikinställning i Azure med hjälp av en Resource Manager-mall](../platform/diagnostic-settings-template.md) för att definiera dem i en mall och aktivera fullständig övervakning för en resurs när den skapas.


## <a name="monitoring-in-the-azure-portal"></a>Övervakning i Azure-portalen
 Du kan komma åt övervakningsdata för de flesta Azure-resurser från resursens meny i Azure-portalen. Detta ger dig åtkomst till en enskild resurs data med hjälp av standard Azure Monitor-verktyg. Vissa Azure-tjänster ger olika alternativ, så du bör referera till dokumentationen för den tjänsten för ytterligare information. Använd **Azure Monitor-menyn** för att analysera data från alla övervakade resurser. 

### <a name="overview"></a>Översikt
Många tjänster kommer att omfatta övervakning av data på deras **översiktssida** som en snabb titt på deras funktion. Detta baseras vanligtvis på en delmängd av plattformsmått som lagras i Azure Monitor Metrics. Andra övervakningsalternativ är vanligtvis tillgängliga i en **övervakningssektion** av tjänsterna. .

![Översiktssida](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Insikter och lösningar 
Vissa tjänster tillhandahåller verktyg utöver standardfunktionerna i Azure Monitor. Insikter ger en anpassad [övervakningsupplevelse](../insights/insights-overview.md) som bygger på Azure Monitor-dataplattformen och standardfunktionerna. [Lösningar](../insights/solutions.md) ger fördefinierad övervakningslogik som bygger på Azure Monitor Logs. 

Om en tjänst har en Azure Monitor-insikt kan du komma åt den från **Övervakning** på varje resurs meny. Få tillgång till alla insikter och lösningar från **Azure Monitor-menyn.**

![Insikter](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Mått
Analysera mått i Azure-portalen med hjälp av [statistikutforskaren](../platform/metrics-getting-started.md) som är tillgänglig från **menyalternativet Mått** för de flesta tjänster. Med det här verktyget kan du arbeta med enskilda mått eller kombinera flera för att identifiera korrelationer och trender. 

- Se [Komma igång med Azure Metrics Explorer](../platform/metrics-getting-started.md) för grunderna i att använda statistik explorer.
- Se [Avancerade funktioner i Azure Metrics Explorer](../platform/metrics-charts.md) för avancerade funktioner i utforskaren för mått, till exempel använda flera mått och använda filter och delning.

![Mått](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Aktivitetslogg 
Visa poster i aktivitetsloggen i Azure-portalen med det första filtret inställt på den aktuella resursen. Kopiera aktivitetsloggen till en Log Analytics-arbetsyta för att komma åt den för att använda den i loggfrågor och arbetsböcker. 

- Se [Visa och hämta Azure Activity-logghändelser](../platform/activity-log-view.md) för information om hur du visar aktivitetsloggen och hämtar poster med hjälp av en mängd olika metoder.
- Se dokumentationen för din Azure-tjänst för de specifika händelser som loggas.

![Aktivitetslogg](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure Monitor-loggar
Azure Monitor Loggar konsoliderar loggar och mått från flera tjänster och andra datakällor för analys med ett kraftfullt frågeverktyg. Som beskrivits ovan skapar du en diagnostikinställning för att samla in plattformsmått, aktivitetsloggar och resursloggar i en Log Analytics-arbetsyta i Azure Monitor.

[Med Log Analytics](../log-query/get-started-portal.md) kan du arbeta med [loggfrågor](../log-query/log-query-overview.md), vilket är en kraftfull funktion i Azure Monitor som gör att du kan utföra avancerad analys av loggdata med hjälp av ett fullständigt frågespråk. Öppna Log Analytics från loggar på **övervakningsmenyn** för en Azure-resurs för att arbeta med loggfrågor med hjälp av resursen som **Monitoring** [frågeomfattning](../log-query/scope.md#query-scope). På så sätt kan du analysera data över flera tabeller för just den resursen. Använd **loggar** från Azure Monitor-menyn för att komma åt loggar för alla resurser. 

- Se [Komma igång med loggfrågor i Azure Monitor](../log-query/get-started-queries.md) för en självstudiekurs om hur du använder frågespråket som används för att skriva loggfrågor.
- Se [Samla in Azure-resursloggar i Log Analytics-arbetsytan i Azure Monitor](../platform/resource-logs-collect-workspace.md) för information om hur resursloggar samlas in i Azure Monitor-loggar och information om hur du kommer åt dem i en fråga.
- Se [insamlingsläge](../platform/resource-logs-collect-workspace.md#resource-log-collection-mode) för en förklaring av hur resursloggdata är strukturerade i Azure Monitor Logs.
- Mer information om tabellen finns i dokumentationen för varje Azure-tjänst i Azure Monitor Logs.

![Loggar](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Övervakning från kommandoraden
Du kan komma åt övervakningsdata som samlats in från din resurs från en kommandorad eller inkludera i ett skript med [Azure PowerShell](/powershell/azure/) eller [Azure Command Line Interface](/cli/azure/). 

- Se [CLI-måttreferens](/cli/azure/monitor/metrics) för åtkomst till måttdata från CLI.
- Se [CLI Log Analytics-referens](/cli/azure/ext/log-analytics/monitor/log-analytics) för åtkomst till Azure Monitor Logs-data med hjälp av en loggfråga från CLI.
- Se [Azure PowerShell-måttreferens](/powershell/module/azurerm.insights/get-azurermmetric) för åtkomst till måttdata från Azure PowerShell.
- Se [Azure PowerShell-loggfrågereferens](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) för åtkomst till Azure Monitor Logs data med hjälp av en loggfråga från Azure PowerShell.

## <a name="monitoring-from-rest-api"></a>Övervakning från REST API
Inkludera övervakningsdata som samlats in från din resurs i ett anpassat program med hjälp av ett REST API.

- Se [Azure Monitoring REST API-genomgång](../platform/rest-api-walkthrough.md) för information om hur du kommer åt mått från AZURE Monitor REST API.
- Se [AZURE Log Analytics REST API](https://dev.loganalytics.io/) för information om hur du kommer åt Azure Monitor Loggar data med hjälp av en loggfråga från Azure PowerShell.

## <a name="alerts"></a>Aviseringar
[Aviseringar](../platform/alerts-overview.md) meddelar dig proaktivt och kan vidta åtgärder när viktiga villkor hittas i dina övervakningsdata. Du skapar en aviseringsregel som definierar ett mål för aviseringen, villkoren för om du vill skapa en avisering och eventuella åtgärder som ska vidtas som svar.

Olika typer av övervakningsdata används för olika typer av varningsregler.

- [Aktivitetsloggavisering](../platform/alerts-activity-log.md) - Skapa en avisering när en post skapas i aktivitetsloggen som matchar specifika villkor. På så sätt kan du meddelas till exempel när en viss typ av resurs skapas eller om en konfigurationsändring misslyckas.
- [Måttavisering](../platform/alerts-metric.md) - Skapa en avisering när ett måttvärde överskrider ett visst tröskelvärde. Måttaviseringar är mer responsiva än andra aviseringar och kan lösas automatiskt när problemet korrigeras.
- [Loggfrågavarning](../platform/alerts-log.md) - Kör en loggfråga med jämna mellanrum och skapa en avisering om ett visst villkor hittas. På så sätt kan du utföra komplexa analyser över flera datauppsättningar och .

Använd **aviseringar** från en resurs meny för att visa aviseringar och hantera varningsregler för den resursen. Endast aktivitetsloggaviseringar och måttaviseringar använder enskilda Azure-resurser som mål. Loggfrågavarningar använder en Log Analytics-arbetsyta som ett mål och baseras på en fråga som kan komma åt alla loggar som lagras på arbetsytan. Använd Azure Monitor-menyn för att visa och hantera aviseringar för alla resurser och hanteringsloggfrågevarningsregler.

- Mer information om hur du skapar varningsregler finns i artiklarna för de olika typerna av aviseringar ovan.
- Se [Skapa och hantera åtgärdsgrupper i Azure-portalen](../platform/action-groups.md) för information om hur du skapar en åtgärdsgrupp som gör att du kan hantera svar på aviseringar.



## <a name="next-steps"></a>Nästa steg

* Mer information om resursloggar för olika [Azure-tjänster finns i Tjänster, scheman och kategorier för Azure Resource Logs.](../platform/diagnostic-logs-schema.md)  
