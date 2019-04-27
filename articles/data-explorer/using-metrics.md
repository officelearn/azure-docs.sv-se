---
title: Övervaka prestanda, hälsotillstånd och användning med mått i Azure Data Explorer
description: Lär dig hur du använder Azure Data Explorer mått för att övervaka klustrets prestanda, hälsa och användning.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: a9c9f4d827d21c374bebba9d39e33b0bcad8a83e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60826806"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Övervaka prestanda, hälsotillstånd och användning med mått i Azure Data Explorer

Azure Data Explorer är en snabb, fullständigt hanterad dataanalystjänst för realtidsanalys av stora mängder data som strömmar från program, webbplatser, IoT-enheter med mera. För att använda Azure Data Explorer skapar du först ett kluster och skapar en eller flera databaser i klustret. Sedan matar du in (läser in) data i databasen så att du kan köra frågor mot den. Azure Data Explorer mätvärden är viktiga indikatorer om hälsotillstånd och prestanda för klusterresurserna. Använda mått som beskrivs i den här artikeln att övervaka Azure Data Explorer klusterhälsa och prestanda i ditt specifika scenario som fristående mått. Du kan också använda mått som grund för operativa [Azure-instrumentpaneler](/azure/azure-portal/azure-portal-dashboards) och [Azure Alerts](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>Nödvändiga komponenter

* Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Skapa en [kluster och databas](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="using-metrics"></a>Med hjälp av mätvärden

I Datautforskaren i Azure-kluster väljer **mått** att öppna fönstret mått och starta analys på ditt kluster.

![Välja mått](media/using-metrics/select-metrics.png)

I fönstret mått:

![Mått-fönstret](media/using-metrics/metrics-pane.png)

1. Om du vill skapa ett måttdiagram, Välj **mått** namn och relevanta **aggregering** per mått som beskrivs nedan. Den **Resource** och **mått Namespace** datumväljare är förvalda i Datautforskaren i Azure-klustret.

    **Mått** | **Enhet** | **Aggregering** | **Metrisk beskrivning**
    |---|---|---|---|
    | Cache-användning | Procent | AVG, Max, Min | Procentandel tilldelade cache-resurser som används av klustret. Cache refererar till storleken på SSD som allokerats för användaraktivitet enligt definierade cache-principen. En genomsnittlig cache-användning på 80% eller mindre är en hållbar status för ett kluster. Om den genomsnittliga cache-användningen är över 80%, klustret bör vara [skalas upp](manage-cluster-scale-up.md) till en storage optimerat prisnivå eller [utskalad](manage-cluster-scale-out.md) till fler instanser. Du kan också anpassa princip (färre dagar i cacheminnet). Om cachen användningen är över 100%, storleken på data som ska cachelagras enligt principen för cachelagring är större som den totala storleken på cacheminnet på klustret. |
    | Processor | Procent | AVG, Max, Min | Procentandel tilldelade beräkningsresurser som används av datorer i klustret. En Genomsnittlig CPU med 80% eller mindre är hållbar för ett kluster. Det högsta värdet för processor är 100%, vilket innebär att det finns inga fler beräkningsresurser för att bearbeta data. När ett kluster inte presterar bra, kontrollerar du det maximala värdet för processor och se om det finns specifika CPU: er som har blockerats. |
    | Händelser som bearbetas (för Event Hubs) | Antal | Max, Min, Sum | Totalt antal händelser läsa från event hubs och bearbetas av klustret. Händelserna är uppdelade i avvisade händelser och händelser som accepteras av kluster-motorn. |
    | Datainmatningssvarstid | Sekunder | AVG, Max, Min | Svarstid för data som samlas in från den tidpunkt som data togs emot i klustret tills den är redo för frågan. Datainmatning svarstiden beror på inmatning-scenario. |
    | Datainmatning resultat | Antal | Antal | Totalt antal åtgärder för inmatning som misslyckades och lyckades. Använd **gäller dela** att skapa buckets att lyckas och misslyckas resultat och analysera dimensioner (**värdet** > **Status**).|
    | Datainmatning användning | Procent | AVG, Max, Min | Den procentandel av verkliga resurser som används för att mata in data från de totala resurserna som allokerats i principen kapacitet att utföra inmatning. Standardprincipen för kapacitet är längre än 512 samtidiga inmatning åtgärder eller 75% av klusterresurserna investerat i inmatning. Genomsnittlig inmatning användningen med 80% eller mindre är ett hållbar tillstånd för ett kluster. Högsta värdet för inmatning användning är 100%, vilket innebär att alla möjligheten för inmatning av klustret används och kan resultera i en kö för inmatning. |
    | Datainmatning volymen (i MB) | Antal | Max, Min, Sum | Den totala storleken på data som matas in i klustret (i MB) före komprimering. |
    | Hålla igång | Antal | Medel | Spårar svarstiden för klustret. Ett fullständigt dynamiska kluster returnerar värdet 1 och ett blockerade eller frånkopplat kluster returnerar 0. |
    | Frågevaraktighet | Sekunder | Antal, genomsnitt, Min, Max, Sum | Total tid tills frågeresultat tas emot (omfattar inte Nätverksfördröjningen). |
    | | | |

    Ytterligare information angående [klustermått för Azure Data Explorer som stöds](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)

2. Välj den **Lägg till mått** för att visa flera mått som ritas i samma diagram.
3. Välj den **+ nytt diagram** för att visa flera diagram i en vy.
4. Använd väljaren för tid för att ändra tidsintervallet (standard: senaste 24 timmarna).
5. Använd [ **Lägg till filter** och **gäller dela** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) för mått med dimensioner.
6. Välj **fäst på instrumentpanelen** att lägga till din diagramkonfigurationen till instrumentpaneler så att du kan visa den igen.
7. Ange **ny aviseringsregel** att visualisera dina mått baserat på set-villkor. Ny aviseringsregel tas din målresursen, mått, dela och filterdimensioner från ditt diagram. Ändra de här inställningarna i den [varningsregel skapa fönstret](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Mer information om hur du använder den [Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started).


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabbstart: Fråga efter data i Azure Data Explorer](web-query-data.md)
