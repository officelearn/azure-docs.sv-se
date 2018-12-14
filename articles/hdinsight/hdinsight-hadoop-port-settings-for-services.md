---
title: Portar som används av Hadoop-tjänster på HDInsight - Azure
description: En lista över portar som används av Hadoop-tjänster som körs på HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 22e15f58f3d4e7f4db3ac3bd519dbb286a36ef95
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384147"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>Portar som används av Apache Hadoop-tjänster på HDInsight

Det här dokumentet innehåller en lista över portar som används av Apache Hadoop-tjänster som körs på Linux-baserade HDInsight-kluster. Det innehåller även information om portar som används för att ansluta till klustret med SSH.

## <a name="public-ports-vs-non-public-ports"></a>Offentliga portar jämfört med icke-offentlig portar

Linux-baserade HDInsight-kluster exponera bara tre portar offentligt på internet. 22, 23 och 443. Dessa portar används för att få säker åtkomst till klustret med SSH och tjänster som exponeras via en säker HTTPS-protokollet.

Internt HDInsight implementeras av flera Azure virtuella datorer (noder i klustret) som körs på Azure Virtual Network. I det virtuella nätverket tillgång från portarna som exponeras inte via internet. Till exempel om du ansluter till en av huvudnoderna med hjälp av SSH från klustrets huvudnod du kan sedan direkt åtkomst till tjänster som körs på klusternoderna.

> [!IMPORTANT]  
> Om du inte anger ett virtuellt Azure nätverk som ett alternativ för HDInsight, skapas en automatiskt. Men du kan inte ansluta till andra datorer (till exempel andra Azure-datorer eller utvecklingsdatorn klienten) till den här virtuella nätverket.


