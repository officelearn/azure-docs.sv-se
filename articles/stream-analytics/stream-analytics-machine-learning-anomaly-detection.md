---
title: "Avvikelseidentifiering i Azure användning Guide (förhandsversion) | Microsoft Docs"
description: "Använd stream analytics och maskininlärning att identifiera avvikelser."
services: stream-analytics
documentationcenter: 
author: dubansal
manager: jhubbard
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/12/2018
ms.author: dubansal
ms.openlocfilehash: d8762ea608afed707d41a3c0a1a8725457a0e4dc
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Avvikelseidentifiering i Azure Stream Analytics

> [!IMPORTANT]
> Den här funktionen är i förhandsversionen kan inte använda med produktionsarbetsbelastningar rekommenderar.

Den **AnomalyDetection** operator som används för att identifiera olika typer av avvikelser i händelseströmmar. Till exempel en långsam minskning av ledigt minne under lång tid kan tyda på en minnesläcka eller antalet webbtjänstbegäranden som är stabil inom ett intervall kan kraftigt öka eller minska.  

Operatorn AnomalyDetection identifierar tre typer av avvikelser: 

* **Dubbelriktad nivå ändra**: en varaktig öka eller minska i nivån av värden, både uppåt och nedåt. Det här värdet skiljer sig från toppar och korta som ögonblicklig eller tillfällig ändringar.  

* **Långsamma positivt Trend**: en långsam ökning trend över tid.  

* **Långsamma negativa utvecklingen**: en långsam minskning i trend över tid.  

När du använder operatorn AnomalyDetection, måste du ange den **Limit Duration** satsen. Den här satsen Anger tidsintervall (hur långt tillbaka i historik från den aktuella händelsen) ska beaktas när upptäcka avvikelser. Den här operatorn kan eventuellt begränsas till endast de händelser som motsvarar en viss egenskap eller villkor med hjälp av den **när** satsen. Den här operatorn kan också bearbeta händelsegrupper separat baserat på nyckeln som anges i den **partitions av** satsen. Utbildning och förutsägelse ske separat för varje partition. 

## <a name="syntax-for-anomalydetection-operator"></a>Syntax för AnomalyDetection operatorn

`ANOMALYDETECTION(<scalar_expression>) OVER ([PARTITION BY <partition key>] LIMIT DURATION(<unit>, <length>) [WHEN boolean_expression])` 

**Exempel på användning**  

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id > 100) FROM input`

### <a name="arguments"></a>Argument

* **scalar_expression** -skalärt uttryck som utförs av avvikelseidentifiering. Tillåtna värden för den här parametern innehåller flyttal eller Bigint-datatyper som en enda (skalära) returvärde. Jokerteckenuttryck  **\***  tillåts inte. Skalärt uttryck får inte innehålla andra analysfunktioner eller externa funktioner. 

* **partition_by_clause** – `PARTITION BY <partition key>` satsen dividerar learning och utbildning över olika partitioner. Med andra ord en separat modell används per värde för `<partition key>` och endast händelser som har värdet används för inlärning och utbildning i den här modellen. Till exempel följande fråga tåg och resultat en läsning mot andra värden för samma sensorn:

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

* **satsen limit_duration** `DURATION(<unit>, <length>)` -Anger tidsintervall (hur långt tillbaka i historik från den aktuella händelsen) ska beaktas när upptäcka avvikelser. Se [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics) för en detaljerad beskrivning av enheter som stöds och deras förkortningar. 

* **when_clause** -anger ett booleskt villkor för händelser i beräkningen avvikelseidentifiering identifiering.

### <a name="return-types"></a>Returtyper

Operatorn AnomalyDetection returnerar en post som innehåller samtliga tre värden som utdata. De egenskaper som är associerade med de olika typerna av avvikelseidentifiering detektorerna är:

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

Extrahera enskilda värden utanför posten med hjälp av **GetRecordPropertyValue** funktion. Exempel:

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) > 3.25` 

Avvikelseidentifiering av en typ som har identifierats när något av avvikelseidentifiering poäng överskrider ett tröskelvärde. Tröskelvärdet kan vara ett flyttal tal > = 0. Tröskelvärdet är en kompromiss mellan känslighet och förtroende. Ett lägre tröskelvärde skulle göra identifiering känsliga för ändringar och flera aviseringar genereras ett högre tröskelvärde kan göra identifiering mindre känsliga och lita mer men maskera vissa avvikelser. Det exakta tröskelvärdet som ska använda beror på scenariot. Det finns ingen övre gräns, men det rekommenderade intervallet är 3,25 5. 

