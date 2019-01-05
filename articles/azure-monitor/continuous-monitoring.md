---
title: Kontinuerlig övervakning med Azure Monitor | Microsoft Docs
description: Beskriver specifika steg för att använda Azure Monitor för att aktivera kontinuerlig övervakning i dina arbetsflöden.
author: bwren
manager: carmonm
editor: ''
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: bwren
ms.openlocfilehash: 8cbd09beffb8d86ab35e5fc1cf15c37b1bef9eb2
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050616"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Kontinuerlig övervakning med Azure Monitor

Kontinuerlig övervakning refererar till processen och teknik som krävs för att införliva övervakning över varje fas i din DevOps och livscykler för IT-åtgärder. Det hjälper dig att kontinuerligt Kontrollera hälsa, prestanda och tillförlitlighet för ditt program och infrastruktur som flyttas från utveckling till produktion. Kontinuerlig övervakning bygger på de principerna för kontinuerlig integrering och kontinuerlig distribution (CI/CD) som hjälper dig att utveckla och leverera programvara snabbare och mer tillförlitligt för att tillhandahålla kontinuerligt mervärde åt dina användare.

[Azure Monitor](overview.md) är den enhetliga lösningen för fjärrövervakning i Azure som ger fullständig observability mellan program och infrastruktur i molnet och lokalt. Det fungerar sömlöst med [Visual Studio och Visual Studio Code](https://visualstudio.microsoft.com/) under utveckling och testning och integreras med [Azure DevOps](/azure/devops/user-guide/index) för versionshantering och arbetsuppgiftshantering under distributionen och åtgärder. Det kan även integreras mellan ITSM och SIEM-verktyg för att hjälpa att spåra problem och händelser i dina befintliga IT-processer.

Den här artikeln beskriver specifika steg för att använda Azure Monitor för att aktivera kontinuerlig övervakning i dina arbetsflöden. Det innehåller länkar till annan dokumentation som innehåller information om hur du implementerar olika funktioner.


## <a name="enable-monitoring-for-all-your-applications"></a>Aktivera övervakning för alla dina program
För att få observability i hela miljön, måste du aktivera övervakning på alla dina webbprogram och tjänster. Detta kan du enkelt visualisera slutpunkt till slutpunkt-transaktioner och anslutningar för alla komponenter.

- [Azure DevOps-projekt](../devops-project/overview.md) ger dig ett förenklat sätt med befintlig kod och Git-lagringsplats, eller välja ett av exempelprogrammen för att skapa en pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) till Azure.
- [Kontinuerlig övervakning i din DevOps-releasepipeline](../azure-monitor/app/continuous-monitoring.md) gör det möjligt att gate eller ångra distributionen utifrån övervakningsdata.
- [Statusövervakaren](../azure-monitor/app/monitor-performance-live-website-now.md) gör att du kan instrumentera en live .NET-app i Windows med Azure Application Insights utan att behöva ändra eller omdistribuera din kod.
- Om du har åtkomst till koden för ditt program kan aktivera fullständig övervakning med [Programinsikter](../application-insights/app-insights-overview.md) genom att installera Azure Monitor Application Insights SDK för [.NET](../application-insights/quick-monitor-portal.md), [Java ](../application-insights/app-insights-java-quick-start.md), [Node.js](../application-insights/app-insights-nodejs-quick-start.md), eller [andra programmeringsspråk](../azure-monitor/app/platforms.md). På så sätt kan du ange anpassade händelser, mått eller sidvisningar som är relevanta för ditt program och din verksamhet.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>Aktivera övervakning för hela infrastrukturen
Program är bara lika tillförlitlig som den underliggande infrastrukturen. Med övervakning aktiverats över hela infrastrukturen hjälper dig att uppnå full observability och gör det lättare att identifiera eventuella grundorsaken när något inte fungerar. Azure Monitor kan du spåra status och prestanda för din hela hybridinfrastruktur, inklusive resurser som virtuella datorer, behållare, lagring och nätverk.

- Du får automatiskt [plattform mått, aktivitetsloggar och diagnostikloggar](platform/data-sources.md) från de flesta av dina Azure-resurser utan att någon konfiguration.
- Aktivera djupare övervakning för virtuella datorer med [Azure Monitor för virtuella datorer](insights/vminsights-overview.md).
-  Aktivera djupare övervakning för AKS-kluster med [Azure Monitor för behållare](insights/container-insights-overview.md).
- Lägg till [övervakningslösningar](insights/solutions-inventory.md) för olika program och tjänster i din miljö.


[Infrastruktur som kod](/devops/learn/what-is-infrastructure-as-code) är hantering av infrastrukturen i en beskrivande modell, med samma versionshantering som DevOps-team för källkoden. Den lägger till tillförlitlighet och skalbarhet för din miljö och gör att du kan använda liknande processer som används för att hantera dina program.

-  Använd [Resource Manager-mallar](platform/template-workspace-configuration.md) att aktivera övervakning och konfigurera aviseringar via ett stort antal resurser.
- Använd [Azure Policy](../governance/policy/overview.md) till tillämpar olika regler på resurserna. Detta säkerställer att resurserna följer företagets standarder och serviceavtal. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Kombinera Azure-resursgrupper
Ett typiskt program på Azure idag innehåller flera resurser som virtuella datorer och App Services eller mikrotjänster som finns på Cloud Services, AKS-kluster eller Service Fabric. De här programmen använda ofta beroenden som Event Hubs, lagring, SQL och Service Bus.

