---
title: Etablera dataflöde för Azure Cosmos DB | Microsoft Docs
description: Lär dig hur du ställer in dataflöde för Azure Cosmos DB containsers, samlingar, diagram och tabeller.
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: andrl
ms.openlocfilehash: 2da00f700f5cc234455cc686377e5863f1c35bdd
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734479"
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers-and-database"></a>Ange och hämta dataflöde för Azure Cosmos DB-behållare och databasen

Du kan ange dataflödet för en Azure Cosmos DB-behållare eller en uppsättning behållare med hjälp av Azure-portalen eller med hjälp av klienten SDK: er. 

**Etablera dataflöde för en enskild behållare:** när du etablerar dataflödet för en uppsättning behållare måste alla de här behållarna dela det etablerade dataflödet. Etablering genomströmning för enskilda behållare garanterar att reservationen av dataflöde för den specifika behållaren. När du tilldelar RU/sek på enskilda behållarenivån, behållarna som kan skapas som *fast* eller *obegränsad*. Containrar med fast storlek har en maxgräns på 10 GB och en genomströmning på 10 000 RU/s. Om du vill skapa en obegränsad behållare, måste du ange ett minsta dataflöde på 1 000 RU/s och en [partitionsnyckel](partition-data.md). Eftersom dina data kan behöva delas upp på flera partitioner, är det nödvändigt att välja en partitionsnyckel som har en hög kardinalitet (100 till miljontals distinkta värden). Genom att välja en partitionsnyckel med många distinkta värden kan du se till att dina behållare/tabell/diagram och begäranden kan skalas enhetligt av Azure Cosmos DB. 

**Etablera dataflöde för en uppsättning behållare eller en databas:** etablering dataflödet för en databas kan du dela dataflödet mellan alla behållare som hör till den här databasen. Du kan ha en uppsättning behållare som delar dataflöde samt behållare som kan ha dedikerade dataflöde i en Azure Cosmos DB-databas. När du tilldelar RU/sek i en behållare, behållare som hör till den här uppsättningen behandlas som *obegränsad* behållare och måste ange en partitionsnyckel.

Utifrån dataflöden, allokerar Azure Cosmos DB fysiska partitioner som värd för dina behållare och delar upp/rebalances data över partitioner som den växer. Behållare och nivån dataflöde Databasetableringen är två separata erbjudanden och växla mellan något av dessa kräver att migrera data från källa till mål. Vilket innebär att du behöver skapa en ny databas eller en ny samling och sedan migrera data med hjälp av [bulk executor biblioteket](bulk-executor-overview.md) eller [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). Följande bild illustrerar etablering dataflöde på olika nivåer:

![Etablera enheter för programbegäran för enskilda behållare och uppsättning behållare](./media/request-units/provisioning_set_containers.png)

I nästa avsnitt kommer du lära dig de steg som krävs för att konfigurera dataflöde på olika nivåer för ett Azure Cosmos DB-konto. 

## <a name="provision-throughput-by-using-azure-portal"></a>Etablera dataflöde med hjälp av Azure-portalen

### <a name="provision-throughput-for-a-container-collectiongraphtable"></a>Etablera dataflöde för en behållare (samling/diagram/tabell)

