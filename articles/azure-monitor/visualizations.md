---
title: Visualisera data från Azure Monitor | Microsoft-dokument
description: Innehåller en sammanfattning av tillgängliga metoder för att visualisera mått och logga data som lagras i Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 877616f6fd31bdfbe193bd8f03efb3f79317ad42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535377"
---
# <a name="visualizing-data-from-azure-monitor"></a>Visualisera data från Azure Monitor
Den här artikeln innehåller en sammanfattning av tillgängliga metoder för att visualisera logg- och måttdata som lagras i Azure Monitor.

Visualiseringar som diagram och diagram kan hjälpa dig att analysera dina övervakningsdata för att öka detaljnivån i problem och identifiera mönster. Beroende på vilket verktyg du använder kan du också ha möjlighet att dela visualiseringar med andra användare inom och utanför organisationen.

## <a name="workbooks"></a>Arbetsböcker
[Arbetsböcker](../azure-monitor/app/usage-workbooks.md) är interaktiva dokument som ger djup inblick i dina data, undersökningar och samarbeten inom teamet. Specifika exempel där arbetsböcker är användbara är felsökningsguider och incidentpostmortem.

![Arbetsbok](media/visualizations/workbook.png)

### <a name="advantages"></a>Fördelar
- Stöder både mått och loggar.
- Stöder parametrar som aktiverar interaktiva rapporter där om du väljer ett element i en tabell uppdateras associerade diagram och visualiseringar dynamiskt.
- Dokumentliknande flöde.
- Alternativ för personliga eller delade arbetsböcker.
- Enkel, samarbetsvänlig författarupplevelse.
- Mallar stöder offentligt GitHub-baserat mallgalleri.

### <a name="limitations"></a>Begränsningar
- Ingen automatisk uppdatering.
- Ingen tät layout som instrumentpaneler, vilket gör arbetsböcker mindre användbara som en enda glasruta. Tänkt mer för att ge djupare insikter.


## <a name="azure-dashboards"></a>Azure-instrumentpaneler
[Azure-instrumentpaneler](../azure-portal/azure-portal-dashboards.md) är den primära instrumentpanelstekniken för Azure. De är särskilt användbara när du tillhandahåller en enda glasruta över din Azure-infrastruktur och dina tjänster så att du snabbt kan identifiera viktiga problem.

![Instrumentpanel](media/visualizations/dashboard.png)

### <a name="advantages"></a>Fördelar
- Djup integrering i Azure. Visualiseringar kan fästas på instrumentpaneler från flera Azure-sidor, inklusive Mått Explorer, Logganalys och Application Insights.
- Stöder både mått och loggar.
- Kombinera data från flera källor, inklusive utdata från [statistikutforskaren,](platform/metrics-charts.md) [loggfrågor](log-query/log-query-overview.md)och [kartor](app/app-map.md) och tillgänglighet i Application Insights.
- Alternativ för personliga eller delade instrumentpaneler. Integrerad med [Azure-rollbaserad autentisering (RBAC)](../role-based-access-control/overview.md).
- Automatisk uppdatering. Måttuppdatering beror på tidsintervall med minst fem minuter. Loggar uppdatera varje timme, med ett manuellt uppdateringsalternativ på begäran genom att klicka på "uppdatera"-ikonen på en viss visualisering eller genom att uppdatera hela instrumentpanelen.
- Parametrized metrics instrumentpaneler med tidsstämpel och anpassade parametrar.
- Flexibla layoutalternativ.
- Helskärmsläge.


### <a name="limitations"></a>Begränsningar
- Begränsad kontroll över loggvisualiseringar utan stöd för datatabeller. Totalt antal dataserier är begränsat till 10 med ytterligare dataserier grupperade under en _annan_ bucket.
- Inget anpassat parameterstöd för loggdiagram.
- Loggdiagram är begränsade till de senaste 30 dagarna.
- Loggdiagram kan bara fästas på delade instrumentpaneler.
- Ingen interaktivitet med instrumentpanelsdata.
- Begränsad kontextuell detaljgranskning.


## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) är särskilt användbart för att skapa affärscentrerade instrumentpaneler och rapporter, samt rapporter som analyserar långsiktiga KPI-trender. Du kan [importera resultatet av en loggfråga](platform/powerbi.md) till en Power BI-datauppsättning så att du kan dra nytta av dess funktioner, till exempel kombinera data från olika källor och dela rapporter på webben och mobila enheter.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Fördelar
- Omfattande visualiseringar.
- Omfattande interaktivitet inklusive inzoom och korsfiltrering.
- Lätt att dela i hela organisationen.
- Integrering med andra data från flera datakällor.
- Bättre prestanda med resultat som cachelagras i en kub.


### <a name="limitations"></a>Begränsningar
- Stöder loggar men inte mått.
- Ingen Azure-integrering. Det går inte att hantera instrumentpaneler och modeller via Azure Resource Manager.
- Frågeresultat måste importeras till Power BI-modellen för att konfigurera. Begränsning av resultatstorlek och uppdatering.
- Begränsad datauppdatering på åtta gånger per dag.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) är en öppen plattform som utmärker sig i operativa instrumentpaneler. Det är särskilt användbart för att upptäcka och isolera och triaging operativa incidenter. Du kan lägga till [Plugin-programmet För Grafana Azure Monitor-datakälla i](platform/grafana-plugin.md) din Azure-prenumeration så att den visualiserar dina Azure-måttdata.

![Grafana](media/visualizations/grafana.png)

### <a name="advantages"></a>Fördelar
- Omfattande visualiseringar.
- Rika ekosystem av datakällor.
- Datainteraktivitet inklusive zooma in.
- Stöder parametrar.

### <a name="limitations"></a>Begränsningar
- Ingen Azure-integrering. Det går inte att hantera instrumentpaneler och modeller via Azure Resource Manager.
- Kostnad för att stödja ytterligare Grafana-infrastruktur eller extra kostnad för Grafana Cloud.


## <a name="build-your-own-custom-application"></a>Skapa ditt eget anpassade program
Du kan komma åt data i logg- och måttdata i Azure Monitor via deras API med valfri REST-klient, vilket gör att du kan skapa dina egna anpassade webbplatser och program.

### <a name="advantages"></a>Fördelar
- Fullständig flexibilitet i användargränssnitt, visualisering, interaktivitet och funktioner.
- Kombinera mått och logga data med andra datakällor.

### <a name="disadvantages"></a>Nackdelar
- Betydande tekniska insatser krävs.


## <a name="azure-monitor-views"></a>Azure-övervakarvyer

> [!IMPORTANT]
> Vyer håller på att vara inaktuella. Se [Azure Monitor-vydesignern till övergångsguide](platform/view-designer-conversion-overview.md) för arbetsböcker för vägledning om hur du konverterar vyer till arbetsböcker.

[Med vyer i Azure Monitor](platform/view-designer.md) kan du skapa anpassade visualiseringar med loggdata. De används genom [att övervaka lösningar](insights/solutions.md) för att presentera de data de samlar in.


![Visa](media/visualizations/view.png)

### <a name="advantages"></a>Fördelar
- Omfattande visualiseringar för loggdata.
- Exportera och importera vyer för att överföra dem till andra resursgrupper och prenumerationer.
- Integreras i Azure Monitor-hanteringsmodell med arbetsytor och övervakningslösningar.
- [Filter](platform/view-designer-filters.md) för anpassade parametrar.
- Interaktiv, stöder flera nivåer drill-in (vy som borrar i en annan vy)

### <a name="limitations"></a>Begränsningar
- Stöder loggar men inte mått.
- Inga personliga åsikter. Tillgänglig för alla användare med åtkomst till arbetsytan.
- Ingen automatisk uppdatering.
- Begränsade layoutalternativ.
- Inget stöd för att fråga över flera arbetsytor eller Programinsiktsprogram.
- Frågor är begränsade i svarsstorlek till 8 MB och frågekörningstid på 110 sekunder.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [de data som samlas in av Azure Monitor](platform/data-platform.md).
- Lär dig mer om [Azure-instrumentpaneler](../azure-portal/azure-portal-dashboards.md).
- Lär dig mer om [vyer i Azure Monitor](platform/view-designer.md).
- Läs mer om [arbetsböcker](../azure-monitor/app/usage-workbooks.md).
- Lär dig mer om [importloggdata till Power BI](../azure-monitor/platform/powerbi.md).
- Lär dig mer om plugin:en av [Grafana Azure Monitor-datakällan](../azure-monitor/platform/grafana-plugin.md).

