---
title: Övervaka Azure Datautforskaren prestanda, hälsa & användning med mått
description: Lär dig hur du använder Azure Datautforskaren mått för att övervaka klustrets prestanda, hälsa och användning.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 1319b8cd6ac8a0eb83381c24bcde9996458e47a7
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560312"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Övervaka Azure Datautforskaren prestanda, hälsa och användning med mått

Azure Data Explorer är en snabb, fullständigt hanterad dataanalystjänst för realtidsanalys av stora mängder data som strömmar från program, webbplatser, IoT-enheter med mera. För att använda Azure Data Explorer skapar du först ett kluster och skapar en eller flera databaser i klustret. Sedan matar du in (läser in) data i databasen så att du kan köra frågor mot den. Azure Datautforskaren-mått ger viktiga indikatorer för kluster resursernas hälso tillstånd och prestanda. Använd de mått som beskrivs i den här artikeln för att övervaka Azure Datautforskaren kluster hälsa och prestanda i ditt särskilda scenario som fristående mått. Du kan också använda Mät värden som grund för Azure- [instrumentpaneler](/azure/azure-portal/azure-portal-dashboards) och [Azure-aviseringar](/azure/azure-monitor/platform/alerts-metric-overview)med drift.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/).
* Ett [kluster och en databas](create-cluster-database-portal.md).

## <a name="using-metrics"></a>Använda mått

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. I ditt Azure Datautforskaren-kluster väljer du **mått** för att öppna fönstret mått och börjar analyser på klustret.
    ![välja mått](media/using-metrics/select-metrics.png).
