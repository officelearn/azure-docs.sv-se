---
title: Övervaka XTP Minneslagring
description: Uppskatta och övervaka XTP Användning av lagring i minnet, kapacitet; lösa kapacitetsfel 41823
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 22ff83b1ccd009624082e45073123a45006df70f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209442"
---
# <a name="monitor-in-memory-oltp-storage"></a>BILDSKÄRM I MINNET OLTP-lagring

När du använder [IN-Memory OLTP](sql-database-in-memory.md)finns data i minnesoptimerade tabeller och tabellvariabler i OLTP-lagring i minnet. Varje Premium- och affärskritisk tjänstnivå har en maximal OLTP-lagringsstorlek i minnet. Se [DTU-baserade resursgränser - en databas,](sql-database-dtu-resource-limits-single-databases.md) [DTU-baserade resursgränser - elastiska pooler,](sql-database-dtu-resource-limits-elastic-pools.md)[vCore-baserade resursgränser - enskilda databaser](sql-database-vcore-resource-limits-single-databases.md) och [vCore-baserade resursgränser - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).

När den här gränsen har överskridits kan infognings- och uppdateringsåtgärder börja misslyckas med fel 41823 för enskilda databaser och fel 41840 för elastiska pooler. Då måste du antingen ta bort data för att hämta minne eller uppgradera tjänstnivån eller beräkningsstorleken för databasen.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Ta reda på om data passar inom oltp-lagringstaket för in-memory

Bestäm lagringstaken för de olika tjänstnivåerna. Se [DTU-baserade resursgränser - en databas,](sql-database-dtu-resource-limits-single-databases.md) [DTU-baserade resursgränser - elastiska pooler,](sql-database-dtu-resource-limits-elastic-pools.md)[vCore-baserade resursgränser - enskilda databaser](sql-database-vcore-resource-limits-single-databases.md) och [vCore-baserade resursgränser - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).

Att uppskatta minneskraven för en minnesoptimerad tabell fungerar på samma sätt för SQL Server som i Azure SQL Database. Ta några minuter att granska den artikeln om [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Tabell- och tabellvariabelrader samt index räknas in i den maximala användardatastorleken. Dessutom behöver ALTER TABLE tillräckligt med utrymme för att skapa en ny version av hela tabellen och dess index.

## <a name="monitoring-and-alerting"></a>Övervakning och avisering
Du kan övervaka lagring i minnet som en procentandel av lagringstaket för din beräkningsstorlek i [Azure-portalen:](https://portal.azure.com/) 

1. Leta reda på rutan Resursanvändning på bladet Databas och klicka på Redigera.
2. Välj mått `In-Memory OLTP Storage percentage`.
3. Om du vill lägga till en avisering klickar du på rutan Resursutnyttjande för att öppna bladet Mått och klickar sedan på Lägg till avisering.

Eller använd följande fråga för att visa minnesanvändningen:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Korrigera oltp-lagringssituationer utanför minnet - Fel 41823 och 41840

Genom att trycka på in-memory OLTP-lagringstaket i databasen resulterar i att insert-, UPDATE-, ALTER- och CREATE-åtgärder misslyckas med felmeddelandet 41823 (för enskilda databaser) eller fel 41840 (för elastiska pooler). Båda felen gör att den aktiva transaktionen avbryts.

Felmeddelanden 41823 och 41840 anger att de minnesoptimerade tabellerna och tabellvariablerna i databasen eller poolen har nått maximal OLTP-lagringsstorlek i minnet.

Lös det här felet antingen:

* Ta bort data från de minnesoptimerade tabellerna, vilket kan avlasta data till traditionella diskbaserade tabeller. Eller
* Uppgradera tjänstnivån till en med tillräckligt med minneslagring för de data du behöver för att behålla i minnesoptimerade tabeller.

> [!NOTE] 
> I sällsynta fall kan felen 41823 och 41840 vara tillfälliga, vilket innebär att det finns tillräckligt med tillgänglig OLTP-lagring i minnet och att försöka igen åtgärden lyckas. Vi rekommenderar därför att både övervaka den totala tillgängliga IN-Memory OLTP-lagring och att försöka igen när du först stöter på fel 41823 eller 41840. Mer information om logik för återförsök finns i [Logik för konfliktidentifiering och återförsök med IN-Memory OLTP](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Nästa steg
Övervakningsvägledning finns i [Övervaka Azure SQL Database med hjälp av dynamiska hanteringsvyer](sql-database-monitoring-with-dmvs.md).
