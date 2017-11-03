---
title: "Viktig information för Hadoop-komponenter på Azure HDInsight | Microsoft Docs"
description: "Senaste viktig information och versioner av Hadoop-komponenter för Azure HDInsight. Hämta development tips och information för Spark, R Server, Hive och mycket mer."
services: hdinsight
documentationcenter: 
editor: cgronlun
manager: jhubbard
author: nitinme
tags: azure-portal
ms.assetid: a363e5f6-dd75-476a-87fa-46beb480c1fe
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: nitinme
ms.openlocfilehash: 96b2b4976729da5b7d8b75909dbe099090240c08
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Viktig information för Hadoop-komponenter på Azure HDInsight

Den här artikeln innehåller information om den **senaste** uppdateringar med Azure HDInsight. Mer information om tidigare versioner finns [HDInsight Release Notes Arkiv](hdinsight-release-notes-archive.md).

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight versionshantering artikel](hdinsight-component-versioning.md).


## <a name="notes-for-08012017-release-of-hdinsight"></a>Information om 2017-08/01 versionen av HDInsight

| Rubrik | Beskrivning | Område som påverkas  | Typ av kluster  | 
| --- | --- | --- | --- | --- |
| Version av Microsoft R Server 9.1 i HDInsight |HDInsight stöder nu etablering R Server 9.1 kluster i HDInsight. Mer information om Microsoft R Server 9.1 versionen finns [bloggen](https://blogs.technet.microsoft.com/dataplatforminsider/2017/04/19/introducing-microsoft-r-server-9-1-release/). |Tjänst |R-server |
| HDInsight 3,6 innehåller nu nyare versioner av Hadoop-stacken|<ul><li>En detaljerad lista över uppdaterade versioner finns [Hadoop komponent-versioner som är tillgängliga i HDInsight](hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions).</li><li>En lista över programfel som har korrigerats i de senaste versionerna av Hadoop-stacken, se [Apache Korrigeringsinformation](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/patch_parent.html).</li><li>En lista över bryta ändringar mellan HDP 2.6.1 (vilket är nu tillgängligt i HDInsight 3,6), se [https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html).</li><li>En lista över kända problem i HDP 2.6.1 finns [kända problem](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/known_issues.html).</li></ul> |Tjänst |Alla |Saknas |
| Uppdateringar till interaktiva Hive (förhandsgranskning)-kluster |<ul><li><b>Funktionen förbättring.</b> Implementering av cachelagrade metastore som minskar belastningen på serverdelen SQL genom cachelagring av metadata och förbättrar prestandan för alla metadataåtgärder.  Denna förbättring är nu en standard på alla interaktiva Hive-kluster. Mer information finns i [https://issues.apache.org/jira/browse/HIVE-16520](https://issues.apache.org/jira/browse/HIVE-16520).</li><li><b>Funktionen förbättring.</b> Läser in dynamiska partitionen är optimerad. Mer information finns i [https://issues.apache.org/jira/browse/HIVE-14204] (https://issues.apache.org/jira/browse/HIVE-14204).</li><li><b>Funktionen förbättring.</b> Konfiguration av optimeringar för HDInsight på Linux.</li><li><b>Buggfix.</b> `CredentialProviderFactory$getProviders`är inte trådsäker. Det här problemet löses nu. Mer information finns i [https://issues.apache.org/jira/browse/HADOOP-14195](https://issues.apache.org/jira/browse/HADOOP-14195).</li><li><b>Buggfix.</b> Hög CPU-användning med WASB drivrutinen `liststatus` API, vilket resulterar i felaktiga ATS prestanda. Det här problemet löses nu. Mer information finns i [https://github.com/Azure/azure-storage-java/pull/154](https://github.com/Azure/azure-storage-java/pull/154).</li></ul> |Tjänst |Interaktiva Hive (förhandsgranskning) |
| Uppdateringar av Hadoop-kluster |Ökad tillförlitlighet för Templeton jobbet igen. Mer information finns i [https://issues.apache.org/jira/browse/HIVE-15947](https://issues.apache.org/jira/browse/HIVE-15947) |Tjänst |Hadoop |
| YARN uppdateringar | HDInsight skapar nu en 250 GB Ambari databas (utan ökar kostnaden), vilket resulterar i en bättre upplevelse för kunder. Den här ändringen ska förhindrar att ATS komma fullt och förmodligen få en bättre prestanda. |Tjänst |Alla |
| Spark-uppdateringar | Versionen av Spark 2.1.1. Mer information finns i [Spark versionen 2.1.1](https://spark.apache.org/releases/spark-release-2-1-1.html). | Tjänst | Spark |

  



## <a name="04062017---general-availability-of-hdinsight-36"></a>06/04/2017 - allmän tillgång till HDInsight 3,6

* Med den här versionen lägger Azure HDInsight version 3,6, som baseras på HDP 2.6. HDP 2.6 viktig information som är tillgängliga [här](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) och du hittar mer information om HDInsight-versioner [här](hdinsight-component-versioning.md). HDInsight 3,6 är tillgänglig för följande arbetsbelastningar:

    * Hadoop v2.7.3
    * HBase v1.1.2
    * Storm v1.1.0
    * Spark v2.1.0
    * Interaktiva Hive v2.1.0

* **Stöd för Hive-vyn 2.0**. Detta förbättrar användarupplevelsen för interaktiva Hive. Mer information finns i [Hortonworks dokumentationen](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html).

* **Prestandaförbättringar med Hive LLAP**. Mer information finns i [Hortonworks dokumentationen](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/).

* **Nya funktioner i Hive**. Se [Hortonworks dokumentationen](https://hortonworks.com/apache/hive/#section_4).

* **Hive CLI utfasningen**: Hive CLI är inaktuell och kunder uppmuntras att använda Beeline i stället. Mer information finns i [Apache-dokumentationen](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline). Instruktioner om hur du använder Beeline med HDInsight finns [använder Beeline med HDInsight Hadoop-kluster](hdinsight-hadoop-use-hive-beeline.md).

* **Nya funktioner i Apache Phoenix och HBase**.
    * Kvot för Lagringsstöd: ofta används i miljöer med flera innehavare så att begränsat lagringsutrymme på en per tabell och namnområdesnivån.
    * Phoenix indexering förbättringar: inkrementell indexering och återskapa/Fortsätt indexering från föregående fel.
    * Phoenix integritet verktyg: har stöd för validering av schemat, index och andra metadata.


* **Problem med HBase**: när du kör en CSV samtidigt överför MapReduce-jobb, följande syntax kan resultera i ett fel.

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    Använd följande syntax i stället:

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>02/28/2017 - versionen av Spark 2.1 i HDInsight 3,6 (förhandsgranskning)
* [Spark 2.1](http://spark.apache.org/releases/spark-release-2-1-0.html) förbättrar många stabilitet och användbarhet problem med tidigare versioner. Medför det också nya funktioner över alla Spark-arbetsbelastningar, t.ex Spark Core, SQL, ML och Streaming.
* Strukturerade Streaming hämtar förbättrad skalbarhet med stöd för händelsen tid vattenstämplar och Kafka 0.10 connector.
* Spark SQL partitionering nu hanteras med hjälp av ny mekanism för hantering av skalbara Partition. Se mer information [här](http://spark.apache.org/releases/spark-release-2-1-0.html) om hur du uppgraderar.
* 2.1 Spark på Azure HDInsight 3,6 Preview för tillfället stöder inte BI verktyget anslutning med hjälp av ODBC-drivrutinen.
* Azure Data Lake Store-åtkomst från 2.1 Spark-kluster stöds inte i den här förhandsgranskningen.


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>18/11/2016 - versionen av Spark 2.0.1 i HDInsight 3.5
Spark 2.0.1 är nu tillgänglig på Spark-kluster (HDInsight version 3.5).

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>11/16/2016 - versionen av R Server 9.0 på HDInsight 3.5 (Spark 2.0)
*   R Server-kluster innehåller nu ett alternativ för två versioner: R Server 9.0 på HDI 3.5 (Spark 2.0) och R Server 8.0 på HDI 3.4 (Spark 1.6).
*   R Server 9.0 på HDI 3.5 (Spark 2.0) bygger på R 3.3.2 och innehåller nya ScaleR datakälla funktionerna som kallas RxHiveData och RxParquetData för att läsa in data från Hive och parkettgolv direkt till Spark DataFrames för analys av ScaleR. Mer information finns i infogat hjälp om dessa funktioner i R med hjälp av den **? RxHiveData** och **? RxParquetData** kommandon.
*   RStudio Server community edition installeras nu normalt (med ett alternativ för välja bort) i bladet klusterkonfigurationen som en del av etablering flödet.

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>11/09/2016 - versionen av 2.0 Spark i HDInsight
* 2.0 Spark-kluster i HDInsight 3.5 stöder nu Livius och Jupyter-tjänster.

## <a name="10262016---release-of-r-server-on-hdinsight"></a>26/10/2016 - versionen av R Server på HDInsight
* URI för noden åtkomsten har ändrats till **klusternamn**-ed-ssh.azurehdinsight.net
* R Server på HDInsight klusteretablering har effektiviserats.
* R Server på HDInsight är nu tillgängligt som regelbundet HDInsight ”R Server” cluster typ och inte längre installeras som en separat HDInsight-program. Kantnod och R Server binärfiler tillhandahålls nu som en del av Klusterdistribution R Server. Detta förbättrar hastighet och tillförlitlighet för etablering. Priserna för R Server uppdateras.
* R Server klusterpriset typen nu baserat på standardnivån pris plus R Server tillägg pris. Premium-nivån är reserverat för Premium-funktioner som är tillgängliga för olika klustertyper och används inte för typ av R Server-kluster. Den här ändringen påverkar inte gällande priser av R-Server. ändras endast hur avgifterna visas i växeln. Alla befintliga R Server-kluster fortsätter att fungera och Resource Manager-mallar fortsätter att fungera tills utfasningen meddelande. **Det rekommenderas även om du vill uppdatera dina skriptbaserade distributioner om du vill använda nya Resource Manager-mall.**






