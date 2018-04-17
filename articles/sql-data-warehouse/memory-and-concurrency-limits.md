---
title: Minne och samtidighet gränser - Azure SQL Data Warehouse | Microsoft Docs
description: Visa gränserna minne och samtidighet som allokerats till de olika prestandanivåer och resursklasser i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.topic: conceptual
ms.component: manage
ms.date: 04/11/2018
ms.author: kevin
ms.reviewer: jrj
ms.openlocfilehash: 096dd5f1bac87e1442963b62067896b7abf20a8e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Minne och samtidighet gränser för Azure SQL Data Warehouse
Visa gränserna minne och samtidighet som allokerats till de olika prestandanivåer och resursklasser i Azure SQL Data Warehouse. Mer information och tillämpa dessa funktioner på din plan för hantering av arbetsbelastning finns [resursklasser för hantering av arbetsbelastning](resource-classes-for-workload-management.md). 

## <a name="performance-tiers"></a>Prestandanivåer

SQL Data Warehouse erbjuder två prestandanivåer som är optimerade för analytiska arbetsbelastningar. En prestandanivå är ett alternativ som anger hur ditt data warehouse. Det här alternativet är en av de första val som du gör när du skapar ett datalager.  

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T140/player]

- På **prestandanivån Optimerat för elasticitet** avgränsas lagren för beräkning och lagring i arkitekturen. Det här alternativet är perfekt för arbetsbelastningar som kan dra full nytta av avgränsningen mellan beräkning och lagring genom frekvent skalning för bättre stöd för korta perioder med hög aktivitet. Den här beräkningsnivån har det lägsta startpriset och stöd för skalning som omfattar merparten av kundens arbetsbelastningar.

- På **prestandanivån Optimerat för beräkning** används den senaste Azure-maskinvaran för att införa ett nytt NVMe-Solid State Disk-cacheminne som lagrar de data som ofta används nära processorerna. Den här prestandanivån är perfekt för komplexa frågor eftersom all I/O sparas lokalt på beräkningslagret genom att lagringen automatiskt nivåindelas. Dessutom har kolumnlagringen förbättrats för att lagra obegränsad data i ditt SQL Data Warehouse. Prestandanivån Optimerat för beräkning ger störst skalbarhet och hjälper dig att skala upp till 30 000 cDWU (informationslagerenheter för beräkning). Välj den här nivån för arbetsbelastningar som kräver kontinuerlig och blixtsnabb prestanda.

## <a name="data-warehouse-limits"></a>Databegränsningar för datalager
Följande tabeller visar den maximala kapaciteten för datalagret på olika prestandanivåer. Om du vill ändra prestandanivå [skala beräknings - portal](quickstart-scale-compute-portal.md).

### <a name="optimized-for-elasticity"></a>Optimerad för elasticitet

Servicenivåer för optimerad för elasticitet prestanda nivå mellan DW100 och DW6000. 

| Prestandanivå | Maximalt antal samtidiga frågor | Compute-noder | Distributioner per Compute-nod | Maximalt minne per distribution (MB) | Maximalt minne per datalager (GB) |
|:-------------:|:----------------------:|:-------------:|:------------------------------:|:--------------------------------:|:----------------------------------:|
| DW100         | 4                      | 1             | 60                             | 400                              |  24                                |
| DW200         | 8                      | 2             | 30                             | 800                              |  48                                |
| DW300         | 12                     | 3             | 20                             | 1,200                            |  72                                |
| DW400         | 16                     | 4             | 15                             | 1,600                            |  96                                |
| DW500         | 20                     | 5             | 12                             | 2,000                            | 120                                |
| DW600         | 24                     | 6             | 10                             | 2,400                            | 144                                |
| DW1000        | 32                     | 10            | 6                              | 4,000                            | 240                                |
| DW1200        | 32                     | 12            | 5                              | 4,800                            | 288                                |
| DW1500        | 32                     | 15            | 4                              | 6,000                            | 360                                |
| DW2000        | 32                     | 20            | 3                              | 8,000                            | 480                                |
| DW3000        | 32                     | 30            | 2                              | 12,000                           | 720                                |
| DW6000        | 32                     | 60            | 1                              | 24,000                           | 1440                               |

### <a name="optimized-for-compute"></a>Optimerad för beräkning

Optimerad för beräkning prestandanivån innehåller 2,5 x mer minne per fråga än optimerad för elasticitet prestandanivå. Den här extra minne hjälper optimerad för beräkning prestandanivån dess snabb prestanda.  Prestandanivåer för optimerad för beräkning prestanda nivå mellan DW1000c och DW30000c. 

| Prestandanivå | Maximalt antal samtidiga frågor | Compute-noder | Distributioner per Compute-nod | Maximalt minne per distribution (GB) | Maximalt minne per datalager (GB) |
|:-------------:|:----------------------:|:-------------:|:------------------------------:|:--------------------------------:|:----------------------------------:|
| DW1000c       | 32                     | 2             | 30                             |  10                              |   600                              |
| DW1500c       | 32                     | 3             | 20                             |  15                              |   900                              |
| DW2000c       | 32                     | 4             | 15                             |  20                              |  1200                              |
| DW2500c       | 32                     | 5             | 12                             |  25                              |  1500                              |
| DW3000c       | 32                     | 6             | 10                             |  30                              |  1800                              |
| DW5000c       | 32                     | 10            | 6                              |  50                              |  3000                              |
| DW6000c       | 32                     | 12            | 5                              |  60                              |  3600                              |
| DW7500c       | 32                     | 15            | 4                              |  75                              |  4500                              |
| DW10000c      | 32                     | 20            | 3                              | 100                              |  6000                              |
| DW15000c      | 32                     | 30            | 2                              | 150                              |  9000                              |
| DW30000c      | 32                     | 60            | 1                              | 300                              | 18000                              |

