---
title: Testa en fråga med exempeldata i Azure Stream Analytics
description: Den här artikeln beskriver hur du testar en fråga med några exempel på indata i Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 305b767ee86de6c8b04fed17514a9092afc2d735
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="test-a-query-and-sample-input-in-azure-stream-analytics"></a>Testa en fråga och indata i Azure Stream Analytics-exempel 

Du kan prova inkommande händelser som kommer från en fil och testa frågor i portalen utan att behöva starta eller stoppa ett jobb med hjälp av Azure Stream Analytics.

## <a name="testing-your-query"></a>Testa din fråga

I informationsfönstret för Stream Analytics-jobbet öppna den **frågeredigeraren** bladet genom att klicka på frågans namn under **frågan**. (I vårt Exempelscenario, eftersom ingen fråga har skapats ännu, klickar du på den **< >** platshållaren.)

![Stream Analytics frågeredigeraren](media/stream-analytics-sample-data-input/stream-analytics-query-editor.png)

Precis som i den tidigare versionen visas bladet omfattande redigerare för att skapa frågan. Nu har bladet uppdaterats med en ny vänster som visar in- och utgångar som används av frågan och beskrivning för det här jobbet.

![Stream Analytics query editor inmatningar och matar ut listor](media/stream-analytics-sample-data-input/stream-analytics-query-editor-highlight.png)

Dessutom visas är ytterligare indata och utdata, som inte har definierats. De kommer från den nya fråga mall som du börjar med. De ändrar eller försvinna även helt, när du redigerar frågan. Du kan ignorera dem just nu.

Högerklicka på någon av dina inmatningar för att testa med exempeldata i indata, och välj sedan **ladda upp exempeldata från filen**.

![Stream Analytics query editor överför exempeldata från fil (kommando)](media/stream-analytics-sample-data-input/stream-analytics-query-editor-upload.png)

När överföringen är klar klickar du på **testa** att testa den här frågan mot exempeldata som du angav.

![Knappen Stream Analytics query editor Test](media/stream-analytics-sample-data-input/stream-analytics-query-editor-test.png)

Om du vill spara testet utdata för senare användning visas utdata från frågan i webbläsaren med en länk till nedladdningsresultaten. Du kan nu enkelt och upprepade gånger ändra din fråga och testa det flera gånger för att se hur utdata ändras.

![Stream Analytics query editor exempel på utdata](media/stream-analytics-sample-data-input/stream-analytics-query-editor-samples-output.png)

En andra utdata har lagts till i föregående bild, kallas **HighAvgTempOutput**.

Du kan se resultaten för varje utdata separat och växla mellan dem när du använder flera utdata i en fråga.

När du är nöjd med resultaten du spara frågan, starta jobbet, tillbaka sitta och titta på Magiskt tal för Stream Analytics.

## <a name="get-help"></a>Få hjälp

För ytterligare hjälp försök den [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
