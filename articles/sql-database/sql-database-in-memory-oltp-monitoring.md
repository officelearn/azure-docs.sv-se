---
title: Övervaka XTP minnesintern lagring | Microsoft Docs
description: Uppskattning och övervaka XTP minnesintern lagring använder, kapacitet. Åtgärda felet kapacitet 41823
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jodebrui
ms.author: jodebrui
ms.reviewer: genemi
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: ac7b568d95b9a2c382b1c167965942f0733012c4
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874931"
---
# <a name="monitor-in-memory-oltp-storage"></a>Övervaka Minnesintern OLTP-lagring
När du använder [In-Memory OLTP](sql-database-in-memory.md), finns data i minnesoptimerade tabeller och tabellvariabler i InMemory-OLTP-lagring. Varje Premium och affärskritisk tjänstenivå har en maximal storlek för In-Memory OLTP-lagring. Se [DTU-baserade resursbegränsningar - enkel databas](sql-database-dtu-resource-limits-single-databases.md), [DTU-baserade resursbegränsningar - elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md),[vCore-baserade resursbegränsningar - enskilda databaser](sql-database-vcore-resource-limits-single-databases.md) och [vCore-baserade resursbegränsningar - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).

När den här gränsen överskrids, infognings- och åtgärder kan börjar misslyckas med fel 41823 för enskilda databaser och 41840 för elastiska pooler. I det här läget måste du antingen ta bort data för att frigöra minne, eller uppgradera tjänstnivån eller beräkna databasens storlek.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Avgöra om data passar In-Memory OLTP-lagringstak
Kontrollera storage-begränsningar för olika tjänstnivåer. Se [DTU-baserade resursbegränsningar - enkel databas](sql-database-dtu-resource-limits-single-databases.md), [DTU-baserade resursbegränsningar - elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md),[vCore-baserade resursbegränsningar - enskilda databaser](sql-database-vcore-resource-limits-single-databases.md) och [vCore-baserade resursbegränsningar - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).

Uppskatta minneskraven för en minnesoptimerad tabell fungerar på samma sätt för SQL Server som den har i Azure SQL Database. Ta en stund att granska den här artikeln på [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Tabell- och variabeln tabellrader samt index, räknas i förhållande datastorlek max användare. ALTER TABLE måste dessutom ha tillräckligt med utrymme för att skapa en ny version av hela tabellen och dess index.

## <a name="monitoring-and-alerting"></a>Övervakning och avisering
Du kan övervaka användning av InMemory-minne som en procentandel av lagringstak för beräkningsstorleken på din i den [Azure-portalen](https://portal.azure.com/): 

1. På bladet Database hitta resursen användning rutan och klicka på Redigera.
2. Välj måtten som `In-Memory OLTP Storage percentage`.
3. Klicka på rutan resursanvändningen för att öppna bladet mått för att lägga till en avisering, och sedan på Lägg till avisering.

Eller Använd följande fråga för att visa lagringsanvändning i minnet:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Korrigera ut av InMemory OLTP-lagring situationer - fel 41823 och 41840
Träffa InMemory-OLTP-lagringstak i databasresultat i INSERT, uppdatera, ändra och skapa-åtgärder misslyckas med felmeddelandet 41823 (för enkla databaser) eller fel 41840 (för elastiska pooler). Båda fel orsakar den aktiva transaktionen att avbryta.

Felmeddelanden 41823 och 41840 tyda på att de minnesoptimerade tabeller och tabellvariabler i databasen eller poolen har nått den maximala storleken för In-Memory OLTP-lagring.

Du löser det här felet, antingen:

* Ta bort data från de minnesoptimerade tabellerna, potentiellt avlastning av data till traditionella, diskbaserade tabeller. eller,
* Uppgradera tjänstnivån till en med tillräckligt med minnesintern lagring för de data som du behöver i minnesoptimerade tabeller.

> [!NOTE] 
> I sällsynta fall kan fel 41823 och 41840 vara tillfälligt, vilket innebär att det finns tillräckligt med tillgängliga InMemory-OLTP-lagring och gör om åtgärden lyckas. Vi rekommenderar därför att både övervaka den övergripande tillgängliga InMemory-OLTP-lagringen och försök igen när den först påträffar fel 41823 eller 41840. Läs mer om logik för omprövning, [konfliktidentifiering och Omprövningslogik med In-Memory OLTP](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Nästa steg
Övervaka vägledning finns i [övervaka Azure SQL Database med dynamiska hanteringsvyer](sql-database-monitoring-with-dmvs.md).
