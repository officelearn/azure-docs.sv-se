---
title: Övervaka virtuella Azure-datorer med Azure Monitor
description: Beskriver hur du samlar in och analyserar övervakningsdata från virtuella datorer i Azure med Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 2cb53d0c88d8c29da2bd8bf52d6536555d56c76e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283947"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Övervaka virtuella Azure-datorer med Azure Monitor
I den här artikeln beskrivs hur du använder Azure Monitor för att samla in och analysera övervakningsdata från virtuella Azure-datorer för att upprätthålla deras hälsa. Virtuella datorer kan övervakas för tillgänglighet och prestanda med Azure Monitor som alla [andra Azure-resurser,](monitor-azure-resource.md)men de är unika från andra resurser eftersom du också måste övervaka gästoperativsystemet och gästsystemet och arbetsbelastningarna som körs i den. 

> [!NOTE]
> Den här artikeln innehåller en fullständig översikt över begreppen och alternativen för övervakning av virtuella datorer i Azure Monitor. Information om hur du snabbt börjar övervaka dina virtuella datorer utan att fokusera på de underliggande begreppen finns i [Snabbstart: Övervaka en virtuell Azure-dator med Azure Monitor](../learn/quick-monitor-azure-vm.md).


## <a name="differences-from-other-azure-resources"></a>Skillnader från andra Azure-resurser
[Övervakning av Azure-resurser med Azure Monitor](monitor-azure-resource.md) beskriver övervakningsdata som genereras av Azure-resurser och hur du kan använda funktionerna i Azure Monitor för att analysera och avisera om dessa data. Du kan samla in och agera på samma övervakningsdata från virtuella Azure-datorer med följande skillnader:

