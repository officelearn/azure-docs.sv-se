---
title: Resurser för utveckling av ett informationslager i Azure | Microsoft Docs
description: Koncept för utveckling, designbeslut, rekommendationer och kodning tekniker för SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 08/29/2018
ms.author: kevinvngo
ms.reviewer: igorstan
ms.openlocfilehash: 5cb3b3b261bcb762187b165e297225080b0fee81
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306066"
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Designbeslut och kodning tekniker för SQL Data Warehouse
Ta en titt på dessa utvecklingsartiklarna att bättre förstå viktiga designbeslut, rekommendationer och kodning tekniker för SQL Data Warehouse.

## <a name="key-design-decisions"></a>Viktiga designbeslut
I följande artiklar Markera begrepp och designbeslut för att utveckla ett distribuerade data warehouse med SQL Data Warehouse:

* [Anslutningar][connections]
* [samtidighet][concurrency]
* [Transaktioner][transactions]
* [användardefinierade scheman][user-defined schemas]
* [tabelldistribution][table distribution]
* [Tabellindex][table indexes]
* [Tabellpartitioner][table partitions]
* [CTAS][CTAS]
* [statistik][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Rekommendationer för utveckling och kodning tekniker
Artiklarna innehåller specifika tekniker för kodning, tips och rekommendationer för att utveckla ditt SQL Data Warehouse:

* [Lagrade procedurer][stored procedures]
* [etiketter][labels]
* [Vyer][views]
* [Temporära tabeller][temporary tables]
* [dynamisk SQL][dynamic SQL]
* [slingor][looping]
* [Gruppera efter alternativ][group by options]
* [variabeltilldelning][variable assignment]

## <a name="next-steps"></a>Nästa steg
Mer information, finns i [SQL Data Warehouse T-SQL-uttryck](sql-data-warehouse-reference-tsql-statements.md).

<!--Image references-->

<!--Article references-->
[concurrency]: ./resource-classes-for-workload-management.md
[connections]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[dynamic SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[group by options]: ./sql-data-warehouse-develop-group-by-options.md
[labels]: ./sql-data-warehouse-develop-label.md
[looping]: ./sql-data-warehouse-develop-loops.md
[statistics]: ./sql-data-warehouse-tables-statistics.md
[stored procedures]: ./sql-data-warehouse-develop-stored-procedures.md
[table distribution]: ./sql-data-warehouse-tables-distribute.md
[table indexes]: ./sql-data-warehouse-tables-index.md
[table partitions]: ./sql-data-warehouse-tables-partition.md
[temporary tables]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[user-defined schemas]: ./sql-data-warehouse-develop-user-defined-schemas.md
[variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[views]: ./sql-data-warehouse-develop-views.md


<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