- Kombinera resurser inAzure resursgrupper för att få full insyn i alla dina resurser som utgör din olika program. [Azure Monitor för resursgrupper](../azure-monitor/insights/resource-group-insights.md) ger ett enkelt sätt att hålla koll på hälsotillstånd och prestanda för hela programmet för fullständig och kan gå nedåt i respektive komponenter för alla undersökningar eller felsökning.

## <a name="ensure-quality-through-continuous-deployment"></a>Se till att kvalitet via kontinuerlig distribution
Kontinuerlig integrering / kontinuerlig distribution kan du automatiskt vill integrera och distribuera kodändringar i ditt program baserat på resultatet av automatiserad testning. Det underlättar distributionen och säkerställer kvaliteten på alla ändringar innan de flyttar till produktion.


- Använd [Azure Pipelines](/azure/devops/pipelines) kan implementera kontinuerlig distribution och automatisera hela processen från kodbekräftelse till produktion baserat på dina CI/CD-tester.
- Använd [kvalitet Gates](/devops/pipelines/release/approvals/gates) att integrera övervakning i din före eller efter distributionen. Detta säkerställer att du uppfyller health/prestandarelaterade nyckeltal (KPI: er) som dina program flyttar från utveckling till produktion och eventuella skillnader i infrastruktursmiljö eller skalning inte negativt påverka din KPI: er.
- [Underhålla separata övervakning instanser](../azure-monitor/app/separate-resources.md) mellan dina olika distributionsmiljöer som utveckling, testning, Kanarieöarna och Prod. Detta säkerställer att insamlade data är relevanta för associerade program och infrastruktur. Om du behöver att korrelera data mellan miljöer kan du använda [flera resource diagrammen i Metrics Explorer](../azure-monitor/platform/metrics-charts.md) eller skapa [mellan resurser frågor i Log Analytics](log-query/cross-workspace-query.md).


## <a name="create-actionable-alerts-with-actions"></a>Skapa användbara aviseringar med åtgärder
En viktig aspekt av övervakning proaktivt meddela administratörer om eventuella aktuella och förväntade problem. 

- Skapa [aviseringar i Azure Monitor](../azure-monitor/platform/alerts-overview.md) baserat på loggar och mått för att identifiera förutsägbara fel tillstånd. Du bör ha ett mål för att göra alla aviseringar användbara vilket innebär att de motsvarar faktiska kritiska tillstånd och försöka minska falska positiva identifieringar. Använd [dynamiska tröskelvärden](platform/alerts-dynamic-thresholds.md) att automatiskt beräkna baslinjer på måttdata i stället för att definiera din egen statiska tröskelvärden. 
- Definiera åtgärder så att du använder det mest effektiva sättet att meddela dina administratörer. Tillgängliga [åtgärder för meddelande](platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) är SMS, e-postmeddelanden, push-meddelanden eller röstsamtal.
- Använd mer avancerade åtgärder för att [ansluta till ITSM-verktyg](platform/itsmc-overview.md) eller andra aviseringshantering system via [webhooks](platform/activity-log-alerts-webhook.md).
- Åtgärda situationer som identifierats i aviseringar samt med [Azure Automation-runbooks](../automation/automation-webhooks.md) eller [Logikappar](/connectors/custom-connectors/create-webhook-trigger) som kan startas från en avisering med webhookar. 
- Använd [autoskalning](../azure-monitor/learn/tutorial-autoscale-performance-schedule.md) att dynamiskt öka och minska dina beräkningsresurser utifrån insamlade mätvärdena.

## <a name="prepare-dashboards-and-workbooks"></a>Förbereda instrumentpaneler och arbetsböcker
Se till att din utveckling och drift har åtkomst till samma telemetri och verktyg kan användas för att visa mönster i hela miljön och minimera dina Mean Time att identifiera (MTTD) och tiden till återställning (MTTR).

- Förbereda [anpassade instrumentpaneler](../application-insights/app-insights-tutorial-dashboards.md) baserat på vanliga mått och loggar för olika roller i din organisation. Instrumentpaneler kan kombinera data från alla Azure-resurser.
- Förbereda [arbetsböcker](../azure-monitor/app/usage-workbooks.md) till lagrets delning mellan utveckling och drift. Dessa kunde förberedas som dynamiska rapporter med diagram med mätvärden och loggfrågor eller även följande felsökningsguider sammanställts av utvecklare som hjälper kundsupport eller åtgärder för att hantera grundläggande problem.

## <a name="continuously-optimize"></a>Optimera kontinuerligt
 Övervakning är en av de grundläggande delarna av populära Build-mått-Läs filosofin, som rekommenderar kontinuerligt spåra dina KPI: er och användarmått beteende och sedan försöker du att optimera dem via planering iterationer. Azure Monitor kan du samla in mått och loggar som är relevanta för din verksamhet och lägga till nya datapunkter i nästa distributionen efter behov.

- Använda verktygen i Application Insights till [spåra slutanvändarbeteendet och engagement](../application-insights/app-insights-tutorial-users.md).
- Använd [Följdanalys](../azure-monitor/app/usage-impact.md) att hjälpa dig att prioritera vilka områden att fokusera på enheten till viktiga KPI: er.


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om skillnaden komponenterna i [Azure Monitor](overview.md).
- [Lägga till kontinuerlig övervakning](../azure-monitor/app/continuous-monitoring.md) till din releasepipeline.