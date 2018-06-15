---
title: Minne och samtidighet gränser - Azure SQL Data Warehouse | Microsoft Docs
description: Visa gränserna minne och samtidighet som allokerats till de olika prestandanivåer och resursklasser i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 05/07/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 46d41e3ee85deb20f189bc9c82a255178f3d7eee
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942273"
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Minne och samtidighet gränser för Azure SQL Data Warehouse
Visa gränserna minne och samtidighet som allokerats till de olika prestandanivåer och resursklasser i Azure SQL Data Warehouse. Mer information och tillämpa dessa funktioner på din plan för hantering av arbetsbelastning finns [resursklasser för hantering av arbetsbelastning](resource-classes-for-workload-management.md). 

Det finns för närvarande två generationer som är tillgängliga med SQL Data Warehouse – Gen1 och Gen2. Vi rekommenderar att du utnyttja Gen2 för SQL Data Warehouse för att få bästa möjliga prestanda för din arbetsbelastning i informationslager. Gen2 introducerar en ny NVMe fasta tillstånd diskcache som håller den mest ofta använda data nära processorerna. Detta tar bort fjärr-i/o för dina mest intensiv och krävande arbetsbelastningar. Utöver prestanda ger Gen2 den mest skala genom att du kan skala upp till 30 000 Informationslagerenheter och obegränsad kolumner lagring. Vi kommer fortfarande stöd för föregående generation (Gen1) för SQL Data Warehouse och behålla samma funktioner; men vi rekommenderar att du [uppgradera till Gen2](upgrade-to-latest-generation.md) tidigaste dataskyddsstatistik. 

## <a name="data-warehouse-capacity-settings"></a>Kapacitetsinställningarna för data warehouse
Följande tabeller visar den maximala kapaciteten för datalagret på olika prestandanivåer. Om du vill ändra prestandanivå [skala beräknings - portal](quickstart-scale-compute-portal.md).

### <a name="gen2"></a>Gen2

Gen2 innehåller 2,5 x mer minne per fråga än Gen1. Den här extra minne hjälper Gen2 dess snabb prestanda.  Prestandanivåer för Gen2 mellan DW1000c och DW30000c. 

