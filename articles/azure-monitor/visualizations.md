---
title: Visualisera data från Azure Monitor | Microsoft Docs
description: Innehåller en sammanfattning av de tillgängliga metoderna för att visualisera mått och loggar data som lagras i Azure Monitor.
author: bwren
manager: carmonm
editor: ''
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/24/2018
ms.author: bwren
ms.openlocfilehash: cea5cb924914cff1195520d6bd28a8b62fce8698
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264418"
---
# <a name="visualizing-data-from-azure-monitor"></a>Visualisera data från Azure Monitor
Den här artikeln innehåller en sammanfattning av de tillgängliga metoderna för att visualisera logg- och måttdata som lagras i Azure Monitor.

Visualiseringar, till exempel tabeller och diagram kan hjälpa dig att analysera övervakningsdata för att gå nedåt i problem och identifiera mönster. Beroende på vilket verktyg som du använder kanske du också alternativet för att dela visualiseringar med andra användare i och utanför din organisation.

## <a name="azure-dashboards"></a>Azure-instrumentpaneler
[Azure-instrumentpaneler](../azure-portal/azure-portal-dashboards.md) är den primära dashboarding tekniken för Azure. De är särskilt användbart i att tillhandahålla enda glasruta över dina Azure-infrastruktur och tjänster så att du kan snabbt identifiera viktiga problem.

![Instrumentpanel](media/visualizations/dashboard.png)

### <a name="advantages"></a>Fördelar
- Djup integrering i Azure. Visualiseringar kan fästas på instrumentpaneler från flera Azure sidor som mått analytics, logganalys och Application Insights.
- Har stöd för både mått och loggar.
- Kombinera data från flera källor, inklusive utdata från [måttutforskaren](../azure-monitor/platform/metrics-charts.md), [Log Analytics-frågor](../azure-monitor/log-query/log-query-overview.md), och [mappar](../azure-monitor/app/app-map.md) och tillgänglighet i Application Insights.
- Alternativet för personliga eller delade instrumentpaneler. Integrerad med Azure [Rollbaserad autentisering (RBAC)](../role-based-access-control/overview.md).
- Automatisk uppdatering. Mått-uppdatering är beroende av tidsintervall med minst fem minuter. Loggar uppdatera på en minut.
- Parametriserade mått instrumentpaneler med tidsstämpel och anpassade parametrar.
- Layoutalternativ för flexibel.
- Helskärmsläge.


### <a name="limitations"></a>Begränsningar
- Begränsad kontroll över log visualiseringar utan stöd för datatabeller. Totalt antal dataserier är begränsad till 10 med ytterligare dataserien grupperade under en _andra_ bucket.
- Inga anpassade parametrar har stöd för logaritmiska diagram.
- Logaritmiska diagram är begränsade till senaste 30 dagarna.
- Logaritmiska diagram kan bara fästas på delade instrumentpaneler.
- Inga interaktivitet med data från instrumentpanelen.
- Begränsad sammanhangsberoende nedåt.

## <a name="azure-monitor-views"></a>Azure Monitor-vyer
[Vyer i Azure Monitor](../log-analytics/log-analytics-view-designer.md) kan du skapa anpassade visualiseringar med loggdata. De används av [övervakningslösningar](insights/solutions.md) att presentera information som samlas in.

![Visa](media/visualizations/view.png)

### <a name="advantages"></a>Fördelar
- Omfattande visualiseringar för loggdata.
- Exportera och importera vyer för att överföra dem till andra resursgrupper och prenumerationer.
- Integrerar Log Analytics Hanteringsmodellen med arbetsytor och övervakar lösningar.
- [Filter](platform/view-designer-filters.md) för anpassade parametrar.
- Interaktiv, har stöd för flera nivåer drill-i (vy som går till en annan vy)

### <a name="limitations"></a>Begränsningar
- Stöder loggar, men inte mått.
- Inga personliga vyer. Tillgänglig för alla användare med åtkomst till arbetsytan.
- Ingen automatisk uppdatering.
- Begränsade layoutalternativ.
- Inget stöd för att fråga över flera arbetsytor eller Application Insights-program.
- Frågor begränsas i storlek till 8MB och fråga körningstid 110 sekunder.



