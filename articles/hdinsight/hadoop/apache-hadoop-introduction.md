---
title: "Vad är HDInsight och Hadoop- och Spark-teknikstacken? - Azure | Microsoft Docs"
description: "En introduktion till HDInsight och Hadoop- och Spark-teknikstacken samt deras komponenter, inklusive Kafka, Hive, Storm och HBase för stordataanalys."
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
ms.date: 12/13/2017
ms.author: cgronlun
ms.openlocfilehash: c53c4eba6d46c03bbfc6bb316ae4e505abb7b781
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2018
---
# <a name="introduction-to-azure-hdinsight-and-the-hadoop-and-spark-technology-stack"></a>Introduktion till Azure HDInsight och Hadoop- och Spark-teknikstacken
Den här artikeln ger en introduktion till Azure HDInsight. Azure HDInsight är en fullständigt hanterad analystjänst med fullständigt spektrum med öppen källkod för företag. Du kan använda ramverk baserade på öppen källkod, till exempel Hadoop, Spark, Hive, LLAP, Kafka, Storm, R med flera. 

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

[Apache Hadoop](http://hadoop.apache.org/) refererar till ett ekosystem av program med öppen källkod som är ett ramverk för distribuerad bearbetning och analys av stordata-uppsättningar i kluster. Hadoop-teknikstacken innehåller relaterade program och verktyg, inklusive Apache Hive, HBase, Spark, Kafka och många andra. 

Mer information om tillgängliga stackkomponenter med Hadoop-teknik i HDInsight finns i [Komponenter och versioner som är tillgängliga med HDInsight][component-versioning]. Mer information om Hadoop i HDInsight finns på [sidan om Azure-funktioner för HDInsight](https://azure.microsoft.com/services/hdinsight/).

[Apache Spark](http://spark.apache.org) är ett ramverk för parallellbearbetning med öppen källkod som stöder intern bearbetning för att höja prestandan hos program för stordataanalys. Du kan läsa mer om Spark i HDInsight i [Introduktion till Spark i Azure HDInsight](../spark/apache-spark-overview.md). 

<a href="https://ms.portal.azure.com/#create/Microsoft.HDInsightCluster" target="_blank"><img src="./media/apache-hadoop-introduction/deploy-to-azure.png" alt="Deploy an Azure HDInsight cluster"></a>

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>Vad är HDInsight och Hadoop-teknikstacken? 
Azure HDInsight är en molndistribution av Hadoop-komponenter från [Hortonworks Data Platform (HDP)](https://hortonworks.com/products/data-center/hdp/). Med Azure HDInsight kan du bearbeta stora mängder data på ett enkelt, snabbt och kostnadseffektivt sätt. Du kan använda de mest populära ramverken baserade på öppen källkod, till exempel Hadoop, Spark, Hive, LLAP, Kafka, Storm, R med flera. Med de här ramverken möjliggörs en mängd olika scenarier, t.ex. extrahering, transformering och inläsning (ETL), informationslagerhantering, maskininlärning och IoT.

## <a name="what-is-big-data"></a>Vad är stordata?

Stordata samlas in i ständigt växande volymer, med allt högre hastighet och i fler olika format än någonsin tidigare. De kan vara historiska (dvs. lagrade) eller realtidsdata (vilket innebär att de strömmas direkt från källan). Under [Scenarier för att använda HDInsight](#scenarios-for-using-hdinsight) kan du läsa mer om vanliga användningsområden för stordata.

## <a name="why-should-i-use-hdinsight"></a>Varför ska jag använda HDInsight?

Det här avsnittet visar funktionerna i Azure HDInsight.


|Funktion  |Beskrivning  |
|---------|---------|
|Molnbaserat     |     Med Azure HDInsight kan du skapa optimerade kluster för [Hadoop](apache-hadoop-linux-tutorial-get-started.md), [Spark](../spark/apache-spark-jupyter-spark-sql.md), [Interactive query (LLAP)](../interactive-query/apache-interactive-query-get-started.md), [Kafka](../kafka/apache-kafka-get-started.md), [Storm](../storm/apache-storm-tutorial-get-started-linux.md), [HBase](../hbase/apache-hbase-tutorial-get-started-linux.md) och  [R Server](../r-server/r-server-get-started.md) i Azure. HDInsight tillhandahåller även ett serviceavtal från slutpunkt till slutpunkt för alla produktionsarbetsbelastningar.  |
|Billigt och skalbart     | Med HDInsight kan du [skala](../hdinsight-administer-use-portal-linux.md)  upp eller ned arbetsbelastningar. Du kan minska kostnaderna genom att  [skapa kluster på begäran](../hdinsight-hadoop-create-linux-clusters-adf.md) och endast betala för det som du använder. Du kan också skapa datapipelines för att operationalisera dina jobb. Frikopplad beräkning och lagring ger bättre prestanda och flexibilitet. |
|Säkert och följer standarder    | Med HDInsight kan du skydda företagets datatillgångar med [Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md), [kryptering](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md) och integrering med [Azure Active Directory](../domain-joined/apache-domain-joined-introduction.md). HDInsight uppfyller också de vanligaste [efterlevnadskraven](https://azure.microsoft.com/overview/trusted-cloud) för olika branscher och myndigheter.        |
|Övervakning    | Azure HDInsight kan integreras med [Azure Log Analytics](../hdinsight-hadoop-oms-log-analytics-tutorial.md) så att du får ett enda gränssnitt som du kan använda för att övervaka alla dina kluster.        |
|Global tillgänglighet | HDInsight är tillgängligt i fler  [regioner](https://azure.microsoft.com/regions/services/) än något annat erbjudande för stordataanalys. Azure HDInsight är också tillgängligt i Azure Government, Kina och Tyskland så att du kan uppfylla företagets behov i viktiga områden. |  
|Produktivitet     |  Med Azure HDInsight kan du använda omfattande produktiva verktyg för Hadoop och Spark med de utvecklingsmiljöer du föredrar. Dessa utvecklingsmiljöer omfattar stöd för [Visual Studio](apache-hadoop-visual-studio-tools-get-started.md), [Eclipse](../spark/apache-spark-eclipse-tool-plugin.md) och [IntelliJ](../spark/apache-spark-intellij-tool-plugin.md) för Scala, Python, R, Java och .NET. Dataexperter kan också samarbeta med vanliga anteckningsböcker som [Jupyter](../spark/apache-spark-jupyter-notebook-kernels.md) och [Zeppelin](../spark/apache-spark-zeppelin-notebook.md).    |
|Utökningsbarhet     |  Du kan utöka HDInsight-klustren genom att installera komponenter (Hue, Presto osv.) med hjälp av [skriptåtgärder](../hdinsight-hadoop-customize-cluster-linux.md), genom att [lägga till gränsnoder](../hdinsight-apps-use-edge-node.md) eller genom [integrering med andra stordatacertifierade program](../hdinsight-apps-install-applications.md). HDInsight ger enkel integrering med de vanligaste stordatalösningarna med distribution med [ett klick](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/).|

## <a name="scenarios-for-using-hdinsight"></a>Scenarier för att använda HDInsight

Azure HDInsight kan användas inom olika scenarier inom stordatabearbetning. Det kan vara historiska data (data som redan har samlats in och lagrats) eller realtidsdata (data som strömmas direkt från källan). Dessa scenarier för bearbetning av sådana data kan sammanfattas i följande kategorier: 

### <a name="batch-processing-etl"></a>Batchbearbetning (ETL)

Extrahering, transformering och laddning (ETL) är en process där ostrukturerade eller strukturerade data extraheras från heterogena datakällor. De transformeras sedan till ett strukturerat format och laddas in i ett datalager. Du kan använda transformerade data för datavetenskap eller datalagerhantering.

### <a name="internet-of-things-iot"></a>Sakernas Internet (IoT)

Du kan använda HDInsight för att bearbeta strömmande data som tas emot i realtid från olika enheter. Om du vill ha mer information kan du [läsa det här blogginlägget från Azure som tillkännager den offentliga förhandsversionen av Apache Kafka på HDInsight med Azure Managed Disks](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/).

![HDInsight-arkitektur: Sakernas Internet](./media/apache-hadoop-introduction/hdinsight-architecture-iot.png) 

### <a name="data-science"></a>Data science

Du kan använda HDInsight för att skapa program som utvinner viktiga insikter från data. Du kan också använda Azure Machine Learning ovanpå detta för att förutspå framtida trender för din verksamhet. Om du vill ha mer information kan du [läsa den här kundberättelsen](https://customers.microsoft.com/story/pros).

![HDInsight-arkitektur: Data science](./media/apache-hadoop-introduction/hdinsight-architecture-data-science.png)

### <a name="data-warehousing"></a>Datalagerhantering

Du kan använda HDInsight för att köra interaktiva frågor i petabyte-skala på strukturerade eller ostrukturerade data i valfritt format. Du kan också skapa modeller för att koppla dem till BI-verktyg. Om du vill ha mer information kan du [läsa den här kundberättelsen](https://customers.microsoft.com/story/milliman). 

![HDInsight-arkitektur: Datalagerhantering](./media/apache-hadoop-introduction/hdinsight-architecture-data-warehouse.png)

### <a name="hybrid"></a>Hybrid

Med HDInsight kan du utöka din befintliga lokala infrastruktur för stordata till Azure så att du kan dra nytta av avancerade analysfunktioner i molnet.

![HDInsight-arkitektur: Hybrid](./media/apache-hadoop-introduction/hdinsight-architecture-hybrid.png)

## <a name="overview"></a>Klustertyper i HDInsight
HDInsight omfattar specifika klustertyper och anpassningsmöjligheter för klustret, till exempel funktioner för att lägga till komponenter, verktyg och språk.

### <a name="spark-kafka-interactive-query-hbase-customized-and-other-cluster-types"></a>Spark, Kafka, Interactive Query, HBase, anpassade och andra klustertyper
HDInsight erbjuder följande klustertyper:

* **[Apache Hadoop](https://wiki.apache.org/hadoop)**: Ett ramverk som använder [HDFS](#hdfs), [YARN](#yarn)-resurshantering och en enkel [MapReduce](#mapreduce)-programmeringsmodell för att behandla och analysera batchdata parallellt.

* **[Apache Spark](http://spark.apache.org/)**: Ett ramverk för parallellbearbetning som stöder intern bearbetning för att höja prestandan hos program för stordataanalys. Spark fungerar för SQL, strömmande data och maskininlärning. Se [Vad är Apache Spark i HDInsight?](../spark/apache-spark-overview.md)

* **[Apache HBase](http://hbase.apache.org/)**: En NoSQL-databas som bygger på Hadoop och ger direktåtkomst och stark konsekvens för stora mängder ostrukturerade och delstrukturerade data, potentiellt miljarder rader gånger miljoner kolumner. Se [Vad är HBase på HDInsight?](../hbase/apache-hbase-overview.md)

* **[Microsoft R Server](https://msdn.microsoft.com/microsoft-r/rserver)**: En server som fungerar som värd och hanterar parallella, distribuerade R-processer. Den förser på begäran dataforskare, statistiker och R-programmerare med åtkomst till skalbara och distribuerade analysmetoder på HDInsight. Se [Översikt över R Server på HDInsight](../r-server/r-server-overview.md).

* **[Apache Storm](https://storm.incubator.apache.org/)**: Ett distribuerat system för beräkningar i realtid för snabb bearbetning av stora dataströmmar. Storm finns som ett hanterat kluster i HDInsight. Se [Analysera sensordata i realtid med Storm och Hadoop](../storm/apache-storm-sensor-data-analysis.md).

* **[Apache Interactive Query-förhandsgranskning (även kallat: Live Long and Process)](https://cwiki.apache.org/confluence/display/Hive/LLAP)**: Minnesintern cachelagring för interaktiva och snabba Hive-frågor. Se [Använda Interactive Query i HDInsight](../interactive-query/apache-interactive-query-get-started.md).

* **[Apache Kafka](https://kafka.apache.org/)**: En öppen källkodsplattform som används för att skapa strömmande datapipelines och program. Kafka tillhandahåller även en meddelandeköfunktion med vilken du kan publicera och prenumerera på dataströmmar. Se [Introduktion till Apache Kafka på HDInsight](../kafka/apache-kafka-introduction.md).

## <a name="open-source-components-in-hdinsight"></a>Komponenter med öppen källkod i HDInsight

Med Azure HDInsight kan du skapa kluster med ramverk med öppen källkod, till exempel Hadoop, Spark, Hive, LLAP, Kafka, Storm, HBase och R. Dessa kluster har som standard andra komponenter med öppen källkod som ingår i klustret, till exempel [Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md), [Avro](http://avro.apache.org/docs/current/spec.html), [Hive](http://hive.apache.org), [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog/), [Mahout](https://mahout.apache.org/), [MapReduce](http://wiki.apache.org/hadoop/MapReduce), [YARN](http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Phoenix](http://phoenix.apache.org/), [Pig](http://pig.apache.org/), [Sqoop](http://sqoop.apache.org/), [Tez](http://tez.apache.org/), [Oozie](http://oozie.apache.org/) och [ZooKeeper](http://zookeeper.apache.org/).  


## <a name="programming-languages-in-hdinsight"></a>Programmeringsspråk i HDInsight
HDInsight-kluster, inklusive Hadoop, HBase, Kafka, Spark med flera, stöder ett antal programmeringsspråk. Vissa programmeringsspråk är inte installerade som standard. När det gäller bibliotek, moduler eller paket som inte är installerade som standard [använder du en skriptåtgärd för att installera komponenten](../hdinsight-hadoop-script-actions-linux.md). 

### <a name="default-programming-language-support"></a>Programmeringsspråk som stöds som standard
Som standard stöder HDInsight-kluster:

* Java
* Python

Du kan installera ytterligare språk med [skriptåtgärder](../hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Java Virtual Machine-språk (JVM)
Många andra språk än Java kan köras på en Java Virtual Machine (JVM). Om du kör vissa av dessa språk kan det dock hända att du måste installera ytterligare komponenter på klustret.

Följande JVM-baserade språk stöds i HDInsight-kluster:

* Clojure
* Jython (Python för Java)
* Scala

### <a name="hadoop-specific-languages"></a>Hadoop-specifika språk
HDInsight-kluster stöder följande språk som är specifika för Hadoop-teknikstacken:

* Pig Latin för Pig-jobb
* HiveQL för Hive-jobb och SparkSQL

## <a name="business-intelligence-on-hdinsight"></a>Business intelligence i HDInsight
Välbekanta verktyg för Business Intelligence (BI) hämtar, analyserar och rapporterar data som integreras med HDInsight med antingen Power Query-tillägget eller ODBC-drivrutinen för Microsoft Hive:

* [Apache Spark BI med hjälp av datavisualiseringsverktyg med Azure HDInsight](../spark/apache-spark-use-bi-tools.md)

* [Visualisera Hive-data med Microsoft Power BI i Azure HDInsight](apache-hadoop-connect-hive-power-bi.md) 

* [Visualisera Interactive Query Hive-data med Power BI i Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)

* [Ansluta Excel till Hadoop med Power Query](apache-hadoop-connect-excel-power-query.md): Information om hur du ansluter Microsoft Power Query för Excel till det Azure Storage-konto som lagrar data från HDInsight-klustret. En Windows-arbetsstation krävs. 

* [Ansluta Excel till Hadoop med ODBC-drivrutinen för Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md): Information om hur du importerar data från HDInsight med hjälp av ODBC-drivrutinen för Microsoft Hive. En Windows-arbetsstation krävs. 

* [Microsoft Cloud-plattformen](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): Information om Power BI för Office 365 samt om hur du hämtar utvärderingsversionen för SQL Server och konfigurerar SharePoint Server 2013 och SQL Server BI.

* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx)

* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx)


## <a name="next-steps"></a>Nästa steg

* [Kom igång med Hadoop i HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Kom igång med Spark i HDInsight](../spark/apache-spark-jupyter-spark-sql.md)
* [Kom igång med Kafka på HDInsight](../kafka/apache-kafka-get-started.md)
* [Kom igång med Storm i HDInsight](../storm/apache-storm-tutorial-get-started-linux.md)
* [Kom igång med HBase i HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
* [Kom igång med Interactive Query (LLAP) i HDInsight](../interactive-query/apache-interactive-query-get-started.md)
* [Kom igång med R Server i HDInsight](../r-server/r-server-get-started.md)
* [Hantera HDInsight-kluster](../hdinsight-administer-use-portal-linux.md)
* [Skydda HDInsight-kluster](../domain-joined/apache-domain-joined-introduction.md)
* [Övervaka HDInsight-kluster](../hdinsight-hadoop-oms-log-analytics-tutorial.md)


[component-versioning]: ../hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/
