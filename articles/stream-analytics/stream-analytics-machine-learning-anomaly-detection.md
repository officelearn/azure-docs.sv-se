---
title: Avvikelseidentifiering i Azure Stream Analytics (förhandsversion)
description: Den här artikeln beskriver hur du använder Azure Stream Analytics och Azure Machine Learning tillsammans för att identifiera avvikelser.
services: stream-analytics
author: dubansal
ms.author: dubansal
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: 2f35f54c7ec5ad169673aebe08602294270f470a
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364463"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Avvikelseidentifiering i Azure Stream Analytics

> [!IMPORTANT]
> Den här funktionen håller på att blir inaktuella, men kommer att ersättas med nya funktioner. Mer information finns i [åtta nya funktioner i Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/) blogginlägg.

Den **AnomalyDetection** operator som används för att identifiera olika typer av avvikelser i händelseströmmar. Till exempel en långsam minskning i ledigt minne över en längre tid kan tyda på en minnesläcka eller antalet webbtjänstbegäranden som är stabila inom ett intervall kan avsevärt öka eller minska.  

Operatorn AnomalyDetection identifierar tre typer av avvikelser: 

* **Dubbelriktad nivå ändra**: en varaktig ökning eller minskning i nivån av värden, både uppåt och nedåt. Det här värdet är detsamma som toppar och dalar, som är ögonblicklig eller tillfällig ändringar.  

* **Långsam positivt Trend**: en långsam ökning trend över tid.  

* **Långsam försämringen**: en långsam minskning i trend över tid.  

När du använder operatorn AnomalyDetection, måste du ange den **Limit Duration** satsen. Den här satsen Anger tidsintervall (hur långt tillbaka i historiken från den aktuella händelsen) bör övervägas när du söker efter avvikelser. Den här operatorn kan du kan också begränsas till endast de händelser som matchar en viss egenskap eller ett villkor med hjälp av den  **när**   satsen. Den här operatorn kan också välja att bearbeta grupper av händelser som separat baserat på nyckeln som anges i den  **partitionera av**   satsen. Utbildning och förutsägelse sker separat för varje partition. 

## <a name="syntax-for-anomalydetection-operator"></a>Syntax för operatorn AnomalyDetection

`ANOMALYDETECTION(<scalar_expression>) OVER ([PARTITION BY <partition key>] LIMIT DURATION(<unit>, <length>) [WHEN boolean_expression])` 

**Exempel på användning**  

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id > 100) FROM input`

### <a name="arguments"></a>Argument

* **scalar_expression** -skalärt uttryck som utförs för avvikelseidentifiering. Tillåtna värden för den här parametern innehåller flyttal eller Bigint-datatyper som en enda (skalära) returvärdet. Jokerteckenuttryck **\*** tillåts inte. Skalärt uttryck får inte innehålla andra analysfunktioner eller externa funktioner. 

* **partition_by_clause** – `PARTITION BY <partition key>` satsen dividerar inlärning och utbildning i separata partitioner. Med andra ord en separat modell används per värde för `<partition key>` och endast händelser med detta värde används för inlärning och utbildning i den modellen. Till exempel följande fråga tåg och poängsätter en läsning mot andra avläsningar av samma sensorn:

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

* **limit_duration satsen** `DURATION(<unit>, <length>)` -Anger tidsintervall (hur långt tillbaka i historiken från den aktuella händelsen) bör övervägas när du söker efter avvikelser. Se [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics) för en detaljerad beskrivning av enheter som stöds och deras förkortningar. 

* **when_clause** -anger ett booleskt villkor för de händelser som anses vara i beräkningen för avvikelseidentifiering identifiering.

### <a name="return-types"></a>Returnera typer

Operatorn AnomalyDetection returnerar en post som innehåller samtliga tre värden som utdata. De egenskaper som är associerade med de olika typerna av detektorer för avvikelseidentifiering är:

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

Om du vill extrahera enskilda värden från posten, använda den **GetRecordPropertyValue** funktion. Exempel:

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) > 3.25` 

Avvikelseidentifiering av en typ identifieras när något av avvikelseidentifiering poängen överskrider ett tröskelvärde. Tröskelvärdet kan vara valfri Flyttalsnummer > = 0. Tröskelvärdet är en kompromiss mellan känslighet och det förtroende. Ett lägre tröskelvärde skulle till exempel göra identifiering mer känsliga för ändringar och generera fler aviseringar medan ett högre tröskelvärde kan göra identifiering mindre känsliga och tryggare men maskera vissa avvikelser. Det exakta tröskelvärdet som ska använda beror på scenariot. Det finns ingen övre gräns, men det rekommendera intervallet är 3,25 5. 

