---
title: Felsöka Azure Cosmos DB inte hittade undantag
description: Identifiera och åtgärda undantag som inte hittades
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f391772672904a1e2bdfa0a741d9f85a6edeea8b
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294621"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found"></a>Diagnostisera och Felsök Azure Cosmos DB som inte hittas
HTTP-statuskoden 404 representerar att resursen inte längre finns.

## <a name="expected-behavior"></a>Förväntat beteende
Det finns många giltiga scenarier där ett program förväntar sig en 404 och hanterar scenariot korrekt.

## <a name="not-found-was-returned-for-an-item-that-should-exist-or-does-exist"></a>Hittades inte för ett objekt som ska finnas eller som finns
Nedan visas möjliga orsaker till att en status kod 404 returneras om objektet ska eller avslutas.

### <a name="1-race-condition"></a>1. konkurrens villkor
Det finns flera SDK-klient instanser och läsningen skedde innan skrivningen.

#### <a name="solution"></a>Lösning:
1. Standard kontot är konsekvens för Cosmos DB är konsekvens på sessionen. När ett objekt skapas eller uppdateras returnerar svaret en sessions-token som kan skickas mellan SDK-instanser för att garantera att Read-begäran läser från en replik med den ändringen.
2. Ändra [konsekvens nivån](consistency-levels-choosing.md) till en [starkare nivå](consistency-levels-tradeoffs.md)

### <a name="2-invalid-partition-key-and-id-combination"></a>2. ogiltig kombination av partitionsnyckel och ID
Kombinationen av partitionsnyckel och ID är ogiltig.

#### <a name="solution"></a>Lösning:
Åtgärda den program logik som orsakar felaktig kombination. 

### <a name="3-invalid-character-in-item-id"></a>3. ogiltigt Character i objekt-ID
Ett objekt infogas i Cosmos DB med ett [ogiltigt Character](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks) i objekt-ID: t.

#### <a name="solution"></a>Lösning:
Vi rekommenderar att användare ändrar ID till ett annat värde som inte innehåller specialtecknen. Om du ändrar ID: t inte är ett alternativ kan du base64 koda ID: t för att undanta specialtecknen.

Objekt som redan har infogats i behållaren det går att ersätta ID: t med hjälp av RID-värden i stället för namnbaserade referenser.
```c#
// Get a container reference that use RID values
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// List of invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // It recommend to chose a new ID that does not contain special characters, but
        // if that is not possible then it can be Base64 encoded to escape the special characters
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value using the rid based container reference
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validate the new ID can be read using the original name based contianer reference
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="4-time-to-live-ttl-purge"></a>4. rensning av TTL (Time to Live)
Värdet [för egenskapen TTL (Time to Live)](https://docs.microsoft.com/azure/cosmos-db/time-to-live) har angetts för objektet. Objektet tömdes eftersom tiden för Live hade upphört att gälla.

#### <a name="solution"></a>Lösning:
Ändra tiden till Live för att förhindra att objektet tas bort.

### <a name="5-lazy-indexing"></a>5. Lazy-indexering
Den [Lazy-indexeringen](index-policy.md#indexing-mode) har inte fångats upp.

#### <a name="solution"></a>Lösning:
Vänta tills indexeringen har fångats upp eller ändra indexerings principen

### <a name="6-parent-resource-deleted"></a>6. överordnad resurs har tagits bort
Databasen och/eller behållaren som objektet finns i har tagits bort.

#### <a name="solution"></a>Lösning:
1. [Återställ](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore#backup-retention-period) den överordnade resursen eller återskapa resurserna.
2. Skapa en ny resurs som ersätter den borttagna resursen

## <a name="next-steps"></a>Nästa steg
* [Diagnostisera och Felsök](troubleshoot-dot-net-sdk.md) problem när du använder Azure Cosmos dB .NET SDK
* Lär dig mer om prestanda rikt linjer för [.net v3](performance-tips-dotnet-sdk-v3-sql.md) och [.NET v2](performance-tips.md)