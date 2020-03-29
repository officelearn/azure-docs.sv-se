---
title: Skapa en syntetisk partitionsnyckel i Azure Cosmos DB
description: Lär dig hur du använder syntetiska partitionsnycklar i dina Azure Cosmos-behållare för att fördela data och arbetsbelastning jämnt över partitionsnycklarna
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: e8786c2d6e93c18a5bf9856a5555d6b528f842c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441221"
---
# <a name="create-a-synthetic-partition-key"></a>Skapa en syntetisk partitionsnyckel

Det är det bästa sättet att ha en partitionsnyckel med många olika värden, till exempel hundratals eller tusentals. Målet är att fördela dina data och arbetsbelastning jämnt över de objekt som är associerade med dessa partitionsnyckelvärden. Om en sådan egenskap inte finns i dina data kan du skapa en *syntetisk partitionsnyckel*. I det här dokumentet beskrivs flera grundläggande tekniker för att generera en syntetisk partitionsnyckel för Cosmos-behållaren.

## <a name="concatenate-multiple-properties-of-an-item"></a>Sammanfoga flera egenskaper för en artikel

Du kan skapa en partitionsnyckel genom att sammanfoga `partitionKey` flera egenskapsvärden till en enda artificiell egenskap. Dessa nycklar kallas syntetiska nycklar. Tänk dig till exempel följande exempeldokument:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

För föregående dokument är ett alternativ att ange /deviceId eller /date som partitionsnyckel. Använd det här alternativet om du vill partitionera behållaren baserat på antingen enhets-ID eller datum. Ett annat alternativ är att sammanfoga dessa `partitionKey` två värden till en syntetisk egenskap som används som partitionsnyckel.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

I realtidsscenarier kan du ha tusentals objekt i en databas. I stället för att lägga till den syntetiska nyckeln manuellt definierar du logik på klientsidan för att sammanfoga värden och infoga den syntetiska nyckeln i objekten i Cosmos-behållarna.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Använda en partitionsnyckel med ett slumpmässigt suffix

En annan möjlig strategi för att fördela arbetsbelastningen jämnare är att lägga till ett slumptal i slutet av partitionsnyckelvärdet. När du distribuerar objekt på det här sättet kan du utföra parallella skrivåtgärder över partitioner.

Ett exempel är om en partitionsnyckel representerar ett datum. Du kan välja ett slumptal mellan 1 och 400 och sammanfoga det som ett suffix till datumet. Den här metoden resulterar `2018-08-09.1``2018-08-09.2`i partitionsnyckelvärden `2018-08-09.400`som , och så vidare, genom . Eftersom du randomiserar partitionsnyckeln sprids skrivåtgärderna på behållaren varje dag jämnt över flera partitioner. Denna metod resulterar i bättre parallellism och övergripande högre genomströmning.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Använda en partitionsnyckel med förberäknade suffix 

Den slumpmässiga suffixstrategin kan avsevärt förbättra skrivflödet, men det är svårt att läsa ett visst objekt. Du vet inte suffixvärdet som användes när du skrev objektet. Om du vill göra det enklare att läsa enskilda objekt använder du den förberäknade suffixstrategin. I stället för att använda ett slumptal för att fördela objekten mellan partitionerna använder du ett tal som beräknas baserat på något som du vill fråga efter.

Tänk på föregående exempel, där en behållare använder ett datum som partitionsnyckel. Anta nu att varje `Vehicle-Identification-Number` `VIN`objekt har ett ( ) attribut som vi vill komma åt. Anta vidare att du ofta kör frågor `VIN`för att hitta objekt efter , förutom datum. Innan programmet skriver objektet till behållaren kan det beräkna ett hash-suffix baserat på VIN och lägga till det i partitionsnyckeldatumet. Beräkningen kan generera ett tal mellan 1 och 400 som är jämnt fördelat. Detta resultat liknar de resultat som produceras av metoden för slumpmässigt suffixstrategi. Partitionsnyckelvärdet är då det datum som sammanfogas med det beräknade resultatet.

Med den här strategin är skrivningarna jämnt fördelade över partitionsnyckelvärdena och över partitionerna. Du kan enkelt läsa ett visst objekt och datum, eftersom `Vehicle-Identification-Number`du kan beräkna partitionsnyckelvärdet för ett visst . Fördelen med den här metoden är att du kan undvika att skapa en enda frekvent partitionsnyckel, dvs en partitionsnyckel som tar all arbetsbelastning. 

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om partitioneringskonceptet i följande artiklar:

* Läs mer om [logiska partitioner](partition-data.md).
* Läs mer om hur [du etablerar dataflöde på Azure Cosmos-behållare och databaser](set-throughput.md).
* Lär dig hur du [etablerar dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* Lär dig hur du [etablerar dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).
