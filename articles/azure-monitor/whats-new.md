---
title: Nyheter i Azure Monitor-dokumentationen
description: Viktiga uppdateringar av Azure Monitor-dokumentationen uppdateras varje månad.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 07/08/2020
ms.openlocfilehash: 4d061e7a105fc73f7f44c8336df82ff363ee6ded
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86203750"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Vad är nytt i Azure Monitor-dokumentationen?

Den här artikeln innehåller listor Azure Monitor artiklar som antingen är nya eller som har uppdaterats markant. Den kommer att uppdateras den första veckan i varje månad för att inkludera artikel uppdateringar från föregående månad.

## <a name="june-2020"></a>Juni 2020

### <a name="general"></a>Allmänt
- [Distribuera Azure Monitor](platform/deploy-scale.md) – ny artikel.
- [Azure Monitor billingtype-egenskap med kundhanterad nyckel](platform/customer-managed-keys.md) uppdaterad. PowerShell-kommandon har lagts till.

### <a name="agents"></a>Agenter
- [Översikt över Log Analytics agent](platform/log-analytics-agent.md) – ytterligare python 2-krav har lagts till.

### <a name="alerts"></a>Aviseringar
- [Så här uppdaterar du aviserings regler eller åtgärds regler när deras mål resurs flyttas till en annan Azure-region](platform/alerts-resource-move.md) – ny artikel.
- [Fel sökning av Azure Metric-aviseringar](platform/alerts-troubleshoot-metric.md) – ny artikel.
- [Felsöka logg aviseringar i Azure Monitor](platform/alerts-troubleshoot-metric.md) – ny artikel.
  
### <a name="application-insights"></a>Application Insights
- [Azure Application Insights för JavaScript-webbappar](app/javascript.md) – uppdatera till JavaScript SDK-avsnittet. Det uppdaterade kodfragmentet för att rapportera inläsnings problem.
- [Konfigurera BYOS (ta med din egen lagring) för profiler & Snapshot debugger](app/profiler-bring-your-own-storage.md) – ny artikel.
- [Spårning av inkommande begär anden i Azure Application insikter med Openräkningar python](app/opencensus-python-request.md) -uppdaterad loggning och konfiguration för openräkning.
- [Övervaka en live ASP.NET-webbapp med Azure Application Insights](app/monitor-performance-live-website-now.md) – uppdaterat decprecation-datum för statusövervakare v1.
- [Övervaka Node.js tjänster med Azure Application insikter](app/nodejs.md) – flera uppdateringar, inklusive migrering från tidigare-versioner och SDK-konfiguration
- [Övervaka python-program med Azure Monitor (för hands version)](app/opencensus-python.md) – tillagt avsnitt om konfiguration av Azure Monitor exportörer.
- [Övervaka dina appar utan kod ändringar – Auto-Instrumentation för Azure Monitor Application Insights](app/codeless-overview.md) nya artikeln.
- [FELSÖKA SDK-inläsnings fel för JavaScript-webbprogram](app/javascript-sdk-load-failure.md) – ny artikel.

### <a name="containers"></a>Containrar
- [Så här slutar du övervaka ditt hybrid Kubernetes-kluster](insights/container-insights-optout-hybrid.md) för Arc-aktiverad Kubernetes.
- [Konfigurera Azure Arc-aktiverat Kubernetes-kluster med Azure Monitor för behållare](insights/container-insights-enable-arc-enabled-clusters.md) – ny artikel.
- [Konfigurera Azure Red Hat OpenShift v4. x med Azure Monitor för behållare](insights/container-insights-azure-redhat4-setup.md) – uppdaterade krav.
- [Konfigurera Azure Monitor för behållare Live data (förhands granskning)](insights/container-insights-livedata-setup.md) -borttagen anmärkning om funktionen är inte tillgänglig i Azure amerikanska myndigheter.

### <a name="insights"></a>Insikter
- [Vanliga frågor och svar – övervakare av nätverksprestanda lösning i Azure](insights/network-performance-monitor-faq.md) – vanliga frågor och svar om ExpressRoute-övervakaren.

