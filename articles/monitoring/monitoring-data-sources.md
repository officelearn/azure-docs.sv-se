---
title: Källor för övervakningsdata i Azure | Microsoft Docs
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
ms.date: 07/05/2018
ms.author: bwren
ms.openlocfilehash: 48cbfac78b41b47419799584837e094d45757628
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627465"
---
# <a name="sources-of-monitoring-data-in-azure"></a>Källor för övervakningsdata i Azure
Den här artikeln beskriver data som kan övervaka hälsotillstånd och prestanda för dina Azure-resurser och program som körs på dem.  Samla in och analysera dessa data med verktygen som beskrivs i [samla in övervakningsdata i Azure](monitoring-data-collection.md)

Övervakning av data i Azure kommer från olika källor som kan ordnas i nivåer, den högsta nivån som ditt program och den lägsta nivån som Azure-plattformen. Detta illustreras i följande diagram där varje nivå som beskrivs i detalj i följande avsnitt.

![Nivåer av övervakning av data](media/monitoring-data-sources/monitoring-tiers.png)


## <a name="azure-platform"></a>Azure-plattformen
Telemetrirelaterade hälsa och åtgärden Azure själva omfattar information om driften och hanteringen av din Azure-prenumeration eller -klient. Den innehåller service health-data som lagras i Azure-aktivitetsloggen och granskningsloggar från Azure Active Directory.

![Azure-samling](media/monitoring-data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../monitoring-and-diagnostics/monitoring-service-notifications.md) innehåller information om hälsotillståndet för Azure-tjänster i din prenumeration som ditt program och resurser som förlitar sig på. Du kan skapa aviseringar för att aviseras om aktuella och förväntade allvarliga problem som kan påverka ditt program. Tjänstehälsa poster lagras i den [Azure-aktivitetsloggen](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), så du kan visa dem i aktiviteten Log Utforskaren och kopiera dem till Log Analytics.

