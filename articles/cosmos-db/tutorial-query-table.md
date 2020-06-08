---
title: Hur frågar man efter tabelldata SQL i Azure Cosmos DB?
description: Lär dig hur du frågar data som lagras i Azure Cosmos DB Tabell-API-kontot med hjälp av OData-filter och LINQ-frågor
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 06/05/2020
ms.reviewer: sngun
ms.openlocfilehash: 14a6d2b448bb943356ae1738c3d53d9c6fee1a98
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2020
ms.locfileid: "84484677"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-table-api"></a>Självstudie: Fråga Azure Cosmos DB med hjälp av tabell-API

Azure Cosmos DB [Table-API](table-introduction.md) stöder OData- och [LINQ](https://docs.microsoft.com/rest/api/storageservices/fileservices/writing-linq-queries-against-the-table-service)-frågor mot nyckel-/värdedata (tabell).  

Den här artikeln beskriver följande uppgifter:

> [!div class="checklist"]
> * Fråga data med tabell-API

Frågorna i den här artikeln använder följande `People`-exempeltabell:

| PartitionKey | RowKey | E-post | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0101 |
| Smith | Ben | Ben@contoso.com| 425-555-0102 |
| Smith | Jeff | Jeff@contoso.com| 425-555-0104 |

I [Fråga tabeller och entiteter](https://docs.microsoft.com/rest/api/storageservices/fileservices/querying-tables-and-entities) finns mer information om hur du frågar med tabell-API.

Mer information om de förstklassiga funktioner som Azure Cosmos DB erbjuder finns i [Azure Cosmos DB Table API](table-introduction.md) och [Utveckla med tabell-API:t i .NET](tutorial-develop-table-dotnet.md).

## <a name="prerequisites"></a>Krav

För att de här frågorna ska fungera måste du ha ett konto i Azure Cosmos DB och ha entitetsdata i containern. Har du detta? Slutför [snabbstarten på fem minuter](create-table-dotnet.md) eller [självstudien för utvecklare](tutorial-develop-table-dotnet.md) om du behöver skapa ett konto och fylla i databasen.

## <a name="query-on-partitionkey-and-rowkey"></a>Fråga om PartitionKey och RowKey

Eftersom egenskaperna PartitionKey och RowKey utformar en entitets primärnyckel kan du använda följande specialsyntax för att identifiera entiteten:

**Söka i data**

```
https://<mytableendpoint>/People(PartitionKey='Harp',RowKey='Walter')  
```

**Gör**

| PartitionKey | RowKey | E-post | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0104 |

Du kan också ange egenskaperna som en del av alternativet `$filter`, som du ser i följande avsnitt. Lägg märke till att nyckelns egenskapsnamn och konstanta värden är skiftlägeskänsliga. Både egenskaperna PartitionKey och RowKey är av typen Sträng.

## <a name="query-by-using-an-odata-filter"></a>Fråga med hjälp av ett OData-filter

När du skapar en filtersträng ska du tänka på följande regler:

* Använd de logiska operatorerna som definieras av OData-protokollspecifikationen för att jämföra en egenskap med ett värde. Observera att det inte går att jämföra en egenskap med ett dynamiskt värde. En sida av uttrycket måste vara en konstant.
* Egenskapsnamnet, operatorn och det konstanta värdet måste separeras av URL-kodade blanksteg. Ett blanksteg är URL-kodat som `%20`.
* Alla delar av filtersträngen är skiftlägeskänsliga.
* Det konstanta värdet måste vara av samma datatyp som egenskapen för filtret för att returnera giltiga resultat. Mer information om egenskapstyper som stöds finns i [Förstå Tabelltjänst-datamodellen](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model).

Här är en exempelfråga som visar hur du filtrerar via PartitionKey och e-postegenskaperna med ett OData-`$filter`.

**Söka i data**

```
https://<mytableapi-endpoint>/People()?$filter=PartitionKey%20eq%20'Smith'%20and%20Email%20eq%20'Ben@contoso.com'
```

Mer information om hur du skapar filteruttryck för olika datatyper finns i [Fråga tabeller och entiteter](https://docs.microsoft.com/rest/api/storageservices/querying-tables-and-entities).

**Gör**

| PartitionKey | RowKey | E-post | PhoneNumber |
| --- | --- | --- | --- |
| Smith |Ben | Ben@contoso.com| 425-555-0102 |

Frågorna på datetime-egenskaper returnerar inte några data när de körs i Azure Cosmos DB Tabell-API. Medan Azure Table Storage lagrar datum värden med tids kornig het för Tick, använder Tabell-API i Azure Cosmos DB `_ts` egenskapen. `_ts`Egenskapen är en andra nivå av granularitet, vilket inte är ett OData-filter. I så fall blockeras frågorna på egenskaperna för tidsstämpeln av Azure Cosmos DB. Som en lösning kan du definiera en anpassad egenskap för data typen datetime eller Long och ange datumvärdet från klienten.

## <a name="query-by-using-linq"></a>Fråga med hjälp av LINQ 
Du kan också fråga med hjälp av LINQ, som översätter till motsvarande OData-frågeuttryck. Här är ett exempel på hur du skapar frågor med .NET SDK:

```csharp
IQueryable<CustomerEntity> linqQuery = table.CreateQuery<CustomerEntity>()
            .Where(x => x.PartitionKey == "4")
            .Select(x => new CustomerEntity() { PartitionKey = x.PartitionKey, RowKey = x.RowKey, Email = x.Email });
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Lär dig att fråga med tabell-API

Du kan nu fortsätta till nästa självstudie för att lära dig hur du distribuerar dina data globalt.

> [!div class="nextstepaction"]
> [Distribuera dina data globalt](tutorial-global-distribution-table.md)
