---
title: Apache Ambari för optimering av klusterkonfigurationer – Azure HDInsight
description: Använd webbgränssnittet Apache Ambari för att konfigurera och optimera Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: 15a2c75a7619a815655be0fd9fd3044d86acd057
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79272570"
---
# <a name="use-apache-ambari-to-optimize-hdinsight-cluster-configurations"></a>Använd Apache Ambari för att optimera HDInsight-klusterkonfigurationer

HDInsight tillhandahåller [Apache Hadoop](https://hadoop.apache.org/) kluster för storskaliga data bearbetnings program. Det kan vara svårt att hantera, övervaka och optimera dessa komplexa kluster med flera noder. [Apache Ambari](https://ambari.apache.org/) är ett webb gränssnitt för att hantera och övervaka HDInsight Linux-kluster.  För Windows-kluster använder du [Ambari-REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

En introduktion till att använda Ambari-webbgränssnittet finns i [Hantera HDInsight-kluster med hjälp av Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md)

Logga in på Ambari på `https://CLUSTERNAME.azurehdidnsight.net` med dina autentiseringsuppgifter för klustret. Den första skärmen visar en översikts instrument panel.

![Användarens instrument panel för Apache Ambari visas](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

Ambari-webbgränssnittet kan användas för att hantera värdar, tjänster, aviseringar, konfigurationer och vyer. Ambari kan inte användas för att skapa ett HDInsight-kluster, uppgradera tjänster, hantera stackar och versioner, inaktivera eller reprovision-värdar eller lägga till tjänster i klustret.

## <a name="manage-your-clusters-configuration"></a>Hantera klustrets konfiguration

Konfigurations inställningar hjälper dig att finjustera en viss tjänst. Om du vill ändra konfigurations inställningar för en tjänst väljer du tjänsten på sid panelen **tjänster** (till vänster) och navigera sedan till fliken **konfigurationer** på sidan tjänst information.

![Apache Ambari Services-sidofält](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Ändra NameNode Java-Heap-storlek

NameNode Java-heap-storleken beror på många faktorer, till exempel belastningen på klustret, antalet filer och antalet block. Standard storleken på 1 GB fungerar bra med de flesta kluster, men vissa arbets belastningar kan kräva mer eller mindre minne.

Ändra NameNode Java-Heap-storlek:

1. Välj **HDFS** från panelen tjänster och navigera till fliken **konfigurationer** .

    ![Apache Ambari HDFS-konfiguration](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. Hitta inställningen **NameNode Java-Heap-storlek**. Du kan också använda text rutan **filter** för att skriva och söka efter en viss inställning. Välj **Penn** ikonen bredvid inställnings namnet.

    ![Apache Ambari NameNode Java-Heap-storlek](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Skriv det nya värdet i text rutan och tryck sedan på **RETUR** för att spara ändringen.

    ![Ambari Edit NameNode Java heap size1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. Storleken på NameNode Java-heap ändras till 1 GB från 2 GB.

    ![Redigerad NameNode Java-heap SIZE2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Spara ändringarna genom att klicka på den gröna knappen **Spara** överst på konfigurations skärmen.

    ![Ambari Ambari Spara konfigurationer](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="apache-hive-optimization"></a>Apache Hive optimering

I följande avsnitt beskrivs konfigurations alternativ för att optimera övergripande Apache Hive prestanda.

1. Om du vill ändra konfigurations parametrar för Hive väljer du **Hive** från tjänsternas sid List.
1. Navigera till fliken **konfigurationer** .

### <a name="set-the-hive-execution-engine"></a>Ange körnings motor för Hive

Hive innehåller två körnings motorer: [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) och [Apache TEZ](https://tez.apache.org/). Tez är snabbare än MapReduce. HDInsight Linux-kluster har Tez som standard körnings motor. Ändra körnings motorn:

1. Skriv **körnings motor** i rutan filter på fliken Hive- **konfigurationer** .

    ![Ambari för att söka körnings motor för Apache](./media/hdinsight-changing-configs-via-ambari/ambari-search-execution.png)

1. Standardvärdet för egenskapen **Optimization** är **Tez**.

    ![Optimering – Apache Tez-motor](./media/hdinsight-changing-configs-via-ambari/optimization-apache-tez.png)

### <a name="tune-mappers"></a>Finjustera mappningar

Hadoop försöker dela (*Mappa*) en enskild fil i flera filer och bearbeta de resulterande filerna parallellt. Antalet mappningar beror på antalet delningar. Följande två konfigurations parametrar driver antalet delningar för Tez körnings motor:

* `tez.grouping.min-size`: Sänk gränsen för en grupperad delnings storlek med standardvärdet 16 MB (16 777 216 byte).
* `tez.grouping.max-size`: den övre gränsen för en grupperad delnings storlek med standardvärdet 1 GB (1 073 741 824 byte).

Som prestanda regel kan du minska båda parametrarna för att förbättra svars tiden, öka för mer data flöde.

Om du till exempel vill ange fyra Mapper-uppgifter för en data storlek på 128 MB anger du båda parametrarna till 32 MB vardera (33 554 432 byte).

1. Om du vill ändra gräns parametrarna navigerar du till fliken **configs** i Tez-tjänsten. Expandera panelen **Allmänt** och leta upp `tez.grouping.max-size`-och `tez.grouping.min-size`-parametrarna.

1. Ange båda parametrarna till **33 554 432** byte (32 MB).

    ![Apache Ambari Tez-grupperingsinställningar](./media/hdinsight-changing-configs-via-ambari/apache-tez-grouping-size.png)

Dessa ändringar påverkar alla Tez-jobb på servern. Om du vill få ett optimalt resultat väljer du lämpliga parameter värden.

### <a name="tune-reducers"></a>Finjustera dämpare

[Apache Orc](https://orc.apache.org/) och [fästfunktionen](https://google.github.io/snappy/) ger både höga prestanda. Hive kan dock ha för få Minskare som standard, vilket orsakar Flask halsar.

Anta till exempel att du har en indata-storlek på 50 GB. Dessa data i ORC-format med Fästnings komprimering är 1 GB. Hive beräknar antalet reducerare som behövs som: (antal byte indata till Mapper/`hive.exec.reducers.bytes.per.reducer`).

Med standardinställningarna är det här exemplet 4-dämpare.

Parametern `hive.exec.reducers.bytes.per.reducer` anger antalet byte som bearbetas per minskning. Standardvärdet är 64 MB. Att justera det här värdet nedåt ökar parallellt och kan förbättra prestandan. Om du finjusterar för lågt kan du också skapa för många reducerare, vilket kan påverka prestanda negativt. Den här parametern baseras på dina specifika data krav, komprimerings inställningar och andra miljö faktorer.

1. Om du vill ändra parametern navigerar du till fliken Hive- **konfiguration** och letar upp **data per minsknings** parameter på sidan Inställningar.

    ![Apache Ambari-data per minskning](./media/hdinsight-changing-configs-via-ambari/ambari-data-per-reducer.png)

1. Välj **Redigera** för att ändra värdet till 128 MB (134 217 728 byte) och tryck sedan på **RETUR** för att spara.

    ![Ambari data per minsknings-redige rad](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Med en indata-storlek på 1 024 MB, med 128 MB data per minskning, finns det 8 Minskare (1024/128).

1. Ett felaktigt värde för **data per minsknings** parameter kan resultera i ett stort antal avreducerare, vilket negativt påverkar frågans prestanda. Om du vill begränsa det maximala antalet avgränsare anger `hive.exec.reducers.max` till ett lämpligt värde. Standardvärdet är 1009.

### <a name="enable-parallel-execution"></a>Aktivera parallell körning

En Hive-fråga körs i en eller flera steg. Om de oberoende faserna kan köras parallellt ökar frågans prestanda.

1. Om du vill aktivera parallell frågekörningen går du till fliken Hive- **konfiguration** och söker efter egenskapen `hive.exec.parallel`. Standardvärdet är false. Ändra värdet till sant och spara värdet genom att trycka på **RETUR** .

1. Om du vill begränsa antalet jobb som ska köras parallellt ändrar du egenskapen `hive.exec.parallel.thread.number`. Standardvärdet är 8.

    ![Parallell visning för Apache Hive exec](./media/hdinsight-changing-configs-via-ambari/apache-hive-exec-parallel.png)

### <a name="enable-vectorization"></a>Aktivera vectorization

Hive bearbetar data rad för rad. Vectorization dirigerar Hive för att bearbeta data i block om 1 024 rader i stället för en rad i taget. Vectorization kan bara användas för fil formatet ORC.

1. Om du vill aktivera en Vector-frågekörningen går du till fliken Hive- **konfiguration** och söker efter parametern `hive.vectorized.execution.enabled`. Standardvärdet är true för Hive 0.13.0 eller senare.

1. Om du vill aktivera vektorbaserad körning för frågans minsknings sida ställer du in parametern `hive.vectorized.execution.reduce.enabled` på True. Standardvärdet är false.

    ![Apache Hive vektorbaserad körning](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Aktivera kostnads baserad optimering (CBO)

Som standard följer Hive en uppsättning regler för att hitta en optimal plan för frågekörningen. Kostnads baserad optimering (CBO) utvärderar flera planer på att köra en fråga och tilldelar en kostnad till varje plan och bestämmer sedan billigaste-planen för att köra en fråga.

Om du vill aktivera CBO navigerar du till **Hive** - > **config** > **Inställningar** och letar reda på **Aktivera kostnads baserad optimering**och byter sedan växlings knappen till **på**.

![Kostnads baserad, HDInsight-baserad optimering](./media/hdinsight-changing-configs-via-ambari/hdinsight-cbo-config.png)

Följande ytterligare konfigurations parametrar ökar prestanda för Hive-frågor när CBO är aktiverat:

* `hive.compute.query.using.stats`

    När värdet är true använder Hive statistik som lagrats i dess metaarkiv för att besvara enkla frågor som `count(*)`.

    ![Apache Hive beräknings fråga med statistik](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Kolumn statistik skapas när CBO är aktive rad. Hive använder kolumn statistik, som lagras i metaarkiv, för att optimera frågor. Att hämta kolumn statistik för varje kolumn tar längre tid när antalet kolumner är högt. Om värdet är false inaktiverar den här inställningen hämtning av kolumn statistik från metaarkiv.

    ![Kolumn statistik för Apache Hive stats uppsättning](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Grundläggande standardpartitions statistik, till exempel antal rader, data storlek och fil storlek, lagras i metaarkiv. När värdet är true hämtas partitionsstrukturen från metaarkiv. Om värdet är false hämtas fil storleken från fil systemet och antalet rader hämtas från radens schema.

    ![Registrerings statistik för Hive-uppsättning](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Aktivera mellanliggande komprimering

Kart aktiviteter skapar mellanliggande filer som används av minsknings aktiviteterna. Mellanliggande komprimering minskar storleken på mellanliggande fil.

Hadoop-jobb är vanligt vis I/O-Flask halsar. Komprimering av data kan påskynda I/O och övergripande nätverks överföring.

De tillgängliga komprimerings typerna är:

| Format | Verktyg | Algoritm | Fil namns tillägg | Delbara? |
| -- | -- | -- | -- | -- |
| Gzip | Gzip | DEFLATE | .gz | Nej |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | Ja |
| LZO | Lzop | LZO | .lzo | Ja, om det är indexerat |
| Snappy | Ej tillämpligt | Snappy | Snappy | Nej |

Som en allmän regel är det viktigt att det går att dela upp komprimerings metoden, annars skapas mycket få mappningar. Om indata är text är `bzip2` det bästa alternativet. För ORC-format är fästfunktionen det snabbaste komprimerings alternativet.

1. Om du vill aktivera mellanliggande komprimering navigerar du till fliken Hive- **konfigurationer** och anger sedan parametern `hive.exec.compress.intermediate` till true. Standardvärdet är false.

    ![Hive exec komprimera mellanliggande](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Om du vill komprimera mellanliggande filer väljer du en komprimerings-codec med lägre CPU-kostnad, även om codecen inte har en hög komprimerings effekt.

1. Om du vill ange den mellanliggande komprimerings-codecen lägger du till den anpassade egenskapen `mapred.map.output.compression.codec` i `hive-site.xml` eller `mapred-site.xml` filen.

1. Så här lägger du till en anpassad inställning:

    a. Navigera till **Hive** - > **config** > **Avancerad** > **anpassad Hive-plats**.

    b. Välj **Lägg till egenskap...** längst ned i fönstret för den anpassade Hive-platsen.

    c. I fönstret Lägg till egenskap anger du `mapred.map.output.compression.codec` som nyckel och `org.apache.hadoop.io.compress.SnappyCodec` som värde.

    d. Välj **Lägg till**.

    ![Apache Hive anpassad egenskap Lägg till](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Den mellanliggande filen komprimeras med hjälp av Fästnings komprimering. När egenskapen har lagts till visas den i fönstret anpassad Hive-webbplats.

    > [!NOTE]  
    > Den här proceduren ändrar `$HADOOP_HOME/conf/hive-site.xml`-filen.

### <a name="compress-final-output"></a>Komprimera slutgiltiga utdata

Den slutliga Hive-utdata kan också komprimeras.

1. Om du vill komprimera den slutliga Hive-utdata navigerar du till fliken Hive- **konfiguration** och anger sedan parametern `hive.exec.compress.output` till sant. Standardvärdet är false.

1. Om du vill välja komprimerings-codecen för utdata lägger du till egenskapen `mapred.output.compression.codec` anpassad i fönstret anpassad Hive-plats, enligt beskrivningen i föregående avsnitt: steg 3.

    ![Apache Hive anpassad egenskap ADD2](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Aktivera spekulativ körning

Spekulativ körning startar ett visst antal duplicerade åtgärder för att identifiera och blockeringslista den långsamma uppgiftsspåraren och samtidigt förbättra den övergripande jobbkörningen genom att optimera enskilda resultat.

Spekulativ körning bör inte aktive ras för långvariga MapReduce-uppgifter med stora mängder ingångar.

* Om du vill aktivera spekulativ körning navigerar du till fliken Hive- **konfiguration** och anger sedan parametern `hive.mapred.reduce.tasks.speculative.execution` till true. Standardvärdet är false.

    ![Hive-mapred minskar spekulativ körning](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Finjustera dynamiska partitioner

Hive gör det möjligt att skapa dynamiska partitioner när poster infogas i en tabell, utan att fördefiniera varje partition och varje partition. Detta är en kraftfull funktion, men det kan leda till att ett stort antal partitioner skapas och ett stort antal filer för varje partition.

1. För att Hive ska utföra dynamiska partitioner ska värdet för parametern `hive.exec.dynamic.partition` vara sant (standardvärdet).

1. Ändra läget för dynamisk partition till *strict*. I strikt läge måste minst en partition vara statisk. Detta förhindrar frågor utan partitions filtret i WHERE-satsen, det vill säga *strikt* förhindrar frågor som genomsöker alla partitioner. Gå till fliken Hive- **konfiguration** och ange `hive.exec.dynamic.partition.mode` till **strikt**. Standardvärdet är icke- **strikt**.

1. Ändra `hive.exec.max.dynamic.partitions` parametern om du vill begränsa antalet dynamiska partitioner som ska skapas. Standardvärdet är 5000.

1. Om du vill begränsa det totala antalet dynamiska partitioner per nod ändrar du `hive.exec.max.dynamic.partitions.pernode`. Standardvärdet är 2000.

### <a name="enable-local-mode"></a>Aktivera lokalt läge

Lokalt läge gör det möjligt för Hive att utföra alla uppgifter för ett jobb på en enskild dator eller ibland i en enda process. Detta förbättrar prestandan för frågor om indata är små och omkostnaderna för att starta aktiviteter för frågor förbrukar en betydande del av den övergripande körningen av frågan.

Om du vill aktivera lokalt läge lägger du till `hive.exec.mode.local.auto`-parametern i den anpassade Hive-plats panelen, enligt beskrivningen i steg 3 i avsnittet [Aktivera mellanliggande komprimering](#enable-intermediate-compression) .

![Apache Hive exec-läge lokalt Auto](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Ange Single MapReduce GROUP BY

När den här egenskapen har angetts till True genererar en multigroup BY-fråga med vanliga Group-by-nycklar ett enda MapReduce-jobb.  

Om du vill aktivera det här beteendet lägger du till parametern `hive.multigroupby.singlereducer` i fönstret anpassad Hive-plats, enligt beskrivningen i steg 3 i avsnittet [Aktivera mellanliggande komprimering](#enable-intermediate-compression) .

![Hive-uppsättning enkel MapReduce multigroup BY](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Ytterligare Hive-optimeringar

I följande avsnitt beskrivs ytterligare Hive-relaterade optimeringar som du kan ange.

#### <a name="join-optimizations"></a>Delta i optimeringar

Standard kopplings typen i Hive är en *blandad koppling*. I Hive läser särskilda mappningar indata och genererar ett kopplings nyckel/värde-par till en mellanliggande fil. Hadoop sorterar och sammanfogar dessa par i en blandad fas. Den här blandade fasen är dyr. Om du väljer höger koppling baserat på dina data kan du förbättra prestanda avsevärt.

| Kopplings typ | När | vilken | Hive-inställningar | Kommentarer |
| -- | -- | -- | -- | -- |
| Blanda koppling | <ul><li>Standard alternativ</li><li>Fungerar alltid</li></ul> | <ul><li>Läser från en del av en av tabellerna</li><li>Buckets och sorteras efter kopplings nyckel</li><li>Skickar en Bucket till varje minskning</li><li>Kopplingen görs på den minimerade Sidan</li></ul> | Ingen väsentlig registrerings inställning krävs | Fungerar varje gång |
| Kart koppling | <ul><li>En tabell får plats i minnet</li></ul> | <ul><li>Läser liten tabell i hash-tabellen för minnet</li><li>Strömmar genom en del av den stora filen</li><li>Kopplar varje post från hash-tabellen</li><li>Kopplingar är endast av mapper</li></ul> | `hive.auto.confvert.join=true` | Mycket snabb, men begränsad |
| Sortera sammanslagnings Bucket | Om båda tabellerna är: <ul><li>Sorterade samma</li><li>Bucket samma</li><li>Ansluter till den sorterade/Bucket kolumnen</li></ul> | Varje process: <ul><li>Läser en Bucket från varje tabell</li><li>Bearbetar raden med det lägsta värdet</li></ul> | `hive.auto.convert.sortmerge.join=true` | Mycket effektivt |

#### <a name="execution-engine-optimizations"></a>Optimeringar för körnings motor

Ytterligare rekommendationer för att optimera Hive-körnings motorn:

| Inställning | Rekommenderas | HDInsight-standard |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | Sant = säkrare, långsammare; falskt = snabbare | false |
| `tez.am.resource.memory.mb` | 4 GB övre gränser för de flesta | Automatiskt justerad |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="apache-pig-optimization"></a>Apache gris-optimering

[Apache gris](https://pig.apache.org/) -egenskaper kan ändras från Ambari-webbgränssnittet för att justera gris-frågor. Om du ändrar gris-egenskaperna från Ambari ändras egenskaperna för gris i `/etc/pig/2.4.2.0-258.0/pig.properties`-filen direkt.

1. Om du vill ändra egenskaper för gris navigerar du till fliken **konfigurations** inställningar för svin och expanderar sedan fönstret **Avancerat gris-Properties** .

1. Hitta, ta bort kommentar och ändra värdet för den egenskap som du vill ändra.

1. Välj **Spara** längst upp till höger i fönstret för att spara det nya värdet. Vissa egenskaper kan kräva att tjänsten startas om.

    ![Avancerade egenskaper för Apache-gris](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)

> [!NOTE]  
> Alla inställningar för sessionsbaserade åsidosätter egenskaps värden i `pig.properties`s filen.

### <a name="tune-execution-engine"></a>Justera körnings motor

Det finns två körnings motorer tillgängliga för att köra gris-skript: MapReduce och Tez. Tez är en optimerad motor och är mycket snabbare än MapReduce.

1. Om du vill ändra körnings motorn går du till fönstret **Avancerat gris-Properties** och letar upp egenskapen `exectype`.

1. Standardvärdet är **MapReduce**. Ändra den till **Tez**.

### <a name="enable-local-mode"></a>Aktivera lokalt läge

På samma sätt som Hive, används lokalt läge för att påskynda jobb med relativt mindre mängd data.

1. Aktivera lokalt läge genom att ange `pig.auto.local.enabled` till **True**. Standardvärdet är false.

1. Jobb som har en indatatyp som är mindre än `pig.auto.local.input.maxbytes` egenskap svärdet anses vara små jobb. Standardvärdet är 1 GB.

### <a name="copy-user-jar-cache"></a>Kopiera användarens jar-cache

Gris kopierar JAR-filerna som krävs av UDF: er till en distribuerad cache för att göra dem tillgängliga för aktiviteter. Dessa JAR v7 ändras inte ofta. Om inställningen är aktive rad tillåter `pig.user.cache.enabled`s inställningen att jar v7 placeras i ett cacheminne för att återanvända dem för jobb som körs av samma användare. Detta resulterar i en mindre ökning av jobbets prestanda.

1. Ange `pig.user.cache.enabled` till sant för att aktivera. Standardvärdet är false.

1. Om du vill ange bas Sök vägen för den cachelagrade jar v7 anger `pig.user.cache.location` bas Sök vägen. Standardvärdet är `/tmp`.

### <a name="optimize-performance-with-memory-settings"></a>Optimera prestanda med minnes inställningar

Följande minnes inställningar kan hjälpa till att optimera skript prestanda för gris.

* `pig.cachedbag.memusage`: mängden minne som allokerats till en påse. En påse är en samling av tupler. En tupel är en ordnad uppsättning fält och ett fält är en del av data. Om data i en påse överskrider det allokerade minnet, kommer de att spillas till disken. Standardvärdet är 0,2, vilket motsvarar 20 procent av tillgängligt minne. Det här minnet delas i alla väskor i ett program.

* `pig.spill.size.threshold`: väskor som är större än den här tröskelvärdet för spill storlek (i byte) spillas till disken. Standardvärdet är 5 MB.

### <a name="compress-temporary-files"></a>Komprimera temporära filer

Gris genererar temporära filer under jobb körningen. Komprimering av de temporära filerna resulterar i ökad prestanda när filer läses eller skrivs till disk. Följande inställningar kan användas för att komprimera temporära filer.

* `pig.tmpfilecompression`: om värdet är true aktive ras temporär fil komprimering. Standardvärdet är false.

* `pig.tmpfilecompression.codec`: den komprimerings-codec som ska användas för att komprimera de temporära filerna. De rekommenderade komprimerings-codecarna är [LZO](https://www.oberhumer.com/opensource/lzo/) och fästfunktionen för lägre processor användning.

### <a name="enable-split-combining"></a>Aktivera delnings kombination

När den är aktive rad kombineras små filer för färre kart aktiviteter. Detta förbättrar effektiviteten hos jobb med många små filer. Ange `pig.noSplitCombination` till sant för att aktivera. Standardvärdet är false.

### <a name="tune-mappers"></a>Finjustera mappningar

Antalet mappningar styrs genom att egenskapen `pig.maxCombinedSplitSize`ändras. Detta anger storleken på de data som ska bearbetas av en enskild kart aktivitet. Standardvärdet är fil systemets standard block storlek. Om du ökar det här värdet minskar antalet Mapper-aktiviteter.

### <a name="tune-reducers"></a>Finjustera dämpare

Antalet dereducerare beräknas utifrån parametern `pig.exec.reducers.bytes.per.reducer`. Parametern anger antalet byte som bearbetas per minskning, som standard 1 GB. Om du vill begränsa det maximala antalet avgränsare ställer du in `pig.exec.reducers.max`-egenskapen som standard 999.

## <a name="apache-hbase-optimization-with-the-ambari-web-ui"></a>Apache HBase-optimering med Ambari-webbgränssnittet

[Apache HBase](https://hbase.apache.org/) -konfigurationen har ändrats från fliken **HBase-konfigurationer** . I följande avsnitt beskrivs några av de viktiga konfigurations inställningar som påverkar HBase prestanda.

### <a name="set-hbase_heapsize"></a>Ange HBASE_HEAPSIZE

HBase-heap-storleken anger den maximala mängd heap som ska användas i megabyte efter *region* och *huvud* servrar. Standardvärdet är 1 000 MB. Detta bör justeras för klustrets arbets belastning.

1. Om du vill ändra går du till fönstret **Avancerat HBase-miljö** på fliken HBase- **konfiguration** och letar sedan upp `HBASE_HEAPSIZE`-inställningen.

1. Ändra standardvärdet till 5 000 MB.

    ![Apache Ambari HBase-minne heapsize](./media/hdinsight-changing-configs-via-ambari/ambari-hbase-heapsize.png)

### <a name="optimize-read-heavy-workloads"></a>Optimera Läs frekventa arbets belastningar

Följande konfigurationer är viktiga för att förbättra prestandan för Läs intensiva arbets belastningar.

#### <a name="block-cache-size"></a>Block cachestorlek

Block-cachen är Read cache. Storleken styrs av `hfile.block.cache.size` parameter. Standardvärdet är 0,4, vilket är 40 procent av det totala region Server minnet. Ju större block-cache-storlek, desto snabbare blir de slumpmässiga läsningarna.

1. Om du vill ändra den här parametern går du till fliken **Inställningar** på fliken HBase **configs** och letar sedan upp **% av RegionServer som har allokerats för att läsa buffertar**.

    ![Cachestorlek för Apache HBase-minnes block](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)

1. Om du vill ändra värdet väljer du **redigerings** ikonen.

#### <a name="memstore-size"></a>Memstores-storlek

Alla redigeringar lagras i minnesbufferten, som kallas en *memstores*. Detta ökar den totala mängden data som kan skrivas till disk i en enda åtgärd och den påskyndar åtkomsten till de senaste redigeringarna. Memstores storlek definieras av följande två parametrar:

* `hbase.regionserver.global.memstore.UpperLimit`: definierar den maximala procent andelen av den region server som Memstores kombinerat kan använda.

* `hbase.regionserver.global.memstore.LowerLimit`: definierar den minsta procent andelen av den region server som Memstores kombinerat kan använda.

Om du vill optimera för slumpmässiga läsningar kan du minska Memstores övre och nedre gränser.

#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Antal rader som hämtades vid genomsökning från disk

Inställningen `hbase.client.scanner.caching` definierar antalet rader som läses från disken när `next`-metoden anropas på en skanner.  Standardvärdet är 100. Ju högre siffra, desto färre fjärran rop som görs från klienten till region servern, vilket resulterade i snabbare genomsökningar. Detta kommer dock även att öka minnes belastningen på klienten.

![Apache HBase antal hämtade rader](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Ange inte värdet så att tiden mellan anrop av nästa metod på en skanner är större än tids gränsen för skannern. Tids gränsen för skannern definieras av egenskapen `hbase.regionserver.lease.period`.

### <a name="optimize-write-heavy-workloads"></a>Optimera Skriv tunga arbets belastningar

Följande konfigurationer är viktiga för att förbättra prestandan för Skriv tunga arbets belastningar.

#### <a name="maximum-region-file-size"></a>Maximal region fil storlek

HBase lagrar data i ett internt fil format som kallas *HFile*. Egenskapen `hbase.hregion.max.filesize` definierar storleken på en enskild HFile för en region.  En region delas upp i två regioner om summan av alla HFiles i en region är större än den här inställningen.

![Apache HBase HRegion Max FILESIZE](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Ju större region fil storlek, desto mindre är antalet delningar. Du kan öka fil storleken för att fastställa ett värde som resulterar i maximal skriv prestanda.

#### <a name="avoid-update-blocking"></a>Undvik att blockera uppdateringar

* Egenskapen `hbase.hregion.memstore.flush.size` definierar storleken som Memstores töms på disk. Standard storleken är 128 MB.

* HBase region block multiplikatorn definieras av `hbase.hregion.memstore.block.multiplier`. Standardvärdet är 4. Det högsta tillåtna värdet är 8.

* HBase blockerar uppdateringar om Memstores är (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) byte.

    Med standardvärdet för tömnings storlek och block multiplikatorer blockeras uppdateringar när Memstores är 128 * 4 = 512 MB i storlek. Öka värdet för `hbase.hregion.memstore.block.multiplier`om du vill minska antalet blockerade uppdateringar.

![Apache HBase region block multiplikator](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)

### <a name="define-memstore-size"></a>Definiera Memstores-storlek

Memstores-storlek definieras av parametrarna `hbase.regionserver.global.memstore.UpperLimit` och `hbase.regionserver.global.memstore.LowerLimit`. Att ställa in dessa värden på samma sätt minskar pauserna under skrivningar (även om de orsakar frekvent tömning) och ger ökad skriv prestanda.

### <a name="set-memstore-local-allocation-buffer"></a>Ange Memstores Local Allocation buffer

Memstores för lokal allokering bestäms av egenskapen `hbase.hregion.memstore.mslab.enabled`. När aktive rad (true) förhindrar detta att heap-fragmentering under tung Skriv åtgärd. Standardvärdet är sant.

![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Nästa steg

* [Hantera HDInsight-kluster med webbgränssnittet Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari-REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
