---
title: Resurser för utveckling av Synapse SQL-funktioner
description: Utvecklings begrepp, design beslut, rekommendationer och kodnings metoder för Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 2a24a61e26bdfaba0619e3965e95b1828380a1c8
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322108"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Utforma beslut och kod metoder för Synapse SQL-funktioner i Azure Synapse Analytics
I den här artikeln hittar du en lista över resurser för dedikerad SQL-pool och Server lös SQL-pool (för hands version) av Synapse SQL. De rekommenderade artiklarna delas upp i två delar: viktiga design beslut och utvecklings-och kod tekniker.

Syftet med dessa artiklar är att hjälpa dig att utveckla den optimala tekniska metoden för Synapse SQL-komponenter i Synapse Analytics.

## <a name="key-design-decisions"></a>Viktiga design beslut
I artiklarna nedan betonas begrepp och design beslut för Synapse SQL-utveckling:

| Artikel | dedikerad SQL-pool | SQL-pool utan Server |
| ------- | -------- | ------------- |
| [Anslutningar](connect-overview.md)                    | Ja | Ja |
| [Resurs klasser och samtidighet](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Ja    | Nej |
| [Transaktioner](develop-transactions.md)              | Ja | Nej |
| [Användardefinierade scheman](develop-user-defined-schemas.md) | Ja | Ja |
| [Table distribution](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (Tabelldistribution)                 | Ja | Nej |
| [Table indexes](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (Tabellindex)                           | Ja | Nej |
| [Table-partitioner](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Ja | Nej |
| [Statistik](develop-tables-statistics.md)            | Ja | Ja |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Ja | Nej |
| [Externa tabeller](develop-tables-external-tables.md) | Ja | Ja |
| [CETAS](develop-tables-cetas.md)                     | Ja | Ja |


## <a name="recommendations"></a>Rekommendationer

Nedan hittar du viktiga artiklar som framhäver vissa kodnings tekniker, tips och rekommendationer för utveckling:

| Artikel | dedikerad SQL-pool | SQL-pool utan Server |
| ------- | -------- | ------------- |
| [Lagrade procedurer](develop-stored-procedures.md)  | Ja                | Nej                      |
| [Etiketter](develop-label.md)                           | Ja                | Nej                      |
| [Vyer](develop-views.md)                             | Ja                | Ja                     |
| [Temporära tabeller](develop-tables-temporary.md)       | Ja                | Ja                     |
| [Dynamisk SQL](develop-dynamic-sql.md)                 | Ja                | Ja                     |
| [Loopar](develop-loops.md)                         | Ja                | Ja                     |
| [Gruppera efter alternativ](develop-group-by-options.md)       | Ja                | Nej                      |
| [Variabeltilldelning](develop-variable-assignment.md) | Ja                | Ja                     |

## <a name="next-steps"></a>Nästa steg
Mer referensinformation finns i [SQL pool T-SQL-uttryck](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