### <a name="azure-activity-log"></a>Azure-aktivitetsloggen
Den [Azure-aktivitetsloggen](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) innehåller tjänsten hälsojournaler tillsammans med poster på några konfigurationsändringar som gjorts i dina Azure-resurser. Aktivitetsloggen är tillgängligt för alla Azure-resurser och visar sina _externa_ vy. Vissa typer av poster i aktivitetsloggen beskrivs i [Azure-aktivitetsloggen Händelseschema](../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

Du kan visa aktivitetsloggen för en viss resurs på en sida i Azure portal eller visa loggarna från flera resurser i den [aktivitet Log Explorer](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Det är särskilt användbart för att kopiera loggposterna till Log Analytics för att kombinera det med andra övervakningsdata. Du kan även skicka dem till andra platser med hjälp av [Händelsehubbar](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md).


### <a name="azure-active-directory-audit-logs"></a>Azure Active Directory med granskningsloggar
[Azure Active Directory-rapportering](../active-directory/reports-monitoring/overview-reports.md) innehåller historik över inloggning aktivitet och granska spårning av ändringar som gjorts i en viss klient. Du kan inte för närvarande kombinera Azure Active Directory-granskningsdata med andra övervakningsdata som det är bara tillgängligt via Azure Active Directory och [Azure Active Directory reporting API](../active-directory/reports-monitoring/concept-reporting-api.md).


## <a name="azure-services"></a>Azure-tjänster
Mått och resurs på diagnostikloggar ger information om den _interna_ driften av Azure-resurser. Dessa är tillgängliga för de flesta Azure-tjänster och lösningar för hantering av ge ytterligare insikter om särskilda tjänster.

![Azure resurssamling](media/monitoring-data-sources/azure-resource-collection.png)


### <a name="metrics"></a>Mått
De flesta Azure services genererar mått som motsvarar deras prestanda och åtgärd. Specifikt [mått kan variera för varje typ av resurs](../monitoring-and-diagnostics/monitoring-supported-metrics.md).  De är tillgängliga från Metrics Explorer och kan kopieras till Log Analytics för analys trender och andra.


### <a name="resource-diagnostic-logs"></a>Resursdiagnostikloggar
Medan aktivitetsloggen innehåller information om åtgärder som utförs på en Azure-resurser, resursnivå [diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) ge insikter om driften av själva resursen.   Krav och innehållet i de här loggarna [varierar beroende på resurstyp](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

Du kan inte visa diagnostikloggar direkt i Azure-portalen, men du kan [skicka dem till Azure storage för arkivering](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) och exportera dem till [Händelsehubb](../event-hubs/event-hubs-what-is-event-hubs.md) för omdirigering till andra tjänster eller [in Analytics](../monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics.md) för analys. Vissa resurser kan skriva direkt till Log Analytics medan andra innan du kan skriva till ett lagringskonto [importeras till Log Analytics](../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).

### <a name="management-solutions"></a>Hanteringslösningar
 [Lösningar för hantering av](../monitoring/monitoring-solutions.md) samla in data för att ge ytterligare insikter i driften av en viss tjänst. De samla in data till Log Analytics där det kan vara analyseras med hjälp av den [frågespråket](../log-analytics/log-analytics-log-search.md) eller vyer som vanligtvis ingår i lösningen.

## <a name="guest-operating-system"></a>Gästoperativsystem
Utöver den telemetri som genereras av alla Azure-tjänster, har beräkningsresurser ett gästoperativsystem som du övervakar. Med installationen av en eller flera agenter kan du samla in telemetri från gästen i samma övervakningsverktyg som Azure-tjänsterna själva.

![Azure-beräkning resurssamling](media/monitoring-data-sources/compute-resource-collection.png)

### <a name="diagnostic-extension"></a>Diagnostiktillägget
Med den [Azure Diagnostics-tillägget](../monitoring-and-diagnostics/azure-diagnostics.md), du kan samla in loggar och prestandadata från klientens operativsystem i Azure-beräkningsresurser. Lagras både mått och loggar som samlats in från klienter i ett Azure storage-konto som du kan [konfigurera Log Analytics för att importera från](../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).  Metrics Explorer förstår hur du läser från storage-konto och ska inkludera mätvärden för klienten med andra insamlade mätvärdena.


### <a name="log-analytics-agent"></a>Log Analytics-agenten
Du kan installera Log Analytics-agenten på alla Windows- eller Linux-dator eller fysisk dator. Den virtuella datorn kan köras i Azure, en annan molnet eller lokalt.  Agenten ansluter till Log Analytics antingen direkt eller via en [ansluten System Center Operations Manager-hanteringsgrupp](../log-analytics/log-analytics-om-agents.md) och kan du samla in data från [datakällor](../log-analytics/log-analytics-data-sources.md) som du konfigurerar eller från [hanteringslösningar](../monitoring/monitoring-solutions.md) som ger ytterligare insikter om program som körs på den virtuella datorn.

### <a name="service-map"></a>Tjänstkarta
[Tjänstkarta](../operations-management-suite/operations-management-suite-service-map.md) kräver en Beroendeagenten för Windows och Linux-datorer. Detta fungerar med Log Analytics som agenten samlar in information om processer som körs på den virtuella datorn och beroenden på externa processer. Den lagrar dessa data i Log Analytics och innehåller en konsol som visuellt visar data som samlas in utöver andra data som lagras i Log Analytics.

## <a name="applications"></a>Appar
Förutom telemetri som ditt program kan skriva till gästoperativsystemet, detaljerad programövervakning görs med [Application Insights](https://docs.microsoft.com/azure/application-insights/). Application Insights kan samla in data från program som körs på en rad olika plattformar. Programmet kan köras i Azure, en annan molnet eller lokalt.

![Programdatainsamling](media/monitoring-data-sources/application-collection.png)


#### <a name="application-data"></a>Programdata
När du aktiverar Application Insights för ett program genom att installera en instrumentationspaket samlar det in mått och loggar som relaterar till prestanda och driften av programmet. Detta inkluderar detaljerad information om sidvisningar, programförfrågningar och undantag. Application Insights lagrar data som samlas in i Azure-mått och Log Analytics. Den innehåller omfattande verktyg för att analysera data, men du kan också analysera den med data från andra källor med hjälp av verktyg, till exempel Metrics Explorer och sökningar i loggen.

Du kan också använda Application Insights till [skapa ett anpassat mått](../application-insights/app-insights-api-custom-events-metrics.md).  På så sätt kan du definiera din egen logik för att beräkna ett numeriskt värde och sedan lagra värdet med andra mått som kan nås från Metric Explorer och används för [Autoskala](../monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) och måttaviseringar.

#### <a name="dependencies"></a>Beroenden
Om du vill övervaka olika logiska åtgärder för ett program, måste du [samla in telemetri från flera komponenter](../application-insights/app-insights-transaction-diagnostics.md). Application Insights har stöd [distribuerade telemetrikorrelation](../application-insights/application-insights-correlation.md) som identifierar beroenden mellan komponenter så att du kan analysera dem tillsammans.

#### <a name="availability-tests"></a>Tillgänglighetstester
[Tillgänglighetstester](../application-insights/app-insights-monitor-web-app-availability.md) i Application Insights kan du testa tillgängligheten och svarstiden för programmet från olika platser på Internet. Du kan göra en enklare ping-test för att verifiera att programmet är aktiv eller skapa ett webbtest som simulerar ett Användarscenario med Visual Studio.  Tillgänglighetstester kräver inte någon instrumentation i programmet.

## <a name="next-steps"></a>Nästa steg

- Läs mer om den [typer av övervakningsdata och Azure-verktygen](monitoring-data-collection.md) används för att samla in och analysera dem.
