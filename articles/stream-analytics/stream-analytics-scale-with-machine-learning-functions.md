---
title: Jobbet skalning med Azure Stream Analytics & AzureML functions | Microsoft Docs
description: Lär dig hur du korrekt skala Stream Analytics-jobb (partitionering, SU antal och mer) när du använder Azure Machine Learning-funktioner.
keywords: ''
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: 47ce7c5e-1de1-41ca-9a26-b5ecce814743
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: dd6effab3ba0b411131414bd757ffe8cc54e49d2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>Skala Stream Analytics-jobbet med Azure Machine Learning-funktioner
Ofta är det enkelt att ställa in ett Stream Analytics-jobb och köra exempeldata genom den. Vad gör vi när vi behöver köra samma jobb med högre datavolym? Det krävs oss att förstå hur du konfigurerar Stream Analytics-jobbet så att det skalas. I det här dokumentet fokuserar på särskilda aspekter skalning Stream Analytics-jobb med Machine Learning-funktioner. Information om hur du skalar Stream Analytics-jobb i allmänhet finns i artikeln [skalning jobb](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Vad är Azure Machine Learning-funktionen i Stream Analytics?
En Machine Learning i Stream Analytics kan användas som ett reguljärt funktionsanrop i Stream Analytics-frågespråket. Bakom scenen emellertid funktionsanropen faktiskt Azure Machine Learning-webbtjänst begäranden. Machine Learning-webbtjänster stöd för ”batchbearbetning” flera rader som kallas Mini batch i samma API webbtjänstanropet, att förbättra totala genomflödet. Se följande artiklar för mer information; [Azure Machine Learning-funktioner i Stream Analytics](https://blogs.technet.microsoft.com/machinelearning/2015/12/10/azure-ml-now-available-as-a-function-in-azure-stream-analytics/) och [Azure Machine Learning-webbtjänster](../machine-learning/studio/consume-web-services.md).

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Konfigurera ett Stream Analytics-jobb med Machine Learning-funktioner
När du konfigurerar en Machine Learning-funktionen för Stream Analytics-jobbet, finns det två parametrar att tänka på, batchstorlek för Machine Learning-funktionsanrop och strömningsenheter (SUs) som etablerats för Stream Analytics-jobbet. För att avgöra lämpliga värden för dessa måste först beslut göras mellan svarstid och genomströmning, det vill säga svarstiden för Stream Analytics-jobbet och dataflöde på varje SU. SUs kan alltid läggas till ett jobb för att öka genomflödet av en bra partitionerade Stream Analytics-fråga, även om ytterligare SUs ökar kostnaden för att köra jobbet.

Därför är det viktigt att fastställa den *tolerans* av fördröjning i Stream Analytics-jobbet körs. Naturligtvis ökar ytterligare svarstiden från att köra Azure Machine Learning tjänstbegäranden med batchstorlek som föreningar svarstiden för Stream Analytics-jobbet. Å andra sidan ökar batchstorlek kan Stream Analytics-jobbet att bearbeta * fler händelser med den *samma nummer* av Machine Learning webbförfrågningar service. Ökningen av svarstiden för Machine Learning web service är ofta sublinear till ökningen av batchstorlek så det är viktigt att tänka på den mest kostnadseffektiva batchstorleken för Machine Learning-webbtjänsten i en viss situation. Standard batchstorlek för webbtjänsten begär är 1000 och kan ändras genom att använda den [Stream Analytics REST API](https://msdn.microsoft.com/library/mt653706.aspx "Stream Analytics REST API") eller [PowerShell-klienten för Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md "PowerShell-klienten för Stream Analytics").

När en batchstorlek har fastställts kan antalet strömning enheter (SUs) kan bestämmas utifrån antalet händelser som funktionen måste processer per sekund. Mer information om enheter för strömning finns [Stream Analytics skala jobb](stream-analytics-scale-jobs.md).

I allmänhet finns 20 samtidiga anslutningar till webbtjänsten för Machine Learning för varje 6 SUs, förutom att 1 SU jobb och 3 SU jobb 20 samtidiga anslutningar också.  Om indata är 200 000 händelser per sekund och batchstorleken lämnas till 1000 standardvärdet är den resulterande web service svarstiden med 1000 händelser Mini batch 200 ms. Det innebär att alla anslutningar kan göra fem begäranden till Machine Learning-webbtjänst på en sekund. 20 anslutningar kan Stream Analytics-jobbet bearbeta 20 000 händelser på 200 ms och därför 100 000 händelser på en sekund. Om du vill bearbeta 200 000 händelser per sekund, måste därför Stream Analytics-jobbet 40 samtidiga anslutningar som kommer till 12 SUs. Följande diagram illustrerar förfrågningar från Stream Analytics-jobbet till Machine Learning-webbtjänstslutpunkt – var 6 SUs har 20 samtidiga anslutningar till webbtjänsten för Machine Learning på max.

![Skala Stream Analytics med Machine Learning funktioner två jobb exempel](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "skala Stream Analytics med Machine Learning funktioner två jobb exemplet")

I allmänhet ***B*** för batchstorlek, ***L*** för web service fördröjning vid batchstorlek B i millisekunder, genomflödet av ett Stream Analytics-jobbet med ***N*** SUs är:

![Skala Stream Analytics med Machine Learning funktioner formel](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "skala Stream Analytics med Machine Learning funktioner formel")

En ytterligare eftertanke kanske de 'högsta antal samtidiga anrop' på tjänstsidan för Machine Learning-webbtjänst, vi rekommenderar att du anger du det högsta värdet (för närvarande 200).

Mer information om den här inställningen finns i [skalning artikel för Machine Learning-webbtjänster](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Exempel – Sentiment analys
I följande exempel innehåller ett Stream Analytics-jobb med sentiment analysen Machine Learning-funktionen, enligt beskrivningen i den [Stream Analytics, Machine Learning integration kursen](stream-analytics-machine-learning-integration-tutorial.md).

Frågan är en enkel fullständigt partitionerad frågan följt av den **sentiment** fungera som visas nedan:

    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery

Studera följande scenario; Stream Analytics-jobbet måste skapas för att analysera sentiment tweets (händelser) med en genomströmning på 10 000 tweets per sekund. Med 1 SU kunde Stream Analytics-jobbet att kunna hantera trafiken? Standardstorleken batch 1000 ska jobbet kunna Håll dig uppdaterad med indata. Ytterligare ska lagts till Machine Learning-funktionen Skapa mer än en sekund av latens, vilket är allmänna standard svarstiden för sentiment analysen Machine Learning-webbtjänst (med en standard batchstorlek 1000). Stream Analytics-jobbet **övergripande** eller svarstid för slutpunkt till slutpunkt kan vara några sekunder. Ta en närmare titt till Stream Analytics-jobbet *särskilt* Machine Learning-funktionsanrop. Med batchstorleken som 1000, en genomströmning på 10 000 händelser ta ungefär 10 begäranden till webbtjänst. Det finns tillräckligt många samtidiga anslutningar för den inkommande trafiken även med 1 SU.

Men vad händer om inkommande händelsetakten ökas med 100 x och Stream Analytics-jobbet måste bearbeta 1 000 000 tweets per sekund? Det finns två alternativ:

1. Öka batchstorleken på eller
2. Partitionen Indataströmmen att bearbeta händelser parallellt

Med det första alternativet jobbet **svarstid** ökar.

Med det andra alternativet måste flera SUs etableras och därför generera flera samtidiga webbtjänstbegäranden för Machine Learning. Detta innebär att jobbet **kostnaden** ökar.

Anta att svarstiden för sentiment analysen Machine Learning-webbtjänsten är 200 ms för 1000-händelsen batchar eller nedan, 250 ms för 5 000 händelse batchar, 300 ms för 10 000-händelsen batchar eller 500 ms för 25 000 händelse batchar.

1. Med alternativet första (**inte** flera SUs-etablering), batchstorlek kan ökas till **25 000**. Detta skulle i sin tur att jobbet ska bearbeta 1 000 000 händelser med 20 samtidiga anslutningar till webbtjänsten för Machine Learning (med en fördröjning på 500 ms per anrop). Så att ytterligare svarstiden för Stream Analytics-jobbet på grund av sentiment funktionen förfrågningar mot Machine Learning webbtjänstbegäranden skulle ökas från **200 ms** till **500 ms**. Dock batchstorlek **kan** ökas oändligt Machine Learning-webbtjänster kräver nyttolastens storlek för en begäran är 4 MB eller mindre webbtjänsten begär timeout efter 100 sekunder av åtgärden.
2. Med det andra alternativet batchstorleken lämnas på 1000, med 200 ms web service svarstid, var 20 samtidiga anslutningar till webbtjänsten skulle kunna bearbeta 1000 * 20 * 5 händelser = 100 000 per sekund. Om du vill bearbeta 1 000 000 händelser per sekund, så måste jobbet 60 SUs. Jämfört med det första alternativet, skulle Stream Analytics-jobbet göra mer batch webbtjänstbegäranden, i sin tur genererar en ökad kostnad.

Nedan finns en tabell för genomflödet i Stream Analytics-jobbet för olika SUs och batch-storlek (i antal händelser per sekund).

| batchstorlek (ML svarstid) | 500 (200 ms) | 1 000 (200 ms) | 5 000 (250 ms) | 10 000-tal (300 ms) | 25 000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3 SUs** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6 SUs** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12 SUs** |5 000 |10 000 |40,000 |60,000 |100,000 |
| **18 SUs** |7 500 |15,000 |60,000 |90,000 |150,000 |
| **24 SUs** |10 000 |20,000 |80,000 |120,000 |200 000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25,000 |50,000 |200 000 |300,000 |500,000 |

Nu, bör du redan har en god förståelse av hur Machine Learning-funktioner i Stream Analytics fungerar. Du förmodligen förstå också Stream Analytics-jobb ”pull” data från datakällor och varje ”pull” returnerar en batch med händelser för Stream Analytics-jobbet att bearbeta. Hur web tjänstbegäranden i den här pull modellen effekten Machine Learning?

Batchstorleken vi använder för Machine Learning-funktioner vara inte normalt sett exakt delbart med antalet händelser som returneras av varje Stream Analytics-jobbet ”pull”. Detta inträffar när Machine Learning-webbtjänst anropas med ”delar” batchar. Detta görs för att inte till ytterligare jobb latens pålägget i buffertsammanslagning händelser från pull till pull.

## <a name="new-function-related-monitoring-metrics"></a>Ny funktion-relaterade övervakning mått
Tre ytterligare funktion-relaterade mått har lagts till i området Övervakare i ett Stream Analytics-jobb. De är funktionen BEGÄRANDEN, FUNKTIONSHÄNDELSER och misslyckade BEGÄRANDEN att funktionen som visas i bilden nedan.

![Skala Stream Analytics med Machine Learning funktioner](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "skala Stream Analytics med Machine Learning-funktioner")

Är definierad på följande sätt:

**FUNKTIONEN BEGÄRANDEN**: antalet begäranden som funktionen.

**FUNKTIONSHÄNDELSER**: antalet händelser i funktionen-begäranden.

**MISSLYCKADE BEGÄRANDEN om funktionen**: antalet funktionsfel begäranden.

## <a name="key-takeaways"></a>Viktiga Takeaways
Följande objekt måste beaktas för att sammanfatta de viktigaste aspekterna för att skala ett Stream Analytics-jobb med Machine Learning-funktioner:

1. Inkommande händelsefrekvens
2. Tillåten svarstiden för Stream Analytics-jobb som körs (och därmed batchstorlek för Machine Learning webbtjänstbegäranden)
3. Etablerade Stream Analytics SUs och antalet Machine Learning webbtjänstbegäranden (ytterligare funktion-relaterade kostnader)

En fullständigt partitionerade Stream Analytics-fråga används som exempel. Om en mer komplex fråga krävs det [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) är en utmärkt resurs Stream Analytics-teamet för att få ytterligare hjälp.

## <a name="next-steps"></a>Nästa steg
Mer information om Stream Analytics finns:

* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
