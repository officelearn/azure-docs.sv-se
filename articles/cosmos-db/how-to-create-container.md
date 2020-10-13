---
title: Skapa en container i Azure Cosmos DB
description: 'Lär dig hur du skapar en behållare i Azure Cosmos DB genom att använda Azure Portal, .NET, Java, python, Node.js och andra SDK: er.'
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/29/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 2362326bccd90af997aa9237ec5f14e39ae62c85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020005"
---
# <a name="create-an-azure-cosmos-container"></a>Skapa en Azure Cosmos-container

I den här artikeln beskrivs olika sätt att skapa en Azure Cosmos-behållare (samling, tabell eller graf) med hjälp av Azure Portal, Azure CLI, PowerShell eller SDK: er som stöds. Den här artikeln visar hur du skapar en container, anger partitionsnyckeln och etablerar dataflöde.

> [!NOTE]
> När du skapar behållare ser du till att du inte skapar två behållare med samma namn men med olika Skift läge. Det beror på att vissa delar av Azure-plattformen inte är Skift läges känsliga, och det kan leda till förvirring/kollisioner för telemetri och åtgärder på behållare med sådana namn.

## <a name="create-a-container-using-azure-portal"></a>Skapa en container med hjälp av Azure-portalen

### <a name="sql-api"></a><a id="portal-sql"></a>SQL-API

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-sql-api-dotnet.md#create-account)eller Välj ett befintligt konto.

1. Öppna fönstret **datautforskaren** och välj **ny behållare**. Ange därefter följande information:

   * Ange om du skapar en ny databas eller använder en befintlig.
   * Ange ett container-ID.
   * Ange en partitionsnyckel.
   * Ange ett data flöde som ska tillhandahållas (till exempel 1000 ru: er).
   * Välj **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="Skärm bild av Datautforskaren fönstret med en ny behållare markerad":::

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="portal-mongodb"></a>API för Azure Cosmos DB för MongoDB

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-mongodb-dotnet.md#create-a-database-account)eller Välj ett befintligt konto.

1. Öppna fönstret **datautforskaren** och välj **ny behållare**. Ange därefter följande information:

   * Ange om du skapar en ny databas eller använder en befintlig.
   * Ange ett container-ID.
   * Ange en shardnyckel.
   * Ange ett data flöde som ska tillhandahållas (till exempel 1000 ru: er).
   * Välj **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="Skärm bild av Datautforskaren fönstret med en ny behållare markerad":::

### <a name="cassandra-api"></a><a id="portal-cassandra"></a>Cassandra-API

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-cassandra-dotnet.md#create-a-database-account)eller Välj ett befintligt konto.

1. Öppna rutan **Datautforskaren** och välj **Ny tabell**. Ange därefter följande information:

   * Ange om du skapar ett nytt nyckelutrymme eller använder ett befintligt.
   * Ange ett tabellnamn.
   * Ange egenskaper och ange en primärnyckel.
   * Ange ett data flöde som ska tillhandahållas (till exempel 1000 ru: er).
   * Välj **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-cassandra.png" alt-text="Skärm bild av Datautforskaren fönstret med en ny behållare markerad":::

> [!NOTE]
> För API för Cassandra används den primära nyckeln som partitionsnyckel.

### <a name="gremlin-api"></a><a id="portal-gremlin"></a>Gremlin-API

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-graph-dotnet.md#create-a-database-account)eller Välj ett befintligt konto.

1. Öppna rutan **Datautforskaren** och välj **Ny graf**. Ange därefter följande information:

   * Ange om du skapar en ny databas eller använder en befintlig.
   * Ange ett graf-ID.
   * Välj **obegränsad** lagringskapacitet.
   * Ange en partitionsnyckel för hörn.
   * Ange ett data flöde som ska tillhandahållas (till exempel 1000 ru: er).
   * Välj **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-gremlin.png" alt-text="Skärm bild av Datautforskaren fönstret med en ny behållare markerad":::

### <a name="table-api"></a><a id="portal-table"></a>Tabell-API

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-table-dotnet.md#create-a-database-account)eller Välj ett befintligt konto.

1. Öppna rutan **Datautforskaren** och välj **Ny tabell**. Ange därefter följande information:

   * Ange ett tabell-ID.
   * Ange ett data flöde som ska tillhandahållas (till exempel 1000 ru: er).
   * Välj **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="Skärm bild av Datautforskaren fönstret med en ny behållare markerad":::

> [!Note]
> För tabell-API anges partitionsnyckeln varje gång du lägger till en ny rad.

## <a name="create-a-container-using-azure-cli"></a>Skapa en behållare med Azure CLI<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

Länkarna nedan visar hur du skapar behållar resurser för Azure Cosmos DB med hjälp av Azure CLI.

För en lista över alla Azure CLI-exempel i alla Azure Cosmos DB API: er, se [Azure CLI-exempel för Azure Cosmos DB](cli-samples.md).

* [Skapa en behållare med Azure CLI](manage-with-cli.md#create-a-container)
* [Skapa en samling för Azure Cosmos DB för MongoDB-API med Azure CLI](./scripts/cli/mongodb/create.md)
* [Skapa en Cassandra-tabell med Azure CLI](./scripts/cli/cassandra/create.md)
* [Skapa en Gremlin-graf med Azure CLI](./scripts/cli/gremlin/create.md)
* [Skapa en Tabell-API tabell med Azure CLI](./scripts/cli/table/create.md)

## <a name="create-a-container-using-powershell"></a>Skapa en behållare med hjälp av PowerShell

Länkarna nedan visar hur du skapar behållar resurser för Azure Cosmos DB med hjälp av PowerShell.

En lista över alla PowerShell-exempel i alla Azure Cosmos DB API: er finns i [PowerShell-exempel](powershell-samples.md)

* [Skapa en behållare med PowerShell](manage-with-powershell.md#create-container)
* [Skapa en samling för Azure Cosmos DB för MongoDB-API med PowerShell](./scripts/powershell/mongodb/create.md)
* [Skapa en Cassandra-tabell med PowerShell](./scripts/powershell/cassandra/create.md)
* [Skapa ett Gremlin-diagram med PowerShell](./scripts/powershell/gremlin/create.md)
* [Skapa en Tabell-API tabell med PowerShell](./scripts/powershell/table/create.md)

## <a name="create-a-container-using-net-sdk"></a>Skapa en container med hjälp av .NET SDK

Om du råkar ut för tids gräns undantag när du skapar en samling, gör du en Läs åtgärd för att verifiera att samlingen har skapats. Läsnings åtgärden genererar ett undantag tills åtgärden Skapa samling har slutförts. En lista över status koder som stöds av Create-åtgärden finns i [HTTP-statuskod för Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) artikeln.

### <a name="sql-api-and-gremlin-api"></a><a id="dotnet-sql-graph"></a>SQL-API och Gremlin-API

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="dotnet-mongodb"></a>API för Azure Cosmos DB för MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> MongoDB Wire Protocol förstår inte konceptet för enheter för [programbegäran](request-units.md). Om du vill skapa en ny samling med ett tillhandahållet data flöde på den använder du Azure Portal eller Cosmos DB SDK: er för SQL API.

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra-API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Nästa steg

* [Partitionering i Azure Cosmos DB](partitioning-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)
* [Etablera dataflöde på containrar och databaser](set-throughput.md)
* [Arbeta med ett Azure Cosmos-konto](account-overview.md)
