---
title: 'Skapa Azure Cosmos-behållare med stor partitionsnyckel med hjälp av Azure-portalen och olika SDK: er.'
description: 'Lär dig hur du skapar en behållare i Azure Cosmos DB med stor Partitionsnyckeln med hjälp av Azure-portalen och olika SDK: er.'
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: ee1568274792621b8ed9f25d211c40440a82208c
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160042"
---
# <a name="create-containers-with-large-partition-key"></a>Skapa behållare med stora partitionsnyckel

Azure Cosmos DB använder hash-baserade partitioneringsschemat för att uppnå horisontell skalning av data. Alla Azure Cosmos-behållare som skapats före maj 3 2019 använder en hash-funktion som beräknar hash-värde baserat på de första 100 byte för Partitionsnyckeln. Om det finns flera partitionsnycklar som har samma första 100 byte, sedan anses dessa logiska partitioner vara samma logiska partition av tjänsten. Detta kan leda till problem som partitionen storlekskvot är felaktig och unika index som tillämpas på partitionsnycklar. Stora partitionsnycklar introduceras för att lösa problemet. Azure Cosmos DB som nu har stöd för stora partitionsnycklar av värden upp till 2 KB. 

Stor partition nycklar stöds med hjälp av funktionen för en förbättrad version av hash-funktion som kan generera ett unikt hash-värde från stor partition-nycklar för upp till 2 KB. Den här hash-versionen rekommenderas även för scenarier med hög partition nyckelns kardinalitet oavsett storlek för Partitionsnyckeln. En partition viktiga kardinalitet definieras som antalet unika logiska partitioner, till exempel i den ordning som ~ 30000 logiska partitioner i en behållare. Den här artikeln beskriver hur du skapar en behållare med en stor partitionsnyckel med hjälp av Azure-portalen och olika SDK: er. 

## <a name="create-a-large-partition-key-net-sdk-v2"></a>Skapa en stor partition-nyckel (.Net SDK-V2)

När du använder .net SDK för att skapa en behållare med stora partitionsnyckel, bör du ange den `PartitionKeyDefinitionVersion.V2` egenskapen. I följande exempel visas hur du anger egenskapen Version inom PartitionKeyDefinition objektet och ange den till PartitionKeyDefinitionVersion.V2:

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

## <a name="create-a-large-partition-key-azure-portal"></a>Skapa en stor partitionsnyckel (Azure portal) 

Om du vill skapa en stor partitionsnyckel när du skapar en ny behållare med Azure portal, markera den **min Partitionsnyckeln är större än 100 byte** alternativet. Som standard avanmäls alla nya behållare till med hjälp av stora partitionsnycklar. Avmarkera kryssrutan om du inte behöver stora partitionsnycklar eller om du har program som körs på SDK-version som är äldre än 1.18.

![Skapa stora partitionsnycklar med hjälp av Azure portal](./media/large-partition-keys/large-partition-key-with-portal.png)
 
## <a name="next-steps"></a>Nästa steg

* [Partitionering i Azure Cosmos DB](partitioning-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)
* [Etablera dataflöde på behållare och databaser](set-throughput.md)
* [Arbeta med Azure Cosmos-konto](account-overview.md)


