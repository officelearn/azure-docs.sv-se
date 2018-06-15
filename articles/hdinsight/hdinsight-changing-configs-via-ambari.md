---
title: Optimera klusterkonfigurationer med Ambari - Azure HDInsight | Microsoft Docs
description: Använda Ambari-webbgränssnittet för att konfigurera och optimera HDInsight-kluster.
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: ashish
ms.openlocfilehash: f3c1edc767ab07bcdd8b09a0e40e291cbd1f3d9a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31406195"
---
# <a name="use-ambari-to-optimize-hdinsight-cluster-configurations"></a>Använda Ambari och optimera klusterkonfigurationer för HDInsight

HDInsight tillhandahåller Apache Hadoop-kluster för storskalig databearbetning program. Hantera, övervaka och optimera dessa komplexa kluster med flera noder kan vara svårt. [Apache Ambari](http://ambari.apache.org/) är ett webbgränssnitt för att hantera och övervaka kluster i HDInsight Linux.  För Windows-kluster, använda Ambari [REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

En introduktion till att använda Ambari-Webbgränssnittet finns [hantera HDInsight-kluster med Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md)

Logga in på Ambari på `https://CLUSTERNAME.azurehdidnsight.net` med dina autentiseringsuppgifter för klustret. Den första skärmen visar en översikt över instrumentpanelen.

![Ambari-instrumentpanelen](./media/hdinsight-changing-configs-via-ambari/ambari-dashboard.png)

Ambari-webbgränssnittet kan användas för att hantera värdar, tjänster, aviseringar, konfigurationer och vyer. Ambari kan inte användas för att skapa ett HDInsight-kluster, uppgradera tjänster, hantera stackar och versioner, inaktivera eller recommission värdar eller lägga till tjänster till klustret.

## <a name="manage-your-clusters-configuration"></a>Hantera din klusterkonfiguration

Inställningar för att finjustera en viss tjänst. För att ändra konfigurationsinställningarna för en tjänst, Välj tjänsten från den **Services** sidopanelen (till vänster) och gå sedan till den **konfigurationerna** fliken i tjänsten detaljsida.

![Tjänster sidopanelen](./media/hdinsight-changing-configs-via-ambari/services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Ändra NameNode Java heap-storlek

Heap-storlek för NameNode Java beror på många faktorer, till exempel belastningen på klustret, hur många filer och antal block. Standardstorleken på 1 GB fungerar bra med de flesta kluster, även om vissa arbetsbelastningar kan kräva mer eller mindre minne. 

Ändra storlek på NameNode Java-heap:

1. Välj **HDFS** från sidopanelen tjänster och navigera till den **konfigurationerna** fliken.

    ![HDFS-konfiguration](./media/hdinsight-changing-configs-via-ambari/hdfs-config.png)

2. Inställning **NameNode Java stackstorleken**. Du kan också använda den **filter** textruta för att ange och söka efter en viss inställning. Välj den **penna** ikonen bredvid Inställningens namn.

    ![NameNode Java heap-storlek](./media/hdinsight-changing-configs-via-ambari/java-heap-size.png)

3. Skriv det nya värdet i textrutan och tryck sedan på **RETUR** att spara ändringen.

    ![Redigera NameNode Java heap-storlek](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit.png)

4. NameNode Java heap-storlek ändras till 2 GB från 1 GB.

    ![Redigera NameNode Java heap-storlek](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

5. Spara dina ändringar genom att klicka på gröna **spara** knappen överst skärm för konfiguration.

    ![Spara ändringar](./media/hdinsight-changing-configs-via-ambari/save-changes.png)

## <a name="hive-optimization"></a>Hive optimering

I följande avsnitt beskrivs konfigurationsalternativ för att optimera prestandan för Hive.

1. Om du vill ändra Hive konfigurationsparametrar, Välj **Hive** från tjänster sidopanelen.
2. Navigera till den **konfigurationerna** fliken.

### <a name="set-the-hive-execution-engine"></a>Ange motorn för körning av Hive

Hive innehåller två körning motorer: MapReduce och Tez. Tez är snabbare än MapReduce. HDInsight Linux-kluster har Tez som motorn för körning av standard. Ändra motorn för körning:

1. I registreringsdatafilen **konfigurationerna** Skriv **motorn för körning av** i filterfältet.

    ![Motorn för körning av sökning](./media/hdinsight-changing-configs-via-ambari/search-execution.png)

2. Den **optimering** standardvärde är **Tez**.

    ![Optimering - Tez](./media/hdinsight-changing-configs-via-ambari/optimization-tez.png)

### <a name="tune-mappers"></a>Finjustera mappers

Hadoop försöker dela (*kartan*) en enskild fil i flera filer och den resulterande processen filer parallellt. Antalet mappers beror på antalet delningar. Följande konfiguration med två parametrar öka antalet delningar för Tez-Körningsmotor:

* `tez.grouping.min-size`: Nedre gräns för storleken på en grupperad delning med ett standardvärde 16 MB (16 777 216 byte).
* `tez.grouping.max-size`: Övre gräns för storleken på en grupperad delning med ett standardvärde på 1 GB (1 073 741 824 byte).

Minska båda parametrarna öka för snabbare dataflöde för att förbättra svarstiden som en prestanda tumregel.

Till exempel för att ange fyra mapper uppgifter för en datastorleken 128 MB, skulle du ange båda parametrarna till högst 32 MB varje (33,554,432 byte).

1. Om du vill ändra parametrarna gränsen, navigera till den **konfigurationerna** fliken i Tez-tjänsten. Expandera den **allmänna** panelen och leta upp den `tez.grouping.max-size` och `tez.grouping.min-size` parametrar.

2. Ange båda parametrarna till **33,554,432** byte (32 MB).

    ![Tez gruppering storlekar](./media/hdinsight-changing-configs-via-ambari/tez-grouping-size.png)
 
Dessa ändringar påverkar alla jobb på Tez för hela servern. Välj lämpliga värden för att få en optimala resultat.

### <a name="tune-reducers"></a>Finjustera förminskningsapparater

ORC och Snappy ger hög prestanda. Hive kan dock ha för få förminskningsapparater som standard orsakar flaskhalsar.

Anta exempelvis att du har en storlek för indata på 50 GB. Att formatera data i ORC med snygga komprimering är 1 GB. Hive beräknar antalet förminskningsapparater behövs som: (antal byte som indata till mappers / `hive.exec.reducers.bytes.per.reducer`).

Det här exemplet är 4 förminskningsapparater med standardinställningarna.

Den `hive.exec.reducers.bytes.per.reducer` parameter anger antalet byte som bearbetas per reducer. Standardvärdet är 64 MB. Justera det här värdet anges ökar parallellitet och kan förbättra prestanda. Justera det för lågt kan också skapa för många förminskningsapparater som potentiellt påverka prestanda. Den här parametern baseras på dina krav för vissa uppgifter komprimeringsinställningar och andra faktorer.

1. Om du vill ändra parametern, navigera till registreringsdatafilen **konfigurationerna** fliken och Sök efter den **Data per Reducer** parameter på inställningssidan.

    ![Data per Reducer](./media/hdinsight-changing-configs-via-ambari/data-per-reducer.png)
 
2. Välj **redigera** att ändra värdet till 128 MB (134,217,728 byte) och tryck sedan på **RETUR** att spara.

    ![Data per Reducer - redigeras](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Få en inkommande storlek på 1 024 MB med 128 MB data per reducer, finns 8 förminskningsapparater (1024/128).

3. Ett ogiltigt värde för den **Data per Reducer** parameter kan resultera i ett stort antal förminskningsapparater, negativt påverka prestanda för frågor. Om du vill begränsa det maximala antalet förminskningsapparater ange `hive.exec.reducers.max` till ett lämpligt värde. Standardvärdet är 1009.

### <a name="enable-parallel-execution"></a>Aktivera parallell körning

En Hive-fråga körs i ett eller flera steg. Om oberoende steg kan köras parallellt, som kommer att öka prestanda för frågor.

1.  Om du vill aktivera parallell frågekörning, navigera till registreringsdatafilen **Config** fliken och Sök efter den `hive.exec.parallel` egenskapen. Standardvärdet är false. Ändrar du värdet till true, och tryck sedan på **RETUR** att spara värdet.
 
2.  Om du vill begränsa antalet jobb som körs parallellt, ändra den `hive.exec.parallel.thread.number` egenskapen. Standardvärdet är 8.

    ![Hive exec parallellt](./media/hdinsight-changing-configs-via-ambari/hive-exec-parallel.png)


### <a name="enable-vectorization"></a>Aktivera vectorization

Hive bearbetar data rad för rad. Vectorization dirigerar Hive för bearbetning av data i block om 1 024 rader i stället för en rad i taget. Vectorization gäller endast till ORC-format.

1. Om du vill aktivera en vectorized Frågekörningen, navigera till registreringsdatafilen **konfigurationerna** fliken och Sök efter den `hive.vectorized.execution.enabled` parameter. Standardvärdet är true för Hive 0.13.0 eller senare.
 
2. Om du vill aktivera vectorized körningen för minska sida av frågan, ange den `hive.vectorized.execution.reduce.enabled` parameter till true. Standardvärdet är false.

    ![Vectorized hive-körning](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Aktivera kostnad-optimering (CBO)

Som standard följer Hive en uppsättning regler för att hitta en optimal körning frågeplanen. Kostnad-optimering (CBO) utvärderar flera planer för att köra en fråga och tilldelar varje plan kostnader och sedan bestämmer billigaste planen för att köra en fråga.

Om du vill aktivera CBO, navigera till registreringsdatafilen **konfigurationerna** fliken och Sök efter `parameter hive.cbo.enable`, växla växlingsknappen till **på**.

![CBO config](./media/hdinsight-changing-configs-via-ambari/cbo.png)

De ytterligare konfigurationsparametrarna öka prestanda för Hive-frågor när CBO aktiveras:

* `hive.compute.query.using.stats`

    När värdet är true Hive använder statistik som lagras i dess metastore för att besvara enkla frågor som `count(*)`.

    ![CBO statistik](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Kolumnen statistik skapas när CBO är aktiverad. Hive använder kolumnstatistik, som lagras i metastore, för att optimera frågor. Det tar längre tid att hämta kolumnstatistik för varje kolumn när antalet kolumner är hög. Värdet false när inaktiverar den här inställningen hämtning kolumnstatistik från metastore.

    ![Hive stats set kolumn statistik](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Grundläggande partitionsstatistik såsom antal rader, storlek och storlek som lagras i metastore. När inställt på true och partitionen stats hämtas från metastore. Om värdet är false filstorleken hämtas från filsystemet och antalet rader som hämtas från raden schemat.

    ![Hive stats set partition statistik](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Aktivera komprimering av mellanliggande

Karta uppgifter skapa mellanliggande filer som används av reducer uppgifter. Mellanliggande komprimering krymper mellanliggande filstorleken.

Hadoop-jobb är vanligtvis i/o en flaskhals. Komprimera data kan snabbare i/o och övergripande nätverksöverföring.

Av tillgängliga komprimeringstyper är:

| Format | Verktyget | Algoritmen | Filnamnstillägg | Delbara? |
| -- | -- | -- | -- | -- |
| Gzip | Gzip | DEFLATE | .GZ | Nej |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | Ja |
| LZO | Lzop | LZO | .lzo | Ja, om indexerade |
| snygga | Gäller inte | snygga | snygga | Nej |

Som en allmän regel med komprimeringsmetoden delbara är viktigt, annars mycket få mappers kommer att skapas. Om indata är text, `bzip2` är det bästa alternativet. ORC-format är Snappy det snabbaste komprimeringsalternativet.

1. Om du vill aktivera komprimering av mellanliggande, navigera till registreringsdatafilen **konfigurationerna** fliken och ange sedan den `hive.exec.compress.intermediate` parameter till true. Standardvärdet är false.

    ![Hive exec komprimera mellanliggande](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]
    > Om du vill komprimera mellanliggande filer, väljer du en komprimerings-codec med lägre CPU kostnad, även om codecen som inte har en hög komprimering utdata.

2. Lägg till den anpassade egenskapen för att ange mellanliggande komprimerings-codec `mapred.map.output.compression.codec` till den `hive-site.xml` eller `mapred-site.xml` filen.

3. Lägg till en anpassad inställning:

    a. Navigera till registreringsdatafilen **konfigurationerna** fliken och markera den **Avancerat** fliken.

    b. Under den **Avancerat** fliken, hitta och expandera den **anpassade hive-plats** fönstret.

    c. Klicka på länken **Lägg till egenskap** längst ned i fönstret för anpassade hive-plats.

    d. I fönstret Lägg till egenskap anger `mapred.map.output.compression.codec` som nyckel och `org.apache.hadoop.io.compress.SnappyCodec` som värde.

    e. Klicka på **Lägg till**.

    ![Hive anpassad egenskap](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Detta ska komprimera filen mellanliggande med snygga komprimering. När egenskapen har lagts till, visas det i anpassade hive-fönstret.

    > [!NOTE]
    > Den här proceduren ändrar den `$HADOOP_HOME/conf/hive-site.xml` filen.

### <a name="compress-final-output"></a>Komprimera slutversionen

Det slutgiltiga resultatet Hive kan också komprimeras.

1. Om du vill komprimera slutversionen Hive, navigera till registreringsdatafilen **konfigurationerna** fliken och ange sedan den `hive.exec.compress.output` parameter till true. Standardvärdet är false.

2. Välj utdata komprimerings-codec genom att lägga till den `mapred.output.compression.codec` egenskapen till anpassade hive-fönstret, enligt beskrivningen i föregående avsnitt steg 3.

    ![Hive anpassad egenskap](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Aktivera spekulativ exekvering

Spekulativ exekvering startar antalet duplicerade uppgifter för att identifiera och svartlista aktivitet spåraren sakta körs samtidigt förbättra den övergripande jobbkörningen genom att optimera aktivitet resultat.

Spekulativ exekvering får inte aktiveras för MapReduce tidskrävande uppgifter med stora mängder indata.

* Om du vill aktivera spekulativ exekvering, navigera till registreringsdatafilen **konfigurationerna** fliken och ange sedan den `hive.mapred.reduce.tasks.speculative.execution` parameter till true. Standardvärdet är false.

    ![Hive mapred minska uppgifter spekulativ exekvering](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Finjustera dynamiska partitioner

Hive gör för att skapa dynamiska partitioner när lägga till poster i en tabell utan fördefiniera varje partition. Det är en kraftfull funktion, men det kan resultera i att skapa ett stort antal partitioner och ett stort antal filer för varje partition.

1. För Hive att göra dynamiska partitioner i `hive.exec.dynamic.partition` parametervärdet ska vara sant (standard).

2. Ändra dynamiska partitionsläget *strikt*. Minst en partition måste vara statiska i strikt läge. Detta förhindrar frågor utan partition filter i WHERE-satsen som är *strikt* förhindrar frågor som söker av alla partitioner. Navigera till registreringsdatafilen **konfigurationerna** fliken och ange sedan `hive.exec.dynamic.partition.mode` till **strikt**. Standardvärdet är **nonstrict**.
 
3. Om du vill begränsa antalet dynamiska partitioner som ska skapas, ändra den '' hive.exec.max.dynamic.partitions'-parametern. Standardvärdet är 5 000.
 
4. Om du vill begränsa det totala antalet dynamiska partitioner per nod, ändra `hive.exec.max.dynamic.partitions.pernode`. Standardvärdet är 2 000.

### <a name="enable-local-mode"></a>Aktivera lokalt läge

Lokalt läge gör Hive att utföra alla uppgifter i ett jobb på en enskild dator eller ibland i en enda process. Detta förbättrar frågeprestanda om indata är liten och kostnader för att starta aktiviteter för frågor av förbrukar en stor del av den övergripande Frågekörningen.

Om du vill aktivera lokalt läge, lägger du till den `hive.exec.mode.local.auto` parameter för anpassade hive-plats panelen enligt beskrivningen i steg 3 i den [aktivera komprimering av mellanliggande](#enable-intermediate-compression) avsnitt.

![Hive exec läge lokala automatiskt](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Ange enskild MapReduce MultiGROUP av

När den här egenskapen har angetts till true, en MultiGROUP genom att fråga med vanliga Gruppera efter nycklar genererar ett enda MapReduce-jobb.  

Aktivera det här problemet genom att lägga till den `hive.multigroupby.singlereducer` parameter för anpassade hive-fönstret, enligt beskrivningen i steg 3 i den [aktivera komprimering av mellanliggande](#enable-intermediate-compression) avsnitt.

![Hive som enda MapReduce MultiGROUP](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Ytterligare Hive optimeringar

I följande avsnitt beskrivs ytterligare Hive-relaterade optimeringar som du kan ange.

#### <a name="join-optimizations"></a>Anslut optimeringar

Standardtyp i Hive är en *blanda koppling*. Särskilda mappers läsa indata i Hive och genererar en koppling nyckel/värde-par till ett mellanliggande fil. Hadoop sorterar och slår ihop dessa par i en blandad fas. Det här steget blanda är dyr. Att välja rätt koppling baserat på dina data förbättrar prestandan.

| Anslutningstyp | När | Hur | Hive-inställningar | Kommentarer |
| -- | -- | -- | -- | -- |
| Blanda koppling | <ul><li>Standardalternativet</li><li>Alltid fungerar</li></ul> | <ul><li>Läser från en del av en tabell</li><li>Buckets och sorterar på den nyckel som koppling</li><li>Skickar en bucket till varje minska</li><li>Anslut till görs på minska sida</li></ul> | Inga betydande Hive inställningen behövs | Fungerar varje gång |
| Mappa koppling | <ul><li>En tabell får plats i minnet</li></ul> | <ul><li>Läser in liten tabell i minnet hash-tabell</li><li>Dataströmmar via en del av stora filer</li><li>Ansluter till varje post från hash-tabell</li><li>Kopplingar är av den enbart</li></ul> | `hive.auto.confvert.join=true` | Mycket snabbt, men begränsad |
| Sortera utskrift Bucket | Om båda tabellerna: <ul><li>Sorteras samma</li><li>Bucketed samma</li><li>Koppla på kolumnen sorteras/bucketgrupperade</li></ul> | Varje process: <ul><li>Läser en bucket från varje tabell</li><li>Bearbetar raden med det lägsta värdet</li></ul> | `hive.auto.convert.sortmerge.join=true` | Mycket effektivt |

#### <a name="execution-engine-optimizations"></a>Körning av motorn optimeringar

Ytterligare rekommendationer för optimering av motorn för körning av Hive:

| Inställning | Rekommenderas | HDInsight standard |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | SANT = säkrare, långsammare; = FALSE snabbare | false |
| `tez.am.resource.memory.mb` | 4 GB övre gränsen för de flesta | Automatiskt justerade |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="pig-optimization"></a>Pig-optimering

Pig-egenskaper kan ändras från det Ambari-webbgränssnittet att justera Pig frågor. Ändra egenskaperna för Pig från Ambari direkt ändrar Pig-egenskaperna i den `/etc/pig/2.4.2.0-258.0/pig.properties` filen.

1. Om du vill ändra egenskaper för Pig, navigera till Pig **konfigurationerna** fliken och expandera sedan den **avancerade egenskaper för pig** fönstret.

2. Hitta, ta bort kommentarerna och ändra värdet på egenskapen som du vill ändra.

3. Välj **spara** på upp till höger i fönstret för att spara det nya värdet. Vissa egenskaper kan kräva en omstart av tjänsten.

    ![Avancerade pig-egenskaper](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)
 
> [!NOTE]
> Alla session nivå åsidosätter egenskapsvärden i den `pig.properties` filen.

### <a name="tune-execution-engine"></a>Finjustera motorn för körning

Två körning motorer är tillgängliga att köra Pig-skript: MapReduce och Tez. Tez är mycket snabbare än MapReduce är en optimerad motor.

1. Ändra motorn för körning i den **avancerade egenskaper för pig** rutan hitta egenskapen `exectype`.

2. Standardvärdet är **MapReduce**. Ändra den till **Tez**.


### <a name="enable-local-mode"></a>Aktivera lokalt läge

Liknar Hive, lokalt läge används för att påskynda jobb med relativt mindre mängder data.

1. Om du vill aktivera lokalt läge, ange `pig.auto.local.enabled` till **SANT**. Standardvärdet är false.

2. Jobb med en inkommande datastorleken understiger `pig.auto.local.input.maxbytes` egenskapsvärde anses vara små jobb. Standardvärdet är 1 GB.


### <a name="copy-user-jar-cache"></a>Kopiera användaren jar-cache

Pig kopieras JAR-filerna som krävs av UDF: er till en distribuerad cache för att göra dem tillgängliga för aktiviteten noder. Dessa burkar ändras inte ofta. Om aktiverad kan den `pig.user.cache.enabled` inställningen burkar ska placeras i en cache för att återanvända för jobben körs av samma användare. Detta resulterar i en mindre prestandaökning jobb.

1. Att aktivera, ange `pig.user.cache.enabled` till true. Standardvärdet är FALSKT.

2. Att ställa in rotsökvägen för cachelagrade burkar `pig.user.cache.location` till rotsökvägen. Standardvärdet är `/tmp`.


### <a name="optimize-performance-with-memory-settings"></a>Optimera prestanda med minnesinställningarna

Följande minnesinställningarna kan hjälpa dig att optimera prestanda för Pig-skriptet.

* `pig.cachedbag.memusage`: Mängden minne som allokerats till en egenskapsuppsättning. En egenskapsuppsättning är en samling av tupplar. En tuppel är en ordnad uppsättning fält och ett fält är en typ av data. Om data i en egenskapsuppsättning utöver allokerat minne är det hamnat till disk. Standardvärdet är 0,2 som motsvarar 20 procent av det tillgängliga minnet. Det här minnet delas mellan alla påsar i ett program.

* `pig.spill.size.threshold`: Påsar större än tröskelvärdet oljesanering storlek (i byte) har hamnat till disk. Standardvärdet är 5 MB.


### <a name="compress-temporary-files"></a>Komprimera temporära filer

Pig genererar temporära filer under jobbkörningen. Komprimera de temporära filerna som resulterar i högre prestanda under läsning eller filer som skrivs till disk. Följande inställningar kan användas för att komprimera temporära filer.

* `pig.tmpfilecompression`: Om värdet är true, aktiverar du komprimering för temporär fil. Standardvärdet är false.

* `pig.tmpfilecompression.codec`: Komprimerings-codec för komprimering av temporära filer. Rekommenderade komprimering codec-rutiner är LZO och Snappy för lägre CPU-användning.

### <a name="enable-split-combining"></a>Aktivera delning kombinera

När aktiverad kombineras små filer för färre kartan uppgifter. Detta förbättrar effektiviteten för jobb med många små filer. Att aktivera, ange `pig.noSplitCombination` till true. Standardvärdet är false.


### <a name="tune-mappers"></a>Finjustera mappers

Antalet mappers styrs genom att ändra egenskapen `pig.maxCombinedSplitSize`. Anger storleken på data som ska bearbetas av en enda mappning-aktivitet. Standardvärdet är standardblockstorleken i filsystemet. Öka det här värdet medför en minskning av antalet mapper uppgifter.


### <a name="tune-reducers"></a>Finjustera förminskningsapparater

Antalet förminskningsapparater beräknas baserat på parametern `pig.exec.reducers.bytes.per.reducer`. Parametern anger antalet byte som bearbetas per reducer, som standard 1 GB. Om du vill begränsa det maximala antalet förminskningsapparater, ange den `pig.exec.reducers.max` egenskapen som standard 999.


## <a name="hbase-optimization-with-the-ambari-web-ui"></a>Optimering av HBase med Ambari-webbgränssnittet

HBase-konfigurationen har ändrats från den **HBase Configs** fliken. I följande avsnitt beskrivs några av de viktiga konfigurationsinställningar som påverkar prestanda för HBase.

### <a name="set-hbaseheapsize"></a>Ange HBASE_HEAPSIZE

Heap-storlek för HBase anger den maximala mängden heap som ska användas i megabyte av *region* och *master* servrar. Standardvärdet är 1 000 MB. Detta bör vara inställd för klustrets arbetsbelastning.

1. Om du vill ändra, gå till den **avancerade HBase-env** rutan i HBase **konfigurationerna** fliken och leta upp den `HBASE_HEAPSIZE` inställningen.

2. Ändra standardvärdet till 5 000 MB.

    ![HBASE_HEAPSIZE](./media/hdinsight-changing-configs-via-ambari/hbase-heapsize.png)


### <a name="optimize-read-heavy-workloads"></a>Optimera Läs tunga arbetsbelastningar

Följande konfigurationer är viktiga för att förbättra prestanda i arbetsbelastningar som Läs-aktiverat.

#### <a name="block-cache-size"></a>Blockstorlek för cache

Blockcache som är skrivskyddade cache. Dess storlek styrs av den `hfile.block.cache.size` parameter. Standardvärdet är 0,4, som är 40 procent av totalt region serverminne. Ju större cache blockstorleken, desto snabbare åtkomsttider blir.

1. Om du vill ändra den här parametern, navigera till den **inställningar** fliken i HBase **konfigurationerna** fliken och leta upp **% av RegionServer tilldelas läs buffertar**.

    ![HBase blockstorlek för cache](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)
 
2. Om du vill ändra värdet väljer den **redigera** ikon.


#### <a name="memstore-size"></a>Memstore storlek

Alla redigeringar lagras i minnesbufferten kallas en *Memstore*. Detta ökar den totala mängden data som kan skrivas till disk i en enda åtgärd och det snabbare efterföljande åtkomst till nyligen gjorda ändringar. Memstore storlek definieras av följande två parametrar:

* `hbase.regionserver.global.memstore.UpperLimit`: Definierar den maximala procentandelen region-server som kan använda för Memstore kombineras.

* `hbase.regionserver.global.memstore.LowerLimit`: Anger den minsta procentandelen av region-server som kan använda för Memstore kombineras.

Du kan minska Memstore övre och nedre gränserna för att optimera för åtkomsttider.


#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Antalet rader som hämtas när du skannar från disken

Den `hbase.client.scanner.caching` inställningen definierar hur många rader som lästs från disken när den `next` metoden anropas för en skanner.  Standardvärdet är 100. Ju högre nummer, desto färre Fjärranropen gjort från klienten till servern region, vilket resulterar i snabbare genomsökningar. Detta ökar dock också minnesbelastning på klienten.

![HBase antalet rader som hämtas](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]
> Ange inte värdet så att tiden mellan anropa next-metoden på en skanner är större än skannern orsakade timeout. Timeout-varaktighet skanner definieras av den `hbase.regionserver.lease.period` egenskapen.


### <a name="optimize-write-heavy-workloads"></a>Optimera skrivintensiv arbetsbelastning

Följande konfigurationer är viktigt att förbättra prestanda för skrivintensiv arbetsbelastning.


#### <a name="maximum-region-file-size"></a>Maximal storlek för filen

HBase lagrar data i en intern filformat, *HFile*. Egenskapen `hbase.hregion.max.filesize` definierar storleken på en enda HFile för en region.  En region är uppdelad i två områden om summan av alla HFiles i en region som är större än den här inställningen.
 
![HBase HRegion maximal filstorlek](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Ju större den region filstorleken, desto mindre antal delningar. Du kan öka filstorleken för att fastställa skrivprestanda resulterar i högsta värdet.


#### <a name="avoid-update-blocking"></a>Undvik att blockera uppdateringen

* Egenskapen `hbase.hregion.memstore.flush.size` definierar storleken som Memstore rensas till disk. Standardstorleken är 128 MB.

* Hbase region block multiplikatorn definieras av `hbase.hregion.memstore.block.multiplier`. Standardvärdet är 4. Det högsta tillåtna är 8.

* HBase blockerar uppdateringar om Memstore (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) byte.

    Med standardvärden för tömning storlek och blockera multiplikator blockeras uppdateringar när Memstore är 128 * 4 = 512 MB i storlek. Om du vill minska uppdateringen blockerar antal öka värdet på `hbase.hregion.memstore.block.multiplier`.

![HBase Region Block multiplikator](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)


### <a name="define-memstore-size"></a>Ange Memstore storlek

Memstore storlek definieras av den `hbase.regionserver.global.memstore.UpperLimit` och `hbase.regionserver.global.memstore.LowerLimit` parametrar. Om värdena är lika till varje andra minskar pausar under skriver (även orsakar oftare lokaliseraren) och resulterar i ökad skrivprestanda.


### <a name="set-memstore-local-allocation-buffer"></a>Ange Memstore lokala allokering av buffert

Memstore lokala allokering av buffert användning bestäms av egenskapen `hbase.hregion.memstore.mslab.enabled`. När aktiverat (SANT), Detta förhindrar heap fragmentering under tung skrivåtgärd. Standardvärdet är true.
 
![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)


## <a name="next-steps"></a>Nästa steg

* [Hantera HDInsight-kluster med Ambari-webbgränssnittet](hdinsight-hadoop-manage-ambari.md)
* [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
