---
title: Portar som används av Hadoop-tjänster på HDInsight - Azure
description: Den här artikeln innehåller en lista över portar som används av Apache Hadoop-tjänster som körs i Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 67cafbb7934381cd4c2936d6e6dfe7fb19d70735
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314699"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>Portar som används av Apache Hadoop-tjänster på HDInsight

Det här dokumentet innehåller en lista över de portar som används av Apache Hadoop-tjänster som körs på HDInsight-kluster. Den innehåller också information om portar som används för att ansluta till klustret med hjälp av SSH.

## <a name="public-ports-vs-non-public-ports"></a>Offentliga portar jämfört med icke-offentliga portar

Linux-baserade HDInsight-kluster exponerar bara tre portar offentligt på internet. 22, 23 och 443. Dessa portar används för att komma åt klustret på ett säkert sätt med hjälp av SSH och tjänster som exponeras via det säkra HTTPS-protokollet.

Internt implementeras HDInsight av flera virtuella Azure-datorer (noderna i klustret) som körs på ett Virtuellt Azure-nätverk. Inifrån det virtuella nätverket kan du komma åt portar som inte exponeras via Internet. Om du till exempel ansluter till en av huvudnoderna med SSH kan du från huvudnoden sedan direkt komma åt tjänster som körs på klusternoderna.

> [!IMPORTANT]  
> Om du inte anger ett Virtuellt Azure-nätverk som ett konfigurationsalternativ för HDInsight skapas ett automatiskt. Du kan dock inte ansluta andra datorer (till exempel andra virtuella Azure-datorer eller klientutvecklingsdatorn) till det här virtuella nätverket.

