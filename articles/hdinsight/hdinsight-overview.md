---
title: Vad är Azure HDInsight?
description: En introduktion till HDInsight och Apache Hadoop och Apache Spark Technology stack och komponenter, inklusive Kafka, Hive, storm och HBase för stor data analys.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 31e8506067133304144614ff58974ee21e9680be
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2020
ms.locfileid: "84709240"
---
# <a name="what-is-azure-hdinsight"></a>Vad är Azure HDInsight?

Azure HDInsight är en hanterad analys tjänst med full spektrum, öppen källkod i molnet för företag. Du kan använda ramverk baserade på öppen källkod, till exempel Hadoop, Apache Spark, Apache Hive, LLAP, Apache Kafka, Apache Storm, R med flera.

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>Vad är HDInsight och Hadoop-teknikstacken?

Azure HDInsight är en moln distribution av Hadoop-komponenter. Med Azure HDInsight kan du bearbeta stora mängder data på ett enkelt, snabbt och kostnadseffektivt sätt. Du kan använda de mest populära ramverken baserade på öppen källkod, till exempel Hadoop, Spark, Hive, LLAP, Kafka, Storm, R med flera. Med de här ramverken möjliggörs en mängd olika scenarier, t.ex. extrahering, transformering och inläsning (ETL), informationslagerhantering, maskininlärning och IoT.