Den maximala cDWU är DW30000c som har 60 datornoder och en distributionsplats per Compute-nod. Till exempel bearbetar ett 600 TB data warehouse på DW30000c cirka 10 TB per Compute-nod.


## <a name="concurrency-maximums"></a>Concurrency maxkapacitet
För att säkerställa att varje fråga har tillräckligt med resurser för att köras effektivt SQL Data Warehouse beräkna spårar resursutnyttjande genom att tilldela varje fråga samtidighet platser. Systemet samlar frågor i en kö där de vänta tills det finns tillräckligt med [samtidighet fack](resource-classes-for-workload-management.md#concurrency-slots) är tillgängliga. 

Concurrency fack avgör också CPU prioritering. Mer information finns i [analysera din arbetsbelastning](analyze-your-workload.md)

### <a name="optimized-for-compute"></a>Optimerad för beräkning
I följande tabell visas de maximalt antal samtidiga frågor och samtidighet fack för varje [dynamiska resursklassen](resource-classes-for-workload-management.md). Dessa gäller för optimerad för beräkning prestandanivå.

**Dynamisk resursklasser**
| Prestandanivå | Maximalt antal samtidiga frågor | Concurrency-fack som är tillgängliga | Platser som används av smallrc | Platser som används av mediumrc | Platser som används av largerc | Platser som används av xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW1000c       | 32                         |   40                        | 1                     |  8                     |  16                   |  32                    |
| DW1500c       | 32                         |   60                        | 1                     |  8                     |  16                   |  32                    |
| DW2000c       | 32                         |   80                        | 1                     | 16                     |  32                   |  64                    |
| DW2500c       | 32                         |  100                        | 1                     | 16                     |  32                   |  64                    |
| DW3000c       | 32                         |  120                        | 1                     | 16                     |  32                   |  64                    |
| DW5000c       | 32                         |  200                        | 1                     | 32                     |  64                   | 128                    |
| DW6000c       | 32                         |  240                        | 1                     | 32                     |  64                   | 128                    |
| DW7500c       | 32                         |  300                        | 1                     | 64                     | 128                   | 128                    |
| DW10000c      | 32                         |  400                        | 1                     | 64                     | 128                   | 256                    |
| DW15000c      | 32                         |  600                        | 1                     | 64                     | 128                   | 256                    |
| DW30000c      | 32                         | 1200                        | 1                     | 64                     | 128                   | 256                    |

**Statisk resursklasser**

I följande tabell visas de maximalt antal samtidiga frågor och samtidighet fack för varje [Statiska resursklassen](resource-classes-for-workload-management.md).  

| Servicenivå | Maximalt antal samtidiga frågor | Concurrency-fack som är tillgängliga |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2000c       | 32                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 32                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 32                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW5000c       | 32                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 32                         |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 32                         |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 32                         |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 32                         |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 32                         | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

### <a name="optimized-for-elasticity"></a>Optimerad för elasticitet
I följande tabell visas de maximalt antal samtidiga frågor och samtidighet fack för varje [dynamiska resursklassen](resource-classes-for-workload-management.md).  Dessa gäller för optimerad för elasticitet prestandanivå.

**Dynamisk resursklasser**

| Servicenivå | Maximalt antal samtidiga frågor | Concurrency-fack som är tillgängliga | smallrc | mediumrc | largerc | xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:-------:|:--------:|:-------:|:--------:|
| DW100         |  4                         |   4                         | 1       |  1       |  2      |   4      |
| DW200         |  8                         |   8                         | 1       |  2       |  4      |   8      |
| DW300         | 12                         |  12                         | 1       |  2       |  4      |   8      |
| DW400         | 16                         |  16                         | 1       |  4       |  8      |  16      |
| DW500         | 20                         |  20                         | 1       |  4       |  8      |  16      |
| DW600         | 24                         |  24                         | 1       |  4       |  8      |  16      |
| DW1000        | 32                         |  40                         | 1       |  8       | 16      |  32      |
| DW1200        | 32                         |  48                         | 1       |  8       | 16      |  32      |
| DW1500        | 32                         |  60                         | 1       |  8       | 16      |  32      |
| DW2000        | 32                         |  80                         | 1       | 16       | 32      |  64      |
| DW3000        | 32                         | 120                         | 1       | 16       | 32      |  64      |
| DW6000        | 32                         | 240                         | 1       | 32       | 64      | 128      |

**Statisk resursklasser** i följande tabell visas de maximalt antal samtidiga frågor och samtidighet fack för varje [statiska resursklassen](resource-classes-for-workload-management.md).  Dessa gäller för optimerad för elasticitet prestandanivå.

| Servicenivå | Maximalt antal samtidiga frågor | Maximal samtidighet fack |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:-------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100         | 4                          |   4                       | 1         | 2          | 4          | 4          |  4         |  4         |  4         |   4        |
| DW200         | 8                          |   8                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW300         | 12                         |  12                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400         | 16                         |  16                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500         | 20                         |  20                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW600         | 24                         |  24                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000        | 32                         |  40                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1200        | 32                         |  48                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500        | 32                         |  60                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000        | 32                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 32                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 32                         | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

När något av dessa tröskelvärden uppfylls är nya frågor i kö och utförs på grundval först in, först ut.  En frågor har slutförts, och antalet frågor och fack faller under den Frigör SQL Data Warehouse köade frågor. 

## <a name="next-steps"></a>Nästa steg

Mer information om hur man utnyttjar resursklasser för att optimera din arbetsbelastning ytterligare Läs följande artiklar:
* [Resursklasser för hantering av arbetsbelastning](resource-classes-for-workload-management.md)
* [Analysera din arbetsbelastning](analyze-your-workload.md)

