---
title: Syntetiska partitionsnycklar i Azure Cosmos DB
description: Lär dig hur du använder syntetiska partitionsnycklar i Azure Cosmos DB-behållare
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: andrl
ms.openlocfilehash: f6323549c0ecf8f0196d327779f7f7cb67c6e03f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263514"
---
# <a name="create-a-synthetic-partition-key"></a>Skapa en syntetisk partitionsnyckel

Det är en bra idé att ha en partitionsnyckel med många distinkta värden, till exempel hundratals eller tusentals. Målet är att distribuera dina data och arbetsbelastningen jämnt över de objekt som är associerade med de här partitionsnyckelvärdena. Om sådan egenskap inte finns i dina data, kan en syntetisk partitionsnyckel skapas. I följande avsnitt beskrivs flera grundläggande tekniker för att generera en syntetisk partitionsnyckel för behållaren.

## <a name="concatenating-multiple-properties-of-an-item"></a>Sammanfoga flera egenskaper för ett objekt

Du kan utforma en partitionsnyckel genom att sammanfoga flera värden i en enda artificiella `partitionKey` egenskapen. De här nycklarna kallas syntetiska nycklar. Tänk dig följande exempel dokument:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

För tidigare dokumentet är ett alternativ att ange /deviceId eller /date som partitionsnyckel, om du vill partitionera din behållare baserat på enhets-ID eller datum. Ett annat alternativ är att sammanfoga två gånger i ett syntetiskt `partitionKey` egenskap som används som partitionsnyckel.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

I realtid scenarier, du har tusentals dokument i en databas, så du bör definiera klientsidan logik för att sammanfoga värden och infoga syntetiska nyckeln i dokumenten i stället för att lägga till nyckeln syntetiska och manuellt.

## <a name="using-a-partition-key-with-random-suffix"></a>Om du använder en partitionsnyckel med slumpmässiga suffix

En annan möjlig strategi att fördela arbetsbelastningen jämnare är att lägga till ett slumptal i slutet av partitionsnyckelvärdet. Fördela objekt i det här sättet kan du utföra parallell skrivåtgärder över partitioner.

Om en partitionsnyckel representerar ett datum, kan du välja ett slumptal mellan 1 och 400 och sammanfoga som suffix i datumet. Den här metoden leder partitionsnyckelvärdena som 2018-08-09.1, 2018-08-09.2, och så vidare, via 2018-08-09.400. Eftersom du randomisering Partitionsnyckeln fördelade skrivåtgärder för behållaren varje dag jämnt över flera partitioner. Den här metoden resulterar i bättre parallellitet och övergripande högre dataflöde.

## <a name="using-a-partition-key-with-pre-calculated-suffixes"></a>Om du använder en partitionsnyckel med förberäknade suffix 

Även om randomisering strategi kan avsevärt förbättra genomströmning för skrivning, är det svårt att läsa ett specifikt objekt eftersom du inte vet suffix-värde som används när du skriver artikeln. Du kan använda förberäknade suffix-strategi för att göra det lättare att läsa enskilda objekt. Använd ett tal som beräknar baserat på något som du vill fråga i stället för att distribuera objekt mellan partitioner med hjälp av ett slumptal.

Överväg att i föregående exempel, där en behållare använder ett datum i Partitionsnyckeln. Nu antar vi att varje objekt har en tillgänglig VIN (Vehicle-identifieringsnummer)-attributet och om du ofta köra frågor för att hitta objekt av VIN, dessutom hittills. Innan ditt program skriver objektet till behållaren, kan den beräkna ett hash-suffix baserat på VIN och lägger till dem i partitionen viktiga datum. Beräkningen kan generera ett tal mellan 1 och 400 som är jämnt fördelade, vilket liknar de resultat som skapas av metoden slumpmässiga strategi. Partitionsnyckelvärdet kommer sedan att det datum som sammanfogas med det beräknade värdet.

Med den här strategin är skrivningar jämnt fördelade över partitionsnyckelvärdena och över partitioner. Du kan enkelt läsa en viss artikel och datum, eftersom du kan beräkna partitionsnyckelvärdet för ett specifikt Vehicle-ID-nummer. Fördelen med den här metoden är att du slipper skapa en enda frekvent partitionsnyckel (den partitionsnyckel som tar alla arbetsbelastningar). 

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om begreppet partitionering i följande artiklar:

* Läs mer om [logiska partitioner](partition-data.md)
* Läs mer om [etablering dataflöde på Cosmos-behållare och databaser](set-throughput.md)
* Lär dig [hur du etablera dataflöde för en Cosmos-behållare](how-to-provision-container-throughput.md)
* Lär dig [hur du etablera dataflöde för en Cosmos-databas](how-to-provision-database-throughput.md)
