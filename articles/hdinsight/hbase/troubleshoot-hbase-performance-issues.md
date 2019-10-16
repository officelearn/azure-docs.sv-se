---
title: Felsök problem med Apache HBase-prestanda i Azure HDInsight
description: Olika rikt linjer för prestanda justering av Apache HBase och tips för att få optimala prestanda i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: c67f21a6ed8a7697977bb7737f0e46348efb2530
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "71266658"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Felsök problem med Apache HBase-prestanda i Azure HDInsight

Det här dokumentet beskriver olika rikt linjer för prestanda justering av Apache-HBase och tips för att få optimala prestanda i Azure HDInsight. Många av de här tipsen beror på arbets belastningen och Läs-/skriv-/skannings mönstret. Testa konfigurations ändringarna noggrant innan du använder dem i en produktions miljö.

## <a name="hdinsight-hbase-performance-insights"></a>HDInsight HBase Performance Insights

Den övre Flask halsen i de flesta HBase-arbetsbelastningar är Skriv loggs loggen (WAL). Det påverkar kraftigt skriv prestanda. HDInsight-HBase har en avgränsad lagrings beräknings modell, dvs. data lagras på distans i Azure Storage men region servrarna finns på de virtuella datorerna. Till dess nyligen skrevs Skriv loggen också till Azure Storage vilket förstärker denna Flask hals i fallet med HDInsight. Funktionen [accelererade skrivningar](./apache-hbase-accelerated-writes.md) är utformad för att lösa det här problemet genom att skriva loggen i förväg till Azure Premium SSD Managed disks. Detta ger bättre skriv prestanda och hjälper många problem med vissa av de Skriv intensiva arbets belastningarna.

Använd [Premium Block Blob-lagringskonto](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) som fjärrlagring för att få en betydande förbättring av läsåtgärder. Det här alternativet är endast möjligt om funktionen Write Ahead Log är aktiverad.

## <a name="compaction"></a>Komprimerings

Komprimering är en annan potentiell Flask hals som huvudsakligen överenskommits i communityn.  Som standard är större komprimering inaktiverat i HDInsight HBase-kluster. Detta beror på att det är en resurs intensiv process som gör det möjligt för kunderna att schemalägga den enligt deras arbets belastnings egenskaper – det vill säga under låg belastnings tid. Även om vår lagring är fjärran sluten (backas upp av Azure Storage) i stället för lokal HDFS, som är vanligt på de flesta lokal instanser, är data plats inte ett problem, vilket är ett av de primära målen med större komprimering.

Antagandet är att kunden bör vara försiktig med att schemalägga den stora komprimeringen enligt deras bekvämlighet. Om det här underhållet inte är slutfört, kommer komprimeringen att kraftigt påverka Läs prestanda i lång tid.

För genomsöknings åtgärder är det särskilt en anledning att göra fördröjningar mycket högre än 100 MS. Kontrol lera om större komprimering har schemalagts korrekt.

## <a name="know-your-apache-phoenix-workload"></a>Lär dig hur du Apache Phoenix arbets belastningen

Genom att besvara följande frågor får du hjälp att förstå din Apache Phoenix arbets belastning bättre:

* Översätts alla dina "läspaket"-översättning till genomsökningar?
    * I så fall, vilka är egenskaperna för dessa genomsökningar?
    * Har du optimerat ditt Phoenix Table-schema för de här genomsökningarna, inklusive lämplig indexering?
* Har du använt `EXPLAIN`-satsen för att förstå frågan planerar genereringen av "läsningar".
* Är dina skrivningar "upsert-väljer"?
    * I så fall kan de också göra genomsökningar. Förväntad svars tid för genomsökningar är i ordningen 100 MS i genomsnitt, i stället för 10 ms för punkt får du i HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Test metodik och mått övervakning

Om du använder benchmarks, till exempel YCSB, JMeter eller Pherf för att testa och justera prestanda, kontrollerar du följande:

1. Klient datorerna blir inte en Flask hals (kontrol lera CPU-användning på klient datorer).

1. Konfigurationer på klient sidan – till exempel antalet trådar och så vidare, justeras på lämpligt sätt för att fylla-klientens bandbredd.

1. Test resultaten registreras korrekt och systematiskt.

Om dina frågor plötsligt har börjat göra mycket sämre än tidigare – kontrollerar du om det finns möjliga buggar i program koden – är det plötsligt att generera stora mängder ogiltiga data, så att du kan öka Läs fördröjningen på ett naturligt sätt?

## <a name="migration-issues"></a>Problem med migrering

Om du migrerar till Azure HDInsight bör du se till att migreringen utförs systematiskt och korrekt, helst via automatisering. Undvik manuell migrering. Kontrol lera följande:

1. Tabellattribut – till exempel komprimering, blomma filter och så vidare, bör migreras korrekt.

1. För Phoenix-tabeller bör salt inställningarna mappas korrekt till den nya kluster storleken. Till exempel rekommenderas antalet salt buckets för att vara multipl av antalet arbetsnoder i kluster – den speciella multipeln är en faktor för mängden frekventa upptäcka.  

## <a name="server-side-config-tunings"></a>Konfigurations justering på Server Sidan

I HDInsight-HBase lagras HFiles på Fjärrlagring, vilket innebär att när det finns ett cache-minne, kommer kostnaden för läsningarna att vara högre än lokal system, som har data som backas upp av Local HDFS tack vare den nätverks fördröjning som ingår. I de flesta fall är intelligent användning av HBase-cache (block-cache och Bucket cache) utformad för att kringgå det här problemet. Det kommer dock att finnas tillfälliga fall där det kan vara problem med kunden. Att använda Premium Block Blob-kontot har hjälpt detta något. Men med WASB-blobben (Windows Azure Storage driv rutin) som förlitar sig på vissa egenskaper, till exempel `fs.azure.read.request.size` för att hämta data i block baserat på vad den bestämmer sig för att vara läsnings läge (sekventiell vs slumpmässig) kan vi fortsätta att se instanser av högre latens med läsningar. Vi har hittat de empiriska experiment som ställer in block storleken för läsnings begär Anden (`fs.azure.read.request.size`) till 512 KB och matchar block storleken för HBase-tabellerna så att de blir det bästa resultatet i praktiken.

HDInsight HBase, för de flesta kluster med stora storleks noder, ger `bucketcache` som en fil på lokal SSD som är kopplad till den virtuella datorn, vilket kör `regionservers`. Vid tillfällen kan användningen av heap-cachen i stället ge en viss förbättring. Detta har begränsningen att använda tillgängligt minne och kan vara mindre storlek än filbaserad cache, så det är inte alltid uppenbart att det är det bästa valet.

Några av de andra speciella parametrarna som vi har justerat som verkar ha hjälpt till att variera i grader enligt nedan:

1. Öka `memstore`-storlek från standard 128 MB till 256 MB – den här inställningen rekommenderas vanligt vis för tungt Skriv scenario.

1. Öka antalet trådar som är dedikerade för komprimering – från standardvärdet 1 till 4. Den här inställningen är relevant om vi iakttar frekventa smärre komprimeringar.

1. Undvik att blockera `memstore`-tömning på grund av en lagrings gräns. `Hbase.hstore.blockingStoreFiles` kan ökas till 100 för att tillhandahålla den här bufferten.

1. För att kontrol lera tömningar kan standardvärdena åtgärdas enligt nedan:

    1. `Hbase.regionserver.maxlogs` kan vara Upped till 140 från 32 (undvika tömningar på grund av WAL-gränser).

    1. `Hbase.regionserver.global.memstore.lowerLimit` = 0,55.

    1. `Hbase.regionserver.global.memstore.upperLimit` = 0,60.

1. Phoenix-/regionsspecifika konfigurationer för inställning av trådpool:

    1. `Phoenix.query.queuesize` kan höjas till 10000.

    1. `Phoenix.query.threadpoolsize` kan höjas till 512.

1. Andra Phoenix-speciella konfigurationer:

    1. `Phoenix.rpc.index.handler.count` kan anges till 50 om vi har stora eller många indexs ökningar.

    1. `Phoenix.stats.updateFrequency` – kan vara Upped till 1 timme från standardvärdet 15 minuter.

    1. `Phoenix.coprocessor.maxmetadatacachetimetolivems` – kan vara Upped till 1 timme från 30 minuter.

    1. `Phoenix.coprocessor.maxmetadatacachesize` – kan vara Upped till 50 MB från 20 MB.

1. RPC-tidsgräns – HBase RPC-tidsgräns, timeout för klient skanner, och Phoenix-frågans tids gräns kan ökas till 3 minuter. Det är viktigt att Observera att parametern `hbase.client.scanner.caching` har angetts till ett värde som matchar vid Server slut och klient slut. Annars leder inställningen till fel relaterade till `OutOfOrderScannerException` vid klientens slut. Den här inställningen ska vara inställd på ett lågt värde för stora genomsökningar. Vi anger värdet 100.

## <a name="other-considerations"></a>Andra överväganden

Några andra parametrar som ska beaktas vid justering:

1. `Hbase.rs.cacheblocksonwrite` – den här inställningen är inställd på True som standard på HDI.

1. Inställningar som gör det möjligt att skjuta upp mindre komprimering för senare.

1. Experimentella inställningar som att justera procent andelen av köer som är reserverade för läsnings-och skriv förfrågningar.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

- Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

- Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

- Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