Om du vill ansluta ytterligare datorer till det virtuella nätverket måste du först skapa det virtuella nätverket och sedan ange det när du skapar HDInsight-klustret. Mer information finns i [Planera ett virtuellt nätverk för HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="public-ports"></a>Offentliga hamnar

Alla noder i ett HDInsight-kluster finns i ett Virtuellt Azure-nätverk och kan inte nås direkt från Internet. En offentlig gateway ger internetåtkomst till följande portar, som är vanliga för alla HDInsight-klustertyper.

| Tjänst | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- |
| Sshd |22 |SSH |Ansluter klienter till sshd på den primära headnode. Mer information finns i [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Sshd |22 |SSH |Ansluter klienter till sshd på kantnoden. Mer information finns i [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Sshd |23 |SSH |Ansluter klienter till sshd på den sekundära headnode. Mer information finns i [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Ambari webbgränssnitt. Se [Hantera HDInsight med hjälp av Apache Ambari Webbgränssnitt](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Ambari REST API. Se [Hantera HDInsight med Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |HCatalog REST API. Se [Använd MapReduce med Curl](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Ansluter till Hive med ODBC. Se [Ansluta Excel till HDInsight med Microsoft ODBC-drivrutinen](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Ansluter till ApacheHive med JDBC. Se [Anslut till Apache Hive på HDInsight med hive JDBC-drivrutinen](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Följande är tillgängliga för specifika klustertyper:

| Tjänst | Port | Protokoll | Klustertyp | Beskrivning |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |HBase REST API. Se [Komma igång med Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |Spark REST API. Se [Skicka Apache Spark jobb på distans med Apache Livy](spark/apache-spark-livy-rest-interface.md) |
| Spark Sparsamhet server |443 |HTTPS |Spark |Spark Sparsamhet server som används för att skicka Hive frågor. Se [Använda beeline med Apache Hive på HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Storm webbgränssnitt. Se [Distribuera och hantera Apache Storm-topologier på HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) |
| Kafka Vila proxy |443 |HTTPS |Kafka |Kafka REST API. Se [Interagera med Apache Kafka-kluster i Azure HDInsight med hjälp av en REST-proxy](kafka/rest-proxy.md) |

### <a name="authentication"></a>Autentisering

Alla tjänster som exponeras offentligt på internet måste autentiseras:

| Port | Autentiseringsuppgifter |
| --- | --- |
| 22 eller 23 |De SSH-användarautentiseringsuppgifter som angetts när klustret skapades |
| 443 |Inloggningsnamnet (standard: admin) och lösenord som angavs när klustret skapades |

## <a name="non-public-ports"></a>Icke-offentliga portar

> [!NOTE]  
> Vissa tjänster är endast tillgängliga på specifika klustertyper. HBase är till exempel bara tillgängligt på HBase-klustertyper.

> [!IMPORTANT]  
> Vissa tjänster körs bara på en headnode i taget. Om du försöker ansluta till tjänsten på den primära headnoden och får ett felmeddelande försöker du igen med den sekundära headnoden.

### <a name="ambari"></a>Ambari

| Tjänst | Noder | Port | URL-sökväg | Protokoll |
| --- | --- | --- | --- | --- |
| Ambari webbgränssnitt | Huvudnoder | 8080 | / | HTTP |
| Ambari REST API | Huvudnoder | 8080 | /api/v1 | HTTP |

Exempel:

* Ambari REST API:`curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>HDFS-portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| Webbgränssnittet NameNode |Huvudnoder |30070 |HTTPS |Webbgränssnitt för att visa status |
| NameNode metadata tjänst |huvudnoder |8020 |Ipc |Metadata för filsystemet |
| DataNod |Alla arbetsnoder |30075 |HTTPS |Webbgränssnitt för att visa status, loggar, etc. |
| DataNod |Alla arbetsnoder |30010 |&nbsp; |Dataöverföring |
| DataNod |Alla arbetsnoder |30020 |Ipc |Åtgärder för metadata |
| Sekundärt namnNod |Huvudnoder |50090 |HTTP |Kontrollpunkt för NameNode-metadata |

### <a name="yarn-ports"></a>YARN-portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| Webbgränssnittet i Resource Manager |Huvudnoder |8088 |HTTP |Webbgränssnitt för Resurshanteraren |
| Webbgränssnittet i Resource Manager |Huvudnoder |8090 |HTTPS |Webbgränssnitt för Resurshanteraren |
| Administrationsgränssnitt för Resurshanteraren |huvudnoder |8141 |Ipc |För ansökan inlagor (Hive, Hive server, Pig, etc.) |
| Schemaläggaren för Resurshanteraren |huvudnoder |8030 |HTTP |Administrativt gränssnitt |
| Programgränssnittet för Resource Manager |huvudnoder |8050 |HTTP |Adress till gränssnittet för programhanteraren |
| NodeManager |Alla arbetsnoder |30050 |&nbsp; |Adressen till containerhanteraren |
| Webbgränssnittet NodeManager |Alla arbetsnoder |30060 |HTTP |Resurshanterarens gränssnitt |
| Tidslinjeadress |Huvudnoder |10200 |RPC |Tjänsten Tidslinjetjänsten RPC. |
| Webbgränssnittet för tidslinje |Huvudnoder |8188 |HTTP |Webbgränssnittet för tidslinjetjänsten |

### <a name="hive-ports"></a>Hive-portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| HiveServer2 |Huvudnoder |10001 |Sparsamhet |Tjänst för anslutning till Hive (Sparsamhet/JDBC) |
| Hive-metaarkiv |Huvudnoder |9083 |Sparsamhet |Tjänst för anslutning till Hive-metadata (Sparsamhet/JDBC) |

### <a name="webhcat-ports"></a>WebHCat-portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| WebHCat-server |Huvudnoder |30111 |HTTP |Webb-API ovanpå HCatalog och andra Hadoop-tjänster |

### <a name="mapreduce-ports"></a>MapReduce-portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| JobbHistoria |Huvudnoder |19888 |HTTP |MapReduce JobHistory webb UI |
| JobbHistoria |Huvudnoder |10020 |&nbsp; |MapReduce JobHistory server |
| ShuffleHandler (shuffleHandler) |&nbsp; |13562 |&nbsp; |Överför mellanliggande kartutdata till begäran om reducerare |

### <a name="oozie"></a>Oozie

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| Oozie server |Huvudnoder |11000 |HTTP |URL för Oozie-tjänsten |
| Oozie server |Huvudnoder |11001 |HTTP |Port för Oozie admin |

### <a name="ambari-metrics"></a>Ambari-mått

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| Tidslinje (programhistorik) |Huvudnoder |6188 |HTTP |Webbgränssnittet för TimeLine-tjänsten |
| Tidslinje (programhistorik) |Huvudnoder |30200 |RPC |Webbgränssnittet för TimeLine-tjänsten |

### <a name="hbase-ports"></a>HBase-portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| HMaster (HMaster) |Huvudnoder |16000 |&nbsp; |&nbsp; |
| Webbgränssnitt för HMaster-information |Huvudnoder |16010 |HTTP |Porten för webbgränssnittet för HBase Master |
| Regionserver |Alla arbetsnoder |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |Porten som klienter använder för att ansluta till ZooKeeper |

### <a name="kafka-ports"></a>Kafka-portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| Mäklare |Arbetsnoder |9092 |[Kafka Tråd Protokoll](https://kafka.apache.org/protocol.html) |Används för klientkommunikation |
| &nbsp; |Zookeeper noder |2181 |&nbsp; |Porten som klienter använder för att ansluta till Zookeeper |
| REST-proxy | Kafka-hanteringsnoder |9400 |HTTPS |[Kafka REST-specifikation](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/) |

### <a name="spark-ports"></a>Spark-portar

| Tjänst | Noder | Port | Protokoll | URL-sökväg | Beskrivning |
| --- | --- | --- | --- | --- | --- |
| Spark Sparsamhet servrar |Huvudnoder |10002 |Sparsamhet | &nbsp; | Tjänst för anslutning till Spark SQL (Sparsamhet/JDBC) |
| Livy server | Huvudnoder | 8998 | HTTP | &nbsp; | Tjänst för att köra satser, jobb och program |
| Jupyter Notebook | Huvudnoder | 8001 | HTTP | &nbsp; | Jupyter anteckningsbok webbplats |

Exempel:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. I det `10.0.0.11` här exemplet är IP-adressen för headnode som är värd för Livy-tjänsten.
