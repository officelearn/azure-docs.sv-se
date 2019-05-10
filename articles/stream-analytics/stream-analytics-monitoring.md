---
title: Förstå övervakning av jobb i Azure Stream Analytics
description: Den här artikeln beskriver hur du övervakar Azure Stream Analytics-jobb i Azure-portalen.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 43dd8be998e0f8f3b5a2b783c6a01d5b5ef3da12
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506913"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Förstå övervakning av Stream Analytics-jobb och övervaka frågor

## <a name="introduction-the-monitor-page"></a>Introduktion: Sidan Övervakare
Azure portal både lyfta fram viktiga prestandamått som kan användas för att övervaka och felsöka din fråga och jobbet prestanda. Om du vill se de här måtten, bläddra till Stream Analytics-jobbet du är intresserad av att se mått för och visa den **övervakning** avsnitt på sidan Översikt.  

![Stream Analytics-jobbet övervakning länk](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

I fönstret visas som visas:

![Stream Analytics-jobb som instrumentpanelen](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Mått som är tillgängliga för Stream Analytics
| Mått                 | Definition                               |
| ---------------------- | ---------------------------------------- |
| Eftersläpande inmatningshändelser       | Antal inkommande händelser som är eftersläpande. Ett annat värde än noll för det här måttet innebär att ditt jobb inte kan hålla jämna steg med antalet inkommande händelser. Om värdet är långsamt ökar eller konsekvent noll, bör du skala ut ditt jobb. Du kan lära dig mer genom att besöka [förstå och justera Direktuppspelningsenheter](stream-analytics-streaming-unit-consumption.md). |
| Datakonverteringsfel | Antalet utdata-händelser som inte kan konverteras till schemat för utdata som förväntas. Felprincip kan ändras till ”släppa” för att ta bort händelser som stöta på det här scenariot. |
| Tidiga indatahändelser       | Händelser vars Programtidsstämpel är tidigare än ankomsttid med mer än 5 minuter. |
| Misslyckade funktionsförfrågningar | Antal misslyckade Azure Machine Learning-funktionsanrop (om tillgängligt). |
| Funktionshändelser        | Antalet händelser som skickas till Azure Machine Learning-funktionen (om tillgängligt). |
| Funktionsförfrågningar      | Antal anrop till funktionen Azure Machine Learning (om tillgängligt). |
| Fel vid deserialisering av indata       | Antal inkommande händelser som inte kunde deserialiseras.  |
| Indatahändelsebyte      | Mängden data som tas emot av Stream Analytics-jobb i byte. Detta kan användas för att verifiera att händelser skickas till Indatakällan. |
| Indatahändelser           | Antalet poster som har deserialiserats från inkommande händelser. Det här antalet inkluderar inte inkommande händelser som leder till fel vid deserialisering av. |
| Indatakällorna har tagits emot       | Antal meddelanden som tas emot av jobbet. Ett meddelande är en enda EventData för Event Hub. För Blob är ett meddelande en enda blob. Observera att indata källor räknas innan deserialisering. Om det finns fel vid deserialisering av, kan indatakällor vara större än inmatningshändelser. I annat fall kan det vara mindre än eller lika med att ange händelser eftersom varje meddelande kan innehålla flera händelser. |
| Sena indatahändelser      | Händelser som anlänt senare än det konfigurera sent ankomst tolerans fönstret. Läs mer om [Azure Stream Analytics händelse ordning överväganden](stream-analytics-out-of-order-and-late-events.md) . |
| Out ordning händelser    | Antal händelser som tagits emot fel ordning som antingen släpptes eller får en justerade tidsstämpel, baserat på händelse ordning principen. Detta kan påverkas av konfigurationen av inställningen ordning för Out of Tolerance fönster. |
| Utdatahändelser          | Mängden data som skickas av Stream Analytics-jobb till utdatamål, i antal händelser. |
| Körningsfel         | Totalt antal fel som rör frågebearbetning (exklusive fel hittades när mata in händelser eller genererar resultat) |
| SU % utnyttjande       | Användningen av enhet(er) för strömning som har tilldelats till ett jobb från fliken Skala för jobbet. Indikatorn når 80% eller ovan, är hög sannolikhet att bearbetning av händelser kan fördröjas eller stoppats. |
| Vattenstämpelfördröjning       | Den maximala vattenstämpel fördröjningen för alla partitioner för alla utdata i jobbet. |

Du kan använda de här måtten till [övervaka prestanda för ditt Stream Analytics-jobb](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Anpassa övervakning i Azure portal
Du kan justera vilken typ av diagram, mått som visas, och tidsintervall i inställningarna för redigera diagram. Mer information finns i [hur du anpassar övervakning](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Stream Analytics-frågan Övervakningsdiagrammet tid](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Senaste utdata
En annan intressant datapunkt för att övervaka ditt jobb är tidpunkten för de senaste utdata som visas på sidan Översikt.
Det här är den program-tid (dvs. den tid som använder tidsstämpel från händelsedata) på senaste utdata från jobbet.

## <a name="get-help"></a>Få hjälp
Om du behöver mer hjälp kan du besöka vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

