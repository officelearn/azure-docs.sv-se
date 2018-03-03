---
title: "Övervaka Azure-program och resurser | Microsoft Docs"
description: "Översikt över Microsoft-tjänster och funktioner som bidrar till en fullständig övervakning strategi för dina Azure-tjänster och program."
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
ms.openlocfilehash: d8da175a551f7c589c313b2289b2a0209dbd2b56
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="monitoring-azure-applications-and-resources"></a>Övervaka Azure-program och resurser

Övervakning är att samla in och analysera data för att avgöra prestanda, hälsotillstånd och tillgänglighet för dina affärsprogram och de resurser som den är beroende av. En effektiv övervakning strategi hjälper dig att förstå den detaljerade driften av komponenterna i ditt program. Det hjälper dig också att öka din drifttid genom att meddela dig om kritiska problem så att du kan lösa dem innan de hunnit bli problem.

Azure innehåller flera tjänster som utför en specifik roll eller aktivitet individuellt i övervakningsutrymmet. Tillsammans levererar tjänsterna en heltäckande lösning för att samla in, analysera och fungerar på telemetri från ditt program och Azure-resurser som stöder dessa. De kan också fungera om du vill övervaka kritiska lokala resurser för att tillhandahålla en hybrid övervakning miljö. Det första steget i att utveckla en fullständig övervakning strategi för ditt program är att förstå de verktyg och data som är tillgängliga. 

Följande diagram visar en översikt över de komponenter som arbetar tillsammans för att tillhandahålla övervakning av Azure-resurser. Följande avsnitt beskriver de här komponenterna och ger länkar till detaljerad teknisk information.

![Övervakningsöversikt](media/monitoring-overview/overview.png)

## <a name="basic-monitoring"></a>Grundläggande övervakning
Grundläggande övervakning innehåller de grundläggande, obligatoriska övervakning över Azure-resurser. Dessa tjänster kräver minimal konfiguration och samlar in core telemetri som premium övervakningstjänster använder.    

### <a name="azure-monitor"></a>Azure Monitor
[Azure-Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) aktiverar grundläggande övervakning för Azure-tjänsten genom att tillåta mängden [mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md), [aktivitetsloggar](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), och [diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Aktivitetsloggen anger du när nya resurser skapas eller ändras. 

Mått är tillgängliga som ger prestandastatistik för olika resurser och även operativsystemet på en virtuell dator. Du kan visa dessa data med en av de olika utforskarfönster i Azure-portalen, skickar den till Azure Log Analytics för trender och detaljerad analys eller skapa Varningsregler proaktivt meddelar om kritiska problem.

### <a name="service-health"></a>Service Health
Hälsotillståndet för programmet är beroende av Azure-tjänster som den är beroende av. [Tjänstens hälsa för Azure](../service-health/service-health-overview.md) identifierar eventuella problem med Azure-tjänster som kan påverka ditt program. Tjänstens hälsa kan du planera för schemalagt underhåll.

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](../advisor/advisor-overview.md) ständigt övervakar din resurs konfiguration och användning telemetri. Sedan får du anpassade rekommendationer baserat på bästa praxis. Följa dessa rekommendationer hjälper dig att förbättra prestanda, säkerhet och tillgänglighet för de resurser som har stöd för dina program.


## <a name="premium-monitoring-services"></a>Premium övervakningstjänster
Följande Azure-tjänster innehåller omfattande funktioner för att samla in och analysera övervakningsdata. Dessa tjänster bygger på grundläggande övervakning och dra nytta av vanliga funktioner i Azure. De ger kraftfulla analytics insamlade data för att ge unik insikter om dina program och infrastruktur. De presentera data i samband med scenarier som är avsedda för olika målgrupper.

