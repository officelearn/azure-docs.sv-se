---
title: Fråga enheter i digitala Azure-enheter
titleSuffix: Azure Digital Twins
description: Förstå fakturerings begreppet för frågesträngar i digitala Azure-enheter
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 33c572719d76a2add39aec37329679113fcddb76
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146338"
---
# <a name="query-units-in-azure-digital-twins"></a>Fråga enheter i digitala Azure-enheter 

En Azure Digitals **qu-enhet (** Digital enhets fråga) är en enhet med beräkning på begäran som används för att köra [Azures digitala dubbla frågor](how-to-query-graph.md) med hjälp av [fråge-API](/rest/api/digital-twins/dataplane/query). 

Den sammanfattar de system resurser som processor, IOPS och minne som krävs för att utföra frågor som stöds av digitala Azure-enheter, så att du kan spåra användningen i Query units i stället.

Mängden fråge enheter som används för att köra en fråga påverkas av...

* frågans komplexitet
* storleken på resultat uppsättningen (så att en fråga returnerar 10 resultat förbrukar mer QUs än en fråga av liknande komplexitet som bara returnerar ett resultat)

Den här artikeln förklarar hur du förstår enhets enheter och hur du spårar användning av frågekörning.

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>Hitta användnings enheten för frågekörning i Azure Digitals, dubbla

När du kör en fråga med hjälp av [API: t](/rest/api/digital-twins/dataplane/query)för Azure Digitals dubbla frågor kan du undersöka svars huvudet för att spåra antalet QUs som frågan har använt. Sök efter "fråga-avgift" i svaret som skickas tillbaka från Azure Digitals.

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
* [Dokumentation om API-referens för frågor](/rest/api/digital-twins/dataplane/query/querytwins)

Du hittar frågor relaterade till Azures digitala dubbla gränser i [*referensen: tjänst begränsningar*](reference-service-limits.md).