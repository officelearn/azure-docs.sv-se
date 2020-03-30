---
title: Datakällor i Azure Monitor | Microsoft-dokument
description: Beskriver tillgängliga data för att övervaka hälsotillståndet och prestanda för dina Azure-resurser och de program som körs på dem.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/19/2019
ms.openlocfilehash: d1c0652844556b545cf0617032d21b80dd67d198
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479849"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Källor till övervakningsdata för Azure Monitor
Azure Monitor baseras på en [gemensam övervakningsdataplattform](data-platform.md) som innehåller [loggar](data-platform-logs.md) och [mått](data-platform-metrics.md). Genom att samla in data i den här plattformen kan data från flera resurser analyseras tillsammans med hjälp av en gemensam uppsättning verktyg i Azure Monitor. Övervakningsdata kan också skickas till andra platser för att stödja vissa scenarier, och vissa resurser kan skriva till andra platser innan de kan samlas in i loggar eller mått.

I den här artikeln beskrivs de olika källorna till övervakningsdata som samlas in av Azure Monitor utöver övervakningsdata som skapats av Azure-resurser. Länkar tillhandahålls till detaljerad information om konfiguration som krävs för att samla in dessa data till olika platser.

## <a name="application-tiers"></a>Programnivåer

Källor till övervakningsdata från Azure-program kan ordnas i nivåer, de högsta nivåerna är själva ditt program och de lägre nivåerna är komponenter i Azure-plattformen. Metoden för att komma åt data från varje nivå varierar. Programnivåerna sammanfattas i tabellen nedan och källorna till övervakningsdata på varje nivå visas i följande avsnitt. Se [Övervaka dataplatser i Azure](data-locations.md) för en beskrivning av varje dataplats och hur du kan komma åt dess data.


![Övervakningsnivåer](../media/overview/overview.png)


### <a name="azure"></a>Azure
I följande tabell beskrivs kortfattat de programnivåer som är specifika för Azure. Följa länken för mer information om var och en i avsnitten nedan.

