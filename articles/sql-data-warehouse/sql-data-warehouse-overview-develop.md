---
title: Resurser för att utveckla ett data warehouse i Azure | Microsoft Docs
description: Begrepp för utveckling, designbeslut, rekommendationer och kodning tekniker för SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: d9a272b2f43e080cd44b7179fe6f9dc55507142b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Designbeslut och kodning tekniker för SQL Data Warehouse
Titta igenom dessa development artiklar för att bättre förstå viktiga designbeslut, rekommendationer och kodning tekniker för SQL Data Warehouse.

## <a name="key-design-decisions"></a>Viktiga designbeslut
I följande artiklar Markera begrepp och designbeslut för att utveckla ett distribuerade data warehouse med hjälp av SQL Data Warehouse:

* [Anslutningar][connections]
* [Concurrency][concurrency]
* [Transaktioner][transactions]
* [Användardefinierade scheman][user-defined schemas]
* [tabell-distribution][table distribution]
* [Tabellindex][table indexes]
* [tabellpartitioner][table partitions]
* [CTAS][CTAS]
* [Statistik][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Rekommendationer för utveckling och kodning tekniker
Dessa artiklar markera specifika kodning tekniker, tips och rekommendationer för att utveckla ditt SQL Data Warehouse:

* [Lagrade procedurer][stored procedures]
* [Etiketter][labels]
* [vyer][views]
* [temporära tabeller][temporary tables]
* [Dynamisk SQL][dynamic SQL]
* [slingor][looping]
* [Gruppera efter alternativ][group by options]
* [Variabeltilldelning][variable assignment]

## <a name="next-steps"></a>Nästa steg
Läs mer till referens [SQL Data Warehouse T-SQL-uttryck](sql-data-warehouse-reference-tsql-statements.md).

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
