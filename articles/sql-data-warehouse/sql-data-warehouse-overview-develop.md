---
title: Resurser för att utveckla ett informations lager i Azure | Microsoft Docs
description: Utvecklings begrepp, design beslut, rekommendationer och kodnings tekniker för SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: a78d78618a4cd9bf8d2aaebbd0c0da13697549bc
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479477"
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Design beslut och kod metoder för SQL Data Warehouse
Ta en titt på dessa utvecklings artiklar för att få bättre förståelse för viktiga design beslut, rekommendationer och kodnings metoder för SQL Data Warehouse.

## <a name="key-design-decisions"></a>Viktiga design beslut
Följande artiklar fokuserar på begrepp och design beslut för att utveckla ett distribuerat informations lager med hjälp av SQL Data Warehouse:

* [anslutning][connections]
* [samtidighet][concurrency]
* [transaktioner][transactions]
* [användardefinierade scheman][user-defined schemas]
* [tabell distribution][table distribution]
* [tabell index][table indexes]
* [Table-partitioner][table partitions]
* [CTAS][CTAS]
* [statistik][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Utvecklings rekommendationer och kodnings metoder
De här artiklarna fokuserar på olika kodnings tekniker, tips och rekommendationer för att utveckla SQL Data Warehouse:

* [lagrade procedurer][stored procedures]
* [Etiketter][labels]
* [vyer][views]
* [temporära tabeller][temporary tables]
* [dynamisk SQL][dynamic SQL]
* [loopning][looping]
* [Gruppera efter alternativ][group by options]
* [variabel tilldelning][variable assignment]

## <a name="next-steps"></a>Nästa steg
Mer referensinformation finns i [SQL Data Warehouse T-SQL-uttryck](sql-data-warehouse-reference-tsql-statements.md).

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
