---
title: Apache Ambari för att optimera klusterkonfigurationer - Azure HDInsight
description: Använd webbgränssnittet Apache Ambari för att konfigurera och optimera Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: 15a2c75a7619a815655be0fd9fd3044d86acd057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272570"
---
# <a name="use-apache-ambari-to-optimize-hdinsight-cluster-configurations"></a>Använda Apache Ambari för att optimera HDInsight-klusterkonfigurationer

HDInsight tillhandahåller [Apache Hadoop-kluster](https://hadoop.apache.org/) för storskaliga databehandlingsprogram. Det kan vara svårt att hantera, övervaka och optimera dessa komplexa kluster med flera noder. [Apache Ambari](https://ambari.apache.org/) är ett webbgränssnitt för att hantera och övervaka HDInsight Linux-kluster.  Använd [Ambari REST API för Windows-kluster](hdinsight-hadoop-manage-ambari-rest-api.md).

En introduktion till hur du använder webbgränssnittet i Ambari finns i [Hantera HDInsight-kluster med hjälp av webbgränssnittet Apache Ambari](hdinsight-hadoop-manage-ambari.md)

Logga in på Ambari med `https://CLUSTERNAME.azurehdidnsight.net` dina klusterreferenser. Den första skärmen visar en översiktsinstrumentpanel.

![Apache Ambari-användarinstrumentpanel visas](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

Webbgränssnittet Ambari kan användas för att hantera värdar, tjänster, aviseringar, konfigurationer och vyer. Ambari kan inte användas för att skapa ett HDInsight-kluster, uppgradera tjänster, hantera stackar och versioner, inaktivera eller återförbeställa värdar eller lägga till tjänster i klustret.

## <a name="manage-your-clusters-configuration"></a>Hantera klustrets konfiguration

Konfigurationsinställningar hjälper till att ställa in en viss tjänst. Om du vill ändra en tjänsts konfigurationsinställningar väljer du tjänsten i sidofältet **Tjänster** (till vänster) och navigerar sedan till fliken **Configs** på sidan Serviceinformation.

![Apache Ambari Tjänster sidofält](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Ändra NameNode Java heap storlek

Namnnoden Java heap-storlek beror på många faktorer, till exempel belastningen på klustret, antalet filer och antalet block. Standardstorleken på 1 GB fungerar bra med de flesta kluster, även om vissa arbetsbelastningar kan kräva mer eller mindre minne.

Så här ändrar du storleken namenode Java heap:

1. Välj **HDFS** i sidofältet Tjänster och navigera till fliken **Configs.**

    ![Apache Ambari HDFS-konfiguration](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. Hitta inställningen **NameNode Java heap storlek**. Du kan också använda **filtertextrutan** för att skriva och hitta en viss inställning. Välj **pennikonen** bredvid inställningsnamnet.

    ![Apache Ambari NamnNode Java högstorlek](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Skriv det nya värdet i textrutan och tryck sedan **på Retur** för att spara ändringen.

    ![Ambari Redigera NamnNode Java högstorlek1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. NameNode Java heap-storleken ändras till 1 GB från 2 GB.

    ![Redigerad nameNode Java heap size2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Spara ändringarna genom att klicka på den gröna **spara-knappen** högst upp på konfigurationsskärmen.

    ![Ambari Ambari spara konfigurationer](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="apache-hive-optimization"></a>Apache Hive optimering

I följande avsnitt beskrivs konfigurationsalternativ för att optimera den övergripande Apache Hive-prestandan.

1. Om du vill ändra konfigurationsparametrar för Hive väljer du **Hive** i sidofältet Tjänster.
1. Navigera till fliken **Configs.**

### <a name="set-the-hive-execution-engine"></a>Ställ in Hive-körningsmotorn

Hive ger två utförande motorer: [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) och [Apache TEZ](https://tez.apache.org/). Tez är snabbare än MapReduce. HDInsight Linux-kluster har Tez som standardkörningsmotor. Så här ändrar du utförandemotorn:

1. Skriv **körningsmotorn** i filterrutan på fliken Hive **Configs.**

    ![Apache Ambari Sökmotor](./media/hdinsight-changing-configs-via-ambari/ambari-search-execution.png)

1. **Standardvärdet för optimeringsegenskapen** är **Tez**.

    ![Optimering - Apache Tez motor](./media/hdinsight-changing-configs-via-ambari/optimization-apache-tez.png)

### <a name="tune-mappers"></a>Justera mappers

Hadoop försöker dela upp (*karta*) en enda fil i flera filer och bearbeta de resulterande filerna parallellt. Antalet mappers beror på antalet delningar. Följande två konfigurationsparametrar driver antalet delningar för Tez-körningsmotorn:

* `tez.grouping.min-size`: Lägre gräns för storleken på en grupperad split, med ett standardvärde på 16 MB (16 777 216 byte).
* `tez.grouping.max-size`: Övre gräns för storleken på en grupperad delning, med ett standardvärde på 1 GB (1 073 741 824 byte).

Som en prestandaregel för tummen, minska båda dessa parametrar för att förbättra svarstiden, öka för mer dataflöde.

Om du till exempel vill ange fyra mapperuppgifter för en datastorlek på 128 MB anger du båda parametrarna till 32 MB vardera (33 554 432 byte).

1. Om du vill ändra gränsparametrarna navigerar du till fliken **Configs** i tjänsten Tez. Expandera panelen **Allmänt** och `tez.grouping.max-size` leta `tez.grouping.min-size` reda på parametrarna och.

1. Ställ in båda parametrarna på **33 554 432** byte (32 MB).

    ![Apache Ambari Tez gruppering storlekar](./media/hdinsight-changing-configs-via-ambari/apache-tez-grouping-size.png)

Dessa ändringar påverkar alla Tez-jobb på servern. Välj lämpliga parametervärden för att få ett optimalt resultat.

### <a name="tune-reducers"></a>Trimma reducerare

[Apache ORC](https://orc.apache.org/) och [Snappy](https://google.github.io/snappy/) båda erbjuder hög prestanda. Hive kan dock ha för få reducerare som standard, vilket orsakar flaskhalsar.

Anta till exempel att du har en indatastorlek på 50 GB. Dessa data i ORC-format med Snappy komprimering är 1 GB. Hive uppskattar antalet reducerare som behövs som: (antal byte `hive.exec.reducers.bytes.per.reducer`indata till mappers / ).

Med standardinställningarna är det här exemplet 4 reducerare.

Parametern `hive.exec.reducers.bytes.per.reducer` anger antalet byte som bearbetas per reducer. Standardvärdet är 64 MB. Om du justerar ned det här värdet ökar parallellismen och prestandan förbättras. Justera den för lågt kan också ge för många reducerare, vilket kan påverka prestanda negativt. Den här parametern baseras på dina specifika datakrav, komprimeringsinställningar och andra miljöfaktorer.

1. Om du vill ändra parametern navigerar du till fliken Hive **Configs** och hittar parametern **Data per reducer** på sidan Inställningar.

    ![Apache Ambari-data per reducer](./media/hdinsight-changing-configs-via-ambari/ambari-data-per-reducer.png)

1. Välj **Redigera** om du vill ändra värdet till 128 MB (134 217 728 byte) och tryck sedan på **Retur** för att spara.

    ![Ambari Data per reducer - redigerad](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Med en indatastorlek på 1 024 MB, med 128 MB data per reducer, finns det 8 reducer (1024/128).

1. Ett felaktigt värde för parametern **Data per reducer** kan resultera i ett stort antal reducerare som påverkar frågeprestanda negativt. Om du vill begränsa det maximala `hive.exec.reducers.max` antalet reducerare anger du ett lämpligt värde. Standardvärdet är 1009.

### <a name="enable-parallel-execution"></a>Aktivera parallell körning

En Hive-fråga körs i ett eller flera steg. Om de oberoende faserna kan köras parallellt ökar frågeprestanda.

1. Om du vill aktivera parallell körning av frågor navigerar `hive.exec.parallel` du till fliken Hive **Config** och söker efter egenskapen. Standardvärdet är false. Ändra värdet till sant och tryck sedan **på Retur** för att spara värdet.

1. Om du vill begränsa antalet jobb som `hive.exec.parallel.thread.number` ska köras parallellt ändrar du egenskapen. Standardvärdet är 8.

    ![Apache Hive exec parallell display](./media/hdinsight-changing-configs-via-ambari/apache-hive-exec-parallel.png)

### <a name="enable-vectorization"></a>Aktivera vektorisering

Hive bearbetar data rad för rad. Vectorization leder Hive att bearbeta data i block med 1 024 rader i stället för en rad i taget. Vektorisering är endast tillämpligt på ORC-filformatet.

1. Om du vill aktivera en vektoriserad frågekörning navigerar `hive.vectorized.execution.enabled` du till fliken Hive **Configs** och söker efter parametern. Standardvärdet gäller för Hive 0.13.0 eller senare.

1. Om du vill aktivera vektoriserad körning `hive.vectorized.execution.reduce.enabled` för att minska sidan av frågan anger du parametern till true. Standardvärdet är false.

    ![Apache Hive vektoriserad körning](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Aktivera kostnadsbaserad optimering (CBO)

Som standard följer Hive en uppsättning regler för att hitta en optimal frågekörningsplan. Kostnadsbaserad optimering (CBO) utvärderar flera planer för att köra en fråga och tilldelar en kostnad till varje plan och bestämmer sedan den billigaste planen för att köra en fråga.

Om du vill aktivera CBO navigerar du till Inställningarna för > **Hive-konfigurationer** > **Settings** och hittar **Aktivera kostnadsbaserad optimerare**och växlar sedan växlingsknappen till **På**. **Hive**

![HDInsight kostnadsbaserad optimizer](./media/hdinsight-changing-configs-via-ambari/hdinsight-cbo-config.png)

Följande ytterligare konfigurationsparametrar ökar Hive-frågeprestanda när CBO är aktiverat:

* `hive.compute.query.using.stats`

    När den är inställd på true använder Hive statistik som `count(*)`lagras i metabutiken för att svara på enkla frågor som .

    ![Apache Hive-beräkningsfråga med hjälp av statistik](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Kolumnstatistik skapas när CBO är aktiverat. Hive använder kolumnstatistik, som lagras i metabutik, för att optimera frågor. Det tar längre tid att hämta kolumnstatistik för varje kolumn när antalet kolumner är högt. När den här inställningen är inställd på false inaktiveras hämtningskolumnstatistik från metabutiken.

    ![Apache Hive statistik som kolumn statistik](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Grundläggande partitionsstatistik som antal rader, datastorlek och filstorlek lagras i metabutiken. När den är inställd på true hämtas partitionsstatistiken från metabutiken. När det är falskt hämtas filstorleken från filsystemet och antalet rader hämtas från radschemat.

    ![Hive statistik som partition statistik](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Aktivera mellanliggande komprimering

Kartuppgifter skapar mellanliggande filer som används av reducer-uppgifterna. Mellanliggande komprimering krymper den mellanliggande filstorleken.

Hadoop jobb är oftast I / O flaskhals. Komprimera data kan påskynda I/O och den totala nätverksöverföringen.

De tillgängliga komprimeringstyperna är:

| Format | Verktyg | Algoritm | Filtillägg | Splittable? |
| -- | -- | -- | -- | -- |
| Gzip | Gzip | Tömma | .gz | Inga |
| Bzip2 (av )Bzip2 Bzip | Bzip2 (av )Bzip2 Bzip | Bzip2 (av )Bzip2 Bzip |.bz2 (på andra) | Ja |
| Lzo | Lzop | Lzo | . | Ja, om indexeras |
| Snappy | Ej tillämpligt | Snappy | Snappy | Inga |

Som en allmän regel, med komprimeringsmetod splittable är viktigt, annars mycket få mappers kommer att skapas. Om indata är `bzip2` text är det bästa alternativet. För ORC-format är Snappy det snabbaste komprimeringsalternativet.

1. Om du vill aktivera mellanliggande komprimering navigerar `hive.exec.compress.intermediate` du till fliken Hive **Configs** och ställer sedan in parametern på true. Standardvärdet är false.

    ![Hive exec komprimera mellanliggande](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Om du vill komprimera mellanliggande filer väljer du en komprimeringscodec med lägre CPU-kostnad, även om codec inte har en hög komprimeringsutgång.

1. Om du vill ange mellankomprimeringskodc lägger du till den anpassade egenskapen `mapred.map.output.compression.codec` i `hive-site.xml` filen eller. `mapred-site.xml`

1. Så här lägger du till en anpassad inställning:

    a. Navigera till **Hive** > **Configs** > **Advanced** > Custom**hive-site**.

    b. Välj **Lägg till egenskap...** längst ned i fönstret Anpassad registreringsfilt.

    c. I fönstret Lägg till `mapred.map.output.compression.codec` egenskap anger `org.apache.hadoop.io.compress.SnappyCodec` du som nyckel och som värde.

    d. Välj **Lägg till**.

    ![Apache Hive anpassad egenskap lägga till](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Detta komprimerar mellanliggande fil med snappy-komprimering. När egenskapen har lagts till visas den i fönstret Anpassad registreringsfila.

    > [!NOTE]  
    > Den här proceduren `$HADOOP_HOME/conf/hive-site.xml` ändrar filen.

### <a name="compress-final-output"></a>Komprimera slutlig utmatning

Den slutliga Hive-utgången kan också komprimeras.

1. Om du vill komprimera den slutliga Hive-utdata navigerar du `hive.exec.compress.output` till fliken Hive **Configs** och ställer sedan in parametern på true. Standardvärdet är false.

1. Om du vill välja utdatakomprimeringskodifieringskodex lägger du till den `mapred.output.compression.codec` anpassade egenskapen i fönstret Anpassad registreringsfilial, enligt beskrivningen i föregående avsnitts steg 3.

    ![Apache Hive anpassad egenskap add2](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Aktivera spekulativ körning

Spekulativ körning startar ett visst antal dubblettaktiviteter för att identifiera och svartlista den långsamma aktivitetsspåraren, samtidigt som den övergripande jobbkörningen förbättras genom att optimera enskilda uppgiftsresultat.

Spekulativ körning bör inte aktiveras för långvariga MapReduce-uppgifter med stora mängder indata.

* Om du vill aktivera spekulativ körning navigerar du till `hive.mapred.reduce.tasks.speculative.execution` fliken Hive **Configs** och ställer sedan in parametern på true. Standardvärdet är false.

    ![Hive mapred minska uppgifter spekulativa utförande](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Justera dynamiska partitioner

Hive gör det möjligt att skapa dynamiska partitioner när du infogar poster i en tabell, utan att fördefiniera varje partition. Detta är en kraftfull funktion, även om det kan resultera i skapandet av ett stort antal partitioner och ett stort antal filer för varje partition.

1. För att Hive ska kunna `hive.exec.dynamic.partition` göra dynamiska partitioner bör parametervärdet vara sant (standardvärdet).

1. Ändra det dynamiska partitionsläget till *strikt*. I strikt läge måste minst en partition vara statisk. Detta förhindrar frågor utan partitionsfiltret i WHERE-satsen, det vill än *strikt,* förhindrar frågor som söker igenom alla partitioner. Navigera till fliken Hive **Configs** `hive.exec.dynamic.partition.mode` och ange sedan **strikt**. Standardvärdet är **nonstrict**.

1. Om du vill begränsa antalet dynamiska partitioner `hive.exec.max.dynamic.partitions` som ska skapas ändrar du parametern. Standardvärdet är 5000.

1. Om du vill begränsa det totala antalet `hive.exec.max.dynamic.partitions.pernode`dynamiska partitioner per nod ändrar du . Standardvärdet är 2000.

### <a name="enable-local-mode"></a>Aktivera lokalt läge

Lokalt läge gör det möjligt för Hive att utföra alla uppgifter i ett jobb på en enda dator, eller ibland i en enda process. Detta förbättrar frågeprestanda om indata är små och omkostnaderna för att starta aktiviteter för frågor förbrukar en betydande procentandel av den totala frågekörningen.

Om du vill aktivera `hive.exec.mode.local.auto` lokalt läge lägger du till parametern på panelen Anpassad registreringsplats, vilket förklaras i steg 3 i avsnittet [Aktivera mellanliggande komprimering.](#enable-intermediate-compression)

![Apache Hive exec-läge lokal auto](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Ange enstaka MapReduce MultiGROUP BY

När den här egenskapen är true genererar en MultiGROUP BY-fråga med vanliga grupp-för-nycklar ett enda MapReduce-jobb.  

Om du vill aktivera `hive.multigroupby.singlereducer` det här beteendet lägger du till parametern i fönstret Anpassad registreringsplats, vilket förklaras i steg 3 i avsnittet [Aktivera mellanliggande komprimering.](#enable-intermediate-compression)

![Hive set enda MapReduce MultiGROUP BY](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Ytterligare Hive optimeringar

I följande avsnitt beskrivs ytterligare Hive-relaterade optimeringar som du kan ange.

#### <a name="join-optimizations"></a>Gå med optimeringar

Standardkopplingstypen i Hive är en *shuffle-koppling*. I Hive läser särskilda mappers indata och avger ett kopplingsnyckel/värdepar till en mellanliggande fil. Hadoop sorterar och sammanfogar dessa par i en shuffle-fas. Denna shuffle skede är dyrt. Om du väljer rätt koppling baserat på dina data kan prestanda förbättras avsevärt.

| Koppla till typ | När | Hur | Inställningar för Hive | Kommentarer |
| -- | -- | -- | -- | -- |
| Blanda koppling | <ul><li>Standardalternativ</li><li>Fungerar alltid</li></ul> | <ul><li>Läser från en del av en av tabellerna</li><li>Buckets och sorterar på Kopplingsnyckel</li><li>Skickar en bucket till varje minska</li><li>Kopplingen sker på reduce-sidan</li></ul> | Ingen signifikant Hive-inställning behövs | Fungerar varje gång |
| Karta Gå | <ul><li>En tabell får plats i minnet</li></ul> | <ul><li>Läser liten tabell i minneshh-tabellen</li><li>Strömmar genom en del av den stora filen</li><li>Sammanfogar varje post från hash-tabellen</li><li>Kopplingar är enbart av mappern</li></ul> | `hive.auto.confvert.join=true` | Mycket snabb, men begränsad |
| Sortera kopplingsgrupp | Om båda tabellerna är: <ul><li>Sorterade på samma sätt</li><li>Skopa samma</li><li>Sammanfoga i den sorterade/bucketed kolumnen</li></ul> | Varje process: <ul><li>Läser en bucket från varje tabell</li><li>Bearbetar raden med det lägsta värdet</li></ul> | `hive.auto.convert.sortmerge.join=true` | Mycket effektiv |

#### <a name="execution-engine-optimizations"></a>Optimering av utförandemotor

Ytterligare rekommendationer för att optimera Hive-körningsmotorn:

| Inställning | Rekommenderas | Standard för HDInsight |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | Sant = säkrare, långsammare; falskt = snabbare | false |
| `tez.am.resource.memory.mb` | 4 GB övre gräns för de flesta | Justerad automatiskt |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="apache-pig-optimization"></a>Apache Pig optimering

[Apache Pig](https://pig.apache.org/) egenskaper kan ändras från Ambari webbgränssnitt för att ställa pig frågor. Om du ändrar Pig-egenskaper från Ambari `/etc/pig/2.4.2.0-258.0/pig.properties` ändras pigegenskaperna direkt i filen.

1. Om du vill ändra egenskaper för Gris navigerar du till fliken **Griskonfiguration och** expanderar sedan fönstret **Avancerade grisegenskaper.**

1. Hitta, ta bort kommentet och ändra värdet på den egenskap som du vill ändra.

1. Välj **Spara** längst upp till höger i fönstret för att spara det nya värdet. Vissa egenskaper kan kräva en omstart av tjänsten.

    ![Avancerade apachegrisegenskaper](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)

> [!NOTE]  
> Alla inställningar på sessionsnivå åsidosätter egenskapsvärden `pig.properties` i filen.

### <a name="tune-execution-engine"></a>Trimma körningsmotor

Två utförande motorer är tillgängliga för att köra Pig skript: MapReduce och Tez. Tez är en optimerad motor och är mycket snabbare än MapReduce.

1. Om du vill ändra körningsmotorn hittar du `exectype`egenskapen i fönstret **Avancerade grisegenskaper** .

1. Standardvärdet är **MapReduce**. Ändra den till **Tez**.

### <a name="enable-local-mode"></a>Aktivera lokalt läge

I likhet med Hive används lokalt läge för att snabba jobb med relativt mindre mängder data.

1. Om du vill aktivera `pig.auto.local.enabled` det lokala läget ställer du in **på true**. Standardvärdet är false.

1. Jobb med en indatastorlek `pig.auto.local.input.maxbytes` som är mindre än egenskapsvärdet anses vara små jobb. Standardvärdet är 1 GB.

### <a name="copy-user-jar-cache"></a>Kopiera cacheminne för användarburk

Pig kopierar JAR-filer som krävs av UDF till en distribuerad cache för att göra dem tillgängliga för aktivitetsnoder. Dessa burkar ändras inte ofta. Om den aktiveras tillåter `pig.user.cache.enabled` inställningen att burkar placeras i en cache för att återanvända dem för jobb som körs av samma användare. Detta resulterar i en mindre ökning av arbetsprestationer.

1. Om du `pig.user.cache.enabled` vill aktivera ställer du in på true. Standardvärdet är falskt.

1. Om du vill ställa in bassökvägen `pig.user.cache.location` för de cachelagrade burkarna ställer du in på bassökvägen. Standardvärdet är `/tmp`.

### <a name="optimize-performance-with-memory-settings"></a>Optimera prestanda med minnesinställningar

Följande minnesinställningar kan hjälpa till att optimera Pig-skriptprestanda.

* `pig.cachedbag.memusage`: Mängden minne som allokeras till en påse. En påse är samling av tupplar. En tuppel är en ordnad uppsättning fält och ett fält är en del data. Om data i en påse ligger utanför det tilldelade minnet spills de till disken. Standardvärdet är 0,2, vilket motsvarar 20 procent av det tillgängliga minnet. Det här minnet delas över alla påsar i ett program.

* `pig.spill.size.threshold`: Väskor som är större än denna spillstorlekströskel (i byte) spills till disken. Standardvärdet är 5 MB.

### <a name="compress-temporary-files"></a>Komprimera temporära filer

Gris genererar temporära filer under jobbkörningen. Om du komprimerar de temporära filerna ökar prestandaökningen när filer läss eller skrivs till disk. Följande inställningar kan användas för att komprimera temporära filer.

* `pig.tmpfilecompression`: När värdet är true aktiverar den tillfälliga filkomprimeringen. Standardvärdet är false.

* `pig.tmpfilecompression.codec`: Komprimeringskodifieringen som ska användas för att komprimera de temporära filerna. De rekommenderade komprimeringskoderna är [LZO](https://www.oberhumer.com/opensource/lzo/) och Snappy för lägre CPU-användning.

### <a name="enable-split-combining"></a>Aktivera delad kombination

När det är aktiverat kombineras små filer för färre kartuppgifter. Detta förbättrar effektiviteten i jobb med många små filer. Om du `pig.noSplitCombination` vill aktivera ställer du in på true. Standardvärdet är false.

### <a name="tune-mappers"></a>Justera mappers

Antalet mappers styrs genom att `pig.maxCombinedSplitSize`ändra egenskapen . Detta anger storleken på de data som ska bearbetas av en enda kartuppgift. Standardvärdet är filsystemets standardblockstorlek. Om du ökar det här värdet minskar antalet mappningsaktiviteter.

### <a name="tune-reducers"></a>Trimma reducerare

Antalet reducerare beräknas baserat på `pig.exec.reducers.bytes.per.reducer`parametern . Parametern anger antalet byte som bearbetas per reducer, som standard 1 GB. Om du vill begränsa det maximala `pig.exec.reducers.max` antalet reducerare anger du egenskapen som standard 999.

## <a name="apache-hbase-optimization-with-the-ambari-web-ui"></a>Apache HBase optimering med Ambari webbgränssnitt

[Apache HBase-konfigurationen](https://hbase.apache.org/) ändras från fliken **HBase Configs.** I följande avsnitt beskrivs några av de viktiga konfigurationsinställningarna som påverkar HBase-prestanda.

### <a name="set-hbase_heapsize"></a>Ställ in HBASE_HEAPSIZE

HBase heap-storleken anger den maximala mängden heap som ska användas i megabyte per *region* och *huvudservrar.* Standardvärdet är 1 000 MB. Detta bör justeras för klusterarbetsbelastningen.

1. Om du vill ändra navigerar du till fönstret **Avancerat HBase-env** `HBASE_HEAPSIZE` på fliken HBase **Configs** och sedan efter inställningen.

1. Ändra standardvärdet till 5 000 MB.

    ![Apache Ambari HBase minne heapsize](./media/hdinsight-changing-configs-via-ambari/ambari-hbase-heapsize.png)

### <a name="optimize-read-heavy-workloads"></a>Optimera lästunga arbetsbelastningar

Följande konfigurationer är viktiga för att förbättra prestanda för lästunga arbetsbelastningar.

#### <a name="block-cache-size"></a>Blockera cachestorlek

Blockcachen är läscachen. Dess storlek styrs `hfile.block.cache.size` av parametern. Standardvärdet är 0,4, vilket är 40 procent av det totala regionserverminnet. Ju större blockcachestorlek, desto snabbare blir slumpmässiga läsningar.

1. Om du vill ändra den här parametern navigerar du till fliken **Inställningar** på fliken HBase **Configs** och letar sedan reda på **% av RegionServer som allokerats till läsbuffertar**.

    ![Apache HBase minnesblock cachestorlek](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)

1. Om du vill ändra värdet markerar du ikonen **Redigera.**

#### <a name="memstore-size"></a>Memstore storlek

Alla redigeringar lagras i minnesbufferten, som kallas *en Memstore*. Detta ökar den totala mängden data som kan skrivas till disk i en enda åtgärd, och det snabbar efterföljande åtkomst till de senaste redigeringarna. Memstore-storleken definieras av följande två parametrar:

* `hbase.regionserver.global.memstore.UpperLimit`: Definierar den maximala procentandelen av regionservern som Memstore kombinerade kan använda.

* `hbase.regionserver.global.memstore.LowerLimit`: Definierar den minsta procentandel av regionservern som Memstore kombinerade kan använda.

Om du vill optimera för slumpmässiga läsningar kan du minska Memstores övre och nedre gränser.

#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Antal rader som hämtas vid skanning från disk

Inställningen `hbase.client.scanner.caching` definierar antalet rader som läs avstÃ¤r från disken `next` när metoden anropas på en skanner.  Standardvärdet är 100. Ju högre siffra, desto färre fjärranrop som görs från klienten till regionservern, vilket resulterar i snabbare genomsökningar. Detta kommer dock också att öka minnestrycket på klienten.

![Apache HBase antal rader som hämtats](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Ange inte värdet så att tiden mellan anrop av nästa metod på en skanner är större än skannertidsgränsen. Tidsgränsen för skannern `hbase.regionserver.lease.period` bestäms av egenskapen.

### <a name="optimize-write-heavy-workloads"></a>Optimera skrivtunga arbetsbelastningar

Följande konfigurationer är viktiga för att förbättra prestanda för skrivtunga arbetsbelastningar.

#### <a name="maximum-region-file-size"></a>Maximal regionfilstorlek

HBase lagrar data i ett internt filformat, kallat *HFile*. Egenskapen `hbase.hregion.max.filesize` definierar storleken på en enda HFile för en region.  En region är uppdelad i två regioner om summan av alla HFiles i en region är större än den här inställningen.

![Apache HBase HRegion max filstorlek](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Ju större regionfilstorlek, desto mindre antal delar. Du kan öka filstorleken för att bestämma ett värde som resulterar i den maximala skrivprestanda.

#### <a name="avoid-update-blocking"></a>Undvik uppdateringsblockering

* Egenskapen `hbase.hregion.memstore.flush.size` definierar storleken på vilken Memstore spolas till disk. Standardstorleken är 128 MB.

* HBase-regionblockmultiplikatorn `hbase.hregion.memstore.block.multiplier`definieras av . Standardvärdet är 4. Det högsta tillåtna är 8.

* HBase blockerar uppdateringar om Memstore är (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) byte.

    Med standardvärdena för spolstorlek och blockmultiplikator blockeras uppdateringar när Memstore är 128 * 4 = 512 MB i storlek. Om du vill minska antalet uppdateringsblockering ökar du `hbase.hregion.memstore.block.multiplier`värdet för .

![Apache HBase-regionblockmultiplikator](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)

### <a name="define-memstore-size"></a>Definiera Memstore-storlek

Memstore storlek definieras `hbase.regionserver.global.memstore.UpperLimit` av `hbase.regionserver.global.memstore.LowerLimit` och parametrar. Om du ställer in dessa värden som är lika med varandra minskar pauserna under skrivningar (vilket också orsakar mer frekvent spolning) och resulterar i ökad skrivprestanda.

### <a name="set-memstore-local-allocation-buffer"></a>Ange memstore lokal allokeringsbuffert

Memstore lokal allokeringsbuffertanvändning bestäms av egenskapen `hbase.hregion.memstore.mslab.enabled`. När det är aktiverat (sant) förhindrar detta heap-fragmentering under tung skrivåtgärd. Standardvärdet är True.

![hbase.hregion.memstore.mslab.enabled hbase.hregion.memstore.mslab.enabled hbase.hregion.memstore.mslab.enabled hbase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Nästa steg

* [Hantera HDInsight-kluster med apache Ambari-webbgränssnittet](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