### <a name="logs"></a>Loggar
- [Ta bort och Återställ Azure-Log Analytics arbets ytan](platform/delete-workspace.md) – extra PowerShell-kommando. Uppdaterad fel sökning.
- [Hantera Log Analytics arbets ytor i Azure Monitor](platform/manage-access.md) -lagt till exempel för tabeller som inte tillåts i RBAC-avsnittet.
- [Hantera användning och kostnader för Azure Monitor loggar](platform/manage-cost-storage.md) – ytterligare information om beräkning av data storlek. Uppdaterade konfiguration av data volym aviseringar. Information om säkerhets data som samlas in av Azure Sentinel. Klargörande om data kap.
- [Använd Azure Monitor loggar med Azure Logic Apps och Energiautomatiserade](platform/logicapp-flow-connector.md) conector-gränser.

### <a name="metrics"></a>Mått
- [Azure Monitor mått som stöds av resurs typ](platform/metrics-supported.md) – uppdaterade SQL Server mått.


### <a name="platform-logs"></a>Plattformsloggar

- [Exempel på Resource Manager-mallar för diagnostikinställningar](samples/resource-manager-diagnostic-settings.md) – korrigering för diagnostisk inställning för aktivitets loggen.
- [Skicka Azure aktivitets logg till Log Analytics arbets ytan med Azure Portal](learn/quick-collect-activity-log-portal.md) – ny artikel.
- [Skicka Azure aktivitets logg till Log Analytics arbets yta med Azure Resource Manager mall](learn/quick-collect-activity-log-arm.md) – ny artikel.

#### <a name="new-and-updated-articles-from-restructure-and-consolidation-of-platform-log-content"></a>Nya och uppdaterade artiklar från omstrukturera och konsolidering av plattforms logg innehåll
- [Arkivera Azures resurs loggar till lagrings kontot](platform/resource-logs-collect-storage.md)
- [Händelse schema för Azure aktivitets logg](platform/activity-log-schema.md)
- [Azure aktivitets logg](platform/activity-log.md)
- [Azure Monitor CLI-exempel](samples/cli-samples.md)
- [Azure Monitor PowerShell-exempel](samples/powershell-samples.md)
- [Genom gång av Azure Monitoring REST API](platform/rest-api-walkthrough.md)
- [Tjänster och scheman som stöds av Azure-resurs loggar](platform/diagnostic-logs-schema.md)
- [Azure resurs loggar](platform/resource-logs.md)
- [Samla in och analysera Azure aktivitets logg i Azure Monitor](platform/activity-log-collect.md)
- [Samla in Azures resurs loggar i Log Analytics arbets yta](platform/resource-logs-collect-workspace.md)
- [Skapa diagnostikinställningar för att skicka plattforms loggar och mått till olika destinationer](platform/diagnostic-settings.md)
- [Exportera Azure aktivitets loggen](platform/activity-log-export.md)
- [Översikt över Azures plattforms loggar](platform/platform-logs-overview.md)
- [Strömma Azure-plattformar loggar till en händelsehubben](platform/resource-logs-stream-event-hubs.md)
- [Visa Azure aktivitets logg händelser i Azure Monitor](platform/activity-log-view.md)

### <a name="virtual-machines"></a>Virtuella datorer
- [Aktivera Azure Monitor for VMS i Azure Portal](insights/vminsights-enable-single-vm.md) – uppdaterat för att inkludera Azure Arc.
- [Aktivera Azure Monitor for VMS översikt](insights/vminsights-enable-overview.md) – uppdaterat med Azure Arc.
- [Vad är Azure Monitor for VMs?](insights/vminsights-overview.md) – Uppdaterad för att inkludera Azure-båge.


### <a name="visualizations"></a>Visualiseringar
- [Azure Monitor arbets böcker-data källor](platform/workbooks-data-sources.md) – lägga till aviseringar och anpassade slut punkts avsnitt.
- [Felsöka Azure Monitor arbetsböcker-baserade insikter](insights/troubleshoot-workbooks.md) – ny artikel.
- [Uppgradera dina visualiseringar av Log Analytics-instrumentpanel](log-query/dashboard-upgrade.md) – ny artikel.



