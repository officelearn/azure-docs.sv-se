---
title: Nyheter i Azure Monitor-dokumentation
description: Viktiga uppdateringar av Azure Monitor-dokumentationen uppdateras varje månad.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 03/05/2020
ms.openlocfilehash: c29790035ec4e971957784e826a1e8bd8e0c9329
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79500494"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Vad är nytt i Azure Monitor-dokumentationen?
Den här artikeln innehåller listor över Azure Monitor-artiklar som antingen är nya eller har uppdaterats avsevärt. Den uppdateras den första veckan i varje månad för att inkludera artikeluppdateringar från föregående månad.

## <a name="february-2020"></a>Februari 2020

### <a name="agents"></a>Agenter
Flera uppdateringar som en del av omskrivning av diagnostiktilläggsinnehåll.

- [Översikt över Azure-övervakningsagenter](platform/agents-overview.md) - Omstrukturerade tabeller för att bättre klargöra unika funktioner för varje agent.
- [Översikt över Azure Diagnostics-tillägg](platform/diagnostics-extension-overview.md) – Fullständig omskrivning.
- [Använd blob-lagring för IIS och tabelllagring för händelser i Azure Monitor](platform/diagnostics-extension-logs.md) - Allmän omskrivning för uppdatering och tydlighet.
- [Installera och konfigurera Windows Azure diagnostics extension (WAD)](platform/diagnostics-extension-windows-install.md) - Ny artikel. 
- [Windows-diagnostiktilläggsschema](platform/diagnostics-extension-schema-windows.md) - Omorganiseras.
- [Skicka data från Windows Azure diagnostics-tillägget till Azure Event Hubs](platform/diagnostics-extension-stream-event-hubs.md) - Helt omskrivna och uppdaterade.
- [Lagra och visa diagnostikdata i Azure Storage](/azure/cloud-services/diagnostics-extension-to-storage) - Helt omskriven och uppdaterad.
- [Log Analytics virtual machine-tillägg för Windows](../virtual-machines/extensions/oms-windows.md) – Bättre förtydligar relationen med Log Analytics-agenten.
- [Azure Monitor virtual machine extension för Linux](../virtual-machines/extensions/oms-linux.md) - Bättre förtydligar relationen med Log Analytics-agenten.




### <a name="application-insights"></a>Application Insights
- [Anslutningssträngar i Azure Application Insights](app/sdk-connection-string.md) - Ny artikel.

### <a name="insights-and-solutions"></a>Insikter och lösningar

#### <a name="azure-monitor-for-containers"></a>Azure Monitor för containrar
- [Integrera Azure Active Directory med Azure Kubernetes Service](../aks/azure-ad-integration.md) - Lade till anteckning för att skapa ett klientprogram för att stödja RBAC-aktiverat kluster för att stödja Azure Monitor för behållare.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor för virtuella datorer
- [Azure Monitor för virtuella datorer (GA) vanliga frågor och svar](insights/vminsights-ga-release-faq.md) - Ändra till hur prestandadata lagras.

#### <a name="office-365"></a>Office 365
- [Hanteringslösning för Office 365 i Azure](insights/solution-office-365.md) – Uppdaterat utfasningsdatum.


### <a name="logs"></a>Loggar
- [Optimera loggfrågor i Azure Monitor](log-query/query-optimization.md) - Ny artikel.
- [Hantera användning och kostnader för Azure Monitor Logs](platform/manage-cost-storage.md) - Förbättrade exempelfrågor för att förstå din användning.

### <a name="metrics"></a>Mått
- [Azure Monitor-plattformsmått som kan exporteras via diagnostikinställningar](platform/metrics-supported-export-diagnostic-settings.md) – lagt till avsnitt om ändring av beteende för nulls- och nollvärden.


### <a name="visualizations"></a>Visualiseringar
Flera nya artiklar för visningsdesigner till arbetsböcker konvertering guide.

- [Azure Monitor-vydesigner till arbetsböcker övergångsguide](platform/view-designer-conversion-overview.md) - Ny artikel.
- [Azure Monitor-vydesigner till arbetsböcker konverteringsalternativ](platform/view-designer-conversion-options.md) - Ny artikel.
- [Azure Monitor-vydesigner till arbetsböcker panelkonverteringar](platform/view-designer-conversion-tiles.md) - Ny artikel.
- [Azure Monitor-vydesigner till arbetsböcker konvertering sammanfattning och åtkomst](platform/view-designer-conversion-access.md) - Ny artikel.
- [Azure Monitor-vydesigner till arbetsböcker konvertering vanliga uppgifter](platform/view-designer-conversion-tasks.md) - Ny artikel.
- [Azure Monitor-vydesigner till arbetsböcker konverteringsexempel](platform/view-designer-conversion-examples.md) - Ny artikel.




