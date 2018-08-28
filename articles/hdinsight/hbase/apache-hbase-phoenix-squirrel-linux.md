---
title: Använda Apache Phoenix och SQLLine med HBase i Azure HDInsight
description: Lär dig använda Apache Phoenix i HDInsight. Dessutom lär du dig hur du installerar och konfigurerar SQLLine på din dator att ansluta till ett HBase-kluster i HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: jasonh
ms.openlocfilehash: 92e6dcf7bf467bac31798df994e7efa8da40f035
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045666"
---
# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Använda Apache Phoenix med Linux-baserade HBase-kluster i HDInsight
Lär dig hur du använder [Apache Phoenix](http://phoenix.apache.org/) i Azure HDInsight och hur du använder SQLLine. Läs mer om Phoenix [Phoenix i 15 minuter eller mindre](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Phoenix-grammatik finns [Phoenix grammatik](http://phoenix.apache.org/language/index.html).

> [!NOTE]
> Phoenix versionsinformation om HDInsight finns i [Nyheter i Hadoop-klusterversionerna från HDInsight](../hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Använda SQLLine
[SQLLine](http://sqlline.sourceforge.net/) är ett kommandoradsverktyg för att köra SQL.

### <a name="prerequisites"></a>Förutsättningar
Innan du kan använda SQLLine, måste du ha följande objekt:

* **Ett HBase-kluster i HDInsight**. Om du vill skapa en [Kom igång med Apache HBase i HDInsight](./apache-hbase-tutorial-get-started-linux.md).

När du ansluter till ett HBase-kluster som du behöver ansluta till en av de virtuella datorer som ZooKeeper. Varje HDInsight-kluster är tre ZooKeeper virtuella datorer.

**Att hämta värdnamnet ZooKeeper**

1. Öppna Ambari genom att bläddra till **https://\<klusternamnet\>. azurehdinsight.net**.
2. Ange HTTP (kluster)-användarnamn och lösenord för att logga in.
3. I den vänstra menyn väljer du **ZooKeeper**. Tre **ZooKeeper-Server** instanser visas.
4. Välj en av de **ZooKeeper-Server** instanser. På den **sammanfattning** fönstret hitta den **värdnamn**. Den liknar *zk1 jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Använda SQLLine**

1. Ansluta till klustret med hjälp av SSH. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Använd följande kommandon för att köra SQLLine i SSH:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. Om du vill skapa en HBase-tabell och infoga vissa data, kör du följande kommandon:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

Mer information finns i den [SQLLine manuell](http://sqlline.sourceforge.net/#manual) och [Phoenix grammatik](http://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du använder Apache Phoenix i HDInsight. Mer information finns i följande artiklar:

* [Översikt över HDInsight HBase][hdinsight-hbase-overview].
  HBase är en Apache, öppen källkod, NoSQL-databas som bygger på Hadoop och ger direktåtkomst och stark konsekvens för stora mängder Ostrukturerade och semistrukturerade data.
* [Etablera HBase-kluster i Azure Virtual Network][hdinsight-hbase-provision-vnet].
  Med virtual network-integration, kan HBase-kluster bara distribueras till samma virtuella nätverk som dina program, så att program kan kommunicera direkt med HBase.
* [Konfigurera HBase-replikering i HDInsight](apache-hbase-replication.md). Lär dig hur du ställer in HBase-replikering mellan två Azure-datacenter.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md


