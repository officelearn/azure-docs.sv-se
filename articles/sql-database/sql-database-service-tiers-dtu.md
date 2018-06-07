---
title: Azure SQL Database-tjänsten - DTU | Microsoft Docs
description: Läs mer om servicenivåer för enstaka och pool databaser att tillhandahålla prestandanivåer och lagringsstorlek.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 05/22/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 075e04db27006719ec7d6e08eb2696436d1010f4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648657"
---
# <a name="dtu-based-purchasing-model-for-azure-sql-database"></a>DTU-baserade inköpsmodell för Azure SQL Database 


[Azure SQL Database](sql-database-technical-overview.md) erbjuder två köp modeller för bearbetning, lagring och i/o-resurser: DTU-baserade inköpsmodell och en vCore-baserade inköpsmodell (förhandsversion). Följande tabell och diagram Jämför och jämför dessa två köp modeller.

> [!IMPORTANT]
> VCore-baserade inköpsmodell (förhandsgranskning), se [vCore-baserade inköpsmodell](sql-database-service-tiers-vcore.md)


|**Inköpsmodell**|**Beskrivning**|**Bäst för**|
|---|---|---|
|DTU-baserade modellen|Den här modellen är baserad på ett anpassade mått för bearbetning, lagring och i/o-resurser. Prestandanivåer uttrycks i DTU (Database Transaction Unit) för enkla databaser och eDTU (elastic Database Transaction Unit) för elastiska pooler. Mer information om dtu: er och edtu: er Se [vad är dtu: er och edtu: er](sql-database-what-is-a-dtu.md)?|Bäst för kunder som vill enkla, förkonfigurerade alternativ.| 
|vCore-baserade modellen|Den här modellen kan du skala beräknings-och lagringsresurserna oberoende av varandra. Du kan också använda Azure Hybrid-förmån för SQL Server för att få besparingar.|Bäst för kunder som värdet flexibilitet, kontroll- och insyn.|
||||  

![prismodellen](./media/sql-database-service-tiers/pricing-model.png)

## <a name="dtu-based-purchasing-model"></a>DTU-baserade inköpsmodell

Databasen genomströmning Databastransaktionsenheten (DTU) representerar ett blandat mått av CPU, minne, läser och skriver. DTU-baserade inköpsmodell innehåller en uppsättning förinställda paket med beräkningsresurser och ingår lagringsutrymme till enheten olika nivåer av programprestanda. Kunder som föredrar enkelhet ett förkonfigurerade paket och fasta betalningar varje månad kanske DTU-baserade modell passar för deras behov. I DTU-baserade inköpsmodell kunder kan välja mellan **grundläggande**, **Standard**, och **Premium** tjänstnivåer för både [enkel databaser](sql-database-single-database-resources.md) och [elastiska pooler](sql-database-elastic-pool.md). Tjänstnivåer särskiljs med hjälp av flera olika prestandanivåer med en fast mängd inkluderade lagring, fast kvarhållningsperiod för säkerhetskopieringar och fast pris. Alla tjänstnivåer ger flexibilitet för att ändra prestandanivåer utan driftavbrott. Enskilda databaser och elastiska pooler faktureras timvis baserat på tjänstnivå och prestandanivå servicenivå.

> [!IMPORTANT]
> SQL-hanterade databasinstans stöder för närvarande i förhandsversion inte DTU-baserade inköpsmodell. Mer information finns i [Azure SQL-hanterade databasinstans](sql-database-managed-instance.md). 

## <a name="choosing-a-service-tier-in-the-dtu-based-purchasing-model"></a>Välja en tjänstnivå i DTU-baserade inköpsmodell

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

## <a name="performance-level-and-storage-size-limits-in-the-dtu-based-purchasing-model"></a>Prestanda nivå och lagring storleksgränser i DTU-baserade inköpsmodell

Prestandanivåer uttrycks i DTU (Database Transaction Unit) för enkla databaser och eDTU (elastic Database Transaction Unit) för elastiska pooler. Mer information om dtu: er och edtu: er Se [vad är dtu: er och edtu: er](sql-database-what-is-a-dtu.md)?

### <a name="single-databases"></a>Enkla databaser

||Basic|Standard|Premium|
| :-- | --: | --: | --: | --: |
| Maximalt lagringsutrymme storlek * | 2 GB | 1 TB | 4 TB  | 
| Maximal dtu: er | 5 | 3000 | 4000 | |
||||||

Mer information om specifika prestandanivåer och lagring som kan användas för enskilda databaser finns [SQL Database DTU-baserade gränserna för enskilda databaser](sql-database-dtu-resource-limits.md#single-database-storage-sizes-and-performance-levels).

### <a name="elastic-pools"></a>Elastiska pooler

| | **Basic** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Maximala lagringsstorleken per databas *  | 2 GB | 1 TB | 1 TB | 
| Maximala lagringsstorleken per pool * | 156 GB | 4 TB | 4 TB | 
| Maximal edtu: er per databas | 5 | 3000 | 4000 | 
| Maximal edtu: er per pool | 1600 | 3000 | 4000 | 
| Maximalt antal databaser per pool | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> - Lagringsstorlekar som är större än mängden lagringsutrymme som ingår finns i förhandsversionen, och extra kostnader tillkommer. Mer information finns i [Priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 
> - Mer än 1 TB lagringsutrymme i Premium-nivån är tillgänglig i alla regioner förutom följande: Storbritannien, Nord, västra centrala USA, Storbritannien South2, Kina Öst, USDoDCentral, Tyskland Central, USDoDEast, USA Gov Southwest, oss Gov södra centrala, Tyskland nordöst, Kina Nord, oss Gov Östra. Mer omfattande tillgänglighet planeras. Det maximala lagringsutrymmet på Premium-nivån i andra regioner är begränsat till 1 TB. Se [sidan 11-15 i Aktuella begränsningar](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 
Mer information om specifika prestandanivåer och lagring som kan användas för elastiska pooler finns [SQL Database DTU-baserade gränserna för](sql-database-dtu-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).



## <a name="next-steps"></a>Nästa steg

- Mer information om specifika prestandanivåer och lagring som kan användas, finns [SQL Database DTU-baserade gränserna för](sql-database-dtu-resource-limits.md) och [SQL-databas vCore-baserade gränserna för](sql-database-vcore-resource-limits.md).
- Se [SQL-databasen med vanliga frågor om](sql-database-faq.md) för svar på vanliga frågor och svar.
- Lär dig mer om [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../azure-subscription-service-limits.md)
