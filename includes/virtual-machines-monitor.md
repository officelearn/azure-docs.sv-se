---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: fbc6889507e58c4721597a1108337fcb1f8756a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75752206"
---
Med den betydande tillväxten av virtuella datorer som finns i Azure är det viktigt att identifiera prestanda- och hälsoproblem som påverkar program och infrastrukturtjänster som de stöder. Grundläggande övervakning levereras som standard med Azure av måtttyperna CPU-användning, diskanvändning, minnesanvändning och nätverkstrafik som samlas in av värdens hypervisor. Ytterligare mått- och loggdata kan samlas in med [tillägg](../articles/virtual-machines/windows/extensions-features.md) för att konfigurera diagnostik på dina virtuella datorer från gästoperativsystemet.

För att identifiera och hjälpa till att diagnostisera prestanda- och hälsoproblem med gästoperativsystemet, .NET-baserade eller Java-webbprogramkomponenter som körs i den virtuella datorn, levererar Azure Monitor centraliserad övervakning med omfattande funktioner som Azure Monitor för virtuella datorer och Application Insights.

## <a name="diagnostics-and-metrics"></a>Diagnostik och mått 

Du kan ställa in och övervaka insamlingen av [diagnostikdata](https://docs.microsoft.com/cli/azure/vm/diagnostics) med hjälp av [mått](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) i Azure-portalen, Azure CLI, Azure PowerShell och programmering av programmeringsgränssnitt (API:er). Du kan till exempel:

- **Observera grundläggande mått för den virtuella datorn.** På översiktsskärmen för Azure-portalen är de grundläggande måtten som visas CPU-användning, nätverksanvändning, totalt diskbyte och diskåtgärder per sekund.

- **Aktivera insamling av startdiagnostik och visa den med Azure-portalen.** När du tar med din egen avbildning till Azure eller till och med startar en av plattformsavbildningarna kan det finnas många orsaker till att en virtuell dator hamnar i ett icke-startbart tillstånd. Du kan enkelt aktivera startdiagnostik när du skapar en virtuell dator genom att klicka på **Aktiverad** för startdiagnostik under avsnittet Övervakning på skärmen Inställningar.

    Som virtuella datorer startar, fångar boot diagnostic agent startutdata och lagrar den i Azure-lagring. Dessa data kan användas för att felsöka startproblem med de virtuella datorerna. Startdiagnostik aktiveras inte automatiskt när du skapar en virtuell dator från kommandoradsverktyg. Innan du aktiverar startdiagnostik måste du skapa ett lagringskonto för att lagra startloggarna. Om du aktiverar startdiagnostik i Azure-portalen skapas ett lagringskonto automatiskt åt dig.

    Om du inte aktiverade startdiagnostik när den virtuella datorn skapades kan du alltid aktivera den senare med hjälp av [Azure CLI,](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics) [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)eller en [Azure Resource Manager-mall](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Aktivera insamling av gäst-OS diagnostikdata.** När du skapar en virtuell dator har du möjlighet på inställningsskärmen att aktivera gäst-OS-diagnostik. När du aktiverar insamling av diagnostikdata läggs [IaaSDiagnostics-tillägget för Linux](../articles/virtual-machines/linux/diagnostic-extension.md) eller [IaaSDiagnostics-tillägget för Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) till den virtuella datorn, vilket gör att du kan samla in ytterligare disk-, CPU- och minnesdata.

    Med hjälp av insamlade diagnostikdata kan du konfigurera automatisk skalning för dina virtuella datorer. Du kan också konfigurera [Azure Monitor Logs](../articles/azure-monitor/platform/data-platform-logs.md) för att lagra data och ställa in aviseringar så att du vet när prestanda inte är rätt.

## <a name="alerts"></a>Aviseringar

Du kan skapa [aviseringar](../articles/azure-monitor/platform/alerts-overview.md) baserat på specifika prestandamått. Exempel på de problem som du kan aviseras om är när genomsnittlig CPU-användning överskrider ett visst tröskelvärde, eller tillgängligt ledigt diskutrymme sjunker under ett visst belopp. Aviseringar kan konfigureras i [Azure-portalen](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal)med Azure [Resource Manager-mallar](../articles/azure-monitor/platform/alerts-metric-create-templates.md)eller [Azure CLI](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure Service Health

[Azure Service Health](../articles/service-health/service-health-overview.md) ger personlig vägledning och support när problem i Azure-tjänster påverkar dig och hjälper dig att förbereda dig för kommande planerat underhåll. Azure Service Health varnar dig och dina team med hjälp av riktade och flexibla meddelanden.

## <a name="azure-resource-health"></a>Azure Resource Health

[Azure Resource health](../articles/service-health/resource-health-overview.md) hjälper dig att diagnostisera och få support när ett Azure-problem påverkar dina resurser. Det informerar dig om det aktuella och tidigare hälsotillståndet för dina resurser och hjälper dig att åtgärda problem. Resource Health ger teknisk support när du behöver hjälp med problem med Azure-tjänster.

## <a name="azure-activity-log"></a>Azure-aktivitetslogg

[Azure Activity Log](../articles/azure-monitor/platform/platform-logs-overview.md) är en prenumerationslogg som ger insikt i händelser på prenumerationsnivå som har inträffat i Azure. Loggen innehåller en rad data, från Azure Resource Manager operativa data till uppdateringar om Service Health-händelser. Du kan klicka på Aktivitetslogg i Azure-portalen för att visa loggen för din virtuella dator.

Några av de saker du kan göra med aktivitetsloggen är:

- Skapa en [avisering för en aktivitetslogghändelse](../articles/azure-monitor/platform/platform-logs-overview.md).
- [Strömma den till en händelsehubb](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) för inmatning av en tjänst från tredje part eller anpassad analyslösning, till exempel Power BI.
- Analysera den i Power BI med [power bi-innehållspaketet](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Spara den på ett lagringskonto](../articles/azure-monitor/platform/archive-activity-log.md) för arkivering eller manuell inspektion. Du kan ange kvarhållningstiden (i dagar) med hjälp av loggprofilen.

Du kan också komma åt aktivitetsloggdata med hjälp av [Azure PowerShell,](https://docs.microsoft.com/powershell/module/azurerm.insights/) [Azure CLI](https://docs.microsoft.com/cli/azure/monitor)eller Monitor [REST API:er](https://docs.microsoft.com/rest/api/monitor/).

[Azure Resource Logs](../articles/azure-monitor/platform/platform-logs-overview.md) är loggar som skickas ut av din virtuella dator som ger omfattande, frekventa data om dess funktion. Resursloggar skiljer sig från aktivitetsloggen genom att ge insikt om åtgärder som utfördes inom den virtuella datorn.

Några av de saker du kan göra med diagnostikloggar är:

- [Spara dem på ett lagringskonto](../articles/azure-monitor/platform/archive-diagnostic-logs.md) för granskning eller manuell inspektion. Du kan ange kvarhållningstiden (i dagar) med hjälp av Resursdiagnostikinställningar.
- [Strömma dem till eventhubbar](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) för inmatning av en tjänst från tredje part eller anpassad analyslösning, till exempel Power BI.
- Analysera dem med [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Avancerad övervakning

För synlighet för programmet eller tjänsten som stöds av Azure VM och virtuella dator skalningsuppsättningar, identifiering av problem med gästoperativsystemet eller arbetsbelastning som körs i den virtuella datorn för att förstå om det påverkar tillgänglighet eller prestanda för programmet, eller är ett problem med programmet, aktivera både [Azure Monitor för virtuella datorer](../articles/azure-monitor/insights/vminsights-overview.md) och Application [Insights](../articles/azure-monitor/app/app-insights-overview.md).

Azure Monitor för virtuella datorer övervakar dina virtuella Azure-datorer (VM) i stor skala genom att analysera prestanda och hälsa för dina virtuella Windows- och Linux-datorer, inklusive de olika processer och sammankopplade beroenden på andra resurser och externa processer som Upptäcker. Den innehåller flera trendprestandadiagram för att hjälpa till under undersökning av problem och bedöma kapaciteten hos dina virtuella datorer. Beroendekartan visar övervakade och oövervakade datorer, misslyckade och aktiva nätverksanslutningar mellan processer och dessa datorer och visar trenddiagram med standardmått för nätverksanslutning. Kombinerat med Application Insights övervakar du ditt program och fångar in telemetri som HTTP-begäranden, undantag osv. Konfigurera [Azure Monitor-aviseringar](../articles/azure-monitor/platform/alerts-overview.md) för att varna dig om viktiga villkor som upptäckts från övervakningsdata som samlats in av Azure Monitor för virtuella datorer.

## <a name="next-steps"></a>Nästa steg

- Gå igenom stegen i [Övervaka en virtuell Windows-dator med Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) eller [Övervaka en virtuell Linux-dator med Azure CLI](../articles/virtual-machines/linux/tutorial-monitoring.md).

- Läs mer om de bästa metoderna [för övervakning och diagnostik](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
