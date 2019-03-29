---
title: Optimera prestanda för Apache Kafka HDInsight-kluster
description: Innehåller en översikt av metoder för att optimera Apache Kafka-arbetsbelastningar på Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: 3f15f45e0543c582d70463fb9ddc7ac569ff57bc
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576766"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Optimera prestanda för Apache Kafka HDInsight-kluster

Den här artikeln innehåller några förslag för att optimera prestanda för dina arbetsbelastningar för Apache Kafka i HDInsight. Fokus ligger på att justera producent och broker-konfigurationen. Det finns flera olika sätt att mäta prestanda och optimeringar som du använder beror på dina affärsbehov.

## <a name="architecture-overview"></a>Översikt över arkitekturen

Kafka-avsnitt används för att organisera poster. Poster produceras av producenter och används av konsumenterna. Producenter skicka poster till Kafka-Meddelandeköer som lagrar data. Varje arbetsnod i HDInsight-klustret är en asynkron Kafka-meddelandekö.

Ämnen delar upp poster över meddelandeköer. När du förbrukar poster, kan du använda en konsument per partition för att uppnå parallell bearbetning av data.

Replikering används för att duplicera partitionerna mellan noder. Detta skyddar mot avbrott i noden (asynkron meddelandekö). En enda partition inom gruppen med repliker är utsedd till ledare partition. Producenttrafik dirigeras till varje ledande nod med det tillstånd som hanteras av ZooKeeper.

## <a name="identify-your-scenario"></a>Identifiera ditt scenario

Apache Kafka prestanda består av två huvudsakliga delar – dataflöde och svarstid. Dataflödet är den maximala hastighet med vilken data kan bearbetas. Högre dataflöde är vanligtvis bättre. Svarstiden är den tid det tar för data som ska lagras eller hämtas. Lägre fördröjning är vanligtvis bättre. Vara kan svårt att hitta rätt balans mellan dataflöde, svarstider och kostnaden för programmets infrastruktur. Dina prestandakrav kommer sannolikt att matcha en av följande tre vanliga situationer, baserat på om du behöver hög genomströmning, låg latens eller båda:

* Stora dataflöden och låg latens. Det här scenariot kräver både stora dataflöden och låg latens (~ 100 millisekunder). Ett exempel på den här typen av program är tjänsten tillgänglighetsövervakning.
* Högt dataflöde, svarstider. Det här scenariot kräver stora dataflöden (~1.5 Gbit/s), men kan tolerera högre svarstid (< 250 ms). Ett exempel på den här typen av program är inmatning av telemetri data för nära realtid processer som säkerhet och intrång identifiering av program.
* Låg genomströmning, låg latens. Det här scenariot kräver låg latens (< 10 ms) för bearbetning i realtid, men kan tolerera lägre dataflöde. Ett exempel på den här typen av program är stavning och grammatik onlinekontroll.

## <a name="producer-configurations"></a>Producent-konfigurationer

