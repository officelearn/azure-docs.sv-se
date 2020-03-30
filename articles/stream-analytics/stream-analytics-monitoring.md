---
title: Förstå jobbövervakning i Azure Stream Analytics
description: I den här artikeln beskrivs hur du övervakar Azure Stream Analytics-jobb i Azure-portalen.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: 4e9f90035816269d2d41781be34d0d8080628b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431658"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Förstå Stream Analytics jobbövervakning och hur du övervakar frågor

## <a name="introduction-the-monitor-page"></a>Introduktion: Monitorsidan
Azure-portalen visar viktiga prestandamått som kan användas för att övervaka och felsöka din fråga och jobbprestanda. Om du vill se dessa mått bläddrar du till det Stream Analytics-jobb som du är intresserad av att se mått för och visar avsnittet **Övervakning** på sidan Översikt.  

![Länk för övervakning av Stream Analytics-jobb](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Fönstret visas som visas:

![Instrumentpanel för övervakning av Stream Analytics-jobb](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Statistik tillgängliga för Stream Analytics
| Mått                 | Definition                               |
| ---------------------- | ---------------------------------------- |
| Eftersläpade indatahändelser       | Antal indatahändelser som är eftersläpade. Ett värde som inte är noll för det här måttet innebär att jobbet inte kan hålla jämna steg med antalet inkommande händelser. Om det här värdet långsamt ökar eller konsekvent inte är noll bör du skala ut jobbet. Du kan läsa mer genom att besöka [Förstå och justera strömningsenheter](stream-analytics-streaming-unit-consumption.md). |
| Datakonverteringsfel | Antal utdatahändelser som inte kunde konverteras till det förväntade utdataschemat. Felprincipen kan ändras till "Släpp" för att släppa händelser som stöter på det här scenariot. |
| Tidiga inmatningshändelser       | Händelser vars programtidsstämpel är tidigare än deras ankomsttid med mer än 5 minuter. |
| Misslyckade funktionsbegäranden | Antal misslyckade Azure Machine Learning-funktionsanrop (om sådana finns). |
| Funktionshändelser        | Antal händelser som skickas till Azure Machine Learning -funktionen (om sådana finns). |
| Funktionsbegäranden      | Antal anrop till Azure Machine Learning -funktionen (om sådana finns). |
| Fel vid indataserialisering       | Antal indatahändelser som inte kunde avserialiseras.  |
| Indatahändelsebyten      | Mängden data som tas emot av Stream Analytics-jobbet, i byte. Detta kan användas för att verifiera att händelser skickas till indatakällan. |
| Indatahändelser           | Antal poster som anger deserialiserade från indatahändelserna. Det här antalet inkluderar inte inkommande händelser som resulterar i deserialiseringsfel. |
| Mottagna indatakällor       | Antal meddelanden som tagits emot av jobbet. För Event Hub är ett meddelande ett enda EventData. För Blob är ett meddelande en enda blob. Observera att indatakällor räknas före deserialisering. Om det finns avserialiseringsfel kan indatakällor vara större än indatahändelser. Annars kan den vara mindre än eller lika med indatahändelser eftersom varje meddelande kan innehålla flera händelser. |
| Sena indatahändelser      | Händelser som kom senare än det konfigurerade toleransfönstret för sen ankomst. Läs mer om [överväganden för Azure Stream Analytics-händelseorder](stream-analytics-out-of-order-and-late-events.md) . |
| Händelser utanför ordningen    | Antal händelser som tagits emot i oordning som antingen har tagits bort eller fått en justerad tidsstämpel, baserat på händelseorderprincipen. Detta kan påverkas av konfigurationen av inställningen Förordnad toleransfönster. |
| Utdatahändelser          | Mängden data som skickas av Stream Analytics-jobbet till utdatamålet, i antal händelser. |
| Körningsfel         | Totalt antal fel relaterade till frågebearbetning (exklusive fel som hittats vid inmatning av händelser eller utmatningsresultat) |
| SU% utnyttjande       | Användningen av de enheter för strömning som tilldelats ett jobb från fliken Skala för jobbet. Om denna indikator når 80 %, eller högre, är sannolikheten stor att händelsebearbetningen kan försenas eller stoppas för att göra framsteg. |
| Fördröjning av vattenstämpel       | Den maximala vattenstämpelfördröjningen för alla partitioner för alla utdata i jobbet. |

Du kan använda dessa mått för att [övervaka resultatet för ditt Stream Analytics-jobb](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Anpassa övervakning i Azure-portalen
Du kan justera typen av diagram, mått som visas och tidsintervallet i inställningarna för Redigera diagram. Mer information finns i [Så här anpassar du övervakning](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Tidsdiagram för Stream Analytics-frågeövervakare](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Senaste utdata
En annan intressant datapunkt för att övervaka ditt jobb är tiden för den senaste utdata, som visas på sidan Översikt.
Den här tiden är ansökningstiden (dvs. den tid som använder tidsstämpeln från händelsedata) för den senaste utdata för ditt jobb.

## <a name="get-help"></a>Få hjälp
Om du behöver mer hjälp kan du besöka vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)