| Nivå | Beskrivning | Insamlingsmetod |
|:---|:---|:---|
| [Azure-klient](#azure-tenant) | Information om statusen för Azure-tjänster på klientorganisationsnivå, till exempel Azure Active Directory. | Visa AAD-data i portalen eller konfigurera insamling till Azure Monitor med hjälp av en klientdiagnostikinställning. |
| [Azure-prenumeration](#azure-subscription) | Data som är relaterade till hälso- och hantering av resursövergripande tjänster i din Azure-prenumeration, till exempel Resource Manager och Service Health. | Visa i portalen eller konfigurera insamling till Azure Monitor med hjälp av en loggprofil. |
| [Azure-resurser](#azure-resources) |  Data om åtgärden och prestanda för varje Azure-resurs. | Mått som samlas in automatiskt, visa i Metrics Explorer.<br>Konfigurera diagnostikinställningar för att samla in loggar i Azure Monitor.<br>Övervakningslösningar och insikter tillgängliga för mer detaljerad övervakning för specifika resurstyper. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, andra moln eller lokalt 
I följande tabell beskrivs kortfattat de programnivåer som kan finnas i Azure, ett annat moln eller lokalt. Följa länken för mer information om var och en i avsnitten nedan.

| Nivå | Beskrivning | Insamlingsmetod |
|:---|:---|:---|
| [Operativsystem (gäst)](#operating-system-guest) | Data om operativsystemet på beräkningsresurser. | Installera Log Analytics-agent för att samla in klientdatakällor i Azure Monitor och Dependency Agent för att samla in beroenden som stöder Azure Monitor för virtuella datorer.<br>För virtuella Azure-datorer installerar du Azure Diagnostic Extension för att samla in loggar och mått i Azure Monitor. |
| [Programkod](#application-code) | Data om prestanda och funktionalitet för det faktiska programmet och koden, inklusive prestandaspårningar, programloggar och användartelemetri. | Instrumentera din kod för att samla in data i Application Insights. |
| [Anpassade källor](#custom-sources) | Data från externa tjänster eller andra komponenter eller enheter. | Samla in logg- eller måttdata i Azure Monitor från valfri REST-klient. |

## <a name="azure-tenant"></a>Azure-klientorganisation
Telemetri som är relaterad till din Azure-klient samlas in från klientomfattande tjänster som Azure Active Directory.

![Azure-klientsamling](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Granskningsloggar för Azure Active Directory
[Azure Active Directory-rapportering](../../active-directory/reports-monitoring/overview-reports.md) innehåller historiken för inloggningsaktivitet och granskningsspårning av ändringar som gjorts i en viss klientorganisation. 

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure Monitor-loggar | Konfigurera Azure AD-loggar som ska samlas in i Azure Monitor för att analysera dem med andra övervakningsdata. | [Integrera Azure AD-loggar med Azure Monitor-loggar (förhandsversion)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Exportera Azure AD-loggar till Azure Storage för arkivering. | [Självstudie: Arkivera Azure AD-loggar till ett Azure-lagringskonto (förhandsversion)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Händelsehubb | Strömma Azure AD-loggar till andra platser med hjälp av eventhubbar. | [Självstudiekurs: Strömma Azure Active Directory-loggar till en Azure-händelsehubb (förhandsversion)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Azure-prenumeration
Telemetri som är relaterad till hälso- och driften av din Azure-prenumeration.

![Azure-prenumeration](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Azure-aktivitetslogg 
[Azure-aktivitetsloggen](platform-logs-overview.md) innehåller tjänsthälsojournaler tillsammans med poster om eventuella konfigurationsändringar som gjorts i resurserna i din Azure-prenumeration. Aktivitetsloggen är tillgänglig för alla Azure-resurser och representerar deras _externa_ vy.

| Mål | Beskrivning | Referens |
|:---|:---|
| Aktivitetslogg | Aktivitetsloggen samlas in i ett eget datalager som du kan visa från Azure Monitor-menyn eller använda för att skapa aktivitetsloggaviseringar. | [Fråga aktivitetsloggen i Azure-portalen](activity-log-view.md#azure-portal) |
| Azure Monitor-loggar | Konfigurera Azure Monitor-loggar för att samla in aktivitetsloggen för att analysera den med andra övervakningsdata. | [Samla in och analysera Azure-aktivitetsloggar i Log Analytics-arbetsytan i Azure Monitor](activity-log-collect.md) |
| Azure Storage | Exportera aktivitetsloggen till Azure Storage för arkivering. | [Arkiv aktivitetslogg](resource-logs-collect-storage.md)  |
| Händelsehubbar | Strömma aktivitetsloggen till andra platser med hjälp av eventhubbar | [Strömma aktivitetslogg till eventhubben](resource-logs-stream-event-hubs.md). |

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../service-health/service-health-overview.md) innehåller information om hälsotillståndet för Azure-tjänsterna i din prenumeration som ditt program och dina resurser förlitar sig på.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Aktivitetslogg<br>Azure Monitor-loggar | Tjänsthälsojournaler lagras i Azure Activity-loggen, så att du kan visa dem i Azure-portalen eller utföra andra aktiviteter som du kan utföra med aktivitetsloggen. | [Visa aviseringar om hälsotillståndet för tjänsten med hjälp av Azure Portal](service-notifications.md) |


## <a name="azure-resources"></a>Azure-resurser
Mått och resursloggar ger information om den _interna_ driften av Azure-resurser. Dessa är tillgängliga för de flesta Azure-tjänster och övervakningslösningar och insikter samlar in ytterligare data för vissa tjänster.

![Azure-resurssamling](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>Plattformsmått 
De flesta Azure-tjänster skickar [plattformsmått](data-platform-metrics.md) som återspeglar deras prestanda och åtgärd direkt till måttdatabasen. De specifika [måtten varierar för varje typ av resurs](metrics-supported.md). 

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure-övervakarmått | Plattformsmått skriver till Azure Monitor-måttdatabasen utan konfiguration. Få tillgång till plattformsmått från Metrics Explorer.  | [Komma igång med Azure Metrics Explorer](metrics-getting-started.md)<br>[Mått som stöds med Azure Monitor](metrics-supported.md) |
| Azure Monitor-loggar | Kopiera plattformsmått till loggar för trender och annan analys med hjälp av Log Analytics. | [Azure-diagnostik direkt till Log Analytics](resource-logs-collect-workspace.md) |
| Händelsehubbar | Strömma mått till andra platser med hjälp av eventhubbar. |[Strömma Azure-övervakningsdata till en händelsehubb för förbrukning av ett externt verktyg](stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Resursloggar
[Resursloggar](platform-logs-overview.md) ger insikter om den _interna_ driften av en Azure-resurs.  Resursloggar skapas automatiskt, men du måste skapa en diagnostikinställning för att ange ett mål som de ska samlas in för varje resurs.

Konfigurationskraven och innehållet i resursloggar varierar beroende på resurstyp och alla tjänster skapar dem ännu inte. Mer information om varje tjänst och länkar till detaljerade konfigurationsprocedurer finns [i Tjänster, scheman och kategorier för Azure-resursloggar](diagnostic-logs-schema.md) som stöds. Om tjänsten inte finns med i den här artikeln skapar den tjänsten för närvarande inte resursloggar.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure Monitor-loggar | Skicka resursloggar till Azure Monitor Logs för analys med andra insamlade loggdata. | [Samla in Azure-resursloggar i Log Analytics-arbetsytan i Azure Monitor](resource-logs-collect-storage.md) |
| Lagring | Skicka resursloggar till Azure Storage för arkivering. | [Arkivera Azure-resursloggar](resource-logs-collect-workspace.md) |
| Händelsehubbar | Strömma resursloggar till andra platser med hjälp av eventhubbar. |[Strömma Azure-resursloggar till en händelsehubb](resource-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>Operativsystem (gäst)
Beräkningsresurser i Azure, i andra moln och lokala har ett gästoperativsystem att övervaka. Med installationen av en eller flera agenter kan du samla in telemetri från gästen till Azure Monitor för att analysera den med samma övervakningsverktyg som Azure-tjänsterna själva.

![Azure-beräkningsresurssamling](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Azure Diagnostic-tillägg
Genom att aktivera Azure Diagnostics-tillägget för virtuella Azure-datorer kan du samla in loggar och mått från gästoperativsystemet för Azure-beräkningsresurser, inklusive Azure Cloud Service (klassiska) webb- och arbetsroller, virtuella datorer, virtuell dator skala uppsättningar och Service Fabric.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Lagring | Azure Diagnostics-tillägget skriver alltid till ett Azure Storage-konto. | [Installera och konfigurera Windows Azure diagnostics-tillägg (WAD)](diagnostics-extension-windows-install.md)<br>[Använda Linux-diagnostiktillägget för att övervaka mått och loggar](../../virtual-machines/extensions/diagnostics-linux.md) |
| Azure-övervakarmått | När du konfigurerar diagnostiktillägget för att samla in prestandaräknare skrivs de till Azure Monitor-måttdatabasen. | [Skicka svar om gästoperativsystem till Azure Monitor-måttarkivet med hjälp av en Resource Manager-mall för en virtuell Windows-dator](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Händelsehubbar | Konfigurera diagnostiktillägget för att strömma data till andra platser med hjälp av eventhubbar.  | [Strömmande Azure Diagnostics-data med hjälp av eventhubbar](diagnostics-extension-stream-event-hubs.md)<br>[Använda Linux-diagnostiktillägget för att övervaka mått och loggar](../../virtual-machines/extensions/diagnostics-linux.md) |
| Loggar för programinsikter | Samla in loggar och prestandaräknare från beräkningsresursen som stöder ditt program som ska analyseras med andra programdata. | [Skicka diagnostikdata för molntjänst, virtuell dator eller tjänstinfrastruktur till Application Insights](diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Log Analytics-agent 
Installera Log Analytics-agenten för omfattande övervakning och hantering av dina virtuella Windows- eller Linux-datorer. Den virtuella datorn kan köras i Azure, ett annat moln eller lokalt.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure Monitor-loggar | Log Analytics-agenten ansluter till Azure Monitor antingen direkt eller via System Center Operations Manager och låter dig samla in data från datakällor som du konfigurerar eller från övervakningslösningar som ger ytterligare insikter i program körs på den virtuella datorn. | [Agentdatakällor i Azure Monitor](agent-data-sources.md)<br>[Ansluta Operations Manager till Azure Monitor](om-agents.md) |
| Vm-lagring | Azure Monitor för virtuella datorer använder Log Analytics-agenten för att lagra information om hedtillstånd på en anpassad plats. Mer information finns i nästa avsnitt.  |


### <a name="azure-monitor-for-vms"></a>Azure Monitor för virtuella datorer 
[Azure Monitor för virtuella datorer](../insights/vminsights-overview.md) ger en anpassad övervakningsupplevelse för virtuella datorer som tillhandahåller funktioner utöver grundläggande Azure Monitor-funktioner. Det kräver en beroendeagent på Windows och Linux virtuella datorer som integreras med Log Analytics-agenten för att samla in identifierade data om processer som körs på den virtuella datorn och externa processberoenden.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure Monitor-loggar | Lagrar data om processer och beroenden på agenten. | [Använda Azure Monitor för virtuella datorer (förhandsversion) Mapp för att förstå programkomponenter](../insights/vminsights-maps.md) |



## <a name="application-code"></a>Programkod
Detaljerad programövervakning i Azure Monitor görs med [Application Insights](https://docs.microsoft.com/azure/application-insights/) som samlar in data från program som körs på en mängd olika plattformar. Programmet kan köras i Azure, ett annat moln eller lokalt.

![Insamling av programdata](media/data-sources/applications.png)


### <a name="application-data"></a>Programdata
När du aktiverar Application Insights för ett program genom att installera ett instrumenteringspaket samlar det in mått och loggar som är relaterade till programmets prestanda och funktion. Application Insights lagrar data som samlas in i samma Azure Monitor-dataplattform som används av andra datakällor. Den innehåller omfattande verktyg för att analysera dessa data, men du kan också analysera dem med data från andra källor med hjälp av verktyg som Metrics Explorer och Log Analytics.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure Monitor-loggar | Operativa data om ditt program, inklusive sidvisningar, programbegäranden, undantag och spårningar. | [Analysera loggdata i Azure Monitor](../log-query/log-query-overview.md) |
|                    | Beroendeinformation mellan programkomponenter för att stödja application map och telemetrikorrelation. | [Telemetrikorrelation i Application Insights](../app/correlation.md) <br> [Programkarta](../app/app-map.md) |
|            | Resultat av tillgänglighetstester som testar tillgängligheten och svarstiden för ditt program från olika platser på det offentliga Internet. | [Övervaka tillgänglighet och svarstider på valfri webbplats](../app/monitor-web-app-availability.md) |
| Azure-övervakarmått | Application Insights samlar in mått som beskriver programmets prestanda och funktion utöver anpassade mått som du definierar i ditt program i Azure Monitor-måttdatabasen. | [Loggbaserade och föraggregerade mått i Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[API för Application Insights för anpassade händelser och mått](../app/api-custom-events-metrics.md) |
| Azure Storage | Skicka programdata till Azure Storage för arkivering. | [Exportera telemetri från Application Insights](../app/export-telemetry.md) |
|            | Information om tillgänglighetstester lagras i Azure Storage. Använd Application Insights i Azure-portalen för att hämta för lokal analys. Resultat av tillgänglighetstester lagras i Azure Monitor Logs. | [Övervaka tillgänglighet och svarstider på valfri webbplats](../app/monitor-web-app-availability.md) |
|            | Profilerspårningsdata lagras i Azure Storage. Använd Application Insights i Azure-portalen för att hämta för lokal analys.  | [Profilproduktionsprogram i Azure med Application Insights](../app/profiler-overview.md) 
|            | Felsökning av ögonblicksbilddata som samlas in för en delmängd av undantag lagras i Azure Storage. Använd Application Insights i Azure-portalen för att hämta för lokal analys.  | [Så här fungerar ögonblicksbilder](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Övervakningslösningar och insikter
[Övervakningslösningar](../insights/solutions.md) och [insikter](../insights/insights-overview.md) samlar in data för att ge ytterligare insikter om driften av en viss tjänst eller ett visst program. De kan hantera resurser på olika programnivåer och till och med flera nivåer.

### <a name="monitoring-solutions"></a>Övervakningslösningar

| Mål | Beskrivning | Referens
|:---|:---|:---|
| Azure Monitor-loggar | Övervakningslösningar samlar in data i Azure Monitor-loggar där de kan analyseras med frågespråket eller [vyerna](view-designer.md) som vanligtvis ingår i lösningen. | [Information om datainsamling för övervakningslösningar i Azure](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>Azure Monitor för containrar
[Azure Monitor för behållare](../insights/container-insights-overview.md) ger en anpassad övervakningsupplevelse för [Azure Kubernetes Service (AKS)](/azure/aks/). Den samlar in ytterligare data om dessa resurser som beskrivs i följande tabell.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure Monitor-loggar | Lagrar övervakningsdata för AKS inklusive lager, loggar och händelser. Måttdata lagras också i Loggar för att utnyttja dess analysfunktioner i portalen. | [Förstå prestandan för AKS-kluster med Azure Monitor för containrar](../insights/container-insights-analyze.md) |
| Azure-övervakarmått | Måttdata lagras i måttdatabasen för att driva visualisering och aviseringar. | [Visa behållarmått i statistikutforskaren](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | Ger direkt åtkomst till dina AKS-behållarloggar (Azure Kubernetes Service) (stdout/stderror), händelser och pod-mått i portalen. | [Så här visar du Kubernetes loggar, händelser och pod-mått i realtid](../insights/container-insights-livedata-overview.md) |

### <a name="azure-monitor-for-vms"></a>Azure Monitor för virtuella datorer
[Azure Monitor för virtuella datorer](../insights/vminsights-overview.md) ger en anpassad upplevelse för övervakning av virtuella datorer. En beskrivning av de data som samlas in av Azure Monitor för virtuella datorer ingår i avsnittet [Operativsystem (gäst)](#operating-system-guest) ovan.

## <a name="custom-sources"></a>Anpassade källor
Förutom standardnivåerna för ett program kan du behöva övervaka andra resurser som har telemetri som inte kan samlas in med andra datakällor. För dessa resurser skriver du dessa data till antingen mått eller loggar med hjälp av ett Azure Monitor API.

![Anpassad samling](media/data-sources/custom.png)

| Mål | Metod | Beskrivning | Referens |
|:---|:---|:---|:---|
| Azure Monitor-loggar | API för datainsamling | Samla in loggdata från alla REST-klienter och lagra i Log Analytics-arbetsytan. | [Skicka loggdata till Azure Monitor med HTTP Data Collector API (offentlig förhandsversion)](data-collector-api.md) |
| Azure-övervakarmått | API för anpassade mått | Samla in måttdata från alla REST-klienter och lagra i Azure Monitor-måttdatabasen. | [Skicka anpassade mått för en Azure-resurs till Azure Monitor-måttarkivet med hjälp av ett REST API](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Övriga tjänster
Andra tjänster i Azure skriver data till Azure Monitor-dataplattformen. På så sätt kan du analysera data som samlas in av dessa tjänster med data som samlas in av Azure Monitor och utnyttja samma analys- och visualiseringsverktyg.

| Tjänst | Mål | Beskrivning | Referens |
|:---|:---|:---|:---|
| [Azure Security Center](/azure/security-center/) | Azure Monitor-loggar | Azure Security Center lagrar säkerhetsdata som samlas in på en Log Analytics-arbetsyta som gör att den kan analyseras med andra loggdata som samlas in av Azure Monitor.  | [Datainsamling i Azure Security Center](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](/azure/sentinel/) | Azure Monitor-loggar | Azure Sentinel lagrar data som samlas in från olika datakällor i en Log Analytics-arbetsyta som gör att den kan analyseras med andra loggdata som samlas in av Azure Monitor.  | [Ansluta till datakällor](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>Nästa steg

- Läs mer om de [typer av övervakningsdata som samlas in av Azure Monitor](data-platform.md) och hur du visar och analyserar dessa data.
- Lista de [olika platser där Azure-resurser lagrar data](data-locations.md) och hur du kan komma åt dem. 
