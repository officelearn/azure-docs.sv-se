---
title: Nyheter i Azure Monitor-dokumentationen
description: Viktiga uppdateringar av Azure Monitor-dokumentationen uppdateras varje månad.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 03/05/2020
ms.openlocfilehash: a2e6d2a459a6713aa9372496fc3a933c0a886ed9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80802612"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Vad är nytt i Azure Monitor-dokumentationen?
Den här artikeln innehåller listor Azure Monitor artiklar som antingen är nya eller som har uppdaterats markant. Den kommer att uppdateras den första veckan i varje månad för att inkludera artikel uppdateringar från föregående månad.

## <a name="march-2020"></a>Mars 2020

### <a name="general"></a>Allmänt
- [Azure Monitor översikt](overview.md) – lägg till Azure Monitor översikts video.
- [Azure Monitor kundhanterad nyckel konfiguration](platform/customer-managed-keys.md) – allmänna uppdateringar.
- [Azure Monitor data referens](/azure/azure-monitor/reference) – ny plats.

### <a name="alerts"></a>Aviseringar

- [Skapa, Visa och hantera aktivitets logg aviseringar i Azure Monitor](platform/alerts-activity-log.md) – ytterligare förklaring till Resource Manager-mallen.
- [Förstå hur mått varningar fungerar i Azure Monitor.](platform/alerts-metric-overview.md) – Uppdaterat för myndighets support.
- [Felsöka Azure Monitor aviseringar och aviseringar](platform/alerts-troubleshoot.md) – ny artikel

### <a name="application-insights"></a>Application Insights
- [Automatisera Azure Application insikter med PowerShell](app/powershell.md) -ARMClient exempel.
- [Kontinuerlig export av telemetri från Application Insights](app/export-telemetry.md) – Lägg till tabell med information om export struktur.
- [Aktivera Snapshot debugger för .net-appar i Azure App Service](app/snapshot-debugger-appservice.md) -tillagd Resource Manager-mall exempel.
- [Hantera användning och kostnader för Azure Application insikter](app/pricing.md) – information om data Cap-aviseringar har lagts till.
- [Övervaka python-program med Azure Monitor (för hands version)](app/opencensus-python.md) – tillagda standard mått.
- [Käll kart stöd för JavaScript-program – Azure Monitor Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/source-map-support) – ny artikel.


### <a name="containers"></a>Containrar
- [Azure Monitor vanliga frågor och svar](faq.md) – uppdatering för Azure Monitor för behållare.
- [Konfigurera GPU-övervakning med Azure Monitor för behållare](insights/container-insights-gpu-monitoring.md) – ny artikel

### <a name="insights"></a>Insikter
- [Office 365-hanterings lösning i Azure](insights/solution-office-365.md) – uppdaterat utfasnings datum.

### <a name="logs"></a>Loggar
- [Optimera logg frågor i Azure Monitor](log-query/query-optimization.md) -extra processor villkor för XML-och JSON-parsning.
- [Ta bort och återställa Azure Log Analytics-arbetsytan](platform/delete-workspace.md) – fel sökning har lagts till.
- [Använd Azure Monitor loggar med Azure Logic Apps och energi automatisering](platform/logicapp-flow-connector.md) – uppdaterat för nya Azure Monitor-anslutningar.

### <a name="metrics"></a>Mått
- [Föråldrade disk värden i den Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/portal-disk-metrics-deprecation) nya artikeln.
- [Självstudie – Skapa ett mått diagram i Azure Monitor](learn/tutorial-metrics-explorer.md) -tillagd video.

### <a name="platform-logs"></a>Plattformsloggar
- [Samla in och analysera Azure aktivitets logg i Azure Monitor](platform/activity-log-collect.md) – Skriv om för att få en bättre förklaring av aktivitets loggen med diagnostiska inställningar.

