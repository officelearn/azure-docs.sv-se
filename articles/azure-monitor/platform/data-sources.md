---
title: Datakällor i Azure Monitor | Microsoft Docs
description: Beskriver data som kan övervaka hälsotillstånd och prestanda för dina Azure-resurser och program som körs på dem.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: bwren
ms.openlocfilehash: b77fb3ab5651147c59b9f0afd22a2d6d0159c90e
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357518"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Källor för övervakningsdata för Azure Monitor
Azure Monitor är baserad på en [vanliga data övervakningsplattform](data-platform.md) som innehåller [loggar](data-platform-logs.md) och [mått](data-platform-metrics.md). Samla in data till den här plattformen kan data från flera resurser som ska analyseras tillsammans med hjälp av en gemensam uppsättning verktyg i Azure Monitor. Övervakning av data kan också skickas till andra platser för vissa scenarier och vissa resurser kan skriva till andra platser innan de kan samlas in loggar eller mätvärden.

Den här artikeln beskrivs de olika källorna för övervakningsdata som samlas in av Azure Monitor förutom övervakningsdata som skapats av Azure-resurser. Länkar till detaljerad information om konfiguration som krävs för att samla in dessa data till olika platser.

## <a name="application-tiers"></a>Programnivåer

Källor för övervakningsdata från Azure-program kan ordnas i nivåer, de högsta nivåerna som själva programmet och de lägre nivåerna som komponenter i Azure-plattformen. Metoden för att komma åt data från varje nivå varierar. Programskikt sammanfattas i tabellen nedan och källor för övervakningsdata i respektive nivå som visas i följande avsnitt. Se [övervakning dataplatserna i Azure](data-locations.md) en beskrivning av varje plats och hur du kommer åt sina data.


![Övervakning nivå](../media/overview/overview.png)


### <a name="azure"></a>Azure
I följande tabell beskrivs kortfattat programskikt som är specifika för Azure. Följa länken för mer detaljerad information om på var och en i avsnitten nedan.