| Prestandanivå | Compute-noder | Distributioner per Compute-nod | Minne per datalager (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW1000c           | 2             | 30                             |   600                          |
| DW1500c           | 3             | 20                             |   900                          |
| DW2000c           | 4             | 15                             |  1200                          |
| DW2500c           | 5             | 12                             |  1500                          |
| DW3000c           | 6             | 10                             |  1800                          |
| DW5000c           | 10            | 6                              |  3000                          |
| DW6000c           | 12            | 5                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

Den maximala Gen2 DWU är DW30000c som har 60 datornoder och en distributionsplats per Compute-nod. Till exempel bearbetar ett 600 TB data warehouse på DW30000c cirka 10 TB per Compute-nod.

### <a name="gen1"></a>Gen1

Servicenivåer för Gen1 mellan DW100 och DW6000. 

| Prestandanivå | Compute-noder | Distributioner per Compute-nod | Minne per datalager (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100             | 1             | 60                             |  24                            |
| DW200             | 2             | 30                             |  48                            |
| DW300             | 3             | 20                             |  72                            |
| DW400             | 4             | 15                             |  96                            |
| DW500             | 5             | 12                             | 120                            |
| DW600             | 6             | 10                             | 144                            |
| DW1000            | 10            | 6                              | 240                            |
| DW1200            | 12            | 5                              | 288                            |
| DW1500            | 15            | 4                              | 360                            |
| DW2000            | 20            | 3                              | 480                            |
| DW3000            | 30            | 2                              | 720                            |
| DW6000            | 60            | 1                              | 1440                           |

## <a name="concurrency-maximums"></a>Concurrency maxkapacitet
För att säkerställa att varje fråga har tillräckligt med resurser för att köra effektivt, spårar SQL Data Warehouse resursutnyttjande genom att tilldela varje fråga samtidighet platser. Systemet samlar frågor i en kö där de vänta tills det finns tillräckligt med [samtidighet fack](resource-classes-for-workload-management.md#concurrency-slots) är tillgängliga. Concurrency fack avgör också CPU prioritering. Mer information finns i [analysera din arbetsbelastning](analyze-your-workload.md)

### <a name="gen2"></a>Gen2
 
**Statisk resursklasser**

I följande tabell visas de maximalt antal samtidiga frågor och samtidighet fack för varje [Statiska resursklassen](resource-classes-for-workload-management.md).  

| Servicenivå | Maximalt antal samtidiga frågor | Concurrency-fack som är tillgängliga |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Dynamisk resursklasser**

> [!NOTE]
> Resursklass smallrc på Gen2 lägger dynamiskt minne som servicenivån ökar och stöder endast en högsta 32 samtidiga frågor.  Concurrency-platser och minne som används av smallrc ökar när tjänsten nivån ökar. 
>
>

I följande tabell visas de maximalt antal samtidiga frågor och samtidighet fack för varje [dynamiska resursklassen](resource-classes-for-workload-management.md). Till skillnad från Gen1 är dynamisk resursklasser på Gen2 verkligen dynamiska.  Gen2 använder en 3-10-22-70 procent minnesallokering för små-medel-stora-xlarge resursklasser på alla servicenivåer.

| Servicenivå | Maximalt antal samtidiga frågor | Concurrency-fack som är tillgängliga | Platser som används av smallrc | Platser som används av mediumrc | Platser som används av largerc | Platser som används av xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW1000c       | 32                         |   40                        | 1                     |  4                     |  8                    |  28                    |
| DW1500c       | 32                         |   60                        | 1                     |  6                     |  13                   |  42                    |
| DW2000c       | 32                         |   80                        | 2                     |  8                     |  17                   |  56                    |
| DW2500c       | 32                         |  100                        | 3                     | 10                     |  22                   |  70                    |
| DW3000c       | 32                         |  120                        | 3                     | 12                     |  26                   |  84                    |
| DW5000c       | 32                         |  200                        | 6                     | 20                     |  44                   | 140                    |
| DW6000c       | 32                         |  240                        | 7                     | 24                     |  52                   | 168                    |
| DW7500c       | 32                         |  300                        | 9                     | 30                     |  66                   | 210                    |
| DW10000c      | 32                         |  400                        | 12                    | 40                     |  88                   | 280                    |
| DW15000c      | 32                         |  600                        | 18                    | 60                     | 132                   | 420                    |
| DW30000c      | 32                         | 1200                        | 36                    | 120                    | 264                   | 840                    |



#### <a name="gen1"></a>Gen1

Statisk resursklasser

I följande tabell visas de maximalt antal samtidiga frågor och samtidighet fack för varje [Statiska resursklassen](resource-classes-for-workload-management.md) på **Gen1**.

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
| DW2000        | 48                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 64                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 128                        | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

Dynamisk resursklasser
> [!NOTE]
> Resursklass smallrc på Gen1 allokerar en fast mängd minne per fråga, liknande sätt som statisk resurs klassen staticrc10.  Eftersom smallrc är statisk, har möjlighet att skala upp till 128 samtidiga frågor. 
>
>

I följande tabell visas de maximalt antal samtidiga frågor och samtidighet fack för varje [dynamiska resursklassen](resource-classes-for-workload-management.md) på **Gen1**.

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
| DW2000        | 48                         |  80                         | 1       | 16       | 32      |  64      |
| DW3000        | 64                         | 120                         | 1       | 16       | 32      |  64      |
| DW6000        | 128                        | 240                         | 1       | 32       | 64      | 128      |


När något av dessa tröskelvärden uppfylls är nya frågor i kö och utförs på grundval först in, först ut.  En frågor har slutförts, och antalet frågor och fack faller under den Frigör SQL Data Warehouse köade frågor. 

## <a name="next-steps"></a>Nästa steg

Mer information om hur man utnyttjar resursklasser för att optimera din arbetsbelastning ytterligare Läs följande artiklar:
* [Resursklasser för hantering av arbetsbelastning](resource-classes-for-workload-management.md)
* [Analysera din arbetsbelastning](analyze-your-workload.md)

