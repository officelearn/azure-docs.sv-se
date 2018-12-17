---
title: Datakällor i Azure Monitor | Microsoft Docs
description: Beskriver data som kan övervaka hälsotillstånd och prestanda för dina Azure-resurser och program som körs på dem.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2018
ms.author: bwren
ms.openlocfilehash: 1612c2d47f88b6d065e5307be5ff3c99d8f6d405
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2018
ms.locfileid: "53433448"
---
# <a name="sources-of-data-in-azure-monitor"></a>Datakällor i Azure Monitor
Den här artikeln beskrivs datakällorna som samlas in av Azure Monitor för att övervaka hälsotillstånd och prestanda för dina resurser och program som körs på dem. Dessa resurser kan vara i Azure i en annan molnet eller lokalt.  Se [Data som samlas in av Azure Monitor](data-collection.md) mer information om hur dessa data lagras och hur du kan visa den.

Övervakning av data i Azure kommer från olika källor som kan ordnas i nivåer, de högsta nivåerna som ditt program och alla operativsystem och lägre nivåer som komponenter i Azure-plattformen. Detta illustreras i följande diagram där varje nivå som beskrivs i detalj i följande avsnitt.

![Nivåer av övervakning av data](media/data-sources/monitoring-tiers.png)

## <a name="azure-tenant"></a>Azure-klient
Telemetri som är relaterade till din Azure-klient som samlas in från klienten som helhet tjänster som Azure Active Directory.

![Azure-klient-samling](media/data-sources/tenant-collection.png)

### <a name="azure-active-directory-audit-logs"></a>Azure Active Directory med granskningsloggar
[Azure Active Directory-rapportering](../../active-directory/reports-monitoring/overview-reports.md) innehåller historik över inloggning aktivitet och granska spårning av ändringar som gjorts i en viss klient. Dessa granskningsloggar kan skrivas till Azure Monitor-loggar att analysera dem med andra loggdata.


## <a name="azure-platform"></a>Azure-plattformen
Telemetrirelaterade hälsa och åtgärden Azure själva omfattar information om driften och hanteringen av din Azure-prenumeration. Den innehåller service health-data som lagras i Azure-aktivitetsloggen och granskningsloggar från Azure Active Directory.

![Azure-prenumeration samling](media/data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../monitoring-and-diagnostics/monitoring-service-notifications.md) innehåller information om hälsotillståndet för Azure-tjänster i din prenumeration som ditt program och resurser som förlitar sig på. Du kan skapa aviseringar för att aviseras om aktuella och förväntade allvarliga problem som kan påverka ditt program. Tjänstehälsa poster lagras i den [Azure-aktivitetsloggen](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), så du kan visa dem i aktiviteten Log Utforskaren och kopiera dem till Azure Monitor-loggar.

### <a name="azure-activity-log"></a>Azure-aktivitetsloggen
Den [Azure-aktivitetsloggen](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) innehåller tjänsten hälsojournaler tillsammans med poster på några konfigurationsändringar som gjorts i dina Azure-resurser. Aktivitetsloggen är tillgängligt för alla Azure-resurser och visar sina _externa_ vy. Vissa typer av poster i aktivitetsloggen beskrivs i [Azure-aktivitetsloggen Händelseschema](../../azure-monitor/platform/activity-log-schema.md).

