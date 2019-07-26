---
title: Portar som används av Hadoop-tjänster i HDInsight – Azure
description: En lista med portar som används av Hadoop-tjänster som körs på HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: hrasheed
ms.openlocfilehash: 34ab49378f9237a42bed869a6f6d67249b5238f9
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464698"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>Portar som används av Apache Hadoop Services i HDInsight

Det här dokumentet innehåller en lista över de portar som används av Apache Hadoop tjänster som körs på Linux-baserade HDInsight-kluster. Den innehåller också information om portar som används för att ansluta till klustret med SSH.

## <a name="public-ports-vs-non-public-ports"></a>Offentliga portar eller icke-offentliga portar

Linux-baserade HDInsight-kluster exponerar bara tre portar offentligt på Internet. 22, 23 och 443. Dessa portar används för att på ett säkert sätt få åtkomst till klustret med SSH och tjänster som exponeras över Secure HTTPS-protokollet.

Internt implementeras HDInsight av flera Azure-Virtual Machines (noderna i klustret) som körs på en Azure-Virtual Network. I det virtuella nätverket kan du komma åt portar som inte exponeras via Internet. Om du till exempel ansluter till en av huvudnoderna med hjälp av SSH, från Head-noden kan du direkt komma åt tjänster som körs på klusternoderna.

> [!IMPORTANT]  
> Om du inte anger ett Azure-Virtual Network som ett konfigurations alternativ för HDInsight skapas ett automatiskt. Du kan dock inte ansluta till andra datorer (till exempel andra Azure-Virtual Machines eller klient utvecklings datorn) till det här virtuella nätverket.