Mer information om tillgängliga stackkomponenter med Hadoop-teknik i HDInsight finns i [Komponenter och versioner som är tillgängliga med HDInsight](./hdinsight-component-versioning.md). Mer information om Hadoop i HDInsight finns på [sidan om Azure-funktioner för HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-big-data"></a>Vad är stordata?

Stordata samlas in i ständigt växande volymer, med allt högre hastighet och i fler olika format än någonsin tidigare. De kan vara historiska (dvs. lagrade) eller realtidsdata (vilket innebär att de strömmas direkt från källan). Under [Scenarier för att använda HDInsight](#scenarios-for-using-hdinsight) kan du läsa mer om vanliga användningsområden för stordata.

## <a name="why-should-i-use-azure-hdinsight"></a>Varför ska jag använda Azure HDInsight?

Det här avsnittet visar funktionerna i Azure HDInsight.

|Funktion  |Beskrivning  |
|---------|---------|
|Molnbaserat     |     Med Azure HDInsight kan du skapa optimerade kluster för [Hadoop](./hadoop/apache-hadoop-linux-tutorial-get-started.md), [Spark](./spark/apache-spark-jupyter-spark-sql.md), [Interactive query (LLAP)](./interactive-query/apache-interactive-query-get-started.md), [Kafka](./kafka/apache-kafka-get-started.md), [Storm](./storm/apache-storm-tutorial-get-started-linux.md), [HBase](./hbase/apache-hbase-tutorial-get-started-linux.md) och  [ML Services](./r-server/r-server-overview.md) i Azure. HDInsight tillhandahåller även ett serviceavtal från slutpunkt till slutpunkt för alla produktionsarbetsbelastningar.  |
|Billigt och skalbart     | Med HDInsight kan du [skala](./hdinsight-administer-use-portal-linux.md#scale-clusters)   upp eller ned arbets belastningar.Du kan minska kostnaderna genom att [skapa kluster på begäran](./hdinsight-hadoop-create-linux-clusters-adf.md)   och bara betala för det du använder. Du kan också skapa datapipelines för att operationalisera dina jobb. Frikopplad beräkning och lagring ger bättre prestanda och flexibilitet. |
|Säkert och följer standarder    | Med HDInsight kan du skydda företagets datatillgångar med [Azure Virtual Network](./hdinsight-plan-virtual-network-deployment.md), [kryptering](./hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md) och integrering med [Azure Active Directory](./domain-joined/hdinsight-security-overview.md). HDInsight uppfyller också de vanligaste [efterlevnadskraven](https://azure.microsoft.com/overview/trusted-cloud) för olika branscher och myndigheter.        |
|Övervakning    | Azure HDInsight integreras med [Azure Monitor-loggar](./hdinsight-hadoop-oms-log-analytics-tutorial.md) så att du får ett enda gränssnitt som du kan använda för att övervaka alla dina kluster.        |
|Global tillgänglighet | HDInsight är tillgängligt i fler [regioner](https://azure.microsoft.com/regions/services/)   än något annat erbjudande för stor data analys. Azure HDInsight är också tillgängligt i Azure Government, Kina och Tyskland så att du kan uppfylla företagets behov i viktiga områden. |  
|Produktivitet     |  Med Azure HDInsight kan du använda omfattande produktiva verktyg för Hadoop och Spark med de utvecklingsmiljöer du föredrar. Dessa utvecklingsmiljöer omfattar stöd för [Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md), [VSCode](./hdinsight-for-vscode.md), [Eclipse](./spark/apache-spark-eclipse-tool-plugin.md) och [IntelliJ](./spark/apache-spark-intellij-tool-plugin.md) för Scala, Python, R, Java och .NET. Dataexperter kan också samarbeta med vanliga anteckningsböcker som [Jupyter](./spark/apache-spark-jupyter-notebook-kernels.md) och [Zeppelin](./spark/apache-spark-zeppelin-notebook.md).    |
|Utökningsbarhet     |  Du kan utöka HDInsight-klustren genom att installera komponenter (Hue, Presto osv.) med hjälp av [skriptåtgärder](./hdinsight-hadoop-customize-cluster-linux.md), genom att [lägga till gränsnoder](./hdinsight-apps-use-edge-node.md) eller genom [integrering med andra stordatacertifierade program](./hdinsight-apps-install-applications.md). HDInsight ger enkel integrering med de vanligaste stordatalösningarna med distribution med [ett klick](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/).|

## <a name="scenarios-for-using-hdinsight"></a>Scenarier för att använda HDInsight

Azure HDInsight kan användas inom olika scenarier inom stordatabearbetning. Det kan vara historiska data (data som redan har samlats in och lagrats) eller realtidsdata (data som strömmas direkt från källan). Dessa scenarier för bearbetning av sådana data kan sammanfattas i följande kategorier:

### <a name="batch-processing-etl"></a>Batchbearbetning (ETL)

Extrahering, transformering och laddning (ETL) är en process där ostrukturerade eller strukturerade data extraheras från heterogena datakällor. De transformeras sedan till ett strukturerat format och laddas in i ett datalager. Du kan använda transformerade data för datavetenskap eller datalagerhantering.

### <a name="data-warehousing"></a>Datalagerhantering

Du kan använda HDInsight för att köra interaktiva frågor i petabyte-skala på strukturerade eller ostrukturerade data i valfritt format. Du kan också skapa modeller för att koppla dem till BI-verktyg. Om du vill ha mer information kan du [läsa den här kundberättelsen](https://customers.microsoft.com/story/milliman).

![HDInsight-arkitektur: Datalagerhantering](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-data-warehouse.png "Data lager arkitektur för HDInsight")

### <a name="internet-of-things-iot"></a>Sakernas Internet (IoT)

Du kan använda HDInsight för att bearbeta strömmande data som tas emot i real tid från olika typer av enheter. Om du vill ha mer information kan du [läsa det här blogginlägget från Azure som tillkännager den offentliga förhandsversionen av Apache Kafka på HDInsight med Azure Managed Disks](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/).

![HDInsight-arkitektur: Sakernas Internet](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-iot.png "HDInsight IoT-arkitektur")

### <a name="data-science"></a>Data science

Du kan använda HDInsight för att skapa program som utvinner viktiga insikter från data. Du kan också använda Azure Machine Learning ovanpå detta för att förutspå framtida trender för din verksamhet. Om du vill ha mer information kan du [läsa den här kundberättelsen](https://customers.microsoft.com/story/pros).

![HDInsight-arkitektur: Data science](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-data-science.png "HDInsight data science-arkitektur")

### <a name="hybrid"></a>Hybrid

Med HDInsight kan du utöka din befintliga lokala infrastruktur för stordata till Azure så att du kan dra nytta av avancerade analysfunktioner i molnet.

![HDInsight-arkitektur: Hybrid](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-hybrid.png "HDInsight hybrid arkitektur")

## <a name="cluster-types-in-hdinsight"></a>Klustertyper i HDInsight

HDInsight omfattar specifika klustertyper och anpassningsmöjligheter för klustret, till exempel funktioner för att lägga till komponenter, verktyg och språk. HDInsight erbjuder följande klustertyper:

|Typ av kluster | Beskrivning |
|---|---|
|[Apache Hadoop](./hadoop/apache-hadoop-introduction.md)|Ett ramverk som använder HDFS, YARN-resurshantering och en enkel MapReduce-programmeringsmodell för att behandla och analysera batchdata parallellt.|
|[Apache Spark](./spark/apache-spark-overview.md)|Ett ramverk för parallellbearbetning med öppen källkod som stöder intern bearbetning för att höja prestandan hos program för stordataanalys. Se [Vad är Apache Spark i HDInsight?](./spark/apache-spark-overview.md).|
|[Apache HBase](./hbase/apache-hbase-overview.md)|En NoSQL-databas som bygger på Hadoop och ger slumpmässig åtkomst och stark konsekvens för stora mängder ostrukturerade och delstrukturerade data – potentiellt miljarder rader gånger miljoner kolumner. Se [Vad är HBase på HDInsight?](./hbase/apache-hbase-overview.md)|
|[ML Services](./r-server/r-server-overview.md)|En server som fungerar som värd och hanterar parallella, distribuerade R-processer. Den förser på begäran dataforskare, statistiker och R-programmerare med åtkomst till skalbara och distribuerade analysmetoder på HDInsight. Se [Översikt över ML Services på HDInsight](./r-server/r-server-overview.md).|
|[Apache Storm](./storm/apache-storm-overview.md)|Ett distribuerat system för beräkningar i realtid som ger snabb bearbetning av stora dataströmmar. Storm finns som ett hanterat kluster i HDInsight. Se [Analysera sensordata i realtid med Storm och Hadoop](./storm/apache-storm-overview.md).|
|[Apache interaktiv fråga](./interactive-query/apache-interactive-query-get-started.md)|Minnesintern cachelagring för interaktiva och snabba Hive-frågor. Se [Använda Interactive Query i HDInsight](./interactive-query/apache-interactive-query-get-started.md).|
|[Apache Kafka](./kafka/apache-kafka-introduction.md)|En öppen källkodsplattform som används för att skapa strömmande datapipelines och program. Kafka tillhandahåller även en meddelandeköfunktion med vilken du kan publicera och prenumerera på dataströmmar. Se [Introduktion till Apache Kafka på HDInsight](./kafka/apache-kafka-introduction.md).|

## <a name="open-source-components-in-hdinsight"></a>Komponenter med öppen källkod i HDInsight

Med Azure HDInsight kan du skapa kluster med ramverk med öppen källkod, till exempel Hadoop, Spark, Hive, LLAP, Kafka, Storm, HBase och R. Dessa kluster har som standard andra komponenter med öppen källkod som ingår i klustret, till exempel [Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md), [Avro](https://avro.apache.org/docs/current/spec.html), [Apache Hive](https://hive.apache.org), [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog/), [Apache Mahout](https://mahout.apache.org/), [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Phoenix](https://phoenix.apache.org/), [Apache Pig](https://pig.apache.org/), [Apache Sqoop](https://sqoop.apache.org/), [Apache Tez](https://tez.apache.org/), [Apache Oozie](https://oozie.apache.org/) och [Apache ZooKeeper](https://zookeeper.apache.org/).  

## <a name="programming-languages-in-hdinsight"></a>Programmeringsspråk i HDInsight

HDInsight-kluster, inklusive Hadoop, HBase, Kafka, Spark med flera, stöder ett antal programmeringsspråk. Vissa programmeringsspråk är inte installerade som standard. För bibliotek, moduler eller paket som inte är installerade som standard [använder du en skript åtgärd för att installera komponenten](./hdinsight-hadoop-script-actions-linux.md).

|Programmeringsspråk  |Information  |
|---------|---------|
|Programmeringsspråk som stöds som standard     | Som standard stöder HDInsight-kluster:<ul><li>Java</li><li>Python</li><li>.NET</li><li>Go</li></ul>  |
|Java Virtual Machine-språk (JVM)     | Många andra språk än Java kan köras på en Java Virtual Machine (JVM). Om du kör vissa av dessa språk kan det dock hända att du måste installera ytterligare komponenter på klustret. Följande JVM-baserade språk stöds i HDInsight-kluster: <ul><li>Clojure</li><li>Jython (Python för Java)</li><li>Scala</li></ul>     |
|Hadoop-specifika språk     | HDInsight-kluster stöder följande språk som är specifika för Hadoop-teknikstacken: <ul><li>Pig Latin för Pig-jobb</li><li>HiveQL för Hive-jobb och SparkSQL</li></ul>        |

## <a name="development-tools-for-hdinsight"></a>Utvecklingsverktyg för HDInsight

Du kan använda utvecklingsverktyg för HDInsight, inklusive IntelliJ, Eclipse, Visual Studio Code och Visual Studio för att skapa och skicka HDInsight-datafrågor och -jobb med sömlös Azure-integrering.

* [Azure Toolkit för IntelliJ](./spark/apache-spark-intellij-tool-plugin.md)

* [Azure Toolkit för Sol förmörkelse](./spark/apache-spark-eclipse-tool-plugin.md)

* [Azure HDInsight Tools för VS Code](./hdinsight-for-vscode.md)

* [Azure Data Lake Tools för Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md)

## <a name="business-intelligence-on-hdinsight"></a>Business intelligence i HDInsight

Välbekanta verktyg för Business Intelligence (BI) hämtar, analyserar och rapporterar data som integreras med HDInsight med antingen Power Query-tillägget eller ODBC-drivrutinen för Microsoft Hive:

* [Apache Spark BI med hjälp av datavisualiseringsverktyg med Azure HDInsight](./spark/apache-spark-use-bi-tools.md)

* [Visualisera Apache Hive-data med Microsoft Power BI i Azure HDInsight](./hadoop/apache-hadoop-connect-hive-power-bi.md)

* [Visualisera Interactive Query Hive-data med Power BI i Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)

* [Ansluta Excel till Apache Hadoop med Power Query](./hadoop/apache-hadoop-connect-excel-power-query.md) (kräver Windows)

* [Ansluta Excel till Apache Hadoop med Microsoft Hives ODBC-drivrutin](./hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) (kräver Windows)

## <a name="next-steps"></a>Nästa steg

* [Skapa Apache Hadoop-kluster i HDInsight](./hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
