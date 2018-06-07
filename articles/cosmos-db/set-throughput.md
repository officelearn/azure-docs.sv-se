---
title: Etablera genomströmning för Azure Cosmos DB | Microsoft Docs
description: Lär dig hur du ställer in etablerat dataflöde för din Azure Cosmos DB containsers, samlingar, diagram och tabeller.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: sngun
ms.openlocfilehash: 777655d8976990396b2c78a5b6d977a92b1a2335
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34614085"
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers-and-database"></a>Ange och få genomströmning för Azure Cosmos DB behållare och databasen

Du kan ange dataflöde för en Azure DB som Cosmos-behållare eller en uppsättning av behållare med hjälp av Azure-portalen eller med hjälp av klient-SDK: er. När du etablerar genomströmning för en uppsättning behållare dela dessa behållare etablerat dataflöde. Etablering genomströmning för enskilda behållare garanterar reservationen genomströmning för specifika behållaren. Å andra sidan kan etablering genomströmning för en databas du dela genomströmning bland alla behållare som hör till databasen. Du kan ha en uppsättning behållare som delar genomflödet samt behållare, som har särskilda genomflöde i en Azure Cosmos-DB-databas. 

Baserat på etablerat dataflöde, allokerar Azure Cosmos DB fysiska partitioner som värd för dina behållare och delningar/rebalances data över partitioner när det växer.

När du tilldelar RU/s på nivån enskilda behållaren behållarna kan skapas som *fast* eller *obegränsade*. Behållare med fast storlek har en maxgräns på 10 GB och en genomströmning på 10 000 RU/s. Om du vill skapa ett obegränsat antal behållare, måste du ange en lägsta dataflöde på 1 000 RU/s och en [partitionsnyckel](partition-data.md). Eftersom dina data kan behöva delas mellan flera partitioner, är det nödvändigt att välja en partitionsnyckel som har en hög kardinalitet (100 miljoner distinkta värden). Genom att välja en partitionsnyckel med många distinkta värden kan du se till att ditt diagram-behållare/tabell och begäranden kan skalas enhetligt med Azure Cosmos DB. 

När du tilldelar RU/sek i en behållare kan de behållare som hör till den här uppsättningen behandlas som *obegränsade* behållare och måste ange en partitionsnyckel.

![Etablering av frågeenheter för enskilda behållare och behållare](./media/request-units/provisioning_set_containers.png)

Den här artikeln vägleder dig igenom de steg som krävs för att konfigurera genomströmning på olika nivåer för ett konto i Azure Cosmos DB. 

## <a name="provision-throughput-by-using-azure-portal"></a>Etablera dataflöde med hjälp av Azure-portalen

### <a name="provision-throughput-for-a-container-collectiongraphtable"></a>Etablera genomströmning för en behållare (tabell-samling/diagram)