## <a name="application-insights-workbooks"></a>Application Insights-arbetsböcker
[Arbetsböcker](../azure-monitor/app/usage-workbooks.md) är interaktiva dokument som ger djupare insikter om dina data, undersökningar och samarbete i teamet. Specifika exempel där arbetsböcker är användbara felsöker guider och incident postmortem.

![Arbetsbok](media/visualizations/workbook.png)

### <a name="advantages"></a>Fördelar
- Har stöd för både mått och loggar.
- Har stöd för parametrar som aktiverar interaktiva rapporter som du väljer ett element i en tabell där kommer dynamiskt uppdatera associerade diagram och visualiseringar.
- Dokument-liknande flöde.
- Alternativ för personliga eller delade arbetsböcker.
- Enkelt, samarbetsfunktioner-vänlig redigeringen.
- Mallar stöder offentliga GitHub-baserad mallgalleriet.

### <a name="limitations"></a>Begränsningar
- Ingen automatisk uppdatering.
- Ingen kompakta layout som instrumentpaneler, vilket gör det mindre användbart som en enda glasruta arbetsböcker. Avsedd mer för att tillhandahålla djupare insikter.


## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) är särskilt användbart för att skapa företagsanpassat instrumentpaneler och rapporter samt rapporter analysera långsiktig KPI-trender. Du kan [importerar resultatet av en loggfråga](../log-analytics/log-analytics-powerbi.md) till en Power BI-datauppsättning så att du kan dra nytta av dess funktioner som kombinerar data från olika källor och dela rapporter på webben och mobila enheter.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Fördelar
- Utförliga visuella effekter.
- Omfattande, interaktiva inklusive Zooma in och korsfiltrering.
- Lätt att dela i hela organisationen.
- Integrering med andra data från flera datakällor.
- Bättre prestanda med resultat som cachelagrats i en kub.


### <a name="limitations"></a>Begränsningar
- Stöder loggar, men inte mått.
- Ingen integrering med Azure. Det går inte att hantera instrumentpaneler och -modeller via Azure Resource Manager.
- Frågeresultat måste importeras till Power BI-modellen för att konfigurera. Begränsningen av storlek och uppdatera.
- Begränsad datauppdatering av åtta gånger per dag.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) är en öppen plattform som är perfekt i driftsinstrumentpaneler. Det är särskilt användbart för att identifiera och isolera och sorterar operativa incidenter. Du kan lägga till [Grafana Azure Monitor-plugin-programmet för datakällans](../azure-monitor/platform/grafana-plugin.md) till din Azure-prenumeration att visualisera dina data i Azure-mått.

![Grafana](media/visualizations/grafana.png)

### <a name="advantages"></a>Fördelar
- Utförliga visuella effekter.
- Omfattande ekosystem av datakällor.
- Data interaktivitet, inklusive Zooma in.
- Har stöd för parametrar.

### <a name="limitations"></a>Begränsningar
- Stöder mätvärden men loggar inte.
- Ingen integrering med Azure. Det går inte att hantera instrumentpaneler och -modeller via Azure Resource Manager.
- Kostnader för ytterligare Grafana infrastruktur eller extra kostnad för Grafana moln.


## <a name="build-your-own-custom-application"></a>Skapa dina egna anpassade program
Du kan komma åt data i loggen och måttdata i Azure Monitor via sina API med valfri REST-klient, där du kan skapa egna anpassade webbplatser och program.

### <a name="advantages"></a>Fördelar
- Full flexibilitet i Användargränssnittet, visualisering, interaktivitet och funktioner.
- Kombinera mått och loggar data med andra datakällor.

### <a name="disadvantages"></a>Nackdelar
- Betydande engineering arbete som krävs.


## <a name="next-steps"></a>Nästa steg
- Lär dig mer om den [data som samlas in av Azure Monitor](platform/data-collection.md).
- Lär dig mer om [Azure-instrumentpaneler](../azure-portal/azure-portal-dashboards.md).
- Lär dig mer om [vyer i Azure Monitor](platform/view-designer.md).
- Lär dig mer om [arbetsböcker i Application Insights](../azure-monitor/app/usage-workbooks.md).
- Lär dig mer om [importera loggdata till Power BI](../azure-monitor/platform/powerbi.md).
- Lär dig mer om den [Grafana Azure Monitor-plugin-programmet för datakällans](../azure-monitor/platform/grafana-plugin.md).

