---
title: Använda Apache Phoenix och SQLLine med HBase i Azure HDInsight
description: Lär dig använda Apache Phoenix i HDInsight. Dessutom lär du dig hur du installerar och konfigurerar SQLLine på din dator att ansluta till ett HBase-kluster i HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: hrasheed
ms.openlocfilehash: 0bef586540635ee1bada3475f6316c84dea4308c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123169"
---
# <a name="use-apache-phoenix-with-linux-based-apache-hbase-clusters-in-hdinsight"></a>Använda Apache Phoenix med Linux-baserade Apache HBase-kluster i HDInsight
Lär dig hur du använder [Apache Phoenix](https://phoenix.apache.org/) i Azure HDInsight och hur du använder SQLLine. Läs mer om Phoenix [Apache Phoenix i 15 minuter eller mindre](https://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Phoenix-grammatik finns [Apache Phoenix grammatik](https://phoenix.apache.org/language/index.html).

> [!NOTE]  
> Phoenix versionsinformation om HDInsight finns i [vad är nytt i Apache Hadoop-klusterversionerna från HDInsight](../hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Använda SQLLine
[SQLLine](http://sqlline.sourceforge.net/) är ett kommandoradsverktyg för att köra SQL.

### <a name="prerequisites"></a>Nödvändiga komponenter
Innan du kan använda SQLLine, måste du ha följande objekt:

* **Ett Apache HBase-kluster i HDInsight**. Om du vill skapa en [Kom igång med Apache HBase i HDInsight](./apache-hbase-tutorial-get-started-linux.md).

När du ansluter till ett HBase-kluster måste du ansluta till en av de [Apache ZooKeeper](https://zookeeper.apache.org/) virtuella datorer. Varje HDInsight-kluster är tre ZooKeeper virtuella datorer.

**Att hämta värdnamnet ZooKeeper**

1. Öppna [Apache Ambari](https://ambari.apache.org/) genom att bläddra till **https://\<klusternamnet\>. azurehdinsight.net**.
2. Ange HTTP (kluster)-användarnamn och lösenord för att logga in.
3. I den vänstra menyn väljer du **ZooKeeper**. Tre **ZooKeeper-Server** instanser visas.
4. Välj en av de **ZooKeeper-Server** instanser. På den **sammanfattning** fönstret hitta den **värdnamn**. It looks similar to *zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Använda SQLLine**

1. Ansluta till klustret med hjälp av SSH. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Använd följande kommandon för att köra SQLLine i SSH:

        cd /usr/hdp/current/phoenix-client/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. Om du vill skapa en HBase-tabell och infoga vissa data, kör du följande kommandon:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

Mer information finns i den [SQLLine manuell](http://sqlline.sourceforge.net/#manual) och [Apache Phoenix grammatik](https://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du använder Apache Phoenix i HDInsight. Mer information finns i följande artiklar:

* [Översikt över HDInsight HBase][hdinsight-hbase-overview].
  Apache HBase är en Apache, öppen källkod, NoSQL-databas som bygger på Apache Hadoop och ger direktåtkomst och stark konsekvens för stora mängder Ostrukturerade och semistrukturerade data.
* [Etablera Apache HBase-kluster i Azure Virtual Network][hdinsight-hbase-provision-vnet].
  Med virtual network-integration, kan Apache HBase-kluster bara distribueras till samma virtuella nätverk som dina program, så att program kan kommunicera direkt med HBase.
* [Konfigurera Apache HBase-replikering i HDInsight](apache-hbase-replication.md). Lär dig hur du konfigurerar Apache HBase-replikering mellan två Azure-datacenter.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md