1. I fönstret mått: ![mått rutan](media/using-metrics/metrics-pane.png)
    1. Om du vill skapa ett mått diagram väljer du **måttets** namn och relevant **agg regering** per mått. **Resurs** -och **mått namn rymds** väljarna är i förväg valda för ditt Azure datautforskaren-kluster. Mer information om olika mått finns i [Azure datautforskaren mått som stöds](#supported-azure-data-explorer-metrics).
    1. Välj **Lägg till mått** om du vill se flera mått som är ritade i samma diagram.
    1. Välj **+ nytt diagram** om du vill se flera diagram i en vy.
    1. Använd tid väljaren för att ändra tidsintervallet (standard: senaste 24 timmarna).
    1. Använd [ **Lägg till filter** och **Använd delning** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) för mått som har dimensioner.
    1. Välj **Fäst på instrument panelen** för att lägga till diagram konfigurationen till instrument panelerna så att du kan visa den igen.
    1. Ange **ny varnings regel** för att visualisera måtten med hjälp av Set-kriteriet. Den nya varnings regeln innehåller dina mål resurser, mått, delning och filter dimensioner från diagrammet. Ändra inställningarna i [fönstret Skapa aviserings regel](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Mer information om hur du använder [Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started).

## <a name="supported-azure-data-explorer-metrics"></a>Azure Datautforskaren-mått som stöds

De Azure Datautforskaren-mått som stöds delas upp i olika kategorier efter användning. 

### <a name="cluster-health-metrics"></a>Kluster hälso mått

Kluster hälso måtten spårar klustrets allmänna hälso tillstånd. Detta omfattar användning och svars tid för resurser och inmatningar.

**Mått** | **Processor** | **Aggregering** | **Mått Beskrivning** | **Enheter** |
|---|---|---|---|---|
| Användning av cache | Procent | Genomsn, Max, min | Procent andel allokerade cache-resurser som för närvarande används av klustret. Cache är storleken på SSD som allokerats för användar aktivitet enligt den definierade cache-principen. En genomsnittlig cache-användning på 80% eller mindre är ett hållbart tillstånd för ett kluster. Om den genomsnittliga användningen av cacheminnet är över 80% ska klustret [skalas upp](manage-cluster-vertical-scaling.md) till en optimerad lagrings nivå eller [skala ut](manage-cluster-horizontal-scaling.md) till fler instanser. Du kan också anpassa cache-principen (färre dagar i cacheminnet). Om cache användningen är över 100% är storleken på data som ska cachelagras, enligt principen för cachelagring, större än den totala storleken på cachen i klustret. | Ingen |
| CPU | Procent | Genomsn, Max, min | Procent andel allokerade beräknings resurser som för närvarande används av datorerna i klustret. En genomsnittlig CPU på 80% eller mindre är hållbart för ett kluster. Det maximala värdet för CPU är 100%, vilket innebär att det inte finns några ytterligare beräknings resurser för att bearbeta data. När ett kluster inte fungerar bra kontrollerar du det maximala värdet för processorn för att avgöra om det finns några angivna processorer som är blockerade. | Ingen |
| Förbruknings användning | Procent | Genomsn, Max, min | Procent andel faktiska resurser som används för att mata in data från den totala mängden allokerade resurser, i kapacitets principen, för att utföra inmatningen. Standard kapacitets principen är högst 512 samtidiga inmatnings åtgärder eller 75% av kluster resurserna som investerat i inmatningen. Genomsnittligt intag av användning på 80% eller mindre är ett hållbart tillstånd för ett kluster. Det maximala värdet för förbruknings användningen är 100%, vilket innebär att alla kluster inmatnings funktioner används och att en inmatnings kö kan resultera i detta. | Ingen |
| Behåll Alive | Antal | Gmsn | Spårar klustrets svars tider. Ett fullständigt besvarat kluster returnerar värde 1 och ett blockerat eller frånkopplat kluster returnerar 0. |
| Totalt antal begränsade kommandon | Antal | AVG, Max, min, sum | Antalet begränsade (avvisade) kommandon i klustret, eftersom det maximalt tillåtna antalet samtidiga (parallella) kommandon nåddes. | Ingen |
| Totalt antal omfattningar | Antal | AVG, Max, min, sum | Totalt antal data omfång i klustret. Ändringar i det här måttet kan innebära stora data struktur ändringar och hög belastning på klustret, eftersom sammanfogning av data omfång är en processor kraftig aktivitet. | Ingen |
| | | | |

### <a name="export-health-and-performance-metrics"></a>Exportera hälso-och prestanda mått

Exportera hälso-och prestanda mått spåra allmän hälsa och prestanda för export åtgärder som till exempel sent, resultat, antal poster och användning.

**Mått** | **Processor** | **Aggregering** | **Mått Beskrivning** | **Enheter** |
|---|---|---|---|---|
Löpande export antal exporterade poster    | Antal | Summa | Antalet exporterade poster i alla löpande export jobb. | Ingen |
Kontinuerlig export, maximal försening |    Antal   | Max   | Försenad (i minuter) som rapporteras av de kontinuerliga export jobben i klustret. | Ingen |
Antal väntande pågående export | Antal | Max   | Antalet väntande kontinuerliga export jobb. De här jobben är klara att köras, men väntar i en kö, eventuellt på grund av otillräcklig kapacitet). 
Resultat av kontinuerlig export    | Antal |   Antal   | Resultatet/framgångs resultatet av varje löpande export körning. | ContinuousExportName |
Exportera användning |    Procent | Max   | Använd export kapacitet, av den totala export kapaciteten i klustret (mellan 0 och 100). | Ingen |
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>Hälso tillstånd och prestanda mått

Användnings statistik och prestanda mått spårar allmän hälsa och prestanda för inmatnings åtgärder som svars tid, resultat och volym.

**Mått** | **Processor** | **Aggregering** | **Mått Beskrivning** | **Enheter** |
|---|---|---|---|---|
| Bearbetade händelser (för Event/IoT-hubbar) | Antal | Max, min, Summa | Totalt antal händelser som lästs från Event Hub och bearbetas av klustret. Händelserna delas upp i avvisade händelser och händelser som accepteras av kluster motorn. | EventStatus |
| Inmatnings svars tid | Sekunder | Genomsn, Max, min | Svars tiden för data som hämtas från den tidpunkt då data togs emot i klustret tills de är redo för fråga. Inmatnings svars tiden beror på inmatnings scenariot. | Ingen |
| Inmatnings resultat | Antal | Antal | Totalt antal inmatnings åtgärder som misslyckats och slutförts. Använd **Använd uppdelning** för att skapa Bucket-lyckade och underkänna resultat och analysera dimensionerna (**värde** > **status**).| IngestionResultDetails |
| Inmatnings volym (i MB) | Antal | Max, sum | Den totala storleken på data som matas in i klustret (i MB) före komprimering. | Databas |
| | | | |  

### <a name="query-performance"></a>Frågeprestanda

Fråga prestanda måtten spåra frågans varaktighet och det totala antalet samtidiga eller begränsade frågor.

**Mått** | **Processor** | **Aggregering** | **Mått Beskrivning** | **Enheter** |
|---|---|---|---|---|
| Frågevaraktighet | Millisekunder | Genomsn, min, Max, sum | Total tid tills frågeresultaten tas emot (omfattar inte nätverks svars tid). | QueryStatus |
| Totalt antal samtidiga frågor | Antal | AVG, Max, min, sum | Antalet frågor som körs parallellt i klustret. Detta mått är ett bra sätt att uppskatta belastningen på klustret. | Ingen |
| Totalt antal begränsade frågor | Antal | AVG, Max, min, sum | Antalet begränsade (avvisade) frågor i klustret. Det högsta antalet samtidiga (parallella) frågor som tillåts definieras i den samtidiga Frågeparametern. | Ingen |
| | | | |

### <a name="streaming-ingest-metrics"></a>Strömnings mått för strömning

Strömnings mått för strömning spårar data för strömning och begär ande frekvens, varaktighet och resultat.

**Mått** | **Processor** | **Aggregering** | **Mått Beskrivning** | **Enheter** |
|---|---|---|---|---|
Data hastighet för strömnings intag |    Antal   | RateRequestsPerSecond | Den totala mängden data som matas in i klustret. | Ingen |
Hämtnings tid för strömning   | Millisekunder  | Genomsn, Max, min | Total varaktighet för alla begär Anden om strömnings inmatning. | Ingen |
Begär ande frekvens för strömning   | Antal | Count, AVG, Max, min, sum | Totalt antal begär Anden om strömnings inmatning. | Ingen |
Resultat av strömnings inmatning | Antal | Gmsn   | Totalt antal förfrågningar om strömnings inmatning per resultat typ. | Resultat |
| | | | |

Mer information om [Azure datautforskaren-kluster mått som stöds](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters).


## <a name="next-steps"></a>Nästa steg

* [Självstudie: mata in och fråga övervaknings data i Azure Datautforskaren](/azure/data-explorer/ingest-data-no-code)
* [Övervaka Azure Datautforskaren-inmatnings åtgärder med hjälp av diagnostikloggar](/azure/data-explorer/using-diagnostic-logs)
* [Snabbstart: Fråga efter data i Azure Data Explorer](web-query-data.md)
