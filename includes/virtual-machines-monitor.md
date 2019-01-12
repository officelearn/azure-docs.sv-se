---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 47b58e74f57640098751b38c1a4fb504838c9ced
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54242360"
---
Du kan dra nytta av många möjligheter att övervaka dina virtuella datorer genom att samla in, visa och analysera diagnostiska och logga data. Att göra enkla [övervakning](../articles/azure-monitor/overview.md) för den virtuella datorn kan du använda översiktsskärmen för den virtuella datorn i Azure-portalen. Du kan använda [tillägg](../articles/virtual-machines/windows/extensions-features.md) att konfigurera diagnostik på dina virtuella datorer att samla in ytterligare måttdata. Du kan också använda mer avancerade alternativ för övervakning, till exempel [Programinsikter](../articles/azure-monitor/app/app-insights-overview.md) och [Log Analytics](../articles/azure-monitor/log-query/log-query-overview.md).

## <a name="diagnostics-and-metrics"></a>Diagnostik och mått 

Du kan konfigurera och övervaka mängden [diagnostikdata](https://docs.microsoft.com/cli/azure/vm/diagnostics) med [mått](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) i Azure portal, Azure CLI, Azure PowerShell och program Programming programmeringsgränssnitt (API: er). Du kan till exempel:

- **Se basmått för den virtuella datorn.** På skärmen översikt av Azure-portalen är de grundläggande mått som visas CPU-användning, nätverksanvändning, totalt byte på disk och diskåtgärder per sekund.

- **Aktivera insamling av startdiagnostik och visa den med hjälp av Azure-portalen.** När du använder en egen avbildning till Azure eller även starta en av plattformsavbildningar, kan det finnas många orsaker till varför en virtuell dator i ett icke startbart tillstånd. Du kan enkelt aktivera startdiagnostik när du skapar en virtuell dator genom att klicka på **aktiverad** för Startdiagnostik under avsnittet övervakning av skärmen inställningar.

    Eftersom virtuella datorer startar boot diagnostikagenten samlar in startutdata och lagrar den i Azure storage. Dessa data kan användas för att felsöka startproblem med de virtuella datorerna. Startdiagnostik aktiveras inte automatiskt när du skapar en virtuell dator från kommandoradsverktyg. Innan du aktiverar startdiagnostik måste du skapa ett lagringskonto för att lagra startloggarna. Om du aktiverar startdiagnostik i Azure portal, skapas ett lagringskonto automatiskt åt dig.

    Om du inte aktiverar startdiagnostik när den virtuella datorn skapades, du kan alltid aktivera det senare med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmbootdiagnostics), eller en [Azure Resource Manager-mall](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Aktivera insamling av diagnostikdata för gäst-OS.** När du skapar en virtuell dator kan välja du på inställningsskärmen för att aktivera diagnostik för gäst-OS. När du aktiverar insamling av diagnostikdata, den [IaaSDiagnostics-tillägg för Linux](../articles/virtual-machines/linux/diagnostic-extension.md) eller [IaaSDiagnostics-tillägg för Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) läggs till i den virtuella datorn, vilket gör det möjligt att samla in ytterligare data på disk, CPU och minne.

    Med diagnostikdata som samlas in kan konfigurera du automatisk skalning för dina virtuella datorer. Du kan också konfigurera datorn för att lagra data och ställa in aviseringar så att du vet när prestandan inte är riktigt.

## <a name="alerts"></a>Aviseringar

Du kan skapa [aviseringar](../articles/azure-monitor/platform/alerts-overview.md) baserat på specifika prestandamått. Exempel på de problem som du kan bli aviserad om är när Genomsnittlig CPU-användning överskrider ett visst tröskelvärde eller tillgängligt diskutrymme sjunker under ett visst belopp. Aviseringar kan konfigureras i den [Azure-portalen](../articles/azure-monitor/platform/alerts-classic-portal.md)med hjälp av [Azure PowerShell](../articles/azure-monitor/platform/alerts-classic-portal.md#with-powershell), eller [Azure CLI](../articles/azure-monitor/platform/alerts-classic-portal.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure Service Health

[Azure Service Health](../articles/service-health/service-health-overview.md) ger anpassad vägledning och support när problem med Azure-tjänster påverkar dig och hjälper dig att kommande förbereda för planerat underhåll. Azure Service Health varnar dig och dina team med hjälp av målanpassade, flexibla meddelanden.

## <a name="azure-resource-health"></a>Azure Resource Health

[Azure Resource health](../articles/service-health/resource-health-overview.md) hjälper dig att diagnostisera och få support när ett problem med Azure påverkar dina resurser. Det informerar dig om det aktuella och tidigare hälsotillståndet för dina resurser och hjälper dig att åtgärda problem. Resource Health ger teknisk support när du behöver hjälp med problem med Azure-tjänster.

## <a name="logs"></a>Logs

Den [Azure-aktivitetsloggen](../articles/azure-monitor/platform/activity-logs-overview.md) är en prenumerationslogg som ger insikt i händelser på prenumerationsnivå som har inträffat i Azure. Loggen innehåller en mängd data från Azure Resource Manager driftdata uppdateringar på Service Health-händelser. Du kan klicka på aktivitetsloggen i Azure portal för att visa loggen för den virtuella datorn.

Några av de saker som du kan göra med aktivitetsloggen är:

- Skapa en [aviseringen i en händelse i aktivitetsloggen](../articles/azure-monitor/platform/activity-logs-overview.md).
- [Stream till en Händelsehubb](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) för inmatning av en tjänst från tredje part eller anpassade analyslösning till exempel Power BI.
- Analysera dem i Power BI med hjälp av den [Power BI-Innehållspaketet](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Spara den till ett lagringskonto](../articles/azure-monitor/platform/archive-activity-log.md) för arkivering eller manuell granskning. Du kan ange kvarhållningstid (i dagar) som använder Log-profil.

Du kan också komma åt aktivitetsloggdata med [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), [Azure CLI](https://docs.microsoft.com/cli/azure/monitor), eller [övervakaren REST API: er](https://docs.microsoft.com/rest/api/monitor/).

[Azure diagnostikloggar](../articles/azure-monitor/platform/diagnostic-logs-overview.md) är loggar som genereras av den virtuella datorn och som tillhandahåller omfattande, frekventa data om driften. Diagnostikloggar skiljer sig från aktivitetsloggen genom att tillhandahålla information om åtgärder som utförts på den virtuella datorn.

Några av de saker du kan göra med diagnostikloggar är:

- [Spara dem till ett lagringskonto](../articles/azure-monitor/platform/archive-diagnostic-logs.md) för granskning eller manuell granskning. Du kan ange kvarhållningstid (i dagar) med hjälp av Resursdiagnostikinställningar.
- [Stream dem till Event Hubs](../articles/azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) ska matas in av en tjänst från tredje part eller anpassade analyslösning till exempel Power BI.
- Analysera dem med [OMS Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Avancerad övervakning

- [Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/) ger funktioner för övervakning, varningar, och reparationen för molnet och lokala resurser. Du kan installera ett tillägg på en [Linux VM](../articles/virtual-machines/linux/extensions-oms.md) eller en [Windows VM](../articles/virtual-machines/windows/extensions-oms.md) som OMS-agenten installeras och registreras den virtuella datorn i en befintlig OMS-arbetsyta.

- [Log Analytics](../articles/log-analytics/log-analytics-overview.md) är en tjänst i OMS som övervakar dina molnbaserade och lokala miljöer för att bibehålla tillgänglighet och prestanda. Den samlar in data som genereras av resurser i dina miljöer i molnet och lokalt och från andra övervakningsverktyg för att tillhandahålla analyser över flera källor.

    För Windows och Linux-datorer är den rekommenderade metoden för att samla in loggar och mått genom att installera Log Analytics-agenten. Det enklaste sättet att installera Log Analytics-agenten på en virtuell dator är via den [Log Analytics VM-tillägget](../articles/log-analytics/log-analytics-azure-vm-extension.md). Med hjälp av tillägget förenklas installationen och agenten konfigureras automatiskt att skicka data till den Log Analytics-arbetsyta som du anger. Agenten uppgraderas också automatiskt så att du alltid har de senaste funktionerna och korrigeringarna.

- [Network Watcher](../articles/network-watcher/network-watcher-monitoring-overview.md) kan du övervaka din virtuella dator och dess kopplade resurser som är relaterade till det nätverk som de finns i. Du kan installera tillägget Network Watcher-Agent på en [Linux VM](../articles/virtual-machines/linux/extensions-nwa.md) eller en [Windows VM](../articles/virtual-machines/windows/extensions-nwa.md).

## <a name="next-steps"></a>Nästa steg
- Gå igenom stegen i [övervaka en Windows-dator med Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) eller [övervaka en Linux-dator med Azure CLI](../articles/virtual-machines/linux/tutorial-monitoring.md).
- Läs mer om bästa metoder [övervakning och diagnostik](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
