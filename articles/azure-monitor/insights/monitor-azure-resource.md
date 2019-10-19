---
title: Övervaka Azure-resurser med Azure Monitor | Microsoft Docs
description: Beskriver hur du samlar in och analyserar övervaknings data från resurser i Azure med hjälp av Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 1ec241e261a7710b7a5b92d88f147ce8d148602b
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554038"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Övervaka Azure-resurser med Azure Monitor
När du har viktiga program och affärs processer som förlitar sig på Azure-resurser, vill du övervaka resurserna för deras tillgänglighet, prestanda och drift. I den här artikeln beskrivs övervaknings data som genereras av Azure-resurser och hur du kan använda funktionerna i Azure Monitor för att analysera och varna för dessa data.

> [!IMPORTANT]
> Den här artikeln gäller för alla tjänster i Azure som använder Azure Monitor. Beräknings resurser, inklusive virtuella datorer och App Service, genererar samma övervaknings data som beskrivs här, men har även ett gäst operativ system som också kan generera loggar och mått. I övervaknings dokumentationen för dessa tjänster finns mer information om hur du samlar in och analyserar dessa data.

## <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?
Azure Monitor är en fullständig stack övervaknings tjänst i Azure som innehåller en fullständig uppsättning funktioner för att övervaka dina Azure-resurser, förutom resurser i andra moln och lokalt. [Azure Monitor data plattform](../platform/data-platform.md) samlar in data i [loggar](../platform/data-platform-logs.md) och [Mät värden](../platform/data-platform-metrics.md) där de kan analyseras med en fullständig uppsättning övervaknings verktyg enligt beskrivningen i följande avsnitt.