### <a name="virtual-machines"></a>Virtuella datorer
- [Övervaka virtuella Azure-datorer med Azure Monitor](insights/monitor-vm-azure.md) ny artikel.
- [Snabb start: övervaka virtuella Azure-datorer med Azure Monitor](learn/quick-monitor-azure-vm.md) -uppdaterat för att lägga till Azure Monitor for VMS.
- [Aviseringar från Azure Monitor for VMS](insights/vminsights-alerts.md) – ny artikel
- [Aktivera Azure Monitor for VMS översikt](insights/vminsights-enable-overview.md) – uppdaterade Länkar för agent hämtning.


Allmänna uppdateringar för allmän tillgänglighet för Azure Monitor for VMs
- [Vad är Azure Monitor for VMs?](insights/vminsights-overview.md)
- [Vanliga frågor och svar om Azure Monitor for VMs (GA)](insights/vminsights-ga-release-faq.md) 
- [Aktivera Azure Monitor for VMs med Azure Policy](insights/vminsights-enable-at-scale-policy.md) 
- [Så här diagramerar du prestanda med Azure Monitor for VMs](insights/vminsights-performance.md)
- [Så här frågar du efter loggar från Azure Monitor for VMs](insights/vminsights-log-search.md)
- [Visa program beroenden med Azure Monitor for VMs](insights/vminsights-maps.md) 


### <a name="visualizations"></a>Visualiseringar
- [Visualiserar data från Azure Monitor](visualizations.md) -uppdaterat för att notera planerad utfasning av View Designer.





## <a name="february-2020"></a>Februari 2020

### <a name="agents"></a>Agenter
Flera uppdateringar som en del av omskrivning av innehåll för diagnostik-tillägg.

- [Översikt över Azure Monitoring agents](platform/agents-overview.md) – strukturerade tabeller för att bättre klargöra unika funktioner i varje agent.
- [Översikt över Azure-diagnostik-tillägg](platform/diagnostics-extension-overview.md) – fullständig omskrivning.
- [Använd Blob Storage för IIS och tabell lagring för händelser i Azure Monitor](platform/diagnostics-extension-logs.md) – allmän omskrivning för uppdatering och tydlighet.
- [Installera och konfigurera Windows Azure Diagnostics Extension (wad)](platform/diagnostics-extension-windows-install.md) – ny artikel. 
- [Tilläggs schema för Windows-diagnostik](platform/diagnostics-extension-schema-windows.md) – omorganisera.
- [Skicka data från Windows Azure Diagnostics-tillägget till Azure Event Hubs](platform/diagnostics-extension-stream-event-hubs.md) -helt omskrivna och uppdaterade.
- [Lagra och Visa diagnostikdata i Azure Storage](/azure/cloud-services/diagnostics-extension-to-storage) -helt omskrivna och uppdaterade.
- [Log Analytics tillägg för virtuell dator för Windows](../virtual-machines/extensions/oms-windows.md) – bättre klargör relationen med Log Analytics-agenten.
- [Azure Monitor tillägg för virtuell dator för Linux](../virtual-machines/extensions/oms-linux.md) – bättre klargör relationen med Log Analytics-agenten.




### <a name="application-insights"></a>Application Insights
- [Anslutnings strängar i Azure Application Insights](app/sdk-connection-string.md) – ny artikel.

### <a name="insights-and-solutions"></a>Insikter och lösningar

#### <a name="azure-monitor-for-containers"></a>Azure Monitor för containrar
- [Integrera Azure Active Directory med Azure Kubernetes service](../aks/azure-ad-integration.md) – en anteckning om att skapa ett klient program som stöder RBAC-aktiverat kluster för att stödja Azure Monitor för behållare.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor för virtuella datorer
- [Azure Monitor for VMS (ga) vanliga frågor och svar](insights/vminsights-ga-release-faq.md) – ändra till hur prestanda data lagras.