## <a name="anomaly-detection-algorithm"></a>Algoritm för avvikelseidentifiering

* AnomalyDetection operator använder en **oövervakad inlärning** metod där det inte att ta någon typ av distribution i händelserna. I allmänhet bevaras två modeller parallellt samtidigt, där en av dem används för poäng och andra har tränats i bakgrunden. Avvikelseidentifiering identifiering modeller tränas med hjälp av data från den aktuella dataströmmen snarare än att använda en out-of-band-mekanism. Mängden data som används för träning beror på fönstret storlek d som angetts av användaren i parametern Limit Duration. Varje modell slutar komma tränas baserat på d till 2d som händelser. Du bör ha minst 50 händelser i varje fönster för bästa resultat. 

* AnomalyDetection operator använder **glidande fönstret semantik** för att träna modeller och poäng händelser. Vilket innebär att varje händelse som ska utvärderas för avvikelseidentifiering och ett resultat returneras. Poängen är en indikation på konfidensnivån för den avvikelseidentifiering. 

* AnomalyDetection operatör innehåller en **repeterbarhet garanti** samma indata alltid ger samma resultat oavsett jobbutdata starttid. Starttid för jobbet utdata representerar den tid då händelsen första utdata produceras av jobbet. Den anges av användaren till den aktuella tiden, ett anpassat värde eller senast utdata (om jobbet hade tillverkas utdata tidigare). 

### <a name="training-the-models"></a>Utbildning modeller 

När tid fortlöper modeller tränas med andra data. Om du vill vara meningsfullt av poängen hjälper det för att förstå den underliggande mekanism som modeller tränas. Här, **t<sub>0</sub>**  är den **jobbutdata starttid** och **d** är den **fönsterstorlek** från Limit Duration parameter. Anta som tid är indelad i **hopp storlek d**början från `01/01/0001 00:00:00`. Följande steg används för att träna modellen och poäng händelser:

* När ett jobb startar det läser data från tiden t<sub>0</sub> – 2d.  
* När tid når nästa hopp, en ny modell M1 skapas och börjar få tränats.  
* När tid avancerar av en annan hopp, en ny modell M2 skapas och börjar få tränats.  
* När tid når t<sub>0</sub>, M1 görs aktiva och dess poäng börjar få för utdata.  
* Vid nästa hopp saker tre samtidigt:  

  * M1 längre behövs inte och tas bort.  
  * M2 har tränats tillräckligt ska användas för resultatfunktioner.  
  * En ny modell M3 skapas och börjar få utbildning i bakgrunden.  

* Den här cykeln upprepas för varje hopp där den aktiva modellen ignoreras växla till parallella modellen och starta en tredje modell i bakgrunden. 

Diagrammatically, stegen se ut så här: 

![Utbildning modeller](media/stream-analytics-machine-learning-anomaly-detection/training_model.png)

|Modellen | **Starttid för utbildning** | **Tid för att börja använda dess resultat** |
|---------|---------|---------|
|M1     | 11:20   | 11:33   |
|M2     | 11:30   | 11:40   |
|M3     | 11:40   | 11:50   |

* Modellen M1 startar kurser på 11:20:00, vilket är nästa hopp när jobbet startar läsning kl 11:13. Den första utdata från M1 kl 11:33 efter utbildning med 13 minuter av data. 

* En ny modell M2 startas även kurser på 11:30 am men inte hämta används dess poäng tills 11:40 am, vilket är när det har tränats med 10 minuter av data. 

* M3 följer samma mönster som M2. 

### <a name="scoring-with-the-models"></a>Bedömningen med modeller 

På nivån Machine Learning beräknar algoritmen för avvikelseidentifiering ett strangeness värde för varje inkommande händelse genom att jämföra med händelser i ett tidigare fönster. Beräkningen strangeness skiljer sig för varje typ av avvikelseidentifiering.  

Nu ska vi se strangeness beräkningen i detalj (antar historiska windows med händelser finns en): 