## <a name="may-2020"></a>Maj 2020

### <a name="general"></a>Allmänt

- [Azure Monitor FAQ](faq.md) – tillagt avsnitt för mått.
- [Azure Monitor kundhanterad nyckel](platform/customer-managed-keys.md) – flera ändringar som är i förberedelse för allmän tillgänglighet.
- [Inbyggda princip definitioner för Azure Monitor](samples/policy-samples.md) nya artikeln.
- [Kundägda lagrings konton för logg](platform/private-storage.md) inmatning – ny artikel.
- [Hantera användning och kostnader för Azure Monitor loggar](platform/manage-cost-storage.md) – proportionell fakturering har lagts till i kluster.
- [Använd Azures privata länk för att på ett säkert sätt ansluta nätverk till Azure Monitor](platform/private-link-security.md) nya artikeln.


#### <a name="new-resource-manager-template-samples"></a>Nya mall exempel för Resource Manager 
- [Exempel på Resource Manager-mallar för Azure Monitor](samples/resource-manager-samples.md)
- [Exempel på Resource Manager-mallar för åtgärds grupper](samples/resource-manager-action-groups.md)
- [Exempel på Resource Manager-mallar för agenter](samples/resource-manager-agent.md)
- [Exempel på Resource Manager-mallar för Azure Monitor för behållare](samples/resource-manager-container-insights.md)
- [Exempel på Resource Manager-mallar för Azure Monitor for VMs](samples/resource-manager-vminsights.md)
- [Exempel på Resource Manager-mallar för diagnostikinställningar](samples/resource-manager-diagnostic-settings.md)
- [Exempel på Resource Manager-mallar för Log Analytics arbets ytor](samples/resource-manager-workspace.md)
- [Exempel på Resource Manager-mallar för logg frågor](samples/resource-manager-log-queries.md)
- [Exempel på Resource Manager-mallar för aviserings regler för logg frågor](samples/resource-manager-alerts-log.md)
- [Exempel på Resource Manager-mallar för mått aviserings regler](samples/resource-manager-alerts-metric.md)
- [Exempel på Resource Manager-mallar för arbets böcker](samples/resource-manager-workbooks.md)

### <a name="agents"></a>Agenter
- [Installera och konfigurera Windows Azure Diagnostics Extension (wad)](platform/diagnostics-extension-windows-install.md) – ytterligare information om hur du konfigurerar diagnostik.
- [Översikt över Log Analytics agent](platform/log-analytics-agent.md) – stödda Linux-versioner som stöds.

### <a name="application-insights"></a>Application Insights

- [Övervaka program som körs på Azure Functions med Application Insights-Azure Monitor](app/monitor-functions.md) – ny artikel.
- [Övervaka Node.js tjänster med Azure Application Insights](app/nodejs.md) – allmänna uppdateringar inklusive nytt avsnitt om migrering från tidigare versioner.
- [IP-adresser som används av Application Insights och Log Analytics](app/ip-addresses.md) -TILLAGDa IP-adresser för Webhooks och amerikanska myndigheter.
- [Övervaka program på Azure Kubernetes service (AKS) med Application Insights-Azure Monitor](app/kubernetes-codeless.md) – ny artikel.
- [Fel sökning av inga data-Application Insights för .net](app/asp-net-troubleshoot-no-data.md) -tillagt avsnitt om insamling av loggar med dotNet-trace.
- [Använd program ändrings analys i Azure Monitor för att hitta problem med webb program](app/change-analysis.md) – flera uppdateringar i funktionen för ändrings analys.