- [Vad kan du göra med Azure Monitor mått?](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [Vad kan du göra med Azure Monitor loggar?](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

När du skapar en Azure-resurs är Azure Monitor aktive rad och börjar samla in mått och aktivitets loggar som du kan [Visa och analysera i Azure Portal](#monitoring-in-the-azure-portal). Med viss konfiguration kan du samla in ytterligare övervaknings data och aktivera ytterligare funktioner. Mer information om konfigurations krav finns i [övervaknings data](#monitoring-data) nedan.


## <a name="costs-associated-with-monitoring"></a>Kostnader för övervakning
Det kostar inget att analysera övervaknings data som samlas in som standard. Detta omfattar följande:

- Samla in plattforms mått och analysera dem med Metrics Explorer.
- Samla in aktivitets logg och analysera den i Azure Portal.
- Skapar en varnings regel för aktivitets loggen.

Det finns inga Azure Monitor kostnader för insamling och export av loggar och mått, men det kan finnas relaterade kostnader som är kopplade till målet:

- Kostnader som är kopplade till data inmatning och kvarhållning vid insamling av loggar och mått i Log Analytics-arbetsyta. Se [Azure Monitor prissättning för Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).
- Kostnader som är kopplade till data lagring vid insamling av loggar och mått till ett Azure Storage-konto. Se [Azure Storage priser för Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).
- Kostnader som är kopplade till strömmande av Event Hub när du vidarebefordrar loggar och mått till Azure Event Hubs. Se [priser för Azure Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

Det kan finnas Azure Monitor kostnader som är kopplade till följande. Se [Azure Monitor priser](https://azure.microsoft.com/pricing/details/monitor/):

- Kör en logg fråga.
- Skapa en varnings regel för mått eller logg fråga.
- Skickar ett meddelande från en varnings regel.
- Åtkomst till mått via API.

## <a name="monitoring-data"></a>Övervaka data
Resurser i Azure genererar [loggar](../platform/data-platform-logs.md) och [mått](../platform/data-platform-metrics.md) som visas i följande diagram. Se dokumentationen för varje Azure-tjänst för de data som de genererar och eventuella ytterligare lösningar eller insikter som de tillhandahåller.

![Översikt](media/monitor-azure-resource/logs-metrics.png)



- [Plattforms mått](../platform/data-platform-metrics.md) – numeriska värden som samlas in automatiskt med jämna mellanrum och som beskriver viss aspekt av en resurs vid en viss tidpunkt. 
- [Resurs loggar](../platform/resource-logs-overview.md) – ger insikter om åtgärder som utförts i en Azure-resurs (data planet), till exempel för att få en hemlighet från en Key Vault eller en begäran till en databas. Innehållet och strukturen i resurs loggar varierar beroende på Azure-tjänsten och resurs typen.
- [Aktivitets logg](../platform/activity-logs-overview.md) – ger inblick i åtgärderna på varje Azure-resurs i prenumerationen från utsidan (hanterings planet), till exempel när du skapar en ny resurs eller startar en virtuell dator. Det här är information om vad, vem och när för Skriv åtgärder (skicka, skicka och ta bort) som har tagits till resurserna i din prenumeration.


## <a name="configuration-requirements"></a>Konfigurations krav
Vissa övervaknings data samlas in automatiskt, men du kan behöva utföra vissa konfigurationer beroende på dina behov. Se informationen nedan om du vill ha detaljerad information för varje typ av övervaknings data.

- [Plattforms mått](../platform/data-platform-metrics.md) – plattforms mått samlas in automatiskt i [Azure Monitor Mät värden](../platform/data-platform-metrics.md) utan konfiguration krävs. Skapa en diagnostisk inställning för att skicka poster till Azure Monitor loggar eller vidarebefordra dem utanför Azure.
- [Resurs loggar](../platform/resource-logs-overview.md) – resurs loggar genereras automatiskt av Azure-resurser, men samlas inte in utan en diagnostisk inställning.  Skapa en diagnostisk inställning för att skicka poster till Azure Monitor loggar eller vidarebefordra dem utanför Azure.
- [Aktivitets logg](../platform/activity-logs-overview.md) – aktivitets loggen samlas in automatiskt utan konfiguration som krävs och kan visas i Azure Portal. Skapa en diagnostisk inställning för att kopiera dem till Azure Monitor loggar eller vidarebefordra dem utanför Azure.


## <a name="diagnostic-settings"></a>Diagnostikinställningar
Diagnostiska inställningar definierar var resurs loggar och-mått för en viss resurs ska skickas. Möjliga destinationer är:

- [Log Analytics arbets yta](../platform/resource-logs-collect-workspace.md) som gör det möjligt att analysera data med andra övervaknings data som samlas in av Azure monitor med hjälp av kraftfulla logg frågor och också för att utnyttja andra Azure Monitor funktioner som logg aviseringar och visualiseringar. 
- [Event Hub](../platform/resource-logs-stream-event-hubs.md) för att strömma data till externa system, till exempel Siem från tredje part och andra Log Analytics-lösningar. 
- [Azure Storage-konto](../platform/resource-logs-collect-storage.md) som är användbart för granskning, statisk analys eller säkerhets kopiering.

Följ proceduren i [skapa diagnostisk inställning för att samla in plattforms loggar och mått i Azure](../platform/diagnostic-settings.md) för att skapa och hantera diagnostikinställningar via Azure Portal. Se [skapa diagnostisk inställning i Azure med hjälp av en Resource Manager-mall](../platform/diagnostic-settings-template.md) för att definiera dem i en mall och aktivera fullständig övervakning för en resurs när den skapas.


## <a name="monitoring-in-the-azure-portal"></a>Övervakning i Azure Portal
 Du kan komma åt övervaknings data för de flesta Azure-resurser från resurs menyn i Azure Portal. Detta ger dig åtkomst till en enskild resurs data med hjälp av standard Azure Monitor verktyg. Vissa Azure-tjänster tillhandahåller olika alternativ, så du bör hänvisa till dokumentationen för tjänsten för ytterligare information. Använd **Azure Monitor** -menyn om du vill analysera data från alla övervakade resurser. 

Många tjänster omfattar övervaknings data på sina **översikts** sidor så att du snabbt kan se hur de fungerar. Detta är vanligt vis baserat på en delmängd av plattforms måtten som lagras i Azure Monitor Mät värden. Andra övervaknings alternativ är vanligt vis tillgängliga i ett **övervaknings** avsnitt i tjänsterna. hoppmeny.

![Översikts sida](media/monitor-azure-resource/overview-page.png)

## <a name="analyzing-metrics"></a>Analyserar mått
Analysera enskilda mått och korrelera flera mått för att identifiera korrelationer och trender med hjälp av [Metrics Explorer](../platform/metrics-getting-started.md). Vissa tjänster ger en anpassad upplevelse för att arbeta med sina mått när du öppnar **mått** från resurs menyn.

- Mer information om hur du använder Metrics Explorer finns i [komma igång med Azure Metrics Explorer](../platform/metrics-getting-started.md) .
- Se [avancerade funktioner i Azure Metrics Explorer](../platform/metrics-charts.md) för avancerade funktioner i mått Utforskaren, till exempel att använda flera mått och använda filter och delning.

![Mått](media/monitor-azure-resource/metrics.png)


## <a name="analyzing-logs"></a>Analysera loggar

### <a name="activity-log"></a>Aktivitetslogg 
Visa poster i aktivitets loggen i Azure Portal med det ursprungliga filtret inställt på den aktuella resursen. Kopiera aktivitets loggen till en Log Analytics-arbetsyta för att komma åt den för att använda den i logg frågor och arbets böcker. 

- Se [Visa och hämta händelser i Azure aktivitets loggen](../platform/activity-log-view.md) om du vill ha mer information om hur du visar aktivitets loggen och hämtar poster med hjälp av olika metoder.
- I dokumentationen för Azure-tjänsten finns information om vilka händelser som loggas.

![Aktivitetslogg](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure Monitor-loggar
Azure Monitor loggar konsoliderar loggar och mått från flera tjänster och andra data källor för analys med ett kraftfullt verktyg för frågor. Som beskrivs ovan skapar du en diagnostisk inställning för att samla in plattforms mått, aktivitets logg och resurs loggar i en Log Analytics arbets yta i Azure Monitor.

Med [Log Analytics](../log-query/get-started-portal.md) kan du arbeta med [logg frågor](../log-query/log-query-overview.md), vilket är en kraftfull funktion i Azure Monitor som gör att du kan utföra avancerad analys av loggdata med ett fullständigt aktuellt frågespråk. Öppna Log Analytics från **loggar** på menyn **övervakning** för en Azure-resurs för att arbeta med logg frågor med hjälp av resursen som [fråge omfånget](../log-query/scope.md#query-scope). På så sätt kan du analysera data i flera tabeller för just den resursen. Använd **loggar** från Azure Monitor-menyn för att få åtkomst till loggar för alla resurser. 

- Se [Kom igång med Log Analytics i Azure Monitor](../log-query/get-started-portal.md) för en genom gång av hur du använder Log Analytics för att skriva en fråga och arbeta med resultaten.
- I [Kom igång med logg frågor i Azure Monitor](../log-query/get-started-queries.md) finns en själv studie kurs om hur du använder frågespråket som används för att skriva logg frågor.
- Mer information om hur resurs loggar samlas in i Azure Monitor loggar och information om hur du kommer åt dem i en fråga finns i [samla in Azure-resursposter i Log Analytics arbets ytan i Azure Monitor](../platform/resource-logs-collect-workspace.md) .
- Se [insamlings läge](../platform/resource-logs-collect-workspace.md#collection-mode) för en förklaring av hur resurs logg data är strukturerade i Azure Monitor loggar.
- I dokumentationen för varje Azure-tjänst finns mer information om tabellen i Azure Monitor loggar.

![Loggar](media/monitor-azure-resource/logs.png)


## <a name="alerts"></a>Aviseringar
[Aviseringar](../platform/alerts-overview.md) proaktivt meddelar dig och kan vidta åtgärder när viktiga villkor finns i dina övervaknings data. Du skapar en aviserings regel som definierar ett mål för aviseringen, villkoren för om du vill skapa en avisering och vilka åtgärder som ska vidtas som svar.

Olika typer av övervaknings data används för olika typer av varnings regler.

- [Aktivitets logg avisering](../platform/alerts-activity-log.md) – skapa en avisering när en post skapas i aktivitets loggen som matchar vissa villkor. På så sätt kan du bli meddelad till exempel när en viss typ av resurs skapas eller om en konfigurations ändring Miss lyckas.
- [Mått varning](../platform/alerts-metric.md) – skapa en avisering när ett Metric-värde överskrider ett visst tröskelvärde. Mått varningar är mer svars bara än andra aviseringar och kan lösas automatiskt när problemet har åtgärd ATS.
- [Varning om logg fråga](../platform/alerts-log.md) – kör en logg fråga med jämna mellanrum och skapa en avisering om ett visst villkor hittas. På så sätt kan du utföra komplexa analyser i flera data uppsättningar och.

Använd **aviseringar** från en resurs meny för att visa aviseringar och hantera aviserings regler för resursen. Endast aktivitets logg aviseringar och mått varningar använder enskilda Azure-resurser som mål. Varnings aviseringar för logg frågor använder en Log Analytics-arbetsyta som mål och baseras på en fråga som har åtkomst till alla loggar som lagras på arbets ytan. Använd Azure Monitor-menyn om du vill visa och hantera aviseringar för alla resurser och aviserings reglerna för att hantera logg frågor.

- Se artiklarna för de olika typerna av aviseringar ovan för information om hur du skapar aviserings regler.
- Se [skapa och hantera åtgärds grupper i Azure Portal](../platform/action-groups.md) för information om hur du skapar en åtgärds grupp som gör att du kan hantera svar på aviseringar.

## <a name="insights-and-solutions"></a>Insikter och lösningar 
Vissa tjänster tillhandahåller verktyg utöver standard funktionerna i Azure Monitor. [Lösningar](../insights/solutions.md) tillhandahåller fördefinierad övervaknings logik som bygger på standard Azure Monitor funktioner. [Insikter](../insights/insights-overview.md) ger en anpassad övervaknings upplevelse som bygger på Azure Monitor data plattform och standard funktioner.

Om en resurs har en insikt tillgänglig, kan du komma åt den från **insikter** på resurs-menyn. Få till gång till alla insikter och lösningar från Azure Monitor-menyn.

- Se övervaknings dokumentationen för varje tjänst för att avgöra om det finns insikter eller lösningar tillgängliga.

![Insikter](media/monitor-azure-resource/insights.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om resurs loggar för olika Azure-tjänster finns i [tjänster, scheman och kategorier som stöds för Azure Diagnostic-loggar](../platform/diagnostic-logs-schema.md) .  
