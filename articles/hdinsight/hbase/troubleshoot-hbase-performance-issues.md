---
title: Felsöka problem med Apache HBase-prestanda Azure HDInsight
description: Olika riktlinjer och tips för prestandajustering av Apache HBase och tips för att få optimal prestanda på Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 93698fadcecf190dd8bbc24a9d03978899d3c5e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887163"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Felsöka problem med Apache HBase-prestanda Azure HDInsight

I den här artikeln beskrivs olika riktlinjer och tips för prestandajustering av Apache HBase och tips för att få optimal prestanda på Azure HDInsight. Många av dessa tips beror på den arbetsbelastning och läs/skriv/skanna mönster. Testa dem noggrant innan du tillämpar konfigurationsändringar i en produktionsmiljö.

## <a name="hbase-performance-insights"></a>HBase prestanda insikter

Den översta flaskhalsen i de flesta HBase-arbetsbelastningar är Write Ahead Log (WAL). Det påverkar skrivresultatet allvarligt. HDInsight HBase har en separerad lagringsberäkningsmodell. Data lagras på distans på Azure Storage, även om virtuella datorer är värdar för regionservrarna. Tills nyligen skrevs WAL också till Azure Storage. I HDInsight förstärkte detta beteende denna flaskhals. Funktionen [Accelerated Writes](./apache-hbase-accelerated-writes.md) är utformad för att lösa problemet. Den skriver WAL till Azure Premium SSD-hanterade diskar. Detta gynnar enormt skrivprestanda, och det hjälper många problem som några av de skrivintensiva arbetsbelastningar.

Använd [Premium Block Blob Storage Account](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) som fjärrlagring för att få en betydande förbättring av läsåtgärder. Det här alternativet är endast möjligt om WAL-funktionen är aktiverad.

## <a name="compaction"></a>Packning

Kompaktering är en annan potentiell flaskhals som i grunden överenskommits i samhället. Som standard är större komprimering inaktiverad på HDInsight HBase-kluster. Komprimering är inaktiverat eftersom kunderna, även om det är en resurskrävande process, har full flexibilitet att schemalägga den enligt sina arbetsbelastningar. De kan till exempel schemalägga den under lågtrafik. Datalokal är inte heller ett problem eftersom vår lagring är fjärransluten (backas upp av Azure Storage) i stället för till ett lokalt Hadoop Distributed File System (HDFS).

Kunderna bör schemalägga större packning när det passar dem. Om de inte gör detta underhåll, kommer packning negativt påverka läsprestanda i det långa loppet.

För skanningsoperationer bör medelvärdet för svarstider som är mycket högre än 100 millisekunder vara en anledning till oro. Kontrollera om större komprimering har schemalagts korrekt.

## <a name="apache-phoenix-workload"></a>Apache Phoenix arbetsbelastning

Genom att svara på följande frågor kan du bättre förstå din Apache Phoenix-arbetsbelastning:

* Är alla dina "läser" översätta till skanningar?
    * Om så är fallet, vilka är egenskaperna hos dessa skanningar?
    * Har du optimerat ditt Phoenix-tabellschema för dessa genomsökningar, inklusive lämplig indexering?
* Har du `EXPLAIN` använt uttalandet för att förstå frågan planer din "läser" generera?
* Är dina skrivningar "upsert-selects"?
    * Om så är fallet, skulle de också göra skanningar. Förväntad svarstid för genomsökningar i genomsnitt cirka 100 millisekunder, jämfört med 10 millisekunder för punkt får i HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Testmetodik och mätövervakning

Om du använder riktmärken som Yahoo! Cloud Serving Benchmark, JMeter eller Pherf för att testa och justera prestanda, se till att:

- Klientmaskinerna blir ingen flaskhals. Det gör du genom att kontrollera CPU-användningen på klientdatorer.

- Konfigurationer på klientsidan, liksom antalet trådar, är korrekt inställda för att mätta klientbandbredden.

- Testresultaten registreras korrekt och systematiskt.

Om dina frågor plötsligt började göra mycket värre än tidigare, kontrollera om det finns potentiella fel i din programkod. Är det plötsligt genererar stora mängder ogiltiga data? Om det är, kan det öka läs latenser.

## <a name="migration-issues"></a>Migreringsfrågor

Om du migrerar till Azure HDInsight kontrollerar du att migreringen sker systematiskt och korrekt, helst via automatisering. Undvik manuell migrering. Kontrollera att:

