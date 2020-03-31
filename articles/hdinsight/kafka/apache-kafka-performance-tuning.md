---
title: Optimera prestanda för Apache Kafka HDInsight-kluster
description: Ger en översikt över tekniker för att optimera Apache Kafka-arbetsbelastningar på Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: 752068af531c4a0ecc832d266f88105c14452ecb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75494920"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Optimera prestanda för Apache Kafka HDInsight-kluster

Den här artikeln innehåller några förslag på hur du optimerar prestandan för dina Apache Kafka-arbetsbelastningar i HDInsight. Fokus ligger på att justera producent- och mäklarkonfigurationen. Det finns olika sätt att mäta prestanda, och de optimeringar som du använder beror på dina affärsbehov.

## <a name="architecture-overview"></a>Översikt över arkitekturen

Kafka-ämnen används för att ordna poster. Poster produceras av producenter, och används av konsumenter. Producenter skickar poster till Kafka mäklare, som sedan lagrar data. Varje arbetsnod i HDInsight-klustret är en asynkron Kafka-meddelandekö.

Ämnen delar upp poster över meddelandeköer. När du förbrukar poster, kan du använda en konsument per partition för att uppnå parallell bearbetning av data.

Replikering används för att duplicera partitioner över noder. Detta skyddar mot nod (mäklare) avbrott. En enda partition mellan gruppen av repliker betecknas som partitionsledare. Producenttrafik dirigeras till varje ledande nod med det tillstånd som hanteras av ZooKeeper.

## <a name="identify-your-scenario"></a>Identifiera ditt scenario

Apache Kafka prestanda har två huvudsakliga aspekter - dataflöde och latens. Dataflöde är den maximala hastighet med vilken data kan bearbetas. Högre dataflöde är oftast bättre. Svarstid är den tid det tar för data som ska lagras eller hämtas. Lägre latens är oftast bättre. Det kan vara svårt att hitta rätt balans mellan dataflöde, svarstid och kostnaden för programmets infrastruktur. Dina prestandakrav matchar sannolikt en av följande tre vanliga situationer, baserat på om du behöver högt dataflöde, låg latens eller både och:

* Hög dataflöde, låg latens. Det här scenariot kräver både högt dataflöde och låg latens (~100 millisekunder). Ett exempel på den här typen av program är övervakning av tjänsttillgänglighet.
* Hög dataflöde, hög latens. Det här scenariot kräver högt dataflöde (~1,5 GBps) men kan tolerera högre latens (< 250 ms). Ett exempel på den här typen av program är telemetridatainmatning för nära realtidsprocesser som säkerhets- och intrångsidentifieringsprogram.
* Låg dataflöde, låg latens. Det här scenariot kräver låg latens (< 10 ms) för bearbetning i realtid, men kan tolerera lägre dataflöde. Ett exempel på den här typen av program är stavnings- och grammatikkontroller online.

## <a name="producer-configurations"></a>Producentkonfigurationer

