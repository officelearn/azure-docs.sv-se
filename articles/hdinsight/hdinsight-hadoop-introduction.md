---
title: "Vad är Azure HDInsight, Hadoop-teknikstacken och kluster? - Azure | Microsoft Docs"
description: "En introduktion till HDInsight och Hadoop-teknikstacken och komponenter, inklusive Spark, Kafka, Hive och HBase för stordataanalys."
keywords: "Azure hadoop, hadoop azure, introduktion av hadoop, introduktion av hadoop, hadoop-teknikstacken, introduktion till hadoop, introduktion till hadoop, vad är ett hadoop-kluster, vad är hadoop-kluster, vad används hadoop för"
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: cgronlun
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: b413b6f1a6c73251dfdbe6bf9d23cdfa6510839a
ms.contentlocale: sv-se
ms.lasthandoff: 07/22/2017

---
# <a name="introduction-to-azure-hdinsight-the-hadoop-technology-stack-and-hadoop-clusters"></a>Introduktion till Azure HDInsight, Hadoop-teknikstacken och Hadoop-kluster
 Den här artikeln innehåller en introduktion till Azure HDInsight, en molnfördelning av Hadoop-teknikstacken. Den omfattar också en förklaring av vad Hadoop-kluster är och när du ska använda dem. 

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>Vad är HDInsight och Hadoop-teknikstacken? 
Azure HDInsight är en molndistribution av Hadoop-komponenter från [Hortonworks Data Platform (HDP)](https://hortonworks.com/products/data-center/hdp/). [Apache Hadoop](http://hadoop.apache.org/) refererar till ett ekosystem av program med öppen källkod som är ett ramverk för distribuerad bearbetning och analys av stordata-uppsättningar på datakluster. 


Hadoop-teknikstacken innehåller relaterade program och verktyg, inklusive Apache Hive, HBase, Spark, Kafka och många andra. Mer information om tillgängliga stackkomponenter med Hadoop-teknik i HDInsight finns i [Komponenter och versioner som är tillgängliga med HDInsight][component-versioning]. Mer information om Hadoop i HDInsight finns på [sidan om Azure-funktioner för HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-a-hadoop-cluster-and-when-do-you-use-it"></a>Vad är ett Hadoop-kluster och när använder du dem?
*Hadoop* är också en typ av kluster som har följande:

* YARN för jobbschemaläggning och resurshantering
* MapReduce för parallellbearbetning
* Ett distribuerat Hadoop-filsystem (HDFS)
  
Hadoop-kluster används oftast för batchbearbetning av lagrade data. Andra typer av kluster i HDInsight har ytterligare funktioner: Spark har blivit allt mer populärt tack vare dessa snabbare bearbetning i minnet. Se [Klustertyper r i HDInsight](#overview) för mer information.

## <a name="what-is-big-data"></a>Vad är stordata?
Stordata kan avse en stor samling digital information som:

* Sensordata från industriell utrustning
* Kundaktivitet som samlas in från en webbplats
* Ett Twitter-nyhetsflöde

Stordata samlas in i ständigt växande volymer, med allt högre hastighet och i ett växande antal olika format. De kan vara historiska (dvs. lagrade) eller realtidsdata (vilket innebär att de strömmas direkt från källan). 

## <a name="overview"></a>Klustertyper i HDInsight
HDInsight omfattar specifika klustertyper och anpassningsmöjligheter för klustret, till exempel att lägga till komponenter, verktyg och språk.

### <a name="spark-kafka-interactive-hive-hbase-customized-and-other-cluster-types"></a>HBase, Spark, Kafka, Interactive Hive, Storm, anpassade och andra klustertyper
HDInsight erbjuder följande klustertyper:

* **[Apache Hadoop](https://wiki.apache.org/hadoop)**: Med [HDFS](#hdfs), [YARN](#yarn)-resurshantering och en enkel [MapReduce](#mapreduce)-programmeringsmodell för att behandla och analysera batchdata parallellt.
* **[Apache Spark](http://spark.apache.org/)**: Spark är ett ramverk för parallellbearbetning som stöder minnesintern bearbetning för att höja prestandan hos program för stordataanalys. Det kan användas för SQL, strömning av data och maskininlärning. Se [Vad är Apache Spark i HDInsight?](hdinsight-apache-spark-overview.md)
* **[Apache HBase](http://hbase.apache.org/)**: En NoSQL-databas som bygger på Hadoop och ger direktåtkomst och stark konsekvens för stora mängder ostrukturerade och delstrukturerade data, potentiellt miljarder rader gånger miljoner kolumner. Se [Vad är HBase på HDInsight?](hdinsight-hbase-overview.md)
* **[Microsoft R Server](https://msdn.microsoft.com/microsoft-r/rserver)**: En server som fungerar som värd och hanterar parallella, distribuerade R-processer. Den förser på begäran dataforskare, statistiker och R-programmerare med åtkomst till skalbara och distribuerade analysmetoder på HDInsight. Se [Översikt över R Server på HDInsight](hdinsight-hadoop-r-server-overview.md).
* **[Apache Storm](https://storm.incubator.apache.org/)**: Ett distribuerat system för beräkningar i realtid för snabb bearbetning av stora dataströmmar. Storm finns som ett hanterat kluster i HDInsight. Se [Analysera sensordata i realtid med Storm och Hadoop](hdinsight-storm-sensor-data-analysis.md).
* **[Apache Interactive Hive-förhandsgranskning (även känt som: Live Long and Process)](https://cwiki.apache.org/confluence/display/Hive/LLAP)**: Minnesintern cachelagring för interaktiva och snabba Hive-frågor. Se [Använda Interactive Hive in HDInsight](hdinsight-hadoop-use-interactive-hive.md).
* **[Apache Kafka](https://kafka.apache.org/)**: En öppen källkodsplattform som används för att skapa strömmande datapipelines och program. Kafka tillhandahåller även en meddelandeköfunktion med vilken du kan publicera och prenumerera på dataströmmar. Se [Introduktion till Apache Kafka på HDInsight](hdinsight-apache-kafka-introduction.md).

Du kan också konfigurera kluster med hjälp av följande metoder:
* **[Förhandsgranskning av domänanslutna kluster](hdinsight-domain-joined-introduction.md)**: Ett kluster kopplat till en Active Directory-domän, så att du kan kontrollera åtkomsten till och tillhandahålla styrning av data.
* **[Anpassade kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md)**: Kluster med skript som körs under etableringen och som installerar ytterligare komponenter.

### <a name="example-cluster-customization-scripts"></a>Exempelskript för klusteranpassning
Med skriptåtgärder avses skript som körs under klusteretablering och som kan användas för att installera ytterligare komponenter i klustret. 

HDInsight-teamet tillhandahåller följande exempelskript:

* **[Hue](hdinsight-hadoop-hue-linux.md)**: En samling webbprogram som används för att interagera med ett kluster. Endast för Linux-kluster.
* **[Giraph](hdinsight-hadoop-giraph-install-linux.md)**: Bearbetning av grafer för att skapa modeller av relationer mellan saker eller personer.
* **[Solr](hdinsight-hadoop-solr-install-linux.md)**: En sökplattform för stora företag som tillåter fulltextsökning i data.

Information om hur du utvecklar dina egna skriptåtgärder finns i [Utveckling av skriptåtgärder med HDInsight](hdinsight-hadoop-script-actions-linux.md).

## <a name="components-and-utilities-on-hdinsight-clusters"></a>Komponenter och verktyg i HDInsight-kluster
Följande komponenter och verktyg ingår i HDInsight-kluster:

* **[Ambari](#ambari)**: Klusteretablering, hantering, övervakning och verktyg.
* **[Avro](#avro)** (Microsoft .NET-bibliotek för Avro): Dataserialisering för Microsoft .NET-miljön. 
* **[Hive och HCatalog](#hive)**: SQL-liknande frågefunktioner och ett tabell- och lagringshanteringslager.
* **[Mahout](#mahout)**: För skalbara program med maskininlärning.
* **[MapReduce](#mapreduce)**: Äldre ramverk för distribuerad Hadoop-bearbetning och resurshantering. See [YARN](#yarn).
* **[Oozie](#oozie)**: Arbetsflödeshantering.
* **[Phoenix](#phoenix)**: Relationsdatabaslager över HBase.
* **[Pig](#pig)**: Enklare skriptfunktioner för MapReduce-omformningar.
* **[Sqoop](#sqoop)**: Import och export av data.
* **[Tez](#tez)**: Låter dataintensiva processer köras effektivt i större skala.
* **[YARN](#yarn)**: resurshantering som ingår i Hadoop-kärnbiblioteket.
* **[ZooKeeper](#zookeeper)**: Samordning av processer i distribuerade system.

> [!NOTE]
> Information om specifika komponenter och olika versioner finns i [Hadoop-komponenter och versionshantering i HDInsight][component-versioning]
>
>

### <a name="ambari"></a>Ambari
Apache Ambari används för etablering, hantering och övervakning av Apache Hadoop-kluster. Det innehåller en intuitiv samling operatörsverktyg och en stabil uppsättning API:er som döljer komplexiteten hos Hadoop och förenklar klusteranvändningen. HDInsight-kluster på Linux tillhandahåller både Ambari-webbgränssnitt och Ambari REST API. Ambari Views i HDInsight-kluster ger möjlighet att använda plugin-UI-funktioner.
Se [Hantera HDInsight-kluster med Ambari](hdinsight-hadoop-manage-ambari.md) och <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Apache Ambari API-referens</a>.

### <a name="avro"></a>Avro (Microsoft .NET-bibliotek för Avro)
Microsoft .NET-biblioteket för Avro implementerar Apache Avros kompakta binära dataöverföringsformat för serialisering för Microsoft .NET-miljön. Den definierar en språkoberoende schema så att data serialiseras på ett språk som kan läsas på andra språk. Detaljerad information om formatet finns i <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Specifikation för Apache Avro</a>. Avro-filernas format stöder den distribuerade MapReduce-programmeringsmodellen: filerna är ”delbara”, vilket innebär att du kan söka efter valfri punkt i en fil och börja läsa från ett visst block. Närmare anvisningar finns i [Serialisera data med Microsoft .NET-biblioteket för Avro](hdinsight-dotnet-avro-serialization.md). Stöd för Linux-baserade kluster kommer.

### <a name="hdfs"></a>HDFS
Hadoop Distributed File System (HDFS) är ett distribuerat filsystem som, tillsammans med MapReduce och YARN, utgör kärnan i Hadoop-ekosystemet. HDFS är standardfilsystemet för Hadoop-kluster i HDInsight. Se [Fråga efter data från HDFS-kompatibel lagring](hdinsight-hadoop-use-blob-storage.md).

### <a name="hive"></a>Hive och HCatalog
<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> är datalager-programvara som bygger på Hadoop och som låter dig fråga och hantera stora datauppsättningar i distribuerad lagring genom att använda ett SQL-liknande språk som heter HiveQL. Hive, liksom Pig, är en abstraktion ovanpå MapReduce och den översätter frågor till en serie MapReduce-jobb. Hive ligger begreppsmässigt närmare ett system för relationsdatabashantering än Pig och används för mer strukturerade data. För ostrukturerade data är Pig ett bättre val. Se [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> är ett tabell och lagringshanteringslager för Hadoop som ger dig en relationsbaserad datavy. I HCatalog kan du läsa och skriva filer i alla format som fungerar med en Hive SerDe (serialiserare-deserialiserare).

### <a name="mahout"></a>Mahout
<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> är ett bibliotek med maskininlärningsalgoritmer som körs på Hadoop. Med hjälp av statistiska principer instruerar maskininlärningsprogrammen systemen att lära sig från data och använda tidigare resultat för att fastställa framtida beteende. Se [Generera filmrekommendationer med hjälp av Mahout på Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
MapReduce är det äldre programvaruramverket för Hadoop som används för att skriva program för att batchbearbeta stordatauppsättningar parallellt. Ett MapReduce-jobb delar upp stora datauppsättningar och organiserar data i nyckel-/värdepar för bearbetning. MapReduce-jobb körs på [YARN](#yarn). Se <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> i Hadoop-wikin.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> är ett system för att koordinera arbetsflöden som hanterar Hadoop-jobb. Det är integrerat med Hadoop-stacken och stöder Hadoop-jobb för MapReduce, Pig, Hive och Sqoop. Det kan också användas för att schemalägga jobb som är specifika för ett visst system, som Java-program eller kommandoskript. Se [Använda Oozie med Hadoop](hdinsight-use-oozie-linux-mac.md).

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> är ett relationsdatabaslager över HBase. Phoenix omfattar en JDBC-drivrutin som låter dig söka i och hantera SQL-tabeller direkt. Phoenix översätter frågor och andra instruktioner till egna NoSQL API-anrop (i stället för att använda MapReduce) vilket möjliggör snabbare program ovanpå NoSQL-lagringsplatser. Se [Använda Apache Phoenix och SQuirreL med HBase-kluster](hdinsight-hbase-phoenix-squirrel.md).

### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> är en plattform på hög nivå som låter din utföra komplexa MapReduce-transformeringar på väldigt stora datauppsättningar genom att använda ett enkelt skriptspråk som kallas Pig Latin. Pig översätter Pig Latin-skripten så att de kan köras inom Hadoop. Du kan skapa användardefinierade funktioner (UDF:er) för att utöka Pig Latin. Se [Använda Pig med Hadoop](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> är ett verktyg som överför bulkdata mellan Hadoop och relationsdatabaser som SQL eller andra strukturerade datalager så effektivt som möjligt. Se [Använda Sqoop med Hadoop](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> är ett programramverk som bygger på Hadoop YARN och som kör komplicerade acykliska diagram över allmän databearbetning. Det är en mer flexibel och kraftfull efterföljare till MapReduce-ramverket som gör att dataintensiva processer, som Hive, kan köras mer effektivt i större skala. Se ["Använda Apache Tez för bättre prestanda" i Använda Hive och HiveQL](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>YARN
Apache YARN är nästa generation av MapReduce (MapReduce 2.0 eller MRv2) och stöder databehandlingsscenarier utöver MapReduce-batchbearbetning med bättre skalbarhet och realtidsbearbetning. YARN tillhandahåller resurshantering och ett distribuerat programramverk. MapReduce-jobb körs på YARN. Se <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Nästa generation av Apache Hadoop MapReduce (YARN)</a>.

### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> koordinerar processer i stora distribuerade system med hjälp av ett delat hierarkiskt namnområde med dataregister (znodes). Znodes innehåller små mängder metainformation som behövs för att samordna processer: status, plats, konfiguration och så vidare. Se ett exempel på [ZooKeeper med ett HBase-kluster och Apache Phoenix](hdinsight-hbase-phoenix-squirrel-linux.md). 

## <a name="programming-languages-on-hdinsight"></a>Programmeringsspråk i HDInsight
HDInsight-kluster – Hadoop-, HBase-, Kafka-, Spark- och andrakluster – stöder ett antal programmeringsspråk, men vissa är inte installerade som standard. När det gäller bibliotek, moduler eller paket som inte är installerade som standard [använder du en skriptåtgärd för att installera komponenten](hdinsight-hadoop-script-actions-linux.md). 

### <a name="default-programming-language-support"></a>Programmeringsspråk som stöds som standard
Som standard stöder HDInsight-kluster:

* Java
* Python

Ytterligare språk kan installeras med [skriptåtgärder](hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Java Virtual Machine-språk (JVM)
Många andra språk än Java kan köras med hjälp av en Java Virtual Machine (JVM), men för att kunna köra vissa av dessa språk kan ytterligare komponenter behöva installeras i klustret.

Följande JVM-baserade språk stöds i HDInsight-kluster:

* Clojure
* Jython (Python för Java)
* Scala

### <a name="hadoop-specific-languages"></a>Hadoop-specifika språk
HDInsight-kluster stöder följande språk som är specifika för Hadoop-teknikstacken:

* Pig Latin för Pig-jobb
* HiveQL för Hive-jobb och SparkSQL

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard och HDInsight Premium
HDInsight erbjuder molntjänster för stordata i två kategorier, Standard och Premium. HDInsight Standard tillhandahåller ett kluster för stora företag som organisationer kan använda för att köra sina stordataarbetsbelastningar. HDInsight Premium är en ytterligare utbyggnad av standardkapaciteten med avancerade analys- och säkerhetsfunktioner för ett HDInsight-kluster. Mer information finns i [Azure HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)

## <a name="microsoft-business-intelligence-and-hdinsight"></a>Microsoft Business Intelligence och HDInsight
Välbekanta verktyg för business intelligence (BI) hämtar, analyserar och rapporterar data som integreras med HDInsight med antingen Power Query-tillägget eller ODBC-drivrutinen för Microsoft Hive:

* [Ansluta Excel till Hadoop med Power Query](hdinsight-connect-excel-power-query.md): Information om hur du ansluter Excel till det Azure Storage-konto som lagrar data från HDInsight-klustret med hjälp av Microsoft Power Query för Excel. En Windows-arbetsstation krävs. 
* [Ansluta Excel till Hadoop med ODBC-drivrutinen för Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md): Information om hur du importerar data från HDInsight med hjälp av ODBC-drivrutinen för Microsoft Hive. En Windows-arbetsstation krävs. 
* [Microsoft Cloud-plattformen](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): Information om Power BI för Office 365 samt om hur du hämtar utvärderingsversionen för SQL Server och konfigurerar SharePoint Server 2013 och SQL Server BI.
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx)
* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx)


## <a name="next-steps"></a>Nästa steg

* [Komma igång med Hadoop i HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md): En snabbkurs i hur du etablerar HDInsight Hadoop-kluster och kör Hive-exempelfrågor.
* [Komma igång med Spark i HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): En snabbkurs i hur du skapar ett Spark-kluster och kör interaktiva Spark SQL-frågor.
* [Använda R Server på HDInsight](hdinsight-hadoop-r-server-get-started.md): Börja använda R Server i HDInsight Premium.
* [Etablera HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md): Läs hur man etablerar ett HDInsight Hadoop-kluster via Azure Portal, Azure CLI eller Azure PowerShell.


[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/