## <a name="january-2020"></a>Januari 2020

### <a name="general"></a>Allmänt
- [Vad övervakas av Azure Monitor?](monitor-reference.md) - Ny artikel.

### <a name="agents"></a>Agenter
- [Samla in loggdata med Azure Log Analytics-agent](platform/log-analytics-agent.md) - Uppdaterad informationsförvar för nätverksbrandväggen.


### <a name="alerts"></a>Aviseringar
- [Skapa och hantera åtgärdsgrupper i Azure-portalen](platform/action-groups.md) - Inställning som tagits bort för v2-funktioner som inte längre krävs.
- [Skapa en måttavisering med en Resource Manager-mall](platform/alerts-metric-create-templates.md) - Tillagt exempel för parametern *ignoreDataBefore.*  Lade till begränsningar om regler för flera villkor.
- [Använda Log Analytics Alert REST API](platform/api-alerts.md) - JSON exempel korrigeras.


### <a name="application-insights"></a>Application Insights
- [IP-adresser som används av Application Insights och Log Analytics](app/ip-addresses.md) - Uppdaterade avsnittet Tillgänglighetstest med hur du lägger till en inkommande portregel för att tillåta trafik med Hjälp av Azure Network Security Groups.
- [Felsöka problem med Profiler för Azure Application Insights](app/profiler-troubleshooting.md) – Uppdaterad allmän felsökning.
- [Telemetrisampling i Azure Application Insights](app/sampling.md) – Uppdaterad och omstrukturerad för att förbättra läsbarheten baserat på feedback från kunder.


### <a name="data-security"></a>Datasäkerhet
- [Azure Monitor kundhanterad nyckelkonfiguration](platform/customer-managed-keys.md) - Ny artikel.

### <a name="insights-and-solutions"></a>Insikter och lösningar

#### <a name="azure-monitor-for-containers"></a>Azure Monitor för containrar
- [Konfigurera Azure Monitor för behållare agent datainsamling](insights/container-insights-agent-config.md) - Lagt till information för uppgradering agent på Azure Red Hat OpenShift, och lagt till ytterligare information för att skilja metoderna för uppgradering agent.
- [Skapa prestandaaviseringar för Azure Monitor för behållare](insights/container-insights-alerts.md) – Reviderad information och uppdaterade steg för att skapa en avisering om prestandadata som lagras på arbetsytan med hjälp av aviseringar för arbetsytekontexter.
- [Kubernetes övervakning med Azure Monitor för behållare](insights/container-insights-analyze.md) - Uppdaterade både översiktsartikeln och analysera artikeln om stöd för Windows Kubernetes kluster.
- [Konfigurera Azure Red Hat OpenShift-kluster med Azure Monitor för behållare](insights/container-insights-azure-redhat-setup.md) – Lade till information för uppgraderingsagent på Azure Red Hat OpenShift och lade till ytterligare information för att skilja metoderna för uppgradering av agent.
- [Konfigurera Hybrid Kubernetes-kluster med Azure Monitor för behållare](insights/container-insights-hybrid-setup.md) – Uppdaterad för att återspegla extra stöd för säker port:10250 med Kubelets cAdvisor.
- [Hantera Azure Monitor för behållare agent](insights/container-insights-manage-agent.md) - Uppdaterad information relaterad till beteende och config av metrisk skrapning med Azure Red Hat OpenShift jämfört med andra typer av Kubernetes kluster.
- [Konfigurera Azure Monitor för behållare Prometheus Integration](insights/container-insights-prometheus-integration.md) - Uppdaterad information relaterad till beteende och config av metrisk skrapning med Azure Red Hat OpenShift jämfört med andra typer av Kubernetes-kluster.
- [Så här uppdaterar du Azure Monitor för behållare för mått](insights/container-insights-update-metrics.md) – Uppdaterad information om beteende och konfiguration av måttskrapning med Azure Red Hat OpenShift jämfört med andra typer av Kubernetes-kluster.


#### <a name="azure-monitor-for-vms"></a>Azure Monitor för virtuella datorer
- [Azure Monitor för virtuella datorer (GA) ställde vanliga frågor](insights/vminsights-ga-release-faq.md) – Lagt till information om uppgradering av arbetsyta och agenter till ny version.