Värdet 3,25 som visas i exemplet är bara en föreslagna startpunkt. Finjustera värdet genom att köra åtgärder på din egen datauppsättning och notera värdet tills du når ett tröskelvärde för tillåtna avbrottstid.

## <a name="anomaly-detection-algorithm"></a>Algoritm för avvikelseidentifiering

* Operatorn AnomalyDetection använder en **oövervakad inlärning** metod där det utgår inte från någon typ av distribution i händelserna. I allmänhet bevaras två modeller parallellt vid en given tidpunkt, där en av dem används för bedömning och den andra har tränats i bakgrunden. Avvikelseidentifiering identifiering modeller tränas med data från aktuell ström i stället för att använda en out-of-band-mekanism. Mängden data som används för utbildning beror på fönstret storlek d som angetts av användaren i parametern Limit Duration. Varje modell identisk komma tränas baserat på d till 2d som händelser. Vi rekommenderar att ha minst 50 händelser i varje fönster för bästa resultat. 

* Operatorn AnomalyDetection använder **glidande fönstret semantik** att träna modeller och poängsätta händelser. Vilket innebär att varje händelse som ska utvärderas för avvikelseidentifiering och en poäng returneras. Poängen är en indikation på konfidensnivån för den avvikelseidentifiering. 

* Operatorn AnomalyDetection tillhandahåller en **repeterbarhet garanti** samma indata alltid ger samma poängen oavsett jobbutdata starttid. Jobbets starttid för utdata representerar den tid då den första utdatahändelse produceras av jobbet. Den anges av användaren för att den aktuella tiden, ett anpassat värde eller tid för senaste utdata (om jobbet hade tidigare tillverkas utdata). 

### <a name="training-the-models"></a>Träna modeller 

När tid fortlöper modeller tränas med olika data. Om du vill göra uppfattning om poängen det hjälper dig för att förstå den underliggande mekanism som modeller tränas. Här kan **t<sub>0</sub>**  är den **starttid för jobbutdata** och **d** är den **fönsterstorlek** från Limit Duration parameter. Anta som tid är indelad i **hopp av storlek d**med start från `01/01/0001 00:00:00`. Följande steg används för att träna modellen och poäng händelser:

* När ett jobb startar den läser data från tiden t<sub>0</sub> – 2d.  
* När tiden når nästa hopp, en ny modell M1 och börjar hämta tränas.  
* När tid nyheterna av en annan hopp, en ny modell M2 och börjar hämta tränas.  
* När tiden når t<sub>0</sub>, M1 har aktiverats och dess poäng börjar få för utdata.  
* Vid nästa hopp saker tre på samma gång:  

  * M1 behövs inte längre och den tas bort.  
  * M2 har tränats tillräckligt så att den används för bedömning.  
  * En ny modell M3 skapas och börjar hämta tränas i bakgrunden.  

* Den här cykeln upprepas för varje hopp där den aktiva modellen ignoreras, växla till parallella modellen och starta träna en tredje modell i bakgrunden. 

Diagrammatically, stegen se ut så här: 

![Modeller för utbildning](media/stream-analytics-machine-learning-anomaly-detection/training_model.png)

|**Modellen** | **Starttid för utbildning** | **Dags att börja använda dess poäng** |
|---------|---------|---------|
|M1     | 11:20   | 11:33   |
|M2     | 11:30   | 11:40   |
|M3     | 11:40   | 11:50   |

* Modellen M1 startar utbildning kl. 11:20, vilket är nästa hopp när jobbet börjar läsa kl. 11:13. Den första utdata från M1 kl. 11:33 efter utbildning med 13 minuters data. 

* En ny modell M2 startas även utbildning kl. 11:30 men inte hämta används dess poäng fram till 11:40 am, vilket är när den har tränats med 10 minuters data. 

* M3 följer samma mönster som M2. 

### <a name="scoring-with-the-models"></a>Bedömning med modeller 

På nivån för Machine Learning beräknar algoritmen för avvikelseidentifiering ett strangeness värde för varje inkommande händelse genom att jämföra med händelser i ett fönster för historik. Beräkningen strangeness skiljer sig åt för varje typ av avvikelseidentifiering.  

Vi ska gå igenom strangeness beräkningen i detalj (antar finns en uppsättning historiska windows med händelser): 

1. **Dubbelriktad nivåändring:** utifrån okno historie normala arbetsområde beräknas som [10: e percentilen, 90: e percentilen] dvs 10th percentilvärdet som det nedre gränsen och 90: e percentilvärdet som den övre gränsen. Ett strangeness värde för det aktuella beräknas enligt följande:  

   - 0, om event_value i normala arbetsområde  
   - event_value/90th_percentile om event_value > 90th_percentile  
   - 10th_percentile/event_value, om event_value < 10th_percentile  

