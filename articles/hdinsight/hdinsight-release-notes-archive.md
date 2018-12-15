---
title: Arkiverade viktig information för Azure HDInsight
description: Arkiverade viktig information och versioner av Azure HDInsight.
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: hrasheed
ms.openlocfilehash: 43acf5f59667f45e67156b33e31d28160ba47c13
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408816"
---
# <a name="archived-release-notes-for-azure-hdinsight"></a>Arkiverade viktig information för Azure HDInsight

För den **senaste** uppdateringar med Azure HDInsight, se [HDInsight viktig](hdinsight-release-notes.md).

> [!IMPORTANT]  
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight versionshantering artikeln](hdinsight-component-versioning.md).

## <a name="notes-for-06272018---release-of-new-open-source-versions-adls-gen2-etc-on-hdinsight-36"></a>Information om 06/27/2018 - versionen av nya versioner av öppen källkod, ADLS Gen2 etc. på HDInsight 3.6
Juni 2018-versionen av HDInsight är en betydande version med en mängd funktioner för våra kunder och nya uppdateringar. Finns i det här [publicera](https://azure.microsoft.com/blog/enterprises-get-deeper-insights-with-hadoop-and-spark-updates-on-azure-hdinsight/) för mer information.

Följande är de viktigaste delarna. Detaljerad viktig buggar, kända problem och så vidare, kan du läsa den [viktig information för Azure HDInsight](hdinsight-release-notes.md).

- **Uppdatera Apache Hadoop och andra projekt med öppen källkod** – förutom 1000 + felkorrigeringar över 20 + open source-projekt, den här uppdateringen innehåller en ny version av Apache Spark (2.3) och Apache Kafka (1.0).
- **Uppdatera R Server 9.1 till Machine Learning Services 9.3** – med den här versionen har vi tillhandahåller dataforskare och ingenjörer med bäst av öppen källkod, förbättrad med algoritmiska innovationer och robusta driftsättning, alla tillgängliga i deras önskat språk med hastigheten på Apache Spark. Den här versionen utökar funktionerna i R Server med stöd för Python, vilket leder till ändringen av klustret från R-Server till ML-tjänster som har lagts till. 
- **Stöd för Azure Data Lake Storage Gen2** – HDInsight har stöd för förhandsversionen av Azure Data Lake Storage Gen2. Kunder kommer att kunna välja ett Gen2 ADLS-konto som ett Arkiv för sina HDInsight-kluster i de tillgängliga regionerna.
- **HDInsight Enterprise Security Package uppdateringar (förhandsversion)** – (förhandsversion) tjänstslutpunkter i virtuella nätverk som stöd för Azure blob Storage, ADLS Gen1, Cosmos DB och Azure DB. 

## <a name="notes-for-03202018---release-of-spark-22-on-hdinsight-36"></a>Information om 03/20/2018 - versionen av Spark 2.2 på HDInsight 3.6

- Apache Spark 2.2.0 förbättrar stabiliteten i Spark Core, SQL, ML och ger Structured Streaming GA status. Apache Spark 2.2.0 är nu tillgängligt på HDInsight 3.6.


## <a name="notes-for-08012017-release-of-hdinsight"></a>Information för 2017-08-01-versionen av HDInsight

| Titel | Beskrivning | Berörda området  | Typ av kluster  | 
| --- | --- | --- | --- | --- |
| Version av Microsoft R Server 9.1 på HDInsight |HDInsight har nu stöd för etablering R Server 9.1 kluster på HDInsight. Mer information om Microsoft R Server 9.1 versionen finns i [den här bloggen](https://blogs.technet.microsoft.com/dataplatforminsider/2017/04/19/introducing-microsoft-r-server-9-1-release/). |Tjänst |R Server |
| HDInsight 3.6 innehåller nu nyare versioner av Hadoop-stacken|<ul><li>En detaljerad lista över uppdaterade versioner finns i [Apache Hadoop-komponent-versioner som är tillgängliga i HDInsight](hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).</li><li>En lista över buggar i de senaste versionerna av Hadoop-stacken, se [Apache Patch Information](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/patch_parent.html).</li><li>En lista över större ändringar mellan HDP 2.6.1 (som är nu tillgänglig i HDInsight 3.6), se [ https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html ](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html).</li><li>En lista över kända problem i HDP 2.6.1 finns i [kända problem](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/known_issues.html).</li></ul> |Tjänst |Alla |Gäller inte |
| Uppdateringar till Interactive Hive (förhandsgranskning)-kluster |<ul><li><b>Förbättring av funktionen.</b> Implementeringen av cachelagrade metastore som minskar belastningen på serverdelen SQL genom cachelagring av metadata och förbättrar prestandan för alla åtgärder för metadata.  Den här förbättringen är nu en standard på alla interaktiva Apache Hive-kluster. Mer information finns på [https://issues.apache.org/jira/browse/HIVE-16520](https://issues.apache.org/jira/browse/HIVE-16520).</li><li><b>Förbättring av funktionen.</b> Läser in partition dynamiska optimeras. Mer information finns på [https://issues.apache.org/jira/browse/HIVE-14204](https://issues.apache.org/jira/browse/HIVE-14204).</li><li><b>Förbättring av funktionen.</b> Konfiguration av optimeringar för HDInsight på Linux.</li><li><b>Felkorrigering.</b> `CredentialProviderFactory$getProviders` är inte trådsäker. Det här problemet löses nu. Mer information finns på [https://issues.apache.org/jira/browse/HADOOP-14195](https://issues.apache.org/jira/browse/HADOOP-14195).</li><li><b>Felkorrigering.</b> Hög CPU-användning med WASB-drivrutinen `liststatus` API, vilket resulterar i felaktiga ATS-prestanda. Det här problemet löses nu. Mer information finns på [https://github.com/Azure/azure-storage-java/pull/154](https://github.com/Azure/azure-storage-java/pull/154).</li></ul> |Tjänst |Interaktiv Hive (förhandsgranskning) |
| Uppdateringar av Hadoop-kluster |Ökad tillförlitlighet för Templeton jobbet igen. Mer information finns i [https://issues.apache.org/jira/browse/HIVE-15947](https://issues.apache.org/jira/browse/HIVE-15947) |Tjänst |Hadoop |
| YARN-uppdateringar | HDInsight skapar nu en 250 GB Ambari databas (utan att öka kostnaden), vilket resulterar i en bättre upplevelse för kunder. Den här ändringen bör förhindra att ATS komma fylld uppåt och troligtvis en bättre prestanda. |Tjänst |Alla |
| Spark-uppdateringar | Versionen av Spark 2.1.1. Mer information finns i [Apache Spark-versionen 2.1.1](https://spark.apache.org/releases/spark-release-2-1-1.html). | Tjänst | Spark |

  



## <a name="04062017---general-availability-of-hdinsight-36"></a>04/06/2017 – allmän tillgänglighet för HDInsight 3.6

* Med den här versionen lägger Azure HDInsight version 3.6, som baseras på HDP 2.6. Viktig information för HDP 2.6 finns [här](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) och du hittar mer information om versioner av HDInsight [här](hdinsight-component-versioning.md). HDInsight 3.6 är tillgängligt för följande arbetsbelastningar:

    * Hadoop v2.7.3
    * HBase v1.1.2
    * Storm v1.1.0
    * Spark v2.1.0
    * Interaktiv Hive v2.1.0

* **Stöd för Hive-vyn 2.0**. Detta förbättrar användarupplevelsen för Interactive Hive. Mer information finns i [Hortonworks dokumentation](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html).

* **Prestandaförbättringar med Hive-LLAP**. Mer information finns i [Hortonworks dokumentation](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/).

* **Nya funktioner i Hive**. Se [Hortonworks dokumentation](https://hortonworks.com/apache/hive/#section_4).

* **Hive CLI utfasning**: Hive CLI tas ur bruk och kunder uppmanas att använda Beeline i stället. Mer information finns i [Apache-dokumentationen](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline). Anvisningar för hur du använder Beeline med HDInsight finns i [använda Beeline med HDInsight Hadoop-kluster](hadoop/apache-hadoop-use-hive-beeline.md).

* **Nya funktioner i Apache Phoenix och HBase**.
    * Stöd för Storage-kvot: Ofta används i miljöer med flera innehavare, vilket gör att begränsat lagringsutrymme på en per tabell och namnområdesnivå.
    * Phoenix indexering förbättringar: Skapandet av inkrementell index och återskapa/återuppta indexering från föregående fel.
    * Verktyget för Phoenix dataintegritet: Har stöd för verifiering av schema, index och andra metadata.


* **Problem med HBase**: Följande syntax kan resultera i ett fel när du kör en CSV-bulköverföring MapReduce-jobb.

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    Använd följande syntax i stället:

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>02/28/2017 - versionen av Spark 2.1 i HDInsight 3.6 (förhandsversion)
* [Spark 2.1](http://spark.apache.org/releases/spark-release-2-1-0.html) förbättrar många stabilitets- och användbarhetskorrigeringar problem med tidigare versioner. Det medför också nya funktioner i alla Spark-arbetsbelastningar, till exempel Spark Core, SQL, ML och strömning.
* Strukturerad direktuppspelning hämtar förbättrad skalbarhet med stöd för händelsen tid vattenstämplar och Kafka 0.10 connector.
* Spark SQL partitionering hanteras nu med nya mekanismen för skalbar hantering av Partition. Läs mer [här](http://spark.apache.org/releases/spark-release-2-1-0.html) om hur du uppgraderar.
* Spark 2.1 i Azure HDInsight 3.6 förhandsgranskning för närvarande stöder inte anslutning för BI-verktyg med hjälp av ODBC-drivrutinen.
* Azure Data Lake Store-åtkomst från Spark 2.1 kluster stöds inte i den här förhandsversionen.


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>11/18/2016 - versionen av Spark 2.0.1 på HDInsight 3.5
Spark 2.0.1 är nu tillgängligt på Spark-kluster (HDInsight version 3.5).

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>11/16/2016 - versionen av R Server 9.0 på HDInsight 3.5 (Spark 2.0)
*   R Server-kluster innehåller nu ett alternativ för två versioner: R Server 9.0 på HDI 3.5 (Spark 2.0) och R Server 8.0 på HDI 3.4 (Spark 1.6).
*   R Server 9.0 på HDI 3.5 (Spark 2.0) bygger på R 3.3.2 tillsammans med nya ScaleR-datakällafunktioner heter RxHiveData och RxParquetData för inläsning av data från Hive och Parquet direkt till Spark dataramar för analys av ScaleR. Mer information finns i infogat hjälp om dessa funktioner i R genom användning av den **? RxHiveData** och **? RxParquetData** kommandon.
*   RStudio Server community edition installeras som standard (med ett alternativ för välja bort) på bladet konfiguration av kluster som en del av etablering flödet.

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>11/09/2016 - versionen av Spark 2.0 på HDInsight
* Spark 2.0-kluster i HDInsight 3.5 har nu stöd för Livy och Jupyter-tjänster.

## <a name="10262016---release-of-r-server-on-hdinsight"></a>26/10/2016 - versionen av R Server på HDInsight
* URI för noden åtkomsten har ändrats till **clustername**-ed-ssh.azurehdinsight.net
* R Server på HDInsight klusteretablering har effektiviserats.
* R Server på HDInsight är nu tillgänglig som vanlig HDInsight ”R Server” typ av kluster och inte längre installeras som en separat HDInsight-program. Kantnoden och R Server-binärfiler har nu etablerats som en del av distributionen av R-Server. Detta förbättrar hastigheten och tillförlitligheten för etablering. Prismodellen för R Server uppdateras.
* R Server klusterpriset typ nu baserat på priset för Standard-nivån plus priset för R Server-tillägg. Den här ändringen påverkar inte gällande priser för R Server. ändras endast hur kostnaderna som visas på fakturan. Alla befintliga R Server-kluster fortsätter att fungera och Resource Manager-mallar som fortsätter att fungera tills utfasningsmeddelande. **Vi rekommenderar även om för att uppdatera dina skriptbaserade distributioner för att använda nya Resource Manager-mall.**