#### <a name="new-and-updated-articles-for-preview-of-workspace-based-applications"></a>Nya och uppdaterade artiklar för för hands versionen av arbets ytans baserade program
- [Azure Monitor Application Insights arbets yta-baserat resurs schema](app/apm-tables.md)
- [Skapa en ny Azure Monitor Application Insights arbets ytans baserade resurs](app/create-workspace-resource.md)
- [app ()-uttryck i Azure Monitor logg frågor](log-query/app-expression.md)
- [Logg frågans omfång i Azure Monitor Log Analytics](log-query/scope.md)
- [Fråga över resurser med Azure Monitor](log-query/cross-workspace-query.md)
- [Standard egenskaper i Azure Monitor logg poster](platform/log-standard-properties.md)
- [Struktur för Azure Monitor loggar](log-query/logs-structure.md)





### <a name="containers"></a>Containrar
- [Så här aktiverar du Azure Monitor för behållare](insights/container-insights-onboard.md) – uppdaterad brand Väggs konfigurations tabell.
- [Uppdatera Azure Monitor för behållare för mått](insights/container-insights-update-metrics.md) – uppdatering för att använda hanterade identiteter för att samla in mått.
- [Övervaknings kostnad för Azure Monitor för behållare](insights/container-insights-cost.md) – ny artikel.
- [Konfigurera Azure Monitor för behållare Live-data (för hands version)](insights/container-insights-livedata-setup.md) – stöd för den nya kluster roll bindningen.

### <a name="insights"></a>Insikter
- [Azure Monitor för Azure cache för Redis (för hands version)](insights/redis-cache-insights-overview.md) – ny artikel.
- [Övervaka Key Vault med Azure Monitor för Key Vault (för hands version)](insights/key-vaults-insights-overview.md) – ny artikel.

### <a name="logs"></a>Loggar
- [Skapa & konfigurera Log Analytics med PowerShell](platform/powershell-workspace-configuration.md) – fel söknings avsnittet.
- [Skapa en Log Analytics arbets yta i](learn/quick-create-workspace.md) avsnittet fel sökning Azure Portal-tillägg.
- [Skapa en Log Analytics arbets yta med Azure CLI](learn/quick-create-workspace-cli.md) – fel söknings avsnittet.
- [Ta bort och Återställ Azure Log Analytics-arbetsytan](platform/delete-workspace.md) – uppdaterad information om hur du återställer en borttagen arbets yta.
- [Funktioner i Azure Monitor logg frågor](log-query/functions.md) -borttagna kommentarer om funktioner som inte innehåller andra funktioner.
- [Struktur för Azure Monitor loggar](log-query/logs-structure.md) – för tydliga egenskaps beskrivningar för Application Insights tabell.
- [Använd Azure Monitor loggar med avsnittet begränsningar för Azure Logic Apps och Energis par som](platform/logicapp-flow-connector.md) har lagts till automatiskt.
- [Använd PowerShell för att skapa och konfigurera en Log Analytics arbets yta](platform/powershell-workspace-configuration.md) som har lagts till fel söknings avsnittet.


### <a name="metrics"></a>Mått
- [Azure Monitor mått som stöds av resurs typ](platform/metrics-supported.md) -fördefinierade gäst mått och mått routning. 

### <a name="solutions"></a>Lösningar
- [Optimera din Active Directory-miljö med Azure Monitor](insights/ad-assessment.md) -lagt till versioner som stöds av Windows Server 2019.
- [Optimera din SQL Server-miljö med Azure Monitor](insights/sql-assessment.md) – till stödda versioner av SQL Server.


### <a name="virtual-machines"></a>Virtuella datorer
- [Aktivera Azure Monitor for VMS översikt](insights/vminsights-enable-overview.md) – läggs till i versioner av Ubuntu Server som stöds. Regioner som stöds för Log Analytics-arbetsyta.
- [Så här diagramerar du prestanda med Azure Monitor for VMS](insights/vminsights-performance.md) -tillagda begränsningar för mått som inte är tillgängliga.

### <a name="visualizations"></a>Visualiseringar
- [Azure Monitor arbets böcker och Azure Resource Manager mallar](platform/workbooks-automate.md) – har lagt till Resource Manager-uppdatering för distribution av en mall för arbets böcker.
- [Azure Monitor arbets böcker grupper](platform/workbooks-groups.md) – ny artikel.
- [Azure Monitor arbets böcker – TRANSFORMERA JSON-data med JSONPath](platform/workbooks-jsonpath.md) -ny artikel.