Du kan visa aktivitetsloggen för en viss resurs på en sida i Azure portal eller visa loggarna från flera resurser i den [aktivitet Log Explorer](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Det är särskilt användbart för att kopiera loggposterna till Azure Monitor för att kombinera det med andra övervakningsdata. Du kan även skicka dem till andra platser med hjälp av [Händelsehubbar](../../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md).



## <a name="azure-services"></a>Azure-tjänster
Mått och resurs på diagnostikloggar ger information om den _interna_ driften av Azure-resurser. Dessa är tillgängliga för de flesta Azure-tjänster och lösningar för hantering av ge ytterligare insikter om särskilda tjänster.

![Azure resurssamling](media/data-sources/azure-resource-collection.png)


### <a name="metrics"></a>Mått
De flesta Azure services genererar [plattform mått](data-collection.md#metrics) som motsvarar deras prestanda och åtgärd. Specifikt [mått kan variera för varje typ av resurs](../../azure-monitor/platform/metrics-supported.md).  De är tillgängliga från Metrics explorer och kan kopieras till Log Analytics för analys trender och andra.


### <a name="resource-diagnostic-logs"></a>Resursdiagnostikloggar
Medan aktivitetsloggen innehåller information om åtgärder som utförs på en Azure-resurser, resursnivå [diagnostikloggar](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) ge insikter om driften av själva resursen.   Krav och innehållet i de här loggarna [varierar beroende på resurstyp](../../azure-monitor/platform/tutorial-dashboards.md).

Du kan inte visa diagnostikloggar direkt i Azure-portalen, men du kan [skicka dem till Azure storage för arkivering](../../azure-monitor/platform/archive-diagnostic-logs.md) och exportera dem till [Händelsehubb](../../event-hubs/event-hubs-about.md) för omdirigering till andra tjänster eller [in Analytics](../../monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics.md) för analys. Vissa resurser kan skriva direkt till Log Analytics medan andra innan du kan skriva till ett lagringskonto [importeras till Log Analytics](../../azure-monitor/platform/azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).

### <a name="monitoring-solutions"></a>Övervakningslösningar
 [Övervakningslösningar](../insights/solutions.md) samla in data för att ge ytterligare insikter i driften av en viss tjänst eller ett program. De samlar in data i Azure Monitor-loggar där det kan vara analyseras med hjälp av den [frågespråket](../log-query/log-query-overview.md) eller [vyer](view-designer.md) som vanligtvis ingår i lösningen.

## <a name="guest-operating-system"></a>Gästoperativsystem
Beräkningsresurser i Azure, i andra moln och lokala har ett gästoperativsystem som du övervakar. Med installationen av en eller flera agenter kan du samla in telemetri från gästen i samma övervakningsverktyg som Azure-tjänsterna själva.

![Azure-beräkning resurssamling](media/data-sources/compute-resource-collection.png)

### <a name="azure-diagnostic-extension"></a>Azure-diagnostiktillägget
Det ger en grundläggande nivå av övervakning genom att samla in loggar med Azure Diagnostics-tillägget och prestandadata från klientens operativsystem i Azure-beräkningsresurser.   

### <a name="log-analytics-agent"></a>Log Analytics-agenten
Omfattande övervakning och hantering av din Windows- eller Linux-datorer eller fysiska datorer levereras med Log Analytics-agenten. Den virtuella datorn kan köra i Azure, ett annat moln eller på plats och agenten ansluter till Azure övervaka antingen direkt eller via System Center Operations Manager och kan du samla in data från [datakällor](agent-data-sources.md) som du Konfigurera eller från [övervakningslösningar](../insights/solutions.md) som ger ytterligare insikter om program som körs på den virtuella datorn.

### <a name="dependency-agent"></a>Beroendeagent
[Tjänstkarta](../insights/service-map.md) och [Azure Monitor för virtuella datorer](../insights/vminsights-overview.md) kräver en Beroendeagenten för Windows och Linux-datorer. Detta är integrerat med Log Analytics-agenten till samlar in identifierade data om processer som körs på den virtuella datorn och en extern Processberoenden. Den lagrar dessa data i Azure Monitor och hjälper dig att visualisera de identifierade sammankopplade komponenterna.  

Mer information om skillnaderna mellan agenterna och som ska användas beroende på dina Övervakningskrav, se [Övervakningsöversikt agenter](agents-overview.md).

## <a name="applications"></a>Program
Förutom telemetri som ditt program kan skriva till gästoperativsystemet, detaljerad programövervakning görs med [Application Insights](https://docs.microsoft.com/azure/application-insights/). Application Insights kan samla in data från program som körs på en rad olika plattformar. Programmet kan köras i Azure, en annan molnet eller lokalt.

![Programdatainsamling](media/data-sources/application-collection.png)


### <a name="application-data"></a>Programdata
När du aktiverar Application Insights för ett program genom att installera en instrumentationspaket samlar det in mått och loggar som relaterar till prestanda och driften av programmet. Detta inkluderar detaljerad information om sidvisningar, programförfrågningar och undantag. Application Insights lagrar data som samlas in i Azure Monitor. Den innehåller omfattande verktyg för att analysera data, men du kan också analysera den med data från andra källor med hjälp av verktyg, till exempel mått analytics och log analytics.

Du kan också använda Application Insights till [skapa ett anpassat mått](../../application-insights/app-insights-api-custom-events-metrics.md).  På så sätt kan du definiera din egen logik för att beräkna ett numeriskt värde och sedan lagra värdet med andra mått som kan nås från Metric Explorer och används för [Autoskala](../../azure-monitor/platform/autoscale-custom-metric.md) och måttaviseringar.

### <a name="dependencies"></a>Beroenden
Om du vill övervaka olika logiska åtgärder för ett program, måste du [samla in telemetri från flera komponenter](../../application-insights/app-insights-transaction-diagnostics.md). Application Insights har stöd [distribuerade telemetrikorrelation](../../application-insights/application-insights-correlation.md) som identifierar beroenden mellan komponenter så att du kan analysera dem tillsammans.

### <a name="availability-tests"></a>Tillgänglighetstester
[Tillgänglighetstester](../../application-insights/app-insights-monitor-web-app-availability.md) i Application Insights kan du testa tillgängligheten och svarstiden för programmet från olika platser på Internet. Du kan göra en enklare ping-test för att verifiera att programmet är aktiv eller skapa ett webbtest som simulerar ett Användarscenario med Visual Studio.  Tillgänglighetstester kräver inte någon instrumentation i programmet.

## <a name="custom-sources"></a>Anpassade källor
Förutom standardnivån för ett program, kan du behöva övervaka andra resurser som har telemetri som inte kan samlas in med andra datakällor. Du behöver skriva dessa data med en Azure Monitor-API för dessa resurser.

![Anpassad insamling](media/data-sources/custom-collection.png)

### <a name="data-collector-api"></a>API för datainsamling
Azure Monitor kan samla in loggdata från alla REST-klient som använder den [Data Collector API](data-collector-api.md). På så sätt kan du skapa anpassade övervakningsscenarier och utökad övervakning till resurser som inte exponerar telemetri via andra källor.

## <a name="next-steps"></a>Nästa steg

- Läs mer om den [typerna av övervakningsdata som samlas in av Azure Monitor](data-collection.md) och hur du visar och analyserar dessa data.
