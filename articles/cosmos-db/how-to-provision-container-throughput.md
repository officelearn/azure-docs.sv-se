---
title: Etablera dataflöde för en container i Azure Cosmos DB
description: Lär dig att etablera dataflöde på containernivå i Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 8da27773cc74324c1dde5a95de1abef3256c1f1c
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71811683"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Etablera dataflöde i en Azure Cosmos-container

Den här artikeln beskriver hur du etablerar data flöde i en behållare (samling, Graf eller tabell) i Azure Cosmos DB. Du kan etablera data flöde på en enskild behållare eller [etablera data flöde på en databas](how-to-provision-database-throughput.md) och dela den mellan behållarna i databasen. Du kan etablera data flöde på en behållare med hjälp av Azure Portal, Azure CLI eller Azure Cosmos DB SDK: er.

## <a name="provision-throughput-using-azure-portal"></a>Etablera dataflöde med hjälp av Azure-portalen

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-sql-api-dotnet.md#create-account)eller Välj ett befintligt Azure Cosmos-konto.

1. Öppna fönsterrutan **Data Explorer** och välj **Ny samling**. Ange därefter följande information:

   * Ange huruvida du skapar en ny databas eller använder en befintlig.
   * Ange ett ID för behållare (eller tabell eller graf).
   * Ange ett partitionsnyckelvärde (till exempel `/userid`).
   * Ange ett data flöde som du vill etablera (till exempel 1000 ru: er).
   * Välj **OK**.

    ![Skärmbild av Data Explorer med Ny samling markerat](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Etablera data flöde med hjälp av Azure CLI eller PowerShell

Om du vill skapa en behållare med dedikerat data flöde ser du

* [Skapa en behållare med Azure CLI](manage-with-cli.md#create-a-container)
* [Skapa en behållare med hjälp av PowerShell](manage-with-powershell.md#create-container)

> [!Note]
> Om du konfigurerar data flödet på en behållare i ett Azure Cosmos-konto som kon figurer ATS med Azure Cosmos DB-API: t för MongoDB använder du `/myShardKey` för sökvägen till partitionsnyckel. Om du konfigurerar data flödet på en behållare i ett Azure Cosmos-konto som kon figurer ATS med API för Cassandra använder du `/myPrimaryKey` för sökvägen till partitionsnyckel.

## <a name="provision-throughput-by-using-net-sdk"></a>Etablera dataflöde med hjälp av .NET SDK

> [!Note]
> Använd Cosmos SDK: er för SQL API för att etablera data flöde för alla Cosmos DB-API: er, förutom API för Cassandra.

### <a id="dotnet-most"></a>SQL, MongoDB, Gremlin och tabell-API:er
### <a name="net-v2-sdk"></a>.Net V2 SDK

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

### <a name="net-v3-sdk"></a>.Net V3 SDK
[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

### <a id="dotnet-cassandra"></a>API för Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>Nästa steg

I följande artiklar kan du lära dig hur du etablerar dataflöde i Azure Cosmos DB:

* [Hur du etablerar data flöde på en databas](how-to-provision-database-throughput.md)
* [Begärandeenheter och dataflöde i Azure Cosmos DB](request-units.md)