## <a name="april-2020"></a>April 2020

### <a name="general"></a>Allmänt

- [Azure Monitor kundhanterat nyckel](platform/customer-managed-keys.md) tillagt avsnitt om asynkrona åtgärder
- [Hantera Log Analytics arbets ytor i Azure Monitor](platform/manage-access.md) uppdaterade anpassade loggar.

### <a name="alerts"></a>Aviseringar

- [Åtgärds regler för Azure Monitor aviseringar](platform/alerts-action-rules.md) – tillagd video.
- [Översikt över aviseringar och meddelande övervakning i Azure](platform/alerts-overview.md) -tillagd video.

### <a name="application-insights"></a>Application Insights

- [Program karta i Azure Application insikter](app/app-map.md) – moln roll namn konfiguration för Java-agent har lagts till.
- [Referens för Azure Application Insights .net-Agent-API: t](app/status-monitor-v2-api-reference.md) .
- [IP-adresser som används av Application Insights och Log Analytics](app/ip-addresses.md) uppdaterade IP-adresser för App Insights-och Log Analytics-API: er, åtgärds grupp Webhooks, Azure amerikanska myndigheter.
- [Övervaka Java-program var som helst](app/java-standalone-config.md) – ny artikel.
- [Övervaka Java-program i valfri miljö](app/java-in-process-agent.md) – ny artikel.
- [Övervaka Java-program som körs i en miljö](app/java-standalone-arguments.md) – ny artikel.
- [Övervaka Java-program som körs lokalt](app/java-on-premises.md) – ny artikel.
- [Ta bort Application Insights i Visual Studio](app/remove-application-insights.md) – ny artikel.
- [Telemetri-sampling i Azure Application Insights](app/sampling.md) -Fix i fast priss exempel i python.

### <a name="containers"></a>Containrar

- [Konfigurera Azure Red Hat OpenShift v4. x med Azure Monitor för behållare](insights/container-insights-azure-redhat4-setup.md) – ny artikel.
- [Åtgärda ServiceNow-synkroniseringsproblem manuellt](platform/itsmc-resync-servicenow.md) – ny artikel.
- [Så här stoppar du övervakning av v4-kluster för Azure och Red Hat OpenShift](insights/container-insights-optout-openshift-v4.md) – ny artikel.
- [Så här stoppar du övervakning av Azure Red Hat OpenShift v3-kluster](insights/container-insights-optout-openshift-v3.md) – ny artikel.
- [Så här stoppar du övervakning av ditt hybrid Kubernetes-kluster](insights/container-insights-optout-hybrid.md) – ny artikel.

### <a name="insights"></a>Insikter

- [Övervaka Azure Key Vaults med Azure Monitor för nyckel valv (för hands version)](insights/key-vault-insights-overview.md) – ny artikel.

### <a name="logs"></a>Loggar

- [Azure Monitor tjänst gränser](service-limits.md) – begränsning av användar frågor har lagts till.
- [Hantera användning och kostnader för Azure Monitor loggar](platform/manage-cost-storage.md) – tillagd fakturering för loggar kluster. Lade till Kusto-fråga för att möjliggöra för kunder med äldre pris nivå per nod att avgöra om de ska flyttas till en per GB-eller kapacitets reservations nivå.

### <a name="metrics"></a>Mått

- [Avancerade funktioner i Azure Metrics Explorer](platform/metrics-charts.md) -tillägg av agg regerings avsnitt.

### <a name="workbooks"></a>Arbetsböcker

- [Azure Monitor arbets böcker och Azure Resource Manager mallar](platform/workbooks-automate.md) – har lagt till Resource Manager-mallen för att distribuera en mall för arbets böcker.

## <a name="march-2020"></a>Mars 2020

### <a name="general"></a>Allmänt

