---
title: Viktig information för Azure HDInsight
description: Senaste viktig information om Azure HDInsight. Hämta utvecklingstips och information för Hadoop, Spark, R Server, Hive och mycket mer.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 0beac64ceb0bbf729d2f6d0f6a0ca8d5af499f42
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64725426"
---
# <a name="release-notes-for-azure-hdinsight"></a>Viktig information för Azure HDInsight

Den här artikeln innehåller information om den **senaste** uppdateringar med Azure HDInsight. Information om tidigare versioner finns i [HDInsight Release Notes Arkiv](hdinsight-release-notes-archive.md).

> [!IMPORTANT]  
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight versionshantering artikeln](hdinsight-component-versioning.md).

## <a name="summary"></a>Sammanfattning

Azure HDInsight är en av de mest populära tjänsterna bland företagskunder för analys med Apache Hadoop med öppen källkod och Apache Spark på Azure.

## <a name="new-features"></a>Nya funktioner

Mer information om viktiga ändringar med HDInsight 4.0., se [vad är nytt i HDI 4.0?](../hdinsight/hdinsight-version-release.md).

## <a name="component-versions"></a>Komponent-versioner

De officiella Apache-versionerna av alla komponenter i HDInsight 4.0 anges nedan. Komponenterna i listan är versioner av de senaste stabila versionerna som är tillgängliga.

- Apache Ambari 2.7.1
- Apache Hadoop 3.1.1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1
- Apache Mahout 0.9.0+
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache Pig 0.16.0
- Apache Ranger 0.7.0
- Apache skjutreglaget 0.92.0
- Apache Spark 2.3.1
- Apache Sqoop 1.4.7
- Apache TEZ 0.9.1 till och
- Apache Zeppelin 0.8.0
- Apache ZooKeeper 3.4.6

Senare versioner av Apache komponenter paketeras ibland i HDP-distributionen förutom de versioner som anges ovan. I det här fallet visas i tabellen tekniska förhandsversioner dessa senare versioner och ersätta inte för Apache komponenten versioner av listan ovan i en produktionsmiljö.

## <a name="apache-patch-information"></a>Apache patch-information

Mer information om korrigeringarna som är tillgängliga i HDInsight 4.0 finns i patch lista för varje produkt i tabellen nedan.

| Produktnamn | Patch-information |
|---|---|
| Ambari | [Ambari patch-information](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Information för Hadoop-korrigering](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| HBase | [HBase patch-information](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | Den här versionen innehåller Hive 3.1.0 med inga ytterligare Apache-korrigeringar.  |
| Kafka | Den här versionen innehåller Kafka 1.1.1 med inga ytterligare Apache-korrigeringar. |
| Oozie | [Oozie patch-information](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Phoenix patch-information](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Pig patch-information](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Ranger patch-information](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Spark patch-information](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | Den här versionen innehåller Sqoop 1.4.7 med inga ytterligare Apache-korrigeringar. |
| Tez | Den här versionen innehåller Tez 0.9.1 till och med inga ytterligare Apache-korrigeringar. |
| Zeppelin | Den här versionen innehåller Zeppelin 0.8.0 med inga ytterligare Apache-korrigeringar. |
| Zookeeper | [Zookeeper patch-information](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>Fast Common Vulnerabilities and Exposures

Mer information om säkerhet problem har lösts i den här versionen, se Hortonworks [fast Common Vulnerabilities and Exposures för HDP 3.0.1](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html).

## <a name="known-issues"></a>Kända problem

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>Replikering är bruten för säker HBase med standardinstallation

För HDInsight 4.0, gör du följande steg:

1. Aktivera kommunikationen mellan kluster.
1. Logga in på den aktiva huvudnoden.
1. Hämta ett skript för att aktivera replikering med följande kommando:

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Skriv kommandot `sudo kinit <domainuser>`.
1. Skriv följande kommando för att köra skriptet:

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
För HDInsight 3.6, gör du följande:

1. Logga in på den aktiva HMaster ZK.
1. Hämta ett skript för att aktivera replikering med följande kommando:
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Skriv kommandot `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`.
1. Ange följande kommando:

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>Phoenix Sqlline slutar fungera när du har migrerat HBase-kluster i HDInsight 4.0

Utför följande steg:

1. Ta bort Phoenix i tabellerna nedan:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. Om du inte ta bort någon av tabellerna, startar du om HBase om du vill rensa alla anslutningar till tabellerna.
1. Kör `sqlline.py` igen. Phoenix återskapas alla tabeller som har tagits bort i steg 1.
1. Återskapa Phoenix tabeller och vyer för dina HBase-data.

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>Phoenix Sqlline upphör att fungera efter replikeras HBase Phoenix metadata från HDInsight 3.6 till 4.0

Utför följande steg:

1. Innan du gör replikeringen, gå till målklustret 4.0 och köra `sqlline.py`. Det här kommandot genererar Phoenix tabeller som `SYSTEM.MUTEX` och `SYSTEM.LOG` som endast finns i 4.0.
1. Ta bort följande tabeller:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. Starta replikering för HBase

## <a name="deprecation"></a>Utfasning

Apache Storm och ML-tjänster är inte tillgängliga i HDInsight 4.0.
