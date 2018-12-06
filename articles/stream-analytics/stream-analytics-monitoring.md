---
title: Förstå övervakning av jobb i Azure Stream Analytics
description: Den här artikeln beskrivs hur du övervakar jobb i Azure Stream Analytics
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 81c5eabcfcca6818293ff9dbbfb1515d5729dd79
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963292"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Förstå övervakning av Stream Analytics-jobb och övervaka frågor

## <a name="introduction-the-monitor-page"></a>Introduktion: Övervaka sidan
Azure portal både lyfta fram viktiga prestandamått som kan användas för att övervaka och felsöka din fråga och jobbet prestanda. Om du vill se de här måtten, bläddra till Stream Analytics-jobbet du är intresserad av att se mått för och visa den **övervakning** avsnitt på sidan Översikt.  

![Övervakning av länk](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

I fönstret visas som visas:

![Övervakningsjobbet instrumentpanel](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Mått som är tillgängliga för Stream Analytics
| Mått                 | Definition                               |
| ---------------------- | ---------------------------------------- |
| Eftersläpande inmatningshändelser       | Antal inkommande händelser som är eftersläpande. |
| Datakonverteringsfel | Antalet utdata-händelser som inte kan konverteras till schemat för utdata som förväntas. |
| Tidiga indatahändelser       | Antal händelser som tagits emot tidigt. |
| Misslyckade funktionsförfrågningar | Antal misslyckade Azure Machine Learning-funktionsanrop (om tillgängligt). |
| Funktionshändelser        | Antalet händelser som skickas till Azure Machine Learning-funktionen (om tillgängligt). |
| Funktionsförfrågningar      | Antal anrop till funktionen Azure Machine Learning (om tillgängligt). |
| Fel vid deserialisering av indata       | Antal händelser som inte kunde deserialiseras.  |
| Indatahändelsebyte      | Mängden data som tas emot av Stream Analytics-jobb i byte. Detta kan användas för att verifiera att händelser skickas till Indatakällan. |
| Indatahändelser           | Mängden data som tas emot av Stream Analytics-jobb i antalet händelser. Detta kan användas för att verifiera att händelser skickas till Indatakällan. |
| Indatakällorna har tagits emot       | Antal händelser som kommer från en indatakälla. |
| Sena indatahändelser      | Antalet händelser som kommer sent från källan som har antingen tagits bort eller deras tidsstämpel har baserat på händelse ordning princip konfigurationen av inställningen sent ankomst tolerans fönster justerats. |
| Out ordning händelser    | Antal händelser som tagits emot fel ordning som antingen släpptes eller får en justerade tidsstämpel, baserat på händelse ordning principen. Detta kan påverkas av konfigurationen av inställningen ordning för Out of Tolerance fönster. |
| Utdatahändelser          | Mängden data som skickas av Stream Analytics-jobb till utdatamål, i antal händelser. |
| Körningsfel         | Totalt antal fel som rör frågebearbetning (exklusive fel hittades när mata in händelser eller outputing resultat) |
| SU % utnyttjande       | Användningen av enhet(er) för strömning som har tilldelats till ett jobb från fliken Skala för jobbet. Indikatorn når 80% eller ovan, är hög sannolikhet att bearbetning av händelser kan fördröjas eller stoppats. |
| Vattenstämpelfördröjning       | Den maximala vattenstämpel fördröjningen för alla partitioner för alla utdata i jobbet. |

## <a name="customizing-monitoring-in-the-azure-portal"></a>Anpassa övervakning i Azure portal
Du kan justera vilken typ av diagram, mått som visas, och tidsintervall i inställningarna för redigera diagram. Mer information finns i [hur du anpassar övervakning](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Fråga övervaka tid graph](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


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