- Tabellattribut migreras korrekt. Attribut kan inkludera som komprimering, blomfilter och så vidare.

- Saltningsinställningarna i Phoenix-tabeller mappas på lämpligt sätt till den nya klusterstorleken. Antalet saltkopor ska till exempel vara en multipel av antalet arbetsnoder i klustret. Och du bör använda en multipel som är en faktor för mängden heta spotting.

## <a name="server-side-configuration-tunings"></a>Konfigurationsjusteringar på serversidan

I HDInsight HBase lagras HFiles på fjärrlagring. När det finns en cache miss, kostnaden för läsningar är högre än lokala system eftersom data på lokala system backas upp av lokala HDFS. I de flesta fall är intelligent användning av HBase-cacheminnen (blockcache och bucket cache) utformad för att kringgå det här problemet. I de fall problemet inte kringgås kan det här problemet med ett blob-konto med premiumblock hjälpa till. Windows Azure Storage Blob-drivrutinen är `fs.azure.read.request.size` beroende av vissa egenskaper, till exempel för att hämta data i block baserat på vad den anser vara läsläge (sekventiell kontra slumpmässig), så det kan fortsätta att finnas förekomster av högre latenser med läsningar. Genom empiriska experiment har vi funnit att ställa`fs.azure.read.request.size`in läsbegäran blockstorlek ( ) till 512 KB och matcha blockstorleken på HBase tabeller vara samma storlek ger det bästa resultatet i praktiken.

För de flesta kluster av noder i stor `bucketcache` storlek tillhandahåller HDInsight HBase som en fil på en `regionservers`lokal Premium SSD som är kopplad till den virtuella datorn, som körs . Om du använder cachen utanför heap kan det i stället bli en viss förbättring. Den här lösningen har begränsningen att använda tillgängligt minne och kan vara mindre än filbaserad cache, så det kanske inte alltid är det bästa valet.

Följande är några av de andra specifika parametrar som vi trimmade, och som tycktes hjälpa i varierande grad:

- Öka `memstore` storleken från standard 128 MB till 256 MB. Vanligtvis rekommenderas den här inställningen för tunga skrivscenarier.

- Öka antalet trådar som är avsedda för komprimering, från standardinställningen **1** till **4**. Den här inställningen är relevant om vi observerar frekventa mindre kompakteringar.

- Undvik att `memstore` blockera spolning på grund av butiksgränsen. Om du vill ange `Hbase.hstore.blockingStoreFiles` den här bufferten ökar du inställningen till **100**.

- Om du vill styra in flushes använder du följande inställningar:

    - `Hbase.regionserver.maxlogs`: **140** (undviker spolningar på grund av WAL gränser)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0,55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0,60**

- Phoenix-specifika konfigurationer för trådpooljustering:

    - `Phoenix.query.queuesize`: **10000**

    - `Phoenix.query.threadpoolsize`: **512**

- Andra Phoenix-specifika konfigurationer:

    - `Phoenix.rpc.index.handler.count`: **50** (om det finns stora eller många indexuppslag)

    - `Phoenix.stats.updateFrequency`: **1 timme**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1 timme**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50 MB**

- RPC-timeout: **3 minuter**

   - RPC-timeout inkluderar HBase RPC-timeout, HBase-klientskannertidsutskrift och timeout för Phoenix-frågor. 
   - Kontrollera att `hbase.client.scanner.caching` parametern är inställd på samma värde både vid serveränden och klientslutet. Om de inte är desamma leder den här inställningen till `OutOfOrderScannerException`klientslutfel som är relaterade till . Den här inställningen bör ställas in på ett lågt värde för stora genomsökningar. Vi ställer in detta värde till **100**.

## <a name="other-considerations"></a>Andra överväganden

Följande är ytterligare parametrar för att överväga justering:

- `Hbase.rs.cacheblocksonwrite`– Som standard på HDI är den här inställningen inställd på **true**.

- Inställningar som gör det möjligt att skjuta upp mindre komprimering för senare.

- Experimentella inställningar, till exempel justera procentsatser för köer som är reserverade för läs- och skrivbegäranden.

## <a name="next-steps"></a>Nästa steg

Om problemet kvarstår besöker du någon av följande kanaler för mer support:

- Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

- Anslut [@AzureSupport](https://twitter.com/azuresupport)med . Det här är det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Den kopplar Azure-communityn till rätt resurser: svar, support och experter.

- Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Din Microsoft Azure-prenumeration innehåller åtkomst till prenumerationshantering och faktureringssupport, och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
