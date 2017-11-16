---
title: "Hur man frågar tabelldata i Azure Cosmos DB? | Microsoft Docs"
description: "Lär dig att fråga tabelldata i Azure Cosmos DB"
services: cosmos-db
documentationcenter: 
author: kanshiG
manager: jhubbard
editor: 
tags: 
ms.assetid: 14bcb94e-583c-46f7-9ea8-db010eb2ab43
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 11/15/2017
ms.author: govindk
ms.openlocfilehash: cbb752692fbd618d9e7e14c8a80b582aad657b38
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="azure-cosmos-db-how-to-query-table-data-by-using-the-table-api"></a>Azure Cosmos DB: Hur man frågar tabelldata med tabell-API

Azure Cosmos DB [tabell API](table-introduction.md) stöder OData och [LINQ](https://docs.microsoft.com/rest/api/storageservices/fileservices/writing-linq-queries-against-the-table-service) frågor mot nyckel/värde-(tabell) data.  

Den här artikeln omfattar följande aktiviteter: 

> [!div class="checklist"]
> * Datafrågor med tabell-API

Frågorna i den här artikeln använder i följande exempel `People` tabell:

| PartitionKey | RowKey | E-post | Telefonnummer |
| --- | --- | --- | --- |
| Harp | Viktor | Walter@contoso.com| 425-555-0101 |
| Smith | Ben | Ben@contoso.com| 425-555-0102 |
| Smith | Jeff | Jeff@contoso.com| 425-555-0104 | 

Se [frågor till tabeller och enheter] (https://docs.microsoft.com/rest/api/storageservices/fileservices/querying-tables-and-entities) mer information om hur frågan med tabell-API. 

Mer information om premium-funktioner som erbjuder Azure Cosmos DB finns [Azure Cosmos DB tabell API](table-introduction.md) och [utveckla med tabell-API: et i .NET](tutorial-develop-table-dotnet.md). 

## <a name="prerequisites"></a>Krav

För de här frågorna ska fungera måste du har ett konto i Azure Cosmos DB och har entitetsdata i behållaren. Har inte något av de? Slutför den [fem minuter långa quickstart](create-table-dotnet.md) eller [developer kursen](tutorial-develop-table-dotnet.md) och skapa ett konto som du kan fylla i databasen.

## <a name="query-on-partitionkey-and-rowkey"></a>Fråga om PartitionKey och RowKey
Du kan använda följande särskild syntax för att identifiera enheten eftersom egenskaperna PartitionKey och RowKey formuläret en entitets primärnyckel: 

**Fråga**

```
https://<mytableendpoint>/People(PartitionKey='Harp',RowKey='Walter')  
```
**Resultat**

| PartitionKey | RowKey | E-post | Telefonnummer |
| --- | --- | --- | --- |
| Harp | Viktor | Walter@contoso.com| 425-555-0104 |

Du kan också ange dessa egenskaper som en del av den `$filter` alternativ, som visas i följande avsnitt. Observera att nyckelegenskapen namn och konstanta värden är skiftlägeskänsliga. Både PartitionKey och RowKey är av typen String. 

## <a name="query-by-using-an-odata-filter"></a>Fråga med hjälp av en OData-filter
Tänk reglerna när du konstruera en Filtersträng: 

* Använd logiska operatorer som definieras i specifikationen för OData-protokollet för att jämföra en egenskap till ett värde. Observera att du kan jämföra en egenskap för ett dynamiskt värde. En sida av uttrycket måste vara en konstant. 
* Egenskapsnamn, operatör och konstantvärde måste avgränsas med URL-kodade blanksteg. Ett utrymme är URL-kodade som `%20`. 
* Alla delar av filtersträngen är skiftlägeskänsliga. 
* Det konstanta värdet måste vara av samma datatyp som egenskapen för filtret giltiga sökresultat. Läs mer om stöds egenskapstyperna [förstå den tabelltjänst-datamodellen](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). 

Här är en exempelfråga som visar hur du kan filtrera efter egenskaperna PartitionKey och e-post med hjälp av en OData `$filter`.

**Fråga**

```
https://<mytableapi-endpoint>/People()?$filter=PartitionKey%20eq%20'Smith'%20and%20Email%20eq%20'Ben@contoso.com'
```

Mer information om hur du konstruerar filteruttryck för olika datatyper finns [frågor till tabeller och de entiteter](https://docs.microsoft.com/rest/api/storageservices/querying-tables-and-entities).

**Resultat**

| PartitionKey | RowKey | E-post | Telefonnummer |
| --- | --- | --- | --- |
| Ben |Smith | Ben@contoso.com| 425-555-0102 |

## <a name="query-by-using-linq"></a>Fråga med hjälp av LINQ 
Du kan också fråga med hjälp av LINQ som översätts till motsvarande OData-frågeuttryck. Här är ett exempel på hur du skapar frågor med hjälp av .NET-SDK:

```csharp
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("people");

TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
    .Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition(PartitionKey, QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition(Email, QueryComparisons.Equal,"Ben@contoso.com")
    ));

await table.ExecuteQuerySegmentedAsync<CustomerEntity>(query, null);
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen kommer du har gjort följande:

> [!div class="checklist"]
> * Lärt dig hur man frågan med tabell-API

Du kan nu fortsätta till nästa kurs att lära dig hur du distribuerar dina data globalt.

> [!div class="nextstepaction"]
> [Distribuera dina data globalt](tutorial-global-distribution-table.md)
