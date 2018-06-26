---
title: Azure SQL Database servicenivåer - DTU | Microsoft Docs
description: Läs mer om servicenivåer för enstaka och pool databaser att tillhandahålla prestandanivåer och lagringsstorlek.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: cf17ec616819da94678f2ae4f0f0ca283f99f629
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750432"
---
# <a name="choosing-a-dtu-based-service-tier-performance-level-and-storage-resources"></a>Om du väljer en DTU-baserade tjänstnivå och prestandanivå lagringsresurser 

Tjänstnivåer särskiljs med hjälp av flera olika prestandanivåer med en fast mängd inkluderade lagring, fast kvarhållningsperiod för säkerhetskopieringar och fast pris. Alla tjänstnivåer ger flexibilitet för att ändra prestandanivåer utan driftavbrott. Enskilda databaser och elastiska pooler faktureras timvis baserat på tjänstnivå och prestandanivå servicenivå.

> [!IMPORTANT]
> SQL-hanterade databasinstans stöder för närvarande i förhandsversion inte DTU-baserade inköpsmodell. Mer information finns i [Azure SQL-hanterade databasinstans](sql-database-managed-instance.md). 

## <a name="choosing-a-dtu-based-service-tier"></a>Om du väljer en tjänstnivå med DTU

Om du väljer en tjänstnivå beror huvudsakligen på kontinuitet för företag-, lagrings- och prestandakrav.
||Basic|Standard|Premium|
| :-- | --: |--:| --:| --:| 
|Mål arbetsbelastning|Utveckling och produktion|Utveckling och produktion|Utveckling och produktion||
|SLA för drifttid|99,99 %|99,99 %|99,99 %|Ej tillämpligt i preview|
|Kvarhållning av säkerhetskopior|7 dagar|35 dagar|35 dagar|
|Processor|Låg|Låg, medel, hög|Medelhög och hög|
|I/o-genomströmning (CA) |2.5 IOPS per DTU| 2.5 IOPS per DTU | 48 IOPS per DTU|
|I/o-svarstid (CA)|5 ms (läsa), 10 ms (skriva)|5 ms (läsa), 10 ms (skriva)|2 ms (läsa/skriva)|
|Columnstore-indexering |Gäller inte|S3 och senare|Stöds|
|OLTP i minnet|Gäller inte|Gäller inte|Stöds|
|||||

## <a name="single-database-dtu-and-storage-limits"></a>Enskild databas DTU och Lagringsgränser

Prestandanivåer uttrycks i DTU (Database Transaction Unit) för enkla databaser och eDTU (elastic Database Transaction Unit) för elastiska pooler. Mer information om dtu: er och edtu: er Se [vad är dtu: er och edtu: er](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)?

||Basic|Standard|Premium|
| :-- | --: | --: | --: | --: |
| Maximalt lagringsutrymme | 2 GB | 1 TB | 4 TB  | 
| Maximal dtu: er | 5 | 3000 | 4000 | |
||||||

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Elastisk pool-eDTU-, lagrings- och delade databasen gränser

| | **Basic** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Maximala lagringsstorleken per databas  | 2 GB | 1 TB | 1 TB | 
| Maximala lagringsstorleken per pool | 156 GB | 4 TB | 4 TB | 
| Maximal edtu: er per databas | 5 | 3000 | 4000 | 
| Maximal edtu: er per pool | 1600 | 3000 | 4000 | 
| Maximalt antal databaser per pool | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> Mer än 1 TB lagringsutrymme i Premium-nivån är för närvarande tillgänglig i alla regioner förutom följande: Storbritannien, Nord, västra centrala USA, Storbritannien South2, Kina Öst, USDoDCentral, Tyskland Central, USDoDEast, USA Gov Southwest, oss Gov södra centrala, Tyskland nordöst, Kina Nord USA Gov Öst. Det maximala lagringsutrymmet på Premium-nivån i andra regioner är begränsat till 1 TB. Se [sidan 11-15 i Aktuella begränsningar](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

## <a name="next-steps"></a>Nästa steg

- Mer information om specifika prestandanivåer och lagring som kan användas för enskilda databaser finns [SQL Database DTU-baserade gränserna för enskilda databaser](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels).
- Mer information om specifika prestandanivåer och lagring som kan användas för elastiska pooler finns [SQL Database DTU-baserade gränserna för](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels).