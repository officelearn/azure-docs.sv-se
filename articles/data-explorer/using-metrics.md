---
title: Övervaka Azure Data Explorer-prestanda, hälso- & användning med mått
description: Lär dig hur du använder Azure Data Explorer-mått för att övervaka klustrets prestanda, hälsa och användning.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 1319b8cd6ac8a0eb83381c24bcde9996458e47a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560312"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Övervaka Prestanda, hälsotillstånd och användning i Azure Data Explorer med mått

Azure Data Explorer är en snabb, fullständigt hanterad dataanalystjänst för realtidsanalys av stora mängder data som strömmar från program, webbplatser, IoT-enheter med mera. För att använda Azure Data Explorer skapar du först ett kluster och skapar en eller flera databaser i klustret. Sedan matar du in (läser in) data i databasen så att du kan köra frågor mot den. Azure Data Explorer-mått ger viktiga indikatorer för hälso- och prestanda för klusterresurserna. Använd de mått som beskrivs i den här artikeln för att övervaka Azure Data Explorer-klusterhälsa och prestanda i ditt specifika scenario som fristående mått. Du kan också använda mått som grund för operativa [Azure-instrumentpaneler](/azure/azure-portal/azure-portal-dashboards) och [Azure-aviseringar](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har ett kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).
* Ett [kluster och en databas](create-cluster-database-portal.md).

## <a name="using-metrics"></a>Använda mått

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. I azure data **explorer-klustret** väljer du Mått för att öppna måttfönstret och börja analysera klustret.
    ![Välj mått](media/using-metrics/select-metrics.png).
