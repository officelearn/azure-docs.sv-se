---
title: Resurser för att utveckla en Synapse SQL-pool i Azure Synapse Analytics
description: Utvecklingskoncept, designbeslut, rekommendationer och kodningstekniker för SQL Data Warehouse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 933a175213afd81c81eb237a2b2dd4c3e24e3315
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633157"
---
# <a name="design-decisions-and-coding-techniques-for-a-synapse-sql-pool-in-azure-synapse-analytics"></a>Utforma beslut och kodningstekniker för en Synapse SQL-pool i Azure Synapse Analytics 
 I den här artikeln hittar du ytterligare resurser som hjälper dig att bättre förstå viktiga designbeslut, rekommendationer och kodningstekniker för en SQL-pool i Azure Synapse.

## <a name="key-design-decisions"></a>Viktiga designbeslut
Följande artiklar belyser begrepp och designbeslut för att utveckla ett distribuerat informationslager med hjälp av SQL-poolfunktionen i Azure Synapse:

* [Anslutningar](sql-data-warehouse-connect-overview.md)
* [samtidighet](resource-classes-for-workload-management.md)
* [Transaktioner](sql-data-warehouse-develop-transactions.md)
* [användardefinierade scheman](sql-data-warehouse-develop-user-defined-schemas.md)
* [tabellfördelning](sql-data-warehouse-tables-distribute.md)
* [tabellindex](sql-data-warehouse-tables-index.md)
* [tabellpartitioner](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [Statistik](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Utvecklingsrekommendationer och kodningstekniker
Följande artiklar innehåller specifika kodningstekniker, tips och rekommendationer för att utveckla en SQL-pool:

* [lagrade förfaranden](sql-data-warehouse-develop-stored-procedures.md)
* [Etiketter](sql-data-warehouse-develop-label.md)
* [Visningar](sql-data-warehouse-develop-views.md)
* [temporära tabeller](sql-data-warehouse-tables-temporary.md)
* [dynamisk SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [Looping](sql-data-warehouse-develop-loops.md)
* [gruppera efter alternativ](sql-data-warehouse-develop-group-by-options.md)
* [variabel tilldelning](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Nästa steg
Mer referensinformation finns i [T-SQL-uttryck](sql-data-warehouse-reference-tsql-statements.md).
