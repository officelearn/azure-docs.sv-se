---
title: Testa frågor i Azure Stream Analytics
description: Den här artikeln beskriver hur du testar frågor med exempeldatafiler i Stream Analytics-jobb.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: fb7d936dfdc543c208ce98c588f5ad83704ff5dc
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="test-azure-stream-analytics-queries-in-the-azure-portal"></a>Testa Azure Stream Analytics-frågor i Azure-portalen

Med Azure Stream Analytics kan du testa frågor i Azure portal utan att behöva starta eller stoppa ett jobb.

## <a name="test-the-input"></a>Testa indata

1. Högerklicka på någon av dina inmatningar för att testa med exempeldata i indata, och välj sedan **ladda upp exempeldata från filen**. För närvarande kan du överföra endast JSON-formaterade data. Om dina data är i ett annat format, till exempel CSV, bör du konvertera den till JSON innan du laddar upp. Du kan använda alla opensource konverteringsverktyget som [CSV-fil för JSON-konverteraren](http://www.convertcsv.com/csv-to-json.htm) konvertera data till JSON.

    ![Stream analytics query editor Testa fråga](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

2. När överföringen är klar klickar du på **testa** att testa den här frågan mot exempeldata som du har angett.

    ![Stream analytics fråga editor test exempeldata](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

Utdata från frågan visas i webbläsaren med nedladdningslänk resultat bör du vill spara testet av utdata för senare användning. Du kan nu enkelt och upprepade gånger ändra din fråga och testa det flera gånger för att se hur utdata ändras.

![Stream Analytics query editor exempel på utdata](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

Du kan se resultaten för båda utdata separat och växla mellan dem med flera utdata som används i en fråga.

När du är nöjd med resultaten visas i webbläsaren kan du spara frågan, starta jobbet och låta bearbeta händelser utan fel.

## <a name="get-help"></a>Få hjälp

För ytterligare hjälp försök vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