Om du vill ansluta till fler datorer i det virtuella nätverket måste du först skapa det virtuella nätverket och sedan ange det när du skapar ditt HDInsight-kluster. Mer information finns i [planera ett virtuellt nätverk för HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="public-ports"></a>Offentliga portar

Alla noder i ett HDInsight-kluster finns i en Azure-Virtual Network och kan inte nås direkt från Internet. En offentlig Gateway ger Internet åtkomst till följande portar, som är gemensamma för alla typer av HDInsight-kluster.

| Tjänsten | Port | Protocol | Beskrivning |
| --- | --- | --- | --- |
| sshd |22 |SSH |Ansluter klienter till sshd på den primära huvudnoden. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight). |
| sshd |22 |SSH |Ansluter klienter till sshd på Edge-noden. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight). |
| sshd |23 |SSH |Ansluter klienter till sshd på den sekundära huvudnoden. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight). |
| Ambari |443 |HTTPS |Ambari webb gränssnitt. Se [Hantera HDInsight med hjälp av Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Ambari REST API. Se [Hantera HDInsight med Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |HCatalog REST API. Se [använda MapReduce med vändning](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Ansluter till Hive med ODBC. Se [ansluta Excel till HDInsight med Microsoft ODBC-drivrutinen](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Ansluter till ApacheHive med hjälp av JDBC. Se [ansluta till Apache Hive på HDInsight med Hive-drivrutinen JDBC](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Följande är tillgängliga för vissa kluster typer:

| Tjänsten | Port | Protocol | Klustertyp | Beskrivning |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |HBase REST API. Se [Kom igång med Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |Spark-REST API. Se [skicka Apache Spark jobb via fjärr anslutning med Apache livy](spark/apache-spark-livy-rest-interface.md) |
| Spark Thrift-Server |443 |HTTPS |Spark |Spark Thrift-server som används för att skicka Hive-frågor. Se [använda Beeline med Apache Hive på HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Webb gränssnitt för storm. Se [distribuera och hantera Apache Storm-topologier i HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) |

### <a name="authentication"></a>Authentication

Alla tjänster som är offentligt utsatta för Internet måste autentiseras:

| Port | Autentiseringsuppgifter |
| --- | --- |
| 22 eller 23 |De autentiseringsuppgifter för SSH-användare som anges när klustret skapas |
| 443 |Inloggnings namnet (standard: admin) och lösen ordet som angavs när klustret skapades |

## <a name="non-public-ports"></a>Icke-offentliga portar

> [!NOTE]  
> Vissa tjänster är bara tillgängliga för vissa kluster typer. Till exempel är HBase endast tillgängligt på HBase-kluster typer.

> [!IMPORTANT]  
> Vissa tjänster körs bara på en huvudnoden i taget. Om du försöker ansluta till tjänsten på den primära huvudnoden och får ett fel meddelande försöker du igen med hjälp av den sekundära huvudnoden.

### <a name="ambari"></a>Ambari

| Tjänsten | Noder | Port | URL-sökväg | Protocol | 
| --- | --- | --- | --- | --- |
| Ambari webb gränssnitt | Huvudnoder | 8080 | / | HTTP |
| Ambari REST API | Huvudnoder | 8080 | /api/v1 | HTTP |

Exempel:

* Ambari REST API:`curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>HDFS-portar

| Tjänsten | Noder | Port | Protocol | Beskrivning |
| --- | --- | --- | --- | --- |
| NameNode webb gränssnitt |Huvudnoder |30070 |HTTPS |Webb gränssnitt för att visa status |
| NameNode-metadatatjänst |huvudnoder |8020 |IPC |Metadata för fil system |
| DataNode |Alla arbetsnoder |30075 |HTTPS |Webb gränssnitt för att visa status, loggar osv. |
| DataNode |Alla arbetsnoder |30010 |&nbsp; |Dataöverföring |
| DataNode |Alla arbetsnoder |30020 |IPC |Metadata-åtgärder |
| Sekundär NameNode |Huvudnoder |50090 |HTTP |Kontroll punkt för NameNode metadata |

### <a name="yarn-ports"></a>GARN portar

| Tjänsten | Noder | Port | Protocol | Beskrivning |
| --- | --- | --- | --- | --- |
| Webb gränssnitt för Resource Manager |Huvudnoder |8088 |HTTP |Webb gränssnitt för Resource Manager |
| Webb gränssnitt för Resource Manager |Huvudnoder |8090 |HTTPS |Webb gränssnitt för Resource Manager |
| Administrations gränssnitt för Resource Manager |huvudnoder |8141 |IPC |För program inlämningar (Hive, Hive-Server, gris osv.) |
| Schemaläggaren i Resource Manager |huvudnoder |8030 |HTTP |Administrativt gränssnitt |
| Resource Manager Application Interface |huvudnoder |8050 |HTTP |Adress till program hanterarens gränssnitt |
| NodeManager |Alla arbetsnoder |30050 |&nbsp; |Adressen till behållar hanteraren |
| NodeManager webb gränssnitt |Alla arbetsnoder |30060 |HTTP |Resource Manager-gränssnitt |
| Tids linje adress |Huvudnoder |10200 |RPC |Tids linje tjänstens RPC-tjänst. |
| Webb gränssnitt för tids linje |Huvudnoder |8188 |HTTP |Tids linje tjänstens webb gränssnitt |

### <a name="hive-ports"></a>Hive-portar

| Tjänsten | Noder | Port | Protocol | Beskrivning |
| --- | --- | --- | --- | --- |
| HiveServer2 |Huvudnoder |10001 |Thrift |Tjänst för anslutning till Hive (Thrift/JDBC) |
| Hive-metaarkiv |Huvudnoder |9083 |Thrift |Tjänst för att ansluta till Hive-metadata (Thrift/JDBC) |

### <a name="webhcat-ports"></a>WebHCat-portar

| Tjänsten | Noder | Port | Protocol | Beskrivning |
| --- | --- | --- | --- | --- |
| WebHCat-Server |Huvudnoder |30111 |HTTP |Webb-API ovanpå HCatalog och andra Hadoop-tjänster |

### <a name="mapreduce-ports"></a>MapReduce-portar

| Tjänsten | Noder | Port | Protocol | Beskrivning |
| --- | --- | --- | --- | --- |
| JobHistory |Huvudnoder |19888 |HTTP |MapReduce JobHistory Web UI |
| JobHistory |Huvudnoder |10020 |&nbsp; |MapReduce JobHistory-Server |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Överför mellanliggande kart utdata till begär Ande för minskning |

### <a name="oozie"></a>Oozie

| Tjänsten | Noder | Port | Protocol | Beskrivning |
| --- | --- | --- | --- | --- |
| Oozie-Server |Huvudnoder |11000 |HTTP |URL för Oozie-tjänst |
| Oozie-Server |Huvudnoder |11001 |HTTP |Port för Oozie-administratör |

### <a name="ambari-metrics"></a>Ambari-mått

| Tjänsten | Noder | Port | Protocol | Beskrivning |
| --- | --- | --- | --- | --- |
| Tids linje (program historik) |Huvudnoder |6188 |HTTP |Tids linje tjänstens webb gränssnitt |
| Tids linje (program historik) |Huvudnoder |30200 |RPC |Tids linje tjänstens webb gränssnitt |

### <a name="hbase-ports"></a>HBase-portar

| Tjänsten | Noder | Port | Protocol | Beskrivning |
| --- | --- | --- | --- | --- |
| HMaster |Huvudnoder |16000 |&nbsp; |&nbsp; |
| Webb gränssnitt för HMaster-information |Huvudnoder |16010 |HTTP |Porten för HBase Master webb gränssnittet |
| Region Server |Alla arbetsnoder |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |Den port som klienter använder för att ansluta till ZooKeeper |

### <a name="kafka-ports"></a>Kafka-portar

| Tjänsten | Noder | Port | Protocol | Beskrivning |
| --- | --- | --- | --- | --- |
| Utjämning |Arbetsnoder |9092 |[Kafka Wire Protocol](https://kafka.apache.org/protocol.html) |Används för klient kommunikation |
| &nbsp; |Zookeeper-noder |2181 |&nbsp; |Den port som klienter använder för att ansluta till Zookeeper |

### <a name="spark-ports"></a>Spark-portar

| Tjänsten | Noder | Port | Protocol | URL-sökväg | Beskrivning |
| --- | --- | --- | --- | --- | --- |
| Spark Thrift-servrar |Huvudnoder |10002 |Thrift | &nbsp; | Tjänst för att ansluta till Spark SQL (Thrift/JDBC) |
| Livy-Server | Huvudnoder | 8998 | HTTP | &nbsp; | Tjänst för att köra instruktioner, jobb och program |
| Jupyter Notebook | Huvudnoder | 8001 | HTTP | &nbsp; | Jupyter Notebook-webbplats |

Exempel:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. I det här exemplet `10.0.0.11` är IP-adressen till huvudnoden som är värd för livy-tjänsten.
