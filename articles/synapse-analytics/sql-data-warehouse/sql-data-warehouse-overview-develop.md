---
title: Resurser för att utveckla en dedikerad SQL-pool i Azure Synapse Analytics
description: Utvecklings begrepp, design beslut, rekommendationer och kodnings tekniker för en dedikerad SQL-pool i Azure Synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: f158655cd7aeba647480325966c7f0cfcf65b13c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322135"
---
# <a name="design-decisions-and-coding-techniques-for-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Utforma beslut och kod metoder för en dedikerad SQL-pool i Azure Synapse Analytics 

 I den här artikeln hittar du ytterligare resurser som hjälper dig att få bättre förståelse för viktiga design beslut, rekommendationer och kodnings metoder för en särskild SQL-pool i Azure Synapse.

## <a name="key-design-decisions"></a>Viktiga design beslut

Följande artiklar fokuserar på begrepp och design beslut för att utveckla ett distribuerat informations lager med hjälp av funktionen för dedikerad SQL-pool i Azure Synapse:

* [anslutning](../sql/connect-overview.md)
* [samtidighet](resource-classes-for-workload-management.md)
* [transaktioner](sql-data-warehouse-develop-transactions.md)
* [användardefinierade scheman](sql-data-warehouse-develop-user-defined-schemas.md)
* [tabell distribution](sql-data-warehouse-tables-distribute.md)
* [tabell index](sql-data-warehouse-tables-index.md)
* [Table-partitioner](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [uppgifterna](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Utvecklings rekommendationer och kodnings metoder

Följande artiklar har särskilda kodnings tekniker, tips och rekommendationer för att utveckla en dedikerad SQL-pool:

* [lagrade procedurer](sql-data-warehouse-develop-stored-procedures.md)
* [Etiketter](sql-data-warehouse-develop-label.md)
* [vyer](performance-tuning-materialized-views.md)
* [temporära tabeller](sql-data-warehouse-tables-temporary.md)
* [dynamisk SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [loopning](sql-data-warehouse-develop-loops.md)
* [Gruppera efter alternativ](sql-data-warehouse-develop-group-by-options.md)
* [variabel tilldelning](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Nästa steg

Mer referensinformation finns i [T-SQL-uttryck](sql-data-warehouse-reference-tsql-statements.md).
