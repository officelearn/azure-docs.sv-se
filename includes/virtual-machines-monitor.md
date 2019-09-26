---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/27/2019
ms.author: cynthn
ms.openlocfilehash: 11c9b2ea3ea054415f25f864651df28288aa0025
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266848"
---
Du kan dra nytta av många möjligheter att övervaka dina virtuella datorer genom att samla in, Visa och analysera diagnostik-och loggdata. Om du vill göra en enkel [övervakning](../articles/azure-monitor/overview.md) av den virtuella datorn kan du använda översikts skärmen för den virtuella datorn i Azure Portal. Du kan använda [tillägg](../articles/virtual-machines/windows/extensions-features.md) för att konfigurera diagnostik på de virtuella datorerna för att samla in ytterligare mått data. Du kan också använda mer avancerade övervaknings alternativ, till exempel [Application Insights](../articles/azure-monitor/app/app-insights-overview.md) och [Log Analytics](../articles/azure-monitor/log-query/log-query-overview.md).

## <a name="diagnostics-and-metrics"></a>Diagnostik och mått 

Du kan konfigurera och övervaka insamlingen av [diagnostikdata](https://docs.microsoft.com/cli/azure/vm/diagnostics) med hjälp av [mått](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) i Azure Portal, Azure CLI, Azure PowerShell och programmerings programmerings gränssnitt (API). Du kan till exempel:

- **Observera grundläggande mått för den virtuella datorn.** På översikts skärmen i Azure Portal innehåller de grundläggande måtten som visas processor användning, nätverks användning, totalt antal Disk byte och disk åtgärder per sekund.

- **Aktivera insamlingen av startdiagnostik och visa den med hjälp av Azure Portal.** När du använder en egen avbildning i Azure eller till och med startar en av plattforms avbildningarna kan det finnas många orsaker till att en virtuell dator får ett icke startbart tillstånd. Du kan enkelt aktivera startdiagnostik när du skapar en virtuell dator genom att klicka på **aktive rad** för startdiagnostik under övervaknings avsnittet på inställnings skärmen.

    Vid start av virtuella datorer inhämtar Boot Diagnostic-agenten startutdata och lagrar den i Azure Storage. Dessa data kan användas för att felsöka startproblem med de virtuella datorerna. Startdiagnostik aktive ras inte automatiskt när du skapar en virtuell dator från kommando rads verktyg. Innan du aktiverar startdiagnostik måste du skapa ett lagringskonto för att lagra startloggarna. Om du aktiverar startdiagnostik i Azure Portal skapas ett lagrings konto automatiskt åt dig.

    Om du inte har aktiverat startdiagnostik när den virtuella datorn skapades, kan du alltid aktivera den senare med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)eller en [Azure Resource Manager mall](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Aktivera insamling av gäst operativ systemets diagnostikdata.** När du skapar en virtuell dator har du möjlighet att aktivera gäst operativ systemets diagnostik med inställnings skärmen. När du aktiverar samlingen av diagnostikdata läggs [IaaSDiagnostics-tillägget för Linux](../articles/virtual-machines/linux/diagnostic-extension.md) eller [IaaSDiagnostics-tillägget för Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) till i den virtuella datorn, vilket gör att du kan samla in ytterligare disk-, CPU-och minnes data.

    Med hjälp av insamlade diagnostikdata kan du konfigurera automatisk skalning för dina virtuella datorer. Du kan också konfigurera loggar för att lagra data och konfigurera aviseringar så att du vet när prestandan inte är riktigt korrekt.

## <a name="alerts"></a>Aviseringar

Du kan skapa [aviseringar](../articles/azure-monitor/platform/alerts-overview.md) baserat på vissa prestanda mått. Exempel på problem som du kan aviseras om inkluderar när den genomsnittliga CPU-användningen överskrider ett visst tröskelvärde eller ledigt disk utrymme sjunker under en viss mängd. Aviseringar kan konfigureras i [Azure Portal](../articles/azure-monitor/platform/alerts-classic-portal.md), med [Azure POWERSHELL](../articles/azure-monitor/platform/alerts-classic-portal.md#with-powershell)eller [Azure CLI](../articles/azure-monitor/platform/alerts-classic-portal.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure Service Health

[Azure Service Health](../articles/service-health/service-health-overview.md) ger personlig vägledning och support när problem med Azure-tjänster påverkar dig och hjälper dig att förbereda för kommande planerat underhåll. Azure Service Health varnar dig och dina team med hjälp av riktade och flexibla meddelanden.

## <a name="azure-resource-health"></a>Azure Resource Health

[Azure Resource Health](../articles/service-health/resource-health-overview.md) hjälper dig att diagnostisera och få support när ett Azure-problem påverkar dina resurser. Det informerar dig om det aktuella och tidigare hälsotillståndet för dina resurser och hjälper dig att åtgärda problem. Resource Health ger teknisk support när du behöver hjälp med problem med Azure-tjänster.

## <a name="azure-activity-log"></a>Azure-aktivitetsloggen

[Azure aktivitets loggen](../articles/azure-monitor/platform/activity-logs-overview.md) är en prenumerations logg som ger inblick i händelser på prenumerations nivå som har inträffat i Azure. Loggen innehåller en mängd data, från Azure Resource Manager användnings data till uppdateringar på Service Health händelser. Du kan klicka på aktivitets logg i Azure Portal för att visa loggen för den virtuella datorn.

Några av de saker du kan göra med aktivitets loggen är:

- Skapa en [avisering för en aktivitets logg händelse](../articles/azure-monitor/platform/activity-logs-overview.md).
- [Strömma det till en Event Hub](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) för inmatning av en tjänst från tredje part eller en anpassad analys lösning som PowerBI.
- Analysera den i PowerBI med hjälp av [PowerBI-innehålls paketet](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Spara det till ett lagrings konto](../articles/azure-monitor/platform/archive-activity-log.md) för arkivering eller manuell kontroll. Du kan ange Retentions tiden (i dagar) med hjälp av logg profilen.

Du kan också komma åt aktivitets logg data med hjälp av [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), [Azure CLI](https://docs.microsoft.com/cli/azure/monitor)eller [övervaka REST-API: er](https://docs.microsoft.com/rest/api/monitor/).

[Azure Resource logs](../articles/azure-monitor/platform/resource-logs-overview.md) är loggar som genereras av den virtuella datorn som ger omfattande, frekventa data om hur de fungerar. Resurs loggar skiljer sig från aktivitets loggen genom att tillhandahålla insikter om åtgärder som utförts i den virtuella datorn.

Några av de saker du kan göra med diagnostikloggar är:

- [Spara dem till ett lagrings konto](../articles/azure-monitor/platform/archive-diagnostic-logs.md) för granskning eller manuell kontroll. Du kan ange Retentions tiden (i dagar) med hjälp av inställningarna för resurs diagnostik.
- [Strömma dem till Event Hubs](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) för inmatning av en tjänst från tredje part eller en anpassad analys lösning som PowerBI.
- Analysera dem med [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Avancerad övervakning

- [Azure Monitor](../articles/azure-monitor/overview.md) är en tjänst som övervakar molnet och lokala miljöer för att bevara deras tillgänglighet och prestanda. Den innehåller en omfattande lösning för att samla in, analysera och agera på telemetri från molnet och lokala miljöer. Det hjälper dig att förstå hur dina program fungerar och identifierar proaktivt problem som påverkar dem och de resurser som de förlitar sig på. Du kan installera ett tillägg på en [virtuell Linux-dator](../articles/virtual-machines/linux/extensions-oms.md) eller en [virtuell Windows-dator](../articles/virtual-machines/windows/extensions-oms.md) som installerar Log Analytics agent för att samla in loggdata och lagra data i en Log Analytics arbets yta.

    För virtuella Windows-och Linux-datorer är den rekommenderade metoden för att samla in loggar att installera Log Analytics agenten. Det enklaste sättet att installera Log Analytics agenten på en virtuell dator är genom [Log Analytics VM-tillägget](../articles/log-analytics/log-analytics-azure-vm-extension.md). Med hjälp av tillägget förenklas installationen och agenten konfigureras automatiskt att skicka data till den Log Analytics-arbetsyta som du anger. Agenten uppgraderas också automatiskt så att du alltid har de senaste funktionerna och korrigeringarna.

- Med [Network Watcher](../articles/network-watcher/network-watcher-monitoring-overview.md) kan du övervaka den virtuella datorn och dess associerade resurser när de relaterar till nätverket som de finns i. Du kan installera Network Watcher agent-tillägget på en [virtuell Linux-dator](../articles/virtual-machines/linux/extensions-nwa.md) eller en [virtuell Windows-dator](../articles/virtual-machines/windows/extensions-nwa.md).

- [Azure Monitor for VMS](../articles/azure-monitor/insights/vminsights-overview.md) övervakar dina virtuella Azure-datorer (VM) i stor skala genom att analysera prestanda och hälsa för dina virtuella Windows-och Linux-datorer, inklusive deras olika processer och sammankopplade beroenden för andra resurser och externa tillverknings. 

## <a name="next-steps"></a>Nästa steg
- Gå igenom stegen i [övervaka en virtuell Windows-dator med Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) eller [övervaka en virtuell Linux-dator med Azure CLI](../articles/virtual-machines/linux/tutorial-monitoring.md).
- Lär dig mer om bästa praxis kring [övervakning och diagnostik](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
