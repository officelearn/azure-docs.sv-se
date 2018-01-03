---
title: "Förstå aviseringar i Azure Log Analytics | Microsoft Docs"
description: "Aviseringar i Log Analytics kan identifiera viktig information i OMS-databasen och proaktivt meddelar dig om problem eller anropa åtgärder om du vill försöka åtgärda.  Den här artikeln beskrivs de olika typerna av Varningsregler och hur de definieras."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/13/2017
ms.author: bwren
ms.openlocfilehash: a0897113660f764cb23239b066bc93c479a9a553
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2017
---
# <a name="understanding-alerts-in-log-analytics"></a>Förstå aviseringar i logganalys

Aviseringar i Log Analytics identifiera viktig information i logganalys-databasen.  Den här artikeln innehåller information om hur Varningsregler i logganalys arbete och beskrivs skillnaderna mellan olika typer av Varningsregler.

Processen att skapa Varningsregler, finns i följande artiklar:

- Skapa Varningsregler med [Azure-portalen](log-analytics-alerts-creating.md)
- Skapa Varningsregler med [Resource Manager-mall](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)
- Skapa Varningsregler med [REST API](log-analytics-api-alerts.md)


## <a name="alert-rules"></a>Aviseringsregler

Aviseringar skapas med Varningsregler som automatiskt kör loggen söker regelbundet.  Om resultatet av loggen sökningen matchar särskilda skapas en avisering post.  Regeln kan sedan automatiskt köra en eller flera åtgärder för att proaktivt meddelar dig om aviseringen eller anropa en annan process.  Olika typer av Varningsregler använda olika logik för att utföra den här analysen.

![Log Analytics-aviseringar](media/log-analytics-alerts/overview.png)

Varningsregler definieras av följande information:

- **Sök i loggfilen**.  Den fråga som körs varje gång regeln utlöses.  Poster som returneras av den här frågan används för att avgöra om en avisering skapas.
- **Tidsfönstret**.  Anger tidsintervallet för frågan.  Frågan returnerar bara de poster som har skapats i det här intervallet för den aktuella tiden.  Detta kan vara ett värde mellan 5 minuter och 24 timmar. Om frågan körs klockan 13:15 tidsfönstret har angetts till 60 minuter, returneras endast de poster som skapats mellan 12:15:00 och 1:15 i Återställningsmappen.
- **Frekvensen**.  Anger hur ofta frågan ska köras. Kan vara ett värde mellan 5 minuter och 24 timmar. Måste vara lika med eller mindre än tidsperioden.  Om värdet är större än tidsfönstret riskerar du poster som saknas.<br>Tänk dig ett tidsfönster på 30 minuter och en frekvens som 60 minuter.  Om frågan körs 1:00, returnerar poster mellan 12:30 och 1:00.  Nästa gång frågan körs är 2:00 när återgår den poster mellan 1:30 och 2:00.  Alla poster som skapats mellan 01:00 och 1:30 skulle aldrig utvärderas.
- **Tröskelvärde för**.  Resultaten av loggen sökningen utvärderas för att avgöra om en avisering ska skapas.  Tröskelvärdet är olika för olika typer av Varningsregler.

Varje avisering regel i Log Analytics är en av två typer.  De olika typerna beskrivs i detalj i avsnitten som följer.

