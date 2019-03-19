---
title: Lär dig hur du konfigurerar och hanterar Time to Live i Azure Cosmos DB
description: Lär dig hur du konfigurerar och hanterar Time to Live i Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/14/2018
ms.author: mjbrown
ms.openlocfilehash: bb5997c2ae8f93068b0ad2a77b5109f6c79b9b30
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58003509"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>Konfigurera Time to Live i Azure Cosmos DB

Du kan välja att konfigurera Time to Live (TTL) på containernivån i Azure Cosmos DB, eller så kan du åsidosätta det på en objektnivå när du har ställt in containern. Du kan konfigurera TTL för en container med hjälp av Azure-portalen eller språkspecifika SDK:er. Åsidosättningar av TTL på objektnivå kan konfigureras med hjälp av SDK:erna.

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Aktivera TTL för en container med hjälp av Azure-portalen

Använd följande steg för att aktivera TTL för en container utan upphörande. Aktivera det här alternativet om du vill tillåta att TTL åsidosätts på objektnivå. Du kan också ange TTL genom att ange ett annat värde än noll för sekunder.

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Skapa ett nytt Azure Cosmos DB-konto eller välj ett befintligt konto.

3. Öppna fönstret **Datautforskaren**.

4. Välj en befintlig container, expandera den och ändra följande värden:

   * Öppna fönstret **Scale & Settings** (Skalning och inställningar).
   * Under **Inställning** letar du upp **Time to Live**.
   * Välj **På (inte standard)** eller välj **På** och ange ett TTL-värde
   * Klicka på **Spara** för att spara ändringarna.

   ![Konfigurera Time to Live i Azure-portalen](./media/how-to-time-to-live/how-to-time-to-live-portal.png)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Aktivera TTL för en container med hjälp av SDK

### <a id="dotnet-enable-noexpiry"></a>.NET SDK

```csharp
// Create a new collection with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition,
    new RequestOptions { OfferThroughput = 20000 });
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Ställa in TTL för en container med hjälp av SDK

### <a id="dotnet-enable-withexpiry"></a>.NET SDK

Om du vill ställa in Time to Live för en container måste du ange ett positivt tal som inte är noll som anger tidsperioden i sekunder. Baserat på det konfigurerade TTL-värde tas alla objekt i containern efter den senast ändrade tidsstämpeln för objektet `_ts` bort.

```csharp
// Create a new collection with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition,
    new RequestOptions { OfferThroughput = 20000 });
```

### <a id="nodejs-enable-withexpiry"></a>NodeJS SDK

```javascript
const containerDefinition = {
          id: "sample container1",
        };

async function createcontainerWithTTL(db: Database, containerDefinition: ContainerDefinition, collId: any, defaultTtl: number) {
      containerDefinition.id = collId;
      containerDefinition.defaultTtl = defaultTtl;
      await db.containers.create(containerDefinition);
}
```

## <a name="set-time-to-live-on-an-item"></a>Ställa in TTL-värde på ett objekt

Förutom att ställa in ett standard-TTL-värde i en container kan du ange du en tid för ett objekt. När du ställer in Time to Live på objektnivå åsidosätts standard-TTL för det objektet i containern.

* Om du vill ställa in TTL-värdet för ett objekt måste du ange ett positivt tal som inte är noll, vilket anger inom vilken period i sekunder som objektet ska upphöra att gälla efter den senast ändrade tidsstämpeln för objektet `_ts`.

* Om objektet inte har något TTL-fält tillämpas som standard TTL-värdet som angetts för containern på objektet.

* Om TTL är inaktiverat på containernivå ignoreras TTL-fältet på objektet tills TTL återaktiverats för containern.

### <a id="portal-set-ttl-item"></a>Azure-portalen

Använd följande steg för att aktivera TTL-värde på ett objekt:

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Skapa ett nytt Azure Cosmos DB-konto eller välj ett befintligt konto.

3. Öppna fönstret **Datautforskaren**.

4. Välj en befintlig container, expandera den och ändra följande värden:

   * Öppna fönstret **Scale & Settings** (Skalning och inställningar).
   * Under **Inställning** letar du upp **Time to Live**.
   * Välj **på (inte standard)** eller välj **på** och ange ett TTL-värde. 
   * Klicka på **Spara** för att spara ändringarna.

5. Därefter går du till det objekt du vill ange tiden live, lägga till den `ttl` egenskapen och välj **uppdatering**. 

   ```json
   {
    "id": "1",
    "_rid": "Jic9ANWdO-EFAAAAAAAAAA==",
    "_self": "dbs/Jic9AA==/colls/Jic9ANWdO-E=/docs/Jic9ANWdO-EFAAAAAAAAAA==/",
    "_etag": "\"0d00b23f-0000-0000-0000-5c7712e80000\"",
    "_attachments": "attachments/",
    "ttl": 10,
    "_ts": 1551307496
   }
   ```

### <a id="dotnet-set-ttl-item"></a>.NET SDK

```csharp
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    [JsonProperty(PropertyName="cid")]
    public string CustomerId { get; set; }
    // used to set expiration policy
    [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
    public int? ttl { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    ttl = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

### <a id="nodejs-set-ttl-item"></a>NodeJS SDK

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value", 
          ttl: 2
        };
```


## <a name="reset-time-to-live"></a>Återställa Time to live

Du kan återställa time to live på ett objekt genom att utföra en skriv- eller uppdateringsåtgärd för objektet. Skriv- eller uppdateringsåtgärden ställer in `_ts` på den aktuella tiden och TTL-värdet för objektet som ska upphöra att gälla påbörjas igen. Om du vill ändra TTL-värdet för ett objekt kan du uppdatera fältet precis som du uppdaterar andra fält.

### <a id="dotnet-extend-ttl-item"></a>.NET SDK

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

## <a name="turn-off-time-to-live"></a>Stäng av Time to Live

Om Time to Live har angetts för ett objekt och du inte längre vill att objektet ska att upphöra att gälla kan du sedan hämta objektet, ta bort TTL-fältet och ersätta objektet på servern. När TTL-fältet tas bort från objektet tillämpas standard-TTL-värdet som tilldelats containern på objektet. Ställ i TTL-värdet på -1 för att förhindra att ett objekt upphör att gälla och för att inte ärva TTL-värdet från containern.

### <a id="dotnet-turn-off-ttl-item"></a>.NET SDK

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = null; // inherit the default TTL of the collection

response = await client.ReplaceDocumentAsync(readDocument);
```

## <a name="disable-time-to-live"></a>Inaktivera Time to live

Om du vill inaktivera Time to Live för en container och förhindra att bakgrundsprocessen söker efter objekt som har upphört att gälla ska du ta bort egenskapen `DefaultTimeToLive` för containern. Att ta bort den här egenskapen skiljer sig från att ställa in den på -1. När du ställer in den på -1 finns nya objekt som läggs till i containern kvar för evigt, men du kan åsidosätta det här värdet för specifika objekt i containern. När du tar bort egenskapen TTL-värde från behållaren upphör aldrig objekt, även om det finns de har uttryckligen åsidosätts tidigare standard TTL-värdet.

### <a id="dotnet-disable-ttl"></a>.NET SDK

```csharp
// Get the collection, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Time to Live i följande artikel:

* [Time to live](time-to-live.md)
