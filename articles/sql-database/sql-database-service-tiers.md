---
title: "Azure SQL Database-tjänsten | Microsoft Docs"
description: "Läs mer om servicenivåer för enstaka och pool databaser att tillhandahålla prestandanivåer och lagringsstorlek."
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 08/20/2017
ms.author: carlrab
ms.openlocfilehash: 55f59fddee008eb42b7252d6368a56873a6abd16
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="what-are-azure-sql-database-service-tiers"></a>Vad är Azure SQL Database servicenivåer

[Azure SQL Database](sql-database-technical-overview.md) erbjuder **grundläggande**, **Standard**, **Premium**, och **Premium RS** tjänstnivåer för båda [enkel databaser](sql-database-single-database-resources.md) och [elastiska pooler](sql-database-elastic-pool.md). Tjänstnivåer särskiljs främst med hjälp av ett intervall av prestandanivå och lagringsalternativ för storlek och pris.  Alla tjänstnivåer ger flexibilitet för att ändra storlek på prestanda nivå och lagring.  Enskilda databaser och elastiska pooler faktureras timvis baserat på tjänstnivå och prestandanivå lagringsstorlek.   

## <a name="choosing-a-service-tier"></a>Välja tjänstnivå

Om du väljer en tjänstnivå beror huvudsakligen på kontinuitet för företag-, lagrings- och prestandakrav.
| | **Basic** | **Standard** |**Premium** |**Premium-RS** |
| :-- | --: |--:| --:| --:| 
|Mål arbetsbelastning|Utveckling och produktion|Utveckling och produktion|Utveckling och produktion|Arbetsbelastning som kan tolerera dataförlust upp till 5 minuter på grund av tjänstfel inträffar|
|SLA för drifttid|99,99 %|99,99 %|99,99 %|Ej tillämpligt i preview|
|Kvarhållning av säkerhetskopior|7 dagar|35 dagar|35 dagar|35 dagar|
|Processor|Låg|Låg, medel, hög|Medelhög och hög|Medel|
|I/o-genomflöde|Låg  | Medel | Storleksordning som är högre än Standard|Samma som Premium|
|I/o-svarstid|Högre än Premium|Högre än Premium|Lägre än Basic och Standard|Samma som Premium|
|Columnstore indexering och OLTP i minnet|Saknas|Saknas|Stöds|Stöds|
|||||

## <a name="performance-level-and-storage-size-limits"></a>Storleksbegränsningar för prestanda och lagring

Prestandanivåer uttrycks i Database Transaction Units (Dtu) för enskilda databaser och elastiska Datatransaktionsenheter (edtu: er) för elastiska pooler. Mer information om dtu: er och edtu: er Se [vad är dtu: er och edtu: er?](sql-database-what-is-a-dtu.md).

### <a name="single-databases"></a>Enkla databaser

|  | **Basic** | **Standard** | **Premium** | **Premium-RS**|
| :-- | --: | --: | --: | --: |
| Maximalt lagringsutrymme storlek * | 2 GB | 1 TB | 4 TB  | 1 TB  |
| Maximal dtu: er | 5 | 3000 | 4000 | 1000 |
||||||

### <a name="elastic-pools"></a>Elastiska pooler

| | **Basic** | **Standard** | **Premium** | **Premium-RS**|
| :-- | --: | --: | --: | --: |
| Maximala lagringsstorleken per databas *  | 2 GB | 1 TB | 1 TB | 1 TB |
| Maximala lagringsstorleken per pool * | 156 GB | 4 TB | 4 TB | 1 TB |
| Maximal edtu: er per databas | 5 | 3000 | 4000 | 1000 |
| Maximal edtu: er per pool | 1600 | 3000 | 4000 | 1000 |
| Maximalt antal databaser per pool | 500  | 500 | 100 | 100 |
||||||

> [!IMPORTANT]
> \*Lagringsstorlekar som är större än mängden lagringsutrymme som ingår finns i förhandsversionen, och extra kostnader tillkommer. Mer information finns i [Priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 
>
> \*I Premium-nivån finns för närvarande mer än 1 TB lagringsutrymme i följande regioner: Östra USA 2, USA, västra, Virginia (USA-förvaltad region), Europa, västra, Centrala Tyskland, Sydostasien, Östra Japan, Australien, östra, Centrala Kanada och Östra Kanada. Se [sidan 11-15 i Aktuella begränsningar](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 

Mer information om specifika prestandanivåer och lagring som kan användas, finns [gränserna för SQL-databas](sql-database-resource-limits.md).


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [enkel databasresurser](sql-database-single-database-resources.md).
- Lär dig mer om elastiska pooler, se [elastiska pooler](sql-database-elastic-pool.md).
- Lär dig mer om [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../azure-subscription-service-limits.md)
* Lär dig mer om [dtu: er och edtu: er](sql-database-what-is-a-dtu.md).
* Lär dig mer om hur du övervakar DTU-användningen, se [övervakning och prestandajustering](sql-database-troubleshoot-performance.md).

