---
title: Begränsningar för minne och samtidighet
description: Visa de begränsningar för minne och samtidighet som allokerats till de olika prestandanivåerna och resursklasserna i Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 56ab49949b4ea2a92bc591042b2d43a7f7b2dc63
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632670"
---
# <a name="memory-and-concurrency-limits-for-azure-synapse-analytics"></a>Begränsningar för minne och samtidighet för Azure Synapse Analytics

Visa de begränsningar för minne och samtidighet som allokerats till de olika prestandanivåerna och resursklasserna i Azure Synapse Analytics.  

## <a name="data-warehouse-capacity-settings"></a>Inställningar för datalagerkapacitet

Följande tabeller visar den maximala kapaciteten för informationslagret på olika prestandanivåer. Mer om du vill ändra prestandanivån finns i [Skala beräkning - portal](quickstart-scale-compute-portal.md).

### <a name="service-levels"></a>Servicenivåer

Servicenivåerna sträcker sig från DW100c till DW30000c.

| Prestandanivå | Beräkningsnoder | Distributioner per beräkningsnod | Minne per informationslager (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c (på andra)            | 1             | 60                             |    60                          |
| DW200c (på andra)            | 1             | 60                             |   120                          |
| DW300c (på andra)            | 1             | 60                             |   180                          |
| DW400c (på andra)            | 1             | 60                             |   240                          |
| DW500c            | 1             | 60                             |   300                          |
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

Den maximala servicenivån är DW30000c, som har 60 beräkningsnoder och en distribution per beräkningsnod. Ett 600 TB-informationslager på DW30000c bearbetar till exempel cirka 10 TB per beräkningsnod.

## <a name="concurrency-maximums-for-workload-groups"></a>Maximalt antal samtidigheter för arbetsbelastningsgrupper

I och med införandet av [arbetsbelastningsgrupper](sql-data-warehouse-workload-isolation.md)gäller inte längre begreppet samtidighetsplatser.  Resurser per begäran fördelas i procent och anges i arbetsbelastningsgruppdefinitionen.  Men även med borttagning av samtidighetsplatser finns det minsta belopp för resurser som behövs per frågor baserat på servicenivån.  I tabellen nedan definierades den minsta mängd resurser som behövs per fråga över tjänstnivåer och den tillhörande samtidighet som kan uppnås.

|Servicenivå|Maximala samtidiga frågor|Min % stöd för REQUEST_MIN_RESOURCE_GRANT_PERCENT|
|---|---|---|
|DW100c (på andra)|4|25 %|
|DW200c (på andra)|8|12.5%|
|DW300c (på andra)|12|8 %|
|DW400c (på andra)|16|6.25%|
|DW500c|20|5 %|
|DW1000c|32|3 %|
|DW1500c|32|3 %|
|DW2000c|48|2 %|
|DW2500c|48|2 %|
|DW3000c|64|1.5%|
|DW5000c|64|1.5%|
|DW6000c|128|0,75 %|
|DW7500c|128|0,75 %|
|DW10000c|128|0,75 %|
|DW15000c|128|0,75 %|
|DW30000c|128|0,75 %|
||||

## <a name="concurrency-maximums-for-resource-classes"></a>Maximalt antal samtidigheter för resursklasser

För att säkerställa att varje fråga har tillräckligt med resurser för att köra effektivt spårar SQL Analytics i Azure Synapse resursutnyttjande genom att tilldela samtidighetsplatser till varje fråga. Systemet placerar frågor i en kö baserat på prioritet och samtidighetsplatser. Frågor väntar i kön tills tillräckligt med samtidighetsplatser är tillgängliga. [Viktiga](sql-data-warehouse-workload-importance.md) och samtidighetsplatser avgör CPU-prioritering. Mer information finns i [Analysera din arbetsbelastning](analyze-your-workload.md)

**Statiska resursklasser**

I följande tabell visas de maximala samtidiga frågorna och samtidighetsplatserna för varje [statisk resursklass](resource-classes-for-workload-management.md).  

| Servicenivå | Maximala samtidiga frågor | Tillgängliga ankomst- och avgångstider | Platser som används av staticrc10 | Slots som används av staticrc20 | Kortplatser som används av staticrc30 | Kortplatser som används av staticrc40 | Kortplatser som används av staticrc50 | Kortplatser som används av staticrc60 | Kortplatser som används av staticrc70 | Kortplatser som används av staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c (på andra)        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c (på andra)        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c (på andra)        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c (på andra)        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Dynamiska resursklasser**

I följande tabell visas de maximala samtidiga frågorna och samtidighetsplatserna för varje [dynamisk resursklass](resource-classes-for-workload-management.md). Dynamiska resursklasser använder en minnesprocentallokering på 3-10-22-70 för små-medelstora-stora xlarge-resursklasser på alla tjänstnivåer.

| Servicenivå | Maximala samtidiga frågor | Tillgängliga ankomst- och avgångstider | Slots som används av smallrc | Slots som används av mediumrc | Slots som används av largerc | Slots som används av xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c (på andra)        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c (på andra)        |  8                         |    8                        | 1                     |  1                     |  1                    |   5                    |
| DW300c (på andra)        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c (på andra)        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
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

När det inte finns tillräckligt med samtidighetsplatser som är lediga för att starta frågekörning, köas och körs frågor baserat på betydelse.  Om det finns motsvarande betydelse utförs frågor först in, först ut.  När en fråga avslutas och antalet frågor och platser hamnar under gränserna, SQL Data Warehouse-versioner i kösfrågor.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utnyttjar resursklasser för att optimera arbetsbelastningen ytterligare läser du följande artiklar:

* [Resursklasser för arbetsbelastningshantering](resource-classes-for-workload-management.md)
* [Analysera din arbetsbelastning](analyze-your-workload.md)
