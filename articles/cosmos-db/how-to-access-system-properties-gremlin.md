---
title: Åtkomst till system dokument egenskaper via Azure Cosmos DB Graph
description: Lär dig hur Läs-och skriv Cosmos DB egenskaper för system dokument via Gremlin-API
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/10/2019
author: SnehaGunda
ms.author: sngun
ms.openlocfilehash: c03e4db30d590df21a8ceb3c483ece4b59e548d8
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397325"
---
# <a name="system-document-properties"></a>System dokument egenskaper

Azure Cosmos DB har [system egenskaper](/rest/api/cosmos-db/databases) som,,, ```_ts``` ```_self``` ```_attachments``` ```_rid``` och ```_etag``` i varje dokument. Gremlin-motorn har dessutom egenskaperna ```inVPartition``` och ```outVPartition``` för kanter. Som standard är dessa egenskaper tillgängliga för Traversal. Det är dock möjligt att inkludera vissa egenskaper, eller alla, i Gremlin Traversal.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-tagg

Den här egenskapen används vid optimistisk samtidighetskontroll. Om programmet behöver bryta åtgärden till några separata bläddringskontroll kan den använda eTag-egenskapen för att undvika data förlust i samtidiga skrivningar.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>TTL-värde (time to live)

Om insamlingen av dokument har förfallit och dokument har en ```ttl``` egenskap som har angetts för dem, blir den här egenskapen tillgänglig i Gremlin Traversal som en vanlig hörn-eller kant egenskap. ```ProjectionStrategy``` är inte nödvändigt för att aktivera exponering för TTL (Time to Live).

Hörn som skapats med bläddringen nedan tas automatiskt bort efter **123 sekunder**.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>Nästa steg
* [Optimistisk samtidighet för Cosmos DB](faq.md#how-does-the-sql-api-provide-concurrency)
* [Time to Live (TTL)](time-to-live.md) i Azure Cosmos DB
