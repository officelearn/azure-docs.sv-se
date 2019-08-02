---
title: Använd ompartitionering för att optimera bearbetningen med Azure Stream Analytics
description: Den här artikeln beskriver hur du använder ompartitionering för att optimera Azure Stream Analytics jobb som inte kan vara parallella.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 07/26/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 9c802e6d23daf502da351549c66a7dae1247c068
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517441"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Använd ompartitionering för att optimera bearbetningen med Azure Stream Analytics

Den här artikeln visar hur du använder ompartitionering för att skala din Azure Stream Analytics fråga efter scenarier som inte kan [](stream-analytics-scale-jobs.md)vara helt parallella.

Du kanske inte kan använda [parallellisering](stream-analytics-parallelization.md) om:

* Du styr inte partitionsnyckel för indataströmmen.
* Källan "sprayar" i flera partitioner som senare behöver slås samman. 

## <a name="how-to-repartition"></a>Partitionera om

Ompartitionering eller reshuffling krävs när du bearbetar data på en data ström som inte är shardade enligt ett naturligt indata schema, till exempel **PartitionID** för Event Hubs. När du partitionerar om kan varje Shard bearbetas separat, vilket gör att du kan skala ut den strömmande pipelinen linjärt.

Om du vill partitionera om, använder du nyckelordet **i** efter en **partition by** -sats i frågan. I följande exempel partitioneras data efter **DeviceID** till antalet partitioner på 10. Hashing av **DeviceID** används för att avgöra vilken partition som ska acceptera vilken under data ström. Data rensas oberoende för varje partitionerad ström, förutsatt att utdata har stöd för partitionerade skrivningar och har 10 partitioner.

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

Följande exempel fråga ansluter två strömmar av ompartitionerade data. När du ansluter två strömmar av ompartitionerade data måste strömmarna ha samma partitionsnyckel och antal. Resultatet är en ström som har samma partitionsschema.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

Utmatnings schema ska matcha nyckeln Stream schema och Count så att varje under data ström kan tömmas oberoende av varandra. Data strömmen kan också sammanfogas och partitioneras om igen av ett annat schema innan du tömmer, men du bör undvika den metoden eftersom den lägger till i den allmänna svars tiden för bearbetningen och ökar resursutnyttjande.

## <a name="streaming-units-for-repartitions"></a>Enheter för strömning för ompartitionering

Experimentera och studera resursanvändningen för jobbet för att fastställa det exakta antalet partitioner du behöver. Antalet enheter för [strömning (SU)](stream-analytics-streaming-unit-consumption.md) måste justeras enligt de fysiska resurser som krävs för varje partition. I allmänhet behövs sex SUs för varje partition. Om det inte finns tillräckligt med resurser kopplade till jobbet, kommer systemet bara att använda ompartitionen om den har nytta av jobbet.

## <a name="repartitions-for-sql-output"></a>Ompartitionering för SQL-utdata

När jobbet använder SQL Database för utdata använder du explicit ompartitionering för att matcha det optimala antalet partitioner för att maximera data flödet. Eftersom SQL fungerar bäst med åtta skrivare, behöver du partitionera om flödet till åtta innan du tömmer eller någonstans ytterligare överströms, vilket kan dra nytta av jobbets prestanda. Mer information finns i [Azure Stream Analytics utdata till Azure SQL Database](stream-analytics-sql-output-perf.md).


## <a name="next-steps"></a>Nästa steg

* [Kom igång med Azure Stream Analytics](stream-analytics-introduction.md)
* [Använd Query parallellisering i Azure Stream Analytics](stream-analytics-parallelization.md)