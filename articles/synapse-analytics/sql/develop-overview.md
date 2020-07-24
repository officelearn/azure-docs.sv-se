---
title: Resurser för utveckling av Synapse SQL-funktioner
description: Utvecklings begrepp, design beslut, rekommendationer och kodnings metoder för Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 85b0137f8d89def2f38ffe82199950c9158888d0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87070046"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Utforma beslut och kod metoder för Synapse SQL-funktioner i Azure Synapse Analytics
I den här artikeln hittar du en lista över resurser för SQL-poolen och SQL-funktioner på begäran (för hands version) i Synapse SQL. De rekommenderade artiklarna delas upp i två delar: viktiga design beslut och utvecklings-och kod tekniker.

Syftet med dessa artiklar är att hjälpa dig att utveckla den optimala tekniska metoden för Synapse SQL-komponenter i Synapse Analytics.

## <a name="key-design-decisions"></a>Viktiga design beslut
I artiklarna nedan betonas begrepp och design beslut för Synapse SQL-utveckling:

| Artikel | SQL-pool | SQL på begäran |
| ------- | -------- | ------------- |
| [Anslutningar](connect-overview.md)                    | Yes | Ja |
| [Resurs klasser och samtidighet](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Yes    | Inga |
| [Transaktioner](develop-transactions.md)              | Yes | Inga |
| [Användardefinierade scheman](develop-user-defined-schemas.md) | Yes | Ja |
| [Table distribution](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (Tabelldistribution)                 | Yes | Inga |
| [Table indexes](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (Tabellindex)                           | Yes | Inga |
| [Table-partitioner](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Yes | Inga |
| [Statistik](develop-tables-statistics.md)            | Yes | Ja |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Yes | Inga |
| [Externa tabeller](develop-tables-external-tables.md) | Yes | Ja |
| [CETAS](develop-tables-cetas.md)                     | Yes | Ja |


## <a name="recommendations"></a>Rekommendationer

Nedan hittar du viktiga artiklar som framhäver vissa kodnings tekniker, tips och rekommendationer för utveckling:

| Artikel | SQL-pool | SQL på begäran |
| ------- | -------- | ------------- |
| [Lagrade procedurer](develop-stored-procedures.md)  | Yes                | Inga                      |
| [Etiketter](develop-label.md)                           | Yes                | Inga                      |
| [Vyer](develop-views.md)                             | Yes                | Ja                     |
| [Temporära tabeller](develop-tables-temporary.md)       | Yes                | Ja                     |
| [Dynamisk SQL](develop-dynamic-sql.md)                 | Yes                | Ja                     |
| [Loopar](develop-loops.md)                         | Yes                | Ja                     |
| [Gruppera efter alternativ](develop-group-by-options.md)       | Yes                | Inga                      |
| [Variabeltilldelning](develop-variable-assignment.md) | Yes                | Ja                     |

## <a name="next-steps"></a>Nästa steg
Mer referensinformation finns i [SQL pool T-SQL-uttryck](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

