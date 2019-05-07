---
title: Migrera icke-partitionerad Azure Cosmos DB-behållare till partitionerad behållare
description: Lär dig hur du migrerar alla befintliga icke-partitionerad behållare i partitionerad behållare.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: e8aaee8cb7df794652b2c560c5250921c1a4e060
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160642"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Migrera icke-partitionerad behållare till partitionerad behållare

Azure Cosmos DB stöder skapa behållare utan någon partitionsnyckel. För närvarande kan du skapa icke-partitionerad behållare med hjälp av Azure CLI och Azure Cosmos DB SDK (.Net, Java, NodeJs) som har en version som är mindre än eller lika med till 2.x. Du kan inte skapa icke-partitionerad behållare med hjälp av Azure-portalen. Sådana icke-partitionerad behållare är inte elastisk och har åtgärdat lagringskapacitet på 10 GB och dataflöde gränsen på 10 K RU/s.

De icke-partitionerad behållarna äldre och du bör migrera dina befintliga icke-partitionerad behållare till partitionerad behållare för att skala lagringsutrymme och dataflöde. Azure Cosmos DB erbjuder en systemdefinierade mekanism för att migrera dina icke-partitionerad behållare till partitionerad behållare. Det här dokumentet beskriver hur alla befintliga icke-partitionerad behållare är automatisk migreras till partitionerad behållare. Du kan dra nytta av funktionen för automatisk migrering bara om du använder den V3-versionen av SDK: er på alla språk.

> [!NOTE] 
> För närvarande kan migrera du inte konton för Azure Cosmos DB MongoDB och Gremlin-API med hjälp av stegen som beskrivs i det här dokumentet. 

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Migrera behållare med Partitionsnyckeln systemdefinierade

För att stödja migrering, Azure Cosmos DB definierar en systemdefinierade partitionsnyckel med namnet `/_partitionkey` på alla behållare som inte har en partitionsnyckel. Du kan inte ändra definitionen av en partition när behållarna har migrerats. Till exempel debiteras definitionen av en behållare som har migrerats till en partitionerad behållare enligt följande: 

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
 
När behållaren har migrerats, kan du skapa dokument genom att fylla i `_partitionKey` egenskapen tillsammans med andra egenskaper för dokumentet. Den `_partitionKey` egenskap representerar Partitionsnyckeln för dina dokument. 

Välja rätt Partitionsnyckeln är viktigt att använda det etablerade dataflödet optimalt. Mer information finns i [hur du väljer en partitionsnyckel](partitioning-overview.md) artikeln. 

> [!NOTE]
> Du kan dra nytta av system definierad partitionsnyckel endast om du använder den senaste/V3-versionen av SDK: er på alla språk.

I följande exempel visas en exempelkod för att skapa ett dokument med system definierad partitionsnyckel och läsa dokumentet:

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

    [JsonProperty(PropertyName = "_partitionKey")]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234", 
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

CosmosItemResponse<DeviceInformationItem > response = 
  await migratedContainer.Items.CreateItemAsync(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
CosmosItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  ); 

```

Hela exemplet finns det [.Net-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) GitHub-lagringsplatsen. 
                      
## <a name="migrate-the-documents"></a>Migrera dokumenten

Medan behållardefinitionen har förbättrats med en partition nyckelegenskap så dokumenten i behållaren inte automatiskt migrerats. Vilket innebär att systemet partition nyckelegenskapen `/_partitionKey` sökväg läggs inte automatiskt till de befintliga dokument. Du måste partitionera om befintliga dokument genom att läsa dokument som har skapats utan någon partitionsnyckel och skriva om dem med `_partitionKey` -egenskapen i dokumenten. 

## <a name="access-documents-that-dont-have-a-partition-key"></a>Få åtkomst till dokument som inte har en partitionsnyckel

Program kan komma åt de befintliga dokument som inte har en partitionsnyckel med hjälp av Systemegenskapen särskilda som kallas ”CosmosContainerSettings.NonePartitionKeyValue”, det här är värdet för icke-migrerade-dokument. Du kan använda den här egenskapen i alla CRUD- och åtgärder. I följande exempel visas ett exempel för att läsa ett enstaka dokument från NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: CosmosContainerSettings.NonePartitionKeyValue, 
  id: device.Id
); 

```

Komplett exempel på hur du partitionera om dokumenten finns det [.Net-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) GitHub-lagringsplatsen. 

## <a name="compatibility-with-sdks"></a>Kompatibilitet med SDK: er

Äldre version av Azure Cosmos DB SDK: er, till exempel V2.x.x och V1.x.x stöder inte nyckelegenskapen systemdefinierade partition. När du läser behållardefinitionen från en äldre SDK kan den innehåller inte någon definition av en partition och de här behållarna fungerar precis som tidigare. Program som har skapats med den äldre versionen av SDK: er fortsätta arbeta med icke-partitionerad skick utan ändringar. 

Om en migrerad behållare används av den senaste/V3-versionen av SDK och du börjar fyllas Partitionsnyckeln som definieras i de nya dokument, kan du inte kommer åt (läsa, uppdatera, ta bort, fråga) dokument, till exempel från äldre SDK: erna längre.

## <a name="next-steps"></a>Nästa steg

* [Partitionering i Azure Cosmos DB](partitioning-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)
* [Etablera dataflöde på behållare och databaser](set-throughput.md)
* [Arbeta med Azure Cosmos-konto](account-overview.md)