---
title: "Vad är Hadoop? Introduktion till Azure HDInsight | Microsoft Docs"
description: "Få en introduktion till Hadoop-ekosystemet och komponenter i HDInsight. HDInsight innehåller Hadoop, Spark, HBase och mycket annat för bearbetning och analys av stordata."
keywords: "stordata-analys, introduktion till hadoop, vad är hadoop, hadoop i molnet, hadoop stack-teknik, hadoop ekosystem"
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/14/2016
ms.author: cgronlun
translationtype: Human Translation
ms.sourcegitcommit: 10d684bd6c9408f2fbd48a0a8804b011f098d71c
ms.openlocfilehash: 8f38b9eaf8bcb498a7983756cbf57f3e372d7fe0


---
# <a name="an-introduction-to-the-hadoop-ecosystem-on-azure-hdinsight"></a>En introduktion till Hadoop-ekosystem i Azure HDInsight
 Den här artikeln ger en introduktion till Hadoop i Azure HDInsight, dess ekosystem och stordata. Läs om Hadoop-komponenterna, gemensam terminologi och scenarier för stordata-analys.

## <a name="what-is-hadoop-on-hdinsight"></a>Vad är Hadoop på HDInsight?
Hadoop refererar till ett ekosystem av program med öppen källkod som är ett ramverk för distribuerad bearbetning, lagring och analys av stordata-uppsättningar på datakluster. Azure HDInsight gör Hadoop-komponenterna från **Hortonworks Data Platform (HDP)**-distributionen tillgängliga i molnet, distribuerar hanterade kluster med hög tillförlitlighet och tillgänglighet, och tillhandahåller säkerhet och styrning i företagsklass med Active Directory.  

Apache Hadoop var det ursprungliga projektet med öppen källkod för bearbetning av stordata. Därefter följde utveckling av relaterad programvara och verktyg som anses del av Hadoops-teknologistack, inklusive Apache Hive, Apache HBase, Apache Spark, Apache Kafka och många andra. Se [Översikt över Hadoop-ekosystemet i HDInsight](#overview) för information.

## <a name="what-is-big-data"></a>Vad är stordata?
Stordata kan avse vilken stor samling digital information som helst, från texten i ett Twitter-flöde eller sensorinformation från industriell utrustning till information om hur kunder surfar och gör inköp på en webbplats. Stordata kan vara historiska data (dvs. lagrade) eller realtidsdata (vilket innebär att de strömmas direkt från källan). Stordata samlas in i ständigt växande volymer, med allt högre hastighet och i ett växande antal olika format.

För att stordata ska kunna ge information eller insikter som kan åtgärdas behöver du samla in relevanta data och ställa rätt frågor. Du måste också se till att data är åtkomliga, rensade och analyserade samt att de presenteras på ett användbart sätt. Det är där stordataanalysen på Hadoop i HDInsight kan vara till hjälp.

## <a name="a-nameoverviewaoverview-of-the-hadoop-ecosystem-in-hdinsight"></a><a name="overview"></a>Översikt över Hadoop-ekosystemet i HDInsight
HDInsight är en molndistribution på Microsoft Azure av den snabbt expanderande Apache Hadoop-teknologistacken för stordata-analys. Den innehåller implementeringar av Apache Spark, HBase, Kafka, Storm, Pig, Hive, Interactive Hive, Sqoop, Oozie, Ambari och så vidare. HDInsight kan också integreras med verktyg för business intelligence (BI) som Power BI, Excel, SQL Server Analysis Services och SQL Server Reporting Services.

### <a name="hadoop-hbase-spark-kafka-interactive-hive-storm-customized-and-other-clusters"></a>Hadoop, HBase, Spark, Kafka, Interactive Hive, Storm, anpassade, och andra kluster
HDInsight erbjuder följande klustertyper:

* **[Apache Hadoop](https://wiki.apache.org/hadoop)**: Ger tillgång till tillförlitlig datalagring med [HDFS](#hdfs) och en enkel [MapReduce](#mapreduce)-programmeringsmodell för parallell bearbetning och analys av data.
* **[Apache Spark](http://spark.apache.org/)**: Spark är ett ramverk för parallellbearbetning som stöder minnesintern bearbetning för att höja prestandan hos program för stordataanalys. Det kan användas för SQL, strömning av data och maskininlärning. Se [Översikt: Vad är Apache Spark i HDInsight?](hdinsight-apache-spark-overview.md)
* **[Apache HBase](http://hbase.apache.org/)**: En NoSQL-databas som bygger på Hadoop och ger direktåtkomst och stark konsekvens för stora mängder ostrukturerade och delstrukturerade data, potentiellt miljarder rader gånger miljoner kolumner. Se [Översikt över HBase på HDInsight](hdinsight-hbase-overview.md).
* **[Microsoft R Server](https://msdn.microsoft.com/en-us/microsoft-r/rserver)**: En server i företagsklass som fungerar som värd och hanterar parallella, distribuerade R-processer. Den förser på begäran dataforskare, statistiker och R-programmerare med åtkomst till skalbara och distribuerade analysmetoder på HDInsight. Se [Översikt över R Server på HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-r-server-overview).
* **[Apache Storm](https://storm.incubator.apache.org/)**: Ett distribuerat system för beräkningar i realtid för snabb bearbetning av stora dataströmmar. Storm finns som ett hanterat kluster i HDInsight. Se [Analysera sensordata i realtid med Storm och Hadoop](hdinsight-storm-sensor-data-analysis.md).
* **[Apache Interactive Hive-förhandsgranskning (även känt som: Live Long and Process)](https://cwiki.apache.org/confluence/display/Hive/LLAP)**: Minnesintern cachelagring för interaktiva och snabba Hive-frågor. Se [Använda Interactive Hive in HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-use-interactive-hive).
* **[Apache Kafka preview](https://kafka.apache.org/)**: En öppen källkodsplattform som används för att skapa strömmande datapipelines och program. Kafka tillhandahåller även en meddelandeköfunktion med vilken du kan publicera och prenumerera på dataströmmar. Se [Introduktion till Apache Kafka på HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-kafka-introduction).
* **[Förhandsgranskning av domänanslutna kluster](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-domain-joined-introduction)**: Ett kluster kopplat till en Active Directory-domän, så att du kan kontrollera åtkomsten till och tillhandahålla styrning av data.
* **[Anpassade kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md)**: Kluster med skript som körs under etableringen och som installerar ytterligare komponenter.

### <a name="example-customization-scripts"></a>Exempelskript för anpassning
Med skriptåtgärder avses skript som körs under klusteretablering och som kan användas för att installera ytterligare komponenter i klustret.

HDInsight-teamet tillhandahåller följande exempelskript:

* [Hue](hdinsight-hadoop-hue-linux.md): En samling webbprogram som används för att interagera med ett kluster. Endast för Linux-kluster.
* [Giraph](hdinsight-hadoop-giraph-install-linux.md): Bearbetning av grafer för att skapa modeller av relationer mellan saker eller personer.
* [R](hdinsight-hadoop-r-scripts-linux.md): Ett språk med öppen källkod och en miljö för statistisk databehandling som används vid maskininlärning.
* [Solr](hdinsight-hadoop-solr-install-linux.md): En sökplattform för stora företag som tillåter fulltextsökning i data.

Information om hur du utvecklar dina egna skriptåtgärder finns i [Utveckling av skriptåtgärder med HDInsight](hdinsight-hadoop-script-actions-linux.md).

## <a name="what-are-the-hadoop-components-and-utilities"></a>Vilka är komponenterna och verktygen i Hadoop?
Följande komponenter och verktyg ingår i HDInsight-kluster.

* **[Ambari](#ambari)**: Klusteretablering, hantering, övervakning och verktyg.
* **[Avro](#avro)** (Microsoft .NET-bibliotek för Avro): Dataserialisering för Microsoft .NET-miljön.
* **[Hive och HCatalog](#hive)**: SQL-liknande frågefunktioner ( Structured Query Language) och ett tabell- och lagringshanteringslager.
* **[Mahout](#mahout)**: För skalbara program med maskininlärning.
* **[MapReduce](#mapreduce)**: Äldre ramverk för distribuerad Hadoop-bearbetning och resurshantering. Se [YARN](#yarn), nästa generation av resursramverket.
* **[Oozie](#oozie)**: Arbetsflödeshantering.
* **[Phoenix](#phoenix)**: Relationsdatabaslager över HBase.
* **[Pig](#pig)**: Enklare skriptfunktioner för MapReduce-omformningar.
* **[Sqoop](#sqoop)**: Import och export av data.
* **[Tez](#tez)**: Låter dataintensiva processer köras effektivt i större skala.
* **[YARN](#yarn)**: Ingår i Hadoop-kärnbiblioteket och nästa generation av MapReduce-ramverket för programvara.
* **[ZooKeeper](#zookeeper)**: Samordning av processer i distribuerade system.

> [!NOTE]
> Information om specifika komponenter och olika versioner finns i [Hadoop-komponenter, versionshantering och tjänsterbjudanden i HDInsight][component-versioning]
>
>

### <a name="a-nameambariaambari"></a><a name="ambari"></a>Ambari
Apache Ambari används för etablering, hantering och övervakning av Apache Hadoop-kluster. Det innehåller en intuitiv samling operatörsverktyg och en stabil uppsättning API:er som döljer komplexiteten hos Hadoop och förenklar klusteranvändningen. HDInsight-kluster på Linux tillhandahåller både Ambari-webbgränssnittet och Ambari REST API, medan Windows-baserade kluster tillhandahåller en delmängd av REST API:et. Ambari Views i HDInsight-kluster ger möjlighet att använda plugin-UI-funktioner.

Se [Hantera HDInsight-kluster med Ambari](hdinsight-hadoop-manage-ambari.md) (endast Linux), [Övervaka Hadoop-kluster i HDInsight med Ambari API](hdinsight-monitor-use-ambari-api.md) och <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Referens för Apache Ambari API</a>.

### <a name="a-nameavroaavro-microsoft-net-library-for-avro"></a><a name="avro"></a>Avro (Microsoft .NET-bibliotek för Avro)
Microsoft .NET-biblioteket för Avro implementerar Apache Avros kompakta binära dataöverföringsformat för serialisering för Microsoft .NET-miljön. Den använder <a target="_blank" href="http://www.json.org/">JavaScript Object Notation (JSON)</a> för att definiera ett språkoberoende schema som garanterar samverkan mellan olika språk, vilket innebär att data kan serialiseras på ett språk och läsas på ett annat. Detaljerad information om formatet finns i <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Specifikation för Apache Avro</a>.
Avro-filernas format stöder den distribuerade MapReduce-programmeringsmodellen. Filerna är "delbara", vilket innebär att du kan söka efter valfri punkt i en fil och börja läsa från ett visst block. Närmare anvisningar finns i [Serialisera data med Microsoft .NET-biblioteket för Avro](hdinsight-dotnet-avro-serialization.md).

### <a name="a-namehdfsahdfs"></a><a name="hdfs"></a>HDFS
Hadoop Distributed File System (HDFS) är ett distribuerat filsystem som, tillsammans med MapReduce och YARN, utgör kärnan i Hadoop-ekosystemet. HDFS är standardfilsystemet för Hadoop-kluster i HDInsight.

### <a name="a-namehiveahive--hcatalog"></a><a name="hive"></a>Hive och HCatalog
<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> är datalager-programvara som bygger på Hadoop och som låter dig fråga och hantera stora datauppsättningar i distribuerad lagring genom att använda ett SQL-liknande språk som heter HiveQL. Hive är liksom Pig en abstraktion ovanpå MapReduce. När du kör Hive översätter det frågor till en serie MapReduce-jobb. Hive ligger begreppsmässigt närmare ett system för relationsdatabashantering än Pig och är därför lämpligt att använda för mer strukturerade data. För ostrukturerade data är Pig ett bättre val. Se [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> är ett tabell och lagringshanteringslager för Hadoop som ger dig en relationsbaserad datavy. I HCatalog kan du läsa och skriva filer i alla format som det går att skriva en Hive SerDe (serialiserare-deserialiserare) för.

### <a name="a-namemahoutamahout"></a><a name="mahout"></a>Mahout
<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> är ett skalbart bibliotek med maskininlärningsalgoritmer som körs på Hadoop. Med hjälp av statistiska principer instruerar maskininlärningsprogrammen systemen att lära sig från data och använda tidigare resultat för att fastställa framtida beteende. Se [Generera filmrekommendationer med hjälp av Mahout på Hadoop](hdinsight-mahout.md).

### <a name="a-namemapreduceamapreduce"></a><a name="mapreduce"></a>MapReduce
MapReduce är det äldre programvaruramverket för Hadoop som används för att skriva program för att batchbearbeta stordatauppsättningar parallellt. Ett MapReduce-jobb delar upp stora datauppsättningar och organiserar data i nyckel-/värdepar för bearbetning.

[YARN](#yarn) är nästa generations Hadoop-resurshanterare och -programramverk och går under namnet MapReduce 2.0. MapReduce-jobb körs på YARN.

Mer information om MapReduce finns i <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> på Hadoop-wikin.

### <a name="a-nameoozieaoozie"></a><a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> är ett system för att koordinera arbetsflöden som hanterar Hadoop-jobb. Det är integrerat med Hadoop-stacken och stöder Hadoop-jobb för MapReduce, Pig, Hive och Sqoop. Det kan också användas för att schemalägga jobb som är specifika för ett visst system, som Java-program eller kommandoskript. Se [Använda Oozie med Hadoop](hdinsight-use-oozie.md).

### <a name="a-namephoenixaphoenix"></a><a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> är ett relationsdatabaslager över HBase. Phoenix omfattar en JDBC-drivrutin som låter dig söka i och hantera SQL-tabeller direkt. Phoenix översätter frågor och andra instruktioner till egna NoSQL API-anrop (i stället för att använda MapReduce) vilket möjliggör snabbare program ovanpå NoSQL-lagringsplatser. Se [Använda Apache Phoenix och SQuirreL med HBase-kluster](hdinsight-hbase-phoenix-squirrel.md).

### <a name="a-namepigapig"></a><a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> är en plattform på hög nivå som låter din utföra komplexa MapReduce-transformeringar på väldigt stora datauppsättningar genom att använda ett enkelt skriptspråk som kallas Pig Latin. Pig översätter Pig Latin-skripten så att de kan köras inom Hadoop. Du kan skapa användardefinierade funktioner (UDF:er) för att utöka Pig Latin. Se [Använda Pig med Hadoop](hdinsight-use-pig.md).

### <a name="a-namesqoopasqoop"></a><a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> är ett verktyg som överför bulkdata mellan Hadoop och relationsdatabaser som SQL eller andra strukturerade datalager så effektivt som möjligt. Se [Använda Sqoop med Hadoop](hdinsight-use-sqoop.md).

### <a name="a-nametezatez"></a><a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> är ett programramverk som bygger på Hadoop YARN och som kör komplicerade acykliska diagram över allmän databearbetning. Det är en mer flexibel och kraftfull efterföljare till MapReduce-ramverket som gör att dataintensiva processer, som Hive, kan köras mer effektivt i större skala. Se ["Använda Apache Tez för bättre prestanda" i Använda Hive och HiveQL](hdinsight-use-hive.md#usetez).

### <a name="a-nameyarnayarn"></a><a name="yarn"></a>YARN
Apache YARN är nästa generation av MapReduce (MapReduce 2.0 eller MRv2) och stöder databehandlingsscenarier utöver MapReduce-batchbearbetning med bättre skalbarhet och realtidsbearbetning. YARN tillhandahåller resurshantering och ett distribuerat programramverk. MapReduce-jobb körs på YARN.

Läs om YARN i <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Nästa generation av Apache Hadoop MapReduce (YARN)</a>.

### <a name="a-namezookeeperazookeeper"></a><a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> koordinerar processer i stora distribuerade system med hjälp av ett delat hierarkiskt namnområde med dataregister (znodes). Znodes innehåller små mängder metainformation som behövs för att samordna processer: status, plats, konfiguration och så vidare.

## <a name="programming-languages-on-hdinsight"></a>Programmeringsspråk i HDInsight
HDInsight-kluster – Hadoop-, HBase-, Storm- och Spark-kluster – stöder ett antal programmeringsspråk, men vissa är inte installerade som standard. När det gäller bibliotek, moduler eller paket som inte är installerade som standard använder du en skriptåtgärd för att installera komponenten. Se [Utveckling av skriptåtgärder med HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="default-programming-language-support"></a>Programmeringsspråk som stöds som standard
Som standard stöder HDInsight-kluster:

* Java
* Python

Ytterligare språk kan installeras med hjälp av skriptåtgärder: [Utveckling av skriptåtgärder med HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Java Virtual Machine-språk (JVM)
Många andra språk än Java kan köras med hjälp av en Java Virtual Machine (JVM), men för att kunna köra vissa av dessa språk kan ytterligare komponenter behöva installeras i klustret.

Följande JVM-baserade språk stöds i HDInsight-kluster:

* Clojure
* Jython (Python för Java)
* Scala

### <a name="hadoop-specific-languages"></a>Hadoop-specifika språk
HDInsight-kluster stöder följande språk som är specifika för Hadoop-ekosystemet:

* Pig Latin för Pig-jobb
* HiveQL för Hive-jobb och SparkSQL

## <a name="a-nameadvantageaadvantages-of-hadoop-in-the-cloud"></a><a name="advantage"></a>Fördelar med Hadoop i molnet
Som en del av molnekosystemet Azure erbjuder Hadoop i HDInsight ett antal fördelar, bland annat:

* Automatisk etablering av Hadoop-kluster. Det är betydligt enklare att skapa HDInsight-kluster än att konfigurera Hadoop-kluster manuellt. Mer information finns i [Etablera Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Avancerade Hadoop-komponenter. Mer information finns i [Hadoop-komponenter, versionshantering och tjänsterbjudanden i HDInsight][component-versioning].
* Tillförlitliga kluster med hög tillgänglighet. Mer information finns i [Tillgänglighet och tillförlitlighet för Hadoop-kluster i HDInsight](hdinsight-high-availability-linux.md).
* Effektiv och ekonomisk datalagring med Azur Blob Storage eller Azure Data Lake Store, som båda är Hadoop-kompatibla alternativ. Mer information finns i [Använda Azure Blob Storage med Hadoop på HDInsight](hdinsight-hadoop-use-blob-storage.md) eller [Använda Data Lake Store med HDInsight-kluster](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal).
* Integrering med andra Azure-tjänster, inklusive [Web apps](https://azure.microsoft.com/documentation/services/app-service/web/) och [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
* Ytterligare VM-storlekar och -typer för att köra HDInsight-kluster. Mer information finns i [Hadoop-komponenter, versionshantering och tjänsterbjudanden i HDInsight][component-versioning].
* Klusterskalning. Med skalning av klustret kan du ändra antalet noder i ett HDInsight-kluster som körs utan att behöva ta bort eller skapa klustret igen.
* Stöd för Virtual Network. HDInsight-kluster kan användas med Azure Virtual Network för isolering av molnresurser eller hybridscenarier som kopplar ihop molnresurserna med dessa i ditt datacenter.
* Låg ingångskostnad. Prova en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/) eller läs i [prisinformationen för HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Mer information om fördelarna med Hadoop i HDInsight finns på [sidan om Azure-funktioner för HDInsight][marketing-page].

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard och HDInsight Premium
HDInsight erbjuder molntjänster för stordata i två kategorier, Standard och Premium. HDInsight Standard tillhandahåller ett kluster för stora företag som organisationer kan använda för att köra sina stordataarbetsbelastningar. HDInsight Premium är en ytterligare utbyggnad med avancerade analys- och säkerhetsfunktioner för ett HDInsight-kluster. Mer information finns i [Azure HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)

## <a name="a-idresourcesaresources-for-learning-more-about-big-data-analysis-hadoop-and-hdinsight"></a><a id="resources"></a>Resurser för att läsa mer om stordata-analys, Hadoop och HDInsight
Komplettera den här introduktionen av Hadoop i molnet och stordataanalys med nedanstående resurser.

### <a name="hadoop-documentation-for-hdinsight"></a>Hadoop-dokumentation för HDInsight
* [HDInsight-dokumentation](https://azure.microsoft.com/documentation/services/hdinsight/): Dokumentationssidan för Hadoop på Azure HDInsight med länkar till artiklar, videor och andra resurser.
* [Komma igång med Hadoop i HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md): En snabbkurs i hur du etablerar HDInsight Hadoop-kluster och kör Hive-exempelfrågor.
* [Komma igång med Spark i HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): En snabbkurs i hur du skapar ett Spark-kluster och kör interaktiva Spark SQL-frågor.
* [Använda R Server på HDInsight](hdinsight-hadoop-r-server-get-started.md): Börja använda R Server i HDInsight Premium.
* [Etablera HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md): Läs hur man etablerar ett HDInsight Hadoop-kluster via Azure Portal, Azure CLI eller Azure PowerShell.

### <a name="apache-hadoop"></a>Apache Hadoop
* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: Läs mer om Apache Hadoop-programbiblioteket, ett ramverk som tillåter distribuerad bearbetning av stora datauppsättningar över datakluster.
* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>: Läs mer om arkitekturen och designen för Hadoops distribuerade filsystem, det primära lagringssystemet som används av Hadoop-program.
* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce-självstudie</a>: Läs mer om programmeringsramverket för att skriva Hadoop-program som snabbt och parallellt bearbetar stora mängder data på stora kluster med Compute-noder.

### <a name="microsoft-business-intelligence"></a>Microsoft Business Intelligence
Välbekanta verktyg för business intelligence (BI) såsom Excel, PowerPivot, SQL Server Analysis Services och SQL Server Reporting Services: hämta, analysera och rapportera data som integreras med HDInsight med antingen Power Query-tillägget eller ODBC-drivrutinen för Microsoft Hive.

Följande BI-verktyg kan vara till hjälp vid stordataanalys:

* [Ansluta Excel till Hadoop med Power Query](hdinsight-connect-excel-power-query.md): Information om hur du ansluter Excel till det Azure Storage-konto som lagrar data som är associerade med ditt HDInsight-kluster med hjälp av Microsoft Power Query för Excel. En Windows-arbetsstation krävs. Fungerar med kluster på Linux eller Windows.
* [Ansluta Excel till Hadoop med ODBC-drivrutinen för Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md): Information om hur du importerar data från HDInsight med hjälp av ODBC-drivrutinen för Microsoft Hive. En Windows-arbetsstation krävs. Fungerar med kluster på Linux eller Windows.
* [Microsoft Cloud-plattformen](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): Information om Power BI för Office 365 samt om hur du hämtar utvärderingsversionen för SQL Server och konfigurerar SharePoint Server 2013 och SQL Server BI.
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx).
* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx).

[marketing-page]: https://azure.microsoft.com/services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/



<!--HONumber=Dec16_HO2-->