- **[Antalet resultat](#number-of-results-alert-rules)**. Avisering skapas när antalet poster som returneras av loggen sökningen överskrider ett angivet tal.
- **[Mått mätning](#metric-measurement-alert-rules)**.  Avisering som skapas för varje objekt i resultaten av loggen sökning med värden som överskrider angiven tröskel.

Skillnaderna mellan varningsregeln typer är som följer.

- **Antalet resultat** varningsregeln skapas alltid en enda avisering stund **mått mätning** varningsregeln skapar en avisering för varje objekt som överstiger tröskelvärdet.
- **Antalet resultat** Varningsregler skapar en avisering när tröskelvärdet överskrids en gång. **Mått mätning** Varningsregler kan skapa en avisering när tröskelvärdet överskrids ett visst antal gånger under ett visst tidsintervall.

## <a name="number-of-results-alert-rules"></a>Antalet resultat Varningsregler
**Antalet resultat** Varningsregler skapar en avisering när antalet poster som returneras av frågan överskrider det angivna tröskelvärdet.

### <a name="threshold"></a>Tröskelvärde
Tröskelvärdet för en **antalet resultat** varningsregeln är helt enkelt större eller mindre än ett visst värde.  Om antalet poster som returneras av loggen sökningen matchar det här villkoret, skapas en avisering.

### <a name="scenarios"></a>Scenarier

#### <a name="events"></a>Händelser
Den här typen av regel för varning är idealisk för att arbeta med händelser, t.ex Windows-händelseloggar Syslog, och anpassade loggar.  Du kanske vill skapa en avisering när en viss felhändelse skapas eller när flera felhändelser skapas inom ett visst tidsintervall.

Ange antalet resultat för att Avisera om en enskild händelse med större än 0 och både frekvens och tid till 5 minuter.  Som kör frågan var 5 minuter och Sök efter en enskild händelse som har skapats sedan den senaste gången frågan kördes.  En längre frekvens kan fördröja tiden mellan händelser som samlas in och den avisering som skapas.

Vissa program får logga in ett tillfälligt fel som inte nödvändigtvis rera en avisering.  Programmet kan till exempel gör processen som skapade felhändelsen och lyckas nästa gång.  I det här fallet kan du inte vill skapa en avisering om flera händelser skapas inom ett visst tidsintervall.  

I vissa fall kanske du vill skapa en avisering om en händelse.  En process kan till exempel logga regelbundna händelser som indikerar att den fungerar korrekt.  Om det inte logga en av dessa händelser inom ett visst tidsintervall, ska en avisering skapas.  I det här fallet kan du ange ett tröskelvärde **mindre än 1**.

#### <a name="performance-alerts"></a>Prestandavarningar
[Prestandadata](log-analytics-data-sources-performance-counters.md) lagras som poster i databasen OMS liknande händelser.  Om du vill Varna när en prestandaräknare överskrider ett visst tröskelvärde ska gränsen tas med i frågan.

Om du vill Varna när processorn körs exempelvis över 90%, använder du en fråga som följande med tröskelvärdet för varningsregeln **större än 0**.

    Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" and CounterValue>90

    

Om du vill meddela när processorn var i genomsnitt över 90% av en viss tidsfönstret använder du en fråga som följande med tröskelvärdet för varningsregeln **större än 0**.

    Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" | where CounterValue>90 | summarize avg(CounterValue) by Computer

    
>[!NOTE]
> Om ditt arbetsområde inte har ännu uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), sedan senare frågorna skulle ändra till följande med en senare med hjälp av den [mäta kommandot](log-analytics-search-reference.md#commands):`Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90`
> `Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90`


## <a name="metric-measurement-alert-rules"></a>Mått mätning Varningsregler

>[!NOTE]
> Mått mätning Varningsregler är för närvarande i förhandsversion.

**Mått mätning** Varningsregler skapar en avisering för varje objekt i en fråga med ett värde som överskrider ett angivet tröskelvärde.  De har olika följande skillnader från **antalet resultat** Varna regler.

#### <a name="log-search"></a>Loggsökning
Du kan använda en fråga för en **antalet resultat** avisering regel att det finns särskilda krav frågan för ett mått mätning varningsregel.  Det måste innehålla en [mäta kommandot](log-analytics-search-reference.md#commands) gruppering av resultaten på ett visst fält. Det här kommandot måste innehålla följande element.

- **Mängdfunktion**.  Anger beräkningen som utförs och kan vara ett numeriskt fält ska aggregeras.  Till exempel **count()** returnerar antalet poster i frågan, **avg(CounterValue)** Returnerar medelvärdet för fältet CounterValue under period.
- **Gruppera fältet**.  En post med ett insamlat värde skapas för varje instans av det här fältet och en avisering genereras för varje.  Till exempel om du vill generera en avisering för varje dator du vill använda **per dator**.   
- **Intervallet**.  Definierar det tidsintervall under vilken data sammanställs.  Till exempel om du har angett **5minutes**, skapas en post för varje instans av fältet samman på 5 minuters intervall under tidsfönster som angetts för aviseringen.

#### <a name="threshold"></a>Tröskelvärde
Tröskelvärdet för mått mätning Varningsregler definieras av ett samlat värde och ett antal intrång.  Om varje datapunkt i loggen sökningen överskrider detta värde, anses det har ett intrång.  Om antalet överträdelser i för alla objekt i resultaten överskrider det angivna värdet, skapas en avisering för objektet.

#### <a name="example"></a>Exempel
Föreställ dig ett scenario där du vill lägga till en avisering om alla datorer överskrids processoranvändning 90% tre gånger under 30 minuter.  Du skapar en aviseringsregel med följande uppgifter.  

**Fråga:** Perf | där ObjectName == ”-Processor” och CounterName == ”% processortid” | sammanfatta AggregatedValue = avg(CounterValue) av bin (TimeGenerated, 5 m), datorn<br>
**Tidsfönstret:** 30 minuter<br>
**Varna frekvens:** 5 minuter<br>
**Aggregera värde:** större än 90<br>
**Utlösaren avisering baserat på:** totalt överträdelser som är större än 2<br>

Frågan skulle skapa ett genomsnittligt värde för varje dator vid 5 minuters mellanrum.  Den här frågan skulle köras var femte minut för data som samlas in under de föregående 30 minuterna.  Exempeldata visas nedan för tre datorer.

![Exempel frågeresultat](media/log-analytics-alerts/metrics-measurement-sample-graph.png)

I det här exemplet skulle separata aviseringar skapas för srv02 och srv03 eftersom de utsatts för intrång tröskelvärdet 90% 3 gånger under tidsperioden.  Om den **utlösaren avisering baserat på:** ändrades till **sidordning** sedan en avisering skapas endast för srv03 eftersom den utsatts för intrång tröskelvärdet för 3 beräkningar i följd.

## <a name="alert-records"></a>Varning-poster
Aviseringen poster som skapats av Varningsregler i logganalys har en **typen** av **avisering** och en **SourceSystem** av **OMS**.  De har egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| Typ |*Varning* |
| SourceSystem |*OMS* |
| *Objektet*  | [Aviseringar för mått mätning](#metric-measurement-alert-rules) har en egenskap för fältet.  Till exempel om loggen sökningen grupper på datorn, varning posten med ha datorn innehåller inget fält med namnet på datorn som värde.
| AlertName |Namnet på aviseringen. |
| AlertSeverity |Allvarlighetsgrad för aviseringen. |
| LinkToSearchResults |Länka till logganalys loggen sökning som returnerar poster från frågan som skapade aviseringen. |
| Fråga |Texten för frågan kördes. |
| QueryExecutionEndTime |Slutet på tidsintervallet för frågan. |
| QueryExecutionStartTime |Början av tidsintervallet för frågan. |
| ThresholdOperator | Operator som användes av regeln. |
| ThresholdValue | Värdet som användes av regeln. |
| TimeGenerated |Datum och tid då aviseringen skapades. |

Det finns andra typer av aviseringar poster som skapats av den [lösning för avisering](log-analytics-solution-alert-management.md) och av [Power BI exporterar](log-analytics-powerbi.md).  Dessa alla har en **typen** av **avisering** men särskiljs från varandra med deras **SourceSystem**.


## <a name="next-steps"></a>Nästa steg
* Installera den [avisering hanteringslösning](log-analytics-solution-alert-management.md) att analysera aviseringar som skapats i logganalys tillsammans med aviseringar som samlas in från System Center Operations Manager.
* Läs mer om [logga sökningar](log-analytics-log-searches.md) som kan generera aviseringar.
* Slutföra en genomgång för [konfigurerar en webhook](log-analytics-alerts-webhooks.md) med en aviseringsregel.  
* Lär dig hur du skriver [runbooks i Azure Automation](https://azure.microsoft.com/documentation/services/automation) att åtgärda problem som identifieras av aviseringar.
