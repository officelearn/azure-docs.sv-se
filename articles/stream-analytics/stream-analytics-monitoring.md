---
title: Förstå övervakning av jobb i Azure Stream Analytics
description: Den här artikeln beskriver hur du övervakar Azure Stream Analytics-jobb i Azure-portalen.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: 9432b43498cd48352427bf1ec2709a2d0f13a797
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172767"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Förstå övervakning av Stream Analytics-jobb och övervaka frågor

## <a name="introduction-the-monitor-page"></a>Insättning Sidan övervakare
Azure Portal Ytors viktiga prestanda mått som kan användas för att övervaka och felsöka frågor och jobb prestanda. Om du vill se de här måtten, bläddra till Stream Analytics-jobbet du är intresserad av att se mått för och visa den **övervakning** avsnitt på sidan Översikt.  

![Stream Analytics-jobbet övervakning länk](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

I fönstret visas som visas:

![Stream Analytics-jobb som instrumentpanelen](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Mått som är tillgängliga för Stream Analytics
| Mått                 | Definition                               |
| ---------------------- | ---------------------------------------- |
| Eftersläpande inmatningshändelser       | Antal inkommande händelser som är eftersläpande. Ett värde som inte är noll för det här måttet innebär att jobbet inte kan fortsätta med antalet inkommande händelser. Om det här värdet ökar långsamt eller ständigt inte är noll, bör du skala ut jobbet. Du kan lära dig mer genom att besöka [förstå och justera strömnings enheter](stream-analytics-streaming-unit-consumption.md). |
| Datakonverteringsfel | Antalet utdata-händelser som inte kan konverteras till schemat för utdata som förväntas. Fel policyn kan ändras till Drop för att släppa händelser som stöter på det här scenariot. |
| Tidiga indatahändelser       | Händelser vars programtidstämpel är tidigare än deras ankomst tid med mer än 5 minuter. |
| Misslyckade funktionsförfrågningar | Antal misslyckade Azure Machine Learning-funktionsanrop (om tillgängligt). |
| Funktionshändelser        | Antalet händelser som skickas till Azure Machine Learning-funktionen (om tillgängligt). |
| Funktionsförfrågningar      | Antal anrop till funktionen Azure Machine Learning (om tillgängligt). |
| Fel vid deserialisering av indata       | Antal ingångs händelser som inte kunde deserialiseras.  |
| Indatahändelsebyte      | Mängden data som tas emot av Stream Analytics-jobb i byte. Detta kan användas för att verifiera att händelser skickas till Indatakällan. |
| Indatahändelser           | Antal poster som har deserialiserats från inmatade händelser. Antalet inkluderar inte inkommande händelser som resulterar i fel vid deserialisering. |
| Indatakällorna har tagits emot       | Antal meddelanden som tagits emot av jobbet. För Event Hub är ett meddelande ett enda EventData. För BLOB är ett meddelande en enda blob. Observera att inmatade källor räknas innan deserialisering. Om det finns deserialiserade fel kan indatakällana vara större än inmatade händelser. Annars kan det vara mindre än eller lika med indatamängden eftersom varje meddelande kan innehålla flera händelser. |
| Sena indatahändelser      | Händelser som anlänt senare än den konfigurerade tolerans perioden för sent införsel. Lär dig mer om att [tänka på när du Azure Stream Analytics händelse ordning](stream-analytics-out-of-order-and-late-events.md) . |
| Out ordning händelser    | Antal händelser som tagits emot fel ordning som antingen släpptes eller får en justerade tidsstämpel, baserat på händelse ordning principen. Detta kan påverkas av konfigurationen av inställningen ordning för Out of Tolerance fönster. |
| Utdatahändelser          | Mängden data som skickas av Stream Analytics-jobb till utdatamål, i antal händelser. |
| Körningsfel         | Totalt antal fel som rör bearbetning av frågor (exklusive fel som påträffats vid inmatning av händelser eller resultat av resultat) |
| SU % utnyttjande       | Användningen av enhet(er) för strömning som har tilldelats till ett jobb från fliken Skala för jobbet. Indikatorn når 80% eller ovan, är hög sannolikhet att bearbetning av händelser kan fördröjas eller stoppats. |
| Vattenstämpelfördröjning       | Den maximala vattenstämpel fördröjningen för alla partitioner för alla utdata i jobbet. |

Du kan använda dessa mått för att [övervaka Stream Analytics jobbets prestanda](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). 

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
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