1. Logga in på [Azure Portal](https://portal.azure.com).  
2. I det vänstra navigeringsfältet väljer **alla resurser** och hitta ditt Azure Cosmos DB-konto.  
3. Du kan konfigurera dataflöde när du skapar en behållare (samling, diagram, tabell) eller uppdatera genomströmning för en befintlig behållare.  
4. Om du vill tilldela dataflöde när du skapar en behållare, öppna den **Datautforskaren** bladet och välj **ny samling** (ny graf, ny tabell för API: er)  
5. Fyll i formuläret **Lägg till samling** bladet. Fält i det här bladet beskrivs i följande tabell:  

   |**Inställning**  |**Beskrivning**  |
   |---------|---------|
   |Databas-id  |  Ange ett unikt namn som identifierar din databas. Databasen är en logisk behållare för en eller flera samlingar. Databasnamn måste innehålla mellan 1 och 255 tecken och får inte innehålla /, \\, #, ? eller avslutande blanksteg. |
   |Samlings-id  | Ange ett unikt namn som identifierar din samling. Samma teckenkrav gäller för samlings-ID:n som databasnamn. |
   |Lagringskapacitet   | Det här värdet representerar lagringskapaciteten för databasen. När du etablerar genomströmning för en enskild samling lagringskapacitet kan vara **fast (10 GB)** eller **obegränsad**. Obegränsade lagringskapacitet måste du ange en partitionsnyckel för dina data.  |
   |Dataflöde   | Varje samling och databas kan ha dataflöde i enheter för programbegäran per sekund.  Fast lagringskapacitet, minsta dataflödet är 400 begäransenheter per sekund (RU/s), för obegränsad lagring kapacitet, minsta dataflöde är inställt på 1000 RU/s.|

6. När du har angett värden för dessa fält, Välj **OK** att spara inställningarna.  

   ![Ange dataflöde för en samling](./media/set-throughput/set-throughput-for-container.png)

7. Expandera databasen och behållaren för att uppdatera genomströmning för en befintlig behållare, och klicka sedan på **inställningar**. I det nya fönstret, skriver du det nya värdet för dataflöde och välj sedan **spara**.  

   ![Uppdatera dataflödet för en samling](./media/set-throughput/update-throughput-for-container.png)

### <a name="provision-throughput-for-a-set-of-containers-or-at-the-database-level"></a>Etablera dataflöde för en uppsättning behållare eller på databasnivå

1. Logga in på [Azure Portal](https://portal.azure.com).  
2. I det vänstra navigeringsfältet väljer **alla resurser** och hitta ditt Azure Cosmos DB-konto.  
3. Du kan konfigurera dataflöde när du skapar en databas eller uppdatera genomströmning för en befintlig databas.  
4. Om du vill tilldela dataflöde när du skapar en databas, öppna den **Datautforskaren** bladet och välj **ny databas**  
5. Fyll i **databas-id** värde, kontrollera **etablera dataflöde** , och konfigurera dataflödesvärde gjorts.  

   ![Ange dataflöde med nytt databasalternativ](./media/set-throughput/set-throughput-with-new-database-option.png)

6. Expandera databasen och behållaren för att uppdatera dataflödet för en befintlig databas, och klicka sedan på **skala**. I det nya fönstret, skriver du det nya värdet för dataflöde och välj sedan **spara**.  

   ![Uppdatera dataflödet för en databas](./media/set-throughput/update-throughput-for-database.png)

### <a name="provision-throughput-for-a-set-of-containers-as-well-as-for-an-individual-container-in-a-database"></a>Etablera dataflöde för en uppsättning behållare också som en enskild behållare i en databas

1. Logga in på [Azure Portal](https://portal.azure.com).  
2. I det vänstra navigeringsfältet väljer **alla resurser** och hitta ditt Azure Cosmos DB-konto.  
3. Skapa en databas och tilldela det dataflöde. Öppna den **Datautforskaren** bladet och välj **ny databas**  
4. Fyll i **databas-id** värde, kontrollera **etablera dataflöde** , och konfigurera dataflödesvärde gjorts.  

   ![Ange dataflöde med nytt databasalternativ](./media/set-throughput/set-throughput-with-new-database-option.png)

5. Därefter skapa en samling i den databas du skapade i steget ovan. Om du vill skapa en samling, högerklicka på databasen Välj **ny samling**.  

6. I den **Lägg till samling** bladet anger du ett namn för samlingen och partitionsnyckel. Du kan också du kan etablera dataflöde för den specifika behållaren om du väljer att inte tilldela ett dataflöde värde, dataflöde som tilldelats databasen delas i samlingen.  

   ![Du kan också ange dataflöde för behållaren](./media/set-throughput/optionally-set-throughput-for-the-container.png)

## <a name="considerations-when-provisioning-throughput"></a>Överväganden vid etablering av dataflöde

Nedan finns några överväganden som hjälper dig att avgöra om en strategi för reservation av dataflöde.

### <a name="considerations-when-provisioning-throughput-at-the-database-level"></a>Överväganden vid etablering av dataflöde på databasnivå

Överväg att etablera dataflöde på databasnivå (det vill säga för en uppsättning behållare) i följande fall:

* Om du har ett dussin eller flera antal behållare som kan dela dataflöde i vissa eller alla.  

* När du migrerar från en enda klient-databas som har utformats för att köras på IaaS-värd virtuella datorer eller lokalt (till exempel NoSQL eller relationsdatabaser) till Azure Cosmos DB och ha många behållare.  

* Om du vill överväga oplanerad toppar i arbetsbelastningar med hjälp av pooler dataflöde på databasnivå.  

* I stället för att inställningen dataflöde på en enskild behållare är du intresserad av den totala genomströmningen i en behållare i databasen.

### <a name="considerations-when-provisioning-throughput-at-the-container-level"></a>Överväganden vid etablering av dataflöde på behållarenivån

Överväg att etablera dataflöde på en enskild behållare i följande fall:

* Om du har mindre antal Azure Cosmos DB-behållare.  

* Om du vill få garanterat dataflöde i en given behållare som backas upp av ett serviceavtal (SLA).

<a id="set-throughput-sdk"></a>

## <a name="set-throughput-by-using-sql-api-for-net"></a>Ange dataflöde med hjälp av SQL-API för .NET

### <a name="set-throughput-at-the-container-level"></a>Ange dataflöde på behållarenivån
Här är ett kodfragment för att skapa en behållare med 3 000 programbegäran per sekund för en enskild behållare med SQL API .NET SDK:

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

### <a name="set-throughput-for-a-set-of-containers-at-the-database-level"></a>Ställa in dataflöde för en uppsättning behållare på databasnivå

Här är ett kodfragment för etablering 100 000 enheter för programbegäran per sekund i en behållare med hjälp av SQL-API .NET SDK:

```csharp
// Provision 100,000 RU/sec at the database level. 
// sharedCollection1 and sharedCollection2 will share the 100,000 RU/sec from the parent database
// dedicatedCollection will have its own dedicated 4,000 RU/sec, independant of the 100,000 RU/sec provisioned from the parent database
Database database = await client.CreateDatabaseAsync(new Database { Id = "myDb" }, new RequestOptions { OfferThroughput = 100000 });

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

Azure Cosmos DB körs på en reservation modell för dataflöde. Det vill säga du faktureras för den dataflöde *reserverade*, oavsett hur mycket av att dataflödet är aktivt *används*. Som ditt program är belastningen, data och användning användningsmönster ändras kan du enkelt skala upp och ned antalet, som reserverade ru: er via SDK: er eller via den [Azure-portalen](https://portal.azure.com).

Varje behållare eller uppsättning behållare, mappas till en `Offer` resurs i Azure Cosmos DB, som innehåller metadata om det etablerade dataflödet. Du kan ändra det tilldelade dataflödet genom att leta upp motsvarande resurs-erbjudande för en behållare och sedan uppdatera den med det nya värdet för dataflöde. Här är ett kodfragment för att ändra dataflödet för en behållare till 5 000 programbegäran per sekund med hjälp av .NET SDK. Du bör uppdatera alla befintliga Azure portal fönster för den ändrade dataflöde visas när du har ändrat dataflödet. 

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

Det finns ingen inverkan på tillgängligheten för din behållare eller uppsättning behållare, när du ändrar dataflödet. Ny reserverat dataflöde är vanligtvis effektiva inom några sekunder på tillämpningen av det nya dataflödet.

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Ange dataflöden med hjälp av SQL-API för Java

Följande kodavsnitt hämtar det aktuella dataflödet och ändrar till 500 RU/s. Ett komplett kodexempel, finns det [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) fil på GitHub. 

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

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>Beräkna dataflöde med hjälp av portalen MongoDB API-mått

Det enklaste sättet att få en bra uppskattning av begäran units för din MongoDB API-databas är att använda den [Azure-portalen](https://portal.azure.com) mått. Med den *antal begäranden* och *kostnad för begäran* diagram, kan du få en uppskattning av hur många enheter för programbegäran varje åtgärd förbrukar och hur många enheter för programbegäran som de använder i förhållande till varandra.

![Portalen MongoDB API-mått][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> Reserverat dataflöde överskreds i MongoDB-API
Program som överstiger det etablerade dataflödet för en behållare eller en uppsättning behållare att rate-limited tills åtgången sjunker under det etablerade dataflödet. När en rate-begränsning inträffar serverdelen går ut på begäran med en `16500` felkod - `Too Many Requests`. Som standard av MongoDB API automatiskt försöker upp till 10 gånger innan det returneras en `Too Many Requests` felkoden. Om du får många `Too Many Requests` felkoder, kan du överväga att lägga till logik för omprövning i ditt programs rutiner för felhantering eller [ökar det etablerade dataflödet för behållaren](set-throughput.md).

## <a id="GetLastRequestStatistics"></a>Hämta kostnad för begäran med hjälp av MongoDB API GetLastRequestStatistics kommando

MongoDB-API: et stöder ett anpassat kommando *getLastRequestStatistics*, för att hämta begäran om avgifterna för en viss åtgärd.

Till exempel köra åtgärden som du vill kontrollera begäran kostar i Mongo-gränssnittet.
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

En metod för att uppskatta hur mycket reserverat dataflöde som krävs för programmet är att registrera begäran kostnaden för enheten förknippade med vanliga åtgärder mot ett representativt objekt som används av ditt program och sedan beräkna antalet åtgärder som du räknar om du vill utföra varje sekund.

> [!NOTE]
> Om du har objekttyper som skiljer sig avsevärt beroende på storleken och antalet indexerade egenskaper kan sedan registrera gäller åtgärden begäran kostnaden för den enhet som hör till var *typ* för vanliga objekt.
> 
> 

## <a name="throughput-faq"></a>Dataflöde vanliga frågor och svar

**Kan jag ställa in min dataflöde på mindre än 400 RU/s?**

400 RU/s är minsta dataflödet på enskild partition Cosmos DB-behållare (1000 RU/s är minimum för en partitionerad behållare). Begära enheter anges med 100 RU/s intervall, men dataflöde kan inte ställas in på 100 RU/s eller ett värde som är mindre än 400 RU/s. Om du letar efter en kostnadseffektiv metod för att utveckla och testa Cosmos DB kan du använda den kostnadsfria [Azure Cosmos DB-emulatorn](local-emulator.md), vilka du kan distribuera lokalt utan kostnad. 

**Hur ställer jag in dataflöde med hjälp av MongoDB-API?**

Det finns inga MongoDB API-tillägg för att ställa in dataflöde. Rekommendationen är att använda SQL-API som visas i [ange dataflödet med hjälp av SQL-API för .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Nästa steg

* Läs om hur du uppskattar dataflöde och begäransenheter i [begära och uppskattning dataflöde i Azure Cosmos DB](request-units.md)

* Mer information om att etablera eller gå global skala med Cosmos DB finns [partitionering och skalning med Cosmos DB](partition-data.md).

[1]: ./media/set-throughput/api-for-mongodb-metrics.png
