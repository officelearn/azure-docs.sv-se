---
title: Få tillgång till systemdokumentegenskaper via Azure Cosmos DB Graph
description: Läs om och skriv Cosmos DB-systemdokumentegenskaper via Gremlin API
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/10/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 4ed7e67ae0ef027b260d0e0f0407e4e05ed5a8f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898312"
---
# <a name="system-document-properties"></a>Egenskaper för systemdokument

Azure Cosmos DB har ```_ts``` [systemegenskaper](https://docs.microsoft.com/rest/api/cosmos-db/databases) ```_rid```som ```_etag``` , ```_self```, ```_attachments```och i alla dokument. Gremlin-motorn har dessutom egenskaperna ```inVPartition``` och ```outVPartition``` för kanter. Som standard är dessa egenskaper tillgängliga för traversal. Det är dock möjligt att inkludera specifika egenskaper, eller alla av dem, i Gremlin traversal.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-tagg

Den här egenskapen används vid optimistisk samtidighetskontroll. Om programmet behöver brytas med några separata traversaler kan det använda egenskapen eTag för att undvika dataförlust i samtidiga skrivningar.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>TTL-värde (time to live)

Om samlingen har dokumentets förfallodatum aktiverat och dokument har ```ttl``` egenskapen inställd på dem, kommer den här egenskapen att vara tillgänglig i Gremlin traversal som en vanlig vertex- eller kantegenskap. ```ProjectionStrategy```är inte nödvändigt för att aktivera exponering för tid till levande egendom.

Hörn som skapats med bläddringen nedan tas automatiskt bort efter **123 sekunder**.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>Nästa steg
* [Optimistisk samtidighet för Cosmos DB](faq.md#how-does-the-sql-api-provide-concurrency)
* [Dags att leva (TTL)](time-to-live.md) i Azure Cosmos DB
