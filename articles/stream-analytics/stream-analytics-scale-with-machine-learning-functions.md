---
title: Skala Machine Learning-funktioner i Azure Stream Analytics
description: Den här artikeln beskriver hur du skalar Stream Analytics-jobb som använder Machine Learning-funktioner genom att konfigurera enheter för partitionering och stream.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 5da09d705246ffd5002a1a21daab2266525f579e
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357499"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-functions"></a>Skala ditt Stream Analytics-jobb med Azure Machine Learning Studio-funktioner

Den här artikeln beskriver hur du effektivt skala Azure Stream Analytics-jobb som använder Azure Machine Learning-funktioner. Information om hur du skalar Stream Analytics-jobb i allmänhet finns i artikeln [skalning jobb](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Vad är en Azure Machine Learning-funktionen i Stream Analytics?

En Machine Learning-funktionen i Stream Analytics kan användas som ett reguljärt funktionsanrop i Stream Analytics-frågespråket. I bakgrunden, men är de här funktionsanropen faktiskt Azure Machine Learning-webbtjänsten begäranden.

Du kan förbättra dataflödet för Machine Learning webbtjänstbegäranden genom ”batchbearbetning” flera rader tillsammans i samma webb-API-anrop. Den här grupperingen kallas en mini batch. Mer information finns i [Azure Machine Learning Studio-webbtjänster](../machine-learning/studio/consume-web-services.md). Stöd för Azure Machine Learning Studio i Stream Analytics finns i förhandsversion.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Konfigurera ett Stream Analytics-jobb med Machine Learning-funktioner

Det finns två parametrar för konfiguration av Machine Learning-funktion som används av ditt Stream Analytics-jobb:

* Batchstorlek för Machine Learning-funktionsanrop.
* Antal enheter för strömning (su) har etablerats för Stream Analytics-jobb.

Bestäm om du vill optimera svarstiden för Stream Analytics-jobb eller dataflödet för varje SU för att avgöra lämpliga värden för su: er. SUs kan alltid att lägga till ett jobb för att öka dataflödet för en väl partitionerade Stream Analytics-fråga. Ytterligare SUs ökar kostnaden för att köra jobbet.

Avgöra svarstiden *tolerans* för ditt Stream Analytics-jobb. Öka batchstorleken ökar svarstiden för dina Azure Machine Learning tjänstbegäranden och svarstiden för Stream Analytics-jobb.

Öka batchstorleken kan Stream Analytics-jobb att bearbeta **fler händelser** med den **samma antal** av Machine Learning web tjänstbegäranden. I Machine Learning web service svarstiden är vanligtvis sublinear ökning på batchstorlek. 

Det är viktigt att tänka på den mest kostnadseffektiva batchstorleken för ett Machine Learning-webbtjänsten i en viss situation. Standard batchstorlek för webbtjänstbegäranden är 1 000. Du kan ändra denna standard storlek med den [Stream Analytics REST API](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "Stream Analytics REST API") eller [PowerShell-klienten för Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

När du har bestämt dig för en batchstorlek, du kan ange antalet strömningsenheter (su) baserat på antalet händelser som funktionen behöver bearbeta per sekund. Mer information om enheter för strömning finns [Stream Analytics skala jobb](stream-analytics-scale-jobs.md).

Varje 6 SUs få 20 samtidiga anslutningar till Machine Learning-webbtjänsten. 1 SU-jobb och 3 SU jobb får dock 20 samtidiga anslutningar.  

Om ditt program genererar 200 000 händelser per sekund och batchstorlek är 1 000, är den resulterande web service svarstiden 200 ms. Priset innebär att alla anslutningar kan göra fem begäranden till Machine Learning-webbtjänsten varje sekund. Med 20 anslutningar kan Stream Analytics-jobbet bearbeta 20 000 händelser på 200 ms och 100 000 händelser på en sekund.

Om du vill bearbeta 200 000 händelser per sekund, behöver Stream Analytics-jobbet 40 samtidiga anslutningar, som kommer till 12 SUs. Följande diagram illustrerar begäranden från Stream Analytics-jobbet till Machine Learning-webbtjänstslutpunkt – var 6 SUs har 20 samtidiga anslutningar till Machine Learning-webbtjänst på max.

![Skala Stream Analytics med Machine Learning-funktioner två jobb exempel](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "skala Stream Analytics med Machine Learning-funktioner två jobb-exempel")

I allmänhet ***B*** för batchstorlek, ***L*** för web service svarstid på batchstorlek B i millisekunder, dataflödet för ett Stream Analytics-jobbet med ***N*** su: er är:

![Skala Stream Analytics med Machine Learning-funktioner formel](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "skala Stream Analytics med formeln för Machine Learning-funktioner")

Du kan också konfigurera 'max samtidiga anrop' på Machine Learning-webbtjänsten. Vi rekommenderar att du anger parametern till det högsta värdet (för närvarande 200).

Mer information om den här inställningen det [skalning artikeln för Machine Learning Web Services](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Exempel – Attitydanalys
I följande exempel innehåller ett Stream Analytics-jobb med attitydanalys Machine Learning-funktion, enligt beskrivningen i den [Stream Analytics, Machine Learning-integrationsvägledning](stream-analytics-machine-learning-integration-tutorial.md).

Frågan är en enkel fullständigt partitionerad fråga följt av den **sentiment** fungerar, som visas i följande exempel:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Låt oss nu undersöka konfigurationen som krävs för att skapa ett Stream Analytics-jobb, vilket gör attitydanalys för tweets med en hastighet av 10 000 tweets per sekund.

Med 1 SU, kan den här Stream Analytics-jobbet hantera trafiken? Jobbet kan hålla jämna steg med indata med batch standardstorlek på 1 000. Standard-svarstiden för attitydanalys Machine Learning-webbtjänsten (med en batch standardstorlek på 1 000) skapar inte mer än en sekund fördröjning.

Stream Analytics-jobbet **övergripande** eller svarstid slutpunkt till slutpunkt är vanligtvis några sekunder. Mer detaljerad titta i den här Stream Analytics-jobbet *särskilt* Machine Learning-funktionsanrop. Med en batchstorlek på 1 000 tar en genomströmning på 10 000 händelserna cirka 10 förfrågningar till webbtjänsten. Det finns tillräckligt många samtidiga anslutningar för den här inkommande trafik även med en SU.

Om den inkommande takten ökar 100 x, behöver Stream Analytics-jobb att bearbeta 1 000 000 tweets per sekund. Det finns två alternativ för att uppnå ökad skala:

1. Förstora batch.
2. Partitionera Indataströmmen för att bearbeta händelser parallellt.

Med det första alternativet, jobbet **svarstid** ökar.

Med det andra alternativet, kommer du behöva etablera flera SUs så att den har flera samtidiga webbtjänstbegäranden för Machine Learning. Den här större antalet SUs, ökar jobbet **kostnaden**.

Nu ska vi titta på skalning med följande replikeringssvarstiderna för varje batchstorlek:

| Svarstid | Batchstorlek |
| --- | --- |
| 200 ms | 1000-händelse batchar eller nedan |
| 250 ms | 5 000 event batchar |
| 300 ms | 10 000-händelse batchar |
| 500 ms | 25 000 event batchar |

1. Med hjälp av det första alternativet (**inte** etablering flera SUs). Batchstorlek kan ökas till **25 000**. Öka batchstorlek på så vis kan jobbet att bearbeta 1 000 000 händelser med 20 samtidiga anslutningar till Machine Learning-webbtjänsten (med en fördröjning på 500 ms per anrop). Så den förlängda svarstiden för Stream Analytics-jobbet på grund av sentiment funktionsförfrågningar mot Machine Learning-webbtjänstbegäranden skulle ökas från **200 ms** till **500 ms**. Dock batchstorlek **kan** ökas oändligt de Machine Learning-webbtjänster kräver nyttolasten storleken på en begäran är 4 MB eller mindre och web timeout för begäranden av tjänsten när du har 100 sekunder för åtgärden.
1. Med det andra alternativet kan batchstorleken lämnas på 1000, med en svarstid på 200 ms web service, var 20 samtidiga anslutningar till webbtjänsten skulle kunna bearbeta 5 * 20 * 1 000 händelser = 100 000 per sekund. Om du vill bearbeta 1 000 000 händelser per sekund, så måste jobbet 60 su: er. Jämfört med det första alternativet, blir Stream Analytics-jobb mer batch webbtjänstbegäranden, i sin tur genererar en ökad kostnad.

Nedan visas en tabell för dataflödet för Stream Analytics-jobb för olika su: er och batch-storlekar (i antal händelser per sekund).

| batchstorlek (ML svarstiden) | 500 (200 ms) | 1 000 (200 ms) | 5,000 (250 ms) | 10 000 (300 ms) | 25,000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3 SUs** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6 SUs** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12 SUs** |5,000 |10 000 |40,000 |60,000 |100,000 |
| **18 SUs** |7,500 |15,000 |60,000 |90,000 |150,000 |
| **24 SUs** |10 000 |20,000 |80,000 |120,000 |200 000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25,000 |50,000 |200 000 |300,000 |500,000 |

Nu bör bör du redan ha en god förståelse av hur Machine Learning-funktioner i Stream Analytics fungerar. Du troligen förstår även att Stream Analytics-jobb ”pull”-data från datakällor och varje ”pull” returnerar en batch med händelser för Stream Analytics-jobb att bearbeta. Hur web tjänstbegäranden i den här pull-modellen inverkan Machine Learning?

Batchstorlek som vi har angetts för Machine Learning-funktioner vara inte normalt sett exakt delbart med antalet händelser som returneras av varje Stream Analytics-jobbet ”pull”. När detta inträffar kan anropas Machine Learning-webbtjänst med ”partiella” batchar. Med hjälp av partiella batchar undviker ådrar sig några ytterligare jobbdata för elasticitet i buffertsammanslagning händelser från pull till pull.

## <a name="new-function-related-monitoring-metrics"></a>Ny funktion-relaterade övervakning mått
Tre ytterligare funktion-relaterade mått har lagts till i området Övervakare i ett Stream Analytics-jobb. De är **FUNKTIONSFÖRFRÅGNINGAR**, **FUNKTIONSHÄNDELSER** och **misslyckades FUNKTIONSFÖRFRÅGNINGAR**, enligt bilden nedan.

![Skala Stream Analytics med Machine Learning-funktioner mått](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "skala Stream Analytics med Machine Learning-funktioner mått")

Finns definieras enligt följande:

**FUNKTIONSFÖRFRÅGNINGAR**: Antal begäranden för funktionen.

**FUNKTIONSHÄNDELSER**: Antalet händelser i function-begäranden.

**MISSLYCKADE FUNKTIONSFÖRFRÅGNINGAR**: Antal misslyckade funktionsförfrågningar.

## <a name="key-takeaways"></a>Viktiga Takeaways

Om du vill skala ett Stream Analytics-jobb med Machine Learning-funktioner, Tänk på följande:

1. Takten för indata.
2. Tolererat svarstiden för att köra Stream Analytics-jobbet (och därmed batchstorlek för Machine Learning-webbtjänstbegäranden).
3. Etablerade Stream Analytics SUs och antalet Machine Learning webbtjänstbegäranden (ytterligare funktion-relaterade kostnader).

En fullständigt partitionerade Stream Analytics-fråga användes som ett exempel. Om en mer komplex fråga krävs det [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) är en fantastisk resurs för att få ytterligare hjälp från Stream Analytics-teamet.

## <a name="next-steps"></a>Nästa steg
Mer information om Stream Analytics finns:

* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
