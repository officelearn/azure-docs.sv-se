---
title: Hive-lager koppling – Apache Zeppelin med livy – Azure HDInsight
description: Lär dig hur du integrerar Hive-dist. lager koppling med Apache Zeppelin på Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 1f9d2d9bd2a58fa4c6f14db8ffd067bb39fc1553
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853815"
---
# <a name="integrate-apache-zeppelin-with-hive-warehouse-connector-in-azure-hdinsight"></a>Integrera Apache Zeppelin med Hive Warehouse Connector i Azure HDInsight

HDInsight Spark-kluster innehåller Apache Zeppelin-anteckningsböcker med olika tolkar. I den här artikeln kommer vi bara att fokusera på livy-tolken för att få åtkomst till Hive-tabeller från Spark med Hive Warehouse Connector.

## <a name="prerequisite"></a>Förutsättning

Slutför installations stegen för [Hive-distributions lager kopplingen](apache-hive-warehouse-connector.md#hive-warehouse-connector-setup) .

## <a name="getting-started"></a>Komma igång

1. Använd [SSH-kommandot](../hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till ditt Apache Spark-kluster. Redigera kommandot nedan genom att ersätta kluster namn med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Kör följande kommando från SSH-sessionen för att anteckna versionerna för `hive-warehouse-connector-assembly` och `pyspark_hwc` :

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

    Spara utdata för senare användning när du konfigurerar Apache Zeppelin.

## <a name="configure-livy"></a>Konfigurera livy

Följande konfigurationer krävs för att komma åt Hive-tabeller från Zeppelin med livy-tolken.

### <a name="interactive-query-cluster"></a>Interaktiva Query-kluster

1. I en webbläsare navigerar du till `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HDFS/configs` där LLAPCLUSTERNAME är namnet på ditt interaktiva fråga-kluster.

1. Gå till **Avancerad**  >  **anpassad kärna – plats**. Välj **Lägg till egenskap...** för att lägga till följande konfigurationer:

    | Konfiguration                 | Värde |
    | ----------------------------- |-------|
    | Hadoop. proxyuser. livy. Groups  | *     |
    | Hadoop. proxyuser. livy. hosts   | *     |

1. Spara ändringarna och starta om alla påverkade komponenter.

### <a name="spark-cluster"></a>Spark-kluster

1. I en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` där kluster namn är namnet på ditt Apache Spark-kluster.

1. Expandera **anpassad livy2-conf**. Välj **Lägg till egenskap...** för att lägga till följande konfiguration:

    | Konfiguration                 | Värde                                      |
    | ----------------------------- |------------------------------------------  |
    | livy. File. local-dir-vitlista | /usr/HDP/Current/hive_warehouse_connector/ |

1. Spara ändringarna och starta om alla påverkade komponenter.

### <a name="configure-livy-interpreter-in-zeppelin-ui-spark-cluster"></a>Konfigurera livy-tolken i Zeppelin UI (Spark-kluster)

1. I en webbläsare går du till `https://CLUSTERNAME.azurehdinsight.net/zeppelin/#/interpreter` , där `CLUSTERNAME` är namnet på ditt Apache Spark-kluster.

1. Navigera till **livy2**.

1. Lägg till följande konfigurationer:

    | Konfiguration                 | Värde                                      |
    | ----------------------------- |:------------------------------------------:|
    | livy. Spark. Hadoop. Hive. LLAP. daemon. service. hosts | @llap0 |
    | livy. Spark. Security. credentials. hiveserver2. Enabled | true |
    | livy. Spark. SQL. Hive. LLAP | true |
    | livy. Spark. garn. Security. credentials. hiveserver2. Enabled | true |
    | livy. superusers | livy, Zeppelin |
    | livy. Spark. jar v7 | `file:///usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-VERSION.jar`.<br>Ersätt VERSION med värdet du fick från att [komma igång](#getting-started)tidigare. |
    | livy. Spark. submit. pyFiles | `file:///usr/hdp/current/hive_warehouse_connector/pyspark_hwc-VERSION.zip`.<br>Ersätt VERSION med värdet du fick från att [komma igång](#getting-started)tidigare. |
    | livy. Spark. SQL. Hive. hiveserver2. JDBC. URL | Ange den som HiveServer2-interaktiva JDBC-URL för det interaktiva fråga klustret. |
    | Spark. Security. credentials. hiveserver2. Enabled | true |

1. Lägg till följande konfiguration för ESP-kluster:

    | Konfiguration| Värde|
    |---|---|
    | livy. Spark. SQL. Hive. hiveserver2. JDBC. URL. Principal | `hive/<headnode-FQDN>@<AAD-Domain>` |

    Ersätt `<headnode-FQDN>` med det fullständigt kvalificerade domän namnet för Head-noden i det interaktiva fråga-klustret.
    Ersätt `<AAD-DOMAIN>` med namnet på den Azure Active Directory (AAD) som klustret är anslutet till. Använd en versal sträng för `<AAD-DOMAIN>` värdet, annars hittas inte autentiseringsuppgiften. Sök `/etc/krb5.conf` efter sfär namnen om det behövs.

1. Spara ändringarna och starta om livy-tolken.

Om livy-tolken inte är tillgänglig ändrar du `shiro.ini` filen som finns i Zeppelin-komponenten i Ambari. Mer information finns i [Konfigurera Apache Zeppelin Security](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.0.1/configuring-zeppelin-security/content/enabling_access_control_for_interpreter__configuration__and_credential_settings.html).  


## <a name="running-queries-in-zeppelin"></a>Köra frågor i Zeppelin 

Starta en Zeppelin-anteckningsbok med livy-tolken och kör följande

```python
%livy2

import com.hortonworks.hwc.HiveWarehouseSession
import com.hortonworks.hwc.HiveWarehouseSession._
import org.apache.spark.sql.SaveMode

# Initialize the hive context
val hive = HiveWarehouseSession.session(spark).build()

# Create a database
hive.createDatabase("hwc_db",true)
hive.setDatabase("hwc_db")

# Create a Hive table
hive.createTable("testers").ifNotExists().column("id", "bigint").column("name", "string").create()

val dataDF = Seq( (1, "foo"), (2, "bar"), (8, "john")).toDF("id", "name")

# Validate writes to the table
dataDF.write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table", "hwc_db.testers").save()

# Validate reads
hive.executeQuery("select * from testers").show()

```

## <a name="next-steps"></a>Nästa steg

* [INSTANSEN och Apache Spark åtgärder](./apache-hive-warehouse-connector-operations.md)
* [INSTANSEN-integrering med Apache Spark och Apache Hive](./apache-hive-warehouse-connector.md)
* [Använda Interactive Query i HDInsight](./apache-interactive-query-get-started.md)