- [Plattformsmått](../platform/data-platform-metrics.md) samlas in automatiskt för virtuella datorer men endast för den [virtuella datorns värd](#monitoring-data). Du behöver en agent för att samla in prestandadata från gästoperativsystemet. 
- Virtuella datorer genererar inte [resursloggar](../platform/platform-logs-overview.md) som ger insikt i åtgärder som utfördes inom en Azure-resurs. Du använder en agent för att samla in loggdata från gästoperativsystemet.
- Du kan skapa [diagnostikinställningar](../platform/diagnostic-settings.md) för en virtuell dator för att skicka plattformsmått till andra mål, till exempel lagrings- och händelsehubbar, men du kan inte konfigurera dessa diagnostikinställningar i Azure-portalen. 

## <a name="monitoring-data"></a>Övervaka data
Virtuella datorer i Azure i Azure genererar [loggar](../platform/data-platform-logs.md) och [mått](../platform/data-platform-metrics.md) som visas i följande diagram.

![Översikt](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>Värd för virtuell dator
Virtuella datorer i Azure genererar följande data för den virtuella datorn som är värd för samma som andra Azure-resurser som beskrivs i [Övervakningsdata](monitor-azure-resource.md#monitoring-data).

- [Plattformsmått](../platform/data-platform-metrics.md) - Numeriska värden som samlas in automatiskt med jämna mellanrum och beskriver någon aspekt av en resurs vid en viss tidpunkt. Plattformsmått samlas in för den virtuella datorns värd, men du behöver diagnostiktillägget för att samla in mått för gästoperativsystemet.
- [Aktivitetslogg](../platform/platform-logs-overview.md) - Ger insikt i åtgärderna på varje Azure-resurs i prenumerationen från utsidan (hanteringsplanet). För en virtuell dator innehåller detta sådan information som när den startades och eventuella konfigurationsändringar.


### <a name="guest-operating-system"></a>Gästoperativsystem
För att samla in data från gästoperativsystemet för en virtuell dator behöver du en agent som körs lokalt på varje virtuell dator och skickar data till Azure Monitor. Flera agenter är tillgängliga för Azure Monitor där var och en samlar in olika data och skriver data till olika platser. Få en detaljerad jämförelse av de olika agenterna på [Översikt över Azure Monitor-agenter](../platform/agents-overview.md). 

- [Log Analytics-agent](../platform/agents-overview.md#log-analytics-agent) - Tillgänglig för virtuella datorer i Azure, andra molnmiljöer och lokalt. Samlar in data till Azure Monitor Logs. Stöder Azure Monitor för virtuella datorer och övervakningslösningar. Det här är samma agent som används för System Center Operations Manager.
- [Dependency Agent](../platform/agents-overview.md#dependency-agent) - Samlar in data om de processer som körs på den virtuella datorn och deras beroenden. Förlitar sig på Log Analytics-agenten för att överföra data till Azure och stöder Azure Monitor för virtuella datorer, tjänstöversikt och Wire Data 2.0-lösningar.
- [Azure Diagnostic-tillägg](../platform/agents-overview.md#azure-diagnostics-extension) - Endast tillgängligt för virtuella Azure Monitor-datorer. Kan samla in data till flera platser men används främst för att samla in gästresultatdata i Azure Monitor Metrics för virtuella Windows-datorer.
- [Telegraf agent](../platform/collect-custom-metrics-linux-telegraf.md) - Samla in prestandadata från Linux virtuella datorer i Azure Monitor Metrics.


## <a name="configuration-requirements"></a>Konfigurationskrav
Om du vill aktivera alla funktioner i Azure Monitor för övervakning av en virtuell dator måste du samla in övervakningsdata från den virtuella datorns värd- och gästoperativsystem till både [Azure Monitor Metrics](../platform/data-platform-logs.md) och [Azure Monitor Logs](../platform/data-platform-logs.md). I följande tabell visas den konfiguration som måste utföras för att aktivera den här samlingen. Du kan välja att inte utföra alla dessa steg beroende på dina specifika krav.

| Konfigurationssteg | Slutförda åtgärder | Funktioner aktiverade |
|:---|:---|:---|
| Ingen konfiguration | - Värdplattformsmått som samlats in till mått.<br>- Aktivitetsloggen insamlad. | - Metrics explorer för värd.<br>- Måttvarningar för värden.<br>- Aktivitetsloggvarningar. |
| [Aktivera Azure Monitor för virtuella datorer](#enable-azure-monitor-for-vms) | - Logga Analytics-agenten installerad.<br>- Beroendeagent installerad.<br>- Gästresultatdata som samlats in till loggar.<br>- Process- och beroendeinformation som samlats in till loggar. | - Resultatdiagram och arbetsböcker för gästspelsdata.<br>- Logga frågor för gästprestandadata.<br>- Logga varningar för gästresultatdata.<br>- Beroendekarta. |
| [Installera diagnostikförlängningen och telegrafagenten](#enable-diagnostics-extension-and-telegraf-agent) | - Gästresultatdata som samlats in till Metrics. | - Metrics explorer för gäst.<br>- Måttvarningar för gäst.  |
| [Konfigurera log analytics-arbetsyta](#configure-log-analytics-workspace) | - Evenemang som samlats in från gästen. | - Logga frågor för gästevenemang.<br>- Logga varningar för gästevenemang. |
| [Skapa diagnostikinställning för virtuell dator](#collect-platform-metrics-and-activity-log) | - Plattformsmått som samlats in till loggar.<br>- Aktivitetslogg som samlats in till loggar. | - Loq-frågor om värdmått.<br>- Logga aviseringar för värdmått.<br>- Logga frågor för aktivitetsloggen.

Var och en av dessa konfigurationssteg beskrivs i följande avsnitt.

### <a name="enable-azure-monitor-for-vms"></a>Aktivera Azure Monitor för virtuella datorer
[Azure Monitor för virtuella datorer](vminsights-overview.md) är en [inblick](insights-overview.md) i Azure Monitor som är det primära verktyget för övervakning av virtuella datorer i Azure Monitor. Det ger följande mervärde jämfört med standardfunktioner i Azure Monitor.

- Förenklad introduktion av Log Analytics-agent och beroendeagent för att möjliggöra övervakning av ett gästoperativsystem och arbetsbelastningar för virtuella datorer. 
- Fördefinierade trendresultatdiagram och arbetsböcker som gör att du kan analysera grundläggande prestandamått från den virtuella datorns gästoperativsystem.
- Beroendekarta som visar processer som körs på varje virtuell dator och de sammankopplade komponenterna med andra datorer och externa källor.

![Azure Monitor för virtuella datorer](media/monitor-vm-azure/vminsights-01.png)

![Azure Monitor för virtuella datorer](media/monitor-vm-azure/vminsights-02.png)


Aktivera Azure Monitor för virtuella datorer från alternativet **Insikter** på menyn för den virtuella datorn i Azure-portalen. Mer information och andra konfigurationsmetoder finns i Översikt över Aktivera [Azure Monitor för virtuella datorer.](vminsights-enable-overview.md)

![Aktivera Azure Monitor för virtuella datorer](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Konfigurera log analytics-arbetsyta
Log Analytics-agenten som används av Azure Monitor för virtuella datorer skickar data till en [Log Analytics-arbetsyta](../platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured). Du kan aktivera insamling av ytterligare prestandadata, händelser och andra övervakningsdata från agenten genom att konfigurera log analytics-arbetsytan. Det behöver bara konfigureras en gång, eftersom alla agenter som ansluter till arbetsytan automatiskt hämtar konfigurationen och omedelbart börjar samla in definierade data. 

Du kan komma åt konfigurationen för arbetsytan direkt från Azure Monitor för virtuella datorer genom att välja **Arbetsytekonfiguration** från **Kom igång**. Klicka på arbetsytans namn för att öppna menyn.

![Konfiguration av arbetsyta](media/monitor-vm-azure/workspace-configuration.png)

Välj **Avancerade inställningar på** arbetsytans meny och sedan **Data** för att konfigurera datakällor. För Windows-agenter väljer du **Windows-händelseloggar** och lägger till vanliga händelseloggar som *System* och *Program*. För Linux-agenter väljer du **Syslog** och lägger till gemensamma faciliteter som *kern* och *demon*. Se [Agent-datakällor i Azure Monitor](../platform/agent-data-sources.md) för en lista över tillgängliga datakällor och information om hur du konfigurerar dem. 

![Konfigurera händelser](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> Du kan konfigurera prestandaräknare som ska samlas in från arbetsytans konfiguration, men detta kan vara redundant med prestandaräknarna som samlas in av Azure Monitor för virtuella datorer. Azure Monitor för virtuella datorer samlar in den vanligaste uppsättningen räknare med en frekvens på en gång per minut. Konfigurera endast prestandaräknare som ska samlas in av arbetsytan om du vill samla in räknare som inte redan samlas in av Azure Monitor för virtuella datorer eller om du har befintliga frågor med hjälp av prestandadata.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>Aktivera diagnostiktillägg och Telegraf-agent
Azure Monitor för virtuella datorer baseras på Log Analytics-agenten som samlar in data i en Log Analytics-arbetsyta. Detta stöder [flera funktioner i Azure Monitor,](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) till exempel [loggfrågor,](../log-query/log-query-overview.md) [loggaviseringar](../platform/alerts-log.md)och [arbetsböcker](../platform/workbooks-overview.md). [Diagnostiktillägget](../platform/diagnostics-extension-overview.md) samlar in prestandadata från gästoperativsystemet för virtuella Windows-datorer till Azure Storage och skickar eventuellt prestandadata till [Azure Monitor Metrics](../platform/data-platform-metrics.md). För virtuella Linux-datorer krävs [Telegraf-agenten](../platform/collect-custom-metrics-linux-telegraf.md) för att skicka data till Azure Metrics.  Detta möjliggör andra funktioner i Azure Monitor, till exempel [statistikutforskaren](../platform/metrics-getting-started.md) och [måttaviseringar](../platform/alerts-metric.md). Du kan också konfigurera diagnostiktillägget för att skicka händelser och prestandadata utanför Azure Monitor med Azure Event Hubs.

Installera diagnostiktillägget för en enda virtuell Windows-dator i Azure-portalen från **inställningsalternativet Diagnostik** på VM-menyn. Välj alternativet för att aktivera **Azure Monitor** på fliken **Sänkor.** Information om hur du aktiverar tillägget från en mall eller kommandorad för flera virtuella datorer finns i [Installation och konfiguration](../platform/diagnostics-extension-overview.md#installation-and-configuration). Till skillnad från Log Analytics-agenten definieras de data som ska samlas in i konfigurationen för tillägget på varje virtuell dator.

![Diagnostikinställning](media/monitor-vm-azure/diagnostic-setting.png)

Se [Installera och konfigurera Telegraf](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) för mer information om hur du konfigurerar Telegraf-agenterna på virtuella Linux-datorer. Menyalternativet **Diagnostikinställning** är tillgängligt för Linux, men du kan bara skicka data till Azure-lagring.

### <a name="collect-platform-metrics-and-activity-log"></a>Samla in plattformsmått och aktivitetslogg
Du kan visa plattformsmått och aktivitetslogg som samlas in för varje värd för virtuella datorer i Azure-portalen. Samla in dessa data i samma Log Analytics-arbetsyta som Azure Monitor för virtuella datorer för att analysera dem med andra övervakningsdata som samlats in för den virtuella datorn. Den här samlingen är konfigurerad med en [diagnostikinställning](../platform/diagnostic-settings.md). Samla in aktivitetsloggen med en [diagnostikinställning för prenumerationen](../platform/diagnostic-settings.md#create-diagnostic-settings-in-azure-portal).

Samla in plattformsmått med en diagnostikinställning för den virtuella datorn. Till skillnad från andra Azure-resurser kan du inte skapa en diagnostikinställning för en virtuell dator i Azure-portalen, men måste använda [en annan metod](../platform/diagnostic-settings.md#create-diagnostic-settings-using-powershell). Följande exempel visar hur du samlar in mått för en virtuell dator med både PowerShell och CLI.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```CLI
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace

```

## <a name="monitoring-in-the-azure-portal"></a>Övervakning i Azure-portalen 
När du har konfigurerat insamling av övervakningsdata för en virtuell dator har du flera alternativ för att komma åt den i Azure-portalen:

- Använd **Azure Monitor-menyn** för att komma åt data från alla övervakade resurser. 
- Använd Azure Monitor för virtuella datorer för övervakning av uppsättningar av virtuella datorer i stor skala.
- Analysera data för en enda virtuell dator från menyn i Azure-portalen. I tabellen nedan visas olika alternativ för övervakning av menyn virtuella datorer.

![Övervakning i Azure-portalen](media/monitor-vm-azure/monitor-menu.png)

| Menyalternativ | Beskrivning |
|:---|:---|
| Översikt | Visar [plattformsmått](../platform/data-platform-metrics.md) för den virtuella datorns värd. Klicka på ett diagram för att arbeta med dessa data i [mått explorer](../platform/metrics-getting-started.md). |
| Aktivitetslogg | [Aktivitetsloggposter](../platform/activity-log-view.md) filtrerade för den aktuella virtuella datorn. |
| Insikter | Öppnar [Azure Monitor för virtuella datorer](../insights/vminsights-overview.md) med kartan för den aktuella virtuella datorn vald. |
| Aviseringar | Visar [aviseringar](../platform/alerts-overview.md) för den aktuella virtuella datorn.  |
| Mått | Öppna [statistikutforskaren](../platform/metrics-getting-started.md) med scopet inställt på den aktuella virtuella datorn. |
| Diagnostikinställningar | Aktivera och konfigurera [diagnostiktillägg](../platform/diagnostics-extension-overview.md) för den aktuella virtuella datorn. |
| Advisor-rekommendationer | Rekommendationer för den aktuella virtuella datorn från [Azure Advisor](/azure/advisor/). |
| Loggar | Öppna [Log Analytics](../log-query/log-query-overview.md#what-is-log-analytics) med [scopet](../log-query/scope.md) inställt på den aktuella virtuella datorn. |
| Anslutningsmonitor | Öppna [Network Watcher Connection Monitor](../../network-watcher/connection-monitor-preview.md) för att övervaka anslutningar mellan den aktuella virtuella datorn och andra virtuella datorer. |


## <a name="analyzing-metric-data"></a>Analysera måttdata
Du kan analysera mått för virtuella datorer med hjälp av statistikutforskaren genom att öppna **mått** från den virtuella datorns meny. Mer information om hur du använder det här verktyget finns [i Komma igång med Azure Metrics Explorer.](../platform/metrics-getting-started.md) 

Det finns två namnområden som används av virtuella datorer för mått:

| Namnområde | Beskrivning |
|:---|:---|
| Virtuell värddator | Värdmått samlas in automatiskt för alla virtuella Azure-datorer. Detaljerad lista över mått på [Microsoft.Compute/virtualMachines](../platform/metrics-supported.md#microsoftcomputevirtualmachines). |
| Gäst i virtuell dator | Gästoperativsystemmått som samlats in från virtuella datorer med diagnostiktillägg installerat och konfigurerat för att skicka till Azure Monitor sink. |

![Mått](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>Analysera loggdata
Virtuella Azure-datorer samlar in följande data till Azure Monitor Logs. 

Azure Monitor för virtuella datorer möjliggör insamling av en förutbestämd uppsättning prestandaräknare som skrivs till tabellen *InsightsMetrics.* Det här är samma tabell som används av [Azure Monitor for Containers](container-insights-overview.md). 

| Datakälla | Krav | Tabeller |
|:---|:---|:---|
| Azure Monitor för virtuella datorer | Aktivera på varje virtuell dator. | InsikterMetri<br>VMBoundPort<br>VMD-dator<br>VMAnslutning<br>VM-bearbetning<br>Mer information [finns i Så här frågar du logger från Azure Monitor för virtuella datorer.](vminsights-log-search.md) |
| Aktivitetslogg | Diagnostikinställning för prenumerationen. | AzureActivity |
| Värdmått | Diagnostikinställning för den virtuella datorn. | AzureMetrics |
| Datakällor från gästoperativsystemet | Aktivera Log Analytics-agent och konfigurera datakällor. | Se dokumentation för varje datakälla. |


> [!NOTE]
> Prestandadata som samlas in av Log Analytics-agenten skriver till *Perf-tabellen* medan Azure Monitor för virtuella datorer samlar in den i tabellen *InsightsMetrics.* Det här är samma data, men tabellerna har en annan struktur. Om du har befintliga frågor baserade på *Perf*måste det skrivas om för att använda *InsightsMetrics*.


## <a name="alerts"></a>Aviseringar
[Aviseringar](../platform/alerts-overview.md) i Azure Monitor meddelar dig proaktivt när viktiga villkor hittas i dina övervakningsdata och eventuellt startar en åtgärd som att starta en Logikapp eller anropa en webhook. Varningsregler definierar den logik som används för att avgöra när en avisering ska skapas. Azure Monitor samlar in data som används av varningsregler, men du måste skapa regler för att definiera aviseringsvillkor i din Azure-prenumeration.

I följande avsnitt beskrivs vilka typer av varningsregler och rekommendationer som ska användas när du ska använda var och en. Den här rekommendationen baseras på funktionerna och kostnaden för aviseringsregeltypen. Mer information om prissättning av aviseringar finns i [Azure Monitor-priser](https://azure.microsoft.com/pricing/details/monitor/).


### <a name="activity-log-alert-rules"></a>Aviseringsregler för aktivitetsloggar
[Aktivitetsloggvarningsregler](../platform/alerts-activity-log.md) avfyras när en transaktion som matchar vissa villkor skapas i aktivitetsloggen. De har ingen kostnad så de bör vara ditt första val om logiken du behöver finns i aktivitetsloggen. 

Målresursen för aktivitetsloggaviseringar kan vara en viss virtuell dator, alla virtuella datorer i en resursgrupp eller alla virtuella datorer i en prenumeration.

Skapa till exempel en avisering om en kritisk virtuell dator stoppas genom att välja *Den virtuella datorn* för avstängning för signalnamnet.

![Avisering om aktivitetslogg](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>Måttaviseringsregler
[Måttvarningsregler](../platform/alerts-metric.md) avfyras när ett måttvärde överskrider ett tröskelvärde. Du kan definiera ett specifikt tröskelvärde eller tillåta Att Azure Monitor dynamiskt bestämmer ett tröskelvärde baserat på historiska data.  Använd måttaviseringar när det är möjligt med måttdata eftersom de kostar mindre och är mer responsiva än loggvarningsregler. De är också tillståndskänsliga vilket innebär att de kommer att lösa sig när måttet sjunker under tröskelvärdet.

Målresursen för aktivitetsloggaviseringar kan vara en viss virtuell dator eller alla virtuella datorer i en resursgrupp.

Om du till exempel vill skapa en avisering när processorn för en virtuell dator överskrider ett visst värde skapar du en måttaviseringsregel med *procent cpu* som signaltyp. Ange antingen ett specifikt tröskelvärde eller tillåt Azure Monitor att ange ett dynamiskt tröskelvärde. 

![Måttavisering](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>Loggaviseringar
[Loggvarningsregler](../platform/alerts-log.md) avfyras när resultatet av en schemalagd loggfråga matchar vissa villkor. Loggfrågavarningar är de dyraste och minst responsiva av varningsreglerna, men de har åtkomst till de mest olika data och kan utföra komplex logik som inte kan utföras av de andra varningsreglerna. 

Målresursen för en loggfråga är en Log Analytics-arbetsyta. Filtrera efter specifika datorer i frågan.

Om du till exempel vill skapa en avisering som kontrollerar om några virtuella datorer i en viss resursgrupp är offline använder du följande fråga som returnerar en post för varje dator som har missat ett pulsslag under de senaste tio minuterna. Använd ett tröskelvärde på 1 som utlöses om minst en dator har ett missat pulsslag.

```kusto
Heartbeat
| where TimeGenerated < ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![Loggavisering](media/monitor-vm-azure/log-alert-01.png)

Om du vill skapa en avisering om ett alltför stort antal misslyckade inloggningar har inträffat på alla virtuella Windows-datorer i prenumerationen använder du följande fråga som returnerar en post för varje misslyckad inloggningshändelse under den senaste timmen. Använd ett tröskelvärde inställt på antalet misslyckade inloggningar som du tillåter. 

```kusto
Event
| where TimeGenerated < ago(1hr)
| where EventID == 4625
```

![Loggavisering](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
System Center Operations Manager (SCOM) ger detaljerad övervakning av arbetsbelastningar på virtuella datorer. Se [molnövervakningsguiden](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/) för en jämförelse av övervakningsplattformar och olika implementeringsstrategier.

Om du har en befintlig SCOM-miljö som du tänker fortsätta använda kan du integrera den med Azure Monitor för att tillhandahålla ytterligare funktioner. Log Analytics-agenten som används av Azure Monitor är samma som används för SCOM så att du har övervakat virtuella datorer skicka data till båda. Du måste fortfarande lägga till agenten i Azure Monitor för virtuella datorer och konfigurera arbetsytan för att samla in ytterligare data enligt ovan, men de virtuella datorerna kan fortsätta att köra sina befintliga hanteringspaket i en SCOM-miljö utan ändringar.

Funktioner för Azure Monitor som utökar en befintlig SCOM-funktioner är följande:

- Använd Log Analytics för att interaktivt analysera dina logg- och prestandadata.
- Använd loggaviseringar för att definiera aviseringar för flera virtuella datorer och med hjälp av långsiktiga trender som inte är möjliga med aviseringar i SCOM.   

Mer information om hur du ansluter din befintliga SCOM-hanteringsgrupp till logganalysarbetsytan finns i [Connect Operations Manager till Azure Monitor.](../platform/om-agents.md)


## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du analyserar data i Azure Monitor-loggar med hjälp av loggfrågor.](../log-query/get-started-queries.md)
* [Lär dig mer om aviseringar med hjälp av mått och loggar i Azure Monitor.](../platform/alerts-overview.md)

