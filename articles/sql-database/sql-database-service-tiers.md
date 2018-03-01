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
ms.date: 02/26/2018
ms.author: carlrab
ms.openlocfilehash: b36af32d900f9426424dd08c43946e7dcb5b39b9
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="what-are-azure-sql-database-service-tiers"></a>Vad är Azure SQL Database servicenivåer?

[Azure SQL Database](sql-database-technical-overview.md) erbjuder **grundläggande**, **Standard**, och **Premium** tjänstnivåer för både [enkel databaser](sql-database-single-database-resources.md) och [elastiska pooler](sql-database-elastic-pool.md). Tjänstnivåer särskiljs främst med hjälp av ett intervall av prestandanivå och lagringsalternativ för storlek och pris.  Alla tjänstnivåer ger flexibilitet för att ändra storlek på prestanda nivå och lagring.  Enskilda databaser och elastiska pooler faktureras timvis baserat på tjänstnivå och prestandanivå lagringsstorlek.   

## <a name="choosing-a-service-tier"></a>Välja tjänstnivå

Om du väljer en tjänstnivå beror huvudsakligen på kontinuitet för företag-, lagrings- och prestandakrav.
| | **Basic** | **Standard** |**Premium**  |
| :-- | --: |--:| --:| --:| 
|Mål arbetsbelastning|Utveckling och produktion|Utveckling och produktion|Utveckling och produktion||
|SLA för drifttid|99,99 %|99,99 %|99,99 %|Ej tillämpligt i preview|
|Kvarhållning av säkerhetskopior|7 dagar|35 dagar|35 dagar|
|Processor|Låg|Låg, medel, hög|Medium, High|
|I/o-genomströmning (CA) |2.5 IOPS per DTU  | 2.5 IOPS per DTU | 48 IOPS per DTU|
|I/o-svarstid (CA)|5 ms (läsa), 10 ms (skriva)|5 ms (läsa), 10 ms (skriva)|2 ms (läsa/skriva)|
|Columnstore indexering och OLTP i minnet|Gäller inte|Gäller inte|Stöds|
|||||

## <a name="performance-level-and-storage-size-limits"></a>Storleksbegränsningar för prestanda och lagring

Prestandanivåer uttrycks i Database Transaction Units (Dtu) för enskilda databaser och elastiska Datatransaktionsenheter (edtu: er) för elastiska pooler. Mer information om dtu: er och edtu: er Se [vad är dtu: er och edtu: er?](sql-database-what-is-a-dtu.md)

### <a name="single-databases"></a>Enkla databaser

|  | **Basic** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Maximalt lagringsutrymme storlek * | 2 GB | 1 TB | 4 TB  | 
| Maximal dtu: er | 5 | 3000 | 4000 | |
||||||

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
> \*Lagringsstorlekar som är större än mängden lagringsutrymme som ingår finns i förhandsversionen, och extra kostnader tillkommer. Mer information finns i [Priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 
>
> \* På Premium-nivån är mer än 1 TB lagringsutrymme för närvarande tillgängligt i följande regioner: Australien, östra, Australien, sydöstra, Brasilien, södra, Kanada, centrala, Kanada, östra, USA, centrala, Frankrike, centrala, Tyskland, centrala, Japan, östra, Japan, västra, Korea, centrala, USA, norra centrala, Nordeuropa, USA, södra centrala, Sydostasien, Storbritannien, södra, Storbritannien, västra, USA, östra 2, USA, västra, Virginia (USA-förvaltad region) och Europa, västra. Se [sidan 11-15 i Aktuella begränsningar](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 

Mer information om specifika prestandanivåer och lagring som kan användas, finns [gränserna för SQL-databas](sql-database-resource-limits.md).


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [enkel databasresurser](sql-database-single-database-resources.md).
- Lär dig mer om elastiska pooler, se [elastiska pooler](sql-database-elastic-pool.md).
- Lär dig mer om [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../azure-subscription-service-limits.md)
* Lär dig mer om [dtu: er och edtu: er](sql-database-what-is-a-dtu.md).
* Lär dig mer om hur du övervakar DTU-användningen, se [övervakning och prestandajustering](sql-database-troubleshoot-performance.md).

