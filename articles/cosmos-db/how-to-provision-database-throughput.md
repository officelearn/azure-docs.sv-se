---
title: Etablera dataflöde för en databas i Azure Cosmos DB
description: 'Lär dig hur du etablerar data flöde på databas nivå i Azure Cosmos DB att använda Azure Portal, CLI, PowerShell och andra SDK: er.'
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: ef7d06dfb074a3453f5589284cbdaf079c48d111
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2020
ms.locfileid: "78933777"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Etablera data flöde för en databas i Azure Cosmos DB

Den här artikeln beskriver hur du etablerar data flöde på en databas i Azure Cosmos DB. Du kan etablera dataflöden för en enda [container](how-to-provision-container-throughput.md) eller för en databas och dela dataflödet mellan containrar i den. Information om hur du använder data flödes nivå och databas nivå genom strömning finns i avsnittet [användnings fall för etablering av data flöde på behållare och databaser](set-throughput.md) . Du kan etablera dataflöde på databasnivå med hjälp av Azure-portalen eller Azure Cosmos DB-SDK:er.

## <a name="provision-throughput-using-azure-portal"></a>Etablera dataflöde med hjälp av Azure-portalen

### <a id="portal-sql"></a>SQL (Core) API

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-sql-api-dotnet.md#create-account)eller Välj ett befintligt Azure Cosmos-konto.

1. Öppna rutan **Data Explorer** och välj **Ny databas**. Ange följande information:

   * Ange ett databas-ID.
   * Välj **Etablera dataflöde**.
   * Ange ett dataflöde (till exempel 1000 RU).
   * Välj **OK**.

    ![Skärmbild av dialogrutan Ny databas](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Etablera data flöde med hjälp av Azure CLI eller PowerShell

Information om hur du skapar en databas med delat data flöde finns i

* [Skapa en databas med Azure CLI](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Skapa en databas med PowerShell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Etablera dataflöde med hjälp av .NET SDK

> [!Note]
> Du kan använda Cosmos SDK: er för SQL API för att etablera data flöde för alla API: er. Om du vill kan du även använda följande exempel för API för Cassandra.

### <a id="dotnet-all"></a>Alla API:er

### <a name="net-v2-sdk"></a>.Net V2 SDK

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a name="net-v3-sdk"></a>.Net V3 SDK

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

### <a id="dotnet-cassandra"></a>Cassandra-API
Liknande kommando kan köras via valfri CQL-kompatibel driv rutin. 
```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```
 
## <a name="next-steps"></a>Nästa steg

I följande artiklar finns information om etablerade data flöden i Azure Cosmos DB:

* [Globalt skalat allokerat data flöde](scaling-throughput.md)
* [Etablera dataflöde på containrar och databaser](set-throughput.md)
* [Etablera dataflöde för en container](how-to-provision-container-throughput.md)
* [Enheter för programbegäran och dataflöde i Azure Cosmos DB](request-units.md)
