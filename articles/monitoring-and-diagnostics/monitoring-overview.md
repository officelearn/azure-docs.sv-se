---
title: "Övervaka Azure-program och resurser | Microsoft Docs"
description: "Översikt över olika Microsoft-tjänster och funktioner som bidrar till en fullständig övervakning strategi för dina Azure-tjänster och program."
author: robb
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: robb,bwren
ms.openlocfilehash: ffd9a6f75a549b246a04adc5480e988b1622c5ca
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="monitoring-azure-applications-and-resources"></a>Övervaka Azure-program och resurser

Övervakning är att samla in och analysera data för att avgöra prestanda, hälsotillstånd och tillgänglighet för dina affärsprogram och resurser som den är beroende av. En effektiv övervakning strategi hjälper dig att förstå den detaljerade driften av de olika komponenterna i ditt program och att öka din drifttid genom att meddela dig om kritiska problem så att du kan lösa dem innan de hunnit bli problem.

Azure innehåller flera tjänster som individuellt utför en specifik roll eller aktivitet i övervakningsutrymmet och tillsammans levererar en heltäckande lösning för att samla in, analysera och fungerar på telemetri från ditt program och de underliggande Azure-resurserna support.  De kan också fungera om du vill övervaka viktiga lokala resurser för att tillhandahålla en hybrid övervakning miljö.   Det första steget i att utveckla en fullständig övervakning strategi för ditt program är att förstå de verktyg och data som är tillgängliga. 

Följande diagram visar en översikt över de olika komponenterna som arbetar tillsammans för att tillhandahålla övervakning av Azure-resurser.  Alla dessa beskrivs i följande avsnitt med länkar till detaljerad teknisk information.

![Övervakningsöversikt](media/monitoring-overview/overview.png)

## <a name="basic-monitoring"></a>Grundläggande övervakning
Grundläggande övervakning innehåller grundläggande obligatoriska övervakning över Azure-resurser.  Dessa tjänster kräver minimal konfiguration och samla in core telemetri som drivs av premium övervaka tjänster.    

### <a name="azure-monitor"></a>Azure Monitor
[Azure-Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) aktiverar grundläggande övervakning för Azure-tjänsten genom att tillåta samling [mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md), [aktivitetsloggar](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), och [diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).  Till exempel talar aktivitetsloggen när nya resurser skapas eller ändras.  Mått är tillgängliga som ger prestandastatistik för olika resurser och även operativsystemet inuti en virtuell dator.  Du kan visa dessa data med en av de olika utforskarfönster i Azure-portalen, skickar den till logganalys för trender och detaljerad analys eller skapa Varningsregler proaktivt meddelar om kritiska problem.

### <a name="service-health"></a>Service Health
Hälsotillståndet för programmet är beroende av Azure-tjänster som den är beroende av.  [Tjänstens hälsa för Azure](../service-health/service-health-overview.md) identifierar eventuella problem med Azure-tjänster som kan påverka programmet och även hjälper dig planera för eventuella schema underhåll.

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](../advisor/advisor-overview.md) ständigt övervakar din resurs konfiguration och användning telemetri för att tillhandahålla du anpassade rekommendationer baserat på bästa praxis.  Följa dessa rekommendationer att hjälpa dig att förbättra prestanda, säkerhet och tillgänglighet för de resurser som stödjer dina program.


## <a name="premium-monitoring-services"></a>Premium övervakningstjänster
Följande Azure-tjänster innehåller omfattande funktioner för att samla in och analysera övervakningsdata.  De bygger på grundläggande övervakning och utnyttjar vanliga funktioner i Azure och ger kraftfulla analytics insamlade data för att ge unik insikter i dina program och infrastruktur.  De presentera data inom ramen för särskilda scenarier som är avsedda för olika målgrupper.

