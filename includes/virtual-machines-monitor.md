---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: fbc6889507e58c4721597a1108337fcb1f8756a2
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75752206"
---
Med den betydande tillväxten för virtuella datorer som finns i Azure är det viktigt att identifiera prestanda-och hälso problem som påverkar program och infrastruktur tjänster som de stöder. Grundläggande övervakning levereras som standard med Azure av mått typerna CPU-användning, disk användning, minnes användning och nätverks trafik som samlas in av värdens hypervisor. Ytterligare mått-och loggdata kan samlas in med hjälp av [tillägg](../articles/virtual-machines/windows/extensions-features.md) för att konfigurera diagnostik på dina virtuella datorer från gäst operativ systemet.

För att identifiera och hjälpa till att diagnostisera prestanda-och hälso problem med gäst operativ systemet, .NET-baserade eller Java-komponenter för webb program som körs inuti den virtuella datorn, Azure Monitor levererar centraliserad övervakning med omfattande funktioner som Azure Monitor for VMs och Application Insights.

## <a name="diagnostics-and-metrics"></a>Diagnostik och mått 

Du kan konfigurera och övervaka insamlingen av [diagnostikdata](https://docs.microsoft.com/cli/azure/vm/diagnostics) med hjälp av [mått](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) i Azure Portal, Azure CLI, Azure PowerShell och programmerings programmerings gränssnitt (API). Du kan till exempel:

- **Observera grundläggande mått för den virtuella datorn.** På översikts skärmen i Azure Portal innehåller de grundläggande måtten som visas processor användning, nätverks användning, totalt antal Disk byte och disk åtgärder per sekund.

- **Aktivera insamlingen av startdiagnostik och visa den med hjälp av Azure Portal.** När du använder en egen avbildning i Azure eller till och med startar en av plattforms avbildningarna kan det finnas många orsaker till att en virtuell dator får ett icke startbart tillstånd. Du kan enkelt aktivera startdiagnostik när du skapar en virtuell dator genom att klicka på **aktive rad** för startdiagnostik under övervaknings avsnittet på inställnings skärmen.

    Vid start av virtuella datorer inhämtar Boot Diagnostic-agenten startutdata och lagrar den i Azure Storage. Dessa data kan användas för att felsöka startproblem med de virtuella datorerna. Startdiagnostik aktive ras inte automatiskt när du skapar en virtuell dator från kommando rads verktyg. Innan du aktiverar startdiagnostik måste du skapa ett lagringskonto för att lagra startloggarna. Om du aktiverar startdiagnostik i Azure Portal skapas ett lagrings konto automatiskt åt dig.

    Om du inte har aktiverat startdiagnostik när den virtuella datorn skapades, kan du alltid aktivera den senare med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)eller en [Azure Resource Manager mall](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Aktivera insamling av gäst operativ systemets diagnostikdata.** När du skapar en virtuell dator har du möjlighet att aktivera gäst operativ systemets diagnostik med inställnings skärmen. När du aktiverar samlingen av diagnostikdata läggs [IaaSDiagnostics-tillägget för Linux](../articles/virtual-machines/linux/diagnostic-extension.md) eller [IaaSDiagnostics-tillägget för Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) till i den virtuella datorn, vilket gör att du kan samla in ytterligare disk-, CPU-och minnes data.

    Med hjälp av insamlade diagnostikdata kan du konfigurera automatisk skalning för dina virtuella datorer. Du kan också konfigurera [Azure Monitor loggar](../articles/azure-monitor/platform/data-platform-logs.md) för att lagra data och konfigurera aviseringar så att du vet när prestanda inte stämmer.

## <a name="alerts"></a>Aviseringar

Du kan skapa [aviseringar](../articles/azure-monitor/platform/alerts-overview.md) baserat på vissa prestanda mått. Exempel på problem som du kan aviseras om inkluderar när den genomsnittliga CPU-användningen överskrider ett visst tröskelvärde eller ledigt disk utrymme sjunker under en viss mängd. Aviseringar kan konfigureras i [Azure Portal](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal), med hjälp av [Azure Resource Manager mallar](../articles/azure-monitor/platform/alerts-metric-create-templates.md)eller [Azure CLI](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure Tjänstehälsa

[Azure Service Health](../articles/service-health/service-health-overview.md) ger personlig vägledning och support när problem med Azure-tjänster påverkar dig och hjälper dig att förbereda för kommande planerat underhåll. Azure Service Health varnar dig och dina team med hjälp av riktade och flexibla meddelanden.

## <a name="azure-resource-health"></a>Azure Resource Health

[Azure Resource Health](../articles/service-health/resource-health-overview.md) hjälper dig att diagnostisera och få support när ett Azure-problem påverkar dina resurser. Du får information om resursernas aktuella och tidigare hälsa samt hjälp med att åtgärda problem. Resource Health ger teknisk support när du behöver hjälp med problem som rör Azure-tjänsten.

## <a name="azure-activity-log"></a>Azure-aktivitetslogg

[Azure aktivitets loggen](../articles/azure-monitor/platform/platform-logs-overview.md) är en prenumerations logg som ger inblick i händelser på prenumerations nivå som har inträffat i Azure. Loggen innehåller en mängd data, från Azure Resource Manager användnings data till uppdateringar på Service Health händelser. Du kan klicka på aktivitets logg i Azure Portal för att visa loggen för den virtuella datorn.

Några av de saker du kan göra med aktivitets loggen är:

- Skapa en [avisering för en aktivitets logg händelse](../articles/azure-monitor/platform/platform-logs-overview.md).
- [Strömma den till en händelsehubben](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) för inmatning av en tjänst från tredje part eller en anpassad analys lösning som Power BI.
- Analysera den i Power BI med hjälp av [Power BI innehålls paketet](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Spara det till ett lagrings konto](../articles/azure-monitor/platform/archive-activity-log.md) för arkivering eller manuell kontroll. Du kan ange Retentions tiden (i dagar) med hjälp av logg profilen.

Du kan också komma åt aktivitets logg data med hjälp av [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), [Azure CLI](https://docs.microsoft.com/cli/azure/monitor)eller [övervaka REST-API: er](https://docs.microsoft.com/rest/api/monitor/).

[Azure Resource logs](../articles/azure-monitor/platform/platform-logs-overview.md) är loggar som genereras av den virtuella datorn som ger omfattande, frekventa data om hur de fungerar. Resurs loggar skiljer sig från aktivitets loggen genom att tillhandahålla insikter om åtgärder som utförts i den virtuella datorn.

Några av de saker du kan göra med diagnostikloggar är:

- [Spara dem till ett lagrings konto](../articles/azure-monitor/platform/archive-diagnostic-logs.md) för granskning eller manuell kontroll. Du kan ange Retentions tiden (i dagar) med hjälp av inställningarna för resurs diagnostik.
- [Strömma dem till Event Hubs](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) för inmatning av en tjänst från tredje part eller en anpassad analys lösning som Power BI.
- Analysera dem med [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Avancerad övervakning

För att få insyn i programmet eller tjänsten som stöds av den virtuella Azure-datorn och skalnings uppsättningar för virtuella datorer, identifiera problem med gäst operativ systemet eller arbets belastningen som körs på den virtuella datorn för att förstå om den påverkar tillgänglighet eller prestanda för programmet, eller är ett problem med programmet, aktivera både [Azure Monitor for VMS](../articles/azure-monitor/insights/vminsights-overview.md) och [Application Insights](../articles/azure-monitor/app/app-insights-overview.md).

Azure Monitor for VMs övervakar dina virtuella Azure-datorer (VM) i skala genom att analysera prestanda och hälsa för dina virtuella Windows-och Linux-datorer, inklusive olika processer och sammankopplade beroenden för andra resurser och externa processer identifierar. Det innehåller flera trend prestanda diagram som hjälper dig att undersöka problem och utvärdera kapaciteten för dina virtuella datorer. Beroende kartan visar övervakade och oövervakade datorer, misslyckade och aktiva nätverks anslutningar mellan processer och dessa datorer och visar trend diagram med standard mått för nätverks anslutning. Tillsammans med Application Insights övervakar du ditt program och samlar in telemetri, till exempel HTTP-begäranden, undantag osv. så att du kan korrelera problem mellan de virtuella datorerna och ditt program. Konfigurera [Azure Monitor aviseringar](../articles/azure-monitor/platform/alerts-overview.md) för att varna dig om viktiga villkor som har identifierats av övervaknings data som samlas in av Azure Monitor for VMS.

## <a name="next-steps"></a>Nästa steg

- Gå igenom stegen i [övervaka en virtuell Windows-dator med Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) eller [övervaka en virtuell Linux-dator med Azure CLI](../articles/virtual-machines/linux/tutorial-monitoring.md).

- Lär dig mer om bästa praxis kring [övervakning och diagnostik](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
