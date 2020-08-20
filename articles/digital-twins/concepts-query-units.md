---
title: Fråga enheter i digitala Azure-enheter
titleSuffix: Azure Digital Twins
description: Förstå fakturerings begreppet för frågesträngar i digitala Azure-enheter
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 5334a1196ac8044c56e615cf8894b44646b48fb4
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88615118"
---
# <a name="query-units-in-azure-digital-twins"></a>Fråga enheter i digitala Azure-enheter 

En Azure Digitals **qu-enhet (** Digital enhets fråga) är en enhet med beräkning på begäran som används för att köra [Azures digitala dubbla frågor](how-to-query-graph.md) med hjälp av [fråge-API](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query). 

Den sammanfattar de system resurser som processor, IOPS och minne som krävs för att utföra frågor som stöds av digitala Azure-enheter, så att du kan spåra användningen i Query units i stället.

Frågans komplexitet påverkar hur många QUs som används för att köra frågan. 

Den här artikeln förklarar hur du förstår enhets enheter och hur du spårar användning av frågekörning.

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>Hitta användnings enheten för frågekörning i Azure Digitals, dubbla 

När du kör en fråga med hjälp av [API: t](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query)för Azure Digitals dubbla frågor kan du undersöka svars huvudet för att spåra antalet QUs som frågan har använt. Sök efter "fråga-avgift" i svaret som skickas tillbaka från Azure Digitals. 

Med Azure Digitals- [SDK](how-to-use-apis-sdks.md) : er kan du extrahera frågans tilläggs rubrik från det växlade svaret. Det här avsnittet visar hur du frågar efter digitala dubbla och hur du itererar över det växlade svaret för att extrahera frågans avgifts huvud. 

Följande kodfragment visar hur du kan extrahera de fråga avgifter som uppkommer när du anropar API: et för frågor. Den itererar över svars Sidorna först för att få åtkomst till frågans avgifts huvud och itererar sedan över de digitala dubbla resultaten på varje sida. 
 
```csharp
AsyncPageable<string> asyncPageableResponseWithCharge = client.QueryAsync("SELECT * FROM digitaltwins"); 
int pageNum = 0; 

// The "await" keyword here is required, as a call is made when fetching a new page. 

await foreach (Page<string> page in asyncPageableResponseWithCharge.AsPages()) 
{ 
    Console.WriteLine($"Page {++pageNum} results:"); 

    // Extract the query-charge header from the page 

    if (QueryChargeHelper.TryGetQueryCharge(page, out float queryCharge)) 
    { 
        Console.WriteLine($"Query charge was: {queryCharge}"); 
    } 

    // Iterate over the twin instances. 

    // The "await" keyword is not required here, as the paged response is local. 

    foreach (string response in page.Values) 
    { 
        BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(response); 
        Console.WriteLine($"Found digital twin '{twin.Id}'"); 
    } 
} 
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du frågar Azure Digital-dubbla finns på:
* [*Begrepp: frågespråk*](concepts-query-language.md)
* [*Anvisningar: fråga det dubbla diagrammet*](how-to-query-graph.md)
* [Dokumentation om API-referens för frågor](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query/querytwins)

Du kan hitta frågor relaterade till Azure Digitals-relaterade gränser i [*referensen: tjänst gränser i offentlig för hands version*](reference-service-limits.md).