#### <a name="office-365"></a>Office 365
- [Hanteringslösning för Office 365 i Azure](insights/solution-office-365.md) – Lade till information och vanliga frågor om migrering till Office 365-lösning i Azure Sentinel. Borttagen introduktionssektion.



### <a name="logs"></a>Loggar
- [Hantera logganalysarbetsytor i Azure Monitor](platform/manage-access.md) – Uppdateringar av inte åtgärder.
- [Hantera användning och kostnader för Azure Monitor Logs](platform/manage-cost-storage.md) - Lade till förtydligande vid beräkning av datavolym i avsnittet Prismodell.
- [Använd Azure Resource Manager-mallar för att skapa och konfigurera en logganalysarbetsyta](platform/template-workspace-configuration.md) – uppdaterad mall med nya prisnivåer.


### <a name="platform-logs"></a>Plattformsloggar
- [Samla in Azure Activity-logg med diagnostikinställningar- Azure Monitor](platform/diagnostic-settings-legacy.md) - Ytterligare information om ändrade egenskaper.
- [Exportera Azure Activity Log](platform/activity-log-export.md) - Uppdaterad för gränssnittsändringar. 





## <a name="december-2019"></a>December 2019

### <a name="agents"></a>Agenter
- [Anslut Linux-datorer till Azure Monitor](platform/agent-linux.md) - Ny artikel.

### <a name="alerts"></a>Aviseringar
- [Skapa en måttavisering med en Resource Manager-mall](platform/alerts-metric-create-templates.md) - Tillagt exempel för anpassade mått.
- [Skapa aviseringar med dynamiska tröskelvärden i Azure Monitor](platform/alerts-dynamic-thresholds.md) – Lagt till avsnitt om tolkning av dynamiska tröskelvärden.
- [Översikt över aviseringar och meddelandeövervakning i Azure](platform/alerts-overview.md) - Uppdaterad resursdiagramfråga.
- [Resurser som stöds för måttaviseringar i Azure Monitor](platform/alerts-metric-near-real-time.md) – Uppdatera till mått och dimensioner som stöds.
- [Växla från äldre Logg Analytics-aviseringar API till nytt Azure Alerts API](platform/alerts-log-api-switch.md) - Lade till anteckning om ändrat aviseringsnamn.
- [Förstå hur måttaviseringar fungerar i Azure Monitor.](platform/alerts-metric-overview.md) - Lade till resurstyper som stöds för övervakning i stor skala.

### <a name="application-insights"></a>Application Insights
- [Application Insights for Worker Service apps (icke-HTTP-appar)](app/worker-service.md) - Lade till standardloggningsnivå i C#-kod. Uppdaterad paketreferensversion.
- [ApplicationInsights.config-referens - Azure](app/configuration-with-applicationinsights-config.md) - Uppdaterad exempelkod.
- [Automatisera Azure Application Insights med PowerShell](app/powershell.md) - Update to Resource Manager-mall.
- [Azure Monitor Application Insights NuGet-paket](app/nuget.md) - Uppdaterade paketversioner.
- [Skapa en ny Azure Application Insights-resurs](app/create-new-resource.md) – Anteckning som lagts till i globalt unikt namn.
- [Diagnostisera med Live Metrics Stream - Azure Application Insights](app/live-stream.md) - Uppdaterad ASP.NET Core SDK-versionskrav.
- [Händelseräknare i Application Insights](app/eventcounters.md) - Uppdaterad kategori och tabell till customMetrics.
- [Utforska Java-spårningsloggar i Azure Application Insights](app/java-trace-logs.md) – Lade till konfiguration för Java-agentloggningströskel.
- [IP-adresser som används av Application Insights och Log Analytics](app/ip-addresses.md) - Uppdaterade IP-adresser för Live Metrics Stream.
- [Övervaka prestanda för Azure-apptjänster](app/azure-web-apps.md) – Har lagt till stöd för ASP.NET Core 3.0. 
- [Övervaka Python-program med Azure Monitor (förhandsversion)](app/opencensus-python.md) – Lade till förtydligande för OpenCensus Python-schemamappning i Azure . Schema för bildskärm
- [Viktig information för Azure Application Insights](app/release-notes.md) - Lade till anteckningar för äldre versioner.
- [Telemetrikanaler i Azure Application Insights](app/telemetry-channels.md) - Uppdaterad varaktighet för ignorerade data under längre anslutningsperiod.
- [Telemetrisampling i Azure Application Insights](app/sampling.md) - Korrigerat kodavsnitt för anpassad TelemetryInitializer.
- [Felsöka programinsikter i ett Java-webbprojekt](app/java-troubleshoot.md) – Tog bort uttryck om att inte stödja beroendesamling i JDK 9.