- [Azure Monitor översikt](overview.md) – lägg till Azure Monitor översikts video.
- [Azure Monitor kundhanterad nyckel konfiguration](platform/customer-managed-keys.md) – allmänna uppdateringar.
- [Azure Monitor data referens](/azure/azure-monitor/reference/) – ny plats.

### <a name="alerts"></a>Aviseringar

- [Skapa, Visa och hantera aktivitets logg aviseringar i Azure Monitor](platform/alerts-activity-log.md) – ytterligare förklaring till Resource Manager-mallen.
- [Förstå hur mått varningar fungerar i Azure Monitor.](platform/alerts-metric-overview.md) – Uppdaterat för myndighets support.
- [Felsöka Azure Monitor aviseringar och meddelanden](platform/alerts-troubleshoot.md) – ny artikel.

### <a name="application-insights"></a>Application Insights

- [Automatisera Azure Application insikter med PowerShell](app/powershell.md) -ARMClient exempel.
- [Kontinuerlig export av telemetri från Application Insights](app/export-telemetry.md) – Lägg till tabell med information om export struktur.
- [Aktivera Snapshot debugger för .net-appar i Azure App Service](app/snapshot-debugger-appservice.md) -tillagd Resource Manager-mall exempel.
- [Hantera användning och kostnader för Azure Application insikter](app/pricing.md) – information om data Cap-aviseringar har lagts till.
- [Övervaka python-program med Azure Monitor (för hands version)](app/opencensus-python.md) – tillagda standard mått.
- [Käll kart stöd för JavaScript-program – Azure Monitor Application Insights](app/source-map-support.md) – ny artikel.

### <a name="containers"></a>Containrar

- [Azure Monitor vanliga frågor och svar](faq.md) – uppdatering för Azure Monitor för behållare.
- [Konfigurera GPU-övervakning med Azure Monitor för behållare](insights/container-insights-gpu-monitoring.md) – ny artikel.

### <a name="insights"></a>Insikter

- [Office 365-hanterings lösning i Azure](insights/solution-office-365.md) – uppdaterat utfasnings datum.

### <a name="logs"></a>Loggar

- [Optimera logg frågor i Azure Monitor](log-query/query-optimization.md) -extra processor villkor för XML-och JSON-parsning.
- [Ta bort och återställa Azure Log Analytics-arbetsytan](platform/delete-workspace.md) – fel sökning har lagts till.
- [Använd Azure Monitor loggar med Azure Logic Apps och energi automatisering](platform/logicapp-flow-connector.md) – uppdaterat för nya Azure Monitor-anslutningar.

### <a name="metrics"></a>Mått

- [Föråldrade disk värden i den Azure Portal](platform/portal-disk-metrics-deprecation.md) nya artikeln.
- [Självstudie – Skapa ett mått diagram i Azure Monitor](learn/tutorial-metrics-explorer.md) -tillagd video.

### <a name="platform-logs"></a>Plattformsloggar

- [Samla in och analysera Azure aktivitets logg i Azure Monitor](platform/activity-log-collect.md) – Skriv om för att få en bättre förklaring av aktivitets loggen med diagnostiska inställningar.

### <a name="virtual-machines"></a>Virtuella datorer

- [Övervaka virtuella Azure-datorer med Azure Monitor](insights/monitor-vm-azure.md) ny artikel.
- [Snabb start: övervaka virtuella Azure-datorer med Azure Monitor](learn/quick-monitor-azure-vm.md) -uppdaterat för att lägga till Azure Monitor for VMS.
- [Aviseringar från Azure Monitor for VMS](insights/vminsights-alerts.md) – ny artikel.
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
- [Lagra och Visa diagnostikdata i Azure Storage](../cloud-services/diagnostics-extension-to-storage.md) -helt omskrivna och uppdaterade.
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
- [ApplicationInsights.config referens – Azure](app/configuration-with-applicationinsights-config.md) -uppdaterad exempel kod.
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

- Om du vill bidra till Azure Monitor-dokumentationen kan du läsa [guiden dokument bidrags givare](/contribute/).
