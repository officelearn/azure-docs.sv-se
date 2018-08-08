---
title: Optimera klusterkonfigurationer med Ambari - Azure HDInsight
description: Använda Ambari-webbgränssnittet för att konfigurera och optimera HDInsight-kluster.
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: ashish
ms.openlocfilehash: b16020ec421eb077dca8318c765834e6f64f3235
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600605"
---
# <a name="use-ambari-to-optimize-hdinsight-cluster-configurations"></a>Använda Ambari och optimera klusterkonfigurationer för HDInsight

HDInsight innehåller Apache Hadoop-kluster för bearbetning av storskaliga program. Hantera, övervaka och optimera dessa komplexa kluster med flera noder kan vara svårt. [Apache Ambari](http://ambari.apache.org/) är ett webbgränssnitt för att hantera och övervaka HDInsight Linux-kluster.  Windows-kluster kan du använda Ambari [REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

En introduktion till med hjälp av Ambari-Webbgränssnittet finns i [hantera HDInsight-kluster med hjälp av Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md)

Logga in på Ambari på `https://CLUSTERNAME.azurehdidnsight.net` med dina autentiseringsuppgifter för kluster. Den första skärmen visar en översikt över instrumentpanelen.

![Ambari-instrumentpanel](./media/hdinsight-changing-configs-via-ambari/ambari-dashboard.png)

Ambari-webbgränssnittet kan användas för att hantera värdar, tjänster, aviseringar, konfigurationer och vyer. Ambari kan inte användas för att skapa ett HDInsight-kluster, uppgradera tjänster, hantera stackar och versioner, inaktivera eller recommission värdar eller lägga till tjänster i klustret.

## <a name="manage-your-clusters-configuration"></a>Hantera din klusterkonfiguration

Inställningar för att finjustera en viss tjänst. För att ändra konfigurationsinställningarna för en tjänst, Välj tjänsten från den **Services** sidopanelen (till vänster) och gå sedan till den **Peeringkonfigurationer** flik i sidan med tjänsten.

![Tjänster sidopanelen](./media/hdinsight-changing-configs-via-ambari/services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Ändra NameNode Java heap-storlek

NameNode Java heap storleken beror på många faktorer, till exempel belastningen på klustret, antal filer och antal block. Standardstorleken 1 GB fungerar bra med de flesta kluster, även om vissa arbetsbelastningar kan kräva mer eller mindre minne. 

Ändra storlek på NameNode Java-heap:

1. Välj **HDFS** på sidopanelen tjänster och navigera till den **Peeringkonfigurationer** fliken.

    ![HDFS-konfiguration](./media/hdinsight-changing-configs-via-ambari/hdfs-config.png)

1. Sök efter inställningen **NameNode Java stackstorlek**. Du kan också använda den **filter** textrutan för att ange och hitta en viss inställning. Välj den **penna** ikonen bredvid Inställningens namn.

    ![NameNode Java heap-storlek](./media/hdinsight-changing-configs-via-ambari/java-heap-size.png)

1. Skriv det nya värdet i textrutan och tryck sedan på **RETUR** att spara ändringen.

    ![Redigera NameNode Java heap-storlek](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit.png)

1. NameNode Java heap-storlek ändras från 1 GB till 2 GB.

    ![Redigera NameNode Java heap-storlek](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Spara ändringarna genom att klicka på gröna **spara** knappen överst på konfigurationsskärmen.

    ![Spara ändringar](./media/hdinsight-changing-configs-via-ambari/save-changes.png)

## <a name="hive-optimization"></a>Hive-optimering

I följande avsnitt beskrivs alternativ för att optimera prestandan för Hive.

1. Om du vill ändra Hive konfigurationsparametrar, Välj **Hive** på sidopanelen tjänster.
1. Navigera till den **Peeringkonfigurationer** fliken.

### <a name="set-the-hive-execution-engine"></a>Ange motorn för körning av Hive

Hive tillhandahåller två motorer för körningen: MapReduce och Tez. Tez är snabbare än MapReduce. HDInsight Linux-kluster har Tez som motorn för körning av standard. Så här ändrar motorn för körning:

1. I Hive **Peeringkonfigurationer** Skriv **motorn för körning av** i filterfältet.

    ![Motorn för körning](./media/hdinsight-changing-configs-via-ambari/search-execution.png)

1. Den **optimering** standardvärde är **Tez**.

    ![Optimering - Tez](./media/hdinsight-changing-configs-via-ambari/optimization-tez.png)

### <a name="tune-mappers"></a>Justera Mappningskomponenter

Hadoop försöker dela (*kartan*) en fil till flera filer och bearbeta den resulterande filer parallellt. Antalet Mappningskomponenter beror på antalet delningar. Följande konfigurationsparametrar för två driva antal delningar för Tez-Körningsmotor:

* `tez.grouping.min-size`: Nedre gräns på storleken på en grupperad delning, med ett standardvärde på 16 MB (16 777 216 byte).
* `tez.grouping.max-size`: Storleken på en grupperad delning med standardvärdet 1 GB (1 073 741 824 byte) övre gräns.

Minska båda parametrarna förbättra svarstiden genom att öka mer dataflöde som en prestanda tumregel.

Till exempel om du vill ange fyra mapper aktiviteter för datastorlekar 128 MB skulle du ange båda parametrarna till 32 MB varje (33,554,432 byte).

1. Om du vill ändra parametrarna gränsen, navigera till den **Peeringkonfigurationer** fliken Tez-tjänsten. Expandera den **Allmänt** panelen och leta upp den `tez.grouping.max-size` och `tez.grouping.min-size` parametrar.

1. Ange båda parametrarna **33,554,432** byte (32 MB).

    ![Tez gruppering storlekar](./media/hdinsight-changing-configs-via-ambari/tez-grouping-size.png)
 
De här ändringarna påverkar alla Tez-jobb på servern. Välj lämplig parametervärden för att få en optimal resultat.

### <a name="tune-reducers"></a>Justera reducerare

ORC och Snappy erbjuder hög prestanda. Men kanske Hive har för få reducerare som standard orsakar flaskhalsar.

Anta exempelvis att du har en storlek för indata på 50 GB. Att data i ORC-format med Snappy komprimering är 1 GB. Hive beräknar antalet reducerare som behövs som: (antal byte som tagits emot till Mappningskomponenter / `hive.exec.reducers.bytes.per.reducer`).

Det här exemplet är 4 reducerare med standardinställningarna.

Den `hive.exec.reducers.bytes.per.reducer` parametern anger antalet byte som bearbetas per reducer. Standardvärdet är 64 MB. Justera det här värdet ned ökar parallellitet och kan förbättra prestanda. Anpassar den för lågt kan också skapa många reducerare som potentiellt negativt påverkar prestanda. Den här parametern baserat på dina specifika krav och komprimeringsinställningar andra miljöfaktorer.

1. Om du vill ändra parametern, navigera till Hive **Peeringkonfigurationer** fliken och Sök efter den **Data per Reducer** parametern på sidan Inställningar.

    ![Data per Reducer](./media/hdinsight-changing-configs-via-ambari/data-per-reducer.png)
 
1. Välj **redigera** att ändra värdet till 128 MB (134,217,728 byte) och tryck sedan på **RETUR** att spara.

    ![Data per Reducer - redigeras](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Får en Indatastorleken på 1 024 MB, med upp till 128 MB data per reducer, det finns 8 reducerare (1024/128).

1. Ett ogiltigt värde för den **Data per Reducer** parameter kan resultera i ett stort antal reducerare som negativt påverkar prestanda för frågor. För att begränsa det maximala antalet reducerare, ange `hive.exec.reducers.max` till ett lämpligt värde. Standardvärdet är 1009.

### <a name="enable-parallel-execution"></a>Aktivera parallell körning

En Hive-frågan körs i ett eller flera steg. Om oberoende stegen kan köras parallellt, som ökar prestanda för frågor.

1.  Om du vill aktivera parallell frågekörning, navigera till Hive **Config** fliken och Sök efter den `hive.exec.parallel` egenskapen. Standardvärdet är FALSKT. Ändra värdet true och tryck sedan på **RETUR** att spara värdet.
 
1.  För att begränsa antalet jobb som ska köras parallellt, ändra den `hive.exec.parallel.thread.number` egenskapen. Standardvärdet är 8.

    ![Hive exec parallellt](./media/hdinsight-changing-configs-via-ambari/hive-exec-parallel.png)


### <a name="enable-vectorization"></a>Aktivera vectorization

Hive bearbetar data rad för rad. Vectorization dirigerar Hive att bearbeta data i block om 1 024 rader i stället för en rad i taget. Vectorization gäller endast ORC-filformat.

1. Om du vill aktivera en vectorized frågekörning, navigera till Hive **Peeringkonfigurationer** fliken och Sök efter den `hive.vectorized.execution.enabled` parametern. Standardvärdet är sant för Hive 0.13.0 eller senare.
 
1. Om du vill aktivera vectorized körning för minska sida av frågan, ange den `hive.vectorized.execution.reduce.enabled` parametern till true. Standardvärdet är FALSKT.

    ![Vectorized hive-körning](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Aktivera kostnadsbaserad optimering (CBO)

Som standard följer Hive en uppsättning regler för att hitta en optimal körning frågeplanen. Kostnadsbaserad optimering (CBO) utvärderar flera planer för att köra en fråga och tilldelar en kostnad till varje plan och sedan avgör den billigaste planen för att köra en fråga.

Om du vill aktivera CBO, navigera till Hive **Peeringkonfigurationer** fliken och Sök efter `parameter hive.cbo.enable`, växla växlingsknappen till **på**.

![CBO config](./media/hdinsight-changing-configs-via-ambari/cbo.png)

Följande ytterligare konfigurationsparametrar öka prestanda för Hive-frågor när CBO är aktiverat:

* `hive.compute.query.using.stats`

    När värdet är true, Hive använder statistik som lagras i dess metaarkiv för att besvara enkla frågor som `count(*)`.

    ![CBO stats](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Kolumnstatistik skapas när CBO är aktiverat. Hive används kolumnstatistik som lagras i metaarkiv, optimera frågor. Det tar längre tid att hämta kolumnstatistik för varje kolumn när antalet kolumner är hög. Den här inställningen inaktiverar när inställt på FALSKT, hämtar kolumnstatistik från metaarkiv.

    ![Hive-stats set kolumnstatistik](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Grundläggande partition statistik, till exempel antalet rader, datastorlek och filstorleken lagras i metaarkiv. När värdet true, partitionen stats hämtas från metaarkiv. Om värdet är false filstorleken hämtas från filsystemet och antalet rader som hämtas från rad schema.

    ![Hive-stats set partition stats](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Aktivera komprimering av mellanliggande

Kartan uppgifter skapar mellanliggande filer som används av reducer aktiviteterna. Mellanliggande komprimering minskar storleken på den mellanliggande filstorleken.

Hadoop-jobb finns vanligtvis i/o skapa en flaskhals eftersom. Genom att komprimera data kan snabbare i/o och övergripande överföring.

Tillgängliga komprimeringstyperna är:

| Format | Verktyg | Algoritmen | Filnamnstillägg | Delbara? |
| -- | -- | -- | -- | -- |
| Gzip | Gzip | SMAL | .GZ | Nej |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | Ja |
| LZO | Lzop | LZO | .lzo | Ja, om indexerade |
| Snappy | Gäller inte | Snappy | Snappy | Nej |

Som en allmän regel är viktigt att ha komprimeringsmetoden delbara, annars mycket få Mappningskomponenter kommer att skapas. Om indata är text, `bzip2` är det bästa alternativet. Snappy är det snabbaste komprimeringsalternativet för ORC-format.

1. Om du vill aktivera mellanliggande komprimering, navigera till Hive **Peeringkonfigurationer** fliken och ange sedan den `hive.exec.compress.intermediate` parametern till true. Standardvärdet är FALSKT.

    ![Hive exec compress mellanliggande](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]
    > Om du vill komprimera mellanliggande filer, väljer du en komprimerings-codec med lägre processor kostnaderna, även om den codec har en hög komprimering utdata.

1. Ange mellanliggande komprimerings-codec genom att lägga till den anpassade egenskapen `mapred.map.output.compression.codec` till den `hive-site.xml` eller `mapred-site.xml` fil.

1. Lägga till en anpassad inställning:

    a. Gå till Hive **Peeringkonfigurationer** fliken och markera den **Avancerat** fliken.

    b. Under den **Avancerat** fliken, hitta och expandera den **anpassade hive-plats** fönstret.

    c. Klicka på länken **Lägg till egenskap** längst ned i fönstret för anpassade hive-plats.

    d. I fönstret Lägg till egenskap anger `mapred.map.output.compression.codec` som nyckel och `org.apache.hadoop.io.compress.SnappyCodec` som värde.

    e. Klicka på **Lägg till**.

    ![Hive anpassad egenskap](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Detta kommer komprimera filen mellanliggande Snappy komprimering. När egenskapen har lagts till, visas det i rutan anpassad hive-plats.

    > [!NOTE]
    > Den här proceduren ändrar den `$HADOOP_HOME/conf/hive-site.xml` filen.

### <a name="compress-final-output"></a>Komprimera slutgiltiga utdata

Den slutgiltiga utdata för Hive kan också komprimeras.

1. Om du vill komprimera den slutgiltiga utdata för Hive, navigera till Hive **Peeringkonfigurationer** fliken och ange sedan den `hive.exec.compress.output` parametern till true. Standardvärdet är FALSKT.

1. Välj komprimerings-codec utdata genom att lägga till den `mapred.output.compression.codec` egenskapen till anpassade hive-fönstret, enligt beskrivningen i föregående avsnitt steg 3.

    ![Hive anpassad egenskap](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Aktivera spekulativ körning

Spekulativ körning startar ett visst antal duplicerade åtgärder för att identifiera och svartlista långsamma uppgift spåraren samtidigt förbättra den övergripande jobbkörningen genom att optimera enskilda resultat.

Spekulativ körning bör t vara aktiverad för långvariga MapReduce-aktiviteter med stora mängder indata.

* Om du vill aktivera spekulativ körning, navigera till Hive **Peeringkonfigurationer** fliken och ange sedan den `hive.mapred.reduce.tasks.speculative.execution` parametern till true. Standardvärdet är FALSKT.

    ![Hive mapred minska uppgifter spekulativ körning](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Finjustera dynamiska partitioner

Hive kan skapa dynamiska partitioner vid infogning poster i en tabell, utan att fördefiniera varje partition. Det är en kraftfull funktion, men det kan resultera i att skapa ett stort antal partitioner och ett stort antal filer för varje partition.

1. För Hive att göra dynamiska partitioner, den `hive.exec.dynamic.partition` parametervärdet måste vara sant (standard).

1. Ändra partitionsläge för dynamisk att *strikt*. Minst en partition måste vara statiska i strikt läge. Detta förhindrar frågor utan att behöva partition filtret i WHERE-satsen, det vill säga *strikt* förhindrar frågor som genomsöka alla partitioner. Gå till Hive **Peeringkonfigurationer** fliken och sedan ange `hive.exec.dynamic.partition.mode` till **strikt**. Standardvärdet är **nonstrict**.
 
1. För att begränsa antalet dynamiska partitioner som ska skapas, ändra den `hive.exec.max.dynamic.partitions` parametern. Standardvärdet är 5 000.
 
1. För att begränsa det totala antalet dynamiska partitioner per nod, ändra `hive.exec.max.dynamic.partitions.pernode`. Standardvärdet är 2000.

### <a name="enable-local-mode"></a>Aktivera lokalt läge

Lokalt läge kan Hive att utföra alla uppgifter i ett jobb på en enskild dator, eller ibland i en enda process. Detta förbättrar prestanda för frågor om indata är liten och arbetet med att starta aktiviteter för frågor som förbrukar en stor del av den övergripande Frågekörningen.

Om du vill aktivera lokalt läge, lägger du till den `hive.exec.mode.local.auto` parametern till anpassade hive-plats panelen, enligt beskrivningen i steg 3 i den [aktivera komprimering av mellanliggande](#enable-intermediate-compression) avsnittet.

![Hive exec läge lokala automatiskt](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Ställ in enkel MapReduce MultiGROUP av

När den här egenskapen har angetts till true, en MultiGROUP genom att fråga med vanliga Gruppera efter-nycklar genereras ett enskilt MapReduce-jobb.  

Aktivera det här beteendet genom att lägga till den `hive.multigroupby.singlereducer` parametern till anpassade hive-plats rutan, enligt beskrivningen i steg 3 i den [aktivera komprimering av mellanliggande](#enable-intermediate-compression) avsnittet.

![Hive som enda MapReduce MultiGROUP](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Ytterligare optimeringar för Hive

I följande avsnitt beskrivs ytterligare Hive-relaterade optimeringar som du kan ange.

#### <a name="join-optimizations"></a>Ansluta till optimeringar

Standard för kopplingstyp i Hive är en *shuffle join*. I Hive, särskilt Mappningskomponenter läsa indata och genererar en nyckel/värde-par för koppling till en mellanliggande-fil. Hadoop sorterar och slår ihop dessa par i en blandad-steget. Det här skedet shuffle är dyr. Att välja rätt kopplingen utifrån dina data kan du förbättra prestanda avsevärt.

| Anslutningstyp | När | Så här | Hive-inställningar | Kommentarer |
| -- | -- | -- | -- | -- |
| Shuffle koppling | <ul><li>Standardalternativet</li><li>Alltid fungerar</li></ul> | <ul><li>Läser från en del av någon av tabellerna</li><li>Buckets och sorterar på Join-nyckel</li><li>Skickar en bucket till varje minska</li><li>Anslut till görs på minska sida</li></ul> | Ingen betydande Hive inställning krävs | Fungerar varje gång |
| Mappa Join | <ul><li>En tabell får plats i minnet</li></ul> | <ul><li>Läser in liten tabell i minnet hash-tabell</li><li>Strömmar via en del av stora filer</li><li>Ansluter till varje post från hash-tabell</li><li>Kopplingar är genom att enbart mappningen</li></ul> | `hive.auto.confvert.join=true` | Mycket snabba men begränsade |
| Sortera Merge Bucket | Om båda tabellerna är: <ul><li>Sorterade samma</li><li>Bucketas samma</li><li>Koppla på kolumnen sorterade/bucketas</li></ul> | Varje process: <ul><li>Läser en bucket från varje tabell</li><li>Bearbetar raden med det lägsta värdet</li></ul> | `hive.auto.convert.sortmerge.join=true` | Ett mycket effektivt |

#### <a name="execution-engine-optimizations"></a>Körningen motorn optimeringar

Ytterligare rekommendationer för att optimera motorn för körning av Hive:

| Inställning | Rekommenderas | HDInsight standard |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | SANT = säkrare, långsammare; = FALSE snabbare | false |
| `tez.am.resource.memory.mb` | 4 GB övre gränsen för de flesta | Automatiskt justerade |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="pig-optimization"></a>Pig-optimering

Pig egenskaper kan ändras från Ambari-webbgränssnittet att justera Pig frågor. Ändra egenskaper för Pig från Ambari direkt ändrar Pig-egenskaper i den `/etc/pig/2.4.2.0-258.0/pig.properties` filen.

1. Om du vill ändra egenskaper för Pig, navigera till Pig **Peeringkonfigurationer** fliken och expandera sedan den **avancerade pig-egenskaper** fönstret.

1. Hitta, ta bort och ändra värdet på egenskapen som du vill ändra.

1. Välj **spara** på upp till höger i fönstret för att spara det nya värdet. Vissa egenskaper kan kräva en omstart av tjänsten.

    ![Avancerade pig-egenskaper](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)
 
> [!NOTE]
> Sessionsnivå inställningar åsidosätter egenskapsvärden i den `pig.properties` filen.

### <a name="tune-execution-engine"></a>Finjustera motorn för körning

Två motorer för körningen är tillgängliga att köra Pig-skript: MapReduce och Tez. Tez är en motor för optimerad och är mycket snabbare än MapReduce.

1. Ändra motorn för körning i den **avancerade pig-egenskaper** fönstret hitta egenskapen `exectype`.

1. Standardvärdet är **MapReduce**. Ändra det till **Tez**.


### <a name="enable-local-mode"></a>Aktivera lokalt läge

Ett liknande sätt som Hive, lokalt läge används för att påskynda jobb med relativt mindre mängder data.

1. Om du vill aktivera lokalt läge, ange `pig.auto.local.enabled` till **SANT**. Standardvärdet är FALSKT.

1. Jobb med en storlek för indata understiger `pig.auto.local.input.maxbytes` egenskapsvärdet anses vara små jobb. Standardvärdet är 1 GB.


### <a name="copy-user-jar-cache"></a>Kopiera jar användarcachen

Pig kopierar JAR-filerna som krävs av UDF: er till en distribuerad cache för att göra dem tillgängliga för uppgiften noder. De här JAR-filer ändras inte ofta. Om aktiverad kan den `pig.user.cache.enabled` inställningen kan en JAR-filer ska placeras i ett cacheminne kan återanvända dem för jobb som körs av samma användare. Detta resulterar i en mindre prestandaökning jobbet.

1. Aktivera, ange `pig.user.cache.enabled` till true. Standardvärdet är FALSKT.

1. Att ställa in grundläggande sökvägen till de cachelagrade JAR-filer `pig.user.cache.location` till rotsökvägen. Standardvärdet är `/tmp`.


### <a name="optimize-performance-with-memory-settings"></a>Optimera prestanda med minnesinställningarna

Följande minnesinställningar för kan hjälpa dig att optimera prestanda för Pig-skript.

* `pig.cachedbag.memusage`: Mängden minne som allokeras till en egenskapsuppsättning. En egenskapsuppsättning är en samling av tupplar. En tuppel är en ordnad uppsättning fält och ett fält är en typ av data. Om data i en egenskapsuppsättning ligger utanför det allokerade minnet, är det hamnat på disk. Standardvärdet är 0,2 som representerar 20 procent av det tillgängliga minnet. Det här minnet delas mellan alla påsar i ett program.

* `pig.spill.size.threshold`: Påsar större än tröskelvärdet oljesanering storlek (i byte) har hamnat på disk. Standardvärdet är 5 MB.


### <a name="compress-temporary-files"></a>Komprimera temporära filer

Pig genererar temporära filer under jobbkörningen. Komprimering av temporära filer resulterar i en prestandaökning när läsa och skriva filer till disken. Följande inställningar kan användas för att komprimera temporära filer.

* `pig.tmpfilecompression`: Om värdet är true, kan du tillfälligt filkomprimering. Standardvärdet är FALSKT.

* `pig.tmpfilecompression.codec`: Komprimerings-codec för komprimering av temporära filer. Rekommenderade komprimering codec är LZO och Snappy för lägre CPU-belastning.

### <a name="enable-split-combining"></a>Aktivera delning kombinera

När det är aktiverat, kombinerat små filer för färre kartan aktiviteter. Detta förbättrar effektiviteten för jobb med många små filer. Aktivera, ange `pig.noSplitCombination` till true. Standardvärdet är FALSKT.


### <a name="tune-mappers"></a>Justera Mappningskomponenter

Antalet Mappningskomponenter styrs genom att ändra egenskapen `pig.maxCombinedSplitSize`. Detta anger storleken på data som ska bearbetas av en enda kartan-aktivitet. Standardvärdet är den filsystem Standardblockstorlek. Ökar det här värdet resulterar i en minskning av antalet mapper uppgifter.


### <a name="tune-reducers"></a>Justera reducerare

Antalet reducerare beräknas baserat på parametern `pig.exec.reducers.bytes.per.reducer`. Parametern anger antalet byte som bearbetas per reducer, som standard 1 GB. För att begränsa det maximala antalet reducerare, ange den `pig.exec.reducers.max` egenskapen, som standard 999.


## <a name="hbase-optimization-with-the-ambari-web-ui"></a>Optimering av HBase med Ambari-webbgränssnittet

HBase-konfigurationen har ändrats från den **HBase Configs** fliken. I följande avsnitt beskrivs några av de viktiga konfigurationsinställningar som påverkar prestanda för HBase.

### <a name="set-hbaseheapsize"></a>Ange HBASE_HEAPSIZE

HBase-stackstorlek anger den maximala mängden heap som ska användas i megabyte av *region* och *master* servrar. Standardvärdet är 1 000 MB. Detta bör vara finjusterad klustrets arbetsbelastning.

1. Om du vill ändra, gå till den **avancerade HBase-env** fönstret i HBase **Peeringkonfigurationer** fliken och hitta den `HBASE_HEAPSIZE` inställningen.

1. Ändra standardvärdet till 5 000 MB.

    ![HBASE_HEAPSIZE](./media/hdinsight-changing-configs-via-ambari/hbase-heapsize.png)


### <a name="optimize-read-heavy-workloads"></a>Optimera Läs tunga arbetsbelastningar

Följande konfigurationer är viktiga för att förbättra prestanda för arbetsbelastningar för Läs-aktiverat.

#### <a name="block-cache-size"></a>Blockstorlek för cache

Blockera cache är läsningscachen. Storleken styrs av den `hfile.block.cache.size` parametern. Standardvärdet är 0.4, vilket är 40 procent av total region serverminne. Ju större cache blockstorleken, desto snabbare icke-sekventiell läsning kommer att.

1. Om du vill ändra den här parametern, navigera till den **inställningar** fliken i HBase **Peeringkonfigurationer** fliken och leta sedan upp **% av RegionServer tilldelas läs buffertar**.

    ![HBase blockstorlek för cache](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)
 
1. Om du vill ändra värdet, Välj den **redigera** ikon.


#### <a name="memstore-size"></a>Memstores storlek

Alla ändringar lagras i minnesbufferten kallas en *Memstores*. Detta ökar den totala mängden data som kan skrivas till disk i en enda åtgärd och det ökar hastigheten för efterföljande åtkomst till de senaste ändringarna. Memstores storlek definieras av följande två parametrar:

* `hbase.regionserver.global.memstore.UpperLimit`: Definierar hur stor procentandel av den regionsserver som Memstores kombineras kan använda.

* `hbase.regionserver.global.memstore.LowerLimit`: Definierar den lägsta procentandelen av den regionsserver som Memstores kombineras kan använda.

Du kan minska Memstores övre och nedre gränserna för att optimera för icke-sekventiell läsning.


#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Antalet rader som hämtas vid sökning från disken

Den `hbase.client.scanner.caching` inställningen anger hur många rader som läses från disk när den `next` metoden anropas på en skanner.  Standardvärdet är 100. Ju högre nummer, desto färre Fjärranropen görs från klienten till servern region, vilket ger snabbare sökningar. Detta ökar dock också minnesbelastning på klienten.

![HBase antalet rader som hämtas](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]
> Ange inte värdet så att tiden mellan anrop av next-metoden på en skanner som är större än tidsgränsen för skannern. Tidslängden skannern definieras av den `hbase.regionserver.lease.period` egenskapen.


### <a name="optimize-write-heavy-workloads"></a>Optimera skrivintensiv arbetsbelastningar

Följande konfigurationer är viktiga för att förbättra prestanda för arbetsbelastningar för skrivning.


#### <a name="maximum-region-file-size"></a>Maximal storlek för fil

HBase lagrar data i en intern filformat, *HFile*. Egenskapen `hbase.hregion.max.filesize` definierar storleken på en enda HFile för en region.  En region är uppdelat i två regioner om summan av alla HFiles i en region är större än den här inställningen.
 
![HBase HRegion max filstorlek](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Ju större region filstorlek, desto mindre antal delningar. Du kan öka filstorleken för att fastställa skrivprestanda leder till högsta värdet.


#### <a name="avoid-update-blocking"></a>Undvik att blockera uppdateringen

* Egenskapen `hbase.hregion.memstore.flush.size` definierar storleken då Memstores har rensats till disk. Standardstorleken är 128 MB.

* Hbase region block multiplikatorn definieras av `hbase.hregion.memstore.block.multiplier`. Standardvärdet är 4. Det maximalt tillåtna är 8.

* HBase blockerar uppdateringar om Memstores (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) byte.

    Med standardvärden för tömning storlek och blockera multiplikatorn blockeras uppdateringar Memstores 128 * 4 = 512 MB i storlek. För att minska den uppdatering som blockerar antal kan öka värdet på `hbase.hregion.memstore.block.multiplier`.

![HBase Region Block multiplikatorn](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)


### <a name="define-memstore-size"></a>Ange Memstores storlek

Memstores storlek definieras av den `hbase.regionserver.global.memstore.UpperLimit` och `hbase.regionserver.global.memstore.LowerLimit` parametrar. Om dessa värden med samma till varje andra minskar pauser under skriver (även orsakar oftare tömdes) och resulterar i ökad skrivprestanda.


### <a name="set-memstore-local-allocation-buffer"></a>Ange Memstores lokala allokering bufferten

Memstores lokala allokering bufferten användning bestäms av egenskapen `hbase.hregion.memstore.mslab.enabled`. När aktiverat (SANT), det förhindrar att heap fragmentering under tung skrivåtgärd. Standardvärdet är sant.
 
![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)


## <a name="next-steps"></a>Nästa steg

* [Hantera HDInsight-kluster med Ambari-webbgränssnittet](hdinsight-hadoop-manage-ambari.md)
* [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