1. **Dubbelriktad förändring:** baserat på fönstret tidigare normala arbetsområde beräknas som [10: e percentilen, 90: e percentilen] som är 10 percentilvärdet som percentilvärdet nedre gräns och 90th som den övre gränsen. Värdet för det aktuella strangeness beräknas enligt följande:  

   - 0, om event_value är inom räckhåll för normal drift  
   - event_value/90th_percentile om event_value > 90th_percentile  
   - 10th_percentile/event_value, om event_value < 10th_percentile  

2. **Långsam positivt trend:** en trendlinje över händelse värdena i fönstret tidigare beräknas och vi ser för en positiv trend inom raden. Värdet för strangeness beräknas enligt följande:  

   - Lutning om lutning är positivt  
   - 0, annars 

1. **Långsam negativa utvecklingen:** en trendlinje över händelse värdena i fönstret tidigare beräknas och vi ser för negativa utvecklingen inom raden. Värdet för strangeness beräknas enligt följande: 

   - Lutning om lutning är negativt  
   - 0, annars  

När strangeness värde för inkommande händelse beräknas ett martingale värde beräknas utifrån strangeness värdet (finns i [Machine Learning-blogg](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/) information om hur martingale värdet beräknas). Det här värdet martingale retuned som avvikelseidentifiering poäng. Värdet martingale ökar långsamt svar på konstigt värden, vilket gör att detektorn förblir stabil sporadiska ändringar och minskar falsklarm. Det finns också en användbar egenskap: 

Sannolikheten [det finns t sådana som M<sub>t</sub> > λ] < 1/λ där M<sub>t</sub> är snabbmeddelanden t martingale värdet och λ är ett verkliga värde. Om vi meddela när exempelvis M<sub>t</sub>> 100 och sannolikheten för falska positiva identifieringar som är mindre än 1/100.  

## <a name="guidance-for-using-the-bi-directional-level-change-detector"></a>Riktlinjer för att använda nivån dubbelriktad ändra detektor 

Dubbelriktad förändring detektorn kan användas i scenarier power avbrott och återställning eller går lite för snabbt timme trafik, t.ex. Men den fungerar på ett sådant sätt att när en modell tränas med vissa data, förändring av en annan är avvikande endast om det nya värdet är högre än den tidigare övre gränsen (uppåtgående nivå ändra skiftläge) eller lägre än den tidigare nedre gränsen (nedåtgående nivå Ändra skiftläge). En modell bör därför inte se datavärdena i intervallet för den nya nivån (uppåt eller nedåt) i ett fönster för utbildning att anses vara avvikande. 

Följande punkter ska beaktas när det här detektor: 

1. När tidsserien plötsligt ser en ökning eller släppa värde, operatorn AnomalyDetection upptäcks. Men återgå till normal identifiering kräver mer planering. Om en tidsserie var i stabilt tillstånd innan avvikelseidentifiering som kan uppnås genom att ange operatorn AnomalyDetection identifiering fönstret högst hälften så långt som avvikelseidentifiering. Det här scenariot förutsätter att den kortaste varaktigheten för avvikelseidentifiering kan uppskattas i förväg och det finns tillräckligt med händelser i den aktuella tidsperioden för att träna modellen tillräckligt (minst 50 händelser). 

   Detta visas i figur 1 och 2 nedan med en övre gräns ändring (samma logik som gäller för en nedre gräns ändring). I båda bilderna är i vågformer en förändring av avvikande. Orange lodrätt betecknar hopp gränser och hoppstorleken är samma som angetts i operatorn AnomalyDetection identifiering-fönster. Grön linjer anger storleken på fönstret utbildning. I bild 1 är hoppstorleken samma som tiden för vilka avvikelseidentifiering varar. I bild 2 är hoppstorleken hälften av tid som avvikelseidentifiering varar. I samtliga fall måste har en ändring av uppåtgående identifierats eftersom den modell som används för resultatfunktioner har tränats på normala data. Men baserat på hur dubbelriktad förändring detektorn fungerar, vi får inte innehålla normal värdena från fönstret utbildning som används för den modell som poäng återgå till normal. I bild 1 innehåller bedömningsprofil modellen utbildning vissa vanliga händelser så att återgå till normal inte kan identifieras. Men i bild 2 utbildningen endast innehåller den avvikande delen, vilket garanterar att återgå till normal har identifierats. Mindre än hälften fungerar även av samma skäl medan allt större avslutas inklusive lite normal händelser. 

   ![AD med storlek lika avvikelseidentifiering längd](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_anomaly_length.png)

   ![AD med fönsterstorleken är lika med hälften av avvikelseidentifiering längd](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_half_anomaly_length.png)

