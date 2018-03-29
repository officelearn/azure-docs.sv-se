---
title: Etablera genomströmning för Azure Cosmos DB | Microsoft Docs
description: Lär dig hur du ställer in etablerat dataflöde för din Azure Cosmos DB containsers, samlingar, diagram och tabeller.
services: cosmos-db
author: mimig1
manager: jhubbard
editor: ''
documentationcenter: ''
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: mimig
ms.openlocfilehash: b9ad94276287a5e46a9d3b3b9332547fba5627e9
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="set-throughput-for-azure-cosmos-db-containers"></a>Ange dataflöde för Azure DB som Cosmos-behållare

Du kan ange dataflöde för din Azure DB som Cosmos-behållare i Azure-portalen eller genom att använda klient-SDK: er. 

I följande tabell visas genomströmning för behållare:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Enskild Partition behållare</strong></p></td>
            <td valign="top"><p><strong>Partitionerade behållare</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Minsta dataflöde</p></td>
            <td valign="top"><p>400 frågeenheter per sekund</p></td>
            <td valign="top"><p>1000 frågeenheter per sekund</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Maximalt dataflöde</p></td>
            <td valign="top"><p>10 000 frågeenheter per sekund</p></td>
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

## <a name="throughput-faq"></a>Genomströmning vanliga frågor och svar

**Kan jag in min genomströmning till mindre än 400 RU/s?**

400 RU/s är minsta dataflöde på Cosmos DB enskild partition behållare (1000 RU/s är minst för partitionerade behållare). Begära enheter anges i intervall om 100 RU/s men dataflöde kan inte anges till 100 RU/s eller ett värde som är mindre än 400 RU/s. Om du letar efter ett kostnadseffektivt sätt att utveckla och testa Cosmos DB, kan du använda den kostnadsfria [Azure Cosmos DB emulatorn](local-emulator.md), som du kan distribuera lokalt utan kostnad. 

**Hur ställer jag througput med MongoDB-API**

Det finns inget MongoDB-API-tillägg för att ange genomflöde. Rekommendationen är att använda SQL-API som visas i [ange genomflödet med hjälp av SQL-API för .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Nästa steg

Läs mer om etablering och pågående planeten skalning med Cosmos-DB i [partitionering och skalning med Cosmos DB](partition-data.md).
