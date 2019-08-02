---
title: Övervaka XTP minnes lagring | Microsoft Docs
description: Uppskatta och övervaka XTP minnes användning, kapacitet; Lös kapacitets fel 41823
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 1c5a57f634c01cc42934a98decd8f392334dede6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567972"
---
# <a name="monitor-in-memory-oltp-storage"></a>Övervaka minnes intern OLTP-lagring

När du använder [minnes intern OLTP](sql-database-in-memory.md)finns data i minnesoptimerade tabeller och table-variabler i minnes intern OLTP-lagring. Varje Premium-och Affärskritisk tjänst nivå har en maximal lagrings storlek i minnet för OLTP. Se [DTU-baserade resurs gränser – enkel databas](sql-database-dtu-resource-limits-single-databases.md), [DTU-baserade resurs gränser-elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md),[vCore resurs gränser – enkla databaser](sql-database-vcore-resource-limits-single-databases.md) och [vCore resurs gränser – elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).

När den här gränsen har överskridits kan infognings-och uppdaterings åtgärder börja Miss lyckas med fel 41823 för enskilda databaser och fel 41840 för elastiska pooler. Vid det här skedet måste du antingen ta bort data för att frigöra minne eller uppgradera tjänst nivån eller databasens beräknings storlek.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Ta reda på om data passar in i minnet för minnes intern OLTP-lagring

Ta reda på lagrings nivåerna för de olika tjänst nivåerna. Se [DTU-baserade resurs gränser – enkel databas](sql-database-dtu-resource-limits-single-databases.md), [DTU-baserade resurs gränser-elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md),[vCore resurs gränser – enkla databaser](sql-database-vcore-resource-limits-single-databases.md) och [vCore resurs gränser – elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).

Att uppskatta minnes kraven för en minnesoptimerade tabell fungerar på samma sätt för SQL Server som i Azure SQL Database. Ta några minuter för att läsa artikeln på [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Tabell-och tabell variabel rader, samt index, räknas mot Max storleken för användar data. Dessutom behöver ALTER TABLE tillräckligt med utrymme för att skapa en ny version av hela tabellen och dess index.

## <a name="monitoring-and-alerting"></a>Övervakning och avisering
Du kan övervaka minnes lagring i minnet i procent av lagrings utrymmet för din beräknings storlek i [Azure Portal](https://portal.azure.com/): 

1. På bladet databas letar du reda på resurs användnings rutan och klickar på Redigera.
2. Välj måttet `In-Memory OLTP Storage percentage`.
3. Om du vill lägga till en avisering klickar du på rutan resursanvändning för att öppna bladet mått och klickar sedan på Lägg till avisering.

Eller Använd följande fråga för att Visa lagrings belastningen i minnet:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Åtgärda inaktuella minnes fel i OLTP-lagring-fel 41823 och 41840

Genom att gå till lagrings gränsen för minnes intern OLTP i databasen resulterar det i att åtgärderna INSERT, UPDATE, ALTER och CREATE fungerar med fel meddelandet 41823 (för enskilda databaser) eller fel 41840 (för elastiska pooler). Båda felen gör att den aktiva transaktionen avbryts.

Fel meddelandena 41823 och 41840 anger att de minnesoptimerade tabellerna och tabellattribut i databasen eller poolen har nått den maximala lagrings storleken för minnes intern OLTP.

För att lösa det här felet, antingen:

* Ta bort data från de minnesoptimerade tabellerna och eventuellt avlasta data till traditionella, diskbaserade tabeller. eller
* Uppgradera tjänst nivån till en med tillräckligt minnes intern lagring för de data du behöver behålla i minnesoptimerade tabeller.

> [!NOTE] 
> I sällsynta fall kan fel 41823 och 41840 vara tillfälliga, vilket innebär att det finns tillräckligt med minnes intern OLTP-lagring och att försök utföra åtgärden igen. Vi rekommenderar därför att både övervaka den övergripande tillgängliga InMemory OLTP-lagring och att försöka igen när du först påträffar fel 41823 eller 41840. Mer information om logik för omprövning finns i [konflikt identifiering och Omförsöks logik med InMemory OLTP](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Nästa steg
Mer information om övervakning finns i [övervaknings Azure SQL Database med hjälp av vyer för dynamisk hantering](sql-database-monitoring-with-dmvs.md).
