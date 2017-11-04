---
title: "Förstå Stream Analytics-jobbet övervakning | Microsoft Docs"
description: "Förstå övervakning Stream Analytics-jobbet"
keywords: "Övervakare för frågan"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 5f5cc00f-4a7b-491e-89e1-dbafea46d399
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 12545dd3875e81f8f2248acceb66d2d840cf6702
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Förstå övervakning av Stream Analytics-jobb och hur du övervakar frågor

## <a name="introduction-the-monitor-page"></a>Introduktion: Sidan Övervakare
Azure portal både yta nyckeltal som kan användas för att övervaka och felsöka din fråga och jobbet prestanda. Om du vill visa de här måtten, bläddra till Stream Analytics-jobbet du är intresserad av att se mätvärden för och visa den **övervakning** avsnitt på sidan Översikt.  

![Övervaka länk](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Fönstret visas som visas:

![Övervaka jobb instrumentpanelen](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Mått som är tillgängliga för Stream Analytics
| Mått                 | Definition                               |
| ---------------------- | ---------------------------------------- |
| SU % användning       | Användning av enheter för strömning tilldelas till ett jobb från fliken Skala för jobbet. Denna indikator når 80% eller ovan är hög sannolikhet att händelsebearbetning kan fördröjas eller stoppats framsteg. |
| Inkommande händelser           | Mängden data som tagits emot av Stream Analytics-jobbet i antal händelser. Detta kan användas för att verifiera att händelser skickas till Indatakällan. |
| Utdatahändelserna          | Mängden data som skickas av Stream Analytics-jobbet utdata målet, i antal händelser. |
| Out-ordning händelser    | Antalet händelser som mottagits i felaktig ordningsföljd som antingen tagits bort eller ges ett justerade timestamp, baserat på händelsen ordning principen. Detta kan påverkas av konfigurationen av inställningen för Out of Tolerance fönstret. |
| Data Konverteringsfel | Antal data Konverteringsfel drabbar Stream Analytics-jobbet. |
| Körningsfel         | Det totala antalet fel som inträffar under körningen av ett Stream Analytics-jobb. |
| Inkommande händelser      | Antal händelser anländer sent från källan som har antingen tagits bort eller tidsstämpel har baserat på händelsen ordning princip konfigurationen av inställningen för sen ankomst tolerans fönstret justerats. |
| Funktionen begäranden      | Antal anrop till funktionen Azure Machine Learning (om sådan finns). |
| Funktionsfel begäranden | Antal misslyckade Azure Machine Learning-funktionsanrop (om sådan finns). |
| Funktionshändelser        | Antalet händelser som skickas till Azure Machine Learning-funktionen (om sådan finns). |
| Händelse-byte      | Mängden data som tagits emot av Stream Analytics-jobbet, i byte. Detta kan användas för att verifiera att händelser skickas till Indatakällan. |


## <a name="customizing-monitoring-in-the-azure-portal"></a>Anpassa övervakning i Azure-portalen
Du kan justera vilken typ av diagram, mått som visas, och tidsintervallet i inställningarna för redigera diagram. Mer information finns i [hur du anpassar övervakning](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Frågan övervaka tid diagram](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Senaste utdata
En annan intressant datapunkt för att övervaka jobbet är tid för senaste utdata visas på översiktssidan.
Det här är den tid programmet (dvs. den tid som använder tidsstämpeln från informationen om händelsen) på de senaste utdata av ditt jobb.

## <a name="get-help"></a>Få hjälp
Om du behöver mer hjälp kan du besöka vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

