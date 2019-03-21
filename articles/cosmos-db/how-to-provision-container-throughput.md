---
title: Etablera dataflöde för en container i Azure Cosmos DB
description: Lär dig att etablera dataflöde på containernivå i Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 28060637db47b42db66f706815066d498032ec11
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258725"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Etablera dataflöde i en Azure Cosmos-container

I den här artikeln beskrivs hur du etablerar dataflödet för en container (samling, graf eller tabell) i Azure Cosmos DB. Du kan etablera dataflöden för en enskild container eller [etablera för en databas](how-to-provision-database-throughput.md) och dela det mellan containrarna i dataflödet. Du kan etablera dataflöde för en container med hjälp av Azure-portalen, Azure CLI eller Azure CosmosDB-SDK:er.

## <a name="provision-throughput-by-using-azure-portal"></a>Etablera dataflöde med hjälp av Azure-portalen

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos DB-konto](create-sql-api-dotnet.md#create-account) eller välj ett befintligt konto.

1. Öppna fönsterrutan **Data Explorer** och välj **Ny samling**. Ange därefter följande information:

   * Ange huruvida du skapar en ny databas eller använder en befintlig.
   * Ange ett samlings-ID (eller tabell eller graf).
   * Ange ett partitionsnyckelvärde (till exempel `/userid`).
   * Ange ett dataflöde (till exempel 1000 RU).
   * Välj **OK**.

![Skärmbild av Data Explorer med Ny samling markerat](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-by-using-azure-cli"></a>Etablera dataflöde med hjälp av Azure CLI

```azurecli-interactive
# Create a container with a partition key and provision throughput of 1000 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

Om du etablerar dataflöde för ett Azure Cosmos DB-konto som har konfigurerats med Azure Cosmos DB API för MongoDB använder du `/myShardKey` för partitionsnyckelns sökväg. Om du etablerar dataflöde för ett Azure Cosmos DB-konto som har konfigurerats för API för Cassandra använder du `/myPrimaryKey` för partitionsnyckelns sökväg.

## <a name="provision-throughput-by-using-net-sdk"></a>Etablera dataflöde med hjälp av .NET SDK

> [!Note]
> Använd SQL API för att etablera dataflöde för alla API: er förutom API för Cassandra.

### <a id="dotnet-most"></a>SQL, MongoDB, Gremlin och tabell-API:er

```csharp
// Create a container with a partition key and provision throughput of 1000 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-cassandra"></a>API för Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 1000 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Nästa steg

I följande artiklar kan du lära dig hur du etablerar dataflöde i Azure Cosmos DB:

* [Så etablerar du dataflöde för en databas](how-to-provision-database-throughput.md)
* [Begärandeenheter och dataflöde i Azure Cosmos DB](request-units.md)
