---
title: Nyheter i Azure Monitor-dokumentationen
description: Viktiga uppdateringar av Azure Monitor-dokumentationen uppdateras varje månad.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 02/05/2020
ms.openlocfilehash: ecee13850e735f827a5465e0f49039f514afe233
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77657749"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Vad är nytt i Azure Monitor-dokumentationen?
Den här artikeln innehåller listor Azure Monitor artiklar som antingen är nya eller som har uppdaterats markant. Den kommer att uppdateras den första veckan i varje månad för att inkludera artikel uppdateringar från föregående månad.

## <a name="january-2020"></a>Januari 2020

### <a name="general"></a>Allmänt
- [Vad övervakas av Azure Monitor?](monitor-reference.md) – Ny artikel.

### <a name="agents"></a>Agenter
- [Samla in loggdata med Azure Log Analytics agent](platform/log-analytics-agent.md) -uppdaterad tabell över nätverks brand Väggs krav.


### <a name="alerts"></a>Aviseringar
- [Skapa och hantera åtgärds grupper i Azure Portal](platform/action-groups.md) -inställningen tas bort för v2-funktioner som inte längre krävs.
- [Skapa en mått varning med en Resource Manager-mall – ett](platform/alerts-metric-create-templates.md) exempel har lagts till för parametern *ignoreDataBefore* .  Begränsningar för regler för flera villkor har lagts till.
- Att [använda Log Analytics varning REST API](platform/api-alerts.md) – JSON-exempel korrigeras.


### <a name="application-insights"></a>Application Insights
- [IP-adresser som används av Application Insights och Log Analytics](app/ip-addresses.md) – uppdaterat avsnittet tillgänglighets test med anvisningar för hur du lägger till en regel för inkommande portar för att tillåta trafik med hjälp av Azure nätverks säkerhets grupper.
- [Felsök problem med Azure Application Insights profiler](app/profiler-troubleshooting.md) -uppdaterad allmän fel sökning.
- [Telemetri-sampling i Azure Application insikter](app/sampling.md) – uppdateras och struktureras om för att förbättra läsbarheten baserat på feedback från kunderna.


### <a name="data-security"></a>Datasäkerhet
- [Azure Monitor kundhanterad nyckel konfiguration](platform/customer-managed-keys.md) – ny artikel.

### <a name="insights-and-solutions"></a>Insikter och lösningar

#### <a name="azure-monitor-for-containers"></a>Azure Monitor för containrar
- [Konfigurera Azure Monitor för behållare agent data insamling](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-agent-config) -ytterligare information för att uppgradera agenten i Azure Red Hat OpenShift och ytterligare information för att särskilja metoderna för att uppgradera agenten.
- [Skapa prestanda varningar för Azure Monitor för behållare](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-alerts) – ändrad information och uppdaterade steg för att skapa en avisering om prestanda data som lagras i arbets ytan med hjälp av arbets ytans kontext aviseringar.
- [Kubernetes övervakning med Azure Monitor för behållare](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-analyze) – uppdaterad både översikts artikeln och analys artikeln om stöd för Windows Kubernetes-kluster.
- [Konfigurera Azure Red Hat OpenShift-kluster med Azure Monitor för behållare](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-azure-redhat-setup) – ytterligare information för att uppgradera agenten i Azure Red Hat OpenShift och ytterligare information som särskiljer metoderna för att uppgradera agenten.
- [Konfigurera hybrid Kubernetes-kluster med Azure Monitor för behållare](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-hybrid-setup) – uppdaterat för att avspegla ytterligare stöd för säker port: 10250 med Kubelet cAdvisor.
- [Så här hanterar du Azure Monitor för behållare agent](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-manage-agent) – uppdaterad information relaterad till beteende och konfiguration av mått kassation med Azure Red Hat OpenShift jämfört med andra typer av Kubernetes-kluster.
- [Konfigurera Azure Monitor för containers Prometheus-integrering](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-prometheus-integration) – uppdaterad information relaterad till beteende och konfiguration av mått kassation med Azure Red Hat OpenShift jämfört med andra typer av Kubernetes-kluster.
- [Uppdatera Azure Monitor för behållare för mått](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-update-metrics) – uppdaterad information relaterad till beteende och konfiguration av mått kassation med Azure Red Hat OpenShift jämfört med andra typer av Kubernetes-kluster.


#### <a name="azure-monitor-for-vms"></a>Azure Monitor för virtuella datorer
- [Azure Monitor for VMS (ga) vanliga frågor](insights/vminsights-ga-release-faq.md) – ytterligare information om att uppgradera arbets ytan och agenter till den nya versionen.