2. **Långsam positivt trend:** en trendlinje över händelsevärden i fönstret tidigare beräknas och åtgärden söker efter positiva trender i raden. Värdet för strangeness beräknas enligt följande:  

   - Luta om lutning är positivt  
   - 0, övrigt 

3. **Långsam försämringen:** en trendlinje över händelsevärden i fönstret tidigare beräknas och åtgärden söker efter negativa utvecklingen i raden. Värdet för strangeness beräknas enligt följande: 

   - Luta om lutning är negativt  
   - 0, övrigt  

När värdet för den inkommande händelsen strangeness beräknas ett martingale värde beräknas utifrån värdet strangeness (finns i den [Machine Learning-blogg](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/) mer information om hur martingale värdet beräknas). Det här värdet martingale retuned som avvikelsepoäng. Värdet martingale ökar långsamt svar på onormalt värden detektor att vara robust sporadiska ändringar och således minskar falska aviseringar. Det finns också en användbar egenskap: 

Sannolikheten [det finns t sådana som M<sub>t</sub> > λ] < 1/λ där M<sub>t</sub> är martingale värdet vid omedelbar t och λ är en verkliga värdet. Exempel: om det finns en avisering när M<sub>t</sub>> 100 och sannolikheten för falska positiva identifieringar är mindre än 1/100.  

## <a name="guidance-for-using-the-bi-directional-level-change-detector"></a>Riktlinjer för att använda dubbelriktad nivån ändra detektor 

Dubbelriktad nivåändring detektor kan användas i scenarier power avbrott och återställning eller rusningstid trafik, t.ex. Men den fungerar på ett sätt att när en modellen tränas med vissa data, en annan nivå förändring är avvikande endast om det nya värdet är högre än den tidigare övre gränsen (uppåtgående nivå ändra skiftläge) eller lägre än den föregående lägre gränsen (nedåtgående nivå Ändra skiftläge). En modell bör därför inte se datavärdena i intervallet för den nya nivån (uppåt eller nedåt) i ett fönster för utbildning att betraktas som avvikande. 

Följande bör övervägas när du använder den här detektor: 

1. När tidsserien plötsligt ser en ökning eller ta bort värde, operatorn AnomalyDetection identifierar den. Men identifierar återgå till normal kräver mer planering. Om en tidsserie var i stabilt tillstånd innan avvikelser som kan uppnås genom att ange operatorn AnomalyDetection identifiering fönstret till högst hälften så långt som avvikelsen. Det här scenariot förutsätter att den kortaste varaktigheten för avvikelsen kan beräknas förbereds i förväg och det finns tillräckligt med händelser under den tidsperioden för att träna modellen tillräckligt (minst 50 händelser). 

   Detta illustreras i bild 1 och 2 nedan på en övre gräns för ändring (samma logik gäller en lägre gränsen ändring). I båda siffror är vågformer en avvikande nivåändring. Orange lodrätt anger hopp gränser och hoppstorlek är samma som fönstret identifiering som angetts i operatorn AnomalyDetection. Grön raderna motsvarar storleken på fönstret utbildning. I bild 1 är hopp storleken samma som tiden för vilka avvikelseidentifiering varar. I bild 2 är hopp halva tiden som avvikelsen varar. I samtliga fall måste identifieras en uppåtgående ändring eftersom modellen som används för bedömning har tränats på normala data. Men baserat på hur dubbelriktad nivåändring detektor fungerar, måste den utesluta de normala värdena från fönstret utbildning som används för den modell som poängsätter återgå till normal. I bild 1 innehåller den bedömningsmodell utbildning vissa vanliga händelser så att återgå till normal inte kan identifieras. Men i bild 2 utbildningen bara innehåller den avvikande delen, vilket garanterar att återgå till normal har identifierats. Mindre än hälften fungerar även för samma anledning medan allt större resulterar i inklusive lite normala händelserna. 

   ![AD med storleken lika avvikelseidentifiering längd](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_anomaly_length.png)

   ![AD med fönsterstorleken är lika med hälften av avvikelseidentifiering längd](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_half_anomaly_length.png)

2. I fall där det inte går att förutse hur lång avvikelsen fungerar den här detektor i bästa prestanda. Men väljer ett snävare tidsintervall som begränsar träningsdata, vilket skulle öka sannolikheten för att identifiera återgå till normal. 

3. I följande scenario är inte längre avvikelsen identifierade som fönstret utbildning innehåller redan en avvikelse i samma högt värde. 

   ![Avvikelser med samma storlek](media/stream-analytics-machine-learning-anomaly-detection/anomalies_with_same_length.png)

## <a name="example-query-to-detect-anomalies"></a>Exempelfråga att identifiera avvikelser 

