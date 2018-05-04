---
title: Använda Apache Phoenix och SQLLine med HBase i Azure HDInsight | Microsoft Docs
description: Lär dig använda Apache Phoenix i HDInsight. Lär dig också att installera och konfigurera SQLLine på datorn för att ansluta till ett HBase-kluster i HDInsight.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: cda0f33b-a2e8-494c-972f-ae0bb482b818
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: 64700567b8acf816f42e6bf8cdc5386b6c65fe3f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Använda Apache Phoenix med Linux-baserade HBase-kluster i HDInsight
Lär dig hur du använder [Apache Phoenix](http://phoenix.apache.org/) i Azure HDInsight och hur du använder SQLLine. Läs mer om Phoenix [Phoenix i 15 minuter eller mindre](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Phoenix-grammatik finns [Phoenix grammatik](http://phoenix.apache.org/language/index.html).

> [!NOTE]
> Phoenix versionsinformation om HDInsight finns i [vad är nytt i Hadoop-klusterversioner som tillhandahålls av HDInsight](../hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Använd SQLLine
[SQLLine](http://sqlline.sourceforge.net/) är ett kommandoradsverktyg för att köra SQL.

### <a name="prerequisites"></a>Förutsättningar
Innan du kan använda SQLLine, måste du ha följande:

* **Ett HBase-kluster i HDInsight**. Om du vill skapa en finns [Kom igång med Apache HBase i HDInsight](./apache-hbase-tutorial-get-started-linux.md).

Du måste ansluta till en av de virtuella datorerna ZooKeeper när du ansluter till ett HBase-kluster. Varje HDInsight-kluster har tre ZooKeeper virtuella datorer.

**Att hämta värdnamnet ZooKeeper**

1. Öppna Ambari genom att bläddra till **https://\<klusternamnet\>. azurehdinsight.net**.
2. Ange HTTP (kluster)-användarnamn och lösenord för att logga in.
3. Välj i den vänstra menyn **ZooKeeper**. Tre **ZooKeeper-Server** instanser visas.
4. Välj en av de **ZooKeeper-Server** instanser. På den **sammanfattning** rutan Sök efter den **värdnamn**. Det liknar *zk1 jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Att använda SQLLine**

1. Ansluta till klustret med hjälp av SSH. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Använd följande kommandon för att köra SQLLine i SSH:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. Om du vill skapa en HBase-tabell och infoga data, kör du följande kommandon:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

Mer information finns i [SQLLine manuell](http://sqlline.sourceforge.net/#manual) och [Phoenix grammatik](http://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du använder Apache Phoenix i HDInsight. Läs mer i följande artiklar:

* [Översikt över HDInsight HBase][hdinsight-hbase-overview].
  HBase är en Apache öppen källkod NoSQL-databas som bygger på Hadoop och som ger direktåtkomst och stark konsekvens för stora mängder Ostrukturerade och halvstrukturerade data.
* [Etablera HBase-kluster i Azure Virtual Network][hdinsight-hbase-provision-vnet].
  Med virtuell nätverksintegration kan HBase-kluster bara distribueras till samma virtuella nätverk som dina program så att program kan kommunicera direkt med HBase.
* [Konfigurera HBase-replikering i HDInsight](apache-hbase-replication.md). Lär dig hur du ställer in HBase-replikering mellan två Azure-datacenter.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md