#### <a name="office-365"></a>Office 365
- [Office 365-hanterings lösning i Azure](insights/solution-office-365.md) – uppdaterat utfasnings datum.


### <a name="logs"></a>Loggar
- [Optimera logg frågor i Azure Monitor](log-query/query-optimization.md) – ny artikel.
- [Hantera användning och kostnader för Azure Monitor loggar](platform/manage-cost-storage.md) – förbättrade exempel frågor som hjälper dig att förstå din användning.

### <a name="metrics"></a>Mått
- [Azure Monitor plattforms mått som exporteras via diagnostikinställningar](platform/metrics-supported-export-diagnostic-settings.md) – avsnittet har lagts till vid ändring av beteendet för null-värden och nollvärden.


### <a name="visualizations"></a>Visualiseringar
Flera nya artiklar för konverterings guiden för Visa designer till arbets böcker.

- [Azure Monitor Visa designer till arbets böcker över gångs guide](platform/view-designer-conversion-overview.md) – ny artikel.
- [Azure Monitor Visa designer till arbets böcker konverterings alternativ](platform/view-designer-conversion-options.md) -ny artikel.
- [Azure Monitor Visa designer till panel konverteringar](platform/view-designer-conversion-tiles.md) – ny artikel.
- [Azure Monitor Visa designer till arbets böcker konverterings Sammanfattning och Access](platform/view-designer-conversion-access.md) -ny artikel.
- [Azure Monitor Visa designer till arbets böcker konvertering av vanliga uppgifter](platform/view-designer-conversion-tasks.md) – ny artikel.
- [Azure Monitor Visa designer till arbets böcker konverterings exempel](platform/view-designer-conversion-examples.md) – ny artikel.




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
- [Konfigurera Azure Monitor för behållare agent data insamling](insights/container-insights-agent-config.md) -ytterligare information för att uppgradera agenten i Azure Red Hat OpenShift och ytterligare information för att särskilja metoderna för att uppgradera agenten.
- [Skapa prestanda varningar för Azure Monitor för behållare](insights/container-insights-alerts.md) – ändrad information och uppdaterade steg för att skapa en avisering om prestanda data som lagras i arbets ytan med hjälp av arbets ytans kontext aviseringar.
- [Kubernetes övervakning med Azure Monitor för behållare](insights/container-insights-analyze.md) – uppdaterad både översikts artikeln och analys artikeln om stöd för Windows Kubernetes-kluster.
- [Konfigurera Azure Red Hat OpenShift-kluster med Azure Monitor för behållare](insights/container-insights-azure-redhat-setup.md) – ytterligare information för att uppgradera agenten i Azure Red Hat OpenShift och ytterligare information som särskiljer metoderna för att uppgradera agenten.
- [Konfigurera hybrid Kubernetes-kluster med Azure Monitor för behållare](insights/container-insights-hybrid-setup.md) – uppdaterat för att avspegla ytterligare stöd för säker port: 10250 med Kubelet cAdvisor.
- [Så här hanterar du Azure Monitor för behållare agent](insights/container-insights-manage-agent.md) – uppdaterad information relaterad till beteende och konfiguration av mått kassation med Azure Red Hat OpenShift jämfört med andra typer av Kubernetes-kluster.
- [Konfigurera Azure Monitor för containers Prometheus-integrering](insights/container-insights-prometheus-integration.md) – uppdaterad information relaterad till beteende och konfiguration av mått kassation med Azure Red Hat OpenShift jämfört med andra typer av Kubernetes-kluster.
- [Uppdatera Azure Monitor för behållare för mått](insights/container-insights-update-metrics.md) – uppdaterad information relaterad till beteende och konfiguration av mått kassation med Azure Red Hat OpenShift jämfört med andra typer av Kubernetes-kluster.


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
- [Application Insights för Worker service-appar (icke-http-appar)](app/worker-service.md) – en standard loggnings nivå har lagts till i C#-koden. Uppdaterad paket referens version.
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