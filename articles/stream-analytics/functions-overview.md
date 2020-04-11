---
title: Användardefinierade funktioner i Azure Stream Analytics
description: Den här artikeln är en översikt över användardefinierade funktioner i Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b29d66e8bb213fbbb162c3249f022e0783f9f62f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115591"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Användardefinierade funktioner i Azure Stream Analytics

Det SQL-liknande frågespråket i Azure Stream Analytics gör det enkelt att implementera analyslogik i realtid för strömmande data. Stream Analytics ger ytterligare flexibilitet genom anpassade funktioner som anropas i din fråga. Följande kodexempel är `sampleFunction` en UDF som anropas som accepterar en parameter, varje `sampleResult`indatapost som jobbet tar emot och resultatet skrivs till utdata som .

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>Typer av funktioner

Azure Stream Analytics stöder följande fyra funktionstyper: 

* Användardefinierade JavaScript-funktioner 
* JavaScript användardefinierade aggregat 
* C# användardefinierade funktioner (med Visual Studio) 
* Azure Machine Learning 

Du kan använda dessa funktioner för scenarier som bedömning i realtid med hjälp av maskininlärningsmodeller, strängmanipuleringar, komplexa matematiska beräkningar, kodning och avkodningsdata. 

## <a name="limitations"></a>Begränsningar

Användardefinierade funktioner är tillståndslösa och returvärdet kan bara vara ett skalärvärde. Du kan inte ropa ut till externa REST-slutpunkter från dessa användardefinierade funktioner, eftersom det sannolikt påverkar jobbets prestanda. 

Azure Stream Analytics registrerar inte alla funktioner som anrop och returnerade resultat. För att garantera repeterbarhet - till exempel åter köra ditt jobb från äldre tidsstämpel ger samma resultat igen - inte använda funktioner som `Date.GetData()` eller `Math.random()`, eftersom dessa funktioner inte returnerar samma resultat för varje åkallan.  

## <a name="diagnostic-logs"></a>Diagnostikloggar

Alla körningsfel anses vara allvarliga och visas genom aktivitets- och diagnostikloggar. Vi rekommenderar att funktionen hanterar alla undantag och fel och returnerar ett giltigt resultat till frågan. Detta förhindrar att ditt jobb går till ett [tillstånd som inte gick.](job-states.md)  


## <a name="next-steps"></a>Nästa steg

* [JavaScript-användardefinierade funktioner i Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [JavaScript-användardefinierade aggregat i Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md)
* [Utveckla .NET Standard användardefinierade funktioner för Azure Stream Analytics-jobb](stream-analytics-edge-csharp-udf-methods.md)
* [Integrera Azure Stream Analytics med Azure Machine Learning](machine-learning-udf.md)

