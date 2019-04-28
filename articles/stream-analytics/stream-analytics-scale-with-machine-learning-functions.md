---
title: Skala Machine Learning-funktioner i Azure Stream Analytics
description: Den här artikeln beskriver hur du skalar Stream Analytics-jobb som använder Machine Learning-funktioner genom att konfigurera enheter för partitionering och stream.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: f11034a4970e3fb95333310af82a6b2a2551f1eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479159"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>Skala ditt Stream Analytics-jobb med Azure Machine Learning-funktioner
Det är enkelt att konfigurera ett Stream Analytics-jobb och köra exempeldata genom den. Vad gör vi när vi behöver köra samma jobb med högre datavolym? Det krävs oss att förstå hur du konfigurerar Stream Analytics-jobbet så att den växer. I det här dokumentet har fokusera vi på särskilda aspekter av en Stream Analytics-jobb med Machine Learning-funktioner. Information om hur du skalar Stream Analytics-jobb i allmänhet finns i artikeln [skalning jobb](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Vad är en Azure Machine Learning-funktionen i Stream Analytics?
En Machine Learning-funktionen i Stream Analytics kan användas som ett reguljärt funktionsanrop i Stream Analytics-frågespråket. Bakom scenen är dock funktionsanropen faktiskt Azure Machine Learning-webbtjänsten begäranden. Machine Learning-webbtjänster stöd ”batchbearbetning” flera rader kallas Mini batch i samma API webbtjänstanropet, att förbättra hela dataflödet. Mer information finns i [Azure Machine Learning Web Services](../machine-learning/studio/consume-web-services.md).

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Konfigurera ett Stream Analytics-jobb med Machine Learning-funktioner
När du konfigurerar en Machine Learning-funktion för Stream Analytics-jobbet, finns det två parametrar att tänka på, batchstorlek för Machine Learning-funktionsanrop och strömningsenheter (su) som tillhandahållits för Stream Analytics-jobb. För att avgöra lämpliga värden för SUs måste först ett beslut göras mellan svarstid och dataflöde, det vill säga svarstiden för Stream Analytics-jobb, och genomströmning på varje SU. SUs kan alltid lägga till ett jobb för att öka genomflödet av en bra partitionerade Stream Analytics-fråga, men ytterligare SUs ökar kostnaden för att köra jobbet.

Därför är det viktigt att fastställa den *tolerans* fördröjning körs ett Stream Analytics-jobb. Ytterligare fördröjning från att köra Azure Machine Learning tjänstbegäranden ökas naturligt med batchstorlek som föreningar svarstiden för Stream Analytics-jobb. Å andra sidan ökar batchstorlek kan Stream Analytics-jobb att bearbeta * fler händelser med den *samma antal* av Machine Learning web tjänstbegäranden. Ökningen av svarstiden för Machine Learning web service är ofta sublinear ökning på batchstorlek så det är viktigt att tänka på den mest kostnadseffektiva batchstorleken för ett Machine Learning-webbtjänsten i en viss situation. Standardstorleken för batch för webbtjänsten begär är 1 000 och kan ändras antingen med hjälp av den [Stream Analytics REST API](https://msdn.microsoft.com/library/mt653706.aspx "Stream Analytics REST API") eller [PowerShell-klienten för Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md "PowerShell-klienten för Stream Analytics").

När en batchstorlek har fastställts, antalet strömmande enheter (su) kan fastställas, baserat på antalet händelser som funktionen behöver bearbeta per sekund. Mer information om enheter för strömning finns [Stream Analytics skala jobb](stream-analytics-scale-jobs.md).

I allmänhet är det 20 samtidiga anslutningar till Machine Learning-webbtjänsten för varje 6 SUs, förutom att en SU-jobb och 3 SU jobb 20 samtidiga anslutningar också.  Om indata är 200 000 händelser per sekund och batchstorleken lämnas till standardvärdet på 1 000 är den resulterande web service svarstiden med 1000 händelser Mini batch 200 ms. Det innebär att alla anslutningar kan göra fem förfrågningar till Machine Learning-webbtjänst på en sekund. Med 20 anslutningar kan Stream Analytics-jobbet bearbeta 20 000 händelser på 200 ms och därför 100 000 händelser på en sekund. Därför måste Stream Analytics-jobbet för att bearbeta 200 000 händelser per sekund, 40 samtidiga anslutningar, som kommer till 12 SUs. Följande diagram illustrerar begäranden från Stream Analytics-jobbet till Machine Learning-webbtjänstslutpunkt – var 6 SUs har 20 samtidiga anslutningar till Machine Learning-webbtjänst på max.

![Skala Stream Analytics med Machine Learning-funktioner två jobb exempel](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "skala Stream Analytics med Machine Learning-funktioner två jobb-exempel")

I allmänhet ***B*** för batchstorlek, ***L*** för web service svarstid på batchstorlek B i millisekunder, dataflödet för ett Stream Analytics-jobbet med ***N*** su: er är:

![Skala Stream Analytics med Machine Learning-funktioner formel](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "skala Stream Analytics med formeln för Machine Learning-funktioner")

En ytterligare överväganden kan vara 'max samtidiga anrop' på Machine Learning web service-sidan, vi rekommenderar att du väljer det högsta värdet (för närvarande 200).

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
Föreställ dig följande; med en genomströmning på 10 000 tweets per sekund måste du skapa ett Stream Analytics-jobb för att utföra känsloanalys av tweets (händelser). Med 1 SU kan den här Stream Analytics-jobb att kunna hantera trafiken? Med batch standardstorlek på 1 000 ska jobbet kunna hålla jämna steg med indata. Ytterligare ska har lagts till Machine Learning-funktionen Skapa inte fler än en sekund för svarstider, vilket är allmänna standard svarstiden för attitydanalys Machine Learning-webbtjänsten (med en batch standardstorlek på 1 000). Stream Analytics-jobbet **övergripande** eller svarstid slutpunkt till slutpunkt är vanligtvis några sekunder. Mer detaljerad titta i den här Stream Analytics-jobbet *särskilt* Machine Learning-funktionsanrop. Med batchstorlek som 1000, en genomströmning på 10 000 händelserna ta ungefär 10 förfrågningar till webbtjänsten. Det finns tillräckligt många samtidiga anslutningar för den här inkommande trafik även med en SU.

Om den inkommande takten ökar 100 x, behöver Stream Analytics-jobb att bearbeta 1 000 000 tweets per sekund. Det finns två alternativ för att uppnå ökad skala:

1. Öka batchstorleken på, eller
2. Partition Indataströmmen att bearbeta händelser parallellt

Med det första alternativet, jobbet **svarstid** ökar.

Med det andra alternativet måste flera SUs etableras och därför skapa flera samtidiga webbtjänstbegäranden för Machine Learning. Det innebär att jobbet **kostnaden** ökar.

Anta att svarstiden för attitydanalys Machine Learning-webbtjänsten är 200 ms för 1000-händelse batchar eller nedan, 250 ms för 5 000 event batchar, 300 ms för 10 000-händelse batchar eller 500 ms för 25 000 event batchar.

1. Med hjälp av det första alternativet (**inte** etablering flera SUs). Batchstorlek kan ökas till **25 000**. Det skulle i sin tur att jobbet att bearbeta 1 000 000 händelser med 20 samtidiga anslutningar till Machine Learning-webbtjänsten (med en fördröjning på 500 ms per anrop). Så den förlängda svarstiden för Stream Analytics-jobbet på grund av sentiment funktionsförfrågningar mot Machine Learning-webbtjänstbegäranden skulle ökas från **200 ms** till **500 ms**. Dock batchstorlek **kan** ökas oändligt de Machine Learning-webbtjänster kräver nyttolasten storleken på en begäran är 4 MB eller mindre webbtjänsten begär timeout efter 100 sekunder för åtgärden.
2. Med det andra alternativet kan batchstorleken lämnas på 1000, med en svarstid på 200 ms web service, var 20 samtidiga anslutningar till webbtjänsten skulle kunna bearbeta 5 * 20 * 1 000 händelser = 100 000 per sekund. Om du vill bearbeta 1 000 000 händelser per sekund, så måste jobbet 60 su: er. Jämfört med det första alternativet, blir Stream Analytics-jobb mer batch webbtjänstbegäranden, i sin tur genererar en ökad kostnad.

Nedan visas en tabell för dataflödet för Stream Analytics-jobb för olika su: er och batch-storlekar (i antal händelser per sekund).

| batchstorlek (ML svarstiden) | 500 (200 ms) | 1 000 (200 ms) | 5,000 (250 ms) | 10 000 (300 ms) | 25,000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3 SUs** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6 SUs** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12 SUs** |5 000 |10 000 |40,000 |60,000 |100 000 |
| **18 SUs** |7 500 |15,000 |60,000 |90,000 |150,000 |
| **24 SUs** |10 000 |20,000 |80,000 |120,000 |200 000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25,000 |50,000 |200 000 |300,000 |500,000 |

Nu bör bör du redan ha en god förståelse av hur Machine Learning-funktioner i Stream Analytics fungerar. Du troligen förstår även att Stream Analytics-jobb ”pull”-data från datakällor och varje ”pull” returnerar en batch med händelser för Stream Analytics-jobb att bearbeta. Hur web tjänstbegäranden i den här pull-modellen inverkan Machine Learning?

Batchstorlek som vi har angetts för Machine Learning-funktioner vara inte normalt sett exakt delbart med antalet händelser som returneras av varje Stream Analytics-jobbet ”pull”. När detta inträffar kan anropas Machine Learning-webbtjänst med ”partiella” batchar. Detta görs för att inte någon ytterligare jobb för elasticitet i buffertsammanslagning händelser från pull till pull.

## <a name="new-function-related-monitoring-metrics"></a>Ny funktion-relaterade övervakning mått
Tre ytterligare funktion-relaterade mått har lagts till i området Övervakare i ett Stream Analytics-jobb. De är FUNKTIONSFÖRFRÅGNINGAR, FUNKTIONSHÄNDELSER och misslyckades FUNKTIONSFÖRFRÅGNINGAR enligt bilden nedan.

![Skala Stream Analytics med Machine Learning-funktioner mått](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "skala Stream Analytics med Machine Learning-funktioner mått")

Finns definieras enligt följande:

**FUNKTIONSFÖRFRÅGNINGAR**: Antal begäranden för funktionen.

**FUNKTIONSHÄNDELSER**: Antalet händelser i function-begäranden.

**MISSLYCKADE FUNKTIONSFÖRFRÅGNINGAR**: Antal misslyckade funktionsförfrågningar.

## <a name="key-takeaways"></a>Viktiga Takeaways
Följande objekt måste beaktas för att sammanfatta de viktigaste aspekterna för att skala ett Stream Analytics-jobb med Machine Learning-funktioner:

1. Takten för indata
2. Tolererat svarstiden för att köra Stream Analytics-jobbet (och därmed batchstorlek för tjänstbegäranden för Machine Learning web)
3. Etablerade Stream Analytics SUs och antalet Machine Learning webbtjänstbegäranden (ytterligare funktion-relaterade kostnader)

En fullständigt partitionerade Stream Analytics-fråga användes som ett exempel. Om en mer komplex fråga krävs det [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) är en fantastisk resurs för att få ytterligare hjälp från Stream Analytics-teamet.

## <a name="next-steps"></a>Nästa steg
Mer information om Stream Analytics finns:

* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
