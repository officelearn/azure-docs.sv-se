Du kan dra nytta av många möjligheter att övervaka dina virtuella datorer genom att samla in, visa och analysera diagnostiska och logga data. Att göra enkla [övervakning](../articles/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) på den virtuella datorn kan du använda översiktsskärmen för den virtuella datorn i Azure-portalen. Du kan använda [tillägg](../articles/virtual-machines/windows/extensions-features.md) att konfigurera diagnostik på dina virtuella datorer för att samla in ytterligare mått data. Du kan också använda mer avancerade alternativ för övervakning, t.ex [Programinsikter](../articles/application-insights/app-insights-overview.md) och [logganalys](../articles/log-analytics/log-analytics-overview.md).

## <a name="diagnostics-and-metrics"></a>Diagnostik- och mått 

Du kan konfigurera och övervaka mängden [diagnostikdata](https://docs.microsoft.com/cli/azure/vm/diagnostics) med [mått](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) i Azure-portalen, Azure CLI, Azure PowerShell och program Programming programmeringsgränssnitt (API). Du kan till exempel:

- **Se grundläggande mått för den virtuella datorn.** På skärmen översikt över Azure portal innehåller de grundläggande mått som visas CPU-användning, nätverksanvändning, summan av disk-byte och diskåtgärder per sekund.

- **Aktivera insamling av startdiagnostikinställningar och visa den med hjälp av Azure-portalen.** När en egen avbildning till Azure eller även starta en av avbildningarna plattform, kan det finnas många orsaker till varför en virtuell dator i en icke-startbart läge. Du kan enkelt aktivera startdiagnostikinställningar när du skapar en virtuell dator genom att klicka på **aktiverad** för Startdiagnostikinställningar under avsnittet övervakning av skärmen.

    Eftersom virtuella datorer starta Start diagnostikagenten avbildas Start utdata och lagrar den i Azure-lagring. Dessa data kan användas för att felsöka problem med VM-start. Startdiagnostikinställningar aktiveras inte automatiskt när du skapar en virtuell dator från kommandoradsverktyg. Innan du aktiverar startdiagnostikinställningar behöver ett lagringskonto skapas för att lagra start loggar. Om du aktiverar startdiagnostikinställningar i Azure-portalen, skapas automatiskt ett storage-konto för dig.

    Om du inte aktiverar startdiagnostikinställningar när den virtuella datorn skapades, du kan aktivera alltid den senare med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmbootdiagnostics), eller en [Azure Resource Manager-mall](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Aktivera insamling av diagnostikdata för gäst-OS.** När du skapar en virtuell dator kan välja du på skärmen för att aktivera diagnostik för gäst-OS. När du aktiverar insamling av diagnostikdata, den [IaaSDiagnostics tillägg för Linux](../articles/virtual-machines/linux/diagnostic-extension.md) eller [IaaSDiagnostics tillägget för Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) har lagts till i den virtuella datorn, som gör det möjligt att samla in ytterligare data på disken, CPU och minne.

    Med informationen som samlats in diagnostik kan konfigurera du autoskalning för dina virtuella datorer. Du kan också konfigurera loggar för att lagra data och ställa in aviseringar så att du vet när prestandan inte är riktigt.

## <a name="alerts"></a>Aviseringar

Du kan skapa [aviseringar](../articles/monitoring-and-diagnostics/monitoring-overview-alerts.md) baserat på specifika prestandamått. Exempel på problem som du kan bli aviserad om är när Genomsnittlig CPU-användning överskrider ett visst tröskelvärde eller tillgängligt diskutrymme sjunker under en viss mängd. Aviseringar kan konfigureras i den [Azure-portalen](../articles/monitoring-and-diagnostics/insights-alerts-portal.md)med hjälp av [Azure PowerShell](../articles/monitoring-and-diagnostics/insights-alerts-powershell.md), eller [Azure CLI](../articles/monitoring-and-diagnostics/insights-alerts-command-line-interface.md).

## <a name="azure-service-health"></a>Azure Service Health

[Tjänstens hälsa för Azure](../articles/service-health/service-health-overview.md) ger personlig information och support när problem i Azure-tjänster påverkar dig och hjälper dig att kommande förbereda för planerat underhåll. Tjänstens hälsa för Azure aviseringar du och ditt team använder mål- och flexibel meddelanden.

## <a name="azure-resource-health"></a>Azure Resource Health

[Azure Resource health](../articles/service-health/resource-health-overview.md) hjälper dig att diagnostisera och få support när ett Azure problem påverkar dina resurser. Det informerar dig om det aktuella och tidigare hälsotillståndet för dina resurser och hjälper dig att åtgärda problem. Resource Health ger teknisk support när du behöver hjälp med problem med Azure-tjänster.

## <a name="logs"></a>Logs

Den [Azure-aktivitetsloggen](../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md) är en prenumerationslogg som ger inblick i prenumerationsnivån händelser som har inträffat i Azure. Loggen innehåller en mängd data från Azure Resource Manager användningsdata till uppdateringar på händelser för Hälsotjänst. Du kan klicka på aktivitetsloggen i Azure-portalen för att visa loggen för den virtuella datorn.

Några av de saker du kan göra med aktivitetsloggen är:

- Skapa en [Avisera om en händelse i aktivitetsloggen](../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md).
- [Strömma till en Händelsehubb](../articles/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md) för införandet av en tjänst från tredje part eller anpassade analytics lösning, till exempel PowerBI.
- Analysera i PowerBI med hjälp av den [PowerBI Innehållspaketet](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Spara den till ett lagringskonto](../articles/monitoring-and-diagnostics/monitoring-archive-activity-log.md) för arkivering eller Manuell kontroll. Du kan ange kvarhållningstiden (i dagar) med den loggen profilen.

Du kan också komma åt aktivitet loggdata med hjälp av [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), [Azure CLI](https://docs.microsoft.com/cli/azure/monitor), eller [övervakaren REST API: er](https://docs.microsoft.com/rest/api/monitor/).

[Azure diagnostikloggar](../articles/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) är loggar som sänds av den virtuella datorn som innehåller omfattande, ofta data om dess drift. Diagnostikloggar skiljer sig från aktivitetsloggen genom att tillhandahålla information om åtgärder som utförts i den virtuella datorn.

Några av de saker du kan göra med diagnostik loggar är:

- [Spara dem till ett lagringskonto](../articles/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) för granskning eller Manuell kontroll. Du kan ange kvarhållningstiden (i dagar) med hjälp av diagnostikinställningar för resursen.
- [Strömma dem i Händelsehubbar](../articles/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) för införandet av en tjänst från tredje part eller anpassade analytics lösning, till exempel PowerBI.
- Analysera dem med [OMS logganalys](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Avancerad övervakning

- [Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/) ger funktioner för övervakning, aviseringar, och reparationen i molnet och lokala resurser. Du kan installera ett tillägg på en [Linux VM](../articles/virtual-machines/linux/extensions-oms.md) eller en [Windows VM](../articles/virtual-machines/windows/extensions-oms.md) som installerar OMS-agent och registrerar den virtuella datorn till en befintlig OMS-arbetsyta.

- [Logga Analytics](../articles/log-analytics/log-analytics-overview.md) är en tjänst i OMS som övervakar molnet och lokala miljöer för att upprätthålla sin tillgänglighet och prestanda. Den samlar in data som genereras av resurser i dina miljöer i molnet och lokalt och från andra övervakningsverktyg för att tillhandahålla analyser över flera källor.

    Den rekommenderade metoden för att samla in loggar och mått är genom att installera agenten Log Analytics för Windows och Linux virtuella datorer. Det enklaste sättet att installera logganalys-agenten på en virtuell dator är via den [Log Analytics VM-tillägget](../articles/log-analytics/log-analytics-azure-vm-extension.md). Med hjälp av tillägget förenklar installationen och konfigurerar automatiskt agent ska skicka data till logganalys-arbetsytan som du anger. Agenten uppgraderas också automatiskt, se till att du har de senaste funktionerna och korrigeringarna.

- [Nätverk Watcher](../articles/network-watcher/network-watcher-monitoring-overview.md) kan du övervaka den virtuella datorn och dess associerade resurser som de hör ihop med det nätverk som de finns i. Du kan installera tillägget Network Watcher Agent på en [Linux VM](../articles/virtual-machines/linux/extensions-nwa.md) eller en [Windows VM](../articles/virtual-machines/windows/extensions-nwa.md).

## <a name="next-steps"></a>Nästa steg
- Gå igenom stegen i [övervaka Windows-dator med Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) eller [övervaka en virtuell Linux-dator med Azure CLI](../articles/virtual-machines/linux/tutorial-monitoring.md).
- Mer information om metodtips runt [övervaknings- och diagnostikfunktionerna](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