2. I fall där avvikelseidentifiering längd inte kan förutsägas fungerar den här detektor i bästa prestanda. Dock väljer ett smalare tidsfönster begränsar utbildning, som ökar sannolikheten för att återgå till normal identifiering. 

3. I följande scenario är inte längre avvikelseidentifiering identifierat som fönstret utbildning innehåller redan en avvikelseidentifiering av samma högt värde. 

   ![Avvikelser med samma storlek](media/stream-analytics-machine-learning-anomaly-detection/anomalies_with_same_length.png)

## <a name="example-query-to-detect-anomalies"></a>Exempelfråga att identifiera avvikelser 

Följande fråga kan användas för att skicka en avisering om ett fel upptäcks.
När Indataströmmen inte uniform hjälper steget aggregering omvandla det till en enhetlig tidsserier. I exemplet används AVG men den speciella typ av aggregering beror på användarscenariot. Dessutom när en tidsserie har luckor som är större än fönstret aggregering, det inte finns några händelser i tidsserien till utlösaren avvikelseidentifiering (enligt glidande fönstret semantik). Därför är antagandet om enhetlighet bruten när nästa händelse tas emot. I sådana situationer ska i luckorna i tidsserien fyllas. En möjlig metod är att ta den sista händelsen i alla hopp fönster som visas nedan.

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

## <a name="performance-guidance"></a>Prestandaråd

* Använd sex enheter för strömning för jobb. 
* Skicka händelser till minst en sekund från varandra.
* En fråga som använder operatorn AnomalyDetection partitionerade kan ge resultat med en beräkning fördröjning på cirka 25 ms i genomsnitt.
* Svarstiden upplever en partitionerad fråga varierar något med antalet partitioner, som antalet beräkningar är högre. Svarstiden är dock ungefär som partitionerade fallet för en jämförbar Totalt antal händelser för alla partitioner.
* Vid läsning av data i real time, har en stor mängd data inhämtas snabbt. Bearbetning av dessa data är för närvarande långsammare. Fördröjning i sådana situationer hittades för att öka linjärt med antalet datapunkter i fönstret i stället för fönstret storlek eller händelse intervall. Överväg att använda en mindre fönsterstorlek för att minska fördröjningen i real time fall. Överväg också att starta jobbet från den aktuella tiden. Några exempel på fördröjningar i en icke-partitionerat fråga: 
    - 60 datapunkter i fönstret identifiering kan resultera i en fördröjning på 10 sekunder med en genomströmning på 3 Mbit/s. 
    - Fördröjningen kan nå ungefär 80 sekunder med en genomströmning på 0,4 Mbit/s på 600 datapunkter.

## <a name="limitations-of-the-anomalydetection-operator"></a>Begränsningar av operatorn AnomalyDetection 

* Den här operatorn stöder för närvarande inte insamling och dip identifiering. Toppar och korta är eget initiativ eller tillfällig ändringar i tidsserien. 

* Den här operatorn hanteras för närvarande inte säsongsvärdet mönster. Säsongsvärdet mönster är upprepade mönster i data, till exempel en annan trafik beteende under helger eller annan shopping trender under svart fredag, som inte är avvikelser, men ett förväntade mönster i beteende. 

* Den här operatorn förväntar sig indata tidsserien vara enhetligt. En händelseström kan göras enhetlig genom att sammanställa över en rullande eller hopping fönster. I scenarier där mellanrummet mellan händelser alltid är mindre än fönstret aggregering, är en rullande fönster tillräcklig för att tidsserien lika. När mellanrummet kan vara större, kan du fylla i luckorna genom att upprepa det sista värdet i ett Hoppande fönster. 

## <a name="references"></a>Referenser

* [Avvikelseidentifiering – använder maskininlärning att identifiera avvikelser i tid series-data](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [Maskininlärning avvikelseidentifiering API](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [Tid serien avvikelseidentifiering](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