#### <a name="office-365"></a>Office 365
- [Office 365-hanterings lösning i Azure](insights/solution-office-365.md) – ytterligare information och vanliga frågor och svar om migrering till Office 365-lösning i Azure Sentinel. Avsnittet har tagits bort.



### <a name="logs"></a>Loggar
- [Hantera Log Analytics arbets ytor i Azure Monitor](platform/manage-access.md) – uppdateringar av inte åtgärder.
- [Hantera användning och kostnader för Azure Monitor loggar](platform/manage-cost-storage.md) – du har lagt till klargöranden vid beräkning av data volym i avsnittet prissättnings modell.
- [Använd Azure Resource Manager mallar för att skapa och konfigurera en mall för Log Analytics-arbetsytan](platform/template-workspace-configuration.md) – uppdaterad med nya pris nivåer.


### <a name="platform-logs"></a>Plattformsloggar
- [Samla in Azure aktivitets logg med diagnostikinställningar – Azure Monitor](platform/diagnostic-settings-legacy.md) – ytterligare information om ändrade egenskaper.
- [Exportera Azure aktivitets loggen](platform/activity-log-export.md) – uppdaterad för ändringar i gränssnittet. 





## <a name="december-2019"></a>December 2019

### <a name="agents"></a>Agenter
- [Anslut Linux-datorer till Azure Monitor](platform/agent-linux.md) -ny artikel.

### <a name="alerts"></a>Aviseringar
- [Skapa en mått varning med en Resource Manager-mall](platform/alerts-metric-create-templates.md) – exempel på anpassat mått har lagts till.
- [Skapa aviseringar med dynamiska tröskelvärden i avsnittet Azure Monitor](platform/alerts-dynamic-thresholds.md) -Added för tolkning av dynamiska tröskel diagram.
- [Översikt över aviseringar och meddelande övervakning i Azure](platform/alerts-overview.md) -uppdaterad resurs diagram fråga.
- [Resurser som stöds för mått varningar i Azure Monitor](platform/alerts-metric-near-real-time.md) – uppdatering av mått och dimensioner som stöds.
- [Växla från äldre Log Analytics aviserings-API till nya](platform/alerts-log-api-switch.md) aviseringar i Azure-aviseringar – Lägg till anteckning om ändrat aviserings namn.
- [Förstå hur mått varningar fungerar i Azure Monitor.](platform/alerts-metric-overview.md) -Resurs typer som stöds för övervakning i skala har lagts till.

### <a name="application-insights"></a>Application Insights
- [Application Insights för Worker service-appar (icke-http-appar)](app/worker-service.md) – en standard C# loggnings nivå har lagts till i koden. Uppdaterad paket referens version.
- [ApplicationInsights. config-referens – Azure](app/configuration-with-applicationinsights-config.md) -uppdaterad exempel kod.
- [Automatisera Azure Application Insights med PowerShell](app/powershell.md) – uppdatera till Resource Manager-mall.
- [Azure Monitor Application Insights NuGet-paket](app/nuget.md) – uppdaterade paket versioner.
- [Skapa en ny Azure Application Insights-resurs](app/create-new-resource.md) -anteckning som har lagts till i globalt unikt namn.
- [Diagnostisera med Live Metrics Stream-Azure Application Insights](app/live-stream.md) – uppdaterat krav för ASP.net Core SDK-version.
- [Händelse räknare i Application Insights](app/eventcounters.md) -uppdaterad kategori och tabell till customMetrics.
- [Utforska Java trace-loggar i Azure Application Insights](app/java-trace-logs.md) -tillagd konfiguration för loggnings tröskeln för Java-agent.
- [IP-adresser som används av Application Insights och Log Analytics](app/ip-addresses.md) uppdaterade IP-adresser för Live Metrics Stream.
- [Övervaka Azure App Services-prestanda](app/azure-web-apps.md) – stöd har lagts till för ASP.net Core 3,0. 
- [Övervaka python-program med Azure Monitor (för hands version)](app/opencensus-python.md) – du har lagt till klargöranden för python-schema mappning i openpool till Azure. Övervaka schema
- [Viktig information för Azure Application insikter](app/release-notes.md) – tillagda anteckningar för äldre versioner.
- [Telemetri kanaler i Azure Application insikter](app/telemetry-channels.md) – uppdaterad varaktighet för förkastade data under den utökade tids perioden för förlorad anslutning.
- [Telemetri-sampling i Azure Application Insights](app/sampling.md) – korrigerat kodfragment för anpassade TelemetryInitializer.
- [Felsök Application Insights i en Java-webbprojekt](app/java-troubleshoot.md) -borttagen instruktion om att inte stödja beroende insamling i JDK 9.

