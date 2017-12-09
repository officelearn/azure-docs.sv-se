---
title: "Azure SQL Data Warehouse prestandanivåer | Microsoft Docs"
description: "Introduktion till elasticitet och beräknings-optimerad prestandanivåer tillgängliga i Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 11/10/2017
ms.author: jrj;barbkess
ms.openlocfilehash: c403a73d03fd5152e2c0617b3e3784926c28f5c3
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2017
---
# <a name="azure-sql-data-warehouse-performance-tiers-preview"></a>Azure SQL Data Warehouse prestandanivåer (förhandsgranskning)
SQL Data Warehouse erbjuder två prestandanivåer som är optimerade för analytiska arbetsbelastningar. Den här artikeln förklarar begreppet prestandanivåer för att välja den lämpligaste prestandanivån för din arbetsbelastning. 


## <a name="what-is-a-performance-tier"></a>Vad är en prestandanivå?
En prestandanivå är ett alternativ som anger hur ditt data warehouse. Det här alternativet är en av de första val som du gör när du skapar ett datalager.  

- På **prestandanivån Optimerat för elasticitet** avgränsas lagren för beräkning och lagring i arkitekturen. Det här alternativet är perfekt för arbetsbelastningar som kan dra full nytta av avgränsningen mellan beräkning och lagring genom frekvent skalning för bättre stöd för korta perioder med hög aktivitet. Den här beräkningsnivån har det lägsta startpriset och stöd för skalning som omfattar merparten av kundens arbetsbelastningar.

- På **prestandanivån Optimerat för beräkning** används den senaste Azure-maskinvaran för att införa ett nytt NVMe-Solid State Disk-cacheminne som lagrar de data som ofta används nära processorerna. Den här prestandanivån är perfekt för komplexa frågor eftersom all I/O sparas lokalt på beräkningslagret genom att lagringen automatiskt nivåindelas. Dessutom har kolumnlagringen förbättrats för att lagra obegränsad data i ditt SQL Data Warehouse. Prestandanivån Optimerat för beräkning ger störst skalbarhet och hjälper dig att skala upp till 30 000 cDWU (informationslagerenheter för beräkning). Välj den här nivån för arbetsbelastningar som kräver kontinuerlig och blixtsnabb prestanda.

## <a name="service-levels"></a>Servicenivåer
Den artikel för servicenivåmål (SNM) är skalbarhet-inställning som anger nivån kostnad och prestanda för ditt informationslager. Servicenivåer för optimerad för beräkning prestanda nivå skala mäts i beräkning informationslagerenheter (cDWU), till exempel DW2000c. Optimerad för elasticitet servicenivåer mäts i dwu: er, till exempel DW2000. Mer information finns i [vad är en data warehouse-enhet?](what-is-a-data-warehouse-unit-dwu-cdwu.md)

I T-SQL anger inställningen SERVICE_OBJECTIVE servicenivån och nivå för prestanda för ditt informationslager.

```sql
--Optimized for Elasticity
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Optimized for Compute
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="memory-maximums"></a>Minne maxkapacitet
Prestandanivåer har olika minne profiler som översätter till en annan mängd minne per fråga. Optimerad för beräkning prestandanivån innehåller 2,5 x mer minne per fråga än optimerad för elasticitet prestandanivå. Den här extra minne hjälper optimerad för beräkning prestandanivån dess blixtsnabb snabb prestanda. Ytterligare minne per fråga kan du köra flera frågor samtidigt eftersom frågor kan använda lägre [resursklasser](resource-classes-for-workload-management.md). 

### <a name="optimized-for-elasticity"></a>Optimerat för elasticitet

Servicenivåer för optimerad för elasticitet prestanda nivå mellan DW100 och DW6000. 

| Servicenivå | Maximalt antal samtidiga frågor | Compute-noder | Distributioner per Compute-nod | Maximalt minne per distribution (MB) | Maximalt minne per datalager (GB) |
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

Servicenivåer för optimerad för beräkning prestanda nivå mellan DW1000c och DW30000c. 

| Servicenivå | Maximalt antal samtidiga frågor | Compute-noder | Distributioner per Compute-nod | Maximalt minne per distribution (GB) | Maximalt minne per datalager (GB) |
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
SQL Data Warehouse ger branschledande samtidighet på ett enda datalager. För att säkerställa att varje fråga har tillräckligt med resurser för att köras effektivt systemet beräkna spårar resursutnyttjande genom att tilldela varje fråga samtidighet platser. Systemet samlar frågor i en kö där de vänta tills tillräckligt med samtidighet platser är tillgängliga. 

Concurrency fack avgör också CPU prioritering. Mer information finns i [analysera din arbetsbelastning](analyze-your-workload.md)

### <a name="concurrency-slots"></a>Concurrency-platser
Concurrency kortplatser är ett bekvämt sätt att spåra resurserna som är tillgängliga för frågekörning. De har liknande biljetter som du köper om du vill reservera platser på en samklang eftersom sittplatser är begränsad. På samma sätt har SQL Data Warehouse ett begränsat antal beräkningsresurser. Frågor reservera beräkningsresurser genom att skaffa samtidighet platser. Innan en fråga kan börja köra, måste den kunna reservera tillräckligt med samtidighet platser. När en fråga är klar, släpper samtidighet platserna. 

* Den optimerade för elasticitet prestandanivån skalas till 240 samtidighet platser.
* Den optimerade för beräkning prestanda nivå kan byggas ut till 1200 samtidighet platser.

Varje fråga förbrukar noll, en eller flera samtidiga platser. System frågor och vissa trivial frågor du inte använda alla platser. Som standard kräver frågor som styrs av resursklasser en concurrency-plats. Mer komplexa frågor kan kräva ytterligare samtidighet platser.  

- Frågor som körs med 10 samtidiga platser kan komma åt 5 gånger fler beräkningsresurser än en fråga som körs med 2 samtidighet platser.
- Om varje fråga kräver 10 samtidiga platser och det finns 40 samtidighet platser, kan endast 4 frågor köras samtidigt.
 
Endast resurs styrs frågor använda samtidighet platser. Det exakta antalet samtidiga fack förbrukas bestäms av frågan [resursklassen](resource-classes-for-workload-management.md).

### <a name="optimized-for-compute"></a>Optimerad för beräkning
I följande tabell visas de maximalt antal samtidiga frågor och samtidighet fack för varje [dynamiska resursklassen](resource-classes-for-workload-management.md).  Dessa gäller för optimerad för beräkning prestandanivå.

**Dynamisk resursklasser**
| Servicenivå | Maximalt antal samtidiga frågor | Concurrency-fack som är tillgängliga | Platser som används av smallrc | Platser som används av mediumrc | Platser som används av largerc | Platser som används av xlargerc |
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

### <a name="optimized-for-elasticity"></a>Optimerat för elasticitet
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
| DW1000        | 32                         |  32                         | 1       |  8       | 16      |  32      |
| DW1200        | 32                         |  32                         | 1       |  8       | 16      |  32      |
| DW1500        | 32                         |  32                         | 1       |  8       | 16      |  32      |
| DW2000        | 32                         |  48                         | 1       | 16       | 32      |  64      |
| DW3000        | 32                         |  64                         | 1       | 16       | 32      |  64      |
| DW6000        | 32                         | 128                         | 1       | 32       | 64      | 128      |

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