Följande avsnitt kommer att belysa några av de viktigaste konfigurationsegenskaperna för att optimera prestanda för dina Kafka-producenter. En detaljerad förklaring av alla konfigurationsegenskaper finns i [Apache Kafka-dokumentation om producentkonfigurationer](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Batchstorlek

Apache Kafka-producenter samlar grupper av meddelanden (så kallade batchar) som skickas som en enhet som ska lagras i en enda lagringspartition. Batchstorlek avser antalet byte som måste finnas innan den gruppen överförs. Om `batch.size` du ökar parametern kan du öka dataflödet, eftersom bearbetningen minskar omkostnaderna från nätverks- och IO-begäranden. Under lätt belastning kan ökad batchstorlek öka Kafka-sändningstiden när tillverkaren väntar på att ett parti ska vara klart. Under tung belastning rekommenderas att öka batchstorleken för att förbättra dataflödet och svarstiden.

### <a name="producer-required-acknowledgments"></a>Producent krävs bekräftelser

Den konfiguration `acks` som krävs för tillverkare bestämmer antalet bekräftelser som krävs av partitionsledaren innan en skrivbegäran anses vara slutförd. Den här inställningen påverkar datatillförlitligheten `0` `1`och `-1`värdena för , eller . Värdet på `-1` innebär att en bekräftelse måste tas emot från alla repliker innan skrivningen är klar. Inställningen `acks = -1` ger starkare garantier mot dataförlust, men det resulterar också i högre svarstid och lägre dataflöde. Om dina programkrav kräver högre `acks = 0` dataflöde provar du att ange eller `acks = 1`. Tänk på att om du inte erkänner alla repliker kan datatillförlitligheten minskas.

### <a name="compression"></a>Komprimering

En Kafka-producent kan konfigureras för att komprimera meddelanden innan de skickas till mäklare. Inställningen `compression.type` anger vilken komprimeringscodec som ska användas. Komprimeringskoderna som stöds är "gzip", "snappy" och "lz4". Komprimering är fördelaktigt och bör övervägas om det finns en begränsning av diskkapacitet.

Bland de två vanliga komprimeringskoderna `gzip` och `snappy`har `gzip` ett högre kompressionsförhållande, vilket resulterar i lägre diskanvändning till priset av högre CPU-belastning. Codec `snappy` ger mindre komprimering med mindre CPU-omkostnader. Du kan bestämma vilken codec som ska användas baserat på mäklare disk eller producent CPU begränsningar. `gzip`komprimera data med en hastighet `snappy`som är fem gånger högre än .

Om du använder datakomprimering ökar antalet poster som kan lagras på en disk. Det kan också öka CPU overhead i fall där det finns en obalans mellan komprimeringsformat som används av producenten och mäklaren. eftersom data måste komprimeras innan de skickas och sedan expanderas före bearbetning.

## <a name="broker-settings"></a>Mäklarinställningar

Följande avsnitt kommer att belysa några av de viktigaste inställningarna för att optimera prestanda för dina Kafka mäklare. En detaljerad förklaring av alla mäklarinställningar finns i [Apache Kafka-dokumentation om producentkonfigurationer](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="number-of-disks"></a>Antal diskar

Lagringsdiskar har begränsad IOPS (Input/Output Operations Per Sekund) och läs-/skrivbyte per sekund. När du skapar nya partitioner lagrar Kafka varje ny partition på disken med minst antal befintliga partitioner för att balansera dem över de tillgängliga diskarna. Trots lagringsstrategi, när du bearbetar hundratals partitionsrepliker på varje disk, kan Kafka enkelt mätta det tillgängliga diskdataflödet. Avvägningen här är mellan dataflöde och kostnad. Om ditt program kräver större dataflöde skapar du ett kluster med fler hanterade diskar per mäklare. HDInsight stöder för närvarande inte att lägga till hanterade diskar i ett kluster som körs. Mer information om hur du konfigurerar antalet hanterade diskar finns i [Konfigurera lagring och skalbarhet för Apache Kafka på HDInsight](apache-kafka-scalability.md). Förstå kostnadskonsekvenserna av att öka lagringsutrymmet för noderna i klustret.

### <a name="number-of-topics-and-partitions"></a>Antal ämnen och partitioner

Kafka producenter skriver till ämnen. Kafka konsumenter läsa från ämnen. Ett ämne associeras med en logg, som är en datastruktur på disken. Kafka lägger till poster från en eller flera tillverkare till slutet av en ämneslogg. En ämneslogg består av många partitioner som är spridda över flera filer. Dessa filer är i sin tur spridda över flera Kafka klusternoder. Konsumenterna läser från Kafka ämnen på sin kadens och kan välja sin position (offset) i ämnet loggen.

Varje Kafka-partition är en loggfil i systemet, och producenttrådar kan skriva till flera loggar samtidigt. På samma sätt, eftersom varje konsumenttråd läser meddelanden från en partition, hanteras även tidskrävande från flera partitioner parallellt.

Om du ökar partitionsdensiteten (antalet partitioner per mäklare) läggs en overhead som är relaterad till metadataåtgärder och per partitionsbegäran/svar mellan partitionsledaren och dess följare. Även i avsaknad av data som flödar igenom hämtar partitionsrepliker fortfarande data från ledare, vilket resulterar i extra bearbetning för att skicka och ta emot begäranden via nätverket.

För Apache Kafka kluster 1.1 och högre i HDInsight, rekommenderar vi att du har högst 1000 partitioner per mäklare, inklusive repliker. Öka antalet partitioner per mäklare minskar dataflödet och kan också orsaka ämnet otillgänglighet. Mer information om stöd för Kafka-partitionen finns [i det officiella blogginlägget från Apache Kafka om ökningen av antalet partitioner som stöds i version 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Mer information om hur du ändrar ämnen finns i [Apache Kafka: ändra ämnen](https://kafka.apache.org/documentation/#basic_ops_modify_topic).

### <a name="number-of-replicas"></a>Antal repliker

Högre replikeringsfaktor resulterar i ytterligare begäranden mellan partitionsledaren och följare. Följaktligen förbrukar en högre replikeringsfaktor mer disk och CPU för att hantera ytterligare begäranden, vilket ökar skrivsvarstiden och minskar dataflödet.

Vi rekommenderar att du använder minst 3x replikering för Kafka i Azure HDInsight. De flesta Azure-regioner har tre feldomäner, men i regioner med endast två feldomäner bör användare använda 4x-replikering.

Mer information om replikering finns i [Apache Kafka: replication](https://kafka.apache.org/documentation/#replication) och [Apache Kafka: ökande replikeringsfaktor](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>Nästa steg

* [Bearbetning av biljontals händelser per dag med Apache Kafka på Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [Vad är Apache Kafka på HDInsight?](apache-kafka-introduction.md)
