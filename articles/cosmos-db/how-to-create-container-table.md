---
title: Skapa en behållare i Azure Cosmos DB Tabell-API
description: 'Lär dig hur du skapar en behållare i Azure Cosmos DB Tabell-API med Azure Portal, .NET, Java, python, Node.js och andra SDK: er.'
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 8c970c820417a5cff7030a8499cf0c3520465231
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491146"
---
# <a name="create-a-container-in-azure-cosmos-db-table-api"></a>Skapa en behållare i Azure Cosmos DB Tabell-API

I den här artikeln beskrivs olika sätt att skapa en behållare i Azure Cosmos DB Tabell-API. Det visar hur du skapar en behållare med hjälp av Azure Portal, Azure CLI, PowerShell eller SDK: er som stöds. Den här artikeln visar hur du skapar en container, anger partitionsnyckeln och etablerar dataflöde.

I den här artikeln beskrivs olika sätt att skapa en behållare i Azure Cosmos DB Tabell-API. Om du använder ett annat API, se [API för MongoDB](how-to-create-container-mongodb.md), [API för Cassandra](how-to-create-container-cassandra.md), [Gremlin API](how-to-create-container-gremlin.md)och [SQL API](how-to-create-container.md) -artiklar för att skapa behållaren.

> [!NOTE]
> När du skapar behållare ser du till att du inte skapar två behållare med samma namn men med olika Skift läge. Det beror på att vissa delar av Azure-plattformen inte är Skift läges känsliga, och det kan leda till förvirring/kollisioner för telemetri och åtgärder på behållare med sådana namn.

## <a name="create-using-azure-portal"></a><a id="portal-table"></a>Skapa med hjälp av Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-table-dotnet.md#create-a-database-account)eller Välj ett befintligt konto.

1. Öppna rutan **Datautforskaren** och välj **Ny tabell**. Ange därefter följande information:

   * Ange ett tabell-ID.
   * Ange ett data flöde som ska tillhandahållas (till exempel 1000 ru: er).
   * Välj **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="Skärmbild av Tabell-API, dialogrutan Lägg till tabell":::

> [!Note]
> För tabell-API anges partitionsnyckeln varje gång du lägger till en ny rad.

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Skapa med Azure CLI

[Skapa en tabell-API-tabell med Azure CLI](./scripts/cli/table/create.md). För en lista över alla Azure CLI-exempel i alla Azure Cosmos DB API: er, se [Azure CLI-exempel för Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Skapa med PowerShell

[Skapa en tabell-API-tabell med PowerShell](./scripts/powershell/table/create.md). En lista över alla PowerShell-exempel i alla Azure Cosmos DB API: er finns i [PowerShell-exempel](powershell-samples.md)

## <a name="next-steps"></a>Nästa steg

* [Partitionering i Azure Cosmos DB](partitioning-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)
* [Etablera dataflöde på containrar och databaser](set-throughput.md)
* [Arbeta med ett Azure Cosmos-konto](./account-databases-containers-items.md)