1. I fönstret Mått: ![Fönstret Mått](media/using-metrics/metrics-pane.png)
    1. Om du vill skapa ett måttdiagram väljer du **Måttnamn** och relevant **aggregering** per mått. **Resurs-** och **måttnamnområdesväljare** är förvalda för azure data explorer-klustret. Mer information om olika mått finns i [Azure Data Explorer-mått som stöds](#supported-azure-data-explorer-metrics).
    1. Välj **Lägg till mått** om du vill visa flera mått som ritats i samma diagram.
    1. Välj **+ Nytt diagram** om du vill visa flera diagram i en vy.
    1. Använd tidsväljaren för att ändra tidsintervallet (standard: senaste 24 timmarna).
    1. Använd [ **Lägg till filter** och **Använd delning** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) för mått som har dimensioner.
    1. Välj **Fäst på instrumentpanelen** om du vill lägga till diagramkonfigurationen på instrumentpanelerna så att du kan visa den igen.
    1. Ange **Ny varningsregel** för att visualisera dina mått med hjälp av de angivna villkoren. Den nya aviseringsregeln innehåller dina målresurs-, mått-, delnings- och filterdimensioner från diagrammet. Ändra dessa inställningar i [fönstret för att skapa varningsregel .](/azure/azure-monitor/platform/metrics-charts#create-alert-rules)

Ytterligare information om hur du använder [Statistikutforskaren](/azure/azure-monitor/platform/metrics-getting-started).

## <a name="supported-azure-data-explorer-metrics"></a>Azure Data Explorer-mått som stöds

Azure Data Explorer-mått som stöds är uppdelade i olika kategorier beroende på användning. 

### <a name="cluster-health-metrics"></a>Klusterhälsomått

Klusterhälsomåtten spårar den allmänna hälsan för klustret. Detta inkluderar resurs- och intagsanvändning och svarstider.

**Mått** | **Enhet** | **Aggregation** | **Beskrivning av mått** | **Dimensioner** |
|---|---|---|---|---|
| Cacheanvändning | Procent | Medel, Max, Min | Procentandel allokerade cacheresurser som för närvarande används av klustret. Cache är storleken på SSD som allokerats för användaraktivitet enligt den definierade cacheprincipen. Ett genomsnittligt cacheutnyttjande på 80 % eller mindre är ett hållbart tillstånd för ett kluster. Om den genomsnittliga cacheanvändningen är över 80 % ska klustret [skalas upp](manage-cluster-vertical-scaling.md) till en lagringsoptimerad prisnivå eller [skalas ut](manage-cluster-horizontal-scaling.md) till fler instanser. Du kan också anpassa cacheprincipen (färre dagar i cacheminnet). Om cacheanvändningen är över 100 %, är storleken på de data som ska cachelagras, enligt cachelagringsprincipen, större än den totala storleken på cacheminnet i klustret. | Inget |
| Processor | Procent | Medel, Max, Min | Procentandel allokerade beräkningsresurser som för närvarande används av datorer i klustret. En genomsnittlig PROCESSOR på 80% eller mindre är hållbar för ett kluster. Det maximala värdet för CPU är 100%, vilket innebär att det inte finns några ytterligare beräkningsresurser för att bearbeta data. När ett kluster inte fungerar bra kontrollerar du det maximala värdet för processorn för att avgöra om det finns specifika processorer som är blockerade. | Inget |
| Utnyttjande av förtäring | Procent | Medel, Max, Min | Procentandel av faktiska resurser som används för att inta data från de totala resurser som allokerats i kapacitetsprincipen för att utföra inmatning. Standardkapacitetsprincipen är inte mer än 512 samtidiga inmatningsåtgärder eller 75 % av klusterresurserna som investeras i inmatning. Genomsnittligt intag utnyttjande av 80% eller mindre är ett hållbart tillstånd för ett kluster. Maximalt värde för intag användning är 100%, vilket innebär att alla kluster intag förmåga används och en intag kö kan uppstå. | Inget |
| Håll dig vid liv | Antal | Genomsn | Spårar klustrets svarstider. Ett fullständigt responsivt kluster returnerar värde 1 och ett blockerat eller frånkopplat kluster returnerar 0. |
| Totalt antal begränsade kommandon | Antal | Genomsnittlig, Max, Min, Summa | Antalet begränsade (avvisade) kommandon i klustret, eftersom det högsta tillåtna antalet samtidiga (parallella) kommandon uppnåddes. | Inget |
| Totalt antal omfattningar | Antal | Genomsnittlig, Max, Min, Summa | Totalt antal datasalar i klustret. Ändringar i det här måttet kan innebära massiva ändringar av datastrukturen och hög belastning i klustret, eftersom sammanslagning av dataomfattningar är en CPU-tung aktivitet. | Inget |
| | | | |

### <a name="export-health-and-performance-metrics"></a>Exportera hälso- och resultatmått

Exporthälso- och resultatmått spårar den allmänna hälso- och prestanda för exportåtgärder som försening, resultat, antal poster och användning.

**Mått** | **Enhet** | **Aggregation** | **Beskrivning av mått** | **Dimensioner** |
|---|---|---|---|---|
Kontinuerligt exporterat antal exporterade poster    | Antal | Summa | Antalet exporterade poster i alla kontinuerliga exportjobb. | Inget |
Kontinuerlig export max lateness |    Antal   | Max   | Förseningen (i minuter) som rapporteras av de kontinuerliga exportjobben i klustret. | Inget |
Kontinuerlig export väntande antal | Antal | Max   | Antalet väntande kontinuerliga exportjobb. Dessa jobb är redo att köras men väntar i en kö, möjligen på grund av otillräcklig kapacitet). 
Kontinuerligt exportresultat    | Antal |   Antal   | Resultatet fel/lyckade resultat för varje kontinuerlig exportkörning. | KontinuerligtExportnamn |
Exportutnyttjande |    Procent | Max   | Exportkapacitet som används, av den totala exportkapaciteten i klustret (mellan 0 och 100). | Inget |
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>Hälso- och prestandamått för intag

Intag hälsa och prestanda mått spåra allmän hälsa och prestanda för inmatningsåtgärder som svarstid, resultat och volym.

**Mått** | **Enhet** | **Aggregation** | **Beskrivning av mått** | **Dimensioner** |
|---|---|---|---|---|
| Händelser som bearbetas (för event/IoT-hubbar) | Antal | Max, Min, Summa | Totalt antal händelser som lästs från händelsehubbar och bearbetats av klustret. Händelserna delas upp i händelser som avvisats och händelser som accepteras av klustermotorn. | EventStatus |
| Svarstid för inmatning | Sekunder | Medel, Max, Min | Svarstid för data som intas, från det att data togs emot i klustret tills de är klara för frågan. Svarstiden för inmatningsfördröjning beror på inmatningsscenariot. | Inget |
| Intag resultat | Antal | Antal | Totalt antal inmatningsåtgärder som misslyckades och lyckades. Använd **tillämpa delning** för att skapa buckets of success och misslyckas resultat och analysera dimensionerna (**Värdestatus** > **Status**).| IntagResultDetaljer |
| Intagsvolym (i MB) | Antal | Max, Summa | Den totala storleken på data som intas i klustret (i MB) före komprimering. | Databas |
| | | | |  

### <a name="query-performance"></a>Frågeprestanda

Frågeprestandamått spårar frågelängd och totalt antal samtidiga eller begränsade frågor.

**Mått** | **Enhet** | **Aggregation** | **Beskrivning av mått** | **Dimensioner** |
|---|---|---|---|---|
| Varaktighet för fråga | Millisekunder | Medel, Min, Max, Summa | Total tid tills frågeresultat tas emot (inkluderar inte nätverksfördröjning). | Frågestatus |
| Totalt antal samtidiga frågor | Antal | Genomsnittlig, Max, Min, Summa | Antalet frågor körs parallellt i klustret. Det här måttet är ett bra sätt att uppskatta belastningen på klustret. | Inget |
| Totalt antal begränsade frågor | Antal | Genomsnittlig, Max, Min, Summa | Antalet begränsade (avvisade) frågor i klustret. Det maximala antalet samtidiga (parallella) frågor som tillåts definieras i den samtidiga frågeprincipen. | Inget |
| | | | |

### <a name="streaming-ingest-metrics"></a>Strömmande intrest-mått

Strömmande inmatningsmått spårar data för inmatning av direktuppspelning och begäranhetshastighet, varaktighet och resultat.

**Mått** | **Enhet** | **Aggregation** | **Beskrivning av mått** | **Dimensioner** |
|---|---|---|---|---|
Datahastighet för strömmande intning |    Antal   | RäntaRequestsPerSecond | Total mängd data som förtärs till klustret. | Inget |
Varaktighet för strömmande intning   | Millisekunder  | Medel, Max, Min | Total varaktighet för alla begäranden om inmatning av direktuppspelning. | Inget |
Streaming Ingest begärsthastighet   | Antal | Räkna, Medel, Max, Min, Summa | Totalt antal begäranden om inmatning av direktuppspelning. | Inget |
Streaming Intest Resultat | Antal | Genomsn   | Totalt antal begäranden om direktuppspelning efter resultattyp. | Resultat |
| | | | |

Ytterligare information om [azure data explorer-klustermått som stöds](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters).


## <a name="next-steps"></a>Nästa steg

* [Självstudiekurs: Övr och frågar övervakningsdata i Azure Data Explorer](/azure/data-explorer/ingest-data-no-code)
* [Övervaka inmatningsåtgärder för Azure Data Explorer med hjälp av diagnostikloggar](/azure/data-explorer/using-diagnostic-logs)
* [Snabbstart: Fråga efter data i Azure Data Explorer](web-query-data.md)
