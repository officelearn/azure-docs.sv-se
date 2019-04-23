---
title: Skapa en syntetisk partitionsnyckel i Azure Cosmos DB för att distribuera dina data och arbetsbelastningen jämnt.
description: Lär dig att använda syntetiska partitionsnycklar i Azure Cosmos-behållare
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: 6b3499c36ae7abd03c4a1f1ca3a3a46e2c315120
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792106"
---
# <a name="create-a-synthetic-partition-key"></a>Skapa en syntetisk partitionsnyckel

Det är bäst att ha en partitionsnyckel med många olika värden, till exempel hundratals eller tusentals. Målet är att distribuera dina data och arbetsbelastningen jämnt över de objekt som är associerade med de här partitionsnyckelvärdena. Om sådan egenskap inte finns i dina data, kan du skapa en *syntetiska partitionsnyckel*. Det här dokumentet beskrivs olika grundläggande metoder för att generera en syntetisk partitionsnyckel för Cosmos-behållare.

## <a name="concatenate-multiple-properties-of-an-item"></a>Sammanfoga flera egenskaper för ett objekt

Du kan utforma en partitionsnyckel genom att sammanfoga flera värden i en enda artificiella `partitionKey` egenskapen. De här nycklarna kallas syntetiska nycklar. Tänk dig följande exempel dokument:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

För tidigare dokumentet är ett alternativ att ange /deviceId eller /date som partitionsnyckel. Använd det här alternativet om du vill partitionera din behållare baserat på enhets-ID eller datum. Ett annat alternativ är att sammanfoga två gånger i ett syntetiskt `partitionKey` egenskap som används som partitionsnyckel.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

Du kan ha tusentals objekt i en databas i realtid scenarier. Definiera klientsidan logik för att sammanfoga värden och infoga syntetiska nyckeln i objekten i dina Cosmos-behållare i stället för att lägga till nyckeln syntetiska och manuellt.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Använda en partitionsnyckel med ett slumpmässigt suffix

En annan möjlig strategi att fördela arbetsbelastningen jämnare är att lägga till ett slumptal i slutet av partitionsnyckelvärdet. När du distribuerar objekt i det här sättet kan du utföra parallella skrivåtgärder över partitioner.

Ett exempel är om en partitionsnyckel representerar ett datum. Du kan välja ett slumptal mellan 1 och 400 och sammanfoga som suffix i datumet. Den här metoden innebär partitionsnyckelvärdena som `2018-08-09.1`,`2018-08-09.2`och så vidare via `2018-08-09.400`. Eftersom du slumpgenerera Partitionsnyckeln fördelade skrivåtgärder för behållaren varje dag jämnt över flera partitioner. Den här metoden resulterar i bättre parallellitet och övergripande högre dataflöde.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Använda en partitionsnyckel med förberäknade suffix 

Slumpmässig suffix strategin kan förbättra genomströmning för skrivning, men det är svårt att läsa ett specifikt objekt. Du vet inte suffix värde som användes när du skrev objektet. Använda förberäknade suffix strategin för att göra det lättare att läsa enskilda objekt. Ange ett tal som beräknas baserat på något som du vill fråga istället för att använda ett slumptal för att distribuera objekt mellan partitionerna.

Överväg att i föregående exempel, där en behållare använder ett datum som partitionsnyckel. Anta nu att varje objekt har en `Vehicle-Identification-Number` (`VIN`) attributet som vi vill ha åtkomst till. Dessutom kan anta att du ofta kör frågor för att hitta objekt av den `VIN`, förutom datum. Innan ditt program skriver objektet till behållaren, kan den beräkna ett hash-suffix baserat på VIN och lägger till dem i partitionen viktiga datum. Beräkningen kan generera ett tal mellan 1 och 400 som är jämnt fördelat. Resultatet liknar de resultat som skapas av metoden slumpmässiga suffix strategi. Partitionsnyckelvärdet är sedan det datum som sammanfogas med det beräknade värdet.

Med den här strategin är skrivningar jämnt fördelade över partitionsnyckelvärdena och över partitioner. Du kan enkelt läsa en viss artikel och datum, eftersom du kan beräkna partitionsnyckelvärdet för ett visst `Vehicle-Identification-Number`. Fördelen med den här metoden är att du slipper skapa en enda frekvent partitionsnyckel, t.ex, en partitionsnyckel som tar alla arbetsbelastningar. 

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om begreppet partitionering i följande artiklar:

* Läs mer om [logiska partitioner](partition-data.md).
* Mer information om hur du [etablera dataflöde för Azure Cosmos-behållare och databaser](set-throughput.md).
* Lär dig hur du [etablera dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* Lär dig hur du [etablera dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).
