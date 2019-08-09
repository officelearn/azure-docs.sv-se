---
title: Viktig information för Azure HDInsight
description: Senaste viktig information för Azure HDInsight. Få utvecklings tips och information för Hadoop, Spark, R Server, Hive med mera.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: b6e9a340a1fdcbe3ee24b8c81d171ade04c63139
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880026"
---
# <a name="release-notes-for-azure-hdinsight"></a>Viktig information för Azure HDInsight

Den här artikeln innehåller information om de **senaste** versionerna av Azure HDInsight-utgåvor. Information om tidigare versioner finns i avsnittet om [versions anmärkningar för HDInsight](hdinsight-release-notes-archive.md).

> [!IMPORTANT]  
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [artikeln om versions hantering i HDInsight](hdinsight-component-versioning.md).

## <a name="summary"></a>Sammanfattning

Azure HDInsight är en av de mest populära tjänsterna bland företagskunder för analys med Apache Hadoop med öppen källkod och Apache Spark på Azure.

## <a name="new-features"></a>Nya funktioner

Mer information om viktiga ändringar med HDInsight 4,0 finns i [Nyheter i HDI 4,0?](../hdinsight/hdinsight-version-release.md).

## <a name="component-versions"></a>Komponent versioner

De officiella Apache-versionerna av alla HDInsight 4,0-komponenter anges nedan. De komponenter som visas är versioner av de senaste säkra versionerna som är tillgängliga.

- Apache Ambari 2.7.1
- Apache Hadoop 3.1.1
- Apache HBase-2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1, 2.1.0
- Apache Mahout 0.9.0 +
- Apache Oozie-4.2.0
- Apache Phoenix 4.7.0
- Apache gris-0.16.0
- Apache Ranger 0.7.0
- Apache-skjutreglage 0.92.0
- Apache Spark 2.3.1, 2.4.0
- Apache Sqoop-1.4.7
- Apache TEZ-0.9.1 till och
- Apache Zeppelin-0.8.0
- Apache ZooKeeper 3.4.6

Senare versioner av Apache-komponenter paketeras ibland i HDP-distributionen utöver de versioner som anges ovan. I det här fallet visas dessa senare versioner i den tekniska för hands versions tabellen och bör inte ersätta Apache-komponentens versioner av listan ovan i en produktions miljö.

## <a name="apache-patch-information"></a>Apache-patch-information

Mer information om korrigeringsfiler som är tillgängliga i HDInsight 4,0 finns i korrigerings listan för varje produkt i tabellen nedan.

| Produktnamn | Uppdaterings information |
|---|---|
| Ambari | [Information om Ambari-korrigering](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Information om Hadoop-korrigering](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| HBase | [Information om HBase-korrigering](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | Den här versionen tillhandahåller Hive-3.1.0 utan ytterligare Apache-korrigeringsfiler.  |
| Kafka | Den här versionen innehåller Kafka 1.1.1 utan ytterligare Apache-korrigeringsfiler. |
| Oozie | [Information om Oozie-korrigering](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Uppdaterings information för Phoenix](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Information om gris-korrigering](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Information om Ranger-korrigering](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Uppdaterings information för Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | Den här versionen innehåller Sqoop-1.4.7 utan ytterligare Apache-korrigeringsfiler. |
| Tez | Den här versionen innehåller Tez-0.9.1 till och utan ytterligare Apache-korrigeringsfiler. |
| Zeppelin | Den här versionen innehåller Zeppelin-0.8.0 utan ytterligare Apache-korrigeringsfiler. |
| Zookeeper | [Information om Zookeeper-korrigering](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>Fasta vanliga sårbarheter och exponeringar

Mer information om säkerhets problem som har lösts i den här versionen finns i Hortonworks " [fasta vanliga sårbarheter och exponeringar för HDP 3.0.1](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html).

## <a name="known-issues"></a>Kända problem

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>Replikeringen är bruten för säker HBase med standard installationen

För HDInsight 4,0 utför du följande steg:

1. Aktivera kommunikation mellan kluster.
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
För HDInsight 3,6 gör du följande:

1. Logga in på Active HMaster ZK.
1. Hämta ett skript för att aktivera replikering med följande kommando:
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Skriv kommandot `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`.
1. Ange följande kommando:

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>Phoenix-sqlline slutar fungera efter migrering av HBase-kluster till HDInsight 4,0

Gör så här:

1. Ta bort följande Phoenix-tabeller:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. Om du inte kan ta bort någon av tabellerna måste du starta om HBase för att ta bort alla anslutningar till tabellerna.
1. Kör `sqlline.py` igen. Phoenix kommer att återskapa alla tabeller som togs bort i steg 1.
1. Återskapa Phoenix-tabeller och vyer för dina HBase-data.

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>Phoenix-sqlline slutar fungera efter replikering av HBase Phoenix-metadata från HDInsight 3,6 till 4,0

Gör så här:

1. Innan du utför replikeringen går du till mål 4,0-klustret och `sqlline.py`kör. Med det här kommandot skapas Phoenix- `SYSTEM.MUTEX` tabeller `SYSTEM.LOG` som och som bara finns i 4,0.
1. Ta bort följande tabeller:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. Starta HBase-replikeringen

## <a name="deprecation"></a>Utfasning

Apache Storm-och ML-tjänster är inte tillgängliga i HDInsight 4,0.
