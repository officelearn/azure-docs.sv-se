---
title: 'Skapa Azure Cosmos-behållare med stor partitionsnyckel med hjälp av Azure Portal och olika SDK: er.'
description: 'Lär dig hur du skapar en behållare i Azure Cosmos DB med stor partitionsnyckel med Azure Portal och olika SDK: er.'
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: be639a67f70ab40f8d7dcc0f3793cbbd4a2ec4a3
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72436804"
---
# <a name="create-containers-with-large-partition-key"></a>Skapa behållare med stor partitionsnyckel

Azure Cosmos DB använder hash-baserad partitionerings schema för att uppnå horisontell skalning av data. Alla Azure Cosmos-behållare som skapats före maj 3 2019 använder en hash-funktion som beräknar hash baserat på den första 100 byte i partitionsnyckel. Om det finns flera partitionstyper som har samma första 100 byte betraktas dessa logiska partitioner som samma logiska partition av tjänsten. Detta kan leda till problem som att kvoten för partitionsstorleken är felaktig och att unika index tillämpas över partitionernas nycklar. Stora partitionsalternativ har introducerats för att lösa det här problemet. Azure Cosmos DB stöder nu stora partitionsnyckel med värden upp till 2 KB.

Stora partitionsuppsättningar stöds med hjälp av funktionerna i en förbättrad version av hash-funktionen, som kan generera en unik hash från stora partitionsnyckel upp till 2 KB. Den här hash-versionen rekommenderas också för scenarier med hög partitionsnyckel, oberoende av storleken på partitionsnyckel. En kardinalitet för partitionsnyckel definieras som antalet unika logiska partitioner, till exempel i ordningen ~ 30000 logiska partitioner i en behållare. Den här artikeln beskriver hur du skapar en behållare med en stor partitionsnyckel med hjälp av Azure Portal och olika SDK: er.

## <a name="create-a-large-partition-key-azure-portal"></a>Skapa en stor partitionsnyckel (Azure Portal)

Om du vill skapa en stor partitionsnyckel när du skapar en ny behållare med hjälp av Azure Portal, kontrollerar du att **nyckeln My partition är större än 100-byte-** alternativet. Avmarkera kryss rutan om du inte behöver stora partitionerings nycklar eller om du har program som körs på SDK-versioner som är äldre än 1,18.

![Skapa stora partitionsalternativ med Azure Portal](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Skapa en stor partitionsnyckel (PowerShell)

Om du vill skapa en behållare med stöd för stor partitionsnyckel, se,

* [Skapa en Azure Cosmos-behållare med en stor nyckel storlek](manage-with-powershell.md##create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Skapa en stor partitionsnyckel (.NET SDK)

Om du vill skapa en behållare med en stor partitionsnyckel med hjälp av .NET SDK anger du egenskapen `PartitionKeyDefinitionVersion.V2`. I följande exempel visas hur du anger egenskapen version i PartitionKeyDefinition-objektet och anger den till PartitionKeyDefinitionVersion. v2.

### <a name="v3-net-sdk"></a>v3 .NET SDK

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

### <a name="v2-net-sdk"></a>v2 .NET SDK

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```

## <a name="supported-sdk-versions"></a>SDK-versioner som stöds

De stora partitionernas nycklar stöds med följande minst SDK-versioner:

|SDK-typ  | Lägsta version   |
|---------|---------|
|.Net     |    1,18     |
|Java-synkronisering     |   2.4.0      |
|Java async   |  2.5.0        |
| REST-API | högre version än `2017-05-03` med hjälp av den `x-ms-version` begär ande rubriken.|

För närvarande kan du inte använda behållare med stor partitionsnyckel i i Power BI och Azure Logic Apps. Du kan använda behållare utan en stor partitionsnyckel från dessa program.

## <a name="next-steps"></a>Nästa steg

* [Partitionering i Azure Cosmos DB](partitioning-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)
* [Etablera data flöde på behållare och databaser](set-throughput.md)
* [Arbeta med Azure Cosmos-konto](account-overview.md)
