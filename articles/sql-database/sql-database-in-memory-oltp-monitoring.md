---
title: Övervaka XTP InMemory-lagringen | Microsoft Docs
description: Uppskattning och övervaka XTP InMemory-lagringen använder kapacitet. Åtgärda felet kapacitet 41823
services: sql-database
author: jodebrui
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: jodebrui
ms.openlocfilehash: f74c9bf06cad8b84d08baf7a0a0504b9cb729bf4
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308687"
---
# <a name="monitor-in-memory-oltp-storage"></a>Övervakaren i minnet OLTP-lagring
När du använder [Minnesintern OLTP](sql-database-in-memory.md), minnesoptimerade tabeller och tabellvariabler finns i InMemory-OLTP-lagring. Varje tjänstnivå Premium och kritiska företag har en maximal storlek för InMemory-OLTP-lagring. Se [DTU-baserade gränserna - enskild databas](sql-database-dtu-resource-limits-single-databases.md), [DTU-baserade gränserna - elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md),[vCore-baserade gränserna - enskilda databaser](sql-database-vcore-resource-limits-single-databases.md) och [vCore-baserade gränserna - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).

När den här gränsen överskrids, infoga och uppdatera operations startar med fel 41823 för fristående databaser och 41840 för elastiska pooler. Då måste du antingen ta bort data om du vill frigöra minne, eller uppgradera prestandanivån för din databas.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Avgöra om data passar i fästpunkten Minnesintern OLTP-lagring
Fastställa lagring caps på olika tjänstnivåer. Se [DTU-baserade gränserna - enskild databas](sql-database-dtu-resource-limits-single-databases.md), [DTU-baserade gränserna - elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md),[vCore-baserade gränserna - enskilda databaser](sql-database-vcore-resource-limits-single-databases.md) och [vCore-baserade gränserna - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).

Uppskatta minneskrav för en minnesoptimerad tabell fungerar på samma sätt för SQL Server som den har i Azure SQL Database. Ta en stund åt att granska artikeln på [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Tabell och variabeln tabellraderna samt index, räknas in datastorleken max användare. ALTER TABLE måste dessutom tillräckligt med utrymme för att skapa en ny version av hela tabellen och dess index.

## <a name="monitoring-and-alerting"></a>Övervakning och avisering
Du kan övervaka InMemory-lagringsanvändning som en procentandel av fästpunkten lagring för din nivå av prestanda i den [Azure-portalen](https://portal.azure.com/): 

1. Leta upp rutan resurs användning i bladet databasen och klicka på Redigera.
2. Välj mått `In-Memory OLTP Storage percentage`.
3. Lägg till en avisering klickar du på kryssrutan resursanvändningen för att öppna bladet mått och sedan klicka på Lägg till avisering.

Eller Använd följande fråga för att visa lagringsanvändningen i minnet:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Korrigera out av InMemory OLTP lagring situationer - fel 41823 och 41840
Träffa Minnesintern OLTP lagring höljet i din databas leder till att infoga, uppdatera, ALTER och skapa åtgärder misslyckas med felmeddelandet 41823 (för fristående databaser) eller fel 41840 (för elastiska pooler). Fel i båda orsakar den aktiva transaktionen att avbryta.

Felmeddelanden 41823 och 41840 indikera att minnesoptimerade tabeller och tabellvariabler i databasen eller poolen har nått den maximala storleken för InMemory-OLTP-lagring.

Så här löser det här felet kan antingen:

* Ta bort data från de minnesoptimerade tabeller som potentiellt avlastning data till traditionella, diskbaserad tabeller. eller,
* Uppgradera tjänstnivån till en med tillräckligt med InMemory-lagringen för de data som du behöver i minnesoptimerade tabeller.

> [!NOTE] 
> I sällsynta fall kan fel 41823 och 41840 vara tillfälligt, vilket innebär att det finns tillräckligt med lagringsutrymme för OLTP i minnet och du gör om åtgärden lyckas. Vi rekommenderar därför att både Övervakare i minnet OLTP övergripande tillgängligt lagringsutrymme och försök igen när du först stöter på fel 41823 eller 41840. Läs mer om logik [konfliktidentifiering och försök logik med InMemory-OLTP](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Nästa steg
För att övervaka information, se [övervakning Azure SQL Database med dynamiska hanteringsvyer](sql-database-monitoring-with-dmvs.md).