### <a name="application-insights"></a>Application Insights
Du kan använda [Azure Application Insights](http://azure.microsoft.com/documentation/services/application-insights) om den finns i molnet eller lokalt, kan du övervaka tillgänglighet, prestanda och användning av ditt program. 

Du kan uppnå djupa insikter av instrumentering programmet att fungera med Application Insights. Sedan kan du snabbt identifiera och diagnostisera fel utan att vänta på en användare att rapportera dem. Du kan göra välgrundade val om underhåll och förbättringar för ditt program med den information som du samlar in. 

Application Insights har omfattande verktyg för att interagera med data som samlas in. Application Insights lagras data i en gemensam databas. Det kan dra nytta av delade funktioner som aviseringar, instrumentpaneler och djupanalys med Log Analytics-frågespråket.

### <a name="log-analytics"></a>Log Analytics
[Logga Analytics](http://azure.microsoft.com/documentation/services/log-analytics) betydelse i Azure övervakning genom att samla in data från en mängd resurser i en databas. Du kan analysera data med hjälp av ett kraftfullt frågespråk. 

Application Insights och Azure Security Center kan du lagra data i logganalys data lagrar och använder dess analytics-motorn. Data är data som samlas in från Azure-Monitor, hanteringslösningar och agenter som installerats på virtuella datorer i molnet eller lokalt. Funktionen delade hjälper dig att bilda en komplett bild av din miljö. 


### <a name="service-map"></a>Tjänstkarta
[Tjänstkarta](../operations-management-suite/operations-management-suite-service-map.md) ger inblick i IaaS-miljö genom att analysera virtuella datorer med olika processer och beroenden på andra datorer och externa processer. Det integreras händelser, prestandadata och lösningar för hantering i logganalys. Du kan sedan visa dessa data i kontexten för varje dator och dess relation till resten av miljön. 

Tjänstkarta liknar [programavbildningen i Application Insights](../application-insights/app-insights-app-map.md). Den fokuserar på infrastrukturkomponenter som har stöd för dina program.

### <a name="network-watcher"></a>Network Watcher
[Nätverk Watcher](../network-watcher/network-watcher-monitoring-overview.md) ger scenariobaserade övervakning och diagnostik för scenarier för olika nätverk i Azure. Data lagras i Azure mätvärden och diagnostikfunktionerna för ytterligare analys. Det fungerar med följande lösningar för övervakning av olika områden i nätverket:
* [Network Performance Monitor (NPM)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/): ett molnbaserade nätverk övervakningslösning som övervakar anslutning över offentliga moln, Datacenter och lokala miljöer.
* [Övervakare för ExpressRoute](https://azure.microsoft.com/en-in/blog/monitoring-of-azure-expressroute-in-preview/): en NPM-funktion som övervakar slutpunkt till slutpunkt-anslutning och prestanda över Azure ExpressRoute-kretsar.
* Trafik Analytics: En molnbaserad lösning som ger inblick i användar- och programaktivitet på nätverket molnet.
* [DNS-Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-dns): en lösning som innehåller säkerhet, prestanda och åtgärder som rör insikter baserat på DNS-servrarna.

### <a name="management-solutions"></a>Hanteringslösningar
[Hanteringslösningar](../log-analytics/log-analytics-add-solutions.md) är paketerade uppsättningar av logik som ger inblick i för ett visst program eller tjänst. De förlitar sig på Log Analytics för att lagra och analysera övervakningsdata som de samlar in. 

Av hanteringslösningar som är tillgängliga från Microsoft och partners för att tillhandahålla övervakning för olika Azure och tjänster från tredje part. Exempel på övervakningslösningar:
* [Behållaren övervakning](../log-analytics/log-analytics-containers.md), som hjälper dig att visa och hantera behållare-värdar.
* [Azure SQL-Analytics](../log-analytics/log-analytics-azure-sql.md), som samlar in och visualizes prestandastatistik för Azure SQL-databaser.


## <a name="shared-functionality"></a>Delade funktioner
Följande Azure verktyg innehåller viktiga funktioner till premium-övervaka tjänster. Flera tjänster dela dem, så du kan dra nytta av vanliga funktioner och konfigurationer över flera tjänster.

### <a name="alerts"></a>Aviseringar
[Azure-aviseringar](../monitoring-and-diagnostics/monitoring-overview-alerts.md) proaktivt meddelar dig om viktiga villkor och potentiellt vidta åtgärder. Varningsregler kan använda data från flera källor, inklusive mått och loggar. De använder [åtgärdsgrupper](../monitoring-and-diagnostics/monitoring-action-groups.md), som innehåller en unik uppsättning mottagare och åtgärder som svar på en avisering. Du kan ha aviseringar startar externa åtgärder med hjälp av webhooks och integrera med dina ITSM verktyg baserat på dina krav.

### <a name="dashboards"></a>Instrumentpaneler
Du kan använda [Azure instrumentpaneler](../azure-portal/azure-portal-dashboards.md) kombinera olika typer av data till ett enda fönster i Azure-portalen. Därefter kan du dela instrumentpanelen med andra Azure-användare. 

Du kan till exempel skapa en instrumentpanel som kombinerar:
- Paneler som visar ett diagram över mått
- En tabell med aktivitetsloggar
- Ett användningsdiagram från Application Insights
- Utdata från en logg sökning i logganalys

Du kan också exportera logganalys data till [Power BI](https://docs.microsoft.com/power-bi/). Det, kan du dra nytta av ytterligare visualiseringar. Du kan också göra data tillgängliga för andra inom och utanför organisationen.

### <a name="metrics-explorer"></a>Metrics Explorer
[Mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md) är numeriska värden som genererats av en Azure-resurs som hjälper dig att förstå drift och prestanda för resursen. Genom att använda Metrics Explorer kan skicka du mått till logganalys för analys med data från andra källor.



### <a name="activity-log"></a>Aktivitetslogg
[Aktivitetsloggen](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) ger data om driften av en Azure-resurs. Informationen omfattar:
- Konfigurationsändringar till resursen.
- Tjänstens hälsa incidenter.
- Rekommendationer för bättre genom att använda resursen.
- Information relaterad till automatiska åtgärder. 

Du kan visa loggar för en viss resurs på en sida i Azure-portalen. Eller du kan visa loggar från flera resurser i aktiviteten loggen Explorer. 

Du kan också skicka aktivitetsloggar till logganalys. Där kan analysera du loggar med hjälp av data som samlas in av hanteringslösningar, agenter på virtuella datorer och andra källor.


## <a name="example-scenarios"></a>Exempelscenarier
Följande är exempel på hög nivå som illustrerar hur du kan använda olika verktyg för prestandaövervakning i Azure för olika scenarier.

### <a name="monitoring-a-web-application"></a>Övervaka ett webbprogram
Överväg att ett webbprogram som har distribuerats i Azure via Azure App Service, Azure Storage och en SQL-databas. Du startar genom att öppna [mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md) och [aktivitetsloggar](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) för dessa resurser på sina sidor i Azure-portalen. Du leta efter viktig information, till exempel antalet begäranden till programmet och genomsnittlig svarstid. Du kan också identifiera ändringar i konfigurationen.

Du gå till övervakaren i portalen för att kunna visa mått och loggar för olika resurser tillsammans. Allteftersom du bestämmer standardparametrar för mätvärdena du [skapa Varningsregler](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md). Reglerna meddela att dig när du exempelvis genomsnittlig svarstid ökar över ett tröskelvärde. För att få en snabb överblick över dina dagliga programprestanda kan skapa du ett Azure instrumentpanelen för att visa diagram över mått som representerar viktiga KPI: er.

Du utför djupare övervakning av programmet, du [konfigureras för Application Insights](../application-insights/quick-monitor-portal.md). Nu kan du samla in ytterligare data som ger ytterligare insikter i drift och prestanda för ditt program. Application Insights identifierar de underliggande relationerna mellan komponenter i din app. Den kan användas för visuell representation via [programavbildningen](../application-insights/app-insights-app-map.md) tillsammans med [slutpunkt till slutpunkt spårning](../application-insights/app-insights-transaction-diagnostics.md) att diagnostisera exakt komponent, beroenden eller undantag där ett problem har uppstått. 

Du skapar [tillgänglighetstester](../application-insights/app-insights-monitor-web-app-availability.md) att proaktivt testa programmet från olika regioner. För att utvecklarna, du [aktivera profileraren](../application-insights/enable-profiler-compute.md) så att du kan spåra begäranden och eventuella undantag till en specifik rad kod. För att få ytterligare insyn i tjänster som används i ditt program, du lägger till den [SQL Analytics lösning](../log-analytics/log-analytics-azure-sql.md) att samla in ytterligare data i logganalys. 

Efter en stund som du vill undersöka grundorsaken till punkter när prestanda på platsen har fallit under ett tröskelvärde. Du kan skriva en fråga med hjälp av logganalys. Det hjälper dig att korrelera användnings- och prestandadata data som samlas in av Application Insights med konfigurationen och prestandadata över Azure-resurser som stöd för ditt program.


### <a name="monitoring-virtual-machines"></a>Övervakning av virtuella datorer
Du har en blandning av Windows och Linux virtuella datorer som körs i Azure. Du använder Azure-Monitor för att visa [aktivitetsloggar](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) och [värdnivå mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md). Du lägger till den [Azure Diagnostics tillägget](../virtual-machines/linux/tutorial-monitoring.md#install-diagnostics-extension) till de virtuella datorerna för att samla in statistik från gästoperativsystemet. Skapa sedan [Varna regler](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) att proaktivt meddela dig om grundläggande mått som processoranvändning och minne korsar tröskelvärden.

Att samla in mer information om virtuella datorer som kör ett affärsprogram du [skapar logganalys-arbetsytan och aktiverar tillägg för virtuell dator](../log-analytics/log-analytics-quick-collect-azurevm.md) på varje dator. Du konfigurerar den [mängd olika datakällor](../log-analytics/log-analytics-data-sources.md) för ditt program och [skapa vyer](../log-analytics/log-analytics-view-designer.md) att rapportera om den dagliga driften och prestanda. Du sedan [skapa Varningsregler](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) att meddela dig när specifika felhändelser tas emot. 

Om du vill övervaka kontinuerligt hälsotillståndet för den installerade agenten måste du lägga till den [Agenthälsa hanteringslösning](../operations-management-suite/oms-solution-agenthealth.md). Att få ytterligare information till programmet, du [Lägg till beroende agenten](../operations-management-suite/operations-management-suite-service-map-configure.md) till de virtuella datorerna för att lägga till dem i [Tjänstkarta](../operations-management-suite/operations-management-suite-service-map.md). Tjänstkarta identifierar viktiga processer och identifierar anslutningar mellan datorer med andra tjänster. 

Efter en rapporterade avbrott använda Tjänstkarta för dataforensik för att identifiera de specifika datorer som problemet. Skapa sedan en [frågan på logganalys data](../log-analytics/log-analytics-log-search-new.md) att identifiera problemet i framtiden. Och du skapar en aviseringsregel som proaktivt meddelar dig när villkoret har identifierats.



## <a name="next-steps"></a>Nästa steg
Läs mer om:

* [Azure Övervakare i ett videoklipp från Ignite 2016](https://myignite.microsoft.com/videos/4977).
* [Komma igång med Azure-Monitor](monitoring-get-started.md).
* [Azure Diagnostics](../azure-diagnostics.md) om du försöker att diagnostisera problem i din tjänst i molnet, virtuell dator, virtuella datorns skaluppsättning eller Azure Service Fabric-programmet.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) om du försöker att diagnostisera problem i din App Service webbapp.
* [Logga Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) för att analysera insamlade övervakningsdata.
