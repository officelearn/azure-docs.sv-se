---
title: Resurser för att utveckla ett informationslager i Azure Synapse Analytics
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
ms.openlocfilehash: aa0f5fd631dfa3e4deca4853c27a667fcf312fec
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350283"
---
# <a name="design-decisions-and-coding-techniques-for-a-data-warehouse-in-azure-synapse-analytics"></a>Utforma beslut och kodningstekniker för ett informationslager i Azure Synapse Analytics 
 I den här artikeln hittar du ytterligare resurser som hjälper dig att bättre förstå viktiga designbeslut, rekommendationer och kodningstekniker för ett informationslager i Azure Synapse.

## <a name="key-design-decisions"></a>Viktiga designbeslut
Följande artiklar belyser begrepp och designbeslut för att utveckla ett distribuerat informationslager med SQL Analytics-funktionen i Azure Synapse:

* [Anslutningar](sql-data-warehouse-connect-overview.md)
* [samtidighet](resource-classes-for-workload-management.md)
* [transaktioner](sql-data-warehouse-develop-transactions.md)
* [användardefinierade scheman](sql-data-warehouse-develop-user-defined-schemas.md)
* [tabellfördelning](sql-data-warehouse-tables-distribute.md)
* [tabellindex](sql-data-warehouse-tables-index.md)
* [tabellpartitioner](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [statistik](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Utvecklingsrekommendationer och kodningstekniker
Följande artiklar innehåller specifika kodningstekniker, tips och rekommendationer för att utveckla ett informationslager med SQL Analytics:

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
