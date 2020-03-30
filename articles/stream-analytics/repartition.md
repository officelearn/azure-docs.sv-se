---
title: Använda partitionering för att optimera Azure Stream Analytics-jobb
description: I den här artikeln beskrivs hur du använder partitionering för att optimera Azure Stream Analytics-jobb som inte kan parallelliseras.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: c70cfb6c1626908a2ba4e707a890f6dc7481c51a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732390"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Använda partitionering för att optimera bearbetningen med Azure Stream Analytics

Den här artikeln visar hur du använder partitionering för att skala din Azure Stream Analytics-fråga efter scenarier som inte kan [parallelliseras](stream-analytics-scale-jobs.md)helt .

Du kanske inte kan använda [parallellisering](stream-analytics-parallelization.md) om:

* Du styr inte partitionsnyckeln för indataströmmen.
* Din källa "sprayer" indata över flera partitioner som senare måste slås samman.

Partitionering eller omfördelning krävs när du bearbetar data på en ström som inte är fragmenterad enligt ett naturligt indataschema, till exempel **PartitionId** för händelsehubbar. När du partitionerar om kan varje shard bearbetas oberoende av varandra, vilket gör att du linjärt kan skala ut din strömningspipeline.

## <a name="how-to-repartition"></a>Så här partitionerar du om

Om du vill partitionera om använder du nyckelordet **INTO** efter en **PARTITION BY-sats** i frågan. I följande exempel partitionerar data efter **DeviceID** till ett partitionsantal på 10. Hashing av **DeviceID** används för att avgöra vilken partition som ska acceptera vilken underström. Data rensas oberoende av varandra för varje partitionerad dataström, förutsatt att utdata stöder partitionerade skrivningar och har 10 partitioner.

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

Följande exempelfråga sammanfogar två strömmar av partitionerade data. När du ansluter till två strömmar av partitionerade data måste strömmarna ha samma partitionsnyckel och antal. Resultatet är en ström som har samma partitionsschema.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

Utdataschemat bör matcha nyckeln för dataströmsschema och antalet så att varje underström kan tömmas oberoende av varandra. Strömmen kan också slås samman och partitioneras om av ett annat schema innan du tömmer den metoden eftersom den lägger till den allmänna svarstiden för bearbetningen och ökar resursutnyttjandet.

## <a name="streaming-units-for-repartitions"></a>Strömningsenheter för ompartitioner

Experimentera och observera resursanvändningen för jobbet för att fastställa det exakta antalet partitioner du behöver. Antalet [strömningsenheter (SU)](stream-analytics-streaming-unit-consumption.md) måste justeras enligt de fysiska resurser som behövs för varje partition. I allmänhet behövs sex SUs för varje partition. Om det inte finns tillräckliga resurser som tilldelats jobbet, kommer systemet bara att tillämpa partitionen om det gynnar jobbet.

## <a name="repartitions-for-sql-output"></a>Partitionering för SQL-utdata

När jobbet använder SQL-databas för utdata använder du explicit partitionering för att matcha det optimala partitionsantalet för att maximera dataflödet. Eftersom SQL fungerar bäst med åtta författare kan omfördelning av flödet till åtta innan du spolar, eller någonstans längre uppströms, gynna jobbprestanda. 

När det finns fler än 8 indatapartitioner kanske ärvning av indatapartitioneringsschemat inte vara ett lämpligt val. Överväg att använda [INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) i frågan för att uttryckligen ange antalet utdataskrivare. 

Följande exempel läser från indata, oavsett om den är naturligt partitionerad, och partitionerar strömmen tiofaldigt enligt DeviceID-dimensionen och tömmer data till utdata. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

Mer information finns i [Azure Stream Analytics-utdata till Azure SQL Database](stream-analytics-sql-output-perf.md).


## <a name="next-steps"></a>Nästa steg

* [Komma igång med Azure Stream Analytics](stream-analytics-introduction.md)
* [Utnyttja frågeallallalisering i Azure Stream Analytics](stream-analytics-parallelization.md)
