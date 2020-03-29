---
title: Skapa Azure Cosmos-behållare med stor partitionsnyckel
description: Lär dig hur du skapar en behållare i Azure Cosmos DB med stor partitionsnyckel med Azure-portalen och olika SDK:er.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 7184a6b85e93c41dfe914813301a4b1a0c88f2cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887690"
---
# <a name="create-containers-with-large-partition-key"></a>Skapa behållare med stor partitionsnyckel

Azure Cosmos DB använder hash-baserat partitioneringsschema för att uppnå horisontell skalning av data. Alla Azure Cosmos-behållare som skapats före den 3 maj 2019 använder en hash-funktion som beräknar hash baserat på de första 100 bytena av partitionsnyckeln. Om det finns flera partitionsnycklar som har samma första 100 byte, betraktas dessa logiska partitioner som samma logiska partition av tjänsten. Detta kan leda till att problem som partitionsstorlekskvot är felaktiga och unika index tillämpas över partitionsnycklarna. Stora partitionsnycklar introduceras för att lösa problemet. Azure Cosmos DB stöder nu stora partitionsnycklar med värden upp till 2 KB.

Stora partitionsnycklar stöds med hjälp av funktionerna i en förbättrad version av hash-funktionen, vilket kan generera en unik hash från stora partitionsnycklar upp till 2 KB. Den här hash-versionen rekommenderas också för scenarier med hög partitionsnyckel kardinalitet oavsett storleken på partitionsnyckeln. En partitionsnyckel kardinalitet definieras som antalet unika logiska partitioner, till exempel i ordningen ~30000 logiska partitioner i en behållare. I den här artikeln beskrivs hur du skapar en behållare med en stor partitionsnyckel med Hjälp av Azure-portalen och olika SDK:er.

## <a name="create-a-large-partition-key-azure-portal"></a>Skapa en stor partitionsnyckel (Azure-portal)

Om du vill skapa en stor partitionsnyckel kontrollerar du alternativet **Min partitionsnyckel är större än alternativet 100 byte** när du skapar en ny behållare med Azure-portalen. Avmarkera kryssrutan om du inte behöver stora partitionsnycklar eller om du har program som körs på SDK-version som är äldre än 1.18.

![Skapa stora partitionsnycklar med Azure-portalen](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Skapa en stor partitionsnyckel (PowerShell)

Om du vill skapa en behållare med stort stöd för partitionsnyckel finns i

* [Skapa en Azure Cosmos-behållare med en stor partitionsnyckelstorlek](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Skapa en stor partitionsnyckel (.Net SDK)

Om du vill skapa en behållare med en stor partitionsnyckel med .NET SDK anger du egenskapen. `PartitionKeyDefinitionVersion.V2` I följande exempel visas hur du anger egenskapen Version i PartitionKeyDefinition-objektet och ställer in den på PartitionKeyDefinitionVersion.V2.

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

De stora partitionsnycklarna stöds med följande minimiversioner av SDK:er:

|SDK-typ  | Lägsta version   |
|---------|---------|
|.Net     |    1.18     |
|Java-synkronisering     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST API | versionen högre `2017-05-03` än `x-ms-version` genom att använda begäranden.|
| Resource Manager-mall | version 2 med `"version":2` hjälp `partitionKey` av egenskapen i objektet. |

För närvarande kan du inte använda behållare med stor partitionsnyckel i Power BI och Azure Logic Apps. Du kan använda behållare utan en stor partitionsnyckel från dessa program.

## <a name="next-steps"></a>Nästa steg

* [Partitionering i Azure Cosmos DB](partitioning-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)
* [Etablera dataflöde på containrar och databaser](set-throughput.md)
* [Arbeta med ett Azure Cosmos-konto](account-overview.md)
