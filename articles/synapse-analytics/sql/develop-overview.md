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
ms.openlocfilehash: f4c61b43855e4a845956a937d9d9cfe738d6f47a
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032953"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Utforma beslut och kod metoder för Synapse SQL-funktioner i Azure Synapse Analytics
I den här artikeln hittar du en lista över resurser för SQL-poolen och SQL-funktioner på begäran (för hands version) i Synapse SQL. De rekommenderade artiklarna delas upp i två delar: viktiga design beslut och utvecklings-och kod tekniker.

Syftet med dessa artiklar är att hjälpa dig att utveckla den optimala tekniska metoden för Synapse SQL-komponenter i Synapse Analytics.

## <a name="key-design-decisions"></a>Viktiga design beslut
I artiklarna nedan betonas begrepp och design beslut för Synapse SQL-utveckling:

| Artikel | SQL-pool | SQL på begäran |
| ------- | -------- | ------------- |
| [Anslutningar](connect-overview.md)                    | Ja | Ja |
| [Resurs klasser och samtidighet](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Ja    | Inga |
| [Transaktioner](develop-transactions.md)              | Ja | Inga |
| [Användardefinierade scheman](develop-user-defined-schemas.md) | Ja | Ja |
| [Table distribution](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (Tabelldistribution)                 | Ja | Inga |
| [Table indexes](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (Tabellindex)                           | Ja | Inga |
| [Table-partitioner](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Ja | Inga |
| [Statistik](develop-tables-statistics.md)            | Ja | Ja |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Ja | Inga |
| [Externa tabeller](develop-tables-external-tables.md) | Ja | Ja |
| [CETAS](develop-tables-cetas.md)                     | Ja | Ja |


## <a name="recommendations"></a>Rekommendationer

Nedan hittar du viktiga artiklar som framhäver vissa kodnings tekniker, tips och rekommendationer för utveckling:

| Artikel | SQL-pool | SQL på begäran |
| ------- | -------- | ------------- |
| [Lagrade procedurer](develop-stored-procedures.md)  | Ja                | Inga                      |
| [Etiketter](develop-label.md)                           | Ja                | Inga                      |
| [Vyer](develop-views.md)                             | Ja                | Ja                     |
| [Temporära tabeller](develop-tables-temporary.md)       | Ja                | Ja                     |
| [Dynamisk SQL](develop-dynamic-sql.md)                 | Ja                | Ja                     |
| [Loopar](develop-loops.md)                         | Ja                | Ja                     |
| [Gruppera efter alternativ](develop-group-by-options.md)       | Ja                | Inga                      |
| [Variabeltilldelning](develop-variable-assignment.md) | Ja                | Ja                     |

## <a name="next-steps"></a>Nästa steg
Mer referensinformation finns i [SQL pool T-SQL-uttryck](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

