---
title: Ändra feed-pull-modell
description: Lär dig hur du använder pull-modellen för Azure Cosmos DB ändra feed för att läsa ändrings flödet och skillnaderna mellan pull-modellen och ändra flödes processor
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/06/2020
ms.reviewer: sngun
ms.openlocfilehash: 2854e3d92462ced3958afd1cf1e7e99d7e9892f6
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984686"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Ändra flödes hämtnings modell i Azure Cosmos DB

Med pull-modellen för ändrings flöden kan du använda Azure Cosmos DB ändra feed i din egen takt. Eftersom du redan kan göra med en [bytes processor](change-feed-processor.md)kan du använda pull-modellen för ändrings flöden för att parallellisera bearbetningen av ändringar i flera bytes konsumenter.

> [!NOTE]
> Pull-modellen för ändrings flöden är för närvarande [en för hands version i Azure Cosmos dB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview) . För hands versionen är inte tillgänglig ännu för andra SDK-versioner.

## <a name="consuming-an-entire-containers-changes"></a>Konsumera en hel behållares ändringar

Du kan skapa en `FeedIterator` för att bearbeta ändrings flödet med hjälp av pull-modellen. När du skapar en `FeedIterator`första, kan du ange ett valfritt `StartTime` i. `ChangeFeedRequestOptions` När inget anges `StartTime` är den aktuella tiden.

Det `FeedIterator` kommer att finnas i två varianter. Förutom exemplen nedan som returnerar enhets objekt kan du också få svar med `Stream` stöd. Med strömmar kan du läsa data utan att först avserialiseras, vilket sparar på klient resurserna.

Här är ett exempel på hur du hämtar `FeedIterator` en som returnerar enhets objekt, i det `User` här fallet ett objekt:

```csharp
FeedIterator<User> iteratorWithPOCOS = container.GetChangeFeedIterator<User>();
```

Här är ett exempel på hur du kan `FeedIterator` hämta en som `Stream`returnerar en:

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator();
```

Med hjälp `FeedIterator`av en kan du enkelt bearbeta en hel behållares ändrings flöde i din egen takt. Här är ett exempel:

```csharp
FeedIterator<User> iteratorForTheEntireContainer= container.GetChangeFeedIterator(new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

while (iteratorForTheEntireContainer.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="consuming-a-partition-keys-changes"></a>Använda ändringar i en partitionsnyckel

I vissa fall kanske du bara vill bearbeta ändringar i en viss partitionsnyckel. Du kan hämta en `FeedIterator` för en speciell partitionsnyckel och bearbeta ändringarna på samma sätt som du kan för en hel behållare:

```csharp
FeedIterator<User> iteratorForThePartitionKey = container.GetChangeFeedIterator(new PartitionKey("myPartitionKeyValueToRead"), new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

while (iteratorForThePartitionKey.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>Använda FeedRange för parallellisering

I [change feed-processorn](change-feed-processor.md)sprids arbetet automatiskt över flera konsumenter. I pull-modellen `FeedRange` för ändrings flöden kan du använda för att parallellisera bearbetningen av ändrings flödet. En `FeedRange` representerar ett intervall med värden för partitionerings nycklar.

Här är ett exempel som visar hur du hämtar en lista över intervall för din behållare:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

När du hämtar en lista över FeedRanges för din behållare får du en `FeedRange` per [fysisk partition](partition-data.md#physical-partitions).

Med hjälp `FeedRange`av en kan du skapa en `FeedIterator` för att parallellisera bearbetning av ändrings flödet över flera datorer eller trådar. Till skillnad från föregående exempel som visade hur du hämtar en enskild `FeedIterator` för hela behållaren kan du använda `FeedRange` för att hämta flera FeedIterators som kan bearbeta ändrings flödet parallellt.

I de fall där du vill använda FeedRanges måste du ha en Orchestrator-process som erhåller FeedRanges och distribuerar dem till dessa datorer. Distributionen kan vara:

* Använder `FeedRange.ToJsonString` och distribuerar detta sträng värde. Konsumenterna kan använda det här värdet med`FeedRange.FromJsonString`
* Om distributionen är i processen skickar du `FeedRange` objekt referensen.

Här är ett exempel som visar hur du läser från början av behållarens ändrings flöde med två hypotetiska separata datorer som läses parallellt:

Dator 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<Person>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorA.HasMoreResults)
{
   FeedResponse<User> users = await iteratorA.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

Dator 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ranges[1], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorB.HasMoreResults)
{
   FeedResponse<User> users = await iteratorB.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="saving-continuation-tokens"></a>Sparar fortsättnings-token

Du kan spara positionen genom att `FeedIterator` skapa en fortsättnings-token. En fortsättnings-token är ett sträng värde som håller koll på dina FeedIterator senaste bearbetade ändringar. Detta gör `FeedIterator` att du senare kan återuppta den här tidpunkten. Följande kod läser igenom ändrings flödet sedan containern skapades. När inga fler ändringar är tillgängliga behåller den en fortsättnings-token så att förbrukningen av ändrings flödet kan återupptas senare.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

string continuation = null;

while (iterator.HasMoreResults)
{
   FeedResponse<User> users = await iterator.ReadNextAsync();
   continuation = orders.ContinuationToken;

   foreach (User user in Users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(continuation);
```

## <a name="comparing-with-change-feed-processor"></a>Jämför med byte av byte av flödes processor

Många scenarier kan bearbeta ändrings flödet med hjälp av antingen [change feed-processorn](change-feed-processor.md) eller pull-modellen. Pull-modellens fortsättnings-och förändrings behållar behållar behållare är båda bok märken för det senast bearbetade objektet (eller objekt gruppen) i ändrings flödet.
Du kan dock inte konvertera fortsättnings-token till en Lease container (eller vice versa).

Du bör överväga att använda pull-modellen i följande scenarier:

- Du vill göra en enstaka läsning av befintliga data i ändrings flödet
- Du vill bara läsa ändringar från en viss partitionsnyckel
- Du vill inte ha en push-modell och vill använda ändrings flödet i din egen takt

Här är några viktiga skillnader mellan processorn för förändrings matnings processor och pull-modell:

|  | Ändringsflödesprocessor| Hämta modell |
| --- | --- | --- |
| Hålla koll på den aktuella punkten vid bearbetning av ändrings flöde | Lån (lagras i en Azure Cosmos DB container) | Fortsättnings-token (lagras i minnet eller sparas manuellt) |
| Möjlighet att spela upp tidigare ändringar | Ja, med push-modell | Ja, med pull-modell|
| Söker efter framtida ändringar | Söker automatiskt efter ändringar baserat på användardefinierad`WithPollInterval` | Manuell |
| Bearbeta ändringar från hela behållaren | Ja, och automatiskt parallellt över flera trådar/datorer som konsumeras från samma behållare| Ja, och manuellt parallellt med FeedTokens |
| Bearbeta ändringar från bara en enda partitionsnyckel | Stöds inte | Ja|
| Support nivå | Allmänt tillgänglig | Förhandsgranskning |

## <a name="next-steps"></a>Nästa steg

* [Översikt över ändra feed](change-feed.md)
* [Använda bearbetningen av Change feeds](change-feed-processor.md)
* [Utlösa Azure Functions](change-feed-functions.md)