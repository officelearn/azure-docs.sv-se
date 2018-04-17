---
title: Portar som används av Hadoop på HDInsight - Azure-tjänster | Microsoft Docs
description: En lista över portar som används av Hadoop-tjänster som körs på HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd14aed9-ec25-4bb3-a20c-e29562735a7d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: larryfr
ms.openlocfilehash: b6510024d1644f1e0b357126e2b971c66eb95dbc
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="ports-used-by-hadoop-services-on-hdinsight"></a>Portar som används av Hadoop-tjänster på HDInsight

Det här dokumentet innehåller en lista över portar som används av Hadoop-tjänster som körs på Linux-baserade HDInsight-kluster. Det innehåller även information om portar som används för att ansluta till klustret med SSH.

## <a name="public-ports-vs-non-public-ports"></a>Offentliga portar jämfört med icke-offentliga portar

Linux-baserade HDInsight-kluster exponera endast tre portar offentligt på internet. 22, 23 och 443. Dessa portar används för säker åtkomst till klustret med SSH och tjänster som exponeras via säker HTTPS-protokollet.

Internt HDInsight implementeras av flera virtuella datorer i Azure (noder i klustret) körs på ett Azure Virtual Network. I det virtuella nätverket tillgång från portarna som exponeras inte via internet. Till exempel om du ansluter till en huvudnoderna med SSH från huvudnod du kan sedan direkt åtkomst till tjänster som körs på klusternoderna.

> [!IMPORTANT]
> Om du inte anger ett virtuellt Azure-nätverk som ett alternativ för HDInsight, skapas en automatiskt. Du kan dock ansluta andra datorer (till exempel andra Azure-datorer eller utvecklingsdatorn klienten) till det här virtuella nätverket.


