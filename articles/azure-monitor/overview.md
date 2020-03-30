---
title: Översikt över Azure Monitor | Microsoft-dokument
description: Översikt över Microsoft-tjänster och funktioner som bidrar till en fullständig övervakningsstrategi för dina Azure-tjänster och program.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: cfdd0beac7d257a424d327df71602b4612c3da3b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79536861"
---
# <a name="azure-monitor-overview"></a>Översikt över Azure Monitor

Azure Monitor maximerar tillgängligheten och prestandan för dina program och tjänster genom att leverera en omfattande lösning för att samla in, analysera och agera på telemetri från molnet och lokala miljöer. Det hjälper dig att förstå hur dina program fungerar och identifierar proaktivt problem som påverkar dem och de resurser som de förlitar sig på.

Bara några exempel på vad du kan göra med Azure Monitor är:

- Identifiera och diagnostisera problem mellan program och beroenden med [Application Insights](app/app-insights-overview.md).
- Korrelera infrastrukturproblem med [Azure Monitor för virtuella datorer](insights/vminsights-overview.md) och Azure Monitor för [behållare](insights/container-insights-overview.md).
- Öka detaljnivån i dina övervakningsdata med [Log Analytics](log-query/log-query-overview.md) för felsökning och djup diagnostik.
- Stödåtgärder i stor skala med [smarta varningar](platform/alerts-smartgroups-overview.md) och [automatiserade åtgärder](platform/alerts-action-rules.md).
- Skapa visualiseringar med [Azure-instrumentpaneler](learn/tutorial-logs-dashboards.md) och [arbetsböcker](app/usage-workbooks.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Översikt

I följande diagram ges en översikt över Azure Monitor på hög nivå. I mitten av diagrammet finns datalager för mått och loggar, som är de två grundläggande typerna av dataanvändning av Azure Monitor. Till vänster finns [källorna till övervakningsdata](platform/data-sources.md) som fyller i dessa [datalager](platform/data-platform.md). Till höger finns de olika funktioner som Azure Monitor utför med dessa insamlade data som analys, aviseringar och direktuppspelning till externa system.

![Översikt över Azure Monitor](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Övervakning av dataplattform

Alla data som samlas in av Azure Monitor passar in i en av två grundläggande typer, [mått och loggar](platform/data-platform.md). [Mått](platform/data-platform-metrics.md) är numeriska värden som beskriver någon aspekt av ett system vid en viss tidpunkt. De är lätta och kan stödja scenarier i nära realtid. [Loggar innehåller](platform/data-platform-logs.md) olika typer av data som är ordnade i poster med olika uppsättningar egenskaper för varje typ. Telemetri som händelser och spårningar lagras som loggar utöver prestandadata så att allt kan kombineras för analys.

För många Azure-resurser ser du data som samlas in av Azure Monitor direkt på deras översiktssida i Azure-portalen. Ta en titt på alla virtuella datorer till exempel, och du ser flera diagram som visar prestandamått. Klicka på något av diagrammen för att öppna data i [statistikutforskaren](platform/metrics-charts.md) i Azure-portalen, vilket gör att du kan kartlägga värdena för flera mått över tid.  Du kan visa diagrammen interaktivt eller fästa dem på en instrumentpanel för att visa dem med andra visualiseringar.

![Mått](media/overview/metrics.png)

Loggdata som samlas in av Azure Monitor kan analyseras med [frågor](log-query/log-query-overview.md) för att snabbt hämta, konsolidera och analysera insamlade data.  Du kan skapa och testa frågor med [Log Analytics](log-query/portals.md) i Azure-portalen och sedan antingen direkt analysera data med hjälp av dessa verktyg eller spara frågor för användning med [visualiseringar](visualizations.md) eller [varningsregler](platform/alerts-overview.md).

Azure Monitor använder en version av [Kusto-frågespråket](/azure/kusto/query/) som används av Azure Data Explorer och som är lämplig för enkla loggfrågor men även innehåller avancerade funktioner som aggregeringar, kopplingar och smart analys. Du kan snabbt lära dig frågespråket med [flera lektioner](log-query/get-started-queries.md).  Viss vägledning erbjuds användare som redan är bekanta med [SQL](log-query/sql-cheatsheet.md) och [Splunk](log-query/splunk-cheatsheet.md).

![Loggar](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Vilka data samlar Azure Monitor in?

Azure Monitor kan samla in data från olika källor. Övervakningen av data för dina program sker i nivåer – från ditt program, operativsystemet och de tjänster som det använder, till själva plattformen. Azure Monitor samlar in data från var och en av följande nivåer:

- **Programövervakningsdata**: Data om prestanda och funktionalitet för den kod du har skrivit, oavsett plattform.
- **Gäst OS övervakningsdata:** Data om operativsystemet som ditt program körs. Det kan köras i Azure, i ett annat moln eller lokalt. 
- **Azure-resursövervakningsdata**: Data om driften av en Azure-resurs.
- **Azure-prenumerationsövervakningsdata:** Data om drift och hantering av en Azure-prenumeration samt data om hälsa och drift av Azure själv. 
- **Azure-klientövervakningsdata:** Data om drift av Azure-tjänster på klientnivå, till exempel Azure Active Directory.

När du skapar en Azure-prenumeration och börjar lägga till resurser, till exempel virtuella datorer och webbappar, börjar Azure Monitor samla in data.  [Aktivitetsloggar postar](platform/platform-logs-overview.md) när resurser skapas eller ändras. [Mått](platform/data-platform.md) visar hur resursen fungerar och vilka resurser den förbrukar. 

Utöka de data som du samlar in i den faktiska funktionen av resurserna genom [att aktivera diagnostik](platform/platform-logs-overview.md) och lägga till en [agent](platform/agent-windows.md) för att beräkna resurser. Detta samlar in telemetri för den interna driften av resursen och gör att du kan konfigurera olika [datakällor](platform/agent-data-sources.md) för att samla in loggar och mått från Windows och Linux gästoperativsystem. 

Aktivera övervakning för ditt [App Services-program](app/azure-web-apps.md) eller [vm- och virtuell datorskalauppsättningsprogram](app/azure-vm-vmss-apps.md)för att aktivera Application Insights för att samla in detaljerad information om ditt program, inklusive sidvisningar, programbegäranden och undantag. Ytterligare kontrollera tillgängligheten för ditt program genom att konfigurera ett [tillgänglighetstest](app/monitor-web-app-availability.md) för att simulera användartrafik.

### <a name="custom-sources"></a>Anpassade källor

Azure Monitor kan samla in loggdata från valfri REST-klient med hjälp av [DATA Collector API](platform/data-collector-api.md). På så sätt kan du skapa anpassade övervakningsscenarier och utöka övervakningen till resurser som inte exponerar telemetri via andra källor.

## <a name="insights"></a>Insikter
Övervakningsdata är bara användbart om det kan öka din insyn i driften av din datormiljö. Azure Monitor innehåller flera funktioner och verktyg som ger värdefulla insikter i dina program och andra resurser som de är beroende av. [Övervakningslösningar](insights/solutions.md) och funktioner som [Application Insights](app/app-insights-overview.md) och [Azure Monitor för behållare](insights/container-insights-overview.md) ger djup insikt i olika aspekter av ditt program och specifika Azure-tjänster. 

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) övervakar tillgängligheten, prestandan och användningen av dina webbprogram oavsett om de finns i molnet eller lokalt. Den utnyttjar den kraftfulla dataanalysplattformen i Azure Monitor för att ge dig djup insikt i programmets åtgärder och diagnostisera fel utan att vänta på att en användare ska rapportera dem. Application Insights innehåller anslutningspunkter till en mängd olika utvecklingsverktyg och integreras med Visual Studio för att stödja dina DevOps-processer.

![App Insights](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Azure Monitor för containrar
[Azure Monitor for containers](insights/container-insights-overview.md) är en funktion som utformats för att övervaka prestanda för behållararbetsbelastningar som distribueras till hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS). Det ger dig prestandasynlighet genom att samla in minnes- och processormått från styrenheter, noder och behållare som är tillgängliga i Kubernetes via Metrics API. Containerloggar samlas också.  När du har aktiverat övervakning från Kubernetes-kluster samlas dessa mått och loggar automatiskt in åt dig via en behållarversion av Log Analytics-agenten för Linux.

![Behållare Hälsa](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Azure Monitor för virtuella datorer
[Azure Monitor för virtuella datorer](insights/vminsights-overview.md) övervakar dina virtuella Azure-datorer (VM) i stor skala genom att analysera prestanda och hälsa för dina virtuella Windows- och Linux-datorer, inklusive deras olika processer och sammankopplade beroenden på andra resurser och externa processer. Lösningen innehåller stöd för övervakning av prestanda och programberoenden för virtuella datorer som finns lokalt eller en annan molnleverantör.  


![VM-insikter](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Övervakningslösningar
[Övervakningslösningar](insights/solutions.md) i Azure Monitor är paketerade uppsättningar av logik som ger insikter för ett visst program eller en viss tjänst. De innehåller logik för att samla in övervakningsdata för programmet eller tjänsten, [frågor](log-query/log-query-overview.md) för att analysera dessa data och [vyer](../log-analytics/log-analytics-view-designer.md) för visualisering. Övervakningslösningar är [tillgängliga från Microsoft](insights/solutions-inventory.md) och partner för att tillhandahålla övervakning för olika Azure-tjänster och andra program.

![Övervakningslösningar](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Svara på kritiska situationer
Förutom att du kan interaktivt analysera övervakningsdata måste en effektiv övervakningslösning proaktivt kunna svara på kritiska förhållanden som identifierats i de data som samlas in. Detta kan vara att skicka ett sms eller e-postmeddelande till en administratör som ansvarar för att undersöka ett problem. Eller så kan du starta en automatiserad process som försöker korrigera ett feltillstånd.


### <a name="alerts"></a>Aviseringar
[Aviseringar i Azure Monitor](platform/alerts-overview.md) meddelar dig proaktivt om kritiska villkor och kan försöka vidta korrigerande åtgärder. Varningsregler baserade på mått ger aviseringar i nära realtid baserat på numeriska värden, medan regler baserade på loggar möjliggör komplex logik över data från flera källor.

Varningsregler i Azure Monitor använder [åtgärdsgrupper](platform/action-groups.md), som innehåller unika uppsättningar mottagare och åtgärder som kan delas mellan flera regler. Baserat på dina krav kan åtgärdsgrupper utföra sådana åtgärder som att använda webhooks för att få aviseringar att starta externa åtgärder eller för att integrera med dina ITSM-verktyg.

![Aviseringar](media/overview/alerts.png)

### <a name="autoscale"></a>Automatisk skalning
Med automatisk skalning kan du ha rätt mängd resurser som körs för att hantera belastningen på ditt program. Det gör att du kan skapa regler som använder mått som samlats in av Azure Monitor för att avgöra när du automatiskt ska lägga till resurser för att hantera ökningar i inläsning och även spara pengar genom att ta bort resurser som sitter inaktiva. Du anger ett minsta och högsta antal instanser och logiken för när resurserna ska ökas eller minskas.

![Automatisk skalning](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Visualisera övervakningsdata
[Visualiseringar](visualizations.md) som diagram och tabeller är effektiva verktyg för att sammanfatta övervakningsdata och presentera dem för olika målgrupper. Azure Monitor har sina egna funktioner för att visualisera övervakningsdata och använder andra Azure-tjänster för att publicera dem för olika målgrupper.

### <a name="dashboards"></a>Instrumentpaneler
[Med Azure-instrumentpaneler](../azure-portal/azure-portal-dashboards.md) kan du kombinera olika typer av data, inklusive både mått och loggar, till en enda ruta i [Azure-portalen](https://portal.azure.com). Du kan också dela instrumentpanelen med andra Azure-användare. Element i Azure Monitor kan läggas till på en Azure-instrumentpanel utöver utdata från alla loggfrågor eller mått diagram. Du kan till exempel skapa en instrumentpanel som kombinerar paneler som visar ett diagram över mått, en tabell med aktivitetsloggar, ett användningsdiagram från Application Insights och utdata från en loggfråga.

![Instrumentpanel](media/overview/dashboard.png)

### <a name="views"></a>Vyer
[Vyer](../log-analytics/log-analytics-view-designer.md) presenterar visuellt loggdata i Azure Monitor.  Varje vy innehåller en enda panel som borrar nedåt till en kombination av visualiseringar, till exempel stapel- och linjediagram, förutom listor som sammanfattar kritiska data.  Övervakningslösningar omfattar vyer som sammanfattar data för ett visst program, och du kan skapa egna vyer för att presentera data från en loggfråga. Precis som andra element i Azure Monitor kan vyer läggas till i Azure-instrumentpaneler.

![Visa](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) är en företagsanalystjänst som tillhandahåller interaktiva visualiseringar över en mängd olika datakällor och är ett effektivt sätt att göra data tillgängliga för andra inom och utanför organisationen. Du kan konfigurera Power BI för att [automatiskt importera loggdata från Azure Monitor](../log-analytics/log-analytics-powerbi.md) för att dra nytta av dessa ytterligare visualiseringar.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integrera och exportera data
Du har ofta kravet på att integrera Azure Monitor med andra system och att skapa anpassade lösningar som använder dina övervakningsdata. Andra Azure-tjänster fungerar med Azure Monitor för att tillhandahålla den här integreringen.

### <a name="event-hub"></a>Händelsehubb
[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs) är en tjänst för direktuppspelningsplattform och händelseinmatning som kan omvandla och lagra data med hjälp av alla analysleverantörer i realtid eller batch-/lagringskort. Använd eventhubbar för att [strömma Azure Monitor-data](platform/stream-monitoring-data-event-hubs.md) till partner-SIEM- och övervakningsverktyg.


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) är en tjänst som gör att du kan automatisera uppgifter och affärsprocesser med hjälp av arbetsflöden som integreras med olika system och tjänster. Aktiviteter är tillgängliga som läser och skriver mått och loggar i Azure Monitor, vilket gör att du kan skapa arbetsflöden som integreras med en mängd andra system.


### <a name="api"></a>API
Flera API:er är tillgängliga för att läsa och skriva mått och loggar till och från Azure Monitor förutom åtkomst till genererade aviseringar. Du kan också konfigurera och hämta aviseringar. Detta ger dig i stort sett obegränsade möjligheter att skapa anpassade lösningar som integreras med Azure Monitor.

## <a name="next-steps"></a>Nästa steg
Läs mer om:

* [Mått och loggar](platform/data-platform.md) för data som samlas in av Azure Monitor.
* [Datakällor](platform/data-sources.md) för hur de olika komponenterna i programmet skickar telemetri.
* [Logga frågor](log-query/log-query-overview.md) för att analysera insamlade data.
* [Metodtips](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) för övervakning av molnprogram och molntjänster.
