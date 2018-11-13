---
title: Etablera dataflöde för en databas i Azure Cosmos DB
description: Lär dig att etablera dataflöde på databasnivå i Azure Cosmos DB
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 4eba0381eb302ca4400fb5669b486fb3ad337993
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262530"
---
# <a name="provision-throughput-for-a-database-in-azure-cosmos-db"></a>Etablera dataflöde för en databas i Azure Cosmos DB

I den här artikeln beskrivs hur du etablerar dataflödet för en databas i Azure Cosmos DB. Du kan etablera dataflöden för en enda [container](how-to-provision-container-throughput.md) eller för en databas och dela dataflödet mellan containrar i den. Du kan etablera dataflöde på databasnivå med hjälp av Azure-portalen eller Cosmos DB SDK.

## <a name="provision-throughput-using-azure-portal"></a>Etablera dataflöde med hjälp av Azure-portalen

### <a id="portal-sql"></a>SQL (Core) API

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Cosmos DB-konto](create-sql-api-dotnet.md#create-a-database-account) eller välj ett befintligt konto.

1. Öppna rutan **Datautforskaren** och välj **Ny databas**. Fyll sedan i formuläret med följande information:

   * Ange ett databas-ID. 
   * Välj Etablera dataflöde.
   * Ange ett dataflöde, till exempel 1000 RU:er.
   * Välj **OK**.

![SQL API provision database throughput](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-net-sdk"></a>Etablera dataflöde med hjälp av .NET SDK

> [!Note]
> Använd SQL API för att etablera dataflöde för alla API: er. Du kan också använda exemplet nedan för API för Cassandra.

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

Se följande artiklar för att lära dig hur du etablerar dataflöde i Cosmos DB:

* [Etablera dataflöde för en container](how-to-provision-container-throughput.md)
* [Enheter för programbegäran och dataflöde i Azure Cosmos DB](request-units.md)
