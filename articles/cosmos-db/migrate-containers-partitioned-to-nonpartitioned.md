---
title: Migrera icke-partitionerade Azure Cosmos-behållare till partitionerade behållare
description: Lär dig hur du migrerar alla befintliga behållare som inte är partitionerade till partitionerade behållare.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mjbrown
ms.openlocfilehash: 742ef62895f3ef64e8fa22ab21d2947bee57776b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623356"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Migrera behållare som inte är partitionerade till partitionerade behållare

Azure Cosmos DB stöder att skapa behållare utan en partitionsnyckel. För närvarande kan du skapa behållare som inte är partitionerade med Hjälp av Azure CLI och Azure Cosmos DB SDK (.Net, Java, NodeJs) som har en version som är mindre än eller lika med 2.x. Du kan inte skapa behållare som inte är partitionerade med Azure-portalen. Sådana icke-partitionerade behållare är dock inte elastiska och har fast lagringskapacitet på 20 GB och genomströmningsgräns på 10 K RU/s.

De icke-partitionerade behållarna är äldre och du bör migrera dina befintliga icke-partitionerade behållare till partitionerade behållare för att skala lagring och dataflöde. Azure Cosmos DB tillhandahåller en systemdefinierad mekanism för att migrera dina icke-partitionerade behållare till partitionerade behållare. Det här dokumentet förklarar hur alla befintliga behållare som inte är partitionerade automatiskt migreras till partitionerade behållare. Du kan bara dra nytta av funktionen för automatisk migrering om du använder V3-versionen av SDK:er på alla språk.

> [!NOTE]
> För närvarande kan du inte migrera Azure Cosmos DB MongoDB- och Gremlin API-konton med hjälp av stegen som beskrivs i det här dokumentet.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Migrera behållare med den systemdefinierade partitionsnyckeln

För att stödja migreringen tillhandahåller Azure Cosmos `/_partitionkey` DB en systemdefinierad partitionsnyckel som namnges på alla behållare som inte har en partitionsnyckel. Du kan inte ändra partitionsnyckeldefinitionen när behållarna har migrerats. Definitionen av en behållare som migreras till en partitionerad behållare blir till exempel följande:

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```

När behållaren har migrerats kan du `_partitionKey` skapa dokument genom att fylla i egenskapen tillsammans med dokumentets andra egenskaper. Egenskapen `_partitionKey` representerar partitionsnyckeln för dina dokument.

Att välja rätt partitionsnyckel är viktigt att använda det etablerade dataflödet optimalt. Mer information finns i [hur du väljer en partitionsnyckelartikel.](partitioning-overview.md)

> [!NOTE]
> Du kan bara dra nytta av systemdefinierad partitionsnyckel om du använder den senaste/V3-versionen av SDK:er på alla språk.

I följande exempel visas en exempelkod för att skapa ett dokument med den systemdefinierade partitionsnyckeln och läsa dokumentet:

**JSON-representation av dokumentet**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey", NullValueHandling = NullValueHandling.Ignore)]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234",
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
}

ItemResponse<DeviceInformationItem > response = 
  await migratedContainer.CreateItemAsync<DeviceInformationItem>(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
ItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  );

```

Det fullständiga exemplet finns i [GitHub-databasen för Net-exempel.][1]
                      
## <a name="migrate-the-documents"></a>Migrera dokumenten

Medan behållardefinitionen förbättras med egenskapen partitionsnyckel migreras inte dokumenten i behållaren automatiskt. Vilket innebär att egenskapssökvägen för systempartitionsnyckeln `/_partitionKey` inte automatiskt läggs till i befintliga dokument. Du måste partitionera om de befintliga dokumenten genom att läsa de dokument som `_partitionKey` skapades utan en partitionsnyckel och skriva om dem med egenskapen i dokumenten.

## <a name="access-documents-that-dont-have-a-partition-key"></a>Komma åt dokument som inte har en partitionsnyckel

Program kan komma åt befintliga dokument som inte har en partitionsnyckel med hjälp av den speciella systemegenskapen "PartitionKey.None", detta är värdet för de icke-migrerade dokumenten. Du kan använda den här egenskapen i alla CRUD- och frågeåtgärder. I följande exempel visas ett exempel för att läsa ett enda dokument från NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

Det fullständiga exemplet på hur du partitionerar om dokumenten finns i [GitHub-databasen för Net-exempel.][1] 

## <a name="compatibility-with-sdks"></a>Kompatibilitet med SDK:er

Äldre versioner av Azure Cosmos DB SDK:er som V2.x.x och V1.x.x stöder inte egenskapen för systemdefinierad partitionsnyckel. Så när du läser behållardefinitionen från en äldre SDK innehåller den ingen partitionsnyckeldefinition och dessa behållare fungerar precis som tidigare. Program som är byggda med den äldre versionen av SDK:er fortsätter att fungera med icke-partitionerade som utan några ändringar. 

Om en migrerad behållare förbrukas av den senaste/V3-versionen av SDK och du börjar fylla den systemdefinierade partitionsnyckeln i de nya dokumenten, kan du inte komma åt (läsa, uppdatera, ta bort, fråga) sådana dokument från de äldre SDK:erna längre.

## <a name="known-issues"></a>Kända problem

**Att fråga efter antalet objekt som infogats utan en partitionsnyckel med hjälp av V3 SDK kan innebära högre dataflödesförbrukning**

Om du frågar från V3 SDK om de objekt som infogas med V2 SDK, eller `PartitionKey.None` de objekt som infogas med hjälp `PartitionKey.None` av parametern V3 SDK med, kan antaletfrågan förbruka mer RU/s om parametern anges i FeedOptions. Vi rekommenderar att du inte `PartitionKey.None` anger parametern om inga andra objekt infogas med en partitionsnyckel.

Om nya objekt infogas med olika värden för partitionsnyckeln, `FeedOptions` kommer det inte att ha några problem att fråga efter sådana objektvärden genom att skicka in rätt nyckel. När du har infogat nya dokument med partitionsnyckel, om du behöver fråga bara antalet dokument utan partitionsnyckelvärdet, kan frågan återigen medföra högre RU/s som liknar de vanliga partitionerade samlingarna.

## <a name="next-steps"></a>Nästa steg

* [Partitionering i Azure Cosmos DB](partitioning-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)
* [Etablera dataflöde på containrar och databaser](set-throughput.md)
* [Arbeta med ett Azure Cosmos-konto](account-overview.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration