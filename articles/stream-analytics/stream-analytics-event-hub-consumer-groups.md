---
title: Felsöka hubb händelsemottagare i Azure Stream Analytics
description: Fråga bästa praxis för att bedöma Händelsehubbar konsumentgrupper i Stream Analytics-jobb.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 20614986fc6c6afa9a92d163bf973a148e0517c0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="debug-azure-stream-analytics-with-event-hub-receivers"></a>Felsöka Azure Stream Analytics med event hub mottagare

Du kan använda Azure Händelsehubbar i Azure Stream Analytics infognings-eller utdata från ett jobb. Bästa praxis för att använda Händelsehubbar är att använda flera konsumentgrupper så jobbet skalbarhet. En orsak är att antalet läsare i Stream Analytics-jobbet för specifika indata påverkar antalet läsare i en enskild konsument-grupp. Det exakta antalet mottagare baseras på interna implementeringsinformation för logiken i skalbar topologi. Antalet mottagare exponeras inte externt. Antalet läsare kan ändra efter jobbets starttid eller under uppgraderingar av jobbet.

> [!NOTE]
> När antalet läsare ändras under en uppgradering av jobb, skrivs tillfälligt varningar till granskningsloggar. Stream Analytics-jobb återskapa automatiskt från dessa tillfälliga problem.

## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>Antalet läsare per partition överskrider Händelsehubbar gräns på fem

Följande: scenarier där antalet läsare per partition är längre än Händelsehubbar fem

* Flera SELECT-satser: Om du använder flera SELECT-satser som refererar till **samma** händelsehubb indata, gör en ny mottagare som ska skapas för varje SELECT-sats.
* UNION: När du använder en UNION är det möjligt att ha flera indata som refererar till den **samma** händelsegruppen NAV- och konsumenter.
* SJÄLVKOPPLING: När du använder en SELF JOIN-åtgärd, är det möjligt att referera till den **samma** händelsehubb flera gånger.

## <a name="solution"></a>Lösning

Följande säkerhetsmetoder kan du minimera scenarier där antalet läsare per partition är längre än Händelsehubbar fem.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Dela din fråga i flera steg med hjälp av en WITH-satsen

WITH-satsen anger en tillfällig namngivna resultatmängd som kan refereras av en FROM-sats i frågan. Du definierar WITH-satsen i körningen omfånget för en enstaka SELECT-instruktion.

Till exempel i stället för den här frågan:

```
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Använd den här frågan:

```
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Kontrollera att indata ska bindas till olika konsumentgrupper

Skapa separata konsumentgrupper för frågor som tre eller flera inmatningar är anslutna till samma Händelsehubbar konsumentgruppen. Detta kräver att skapa ytterligare Stream Analytics-indata.


## <a name="get-help"></a>Få hjälp
Mer hjälp, försök vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Stream Analytics](stream-analytics-introduction.md)
* [Kom igång med Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Språkreferens för Stream Analytics-fråga](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Strömma Analytics management REST API-referens](https://msdn.microsoft.com/library/azure/dn835031.aspx)
