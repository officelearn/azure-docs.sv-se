---
title: Etablera dataflöde för en databas i Azure Cosmos DB
description: Lär dig att etablera dataflöde på databasnivå i Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/03/2019
ms.author: rimman
ms.openlocfilehash: e44017ea2df24445c28361a0f3d7fb2671317b6d
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68000652"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Etablera dataflöde för en databas i Azure Cosmos DB

Den här artikeln beskriver hur du etablera dataflöde för en databas i Azure Cosmos DB. Du kan etablera dataflöden för en enda [container](how-to-provision-container-throughput.md) eller för en databas och dela dataflödet mellan containrar i den. Läs när du ska använda behållare på servernivå och databasnivå dataflöde i den [användningsfall för att etablera dataflöde på behållare och databaser](set-throughput.md) artikeln. Du kan etablera dataflöde på databasnivå med hjälp av Azure-portalen eller Azure Cosmos DB-SDK:er.

## <a name="provision-throughput-using-azure-portal"></a>Etablera dataflöde med hjälp av Azure-portalen

### <a id="portal-sql"></a>SQL (Core) API

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-sql-api-dotnet.md#create-account), eller välj ett befintligt Azure Cosmos-konto.

1. Öppna rutan **Data Explorer** och välj **Ny databas**. Ange följande information:

   * Ange ett databas-ID. 
   * Välj **Etablera dataflöde**.
   * Ange ett dataflöde (till exempel 1000 RU).
   * Välj **OK**.

![Skärmbild av dialogrutan Ny databas](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-powershell"></a>Etablera dataflöde med hjälp av PowerShell

```azurepowershell-interactive
# Create a database and provision throughput of 400 RU/s
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$databaseResourceName = $accountName + "/sql/" + $databaseName

$databaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"= 400 }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseResourceName -PropertyObject $databaseProperties
```

## <a name="provision-throughput-using-net-sdk"></a>Etablera dataflöde med hjälp av .NET SDK

> [!Note]
> Du kan använda SDK: er Cosmos för SQL-API att etablera dataflöde för alla API: er. Om du vill kan du även använda följande exempel för API för Cassandra.

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

### <a id="dotnet-cassandra"></a>API för Cassandra

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för att lära dig om etablerat dataflöde i Azure Cosmos DB:

* [Skala globalt etablerat dataflöde](scaling-throughput.md)
* [Etablera dataflöde på behållare och databaser](set-throughput.md)
* [Etablera dataflöde för en container](how-to-provision-container-throughput.md)
* [Enheter för programbegäran och dataflöde i Azure Cosmos DB](request-units.md)