1. Logga in på [Azure Portal](https://portal.azure.com).  
2. Välj vänstra navigeringsfältet **alla resurser** och hitta Azure DB som Cosmos-konto.  
3. Du kan konfigurera dataflöde när du skapar en behållare (insamling, diagram, tabell) eller uppdatera genomströmning för en befintlig behållare.  
4. Om du vill tilldela dataflöde när du skapar en behållare, öppna den **Data Explorer** och välj **ny samling** (nytt diagram, ny tabell för andra API: er)  
5. Fylla i formuläret **lägga till samlingen** bladet. I det här bladet beskrivs i följande tabell:  

   |**Inställning**  |**Beskrivning**  |
   |---------|---------|
   |Databas-id  |  Ange ett unikt namn som identifierar din databas. Databasen är en logisk behållare för en eller flera samlingar. Databasnamn måste innehålla mellan 1 och 255 tecken och får inte innehålla /, \\, #, ? eller avslutande blanksteg. |
   |Samlings-id  | Ange ett unikt namn som identifierar din samling. Samma teckenkrav gäller för samlings-ID:n som databasnamn. |
   |Lagringskapacitet   | Det här värdet representerar lagringskapaciteten för databasen. Etablera genomströmning för en enskild samling lagringskapacitet kan vara **fast (10 GB)** eller **obegränsad**. Obegränsade lagringskapacitet måste du ange en partitionsnyckel för dina data.  |
   |Dataflöde   | Varje samling och databasen kan ha genomflöde i frågeenheter per sekund.  Fast lagringskapacitet minsta dataflöde är 400 frågeenheter per sekund (RU/s), för obegränsad lagring kapacitet, minsta genomströmning är inställd på 1000 RU/s.|

6. När du anger värden för dessa fält, Välj **OK** spara inställningarna.  

   ![Ange genomströmning för en samling](./media/set-throughput/set-throughput-for-container.png)

7. Om du vill uppdatera genomströmning för en befintlig behållare, expandera databasen och behållaren och klicka sedan på **inställningar**. Skriv det nya värdet för genomflöde i det nya fönstret, och markera sedan **spara**.  

   ![Uppdatera genomströmning för en samling](./media/set-throughput/update-throughput-for-container.png)

### <a name="provision-throughput-for-a-set-of-containers-or-at-the-database-level"></a>Etablera genomströmning för en uppsättning behållare eller på databasnivå

1. Logga in på [Azure Portal](https://portal.azure.com).  
2. Välj vänstra navigeringsfältet **alla resurser** och hitta Azure DB som Cosmos-konto.  
3. Du kan konfigurera dataflöde när du skapar en databas eller uppdatera genomströmning för en befintlig databas.  
4. Om du vill tilldela dataflöde när du skapar en databas, öppna den **Data Explorer** och välj **ny databas**  
5. Fyll i **databas-id** värde, kontrollera **etablera genomströmning** alternativ och konfigurera genomströmning värde. En databas kan etableras med minsta dataflöde värdet 50 000 RU/s.  

   ![Ange dataflöde med nytt databasalternativ](./media/set-throughput/set-throughput-with-new-database-option.png)

6. Om du vill uppdatera genomströmning för en befintlig databas, expandera databasen och behållaren och klicka sedan på **skala**. Skriv det nya värdet för genomflöde i det nya fönstret, och markera sedan **spara**.  

   ![Uppdatera genomströmning för en databas](./media/set-throughput/update-throughput-for-database.png)

### <a name="provision-throughput-for-a-set-of-containers-as-well-as-for-an-individual-container-in-a-database"></a>Etablera genomströmning för en uppsättning behållare också som en enskild behållare i en-databas

1. Logga in på [Azure Portal](https://portal.azure.com).  
2. Välj vänstra navigeringsfältet **alla resurser** och hitta Azure DB som Cosmos-konto.  
3. Skapa en databas och tilldelar den genomflöde. Öppna den **Data Explorer** och välj **ny databas**  
4. Fyll i **databas-id** värde, kontrollera **etablera genomströmning** alternativ och konfigurera genomströmning värde. En databas kan etableras med minsta dataflöde värdet 50 000 RU/s.  

   ![Ange dataflöde med nytt databasalternativ](./media/set-throughput/set-throughput-with-new-database-option.png)

5. Sedan skapa en samling i databasen som du skapade i senare steg. Högerklicka på databasen och välj om du vill skapa en samling **ny samling**.  

6. I den **lägga till samlingen** bladet, anger du ett namn för samlingen och partitions-nyckel. Du kan också du kan etablera genomströmning för specifika behållaren om du inte väljer att tilldela ett värde för genomflöde, genomströmning som tilldelats databasen delas i samlingen.  

   ![Om du vill ange dataflöde för behållaren](./media/set-throughput/optionally-set-throughput-for-the-container.png)

## <a name="considerations-when-provisioning-throughput"></a>Överväganden vid etablering av dataflöde

Nedan finns några överväganden som hjälper dig avgöra om en strategi för reservation genomflöde.

Överväg att etablera genomströmning på databasnivå (d.v.s. för behållare) i följande fall:

* Om du har 12 eller flera antal behållare som kan dela genomflöde i vissa eller alla.  

* När du migrerar från en enskild klient-databas som har utformats för att köras på IaaS-värd för virtuella datorer eller lokalt (till exempel NoSQL eller relationsdatabaser) till Azure Cosmos DB och ha många behållare.  

* Om du vill överväga oplanerad toppar i arbetsbelastningar med hjälp av grupperade genomströmning på databasnivå.  

* I stället för inställningen genomströmning på en enskild behållare och är du intresserad av sammanställda genomflöde i en behållare i databasen.

Överväg att etablera genomströmning vid en enskild behållare i följande fall:

* Om du har mindre antal Azure DB som Cosmos-behållare.  

* Om du vill hämta garanterad genomströmningen i en viss behållare som backas upp av SERVICENIVÅAVTAL.

## <a name="throughput-ranges"></a>Genomströmning intervall

I följande tabell visas genomströmning för behållare:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Enskild Partition behållare</strong></p></td>
            <td valign="top"><p><strong>Partitionerade behållare</strong></p></td>
            <td valign="top"><p><strong>Uppsättning behållare</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Minsta dataflöde</p></td>
            <td valign="top"><p>400 frågeenheter per sekund</p></td>
            <td valign="top"><p>1 000 frågeenheter per sekund</p></td>
            <td valign="top"><p>50 000 frågeenheter per sekund</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Maximalt dataflöde</p></td>
            <td valign="top"><p>10 000 frågeenheter per sekund</p></td>
            <td valign="top"><p>Obegränsat</p></td>
            <td valign="top"><p>Obegränsat</p></td>
        </tr>
    </tbody>
</table>

<a id="set-throughput-sdk"></a>

## <a name="set-throughput-by-using-sql-api-for-net"></a>Ange dataflöde med SQL API för .NET

Här är ett kodfragment för att skapa en behållare med 3 000 frågeenheter per sekund för en enskild behållare med SQL-API .NET SDK:

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

Här är ett kodfragment för etablering 100 000 enheter per sekund för begäran i en behållare med SQL-API .NET SDK:

```csharp
// Provision 100,000 RU/sec at the database level. 
// sharedCollection1 and sharedCollection2 will share the 100,000 RU/sec from the parent database
// dedicatedCollection will have its own dedicated 4,000 RU/sec, independant of the 100,000 RU/sec provisioned from the parent database
Database database = client.CreateDatabaseAsync(new Database { Id = "myDb" }, new RequestOptions { OfferThroughput = 100000 }).Result;

DocumentCollection sharedCollection1 = new DocumentCollection();
sharedCollection1.Id = "sharedCollection1";
sharedCollection1.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection1, new RequestOptions())

DocumentCollection sharedCollection2 = new DocumentCollection();
sharedCollection2.Id = "sharedCollection2";
sharedCollection2.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection2, new RequestOptions())

DocumentCollection dedicatedCollection = new DocumentCollection();
dedicatedCollection.Id = "dedicatedCollection";
dedicatedCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, dedicatedCollection, new RequestOptions { OfferThroughput = 4000 )
```

Azure Cosmos-DB fungerar på en modell för reservation för genomströmning. Det vill säga du debiteras mängden genomströmning *reserverade*, oavsett hur mycket av den genomströmningen är aktivt *används*. Som programmet har belastning, data och användning mönster ändring som du kan enkelt skala uppåt och nedåt antalet, som reserverats RUs via SDK eller med hjälp av den [Azure Portal](https://portal.azure.com).

Varje behållare eller uppsättning behållare, mappas till en `Offer` resurs i Azure Cosmos DB som innehåller metadata om etablerat dataflöde. Du kan ändra det allokerade genomflödet genom att leta upp motsvarande erbjudande resurs för en behållare och sedan uppdateras med det nya värdet för genomströmning. Här är ett kodfragment för att ändra genomflödet av en behållare till 5 000 frågeenheter per andra med .NET SDK:

```csharp
// Fetch the resource to be updated
// For a updating throughput for a set of containers, replace the collection's self link with the database's self link
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

Det finns ingen inverkan på tillgängligheten för din behållare eller uppsättning behållare, när du ändrar genomflödet. Nya reserverat dataflöde är vanligtvis effektiva i sekunder för tillämpning av nya genomflöde.

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Ange genomflödet med hjälp av SQL-API för Java

Följande kodavsnitt hämtar det aktuella genomflödet och ändrar till 500 RU/s. En fullständig kodexemplet finns i [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) fil på GitHub. 

```Java
// find offer associated with this collection
// To change the throughput for a set of containers, use the database's resource id instead of the collection's resource id
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 500;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a id="GetLastRequestStatistics"></a>Hämta genomströmning genom att använda MongoDB API GetLastRequestStatistics-kommando

MongoDB-API: et stöder ett anpassat kommando *getLastRequestStatistics*, för att hämta begäran avgifterna för en viss åtgärd.

Till exempel köra åtgärden som du vill kontrollera begäran kostnad för i Mongo-gränssnittet.
```
> db.sample.find()
```

Kör sedan kommandot *getLastRequestStatistics*.
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

En metod för att uppskatta mängden reserverat dataflöde som krävs för programmet är att registrera begäran enhet tillägget som är associerade med vanliga åtgärder som körs mot ett representativt objekt som används av ditt program och uppskatta antalet åtgärder som du vill utföra varje sekund.

> [!NOTE]
> Om du har objekttyper som varierar kraftigt när det gäller storlek och antalet indexerade egenskaper som är associerade med varje tillämplig åtgärden begäran enhet kostnad registrera *typen* för vanliga objekt.
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>Hämta dataflöde med hjälp av portalen MongoDB API-mått

Det enklaste sättet att hämta en bra uppskattning av begäran enhet avgifter för MongoDB-API-databas är att använda den [Azure-portalen](https://portal.azure.com) mått. Med den *antal begäranden* och *begäran kostnad* diagram, kan du få en uppskattning av hur många begäran enheter varje åtgärden förbrukar och hur många enheter som begäran som de använder i förhållande till varandra.

![Portalen MongoDB API-mått][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> Reserverat dataflöde överskreds i MongoDB-API
Program som överskrider det tillhandahållna dataflödet för en behållare eller en uppsättning behållare kommer att vara begränsad hastighet förrän åtgången sjunker under dataflöde hastighet. När en hastighet-begränsning inträffar serverdelen avslutas förfrågan med en `16500` felkoden - `Too Many Requests`. Som standard MongoDB-API automatiskt försöker upp till 10 gånger innan det returneras en `Too Many Requests` felkoden. Om du tar emot många `Too Many Requests` felkoder du bör du överväga att lägga till en logik i ditt program felhantering rutiner eller [öka etablerat dataflöde för behållaren](set-throughput.md).

## <a name="throughput-faq"></a>Genomströmning vanliga frågor och svar

**Kan jag in min genomströmning till mindre än 400 RU/s?**

400 RU/s är minsta dataflöde på Cosmos DB enskild partition behållare (1000 RU/s är minst för partitionerade behållare). Begära enheter anges i intervall om 100 RU/s men dataflöde kan inte anges till 100 RU/s eller ett värde som är mindre än 400 RU/s. Om du letar efter ett kostnadseffektivt sätt att utveckla och testa Cosmos DB, kan du använda den kostnadsfria [Azure Cosmos DB emulatorn](local-emulator.md), som du kan distribuera lokalt utan kostnad. 

**Hur ställer jag dataflöde med MongoDB-API**

Det finns inget MongoDB-API-tillägg för att ange genomflöde. Rekommendationen är att använda SQL-API som visas i [ange genomflödet med hjälp av SQL-API för .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Nästa steg

* Läs om hur du uppskattar genomflöde och begäran enheter i [begära enheter & uppskattning genomflöde i Azure Cosmos DB](request-units.md)

* Läs mer om etablering och pågående planeten skalning med Cosmos-DB i [partitionering och skalning med Cosmos DB](partition-data.md).

[1]: ./media/set-throughput/api-for-mongodb-metrics.png