Om du vill koppla ytterligare datorer till det virtuella nätverket måste du först skapa det virtuella nätverket och ange den när du skapar ditt HDInsight-kluster. Mer information finns i [utöka HDInsight funktioner med hjälp av ett virtuellt Azure-nätverk](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>Offentliga portar

Alla noder i ett HDInsight-kluster finns i ett virtuellt Azure-nätverk och kan inte användas direkt från internet. En offentlig gateway ger internet-åtkomst till följande portar som är gemensamma för alla typer av HDInsight-kluster.

| Tjänst | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| sshd |22 |SSH |Ansluter klienter till sshd på den primära headnode. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight). |
| sshd |22 |SSH |Ansluter klienter till sshd på kantnoden. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight). |
| sshd |23 |SSH |Ansluter klienter till sshd på den sekundära headnode. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight). |
| Ambari |443 |HTTPS |Ambari-webbgränssnittet. Se [hantera HDInsight med Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Ambari REST API. Se [hantera HDInsight med Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |HCatalog REST API. Se [använda Hive med Curl](hadoop/apache-hadoop-use-pig-curl.md), [använda Pig med Curl](hadoop/apache-hadoop-use-pig-curl.md), [använder MapReduce med Curl](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Ansluter till Hive med ODBC. Se [ansluta Excel till HDInsight med Microsoft ODBC driver](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Ansluter till Hive med JDBC. Se [Anslut till Hive i HDInsight med hjälp av Hive JDBC-drivrutinen](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Följande är tillgängliga för specifika klustertyper:

| Tjänst | Port | Protokoll | Klustertyp | Beskrivning |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |HBase REST-API. Se [komma igång med HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |Spark REST API. Se [skicka Spark jobb via fjärranslutning med Livy](spark/apache-spark-livy-rest-interface.md) |
| Storm |443 |HTTPS |Storm |Storm-webbgränssnittet. Se [distribuera och hantera Storm-topologier på HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) |

### <a name="authentication"></a>Autentisering

Alla tjänster som är offentligt visas på internet måste autentiseras:

| Port | Autentiseringsuppgifter |
| --- | --- |
| 22 eller 23 |SSH-autentiseringsuppgifter som anges när klustret skapas |
| 443 |Inloggningsnamnet (standard: admin) och lösenordet som angavs när klustret skapas |

## <a name="non-public-ports"></a>Icke-offentligt portar

> [!NOTE]
> Vissa tjänster är bara tillgängliga på specifika klustertyper. HBase är till exempel bara tillgänglig på HBase-klustertyper.

> [!IMPORTANT]
> Vissa tjänster bara köras på en headnode i taget. Om du försöker ansluta till tjänsten på den primära headnode och får ett felmeddelande, kan du försöka använda sekundära headnode.

### <a name="ambari"></a>Ambari

| Tjänst | Noder | Port | URL-sökväg | Protokoll | 
| --- | --- | --- | --- | --- |
| Ambari-webbgränssnittet | HEAD-noder | 8080 | / | HTTP |
| Ambari REST API | HEAD-noder | 8080 | / api/v1 | HTTP |

Exempel:

* Ambari REST API: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>HDFS-portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| NameNode webbgränssnittet |HEAD-noder |30070 |HTTPS |Webbgränssnittet att visa status |
| NameNode metadata service |HEAD-noder |8020 |IPC |Filmetadata system |
| DataNode |Alla arbetarnoder |30075 |HTTPS |Webbgränssnittet att visa status, loggar, osv. |
| DataNode |Alla arbetarnoder |30010 |&nbsp; |Dataöverföring |
| DataNode |Alla arbetarnoder |30020 |IPC |Åtgärder för metadata |
| Sekundär NameNode |HEAD-noder |50090 |HTTP |Kontrollpunkt för NameNode metadata |

### <a name="yarn-ports"></a>YARN-portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| Hanteraren för filserverresurser webbgränssnittet |HEAD-noder |8088 |HTTP |Webbgränssnittet för Resource Manager |
| Hanteraren för filserverresurser webbgränssnittet |HEAD-noder |8090 |HTTPS |Webbgränssnittet för Resource Manager |
| Hanteraren för filserverresurser administratörsgränssnittet |HEAD-noder |8141 |IPC |För program bidrag (Hive, Pig, Hive server osv.) |
| Hanteraren för filserverresurser Schemaläggaren |HEAD-noder |8030 |HTTP |Administrativt gränssnitt |
| Programgränssnittet för Resource Manager |HEAD-noder |8050 |HTTP |Adressen för program manager-gränssnittet |
| NodeManager |Alla arbetarnoder |30050 |&nbsp; |Adressen för hanteraren för behållaren |
| NodeManager webbgränssnittet |Alla arbetarnoder |30060 |HTTP |Resource manager-gränssnittet |
| Tidslinjen adress |HEAD-noder |10200 |RPC |Tidslinjen service RPC-tjänsten. |
| Tidslinjen webbgränssnittet |HEAD-noder |8181 |HTTP |Tidslinjen service webbgränssnittet |

### <a name="hive-ports"></a>Hive-portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| HiveServer2 |HEAD-noder |10001 |Thrift |Tjänsten för att ansluta till registreringsdatafilen (Thrift/JDBC) |
| Hive Metastore |HEAD-noder |9083 |Thrift |Tjänsten för att ansluta till Hive-metadata (Thrift/JDBC) |

### <a name="webhcat-ports"></a>WebHCat-portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| WebHCat-server |HEAD-noder |30111 |HTTP |Webb-API ovanpå HCatalog och andra Hadoop-tjänster |

### <a name="mapreduce-ports"></a>MapReduce-portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| Jobbhistorik |HEAD-noder |19888 |HTTP |MapReduce JobHistory webbgränssnittet |
| Jobbhistorik |HEAD-noder |10020 |&nbsp; |MapReduce JobHistory server |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Överföringar mellanliggande kartan matar ut till begär förminskningsapparater |

### <a name="oozie"></a>Oozie

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| Oozie-server |HEAD-noder |11000 |HTTP |URL för Oozie-tjänsten |
| Oozie-server |HEAD-noder |11001 |HTTP |Port för Oozie admin |

### <a name="ambari-metrics"></a>Ambari mått

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| Tidslinjen (programmet historik) |HEAD-noder |6188 |HTTP |Tidslinjen service webbgränssnittet |
| Tidslinjen (programmet historik) |HEAD-noder |30200 |RPC |Tidslinjen service webbgränssnittet |

### <a name="hbase-ports"></a>HBase-portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| HMaster |HEAD-noder |16000 |&nbsp; |&nbsp; |
| HMaster info Webbgränssnittet |HEAD-noder |16010 |HTTP |Porten för HBase Master webbgränssnittet |
| Region server |Alla arbetarnoder |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |Den port som klienter använder för att ansluta till ZooKeeper |

### <a name="kafka-ports"></a>Kafka portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| Service Broker |Arbetsnoder |9092 |[Kafka-protokollet](http://kafka.apache.org/protocol.html) |Används för klientkommunikation |
| &nbsp; |Zookeeper-noder |2181 |&nbsp; |Den port som klienter använder för att ansluta till Zookeeper |

### <a name="spark-ports"></a>Spark-portar

| Tjänst | Noder | Port | Protokoll | URL-sökväg | Beskrivning |
| --- | --- | --- | --- | --- | --- |
| Spark Thrift-servrar |HEAD-noder |10002 |Thrift | &nbsp; | Tjänsten för att ansluta till Spark SQL (Thrift/JDBC) |
| Livius server | HEAD-noder | 8998 | HTTP | &nbsp; | Tjänsten för att köra instruktioner, jobb och program |
| Jupyter-anteckningsbok | HEAD-noder | 8001 | HTTP | &nbsp; | Webbplats för Jupyter-anteckningsbok |

Exempel:

* Livius: `curl -u admin -G "http://10.0.0.11:8998/"`. I det här exemplet `10.0.0.11` är headnode som är värd för tjänsten Livius IP-adress.