I följande avsnitt kommer beskrivs några av de viktigaste konfigurationsegenskaperna optimera prestandan för dina Kafka-producenter. En detaljerad förklaring av alla konfigurationsegenskaper Se [Apache Kafka-dokumentation på producent konfigurationer](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Batchstorlek

Apache Kafka producenter Assemblera grupper av meddelanden (kallas batchar) som skickas som en enhet som ska lagras i en enda storage partition. Batchstorlek innebär antalet byte som måste finnas innan gruppen överförs. Öka den `batch.size` parametern kan öka genomflödet, eftersom det minskar bearbetningen overhead från nätverket och i/o-begäranden. Vid låg belastning öka ökad batchstorlek Kafka skicka svarstid som producenten väntar för en grupp ska bli klar. Vid hög belastning rekommenderar vi för att öka batchstorlek för att förbättra dataflöde och svarstid.

### <a name="producer-required-acknowledgements"></a>Producent krävs bekräftelser

Producenten krävs `acks` konfigurationen anger antalet bekräftelser som krävs av ledaren partitionen innan en skrivbegäran anses har slutförts. Den här inställningen påverkar datatillförlitlighet och det tar att värdena för `0`, `1`, eller `-1`. Värdet för `-1` innebär en bekräftelse måste tas emot från alla repliker innan skrivningen har slutförts. Ange `acks = -1` ger av starkare garantier mot dataförlust, men även resultat i högre svarstider och lägre dataflöde. Om dina programkrav kräver högre dataflöde, prova inställningen `acks = 0` eller `acks = 1`. Kom ihåg att inte uppmärksammades alla repliker kan minska datatillförlitlighet.

### <a name="compression"></a>Komprimering

En Kafka-producent kan konfigureras för att komprimera meddelanden innan de skickas till asynkrona meddelandeköer. Den `compression.type` inställningen anger komprimerings-codec som ska användas. Stöds komprimering codec är ”gzip”, ”snappy”, och ”lz4”. Komprimering kan vara bra och bör övervägas om det finns en begränsning på diskkapacitet.

Bland de två vanliga komprimering codec `gzip` och `snappy`, `gzip` har en högre komprimeringsförhållandet, vilket resulterar i lägre diskanvändning bekostnad högre CPU-belastningen. Den `snappy` codec ger mindre komprimering med mindre processorkraft. Du kan bestämma vilka codec för att använda baserat på broker disk eller producenten CPU-begränsningar. `gzip` Komprimera data till en kostnad som är fem gånger högre än `snappy`.

Med hjälp av datakomprimering ökar antalet poster som kan lagras på en disk. Det kan också öka CPU overhead i fall där det finns ett matchningsfel mellan komprimeringsformat som används av producenten och den asynkrona meddelandekön. När data måste komprimeras innan de skickas och sedan expandera innan bearbetning.

## <a name="broker-settings"></a>Inställningar för koordinatortjänst för

I följande avsnitt kommer beskrivs några av de viktigaste inställningarna optimera prestandan för dina Kafka-meddelandeköer. En detaljerad förklaring av alla mäkla inställningar, se [Apache Kafka-dokumentation på producent konfigurationer](https://kafka.apache.org/documentation/#producerconfigs).


### <a name="number-of-disks"></a>Antal diskar

Storage-diskar har begränsad IOPS (antal Input/Output åtgärder Per sekund) och Läs/Skriv byte per sekund. När du skapar nya partitionerna kan lagrar Kafka varje ny partition på disken med minsta antalet befintliga partitioner som ska belastningsutjämnas tillgängliga diskar. Kafka trots lagringsstrategi vid bearbetning av hundratals partitionsrepliker på varje disk, kan enkelt fylla det tillgängliga diskgenomflödet. Det handlar det om ytterligare här mellan dataflöde och kostnad. Om programmet kräver högre dataflöde kan du skapa ett kluster med flera hanterade diskar per meddelandekö. HDInsight stöder för närvarande inte att lägga till hanterade diskar i ett kluster som körs. Mer information om hur du konfigurerar antalet hanterade diskar finns i [konfigurera lagring och skalbarhet för Apache Kafka på HDInsight](apache-kafka-scalability.md). Förstå kostnaden följderna av att öka mängden lagringsutrymme för noderna i klustret.

### <a name="number-of-topics-and-partitions"></a>Antal ämnen och partitioner

Kafka producenter skriva till ämnen. Kafka-konsumenter läsa från ämnen. Ett ämne är associerad med en logg som är en datastruktur på disken. Kafka lägger till poster från en producenter i slutet av en logg i avsnittet. En logg i avsnittet består av många partitioner som finns på flera filer. De här filerna är, i sin tur fördelade på flera Kafka-klusternoder. Konsumenter läser från Kafka-avsnitt i sina takt och kan välja sin position (förskjutning) i avsnittet loggen.

Varje partition för Kafka är en loggfil på systemet och producent trådar kan skriva till flera loggar samtidigt. Eftersom varje konsument-tråd läser meddelanden från en partition, hanteras förbrukar från flera partitioner på samma sätt kan för samt i parallellt.

Öka partition densitet (antal partitioner per meddelandekö) lägger till en belastning som är relaterade till åtgärder för metadata och per partition begäran/svar mellan ledare partitionen och dess följare. Även i frånvaron av data som flödar hämta partitionsrepliker fortfarande data från ledare, vilket leder till extra bearbetning för skicka och ta emot förfrågningar via nätverket.

För Apache Kafka-kluster 1.1 och ovan i HDInsight, rekommenderar vi att du har högst 1000 partitioner per meddelandekö, inklusive repliker. Öka antalet partitioner per meddelandekö minskar dataflöde och kan även orsaka avsnittet otillgänglighet. Läs mer om stöd för Kafka-partitioner, [officiella Apache Kafka-blogginlägget på ökning av antalet partitioner som stöds i version 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Mer information om hur du ändrar ämnen finns i [Apache Kafka: ändra ämnen](https://kafka.apache.org/documentation/#basic_ops_modify_topic).

### <a name="number-of-replicas"></a>Antal repliker

Högre replikeringsfaktor resulterar i ytterligare begäranden mellan partition ledare och följare. Därför en högre replikeringsfaktor förbrukar mer disk och processor och hantera ytterligare begäranden ökar skriva svarstid och minska dataflöde.

Vi rekommenderar att du använder minst 3 x-replikering för Kafka i Azure HDInsight. De flesta Azure-regioner har tre feldomäner, men i regioner med bara två feldomäner användare ska använda 4 x-replikering.

Mer information om replikering finns i [Apache Kafka: replikering](https://kafka.apache.org/documentation/#replication) och [Apache Kafka: öka replikeringsfaktor](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>Nästa steg

* [Bearbetning av biljontals händelser per dag med Apache Kafka på Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [Vad är Apache Kafka på HDInsight?](apache-kafka-introduction.md)
