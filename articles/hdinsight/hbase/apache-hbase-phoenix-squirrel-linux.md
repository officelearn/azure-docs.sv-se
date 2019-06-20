---
title: 'Snabbstart: Fråga Apache HBase i Azure HDInsight - Apache Phoenix'
description: I den här snabbstarten får du lära dig hur du använder Apache Phoenix i HDInsight. Dessutom lär du dig hur du installerar och konfigurerar SQLLine på din dator att ansluta till ett HBase-kluster i HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 20af6d32d03ae5d4fe37b1a37198ef1f2c50ec95
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137426"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Snabbstart: Fråga Apache HBase i Azure HDInsight med Apache Phoenix

I den här snabbstarten har du lära dig hur du använder Apache Phoenix köra HBase frågor på Azure HDInsight. Apache Phoenix är en SQL-frågemotor för Apache HBase. Den används som en JDBC-drivrutin, och gör att du kan ställa frågor och hantera HBase-tabeller med SQL. [SQLLine](http://sqlline.sourceforge.net/) är ett kommandoradsverktyg för att köra SQL.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett Apache HBase-kluster. Se [Skapa kluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) att skapa ett HDInsight-kluster.  Kontrollera att du väljer den **HBase** typ av kluster.

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>Identifiera en ZooKeeper-nod

När du ansluter till ett HBase-kluster som du behöver ansluta till en av Apache ZooKeeper-noder. Varje HDInsight-kluster är tre ZooKeeper-noder. CURL kan användas för att snabbt identifiera en ZooKeeper-nod. Redigera curl-kommandot nedan genom att ersätta `PASSWORD` och `CLUSTERNAME` med relevanta värden, och ange sedan kommandot i en kommandotolk:

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

En del av utdata ser ut ungefär som:

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

Anteckna värdet för `host_name` för senare användning.

## <a name="create-a-table-and-manipulate-data"></a>Skapa en tabell och manipulera data

Du kan använda SSH för att ansluta till HBase-kluster och sedan använda Apache Phoenix för att skapa HBase-tabeller, infoga data och köra frågor mot data.

1. Använd `ssh` kommando för att ansluta till HBase-kluster. Redigera kommandot nedan genom att ersätta `CLUSTERNAME` med namnet på klustret, och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Ändra katalogen till Phoenix-klienten. Ange följande kommando:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. Starta [SQLLine](http://sqlline.sourceforge.net/). Redigera kommandot nedan genom att ersätta `ZOOKEEPER` med ZooKeeper noden identifierade tidigare och ange sedan kommando:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Skapa en HBase-tabell. Ange följande kommando:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. Använd SQLLine `!tables` kommando för att lista alla tabeller i HBase. Ange följande kommando:

    ```sqlline
    !tables
    ```

6. Infoga värden i tabellen. Ange följande kommando:

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. Fråga tabellen. Ange följande kommando:

    ```sql
    SELECT * FROM Company;
    ```

8. Ta bort en post. Ange följande kommando:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. Ta bort tabellen. Ange följande kommando:

    ```hbase
    DROP TABLE Company;
    ```

10. Använd SQLLine `!quit` kommando för att avsluta SQLLine. Ange följande kommando:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabbstarten kan du ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

Om du vill ta bort ett kluster, se [ta bort ett HDInsight-kluster med din webbläsare, PowerShell eller Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig att använda Apache Phoenix köra HBase frågor på Azure HDInsight. Mer information om Apache Phoenix ger nästa artikel en djupare undersökning.

> [!div class="nextstepaction"]
> [Apache Phoenix i HDInsight](../hdinsight-phoenix-in-hdinsight.md)
