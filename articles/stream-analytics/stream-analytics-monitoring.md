---
title: Förstå jobb övervakning i Azure Stream Analytics
description: I den här artikeln beskrivs hur du övervakar Azure Stream Analytics jobb i Azure Portal.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: f0932104c55e705c76a8d2b086edf828caf60520
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86044098"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Förstå Stream Analytics jobb övervakning och övervaka frågor

## <a name="introduction-the-monitor-page"></a>Introduktion: övervaknings Sidan
Azure Portal Ytors viktiga prestanda mått som kan användas för att övervaka och felsöka frågor och jobb prestanda. Om du vill se dessa mått bläddrar du till Stream Analytics jobb som du är intresse rad av att se mått för och visa avsnittet **övervakning** på sidan Översikt.  

![Länk till Stream Analytics jobb övervakning](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Fönstret visas som det visas:

![Instrument panel för Stream Analytics jobb övervakning](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Mått som är tillgängliga för Stream Analytics
| Mått                 | Definition                               |
| ---------------------- | ---------------------------------------- |
| Eftersläpande inloggade ingångs händelser       | Antal ingångs händelser som är eftersläpande. Ett värde som inte är noll för det här måttet innebär att jobbet inte kan fortsätta med antalet inkommande händelser. Om det här värdet ökar långsamt eller ständigt inte är noll, bör du skala ut jobbet. Du kan lära dig mer genom att besöka [förstå och justera strömnings enheter](stream-analytics-streaming-unit-consumption.md). |
| Data konverterings fel | Antal utgående händelser som inte kunde konverteras till det förväntade schemat för utdata. Fel policyn kan ändras till Drop för att släppa händelser som stöter på det här scenariot. |
| Tidiga ingångs händelser       | Händelser vars programtidstämpel är tidigare än deras ankomst tid med mer än 5 minuter. |
| Misslyckade funktions begär Anden | Antalet misslyckade Azure Machine Learning funktions anrop (om det finns). |
| Funktions händelser        | Antalet händelser som skickats till Azure Machine Learning-funktionen (om det finns). |
| Funktions begär Anden      | Antal anrop till Azure Machine Learning funktion (om det finns). |
| Fel vid deserialisering av indataport       | Antal ingångs händelser som inte kunde deserialiseras.  |
| Inkommande händelse-byte      | Mängden data som tas emot av Stream Analytics jobb, i byte. Detta kan användas för att kontrol lera att händelser skickas till Indatakällan. |
| Inmatade händelser           | Antal poster som har deserialiserats från inmatade händelser. Antalet inkluderar inte inkommande händelser som resulterar i fel vid deserialisering. Samma händelser kan matas in genom att Stream Analytics flera gånger i scenarier som interna återställningar och själv kopplingar. Vi rekommenderar därför att du inte förväntar sig att indata-och utmatnings händelser ska matchas om jobbet har en enkel "passa"-fråga. |
| Mottagna inmatade källor       | Antal meddelanden som tagits emot av jobbet. För Event Hub är ett meddelande ett enda EventData. För BLOB är ett meddelande en enda blob. Observera att inmatade källor räknas innan deserialisering. Om det finns deserialiserade fel kan indatakällana vara större än inmatade händelser. Annars kan det vara mindre än eller lika med indatamängden eftersom varje meddelande kan innehålla flera händelser. |
| Sena ingångs händelser      | Händelser som anlänt senare än den konfigurerade tolerans perioden för sent införsel. Lär dig mer om att [tänka på när du Azure Stream Analytics händelse ordning](stream-analytics-out-of-order-and-late-events.md) . |
| Händelser som inte är i ordning    | Antalet händelser som tagits emot utanför ordningen som antingen släpptes eller fått en anpassad tidsstämpel, baserat på händelse ordnings principen. Detta kan påverkas av konfigurationen av inställningen för avställnings tolerans fönstret. |
| Utgående händelser          | Mängden data som skickas av Stream Analytics jobb till utmatnings målet, i antal händelser. |
| Körnings fel         | Totalt antal fel som rör bearbetning av frågor (exklusive fel som påträffats vid inmatning av händelser eller resultat av resultat) |
| SU%-användning       | Användningen av de strömnings enheter som är kopplade till ett jobb från fliken Scale i jobbet. Om den här indikatorn når 80% eller högre, finns det en hög sannolikhet att händelse bearbetningen kan fördröjas eller stoppas. |
| Fördröjning för vattenstämpel       | Maximal vattenstämpel-fördröjning över alla partitioner i alla utdata i jobbet. |

Du kan använda dessa mått för att [övervaka Stream Analytics jobbets prestanda](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Anpassa övervakning i Azure Portal
Du kan ändra typ av diagram, mått som visas och tidsintervall i redigera diagram inställningar. Mer information finns i [så här anpassar du övervakning](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Tids diagram för Stream Analytics frågans övervaknings tid](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Senaste utdata
En annan intressant data punkt för att övervaka jobbet är tiden för den senaste utmatningen, som visas på sidan Översikt.
Den här gången är program tiden (dvs. tiden med tidsstämpeln från händelse data) för de senaste utdata från jobbet.

## <a name="get-help"></a>Få hjälp
Om du behöver ytterligare hjälp kan du prova vår [Microsoft Q&en fråge sida för Azure Stream Analytics](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)
