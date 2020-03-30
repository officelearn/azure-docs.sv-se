---
title: Skala machine learning-funktioner i Azure Stream Analytics
description: I den här artikeln beskrivs hur du skalar Stream Analytics-jobb som använder Machine Learning-funktioner genom att konfigurera partitionerings- och dataströmsenheter.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 5b08625d055063b3804a35a3344ff01c7edb79de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067009"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Skala ditt Stream Analytics-jobb med Azure Machine Learning Studio (klassiska) funktioner

> [!TIP]
> Vi rekommenderar starkt att du använder [Azure Machine Learning UDFs](machine-learning-udf.md) i stället för Azure Machine Learning Studio (klassisk) UDF för förbättrad prestanda och tillförlitlighet.

I den här artikeln beskrivs hur du effektivt skalar Azure Stream Analytics-jobb som använder Azure Machine Learning-funktioner. Information om hur du skalar Stream Analytics-jobb i allmänhet finns i artikeln [Skalning av jobb](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Vad är en Azure Machine Learning-funktion i Stream Analytics?

En machine learning-funktion i Stream Analytics kan användas som ett vanligt funktionsanrop i frågespråket Stream Analytics. Bakom kulisserna är dock dessa funktionsanrop i själva verket Azure Machine Learning Web Service-begäranden.

Du kan förbättra dataflödet för machine learning-webbtjänstbegäranden genom att "batcha" flera rader tillsammans i samma API-anrop för webbtjänsten. Den här gruppningen kallas en minibatch. Mer information finns i [Azure Machine Learning Studio (klassisk) Web Services](../machine-learning/studio/consume-web-services.md). Stöd för Azure Machine Learning Studio (klassisk) i Stream Analytics är i förhandsversion.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Konfigurera ett Stream Analytics-jobb med machine learning-funktioner

Det finns två parametrar för att konfigurera machine learning-funktionen som används av ditt Stream Analytics-jobb:

* Batchstorlek för machine learning-funktionsanrop.
* Antalet SUs-enheter (Streaming Units) som etablerats för stream analytics-jobbet.

Bestäm om du vill optimera svarstiden för Stream Analytics-jobbet eller dataflödet för varje SU för att fastställa lämpliga värden för SUs. SUs kan alltid läggas till i ett jobb för att öka dataflödet för en väl partitionerad Stream Analytics-fråga. Ytterligare SUs ökar kostnaden för att köra jobbet.

Bestäm *svarstidstoleransen* för ditt Stream Analytics-jobb. Om du ökar batchstorleken ökar svarstiden för dina Azure Machine Learning-begäranden och svarstiden för Stream Analytics-jobbet.

Om du ökar batchstorleken kan Stream Analytics-jobbet bearbeta **fler händelser** med **samma antal** begäranden om maskininlärningswebbtjänst. Ökningen av svarstiden för Machine Learning-webbtjänsten är vanligtvis sublinjär till ökningen av batchstorleken. 

Det är viktigt att tänka på den mest kostnadseffektiva batchstorleken för en machine learning-webbtjänst i en given situation. Standardbatchstorleken för webbtjänstbegäranden är 1000. Du kan ändra den här standardstorleken med hjälp av [REST-APIN](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "REST-API för Stream Analytics") för Stream Analytics eller [PowerShell-klienten för Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

När du har bestämt dig för en batchstorlek kan du ange antalet strömningsenheter (SUs), baserat på antalet händelser som funktionen behöver bearbeta per sekund. Mer information om strömningsenheter finns i [Stream Analytics skala jobb](stream-analytics-scale-jobs.md).

Varje 6 SUs får 20 samtidiga anslutningar till machine learning-webbtjänsten. Men 1 SU jobb och 3 SU jobb får 20 samtidiga anslutningar.  

Om ditt program genererar 200 000 händelser per sekund och batchstorleken är 1000, är den resulterande svarstiden för webbtjänsten 200 ms. Den här hastigheten innebär att varje anslutning kan göra fem förfrågningar till machine learning-webbtjänsten varje sekund. Med 20 anslutningar kan Stream Analytics-jobbet bearbeta 20 000 händelser under 200 ms och 100 000 händelser på en sekund.

Om du vill bearbeta 200 000 händelser per sekund behöver Stream Analytics-jobbet 40 samtidiga anslutningar, som kommer ut till 12 SUs. Följande diagram illustrerar begäranden från Stream Analytics-jobbet till slutpunkten för Machine Learning-webbtjänsten – Var sjätte SUs har 20 samtidiga anslutningar till Machine Learning-webbtjänsten på max.

![Skala Stream Analytics med Machine Learning Functions två jobbexempel](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Skala Stream Analytics med Machine Learning Functions två jobbexempel")

I allmänhet ***B*** för batchstorlek, ***L*** för webbtjänst svarstiden vid batchstorlek B i millisekunder, är dataflödet för ett Stream Analytics-jobb med ***N*** SUs:

![Skala Stream Analytics med machine learning-funktioner formel](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Skala Stream Analytics med machine learning-funktioner formel")

Du kan också konfigurera "max samtidiga samtal" på machine learning-webbtjänsten. Vi rekommenderar att du ställer in den här parametern på det maximala värdet (200 för närvarande).

Mer information om den här inställningen finns i [skalningsartikeln för Machine Learning Web Services](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Exempel – Sentimentanalys
Följande exempel innehåller ett Stream Analytics-jobb med sentimentanalysen Machine Learning-funktionen, enligt beskrivningen i [självstudiekursen för integrering av Dataanalysmaskininlärning](stream-analytics-machine-learning-integration-tutorial.md).

Frågan är en enkel helt partitionerad fråga följt av **sentimentfunktionen,** som visas i följande exempel:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Låt oss undersöka den konfiguration som krävs för att skapa ett Stream Analytics-jobb, som gör sentimentanalys av tweets med en hastighet av 10 000 tweets per sekund.

Kan det här Stream Analytics-jobbet hantera trafiken med 1 SU? Jobbet kan hålla jämna steg med indata med standardbatchstorleken 1000. Standardfördröjningen för sentimentanalysen Machine Learning-webbtjänsten (med en standardbatchstorlek på 1000) skapar inte mer än en sekund av svarstiden.

Stream Analytics-jobbets **övergripande** eller end-to-end-svarstid skulle normalt vara några sekunder. Ta en mer detaljerad titt på det här Stream Analytics-jobbet, *särskilt* machine learning-funktionsanropen. Med en batchstorlek på 1000 tar ett dataflöde på 10 000 händelser cirka 10 förfrågningar till webbtjänsten. Även med en SU, det finns tillräckligt med samtidiga anslutningar för att rymma denna indatatrafik.

Om indatahändelsehastigheten ökar med 100 x måste Stream Analytics-jobbet bearbeta 1 000 000 tweets per sekund. Det finns två alternativ för att uppnå den ökade skalan:

1. Öka batchstorleken.
2. Partitionera indataströmmen för att bearbeta händelserna parallellt.

Med det första alternativet ökar **jobbfördröjningen.**

Med det andra alternativet måste du etablera fler SUs för att få fler samtidiga machine learning-webbtjänstförfrågningar. Detta större antal SUs, ökar **jobbkostnaden**.

Låt oss titta på skalningen med hjälp av följande svarstidsmätningar för varje batchstorlek:

| Svarstid | Batchstorlek |
| --- | --- |
| 200 ms | 1000-eventbatseller nedan |
| 250 ms | 5 000-händelsebatch |
| 300 ms | 10 000-händelsebatch |
| 500 ms | 25 000-händelsebatch |

1. Använda det första alternativet **(inte** etablera fler SUs). Batchstorleken kan ökas till **25 000.** Om du ökar batchstorleken på det här sättet kan jobbet bearbeta 1 000 000 händelser med 20 samtidiga anslutningar till machine learning-webbtjänsten (med en svarstid på 500 ms per anrop). Så den ytterligare svarstiden för Stream Analytics-jobbet på grund av sentimentfunktionsbegäranden mot begäranden om machine learning-webbtjänst skulle ökas från **200 ms** till **500 ms**. Batchstorleken **kan** dock inte ökas oändligt eftersom Machine Learning-webbtjänsterna kräver att nyttolasten för en begäran är 4 MB eller mindre och webbtjänsten begär timeout efter 100 sekunders drift.
1. Med det andra alternativet är batchstorleken kvar på 1000, med 200-ms webbtjänst svarstid, varje 20 samtidiga anslutningar till webbtjänsten skulle kunna bearbeta 1000 * 20 * 5 händelser = 100.000 per sekund. Så för att bearbeta 1.000.000 händelser per sekund, skulle jobbet behöver 60 SUs. Jämfört med det första alternativet skulle Stream Analytics-jobbet göra fler batchbegäranden för webbtjänster, vilket i sin tur genererar en ökad kostnad.

Nedan finns en tabell för dataflödet för Stream Analytics-jobbet för olika SUs och batchstorlekar (i antal händelser per sekund).

| batchstorlek (ML-svarstid) | 500 (200 ms) | 1 000 (200 ms) | 5 000 (250 ms) | 10 000 (300 ms) | 25 000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU (AVSU)** |2 500 |5 000 |20 000 |30,000 |50 000 |
| **3 SUs** |2 500 |5 000 |20 000 |30,000 |50 000 |
| **6 SUs** |2 500 |5 000 |20 000 |30,000 |50 000 |
| **12 SUs** |5 000 |10 000 |40 000 |60 000 |100 000 |
| **18 SUs** |7 500 |15 000 |60 000 |90 000 |150 000 |
| **24 SUs** |10 000 |20 000 |80 000 |120,000 |200 000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25,000 |50 000 |200 000 |300,000 |500 000 |

Vid det här laget bör du redan ha en god förståelse för hur Machine Learning fungerar i Stream Analytics fungerar. Du förstår förmodligen också att Stream Analytics-jobb "hämtar" data från datakällor och varje "pull" returnerar en grupp händelser som Stream Analytics-jobbet ska bearbeta. Hur påverkar den här pull-modellen begäranden om machine learning-webbtjänst?

Normalt är batchstorleken som vi anger för Machine Learning-funktioner inte exakt delbar av antalet händelser som returneras av varje Stream Analytics-jobb "pull". När detta inträffar anropas machine learning-webbtjänsten med "partiella" batchar. Genom att använda partiella batchar undviker du ytterligare jobbfördröjningar i sammanslagningshändelser från pull till pull.

## <a name="new-function-related-monitoring-metrics"></a>Nya funktionsrelaterade övervakningsmått
I området Övervakare för ett Stream Analytics-jobb har ytterligare tre funktionsrelaterade mått lagts till. De är **funktionsbegäranden,** **funktionshändelser** och **misslyckade funktionsbegäranden,** som visas i bilden nedan.

![Skala Stream Analytics med mätvärden för maskininlärningsfunktioner](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Skala Stream Analytics med mätvärden för maskininlärningsfunktioner")

Definieras på följande sätt:

**FUNKTIONSBEGÄRANDEN**: Antalet funktionsbegäranden.

**FUNKTIONSHÄNDELSER**: Antalet händelser i funktionsbegäranden.

**MISSLYCKADE FUNKTIONSBEGÄRANDEN**: Antalet misslyckade funktionsbegäranden.

## <a name="key-takeaways"></a>Viktiga takeaways

Om du vill skala ett Stream Analytics-jobb med machine learning-funktioner bör du tänka på följande:

1. Indatahändelsehastigheten.
2. Den tolererade svarstiden för det strömmande dataspelsjobbet som körs (och därmed batchstorleken för begäranden om machine learning-webbtjänst).
3. De etablerade S:erna för Stream Analytics och antalet söktjänstbegäranden för maskininlärning (de extra funktionsrelaterade kostnaderna).

En helt partitionerad Stream Analytics-fråga användes som ett exempel. Om en mer komplex fråga behövs är [Azure Stream Analytics-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) en bra resurs för att få ytterligare hjälp från Stream Analytics-teamet.

## <a name="next-steps"></a>Nästa steg
Mer information om Stream Analytics finns i:

* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)
