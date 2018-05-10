---
title: Etablera genomströmning för Azure Cosmos DB | Microsoft Docs
description: Lär dig hur du ställer in etablerat dataflöde för din Azure Cosmos DB containsers, samlingar, diagram och tabeller.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: sngun
ms.openlocfilehash: bede91ed3ffc456740a0eb63ed7a15278e99ebe2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers"></a>Ange och få genomströmning för Azure DB som Cosmos-behållare

Du kan ange dataflöde för Azure DB som Cosmos-behållare eller en uppsättning av behållare i Azure-portalen eller genom att använda klient-SDK: er. 

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

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Ange genomflödet med hjälp av Azure portal

1. Öppna i ett nytt fönster i [Azure-portalen](https://portal.azure.com).
2. Klicka på fältet till vänster **Azure Cosmos DB**, eller klicka på **alla tjänster** längst ned, bläddrar sedan till **databaser**, och klicka sedan på **Azure Cosmos DB**.
3. Välj Cosmos-DB-konto.
4. I det nya fönstret, klickar du på **Data Explorer** på navigeringsmenyn.
5. Expandera din databas och en behållare i det nya fönstret, och klicka sedan på **skala & inställningar**.
6. I det nya fönstret, skriver du det nya värdet genomflöde i den **genomströmning** rutan och klicka på **spara**.

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-net"></a>Ange genomflödet med hjälp av SQL-API för .NET

Följande kodavsnitt hämtar det aktuella genomflödet och ändrar till 500 RU/s. Fullständig kodexemplet finns i [CollectionManagement](https://github.com/Azure/azure-documentdb-dotnet/blob/95521ff51ade486bb899d6913880995beaff58ce/samples/code-samples/CollectionManagement/Program.cs#L188-L216) projektet på GitHub.

```csharp
// Fetch the offer of the collection whose throughput needs to be updated
// To change the throughput for a set of containers, use the database's selflink instead of the collection's selflink
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 500 request units per second
offer = new OfferV2(offer, 500);

// Now persist these changes to the collection by replacing the original offer resource
await client.ReplaceOfferAsync(offer);
```

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

Med detta i åtanke är en metod för att uppskatta mängden reserverat dataflöde som krävs för programmet att registrera begäran enhet tillägget som är associerade med vanliga åtgärder som körs mot ett representativt objekt som används av ditt program och sedan beräkna den antal åtgärder som du vill utföra varje sekund.

> [!NOTE]
> Om du har objekttyper som varierar kraftigt när det gäller storlek och antalet indexerade egenskaper som är associerade med varje tillämplig åtgärden begäran enhet kostnad registrera *typen* för vanliga objekt.
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>Hämta dataflöde med hjälp av portalen MongoDB API-mått

Det enklaste sättet att hämta en bra uppskattning av begäran enhet avgifter för MongoDB-API-databas är att använda den [Azure-portalen](https://portal.azure.com) mått. Med den *antal begäranden* och *begäran kostnad* diagram, kan du få en uppskattning av hur många begäran enheter varje åtgärden förbrukar och hur många enheter som begäran som de använder i förhållande till varandra.

![Portalen MongoDB API-mått][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> Reserverat dataflöde överskreds i MongoDB-API
Program som överskrider det tillhandahållna dataflödet för en behållare eller en uppsättning behållare kommer att vara begränsad hastighet förrän åtgången sjunker under dataflöde hastighet. När en hastighet-begränsning inträffar serverdelen förebyggande syfte avslutas förfrågan med en `16500` felkoden - `Too Many Requests`. Som standard MongoDB-API automatiskt försöker upp till 10 gånger innan det returneras en `Too Many Requests` felkoden. Om du tar emot många `Too Many Requests` felkoder du bör du överväga att lägga till en logik i ditt program felhantering rutiner eller [öka etablerat dataflöde för behållaren](set-throughput.md).

## <a name="throughput-faq"></a>Genomströmning vanliga frågor och svar

**Kan jag in min genomströmning till mindre än 400 RU/s?**

400 RU/s är minsta dataflöde på Cosmos DB enskild partition behållare (1000 RU/s är minst för partitionerade behållare). Begära enheter anges i intervall om 100 RU/s men dataflöde kan inte anges till 100 RU/s eller ett värde som är mindre än 400 RU/s. Om du letar efter ett kostnadseffektivt sätt att utveckla och testa Cosmos DB, kan du använda den kostnadsfria [Azure Cosmos DB emulatorn](local-emulator.md), som du kan distribuera lokalt utan kostnad. 

**Hur ställer jag dataflöde med MongoDB-API**

Det finns inget MongoDB-API-tillägg för att ange genomflöde. Rekommendationen är att använda SQL-API som visas i [ange genomflödet med hjälp av SQL-API för .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Nästa steg

Läs mer om etablering och pågående planeten skalning med Cosmos-DB i [partitionering och skalning med Cosmos DB](partition-data.md).

[1]: ./media/set-throughput/api-for-mongodb-metrics.png