För att ansluta till fler datorer till det virtuella nätverket måste du först skapa det virtuella nätverket och ange den när du skapar HDInsight-kluster. Mer information finns i [utöka HDInsight-funktioner med hjälp av Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>Offentliga portar

Alla noder i ett HDInsight-kluster finns i Azure Virtual Network och kan inte nås direkt från internet. En offentlig gateway ger internet-åtkomst till följande portar som är gemensamma för alla typer av HDInsight-kluster.

| Tjänst | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- |
| sshd |22 |SSH |Ansluter klienter till sshd på den primära huvudnoden. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight). |
| sshd |22 |SSH |Ansluter klienter till sshd på gränsnoden. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight). |
| sshd |23 |SSH |Ansluter klienter till sshd på den sekundära huvudnoden. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight). |
| Ambari |443 |HTTPS |Ambari-webbgränssnittet. Se [hantera HDInsight med hjälp av Apache Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Ambari REST API. Se [hantera HDInsight med hjälp av Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |HCatalog REST-API. Se [använda Apache Hive med Curl](hadoop/apache-hadoop-use-pig-curl.md), [använda Apache Pig med Curl](hadoop/apache-hadoop-use-pig-curl.md), [använda MapReduce med Curl](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Ansluter till Hive med ODBC. Se [Anslut Excel till HDInsight med Microsoft ODBC driver](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Ansluter till ApacheHive med JDBC. Se [Anslut till Apache Hive på HDInsight med Hive JDBC-drivrutinen](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Följande är tillgängliga för specifika klustertyper:

| Tjänst | Port | Protokoll | Klustertyp | Beskrivning |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |HBase REST-API. Se [komma igång med Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |Spark REST-API. Se [skicka Apache Spark-jobb via fjärranslutning med Apache Livy](spark/apache-spark-livy-rest-interface.md) |
| Spark Thrift-server |443 |HTTPS |Spark |Spark Thrift-server som används för att skicka Hive-frågor. Se [använda Beeline med Apache Hive på HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Storm-Webbgränssnittet. Se [distribuera och hantera Apache Storm-topologier i HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) |

### <a name="authentication"></a>Autentisering

Alla tjänster som exponeras offentligt på internet måste autentiseras:

| Port | Autentiseringsuppgifter |
| --- | --- |
| 22 eller 23 |SSH-autentiseringsuppgifterna som anges när klustret skapas |
| 443 |Inloggningsnamnet (standard: administratör) och lösenordet som angavs när klustret skapas |

## <a name="non-public-ports"></a>Icke-offentlig portar

> [!NOTE]  
> Vissa tjänster är bara tillgängliga på specifika klustertyper. Till exempel är HBase bara tillgängligt på HBase klustertyper.

> [!IMPORTANT]  
> Vissa tjänster som bara köras på en huvudnod i taget. Om du försöker ansluta till tjänsten på den primära huvudnoden och får ett felmeddelande, försöker du med hjälp av den sekundära huvudnoden.

### <a name="ambari"></a>Ambari

| Tjänst | Noder | Port | URL-sökväg | Protokoll | 
| --- | --- | --- | --- | --- |
| Ambari-webbgränssnittet | Huvudnoder | 8080 | / | HTTP |
| Ambari REST API | Huvudnoder | 8080 | / api/v1 | HTTP |

Exempel:

* Ambari REST API: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>HDFS-portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| NameNode webbgränssnittet |Huvudnoder |30070 |HTTPS |Webbgränssnittet för att visa status |
| NameNode metadatatjänst |Huvudnoder |8020 |IPC |Metadata för fil-system |
| DataNode |Alla arbetsnoder |30075 |HTTPS |Webbgränssnittet att visa status, loggar, osv. |
| DataNode |Alla arbetsnoder |30010 |&nbsp; |Dataöverföring |
| DataNode |Alla arbetsnoder |30020 |IPC |Åtgärder för metadata |
| Sekundär NameNode |Huvudnoder |50090 |HTTP |Kontrollpunkt för NameNode metadata |

### <a name="yarn-ports"></a>YARN-portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| Resource Manager-Webbgränssnittet |Huvudnoder |8088 |HTTP |Webbgränssnittet för Resource Manager |
| Resource Manager-Webbgränssnittet |Huvudnoder |8090 |HTTPS |Webbgränssnittet för Resource Manager |
| Resource Manager-admin-gränssnitt |Huvudnoder |8141 |IPC |För program bidrag (Hive, Pig, Hive server osv.) |
| Resurshanterarens scheduler |Huvudnoder |8030 |HTTP |Administrativt gränssnitt |
| Programgränssnittet för Resource Manager |Huvudnoder |8050 |HTTP |Adressen för program manager-gränssnittet |
| NodeManager |Alla arbetsnoder |30050 |&nbsp; |Adressen för hanteraren för behållare |
| NodeManager webbgränssnittet |Alla arbetsnoder |30060 |HTTP |Resource manager-gränssnittet |
| Tidslinje-adress |Huvudnoder |10200 |RPC |Tidslinje service RPC-tjänsten. |
| Tidslinje-Webbgränssnittet |Huvudnoder |8181 |HTTP |Tidslinje service-Webbgränssnittet |

### <a name="hive-ports"></a>Hive-portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| HiveServer2 |Huvudnoder |10001 |Thrift |Tjänsten för att ansluta till Hive (Thrift/JDBC) |
| Hive-Metaarkiv |Huvudnoder |9083 |Thrift |Tjänsten för att ansluta till Hive-metadata (Thrift/JDBC) |

### <a name="webhcat-ports"></a>WebHCat portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| WebHCat-server |Huvudnoder |30111 |HTTP |Webb-API ovanpå HCatalog och andra Hadoop-tjänster |

### <a name="mapreduce-ports"></a>MapReduce-portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| JobHistory |Huvudnoder |19888 |HTTP |MapReduce JobHistory webbgränssnittet |
| JobHistory |Huvudnoder |10020 |&nbsp; |MapReduce JobHistory server |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Överföringar mellanliggande kartan rozbalily do begär Reducerare |

### <a name="oozie"></a>Oozie

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| Oozie-server |Huvudnoder |11000 |HTTP |URL för Oozie-tjänsten |
| Oozie-server |Huvudnoder |11001 |HTTP |Port för Oozie-administratör |

### <a name="ambari-metrics"></a>Ambari-mått

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| Tidslinje (programmet historik) |Huvudnoder |6188 |HTTP |Tidslinje service-Webbgränssnittet |
| Tidslinje (programmet historik) |Huvudnoder |30200 |RPC |Tidslinje service-Webbgränssnittet |

### <a name="hbase-ports"></a>HBase-portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| HMaster |Huvudnoder |16000 |&nbsp; |&nbsp; |
| HMaster info-Webbgränssnittet |Huvudnoder |16010 |HTTP |Porten för HBase Master-webbgränssnittet |
| Regionsserver |Alla arbetsnoder |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |Den port som klienter använder för att ansluta till ZooKeeper |

### <a name="kafka-ports"></a>Kafka-portar

| Tjänst | Noder | Port | Protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| Broker |Arbetsnoder |9092 |[Kafka-protokollet](http://kafka.apache.org/protocol.html) |Används för klientkommunikation |
| &nbsp; |Zookeeper-noder |2181 |&nbsp; |Den port som klienter använder för att ansluta till Zookeeper |

### <a name="spark-ports"></a>Spark-portar

| Tjänst | Noder | Port | Protokoll | URL-sökväg | Beskrivning |
| --- | --- | --- | --- | --- | --- |
| Spark Thrift-servrar |Huvudnoder |10002 |Thrift | &nbsp; | Tjänsten för att ansluta till Spark SQL (Thrift/JDBC) |
| Livy-server | Huvudnoder | 8998 | HTTP | &nbsp; | Tjänsten för att köra instruktioner, jobb och program |
| Jupyter-notebook-fil | Huvudnoder | 8001 | HTTP | &nbsp; | Jupyter notebook-webbplats |

Exempel:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. I det här exemplet `10.0.0.11` är IP-adressen för huvudnoden som är värd för tjänsten Livy.