### <a name="application-insights"></a>Application Insights
[Application Insights](http://azure.microsoft.com/documentation/services/application-insights) kan du övervakartillgänglighet, prestanda och användning av ditt program om den finns i molnet eller lokalt.  Du kan uppnå djupa insikter så att du kan snabbt identifiera och diagnostisera fel utan att vänta på en användare att rapportera dem av instrumentering programmet att fungera med Application Insights. Du kan göra välgrundade val om underhåll och förbättringar för ditt program med den information som du samlar in.  Förutom de omfattande verktyg för att interagera med data som samlas in, lagras data i en gemensam databas utnyttja Delade funktioner som aviseringar, instrumentpaneler och djupanalys med Log Analytics-frågespråket i Application Insights.

### <a name="log-analytics"></a>Log Analytics
[Logga Analytics](http://azure.microsoft.com/documentation/services/log-analytics) betydelse i Azure övervakning genom att samla in data från en mängd resurser i en databas där den kan analyseras med ett kraftfullt frågespråk.  Application Insights och Azure Security Center kan du lagra data i logganalys data lagras och utnyttja dess analytics-motorn.  Denna kombineras med data som samlas in från Azure-Monitor, hanteringslösningar, och agenter som installerats på virtuella datorer i molnet eller lokalt kan du att bilda en komplett bild av hela miljön. 


### <a name="service-map"></a>Tjänstkarta
[Tjänstkarta](../operations-management-suite/operations-management-suite-service-map.md) ger inblick i IaaS-miljö genom att analysera virtuella datorer med olika processer och beroenden på andra datorer och externa processer.  Det integreras händelser, prestandadata och lösningar för hantering i logganalys så att du kan visa dessa data i kontexten för varje dator och dess relation till resten av miljön.  Tjänstkarta liknar den [programavbildningen i Application Insights](../application-insights/app-insights-app-map.md) men fokuserar på infrastrukturkomponenter stöd för dina program.

### <a name="network-watcher"></a>Network Watcher
[Nätverk Watcher](../network-watcher/network-watcher-monitoring-overview.md) ger scenariobaserade övervakning och diagnostik för scenarier för olika nätverk i Azure.  Den lagrar data i Azure mätvärden och diagnostikfunktionerna för ytterligare analys och fungerar med [hanteringslösningar i logganalys](../log-analytics/log-analytics-azure-networking-analytics.md) för fullständig övervakning av nätverksresurserna.


### <a name="management-solutions"></a>Hanteringslösningar
[Hanteringslösningar](../log-analytics/log-analytics-add-solutions.md) är paketerade uppsättningar av logik som ger inblick i för ett visst program eller tjänst.  De förlitar sig på Log Analytics för att lagra och analysera övervakning information som samlas in.  Hanteringslösningar är tillgängliga från Microsoft och partners som ger övervakning för olika tjänster för Azure och från tredje part. Exempel på övervakningslösningar inkluderar [behållare övervakning](../log-analytics/log-analytics-containers.md) som hjälper dig att visa och hantera dina värdar för behållaren och [Azure SQL Analytics](../log-analytics/log-analytics-azure-sql.md) som samlar in och visualizes prestandamått för SQL Azure-databaser.


## <a name="shared-functionality"></a>Delade funktioner
Följande Azure verktyg innehåller viktiga funktioner till premium-övervaka tjänster.  De delas av flera tjänster så att du kan utnyttja vanliga funktioner och konfigurationer över flera tjänster.

### <a name="alerts"></a>Aviseringar
[Azure-aviseringar](../monitoring-and-diagnostics/monitoring-overview-alerts.md) proaktivt meddelar dig om viktiga villkor och potentiellt vidta åtgärder.  Varningsregler kan utnyttja data från flera källor, inklusive mått och loggar. De använder [åtgärdsgrupper](../monitoring-and-diagnostics/monitoring-action-groups.md) som innehåller en unik uppsättning mottagare och åtgärder som svar på en avisering.  Du kan ha aviseringar starta externa åtgärder med hjälp av webhooks och integrera med dina ITSM verktyg baserat på dina krav.

### <a name="dashboards"></a>Instrumentpaneler
[Azure instrumentpaneler](../azure-portal/azure-portal-dashboards.md) gör att du kan kombinera olika typer av data i ett enda fönster i Azure-portalen och dela med andra Azure-användare.  Du kan till exempel skapa en instrumentpanel som kombinerar paneler som visar ett diagram över mått, en tabell med aktivitetsloggar, en Användningsdiagram från Application Insights och resultatet av en logg sökning i logganalys.

Du kan också exportera logganalys data till [Power BI](https://docs.microsoft.com/power-bi/) dra nytta av ytterligare grafik och göra den tillgänglig för andra inom och utanför organisationen.

### <a name="metrics-explorer"></a>Metrics Explorer
[Mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md) är numeriska värden genereras av Azure-resurser som hjälper dig förstå drift och prestanda för resursen. Du kan skicka mått till logganalys för analys med data från andra källor.



### <a name="activity-logs"></a>Aktivitetsloggar
[Aktivitetsloggar](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) ger information om hur Azure-resurser.  Detta inkluderar sådan information som konfigurationsändringar till resurs, tjänsten hälsa incidenter, rekommendationer i bättre genom att använda resursen och information som rör automatiska åtgärder.  Du kan visa loggar för en viss resurs på en sida i Azure portal eller visa loggarna från flera resurser i aktiviteten loggen Explorer.  Du kan också skicka aktivitetsloggar till logganalys så att de kan analyseras med data som samlas in av hanteringslösningar, agenter på virtuella datorer och andra källor.


## <a name="example-scenarios"></a>Exempelscenarier
Följande är hög nivå exempel som visar hur du kan använda olika verktyg för prestandaövervakning i Azure för olika scenarier.

### <a name="monitoring-a-web-application"></a>Övervaka ett webbprogram
Överväg att ett webbprogram som har distribuerats i Azure med hjälp av App Services, Azure Storage och en SQL-databas.  Du kan starta genom att öppna [mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md) och [aktivitetsloggar](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) för var och en av de enskilda resurserna på sidorna i Azure-portalen.  Det här kan inkludera viktig information, till exempel antalet begäranden till programmet och genomsnittlig svarstid förutom identifierar konfigurationsändringar.

Du kan sedan gå till övervakaren i portalen för att kunna visa mått och loggar för olika resurser tillsammans.  Allteftersom du bestämmer standardparametrar för mätvärdena du [skapa Varningsregler](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) att proaktivt meddela dig när du exempelvis genomsnittlig svarstid ökar över ett tröskelvärde.  För att få en snabb överblick över dina dagliga programprestanda måste skapa du ett Azure instrumentpanelen för att visa diagram över mått som representerar viktiga KPI: er.

Du utför djupare övervakning av programmet, du [konfigureras för Application Insights](../application-insights/quick-monitor-portal.md).  Nu kan du samla in ytterligare data som tillhandahåller ytterligare insikter i drift och prestanda för ditt program.  Application Insights identifierar underliggande relationerna mellan din app komponenter för visuell representation via den [programavbildningen](../application-insights/app-insights-app-map.md) tillsammans med [slutpunkt till slutpunkt spårning](../application-insights/app-insights-transaction-diagnostics.md) att diagnostisera exakt komponent, beroenden eller undantag där ett problem har uppstått.  Du skapar [tillgänglighetstester](../application-insights/app-insights-monitor-web-app-availability.md) att proaktivt testa programmet från olika regioner.  För att utvecklarna, du [aktivera profileraren](../application-insights/enable-profiler-compute.md) så att du kan spåra begäranden och eventuella undantag till en specifik rad kod.  

För att få ytterligare insyn i tjänster som används i ditt program, du lägger till den [SQL Analytics lösning](../log-analytics/log-analytics-azure-sql.md) att samla in ytterligare data till logganalys. Efter en stund som du vill undersöka den grundläggande orsaken för tidsperioder när prestanda på platsen ligger under tröskelvärdet.  Du kan skriva en fråga med logganalys korrelera data användnings- och prestandadata som samlats in av Application Insights med konfiguration och prestanda data i Azure-resurser stöd för ditt program.


### <a name="monitoring-virtual-machines"></a>Övervakning av virtuella datorer
Du har en blandning av Windows och Linux virtuella datorer som körs i Azure.  Du använder Azure-Monitor för att visa [aktivitetsloggar](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) och [värd nivån mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md) och Lägg sedan till den [Azure Diagnostics tillägget](../virtual-machines/linux/tutorial-monitoring.md#install-diagnostics-extension) till de virtuella datorerna för att samla in statistik från gästoperativsystemet.  Du sedan skapa [Varna regler](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) att meddela dig när grundläggande proaktivt mått sådana processoranvändning och minne över tröskelvärden.

Att samla in mer information om virtuella datorer som kör ett affärsprogram du [skapar logganalys-arbetsytan och aktiverar tillägg för virtuell dator](../log-analytics/log-analytics-quick-collect-azurevm.md) på varje dator.  Du konfigurerar [mängd olika datakällor](../log-analytics/log-analytics-data-sources.md) för ditt program och [skapa vyer](../log-analytics/log-analytics-view-designer.md) att rapportera om den dagliga driften och prestanda.  Du sedan [skapa Varningsregler](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) att meddela dig när specifika felhändelser tas emot.  För att kunna övervaka kontinuerligt hälsotillståndet för den installerade agenten måste du lägga till den [Agenthälsa hanteringslösning](../operations-management-suite/oms-solution-agenthealth.md).

Att få ytterligare kunskaper om programmet du [Lägg till beroende agenten](../operations-management-suite/operations-management-suite-service-map-configure.md) till de virtuella datorerna för att lägga till dem i [Tjänstkarta](../operations-management-suite/operations-management-suite-service-map.md).  Den identifierar viktiga processer och identifierar anslutningar mellan datorer med andra tjänster.  Efter en rapporterade avbrott använda Tjänstkarta för dataforensik för att identifiera de specifika datorer som problemet.  Skapa sedan en [frågan på Log Analytics-data](../log-analytics/log-analytics-log-search-new.md) identifiera problemet i framtiden och skapa en aviseringsregel som proaktivt meddelar dig när villkoret har upptäckts.



## <a name="next-steps"></a>Nästa steg
Lär dig mer om

* [Azure Övervakare i ett videoklipp från Ignite 2016](https://myignite.microsoft.com/videos/4977)
* [Komma igång med Azure Övervakare](monitoring-get-started.md)
* [Azure Diagnostics](../azure-diagnostics.md) om du försöker att diagnostisera problem i din molntjänst, virtuell dator, virtuella skala anges eller Service Fabric-programmet.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) om du försöker diagnostiska problem i din App Service Web app.
* [Logga Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) för att analysera insamlade övervakningsdata.