Följande fråga kan användas för att mata ut en avisering om ett fel upptäcks.
När Indataströmmen inte uniform kan aggregering steg omvandla det till en enhetlig tidsserie. I exemplet används Genomsnittlig men vilken typ av aggregering beror på användarscenariot. Dessutom när en tidsserie har luckor som är större än fönstret aggregering, det inte finns några händelser i tidsserien till utlösaren avvikelseidentifiering (enligt glidande fönstret semantik). Därför bryts antagandet om enhetlighet när nästa händelse kommer in. I så fall kan brister i tidsserien åtgärdas. En möjlig metod är att ta den sista händelsen i varje hopp-fönstret som visas nedan.

```sql
    WITH AggregationStep AS 
    (
         SELECT
               System.Timestamp as tumblingWindowEnd,

               AVG(value) as avgValue

         FROM input
         GROUP BY TumblingWindow(second, 5)
    ),

    FillInMissingValuesStep AS
    (
          SELECT
                System.Timestamp AS hoppingWindowEnd,

                TopOne() OVER (ORDER BY tumblingWindowEnd DESC) AS lastEvent

         FROM AggregationStep
         GROUP BY HOPPINGWINDOW(second, 300, 5)

    ),

    AnomalyDetectionStep AS
    (

          SELECT
                hoppingWindowEnd,
                lastEvent.tumblingWindowEnd as lastTumblingWindowEnd,
                lastEvent.avgValue as lastEventAvgValue,
                system.timestamp as anomalyDetectionStepTimestamp,

                ANOMALYDETECTION(lastEvent.avgValue) OVER (LIMIT DURATION(hour, 1)) as
                scores

          FROM FillInMissingValuesStep
    )

    SELECT
          alert = 1,
          hoppingWindowEnd,
          lastTumblingWindowEnd,
          lastEventAvgValue,
          anomalyDetectionStepTimestamp,
          scores

    INTO output

    FROM AnomalyDetectionStep

    WHERE

        CAST(GetRecordPropertyValue(scores, 'BiLevelChangeScore') as float) >= 3.25

        OR CAST(GetRecordPropertyValue(scores, 'SlowPosTrendScore') as float) >=
        3.25

       OR CAST(GetRecordPropertyValue(scores, 'SlowNegTrendScore') as float) >=
       3.25
```

## <a name="performance-guidance"></a>Prestandavägledning

* Använd sex strömningsenheter för jobb. 
* Skicka händelser till minst en sekund från varandra.
* En icke-partitionerad fråga som använder operatorn AnomalyDetection kan ge resultat med en beräkning fördröjning på cirka 25 ms i genomsnitt.
* Den svarstiden genom en partitionerad fråga varierar något med antalet partitioner, eftersom antalet beräkningar är högre. Svarstiden är dock ungefär densamma som icke-partitionerad fallet för en jämförbar Totalt antal händelser för alla partitioner.
* Vid läsning av real time data matas in stora mängder data snabbt. Bearbetning av dessa data är för närvarande långsammare. Svarstid i sådana scenarier hittades för att öka linjärt med antalet datapunkter i fönstret i stället för fönstret storlek eller händelse intervall. Överväg att använda en mindre storlek på fönstret för att minska svarstiden i real time fall. Överväg också att börja ditt jobb från den aktuella tiden. Några exempel på fördröjning i en icke-partitionerad fråga: 
    - 60 datapunkter i fönstret identifiering kan resultera i en fördröjning på 10 sekunder med en genomströmning på 3 Mbit/s. 
    - Vid 600 datapunkter når svarstiden cirka 80 sekunder med en genomströmning på 0,4 Mbit/s.

## <a name="limitations-of-the-anomalydetection-operator"></a>Begränsningar av operatorn AnomalyDetection 

* Den här operatorn stöder för närvarande inte topp- och dip-identifiering. Toppar och dalar är spontant eller tillfällig ändringar i tidsserien. 

* Den här operatorn hanteras för närvarande inte säsongsberoende mönster. Säsongsberoende mönster är upprepade mönster i data, till exempel en annan webbplats beteende under helger eller olika perioder trender under Black Friday, som inte är avvikelser men ett förväntat mönster i beteende. 

* Den här operatorn förväntar sig indata tidsserien vara enhetligt. En händelseström kan göras uniform genom att sammanställa över en rullande eller hoppar fönster. Ett rullande fönster är tillräckliga för att göra tidsserien uniform i scenarier där klyftan mellan händelser alltid är mindre än fönstret aggregering. När mellanrummet kan vara större, kan du fylla i luckorna genom att upprepa det sista värdet med ett Hoppande fönster. 

## <a name="references"></a>Referenser

* [Avvikelseidentifiering – använda maskininlärning att identifiera avvikelser i time series-data](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [Machine learning-API: T för avvikelseidentifiering](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [Time series-avvikelseidentifiering](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