### <a name="insights-and-solutions"></a>Insikter och lösningar
- [Azure Monitor för behållare Vanliga frågor](insights/container-insights-faq.md) – Lade till fråga i fälten Bild och Namn.
- [Azure SQL Analytics-lösning i Azure Monitor](insights/azure-sql.md) - Uppdaterad databas väntar support för hanterade instanser.
- [Konfigurera Azure Monitor för behållare agent datainsamling](insights/container-insights-agent-config.md) - Added inställning för enrich_container_logs.
- [Konfigurera Hybrid Kubernetes-kluster med Azure Monitor för behållare](insights/container-insights-hybrid-setup.md) – tillagd felsökningssektion.
- [Övervaka Active Directory-replikeringsstatus med Azure Monitor](insights/ad-replication-status.md) - .NET Framework-förutsättningen uppdaterad.
- [Network Performance Monitor-lösning i Azure](insights/network-performance-monitor.md) – Lade till regioner som stöds.
- [Optimera din Active Directory-miljö med Azure Monitor](insights/ad-assessment.md) - .NET Framework förutsättning uppdaterad.
- [Optimera din SQL Server-miljö med Azure Monitor](insights/sql-assessment.md) - .NET Framework förutsättning uppdaterad.
- [Optimera din System Center Operations Manager-miljö med Azure Log Analytics](insights/scom-assessment.md) - .NET Framework förutsättning uppdaterad.
- [Anslutningar som stöds med IT Service Management Connector i Azure Log Analytics](platform/itsmc-connections.md) - Lade till New York till ett förutsättningsklient-ID och klienthemlighet.

### <a name="logs"></a>Loggar
- [Ta bort och återställa Azure Log Analytics-arbetsyta](platform/delete-workspace.md) - Added PowerShell-metod.
- [Designa din Azure Monitor Logs-distribution](platform/design-logs-deployment.md) - Inmatningshastigheten för en arbetsyta ökade.

### <a name="metrics"></a>Mått
- [Azure Monitor-plattformsmått som kan exporteras via diagnostikinställningar](platform/metrics-supported-export-diagnostic-settings.md) – ny artikel.

### <a name="platform-logs"></a>Plattformsloggar
Flera artiklar uppdateras som en del av omstruktureringen av innehåll för plattformsloggar baserat på ny funktion för att konfigurera aktivitetslogg med hjälp av diagnostikinställningar.

- [Arkivera Azure-resursloggar till lagringskonto](platform/resource-logs-collect-storage.md)
- [Händelseschema för Azure Activity Log](platform/activity-log-schema.md)
- [Begränsningar för Azure Monitor-tjänsten](service-limits.md)
- [Samla in och analysera Azure-aktivitetsloggar i Log Analytics-arbetsytan](platform/activity-log-collect.md)
- [Samla in Azure Activity-logg med diagnostikinställningar (förhandsversion) – Azure Monitor](platform/diagnostic-settings-legacy.md)
- [Samla in Azure Activity-loggar i en log Analytics-arbetsyta för Azure-klienter](platform/activity-log-collect-tenants.md)
- [Samla in Azure-resursloggar i Log Analytics-arbetsytan](platform/resource-logs-collect-workspace.md)
- [Skapa diagnostikinställning i Azure med hjälp av Resource Manager-mallen](platform/diagnostic-settings-template.md)
- [Skapa diagnostikinställning för att samla in loggar och mått i Azure](platform/diagnostic-settings.md)
- [Exportera Azure-aktivitetsloggen](platform/activity-log-export.md)
- [Översikt över Azure-plattformsloggar](platform/platform-logs-overview.md)
- [Strömma Azure-övervakningsdata till händelsehubb](platform/stream-monitoring-data-event-hubs.md)
- [Strömma Azure-plattformsloggar till en händelsehubb](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>Snabbstarter och självstudier

- [Skapa ett måttdiagram i Azure Monitor](learn/tutorial-metrics-explorer.md) - Ny artikel.
- [Samla in resursloggar från en Azure-resurs och analysera med Azure Monitor](learn/tutorial-resource-logs.md) - Ny artikel.
- [Övervaka en Azure-resurs med Azure Monitor](learn/quick-monitor-azure-resource.md) - Ny artikel.
   
## <a name="next-steps"></a>Nästa steg

- Om du vill bidra till Azure Monitor-dokumentationen läser du [docs contributor guide](https://docs.microsoft.com/contribute/).