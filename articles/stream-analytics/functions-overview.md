---
title: Användardefinierade funktioner i Azure Stream Analytics
description: Den här artikeln är en översikt över användardefinierade funktioner i Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 45e766c624ee96f7faa06fb07d00349e620a4c0a
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133474"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Användardefinierade funktioner i Azure Stream Analytics

SQL-liknande frågespråk i Azure Stream Analytics gör det enkelt att implementera analys logiken i real tid på strömmande data. Stream Analytics ger ytterligare flexibilitet genom anpassade funktioner som anropas i din fråga. Följande kod exempel är en UDF `sampleFunction` som accepterar en parameter, varje inmatnings post som jobbet tar emot och resultatet skrivs till utdata som. `sampleResult`

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>Typer av funktioner

Azure Stream Analytics stöder följande fyra funktions typer: 

* Användardefinierade JavaScript-funktioner 
* Användardefinierade JavaScript-mängder 
* Användardefinierade C#-funktioner (med Visual Studio) 
* Azure Machine Learning 

Du kan använda dessa funktioner för scenarier som real tids poängsättning med hjälp av maskin inlärnings modeller, sträng manipulationer, komplexa matematiska beräkningar, kodning och avkodning av data. 

## <a name="limitations"></a>Begränsningar

Användardefinierade funktioner är tillstånds lösa och returvärdet kan bara vara ett skalärt värde. Det går inte att anropa externa REST-slutpunkter från dessa användardefinierade funktioner, eftersom det förmodligen påverkar jobbets prestanda. 

Azure Stream Analytics har ingen post för alla funktioner-anrop och returnerade resultat. För att garantera repeterbarhet – till exempel att köra jobbet igen från äldre tidsstämpel ger samma resultat igen – Använd inte funktioner som `Date.GetData()` eller `Math.random()`, eftersom dessa funktioner inte returnerar samma resultat för varje anrop.  

## <a name="resource-logs"></a>Resursloggar

Eventuella körnings fel betraktas som allvarliga och exponeras genom aktivitets-och resurs loggar. Vi rekommenderar att din funktion hanterar alla undantag och fel och returnerar ett giltigt resultat till din fråga. Detta förhindrar att jobbet kommer till ett [felaktigt tillstånd](job-states.md).  


## <a name="next-steps"></a>Nästa steg

* [Användardefinierade JavaScript-funktioner i Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Azure Stream Analytics användardefinierade JavaScript-mängder](stream-analytics-javascript-user-defined-aggregates.md)
* [Utveckla .NET standard-användardefinierade funktioner för Azure Stream Analytics jobb](stream-analytics-edge-csharp-udf-methods.md)
* [Integrera Azure Stream Analytics med Azure Machine Learning](machine-learning-udf.md)

