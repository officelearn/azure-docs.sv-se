---
title: Azure SQL Database-servicenivåerna - DTU | Microsoft Docs
description: Läs om tjänstnivåer för en enskild och poolen databaser att tillhandahålla prestandanivåer och lagringsstorlekar.
services: sql-database
author: sachinpMSFT
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 5d16763fc8f3331082b98216d25190b945d95b60
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39411828"
---
# <a name="choosing-a-dtu-based-service-tier-performance-level-and-storage-resources"></a>Välja en DTU-baserade tjänstnivå och prestandanivå lagringsresurser 

Tjänstnivåer åtskiljs av flera olika prestandanivåer med en fast mängd lagringsutrymme, fast kvarhållningsperiod för säkerhetskopior och fast pris. Alla tjänstnivåer ger flexibilitet för att byta prestandanivå utan avbrott. Enkla databaser och elastiska pooler faktureras per timme baserat på tjänstnivå och prestanda.

> [!IMPORTANT]
> SQL Database Managed Instance är stöder för närvarande i förhandsversionen inte en DTU-baserade inköpsmodellen. Mer information finns i [Azure SQL Database Managed Instance](sql-database-managed-instance.md). 

## <a name="choosing-a-dtu-based-service-tier"></a>Välja DTU-baserade tjänstnivå

Välja tjänstnivå beror huvudsakligen på kontinuitet för företag-, lagrings- och prestandakrav.
||Basic|Standard|Premium|
| :-- | --: |--:| --:| --:| 
|Målarbetsbelastning|Utveckling och produktion|Utveckling och produktion|Utveckling och produktion||
|SLA för drifttid|99,99 %|99,99 %|99,99 %|Ej tillämpligt i förhandsversionen|
|Kvarhållning av säkerhetskopior|7 dagar|35 dagar|35 dagar|
|Processor|Låg|Låg, medel, hög|Medel, hög|
|I/o-genomströmning (ungefärlig) |2.5 IOPS per DTU| 2.5 IOPS per DTU | 48 IOPS per DTU|
|I/o-svarstid (ungefärlig)|5 ms (läsa), 10 ms (skriva)|5 ms (läsa), 10 ms (skriva)|2 ms (läsa/skriva)|
|Kolumnlagringsindexering |Gäller inte|S3 och senare|Stöds|
|Minnesintern OLTP|Gäller inte|Gäller inte|Stöds|
|||||

## <a name="single-database-dtu-and-storage-limits"></a>Enkel databas DTU och Lagringsgränser

Prestandanivåer uttrycks i DTU (Database Transaction Unit) för enkla databaser och eDTU (elastic Database Transaction Unit) för elastiska pooler. Mer information om dtu: er och edtu: er finns i [vad är dtu: er och edtu: er](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)?

||Basic|Standard|Premium|
| :-- | --: | --: | --: | --: |
| Lagringsstorlek | 2 GB | 1 TB | 4 TB  | 
| Maximala dtu: er | 5 | 3000 | 4000 | |
||||||

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Elastisk pool-eDTU-, lagrings- och databas i pool gränser

| | **Basic** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Maximala lagringsstorleken per databas  | 2 GB | 1 TB | 1 TB | 
| Maximala lagringsstorleken per pool | 156 GB | 4 TB | 4 TB | 
| Högsta edtu: er per databas | 5 | 3000 | 4000 | 
| Högsta edtu: er per pool | 1600 | 3000 | 4000 | 
| Högsta antal databaser per pool | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> Mer än 1 TB av lagringsutrymmet på Premium-nivån är för närvarande tillgängligt i alla regioner förutom följande: västra centrala USA, Kina, östra, centrala USDoDCentral, Tyskland, USDoDEast USA-förvaltad region sydväst, USGov-Iowa, Tyskland, nordöstra, Kina, norra. Det maximala lagringsutrymmet på Premium-nivån i andra regioner är begränsat till 1 TB. Se [sidan 11-15 i Aktuella begränsningar](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

## <a name="next-steps"></a>Nästa steg

- Mer information om specifika prestandanivåer och lagring som kan användas för enskilda databaser finns [SQL Database DTU-baserade resursbegränsningar för enskilda databaser](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels).
- Mer information om specifika prestandanivåer och lagring som kan användas för elastiska pooler finns [SQL Database DTU-baserade resursbegränsningar](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels).
