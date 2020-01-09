---
title: Skala Machine Learning funktioner i Azure Stream Analytics
description: I den här artikeln beskrivs hur du skalar Stream Analytics jobb som använder Machine Learning funktioner, genom att konfigurera partitionering och strömma enheter.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: dedffab0b17515cedc54569d5debf6d29b273644
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458747"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Skala ditt Stream Analytics jobb med Azure Machine Learning Studio (klassiska) funktioner

Den här artikeln beskriver hur du effektivt skalar Azure Stream Analytics jobb som använder Azure Machine Learning-funktioner. Information om hur du skalar Stream Analytics jobb i allmänhet finns i artikeln [skalförändra jobb](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Vad är en Azure Machine Learning-funktion i Stream Analytics?

En Machine Learning funktion i Stream Analytics kan användas som ett vanligt funktions anrop i Stream Analytics frågespråket. I bakgrunden är dessa funktions anrop faktiskt Azure Machine Learning webb tjänst begär Anden.

Du kan förbättra data flödet för Machine Learning webb tjänst begär Anden genom att "Gruppera" flera rader tillsammans i samma API-anrop för webb tjänsten. Den här grupperingen kallas för en mini-batch. Mer information finns i [Azure Machine Learning Studio (klassiska) webb tjänster](../machine-learning/studio/consume-web-services.md). Stöd för Azure Machine Learning Studio (klassisk) i Stream Analytics är en för hands version.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Konfigurera ett Stream Analytics jobb med Machine Learning funktioner

Det finns två parametrar för att konfigurera den Machine Learning funktionen som används av ditt Stream Analytics-jobb:

* Batchstorlek för Machine Learning funktions anrop.
* Antalet strömnings enheter (SUs) som har allokerats för Stream Analyticss jobbet.

Om du vill fastställa lämpliga värden för SUs bestämmer du om du vill optimera svars tiden för Stream Analytics jobb eller genom strömningen för varje SU. SUs kan alltid läggas till i ett jobb för att öka data flödet för en väl partitionerad Stream Analytics fråga. Ytterligare SUs ökar kostnaden för att köra jobbet.

Fastställ latens *toleransen* för ditt Stream Analytics jobb. Om du ökar batchstorleken ökar svars tiden för dina Azure Machine Learning begär Anden och Stream Analytics jobbets svars tid.

Om du ökar batchstorleken kan Stream Analytics jobbet bearbeta **Fler händelser** med **samma antal** Machine Learning webb tjänst begär Anden. Ökningen av Machine Learning webb tjänst fördröjning är vanligt vis under streck till ökningen av batchstorleken. 

Det är viktigt att tänka på den mest kostnads effektiva batchstorleken för en Machine Learning webb tjänst i en specifik situation. Standard grupp storleken för webb tjänst begär Anden är 1000. Du kan ändra standard storleken med hjälp av [Stream Analytics REST API](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "REST-API för Stream Analytics") eller [PowerShell-klienten för Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

När du har bestämt dig för en batchstorlek kan du ange antalet enheter för strömning (SUs) baserat på antalet händelser som funktionen måste bearbeta per sekund. Mer information om enheter för strömning finns i [Stream Analytics skalnings jobb](stream-analytics-scale-jobs.md).

Varje 6 SUs får 20 samtidiga anslutningar till Machine Learning-webbtjänsten. Men 1 SU-jobb och 3 SU-jobb får 20 samtidiga anslutningar.  

Om ditt program genererar 200 000 händelser per sekund och batchstorleken är 1000, är den resulterande webb tjänstens svars tid 200 MS. Den här frekvensen innebär att varje anslutning kan göra fem förfrågningar till den Machine Learning webb tjänsten varje sekund. Med 20 anslutningar kan Stream Analytics-jobbet bearbeta 20 000-händelser i 200 MS-och 100 000-händelser på en sekund.

För att bearbeta 200 000-händelser per sekund behöver Stream Analyticss jobbet 40 samtidiga anslutningar, som kommer ut till 12 SUs. I följande diagram illustreras begär Anden från Stream Analytics-jobbet till Machine Learning-webbtjänstens slut punkt – varje 6 SUs har 20 samtidiga anslutningar till Machine Learning webb tjänst med max.

![Skala Stream Analytics med Machine Learning funktioner två jobb exempel](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Skala Stream Analytics med Machine Learning funktioner två jobb exempel")

I allmänhet, ***b*** för batchstorlek, ***L*** för webb tjänstens svars tid vid batch-storlek B i millisekunder, är data flödet för ett Stream Analytics jobb med ***N*** SUS:

![Skala Stream Analytics med Machine Learning Functions-formel](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Skala Stream Analytics med Machine Learning Functions-formel")

Du kan också konfigurera "maximalt antal samtidiga anrop" i Machine Learning-webbtjänsten. Vi rekommenderar att du anger den här parametern till det maximala värdet (200 för närvarande).

Mer information om den här inställningen finns i [artikeln om skalning för Machine Learning-webbtjänster](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Exempel – Attitydanalys
I följande exempel finns ett Stream Analytics jobb med sentiment Analysis Machine Learning-funktionen som beskrivs i [själv studie kursen Stream Analytics Machine Learning integration](stream-analytics-machine-learning-integration-tutorial.md).

Frågan är en helt partitionerad fråga som följs av **sentiment** -funktionen, som visas i följande exempel:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Vi tar en titt på den konfiguration som krävs för att skapa ett Stream Analytics jobb, vilket gör sentiment analys av tweets med en hastighet av 10 000 tweets per sekund.

Kan det här Stream Analytics jobbet hantera trafiken med hjälp av 1 SU? Jobbet kan fortsätta med indatamängden med hjälp av standard-batchstorleken på 1000. Standard svars tiden för sentiment Analysis Machine Learning-webbtjänsten (med en standard grupp storlek på 1000) skapar högst en fördröjning.

Stream Analytics jobbets **övergripande** svars tid eller slut punkt till slut punkt brukar vara några sekunder. Ta en mer detaljerad titt på det här Stream Analytics jobbet, *särskilt* Machine Learning funktions anrop. Med en batchstorlek på 1000 tar ett flöde med 10 000-händelser att ta cirka 10 förfrågningar till webb tjänsten. Även om det finns tillräckligt många samtidiga anslutningar för att hantera den här Indataporten med en SU.

Om frekvensen för inaktiva händelser ökar med 100x måste Stream Analyticss jobbet bearbeta 1 000 000 tweets per sekund. Det finns två alternativ för att uppnå den ökade skalningen:

1. Öka batchstorleken.
2. Partitionera indataströmmen för att bearbeta händelserna parallellt.

Med det första alternativet ökar jobb **svars tiden** .

Med det andra alternativet måste du etablera mer SUs om du vill ha fler samtidiga Machine Learning webb tjänst begär Anden. Detta är ett större antal SUs-jobb, vilket ökar jobb **kostnaden**.

Nu ska vi titta på skalningen med följande tids fördröjnings mått för varje batchstorlek:

| Svarstid | Batchstorlek |
| --- | --- |
| 200 MS | 1000 – händelse grupper eller under |
| 250 MS | 5 000 – händelse batchar |
| 300 MS | 10 000 – händelse batchar |
| 500 ms | 25 000 – händelse batchar |

1. Med det första alternativet (**inte** etablering av mer SUS). Batchstorleken kan höjas till **25 000**. Om du ökar batchstorleken på det här sättet kan jobbet bearbeta 1 000 000-händelser med 20 samtidiga anslutningar till Machine Learning-webbtjänsten (med en svars tid på 500 ms per anrop). Den ytterligare svars tiden för Stream Analytics jobb på grund av sentiment-funktions begär Anden mot Machine Learning webb tjänst begär anden skulle öka från **200 MS** till **500 ms**. Det **går dock inte** att öka batchstorleken samtidigt som Machine Learning webb tjänster kräver att nytto Last storleken på en begäran är 4 MB eller mindre, och webb tjänst begär timeout efter 100 sekunders åtgärd.
1. Med det andra alternativet lämnas batchstorleken på 1000, med 200-MS-webbtjänstens svars tid, var 20 samtidiga anslutningar till webb tjänsten att kunna bearbeta 1000 * 20 * 5 händelser = 100 000 per sekund. För att bearbeta 1 000 000-händelser per sekund behöver jobbet 60 SUs. Jämfört med det första alternativet skulle Stream Analytics jobb göra fler webb tjänst begär Anden, vilket i sin tur genererar en ökad kostnad.

Nedan visas en tabell för data flödet för Stream Analytics jobb för olika SUs-och batch-storlekar (i antal händelser per sekund).

| batchstorlek (ML-latens) | 500 (200 ms) | 1 000 (200 MS) | 5 000 (250 MS) | 10 000 (300 MS) | 25 000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5 000 |20,000 |30,000 |50 000 |
| **3 SUs** |2,500 |5 000 |20,000 |30,000 |50 000 |
| **6 SUs** |2,500 |5 000 |20,000 |30,000 |50 000 |
| **12 SUs** |5 000 |10 000 |40,000 |60,000 |100 000 |
| **18 SUs** |7 500 |15 000 |60,000 |90,000 |150,000 |
| **24 SUs** |10 000 |20,000 |80,000 |120,000 |200 000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25,000 |50 000 |200 000 |300,000 |500,000 |

Nu bör du redan ha en god förståelse för hur Machine Learning funktioner i Stream Analytics arbete. Du är förmodligen också medveten om att Stream Analytics jobbs "pull"-data från data källor och varje "pull" returnerar en batch med händelser för det Stream Analytics jobb som ska bearbetas. Hur påverkar den här hämtnings modellen Machine Learning webb tjänst begär Anden?

Normalt är batchstorleken som vi angav för Machine Learning funktioner inte exakt delbar med antalet händelser som returneras av varje Stream Analytics jobb "pull". När detta inträffar anropas Machine Learning-webbtjänsten med del-batchar. Genom att använda del batchar slipper du ytterligare jobb fördröjning i sammanslagning av händelser från pull till pull.

## <a name="new-function-related-monitoring-metrics"></a>Nya funktions relaterade övervaknings mått
I övervaknings ytan i ett Stream Analytics jobb har tre ytterligare Function-relaterade mått lagts till. De är **funktions begär Anden**, **funktions händelser** och **misslyckade funktions begär Anden**, som visas i bilden nedan.

![Skala Stream Analytics med Machine Learning funktions mått](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Skala Stream Analytics med Machine Learning funktions mått")

Definieras enligt följande:

**Funktions begär Anden**: antalet funktions begär Anden.

**Funktions händelser**: antalet händelser i funktions begär Anden.

**Misslyckade funktions begär Anden**: antal misslyckade funktions begär Anden.

## <a name="key-takeaways"></a>Key takeaways

Om du vill skala ett Stream Analytics jobb med Machine Learning funktioner bör du tänka på följande faktorer:

1. Frekvensen för ingående händelser.
2. Den tolererade svars tiden för Stream Analytics jobb som körs (och därmed batch-storleken för de Machine Learning webb tjänst begär Anden).
3. Den tillhandahållna Stream Analytics SUs och antalet Machine Learning webb tjänst begär Anden (ytterligare funktions relaterade kostnader).

En helt partitionerad Stream Analytics-fråga användes som ett exempel. Om en mer komplex fråga krävs är Azure Stream Analytics- [forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) en bra resurs för att få ytterligare hjälp från Stream Analyticss teamet.

## <a name="next-steps"></a>Nästa steg
Mer information om Stream Analytics finns i:

* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