### <a name="insights-and-solutions"></a>Insikter och lösningar
- [Azure Monitor för behållare vanliga frågor och svar](insights/container-insights-faq.md) – tillagd fråga i fälten bild och namn.
- [Azure SQL-analys lösning i Azure Monitor](insights/azure-sql.md) -uppdaterad databas väntar på stöd för hanterade instanser.
- [Konfigurera Azure Monitor för behållare agent data insamling](insights/container-insights-agent-config.md) – ytterligare inställning för enrich_container_logs.
- [Konfigurera hybrid Kubernetes-kluster med Azure Monitor för behållare som](insights/container-insights-hybrid-setup.md) har lagts till fel söknings avsnittet.
- [Övervaka Active Directory replikeringsstatus med Azure Monitor](insights/ad-replication-status.md) -.NET Framework nödvändiga uppdateringar.
- [Övervakare av nätverksprestanda lösning i Azure](insights/network-performance-monitor.md) – tillagda regioner som stöds.
- [Optimera din Active Directory miljö med Azure Monitor](insights/ad-assessment.md) -.NET Framework nödvändiga uppdateringar.
- [Optimera din SQL Server miljö med Azure Monitor](insights/sql-assessment.md) -.NET Framework nödvändiga uppdateringar.
- [Optimera din System Center Operations Manager-miljö med Azure Log Analytics](insights/scom-assessment.md) -.NET Framework nödvändiga uppdateringar.
- [Anslutningar som stöds med anslutningsprogram för hantering av IT-tjänster (ITSM) i Azure Log Analytics](platform/itsmc-connections.md) – tillagt New York till det nödvändiga klient-ID: t och klient hemligheten.

### <a name="logs"></a>Loggar
- [Ta bort och Återställ Azure-Log Analytics arbets yta](platform/delete-workspace.md) som har lagts till med PowerShell-metoden.
- Genom [att utforma dina Azure Monitor loggar distribution](platform/design-logs-deployment.md) efter inmatnings takt för en arbets yta ökade.

### <a name="metrics"></a>Mått
- [Azure Monitor plattforms mått som exporteras via diagnostikinställningar](platform/metrics-supported-export-diagnostic-settings.md) – ny artikel.

### <a name="platform-logs"></a>Plattformsloggar
Flera artiklar har uppdaterats som en del av omstrukturering av innehåll för plattforms loggar baserat på en ny funktion för att konfigurera aktivitets loggen med hjälp av diagnostikinställningar.

- [Arkivera Azures resurs loggar till lagrings kontot](platform/resource-logs-collect-storage.md)
- [Händelse schema för Azure aktivitets logg](platform/activity-log-schema.md)
- [Azure Monitor tjänst gränser](service-limits.md)
- [Samla in och analysera Azure aktivitets loggar i Log Analytics arbets yta](platform/activity-log-collect.md)
- [Samla in Azure aktivitets logg med diagnostikinställningar (för hands version) – Azure Monitor](platform/diagnostic-settings-legacy.md)
- [Samla in Azures aktivitets loggar i en Log Analytics arbets yta över Azure-klienter](platform/activity-log-collect-tenants.md)
- [Samla in Azures resurs loggar i Log Analytics arbets yta](platform/resource-logs-collect-workspace.md)
- [Skapa diagnostisk inställning i Azure med Resource Manager-mall](platform/diagnostic-settings-template.md)
- [Skapa en diagnostisk inställning för insamling av loggar och mått i Azure](platform/diagnostic-settings.md)
- [Exportera Azure aktivitets loggen](platform/activity-log-export.md)
- [Översikt över Azures plattforms loggar](platform/platform-logs-overview.md)
- [Strömma Azure-övervaknings data till händelsehubben](platform/stream-monitoring-data-event-hubs.md)
- [Strömma Azure-plattformar loggar till en händelsehubben](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>Snabbstarter och självstudier

- [Skapa ett mått diagram i Azure Monitor](learn/tutorial-metrics-explorer.md) nya artikeln.
- [Samla in resurs loggar från en Azure-resurs och analysera med Azure Monitor](learn/tutorial-resource-logs.md) nya artikeln.
- [Övervaka en Azure-resurs med Azure Monitor](learn/quick-monitor-azure-resource.md) ny artikel.
   
## <a name="next-steps"></a>Nästa steg

- Om du vill bidra till Azure Monitor-dokumentationen kan du läsa [guiden dokument bidrags givare](https://docs.microsoft.com/contribute/).