---
title: Översikt över Azure Monitor | Microsoft Docs
description: Översikt över Microsoft-tjänster och funktioner som bidrar till en fullständig övervakningsstrategi för dina Azure-tjänster och program.
author: bwren
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/26/2019
ms.author: bwren
ms.openlocfilehash: 0485f8e3b377ce94ec23a4a1a94eb7e189b0232b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784967"
---
# <a name="azure-monitor-overview"></a>Översikt över Azure Monitor

Azure Monitor maximerar tillgänglighet och prestanda för dina program genom att tillhandahålla en omfattande lösning för att samla in, analysera och agera på telemetri från molnet och lokala miljöer. Det hjälper dig att förstå hur dina program fungerar och identifierar proaktivt problem som påverkar dem och de resurser som de förlitar sig på.

> [!VIDEO https://www.youtube.com/embed/_hGff5bVtkM]

## <a name="overview"></a>Översikt
Följande diagram ger en översikt över Azure Monitor. I mitten av diagrammet är datalager för mått och loggar som finns två grundläggande typer av data används av Azure Monitor. Till vänster är den [källor för övervakningsdata](platform/data-sources.md) som fylla i dessa [datalager](platform/data-platform.md). Till höger är olika funktioner som Azure Monitor utför med den här insamlade data, till exempel analys, aviseringar och direktuppspelning till externa system.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

![Översikt över Azure Monitor](media/overview/overview.png)


## <a name="monitoring-data-platform"></a>Övervaka dataplattform
Alla data som samlas in av Azure Monitor passar in i en av två grundläggande typer [mått och loggar](platform/data-platform.md). [Mått](platform/data-platform-metrics.md) är numeriska värden som beskriver någon aspekt av ett system vid en viss tidpunkt. De är enkel och kan stödja scenarier i nästan realtid. [Loggar](platform/data-platform-logs.md) innehåller olika typer av data ordnas i poster med olika uppsättningar med egenskaper för varje typ. Telemetri, till exempel händelser och spårningar lagras som loggar dessutom till prestandadata så att den kan alla kombineras för analys.

För många Azure-resurser visas data som samlas in av Azure Monitor direkt i sina översiktssidan i Azure-portalen. Till exempel ta en titt på en virtuell dator och du ser flera diagram som visar prestandamått. Klicka på något av diagrammen för att öppna data i [måttutforskaren](platform/metrics-charts.md) i Azure-portalen, där du kan skapa diagram över värdena för flera mått med tiden.  Du kan visa diagrammen interaktivt eller fästa dem på en instrumentpanel för att visa dem med andra visualiseringar.

![Mått](media/overview/metrics.png)

Loggdata som samlas in av Azure Monitor kan analyseras med [frågor](log-query/log-query-overview.md) att snabbt hämta, konsolidera och analysera insamlade data.  Du kan skapa och testa frågor med [Log Analytics](log-query/portals.md) i Azure portal och sedan antingen direkt analysera data med verktygen eller spara frågor för användning med [visualiseringar](visualizations.md) eller [avisering regler för](platform/alerts-overview.md).

Azure Monitor använder en version av den [Kusto-frågespråket](/azure/kusto/query/) används av Azure Data Explorer som är lämpliga för enkel logg frågar men innehåller även avancerade funktioner som aggregeringar, kopplingar och smart analytics. Du kan snabbt lära dig frågespråket med [flera lektioner](log-query/get-started-queries.md).  Viss vägledning erbjuds användare som redan är bekanta med [SQL](log-query/sql-cheatsheet.md) och [Splunk](log-query/splunk-cheatsheet.md).

![Loggar](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Vilka data samlar Azure Monitor?
Azure Monitor kan samla in data från olika källor. Du kan se övervakningsdata för dina program i nivåer som sträcker sig från ditt program, alla operativsystem och tjänster som den förlitar sig på, till själva plattformen. Azure Monitor samlar in data från var och en av följande nivåer:

- **Data för programövervakning**: Data om prestanda och funktioner i koden som du har skrivit, oavsett dess plattform.
- **Gäst-OS övervakningsdata**: Information om operativsystemet där programmet körs. Detta kan köras i Azure, en annan molnet eller lokalt. 
- **Azure-resurs övervakningsdata**: Information om driften av en Azure-resurs.
- **Azure-prenumeration övervakningsdata**: Data om driften och hanteringen av en Azure-prenumeration, samt data om klientens hälsotillstånd och driften av Azure själva. 
- **Azure-klient övervakningsdata**: Information om driften av Azure på klientnivå-tjänster, till exempel Azure Active Directory.

När du skapar en Azure-prenumeration och börja lägga till resurser, till exempel virtuella datorer och webbappar, startar Azure Monitor insamling av data.  [Aktivitetsloggar](platform/activity-logs-overview.md) registrera när resurser skapas eller ändras. [Mått](platform/data-platform.md) talar om hur resursen fungerar och de resurser som den förbrukar. 

Utöka dina data som du samlar in i den faktiska användningen av resurser genom att [aktiverar diagnostik](platform/diagnostic-logs-overview.md) och [att lägga till en agent](platform/agent-windows.md) att beräkna resurser. Detta samlar in telemetri för den interna åtgärden på resursen och gör att du kan konfigurera olika [datakällor](platform/agent-data-sources.md) att samla in loggar och mått från Windows och Linux gästoperativsystemet. 

[Lägga till en instrumentationspaket i ditt program](app/azure-web-apps.md)att aktivera Application Insights för att samla in detaljerad information om ditt program, inklusive sidvisningar, programförfrågningar och undantag. Ytterligare kontrollera tillgängligheten för ditt program genom att konfigurera en [tillgänglighetstestet](app/monitor-web-app-availability.md) att simulera trafik för användare.

### <a name="custom-sources"></a>Anpassade källor
Azure Monitor kan samla in loggdata från alla REST-klient som använder den [Data Collector API](platform/data-collector-api.md). På så sätt kan du skapa anpassade övervakningsscenarier och utökad övervakning till resurser som inte exponerar telemetri via andra källor.



## <a name="insights"></a>Insikter
Övervakning av data är endast användbart om det kan öka din insyn i driften av IT-miljön. Azure Monitor innehåller flera funktioner och verktyg som kan ger värdefulla insikter i dina program och andra resurser som de förlitar sig på. [Övervakningslösningar](insights/solutions.md) och funktioner som [Programinsikter](app/app-insights-overview.md) och [Azure Monitor för behållare](insights/container-insights-overview.md) ger djupare insikter om olika aspekter av ditt program och specifika Azure tjänster. 

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) övervakar tillgänglighet, prestanda och användningen av ditt webbprogram om de är finns i molnet eller lokalt. Den använder kraftfull dataplattform analys i Azure Monitor för att ge dig djupare insikter om ditt programs operations och diagnostisera fel utan att behöva vänta på att en användare rapporterar dem. Application Insights innehåller en rad olika utvecklingsverktyg och integreras med Visual Studio för att stödja dina DevOps-processer.

![App Insights](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Azure Monitor för containrar
[Azure Monitor för behållare](insights/container-insights-overview.md) är en funktion som utformats för att övervaka prestanda för arbetsbelastningar distribueras till hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS). Det ger dig insyn i prestanda genom att samla in minne och processor mått från domänkontrollanter, noder och behållare som är tillgängliga i Kubernetes via mått-API. Containerloggar samlas också.  När du aktiverar övervakning från Kubernetes-kluster kan samlas dessa mått och loggar automatiskt in för dig via en behållare version av Log Analytics-agenten för Linux.

![Container Health](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Azure Monitor för virtuella datorer
[Azure Monitor för virtuella datorer](insights/vminsights-overview.md) övervakar dina Azure-datorer (VM) i stor skala genom att analysera prestanda och hälsa för Windows och Linux-datorer, inklusive olika processer och sammankopplade beroenden på andra resurser och externt bearbetar. Lösningen har stöd för övervakning av prestanda och programberoenden för virtuella datorer finns på plats eller en annan molnleverantör.  


![VM-insikter](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Övervakningslösningar
[Övervakningslösningar](insights/solutions.md) är paketerade uppsättningar av logik som ger insikter för ett visst program eller tjänst i Azure Monitor. De innehåller logik för att samla in övervakningsdata för programmet eller tjänsten, [frågor](log-query/log-query-overview.md) att analysera dessa data och [vyer](../log-analytics/log-analytics-view-designer.md) för visualisering. Övervakningslösningar är [tillgängliga från Microsoft](insights/solutions-inventory.md) och partners att tillhandahålla övervakning för olika Azure-tjänster och andra program.

![Övervakningslösningar](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Svara på kritiska situationer
Förutom att du kan analysera övervakningsdata interaktivt, kunna en effektiv lösning för övervakning proaktivt besvara kritiska tillstånd som identifierats i de data som den samlar in. Detta kan vara att skicka en text- eller e-post till en administratör ansvarar för att undersöka problem. Eller du kan starta en automatiserad process som försöker korrigera ett feltillstånd.


### <a name="alerts"></a>Aviseringar
[Aviseringar i Azure Monitor](platform/alerts-overview.md) informera dig om kritiska tillstånd och potentiellt försöker vidta åtgärder. Varningsregler baserat på mått ger nära nog realtid aviseringar baserat på numeriska värden, medan regler baserat på loggar tillåter komplex logik för data från flera källor.

Aviseringsregler i Azure Monitor Använd [åtgärdsgrupper](platform/action-groups.md), som innehåller en unik uppsättning mottagare och åtgärder som kan delas mellan flera regler. Baserat på dina krav, kan åtgärdsgrupper utföra åtgärder, t ex med webhookar ha aviseringar starta externa åtgärder eller för att integrera med dina ITSM-verktyg.

![Aviseringar](media/overview/alerts.png)

### <a name="autoscale"></a>Automatisk skalning
Automatisk skalning kan du har rätt mängd resurser som körs för att hantera belastningen på programmet. Det kan du skapa regler som använder mätvärden som samlats in från Azure Monitor för att avgöra om att automatiskt lägga till resurser för att hantera ökad belastning och sparar du pengar genom att ta bort resurser som sitter inaktiv. Du kan ange ett lägsta och högsta antal instanser och logiken för när du vill öka eller minska resurserna.

![Automatisk skalning](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Visualisera dina övervakningsdata
[Visualiseringar](visualizations.md) som diagram och tabeller är effektiva verktyg för sammanfattas övervakningsdata och presentera den för olika målgrupper. Azure Monitor har egna funktioner för visualisering av övervakning av data och använder andra Azure-tjänster för att publicera den till olika målgrupper.

### <a name="dashboards"></a>Instrumentpaneler
[Azure-instrumentpaneler](../azure-portal/azure-portal-dashboards.md) kan du kombinera olika typer av data, inklusive loggar och mått i ett enda fönster i den [Azure-portalen](https://portal.azure.com). Du kan också dela instrumentpanelen med andra Azure-användare. Element i hela Azure Monitor kan läggas till en Azure-instrumentpanel förutom utdata för log-fråga eller mått diagram. Du kan till exempel skapa en instrumentpanel som kombinerar paneler som visar ett diagram över mått, en tabell med aktivitetsloggar, en Användningsdiagram från Application Insights och utdata från en loggfråga.

![Instrumentpanel](media/overview/dashboard.png)

### <a name="views"></a>Vyer
[Vyer](../log-analytics/log-analytics-view-designer.md) visuellt presentera loggdata i Azure Monitor.  Varje vy innehåller en enda panel som vidare till en kombination av visualiseringar som stapeldiagram och linjediagram utöver listor som summerar kritiska data.  Övervakningslösningar innehåller vyer som summerar data för ett visst program och du kan skapa egna vyer för att presentera data från alla logg-frågor. Vyer kan läggas till Azure-instrumentpaneler som andra element i Azure Monitor.

![Visa](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) är en företagsanalystjänst som ger interaktiva visualiseringar i en mängd olika datakällor och är ett effektivt sätt att göra data tillgängliga för andra inom och utanför organisationen. Du kan konfigurera Power BI för att [importera automatiskt loggdata från Azure Monitor](../log-analytics/log-analytics-powerbi.md) kan utnyttja dessa ytterligare visualiseringar.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integrera och exportera data
Ofta har du kravet på att integrera Azure Monitor med andra system och för att skapa anpassade lösningar som använder dina övervakningsdata. Andra Azure-tjänster fungerar med Azure Monitor för att tillhandahålla den här integreringen.

### <a name="event-hub"></a>Händelsehubb
[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs) är en strömmande plattform och händelseinmatningstjänst som kan omvandla och lagra data med valfri leverantör av realtidsanalys eller batchbearbetnings-/ lagringsadapter. Använda Event Hubs till [strömma Azure Monitor data](platform/stream-monitoring-data-event-hubs.md) att samarbeta SIEM och övervakningsverktyg.


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) är en tjänst som gör det möjligt att automatisera uppgifter och affärsprocesser med hjälp av arbetsflöden som integreras med olika system och tjänster. Aktiviteter är tillgängliga som läsa och skriva mått och loggar i Azure Monitor, där du kan skapa arbetsflöden som integrerar med en mängd andra system.


### <a name="api"></a>API
Det finns flera API: er för att läsa och skriva mått och loggar till och från Azure Monitor förutom att komma åt varningar som genererats. Du kan också konfigurera och hämta aviseringar. Det ger dig i stort sett obegränsade möjligheter att skapa anpassade lösningar som integreras med Azure Monitor.

## <a name="next-steps"></a>Nästa steg
Läs mer om:

* [Mått och loggar](platform/data-platform.md) för de data som samlas in av Azure Monitor.
* [Datakällor](platform/data-sources.md) för hur de olika komponenterna i ditt program skickar telemetri.
* [Loggar frågor](log-query/log-query-overview.md) för att analysera insamlade data.