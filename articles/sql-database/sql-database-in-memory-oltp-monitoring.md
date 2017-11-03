---
title: "Övervaka XTP InMemory-lagringen | Microsoft Docs"
description: "Uppskattning och övervaka XTP InMemory-lagringen använder kapacitet. Åtgärda felet kapacitet 41823"
services: sql-database
documentationcenter: 
author: jodebrui
manager: jhubbard
editor: 
ms.assetid: b617308e-692c-4938-8fa2-070034a3ecef
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jodebrui
ms.openlocfilehash: 613a9ced91d71cc9a65ea67e6ede1a78a03b4bd5
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="monitor-in-memory-oltp-storage"></a>Övervakaren i minnet OLTP-lagring
När du använder [Minnesintern OLTP](sql-database-in-memory.md), minnesoptimerade tabeller och tabellvariabler finns i InMemory-OLTP-lagring. Varje premiumnivån har en maximal Minnesintern OLTP lagringsstorlek, som dokumenteras i [enkel gränserna för databasen](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels) och [gränserna för elastiska poolen](sql-database-resource-limits.md#elastic-pool-change-storage-size). När den här gränsen överskrids, infoga och uppdatera operations startar (med fel 41823). Då måste du antingen ta bort data om du vill frigöra minne, eller uppgradera prestandanivån för din databas.

## <a name="determine-whether-data-will-fit-within-the-in-memory-storage-cap"></a>Avgöra om data får plats inom fästpunkten InMemory-lagring
Fastställa lagring caps på olika tjänstnivåer i Premium. Se [enkel gränserna för databasen](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels) och [gränserna för elastiska poolen](sql-database-resource-limits.md#elastic-pool-change-storage-size).

Uppskatta minneskrav för en minnesoptimerad tabell fungerar på samma sätt för SQL Server som den har i Azure SQL Database. Ta en stund åt att granska avsnittet på [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Observera att den tabell och variabeln rader i tabellen, samt index, räknas in datastorleken max användare. ALTER TABLE måste dessutom tillräckligt med utrymme för att skapa en ny version av hela tabellen och dess index.

## <a name="monitoring-and-alerting"></a>Övervakning och avisering
Du kan övervaka InMemory-lagringsanvändning som en procentandel av fästpunkten lagring för din nivå av prestanda i den [Azure-portalen](https://portal.azure.com/): 

1. Leta upp rutan resurs användning i bladet databasen och klicka på Redigera.
2. Välj mått `In-Memory OLTP Storage percentage`.
3. Lägg till en avisering klickar du på kryssrutan resursanvändningen för att öppna bladet mått och sedan klicka på Lägg till avisering.

Eller Använd följande fråga för att visa i minnet lagringsanvändningen:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-memory-situations---error-41823"></a>Korrigera minnet är slut situationer - fel 41823
Kör minnet är slut resultat i INSERT-, UPDATE- och skapa åtgärder misslyckas med felmeddelandet 41823.

Felmeddelandet 41823 innebär att minnesoptimerade tabeller och tabellvariabler har överskridit den maximala storleken.

Så här löser det här felet kan antingen:

* Ta bort data från de minnesoptimerade tabeller som potentiellt avlastning data till traditionella, diskbaserad tabeller. eller,
* Uppgradera tjänstnivån till en med tillräckligt med InMemory-lagringen för de data som du behöver i minnesoptimerade tabeller.

## <a name="next-steps"></a>Nästa steg
För att övervaka information, se [övervakning Azure SQL Database med dynamiska hanteringsvyer](sql-database-monitoring-with-dmvs.md).