| Nivå | Beskrivning | Metod för insamling |
|:---|:---|:---|
| [Azure Tenant](#azure-tenant) | Information om driften av Azure på klientnivå-tjänster, till exempel Azure Active Directory. | Visa AAD-data i portalen eller konfigurera samlingen till Azure Monitor med hjälp av en diagnostikinställning för klienten. |
| [Azure-prenumeration](#azure-subscription) | Data som rör hälsa och hanteringen av tjänster mellan resurser i Azure-prenumerationen, till exempel Resource Manager och Service Health. | Visa i portalen eller konfigurera samlingen till Azure Monitor med hjälp av en loggprofil för. |
| [Azure-resurser](#azure-resources) |  Information om drift och prestanda för varje Azure-resurs. | Mått samlas in automatiskt, visa i Metrics Explorer.<br>Konfigurera diagnostikinställningar för att samla in loggar i Azure Monitor.<br>Övervakning av lösningar och insikter som är tillgänglig för mer detaljerad övervakning för specifika resurstyper. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, andra molnet eller lokalt 
I följande tabell beskrivs kortfattat programskikt som kan vara i Azure, en annan molnet eller lokalt. Följa länken för mer detaljerad information om på var och en i avsnitten nedan.

| Nivå | Beskrivning | Metod för insamling |
|:---|:---|:---|
| [Operativsystemet (Gäst)](#operating-system-guest) | Information om operativsystemet på beräkningsresurser. | Installera Log Analytics-agenten för att samla in klientdatakällor i Azure Monitor och beroende agenten att samla in beroenden som stöder Azure Monitor för virtuella datorer.<br>Installera Azure-Diagnostiktillägget för att samla in loggar och mått i Azure Monitor för Azure-datorer. |
| [Programkod](#application-code) | Data om prestanda och funktionerna i själva programmet och kod, inklusive prestandaspårningar, programloggar och telemetri för användaren. | Instrumentera din kod för att samla in data till Application Insights. |
| [Anpassade källor](#custom-sources) | Data från externa tjänster eller andra komponenter eller enheter. | Samla in logg-eller mått i Azure Monitor från alla REST-klient. |

## <a name="azure-tenant"></a>Azure-klientorganisation
Telemetri som är relaterade till din Azure-klient som samlas in från klienten som helhet tjänster som Azure Active Directory.

![Azure-klient-samling](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Azure Active Directory med granskningsloggar
[Azure Active Directory-rapportering](../../active-directory/reports-monitoring/overview-reports.md) innehåller historik över inloggning aktivitet och granska spårning av ändringar som gjorts i en viss klient. 

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure Monitor-loggar | Konfigurera Azure AD-loggar som ska samlas in i Azure Monitor för att analysera dem med andra övervakningsdata. | [Integrera Azure AD-loggar med Azure Monitor-loggar (förhandsversion)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Exportera loggar för Azure AD till Azure Storage för arkivering. | [Självstudie: Arkivera Azure AD-loggar till ett Azure storage-konto (förhandsversion)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Händelsehubb | Stream Azure AD med loggar på andra platser med Event Hubs. | [Självstudie: Stream Azure Active Directory-loggar till en Azure event hub (förhandsversion)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Azure-prenumeration
Telemetri som rör hälsa och driften av din Azure-prenumeration.

![Azure-prenumeration](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Azure-aktivitetslogg 
Den [Azure-aktivitetsloggen](activity-logs-overview.md) innehåller tjänsten hälsojournaler tillsammans med poster på några konfigurationsändringar som gjorts till resurserna i din Azure-prenumeration. Aktivitetsloggen är tillgängligt för alla Azure-resurser och visar sina _externa_ vy.

| Mål | Beskrivning | Referens |
|:---|:---|
| Aktivitetslogg | Aktivitetsloggen har samlats in i sitt eget datalager som du kan visa på menyn Azure Monitor eller använda för att skapa aktivitetsloggaviseringar. | [Fråga i aktivitetsloggen i Azure portal](activity-log-view.md#azure-portal) |
| Azure Monitor-loggar | Konfigurera Azure Monitor-loggar för att samla in aktivitetsloggen om du vill analysera den med andra övervakningsdata. | [Samla in och analysera Azure-aktivitetsloggar i Log Analytics-arbetsyta i Azure Monitor](activity-log-collect.md) |
| Azure Storage | Exportera aktivitetsloggen till Azure Storage för arkivering. | [Arkivera aktivitetsloggen](activity-log-export.md#archive-activity-log)  |
| Event Hubs | Stream aktivitetsloggen till andra platser med Event Hubs | [Stream aktivitetsloggen till Event Hub](activity-log-export.md#stream-activity-log-to-event-hub). |

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../service-health/service-health-overview.md) innehåller information om hälsotillståndet för Azure-tjänster i din prenumeration som ditt program och resurser som förlitar sig på.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Aktivitetslogg<br>Azure Monitor-loggar | Tjänstehälsa poster lagras i Azure-aktivitetsloggen så att du kan visa dem i Azure-portalen eller utföra andra aktiviteter som du kan utföra med aktivitetsloggen. | [Visa aviseringar om hälsotillståndet för tjänsten med hjälp av Azure Portal](service-notifications.md) |


## <a name="azure-resources"></a>Azure-resurser
Mått och resurs på diagnostikloggar ger information om den _interna_ driften av Azure-resurser. Dessa är tillgängliga för de flesta Azure-tjänster och övervakning lösningar och insikter kan du samla in ytterligare data för specifika tjänster.

![Azure resurssamling](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>Plattformen mått 
De flesta Azure-tjänster skickar [plattform mått](data-platform-metrics.md) som motsvarar deras prestanda och åtgärd direkt till mått-databasen. Specifikt [mått kan variera för varje typ av resurs](metrics-supported.md). 

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure Monitor-mått | Plattformen mått skriver till Azure Monitor metrics databasen utan konfiguration. Åtkomst-plattformen mått från Metrics Explorer.  | [Komma igång med Azure Metrics Explorer](metrics-getting-started.md)<br>[Mått som stöds med Azure Monitor](metrics-supported.md) |
| Azure Monitor-loggar | Kopiera plattform mått till loggar för analys för populära och andra som använder Log Analytics. | [Azure Diagnostics-data direkt till Log Analytics](collect-azure-metrics-logs.md#azure-diagnostics-direct-to-log-analytics) |
| Event Hubs | Stream mått till andra platser med Event Hubs. |[Stream Azure-övervakningsdata till en händelsehubb för användning av något externt verktyg](stream-monitoring-data-event-hubs.md) |

### <a name="diagnostic-logs"></a>Diagnostikloggar
[Diagnostikloggar](diagnostic-logs-overview.md) ge insikter om den _interna_ driften av en Azure-resurs.  Diagnostikloggar är inte aktiverade som standard. Du måste aktivera dem och ange ett mål för varje resurs. 

Krav och innehållet i diagnostikloggar varierar beroende på resurstyp och alla tjänster ännu skapa diagnostikloggar. Se [tjänster, scheman och kategorier som stöds för diagnostikloggar för Azure](diagnostic-logs-schema.md) mer information om varje tjänst och länkar till detaljerad konfigurationsprocedurer. Om tjänsten inte visas i den här artikeln och sedan tjänsten för närvarande inte skriva till diagnostikloggar.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure Monitor-loggar | Skicka diagnostikloggar till Azure Monitor-loggar för analys med andra insamlade loggdata. Vissa resurser kan skriva direkt till Azure Monitor medan andra skriva till ett lagringskonto innan som importeras till en Log Analytics-arbetsyta. | [Stream, Azure-diagnostikloggar till Log Analytics-arbetsyta i Azure Monitor](diagnostic-logs-stream-log-store.md)<br>[Använd Azure portal för att samla in loggar från Azure Storage](azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage)  |
| Storage | Skicka diagnostik loggar till Azure Storage för arkivering. | [Arkivera Azure diagnostikloggar](archive-diagnostic-logs.md) |
| Event Hubs | Stream diagnostikloggar till andra platser med Event Hubs. |[Stream, Azure-diagnostikloggar till en händelsehubb](diagnostic-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>Operativsystemet (Gäst)
Beräkningsresurser i Azure, i andra moln och lokala har ett gästoperativsystem som du övervakar. Med installationen av en eller flera agenter kan du samla in telemetri från gästen i Azure Monitor för att analysera den med samma övervakningsverktyg som Azure-tjänsterna själva.

![Azure-beräkning resurssamling](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Azure-diagnostiktillägget
Aktiverar Azure Diagnostics-tillägg för virtuella datorer med Azure kan du samla in loggar och mått från gästoperativsystemet för Azure-beräkningsresurser, inklusive Azure Cloud Service (klassisk) rollerna Web och Worker, virtuella datorer, virtuella datorn skalningsuppsättningar och Service Fabric.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Storage | När du aktiverar Diagnostics-tillägg kan skriver den till ett lagringskonto som standard. | [Lagra och visa diagnostikdata i Azure Storage](diagnostics-extension-to-storage.md) |
| Azure Monitor-mått | När du konfigurerar Diagnostics-tillägg för att samla in prestandaräknare, skrivs de till databasen för Azure Monitor-mått. | [Skicka gäst-OS mått av Azure Monitor-måtten lagra med en Resource Manager-mall för en Windows-dator](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Application Insights-Logs | Samla in loggar och prestandaräknare från beräkningsresursen som stöder ditt program som ska analyseras med andra programdata. | [Skicka molntjänst, virtuell dator eller Service Fabric diagnostiska data till Application Insights](diagnostics-extension-to-application-insights.md) |
| Event Hubs | Konfigurera Diagnostics-tillägg för att strömma data till andra platser med Event Hubs.  | [Azure Diagnostics-data i den heta sökvägen för direktuppspelning med Event Hubs](diagnostics-extension-stream-event-hubs.md) |

### <a name="log-analytics-agent"></a>Log Analytics-agenten 
Installera Log Analytics-agenten för omfattande övervakning och hantering av Windows- eller Linux-datorer. Den virtuella datorn kan köras i Azure, en annan molnet eller lokalt.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure Monitor-loggar | Log Analytics-agenten ansluter till Azure övervaka antingen direkt eller via System Center Operations Manager och kan du samla in data från datakällor som du konfigurerar eller övervakningslösningar som tillhandahåller ytterligare insikter i program körs på den virtuella datorn. | [Agent-datakällor i Azure Monitor](agent-data-sources.md)<br>[Ansluta Operations Manager till Azure Monitor](om-agents.md) |


### <a name="azure-monitor-for-vms"></a>Azure Monitor för virtuella datorer 
[Azure Monitor för virtuella datorer](../insights/vminsights-overview.md) tillhandahåller en anpassad övervakning för virtuella datorer som tillhandahåller funktioner utöver Azure Monitor av huvudfunktioner, inklusive status för tjänsten och VM-hälsa. Det krävs en Beroendeagenten för Windows och Linux-datorer som kan integreras med Log Analytics-agenten för att samla in identifierade data om processer som körs på den virtuella datorn och en extern Processberoenden.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure Monitor-loggar | Lagrar data om processer och beroenden på agenten. | [Med Azure Monitor för virtuella datorer (förhandsversion) kartan för att förstå programkomponenter](../insights/vminsights-maps.md) |
| VM-lagring | Azure Monitor för virtuella datorer lagrar information om hälsoavsökning tillstånd på en annan plats. Detta är endast tillgängliga i Azure Monitor för virtuella datorer i Azure-portalen utöver den [Azure Resource health REST API](/rest/api/resourcehealth/). | [Förstå hälsotillståndet för virtuella datorer i Azure](../insights/vminsights-health.md)<br>[Azure Resource health REST API](https://docs.microsoft.com/rest/api/resourcehealth/) |



## <a name="application-code"></a>Programkod
Detaljerad programövervakning i Azure Monitor är klar med [Application Insights](https://docs.microsoft.com/azure/application-insights/) som samlar in data från program som körs på en rad olika plattformar. Programmet kan köras i Azure, en annan molnet eller lokalt.

![Programdatainsamling](media/data-sources/applications.png)


### <a name="application-data"></a>Programdata
När du aktiverar Application Insights för ett program genom att installera en instrumentationspaket samlar det in mått och loggar som relaterar till prestanda och driften av programmet. Application Insights lagrar data som samlas in i samma Azure Monitor-dataplattformen som används av andra datakällor. Den innehåller omfattande verktyg för att analysera data, men du kan också analysera den med data från andra källor med hjälp av verktyg, till exempel Metrics Explorer och Log Analytics.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure Monitor-loggar | Användningsdata om ditt program, inklusive sidvisningar, programförfrågningar, undantag och spår. | [Analysera loggdata i Azure Monitor](../log-query/log-query-overview.md) |
|                    | Beroendeinformation mellan programkomponenter som ska stödja programavbildning och telemetrikorrelation. | [Telemetrikorrelation i Application Insights](../app/correlation.md) <br> [Programkarta](../app/app-map.md) |
|            | Resultaten av tillgänglighetstester som testar tillgängligheten och svarstiden för programmet från olika platser på Internet. | [Övervaka tillgänglighet och svarstider på valfri webbplats](../app/monitor-web-app-availability.md) |
| Azure Monitor-mått | Application Insights samlar in mått som beskriver prestanda och driften av programmet utöver anpassade mått som du definierar i ditt program i Azure Monitor metrics-databasen. | [Loggbaserade och preaggregeras mått i Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[Application Insights API för anpassade händelser och mått](../app/api-custom-events-metrics.md) |
| Azure Storage | Skicka programdata till Azure Storage för arkivering. | [Exportera telemetri från Application Insights](../app/export-telemetry.md) |
|            | Information om tillgänglighetstester lagras i Azure Storage. Använd Application Insights i Azure-portalen för lokala analys. Resultaten av tillgänglighetstester lagras i Azure Monitor-loggar. | [Övervaka tillgänglighet och svarstider på valfri webbplats](../app/monitor-web-app-availability.md) |
|            | Profiler-spårningsdata lagras i Azure Storage. Använd Application Insights i Azure-portalen för lokala analys.  | [Profilen produktionsprogram i Azure med Application Insights](../app/profiler-overview.md) 
|            | Felsök ögonblicksbild data som samlas in för en delmängd av undantag som är lagrade i Azure Storage. Använd Application Insights i Azure-portalen för lokala analys.  | [Så här fungerar ögonblicksbilder](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Övervakningslösningar och insikter
[Övervakningslösningar](../insights/solutions.md) och [Insights](../insights/insights-overview.md) samla in data för att tillhandahålla ytterligare insikter i driften av en viss tjänst eller ett program. De kan hantera resurser i olika programnivåer och även flera nivåer.

### <a name="monitoring-solutions"></a>Övervakningslösningar

| Mål | Beskrivning | Referens
|:---|:---|:---|
| Azure Monitor-loggar | Övervakningslösningar samla in data till Azure Monitor-loggar där det kan vara analyseras med hjälp av frågespråket eller [vyer](view-designer.md) som vanligtvis ingår i lösningen. | [Information om samlingen för att övervaka lösningar i Azure](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>Azure Monitor för behållare
[Azure Monitor för behållare](../insights/container-insights-overview.md) får du en anpassad övervakning för [Azure Kubernetes Service (AKS)](/azure/aks/). Den samlar in ytterligare information om dessa resurser som beskrivs i följande tabell.

| Mål | Beskrivning | Referens |
|:---|:---|:---|
| Azure Monitor-loggar | Butiker övervakningsdata för AKS inklusive inventering, loggar och händelser. Måttdata lagras också i loggarna för att kunna utnyttja dess analysfunktionerna i portalen. | [Förstå prestandan för AKS-kluster med Azure Monitor för containrar](../insights/container-insights-analyze.md) |
| Azure Monitor-mått | Måttdata lagras i databasen för mått att enheten visualisering och aviseringar. | [Visa behållaren mått i metrics explorer](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | I ordning till en näst intill realtid upplevelse anger data direkt från Azure Kubernetes service i Azure Monitor för behållare i Azure-portalen. | [Visa containerloggar i realtid med Azure Monitor för containrar (förhandsversion)](../insights/container-insights-live-logs.md) |

### <a name="azure-monitor-for-vms"></a>Azure Monitor för virtuella datorer
[Azure Monitor för virtuella datorer](../insights/vminsights-overview.md) tillhandahåller en anpassad upplevelse för övervakning av virtuella datorer. En beskrivning av data som samlas in av Azure Monitor för virtuella datorer som ingår i den [operativsystem (Gäst)](#operating-system-guest) ovan.

## <a name="custom-sources"></a>Anpassade källor
Förutom standardnivån för ett program, kan du behöva övervaka andra resurser som har telemetri som inte kan samlas in med andra datakällor. Skriva dessa data till mått eller loggar med ett Azure Monitor-API för dessa resurser.

![Anpassad insamling](media/data-sources/custom.png)

| Mål | Metod | Beskrivning | Referens |
|:---|:---|:---|:---|
| Azure Monitor-loggar | API för datainsamling | Samla in loggdata från en REST-klient och lagra i Log Analytics-arbetsyta. | [Skicka data till Azure Monitor med HTTP Data Collector API (förhandsversion)](data-collector-api.md) |
| Azure Monitor-mått | Anpassade mått-API | Samlar in mätvärden från alla REST-klient och lagra i Azure Monitor metrics-databasen. | [Skicka anpassade mått för en Azure-resurs till arkivet som Azure Monitor-mått med hjälp av ett REST-API](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Övriga tjänster
Andra tjänster i Azure skriva data till Azure Monitor-dataplattform. På så sätt kan du analysera data som samlas in av dessa tjänster med data som samlas in av Azure Monitor och utnyttja samma analys- och visualiseringsverktyg.

| Tjänst | Mål | Beskrivning | Referens |
|:---|:---|:---|:---|
| [Azure Security Center](/azure/security-center/) | Azure Monitor-loggar | Azure Security Center lagrar de säkerhetsdata som samlas in i en Log Analytics-arbetsyta som gör att de kan analyseras med andra loggdata som samlas in av Azure Monitor.  | [Datainsamling i Azure Security Center](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](/azure/sentinel/) | Azure Monitor-loggar | Azure Sentinel lagrar data som samlas in från olika datakällor i en Log Analytics-arbetsyta som gör att de kan analyseras med andra loggdata som samlas in av Azure Monitor.  | [Ansluta datakällor](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>Nästa steg

- Läs mer om den [typerna av övervakningsdata som samlas in av Azure Monitor](data-platform.md) och hur du visar och analyserar dessa data.
- Lista de [olika platser där Azure-resurser datalagring](data-locations.md) och hur du kommer åt den. 
