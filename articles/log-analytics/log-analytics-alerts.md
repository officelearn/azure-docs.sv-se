---
title: Förstå aviseringar i Azure Log Analytics | Microsoft Docs
description: Aviseringar i Log Analytics kan identifiera viktig information i OMS-databasen och proaktivt meddelar dig om problem eller anropa åtgärder om du vill försöka åtgärda.  Den här artikeln beskrivs de olika typerna av Varningsregler och hur de definieras.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2018
ms.author: bwren
ms.openlocfilehash: 5e01ea901f1ba07c0ee5a99720c00c5f03574365
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="understanding-alerts-in-log-analytics"></a>Förstå aviseringar i logganalys

> [!NOTE]
> Aviseringar i logganalys [förlängs till Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Informationen i den här artikeln kan fortfarande användas för att definiera informationen för aviseringar i Azure-portalen som använder logganalys sökningar.

Med aviseringar i Log Analytics kan du identifiera viktig information på Log Analytics-lagringsplatsen. Den här artikeln beskrivs några av designbeslut som måste göras baserat på samlingen frekvensen av data som efterfrågade, slumpmässig fördröjning med datapåfyllning kan orsakas av nätverksfördröjning eller bearbetningskapaciteten och sparar data till loggen Analytics-arbetsyta. Den innehåller information om hur Varningsregler i logganalys arbete och beskrivs skillnaderna mellan olika typer av Varningsregler.

Processen att skapa Varningsregler, finns i följande artiklar:

- Skapa Varningsregler med [Azure-portalen](log-analytics-alerts-creating.md)
- Skapa Varningsregler med [Resource Manager-mall](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)
- Skapa Varningsregler med [REST API](log-analytics-api-alerts.md)

## <a name="considerations"></a>Överväganden

Information om data collection frekvensen för olika lösningar och -datatypen är tillgängliga i den [information om samlingen](log-analytics-add-solutions.md#data-collection-details) av översiktsartikel lösningar. Enligt beskrivningen i den här artikeln samling frekvensen kan vara som ovanligt som var sjunde dag till *på notification*. Det är viktigt att förstå och Tänk på hur ofta samling data innan du skapar en avisering. 

- Samlingen frekvensen anger hur ofta OMS-agenten skickar data till logganalys. Till exempel om samlingen frekvens är 10 minuter och det finns inga andra fördröjningar i systemet, sedan tidsstämplar av överförda data kan vara var som helst mellan noll och 10 minuter gamla innan avsnittet läggs till i databasen och sökbara i logganalys.

- Innan en avisering kan utlösas måste data skrivas till databasen så att den är tillgänglig när en förfrågan. På grund av latens som beskrivs ovan, är samling frekvensen inte samma som den tid som data är tillgängliga för frågor. Till exempel när data kan samlas in exakt var 10 minut, är data tillgängliga i databasen oregelbundet data. Data som samlas in vid noll, 10 och 20 minuters intervall kanske målföretag, är tillgänglig för sökning 25 28 och 35 minuter respektive eller på något annat oregelbundna intervall påverkas av införandet svarstid. Värsta fall dessa fördröjningar dokumenteras i den [SLA för Log Analytics](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1), som inte innehåller en fördröjning som introducerades av samlingen frekvens eller nätverket fördröjningen mellan datorn och Log Analytics-tjänsten.


## <a name="alert-rules"></a>Varningsregler

Aviseringar skapas från varningsregler som automatiskt kör regelbundna loggsökningar. Om resultatet av loggen sökningen matchar särskilda skapas en avisering post. Regeln kan sedan automatiskt köra en eller flera åtgärder för att proaktivt meddela dig om aviseringen eller anropa en annan process. Olika typer av Varningsregler använda olika logik för att utföra den här analysen.

![Log Analytics-aviseringar](media/log-analytics-alerts/overview.png)

Eftersom det finns en förväntad latens med införandet av loggdata, kan det vara oförutsägbara absolut tiden mellan indexering data och när den är tillgänglig för att söka. Nära realtid tillgängligheten för data som samlas in ska beaktas när du definierar Varningsregler.

Det finns en kompromiss mellan aviseringar tillförlitlighet och tillgänglighet av aviseringar. Du kan välja att konfigurera aviseringen parametrar för att minimera falsklarm och varningar som saknas eller kan du välja aviseringsparametrer och snabbt svara på de villkor som övervakas, men ibland genererar felaktiga eller saknade aviseringar.

Varningsregler definieras av följande information:

- **Sök i loggfilen**. Den fråga som körs varje gång regeln utlöses. Poster som returneras av den här frågan används för att avgöra om en avisering skapas.
- **Tidsfönstret**. Anger tidsintervallet för frågan. Frågan returnerar bara de poster som har skapats i det här intervallet för den aktuella tiden. Detta kan vara ett värde mellan 5 minuter och 24 timmar. Intervallet måste vara tillräckligt stort för rimliga fördröjningar i införandet. Tidsfönstret måste vara två gånger så lång längsta tid som du vill kunna hantera.<br> Till exempel om du vill att aviseringar ska vara tillförlitliga 30 minuters fördröjningar måste intervallet vara en timme.

    Det finns två problem som kan uppstår om tidsintervallet är för liten.

    - **Saknade aviseringar**. Anta införandet fördröjningen är 60 minuter ibland men i de flesta fall är 15 minuter. Om tidsfönstret är 30 minuter sedan cachemissar den en avisering när fördröjningen är 60 minuter eftersom data inte är tillgängliga för sökning när aviseringen frågan körs. 
   
        >[!NOTE]
        >Det går inte att försök att diagnostisera varför aviseringen har missats. Till exempel i fallet skrivs data till databasen 60 minuter efter avisering frågan kördes. Om den är tänkt nästa dag på att en avisering uppfylldes och nästa dag frågan körs under korrekt tidsintervall matchar sökkriterierna loggen resultatet. Den visas att aviseringen ska har utlösts. Aviseringen utlöstes i själva verket inte eftersom data inte var ännu tillgängliga när aviseringen frågan kördes. 
        >
 
    - **Falsklarm**. Ibland aviseringsfrågorna är utformade för att identifiera frånvaro av händelser. Ett exempel på detta identifiering när en virtuell dator är offline genom att söka efter uteblivna pulsslag. Som ovan, om pulsslag inte är tillgänglig för sökningen i tidsfönstret aviseringen sedan en avisering genereras eftersom heartbeat-data inte ännu sökbara och därför saknas. Detta är samma resultat som om den virtuella datorn har legitimt offline och inga heartbeat-data som genereras av den. Frågan körs nästa dag via fönstret rätt tid visar att det fanns pulsslag och aviseringar misslyckades. I själva verket på pulsslag inte har tillgängliga för sökning eftersom tidsfönstret aviseringen har angetts för liten.

- **Frekvensen**.  Anger hur ofta frågan ska köras och kan användas för att se aviseringar snabbare för vanliga fall. Värdet kan vara mellan 5 minuter och 24 timmar och ska vara lika med eller mindre än tidsfönstret avisering.  Om värdet är större än tidsfönstret riskerar du poster som saknas.<br>Om målet är tillförlitliga för fördröjningar upp till 30 minuter och normal fördröjningen är 10 minuter, tidsfönstret ska vara en timme och värdet för testfrekvens måste vara 10 minuter. Detta skulle utlösa en avisering med data som har en 10 minut införandet fördröjning mellan 10 och 20 minuter för när aviseringsdata genererades.<br>Att undvika att skapa flera aviseringar för samma data eftersom tidsfönstret är för breda de [undertrycka aviseringar](log-analytics-tutorial-response.md#create-alerts) alternativet kan användas för att undertrycka aviseringar minst så länge som tidsfönstret.
  
- **Tröskelvärde för**. Resultaten av loggen sökningen utvärderas för att avgöra om en avisering ska skapas. Tröskelvärdet är olika för olika typer av Varningsregler.

Varje avisering regel i Log Analytics är en av två typer. De olika typerna beskrivs i detalj i avsnitten som följer.

- **[Antalet resultat](#number-of-results-alert-rules)**. Avisering skapas när antalet poster som returneras av loggen sökningen överskrider ett angivet tal.
- **[Mått mätning](#metric-measurement-alert-rules)**. Avisering som skapas för varje objekt i resultaten av loggen sökning med värden som överskrider angiven tröskel.

Skillnaderna mellan varningsregeln typer är som följer.

- **Antalet resultat** varningsregeln skapar alltid en enda avisering stund **mått mätning** varningsregeln skapar en avisering för varje objekt som överstiger tröskelvärdet.
- **Antalet resultat** Varningsregler skapar en avisering när tröskelvärdet överskrids en gång. **Mått mätning** Varningsregler kan skapa en avisering när tröskelvärdet överskrids ett visst antal gånger under ett visst tidsintervall.

## <a name="number-of-results-alert-rules"></a>Antalet resultat Varningsregler
**Antalet resultat** Varningsregler skapar en avisering när antalet poster som returneras av frågan överskrider det angivna tröskelvärdet.

### <a name="threshold"></a>Tröskelvärde
Tröskelvärdet för en **antalet resultat** varningsregeln är större än eller mindre än ett visst värde. Om antalet poster som returneras av loggen sökningen matchar det här villkoret, skapas en avisering.

### <a name="scenarios"></a>Scenarier

#### <a name="events"></a>Händelser
Den här typen av regel för varning är idealisk för att arbeta med händelser, t.ex Windows-händelseloggar Syslog, och anpassade loggar. Du kanske vill skapa en avisering när en viss felhändelse skapas eller när flera felhändelser skapas inom ett visst tidsintervall.

Ange antalet resultat för att Avisera om en enskild händelse med större än 0 och både frekvens och tid till 5 minuter. Som kör frågan var 5 minuter och Sök efter en enskild händelse som har skapats sedan den senaste gången frågan kördes. En längre frekvens kan fördröja tiden mellan händelser som samlas in och den avisering som skapas.

Vissa program får logga in ett tillfälligt fel som inte nödvändigtvis rera en avisering. Programmet kan till exempel gör processen som skapade felhändelsen och lyckas nästa gång. I det här fallet kan du inte vill skapa en avisering om flera händelser skapas inom ett visst tidsintervall.

I vissa fall kanske du vill skapa en avisering om en händelse. En process kan till exempel logga regelbundna händelser som indikerar att den fungerar korrekt. Om det inte logga en av dessa händelser inom ett visst tidsintervall, ska en avisering skapas. I det här fallet kan du ange ett tröskelvärde **mindre än 1**.

#### <a name="performance-alerts"></a>Prestandavarningar
[Prestandadata](log-analytics-data-sources-performance-counters.md) lagras som poster i databasen OMS liknande händelser. Om du vill Varna när en prestandaräknare överskrider ett visst tröskelvärde ska gränsen tas med i frågan.

Om du vill Varna när processorn körs exempelvis över 90%, använder du en fråga som följande med tröskelvärdet för varningsregeln **större än 0**.

    Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" and CounterValue>90

Om du vill meddela när processorn var i genomsnitt över 90% för ett visst tidsintervall du vill använda en fråga med hjälp av den `measure` kommandot på följande med tröskelvärdet för varningsregeln **större än 0**.

    Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" | summarize avg(CounterValue) by Computer | where CounterValue>90



## <a name="metric-measurement-alert-rules"></a>Mått mätning Varningsregler

>[!NOTE]
> Mått mätning Varningsregler är för närvarande i förhandsversion.

**Mått mätning** Varningsregler skapar en avisering för varje objekt i en fråga med ett värde som överskrider ett angivet tröskelvärde. De har olika följande skillnader från **antalet resultat** Varna regler.

#### <a name="log-search"></a>Loggsökning
Du kan använda en fråga för en **antalet resultat** avisering regel att det finns särskilda krav frågan för ett mått mätning varningsregel.  Det måste innehålla en `measure` kommando för att gruppera resultaten på ett visst fält. Det här kommandot måste innehålla följande element.


- **Mängdfunktion**.  Anger beräkningen som utförs och kan vara ett numeriskt fält ska aggregeras. Till exempel **count()** returnerar antalet poster i frågan, **avg(CounterValue)** Returnerar medelvärdet för fältet CounterValue under period.
- **Gruppera fältet**.  En post med ett insamlat värde skapas för varje instans av det här fältet och en avisering genereras för varje. Till exempel om du vill generera en avisering för varje dator du vill använda **per dator**.   
- **Intervallet**.  Definierar det tidsintervall under vilken data sammanställs.  Till exempel om du har angett **5 minuter**, skapas en post för varje instans av fältet samman på 5 minuters intervall under tidsfönster som angetts för aviseringen.

#### <a name="threshold"></a>Tröskelvärde
Tröskelvärdet för mått mätning Varningsregler definieras av ett samlat värde och ett antal intrång. Om varje datapunkt i loggen sökningen överskrider detta värde, anses det har ett intrång. Om antalet överträdelser i för alla objekt i resultaten överskrider det angivna värdet, skapas en avisering för objektet.

#### <a name="example"></a>Exempel
Föreställ dig ett scenario där du vill lägga till en avisering om alla datorer överskrids processoranvändning 90% tre gånger under 30 minuter. Du skapar en aviseringsregel med följande uppgifter.

**Fråga:** typ = Perf ObjectName = Processor CounterName = ”% processortid” | mäta avg(CounterValue) datorn intervall 5 minut<br>
**Tidsfönstret:** 30 minuter<br>
**Varna frekvens:** 5 minuter<br>
**Aggregera värde:** större än 90<br>
**Utlösaren avisering baserat på:** totalt överträdelser som är större än 5<br>

Frågan skulle skapa ett genomsnittligt värde för varje dator vid 5 minuters mellanrum. Den här frågan skulle köras var femte minut för data som samlas in under de föregående 30 minuterna. Exempeldata visas nedan för tre datorer.

![Exempel frågeresultat](media/log-analytics-alerts/metrics-measurement-sample-graph.png)

I det här exemplet skulle separata aviseringar skapas för srv02 och srv03 eftersom de utsatts för intrång tröskelvärdet 90% 3 gånger under tidsperioden. Om den **utlösaren avisering baserat på:** ändrades till **sidordning** sedan en avisering skapas endast för srv03 eftersom den utsatts för intrång tröskelvärdet för 3 beräkningar i följd.

## <a name="alert-records"></a>Varning-poster
Aviseringen poster som skapats av Varningsregler i logganalys har en **typen** av **avisering** och en **SourceSystem** av **OMS**. De har egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| Typ |*Varning* |
| SourceSystem |*OMS* |
| *Objektet*  | [Aviseringar för mått mätning](#metric-measurement-alert-rules) har en egenskap för fältet. Till exempel om loggen sökningen grupper på datorn, varning posten med ha datorn innehåller inget fält med namnet på datorn som värde.
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
* Slutföra en genomgång för [konfigurerar en webook](log-analytics-alerts-webhooks.md) med en aviseringsregel.
* Lär dig hur du skriver [runbooks i Azure Automation](https://azure.microsoft.com/documentation/services/automation) att åtgärda problem som identifieras av aviseringar.
