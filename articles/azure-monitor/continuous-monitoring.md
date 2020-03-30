---
title: Kontinuerlig övervakning med Azure Monitor | Microsoft-dokument
description: Beskriver specifika steg för att använda Azure Monitor för att aktivera kontinuerlig övervakning i hela arbetsflödena.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/12/2018
ms.openlocfilehash: b9ca8a703ed8a84148abd23e90114402d8806bd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667201"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Kontinuerlig övervakning med Azure Monitor

Kontinuerlig övervakning avser den process och teknik som krävs för att införliva övervakning över varje fas av dina DevOps- och IT-driftlivscykler. Det bidrar till att kontinuerligt säkerställa hälsa, prestanda och tillförlitlighet för ditt program och infrastruktur när den går från utveckling till produktion. Kontinuerlig övervakning bygger på begreppen kontinuerlig integrering och kontinuerlig distribution (CI/CD) som hjälper dig att utveckla och leverera programvara snabbare och mer tillförlitligt för att ge användarna kontinuerligt värde.

[Azure Monitor](overview.md) är den enhetliga övervakningslösningen i Azure som ger full stack observerbarhet över program och infrastruktur i molnet och lokalt. Det fungerar sömlöst med [Visual Studio och Visual Studio Code](https://visualstudio.microsoft.com/) under utveckling och test och integrerar med Azure [DevOps](/azure/devops/user-guide/index) för versionshantering och arbetsobjekthantering under distribution och drift. Den integreras även i de EGNA ITSM- och SIEM-verktygen för att spåra problem och incidenter inom dina befintliga IT-processer.

I den här artikeln beskrivs specifika steg för att använda Azure Monitor för att möjliggöra kontinuerlig övervakning i hela arbetsflödena. Den innehåller länkar till annan dokumentation som innehåller information om hur du implementerar olika funktioner.


## <a name="enable-monitoring-for-all-your-applications"></a>Aktivera övervakning för alla dina program
För att få observerbarhet i hela din miljö måste du aktivera övervakning på alla dina webbapplikationer och tjänster. På så sätt kan du enkelt visualisera heltäckande transaktioner och anslutningar mellan alla komponenter.

- [Azure DevOps-projekt](../devops-project/overview.md) ger dig en förenklad upplevelse med din befintliga kod och Git-databas, eller välj bland något av exempelprogrammen för att skapa en KONTINUERLIG Integration (CI) och CD-pipeline (Continuous Delivery) till Azure.
- [Kontinuerlig övervakning i din DevOps-utgivningspipeline](../azure-monitor/app/continuous-monitoring.md) gör att du kan utfärda eller återställa distributionen baserat på övervakningsdata.
- [Med Statusövervakaren](../azure-monitor/app/monitor-performance-live-website-now.md) kan du instrumentera en live .NET-app i Windows med Azure Application Insights, utan att behöva ändra eller distribuera om koden.
- Om du har åtkomst till koden för ditt program aktiverar du fullständig övervakning med [Application Insights](../azure-monitor/app/app-insights-overview.md) genom att installera Azure Monitor Application Insights SDK för [.NET](../azure-monitor/learn/quick-monitor-portal.md), [Java](../azure-monitor/app/java-get-started.md), [Node.js](../azure-monitor/learn/nodejs-quick-start.md)eller [andra programmeringsspråk](../azure-monitor/app/platforms.md). På så sätt kan du ange anpassade händelser, mått eller sidvisningar som är relevanta för ditt program och ditt företag.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>Aktivera övervakning för hela infrastrukturen
Program är bara lika tillförlitliga som deras underliggande infrastruktur. Om du har aktiverat övervakning i hela infrastrukturen kan du uppnå full observerbarhet och göra det enklare att upptäcka en potentiell grundorsak när något misslyckas. Azure Monitor hjälper dig att spåra hälsa och prestanda för hela hybridinfrastrukturen, inklusive resurser som virtuella datorer, behållare, lagring och nätverk.

- Du får automatiskt [plattformsmått, aktivitetsloggar och diagnostikloggar](platform/data-sources.md) från de flesta av dina Azure-resurser utan konfiguration.
- Aktivera djupare övervakning för virtuella datorer med [Azure Monitor för virtuella datorer](insights/vminsights-overview.md).
-  Aktivera djupare övervakning för AKS-kluster med [Azure Monitor för behållare](insights/container-insights-overview.md).
- Lägg till [övervakningslösningar](insights/solutions-inventory.md) för olika program och tjänster i din miljö.


[Infrastruktur som kod](/azure/devops/learn/what-is-infrastructure-as-code) är hantering av infrastruktur i en beskrivande modell, med samma versionshantering som DevOps-team använder för källkod. Det ger tillförlitlighet och skalbarhet till din miljö och gör att du kan utnyttja liknande processer som används för att hantera dina program.

-  Använd [Resource Manager-mallar](platform/template-workspace-configuration.md) för att aktivera övervakning och konfigurera aviseringar över en stor uppsättning resurser.
- Använd [Azure Policy](../governance/policy/overview.md) för att tillämpa olika regler över dina resurser. Detta säkerställer att dessa resurser förblir kompatibla med dina företagsstandarder och servicenivåavtal. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Kombinera resurser i Azure-resursgrupper
Ett vanligt program på Azure innehåller idag flera resurser, till exempel virtuella datorer och App-tjänster eller mikrotjänster som finns på Molntjänster, AKS-kluster eller Service Fabric. Dessa program använder ofta beroenden som Event Hubs, Storage, SQL och Service Bus.

- Kombinera resurser i Azure Resource Groups för att få full insyn i alla dina resurser som utgör dina olika program. [Azure Monitor för resursgrupper](../azure-monitor/insights/resource-group-insights.md) är ett enkelt sätt att hålla reda på hälsotillståndet och prestanda för hela ditt fullstacksprogram och gör det möjligt att öka detaljborrningen i respektive komponenter för alla undersökningar eller felsökning.

## <a name="ensure-quality-through-continuous-deployment"></a>Säkerställa kvalitet genom kontinuerlig distribution
Kontinuerlig integrering /kontinuerlig distribution gör att du automatiskt kan integrera och distribuera kodändringar till ditt program baserat på resultaten av automatiserad testning. Det effektiviserar distributionsprocessen och säkerställer kvaliteten på eventuella ändringar innan de går in i produktionen.


- Använd [Azure Pipelines](/azure/devops/pipelines) för att implementera kontinuerlig distribution och automatisera hela processen från kod commit till produktion baserat på din CI / CD-tester.
- Använd [Quality Gates](/azure/devops/pipelines/release/approvals/gates) för att integrera övervakning i din fördistribution eller efter distribution. Detta säkerställer att du uppfyller de viktigaste hälso-/prestandamåtten (KPI:er) när dina program flyttas från utveckling till produktion och eventuella skillnader i infrastrukturmiljön eller skalan påverkar inte KPI:erna negativt.
- [Underhåll separata övervakningsinstanser](../azure-monitor/app/separate-resources.md) mellan dina olika distributionsmiljöer som Dev, Test, Canary och Prod. Detta säkerställer att insamlade data är relevanta för de associerade programmen och infrastrukturen. Om du behöver korrelera data mellan miljöer kan du använda [flerresursdiagram i Metrics Explorer](../azure-monitor/platform/metrics-charts.md) eller skapa [resursövergripande frågor i Azure Monitor](log-query/cross-workspace-query.md).


## <a name="create-actionable-alerts-with-actions"></a>Skapa användbara aviseringar med åtgärder
En kritisk aspekt av övervakning är proaktivt meddela administratörer om eventuella aktuella och förväntade problem. 

- Skapa [aviseringar i Azure Monitor](../azure-monitor/platform/alerts-overview.md) baserat på loggar och mått för att identifiera förutsägbara feltillstånd. Du bör ha som mål att göra alla aviseringar genomförbara vilket innebär att de representerar faktiska kritiska förhållanden och försöker minska falska positiva identifieringar. Använd [dynamiska tröskelvärden](platform/alerts-dynamic-thresholds.md) för att automatiskt beräkna originalplaner på måttdata i stället för att definiera dina egna statiska tröskelvärden. 
- Definiera åtgärder för aviseringar om du vill använda det mest effektiva sättet att meddela administratörerna. Tillgängliga [åtgärder för avisering](platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) är SMS, e-post, push-meddelanden eller röstsamtal.
- Använd mer avancerade åtgärder för att [ansluta till ditt ITSM-verktyg](platform/itsmc-overview.md) eller andra varningshanteringssystem via [webhooks](platform/activity-log-alerts-webhook.md).
- Åtgärda situationer som identifierats i aviseringar samt med [Azure Automation-runbooks](../automation/automation-webhooks.md) eller [Logic Apps](/connectors/custom-connectors/create-webhook-trigger) som kan startas från en avisering med webhooks. 
- Använd [automatisk skalning](../azure-monitor/learn/tutorial-autoscale-performance-schedule.md) för att dynamiskt öka och minska dina beräkningsresurser baserat på insamlade mått.

## <a name="prepare-dashboards-and-workbooks"></a>Förbereda instrumentpaneler och arbetsböcker
Genom att se till att din utveckling och dina åtgärder har tillgång till samma telemetri och verktyg kan de visa mönster i hela din miljö och minimera din tid att identifiera (MTTD) och Mean Time To Restore (MTTR).

- Förbered [anpassade instrumentpaneler](../azure-monitor/learn/tutorial-app-dashboards.md) baserat på vanliga mått och loggar för de olika rollerna i organisationen. Instrumentpaneler kan kombinera data från alla Azure-resurser.
- Förbered [arbetsböcker](../azure-monitor/app/usage-workbooks.md) för att säkerställa kunskapsdelning mellan utveckling och verksamhet. Dessa kan förberedas som dynamiska rapporter med måttdiagram och loggfrågor, eller till och med som felsökningsguider som utarbetats av utvecklare som hjälper kundsupport eller åtgärder att hantera grundläggande problem.

## <a name="continuously-optimize"></a>Optimera kontinuerligt
 Övervakning är en av de grundläggande aspekterna av den populära Build-Measure-Learn-filosofin, som rekommenderar att du kontinuerligt spårar dina KPI:er och användarbeteendemått och sedan strävar efter att optimera dem genom att planera iterationer. Azure Monitor hjälper dig att samla in mått och loggar som är relevanta för ditt företag och lägga till nya datapunkter i nästa distribution efter behov.

- Använd verktyg i Application Insights för att [spåra slutanvändarens beteende och engagemang](../azure-monitor/learn/tutorial-users.md).
- Använd [konsekvensanalys](../azure-monitor/app/usage-impact.md) för att prioritera vilka områden du ska fokusera på för att köra till viktiga KPI:er.


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om skillnaden komponenter i [Azure Monitor](overview.md).
- [Lägg till kontinuerlig övervakning](../azure-monitor/app/continuous-monitoring.md) till din releasepipeline.
