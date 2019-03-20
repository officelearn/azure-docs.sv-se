---
title: Etablera dataflöde för en databas i Azure Cosmos DB
description: Lär dig att etablera dataflöde på databasnivå i Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 8c2fe8d42ded5a072a2211b102736326554bc4f4
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57239894"
---
# <a name="provision-throughput-for-a-database-in-azure-cosmos-db"></a>Etablera dataflöde för en databas i Azure Cosmos DB

I den här artikeln beskrivs hur du etablerar dataflödet för en databas i Azure Cosmos DB. Du kan etablera dataflöden för en enda [container](how-to-provision-container-throughput.md) eller för en databas och dela dataflödet mellan containrar i den. Läs när du ska använda behållare på servernivå och databasnivå dataflöde i den [användningsfall för att etablera dataflöde på behållare och databaser](set-throughput.md) artikeln. Du kan etablera dataflöde på databasnivå med hjälp av Azure-portalen eller Azure Cosmos DB-SDK:er.

## <a name="provision-throughput-by-using-azure-portal"></a>Etablera dataflöde med hjälp av Azure-portalen

### <a id="portal-sql"></a>SQL (Core) API

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos DB-konto](create-sql-api-dotnet.md#create-a-database-account) eller välj ett befintligt konto.

1. Öppna rutan **Data Explorer** och välj **Ny databas**. Ange följande information:

   * Ange ett databas-ID. 
   * Välj **Etablera dataflöde**.
   * Ange ett dataflöde (till exempel 1000 RU).
   * Välj **OK**.

![Skärmbild av dialogrutan Ny databas](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-by-using-net-sdk"></a>Etablera dataflöde med hjälp av .NET SDK

> [!Note]
> Använd SQL API för att etablera dataflöde för alla API: er. Om du vill kan du även använda följande exempel för API för Cassandra.

### <a id="dotnet-all"></a>Alla API:er

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 10000
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a id="dotnet-cassandra"></a>API för Cassandra

```csharp
// Create a Cassandra keyspace and provision throughput of 10000 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=10000);
```

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för att lära dig hur du etablerar dataflöde i Azure Cosmos DB:

* [Etablera dataflöde för en container](how-to-provision-container-throughput.md)
* [Enheter för programbegäran och dataflöde i Azure Cosmos DB](request-units.md)
