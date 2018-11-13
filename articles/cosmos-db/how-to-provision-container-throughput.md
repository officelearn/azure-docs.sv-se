---
title: Etablera dataflöde för en container i Azure Cosmos DB
description: Lär dig att etablera dataflöde på containernivå i Azure Cosmos DB
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 3b766cfa339e6cbb568cf57383667d270153401f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262428"
---
# <a name="provision-throughput-for-an-azure-cosmos-db-container"></a>Etablera dataflöde för en Azure Cosmos DB-container

I den här artikeln beskrivs hur du etablerar dataflödet för en container (samling, graf, tabell) i Azure Cosmos DB. Du kan etablera dataflöden för en enda container eller [etablera för en databas](how-to-provision-database-throughput.md) och dela det mellan containrarna i dataflödet. Du kan etablera dataflöde för en container med hjälp av Azure-portalen, Azure CLI eller CosmosDB-SDK:er.

## <a name="provision-throughput-using-azure-portal"></a>Etablera dataflöde med hjälp av Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Cosmos DB-konto](create-sql-api-dotnet.md#create-a-database-account) eller välj ett befintligt konto.

1. Öppna fönsterrutan **Datautforskaren** och välj **Ny samling**. Fyll sedan i formuläret med följande information:

   * Skapa en ny databas eller välj en befintlig.
   * Ange en samlings-ID (eller tabell, graf).
   * Ange ett dataflöde, till exempel 1 000 RU.
   * Välj **OK**.

![SQL API, etablera containerdataflöde](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli"></a>Etablera dataflöde med hjälp av Azure CLI

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

Om du etablerar dataflöde för ett MongoDB API-konto använder du ”/myShardKey” för partitionsnyckelsökvägen, och vid etablering av dataflöde för ett Cassandra API-konto använder du ”/myPrimaryKey” för partitionsnyckelsökvägen.

## <a name="provision-throughput-using-net-sdk"></a>Etablera dataflöde med hjälp av .NET SDK

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

I följande artiklar kan du lära dig hur du etablerar dataflöde i Cosmos DB:

* [Så etablerar du dataflöde för en databas](how-to-provision-database-throughput.md)
* [Begärandeenheter och dataflöde i Azure Cosmos DB](request-units.md)
