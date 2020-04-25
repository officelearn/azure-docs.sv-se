---
title: Apache ZooKeeper servern kan inte bilda ett kvorum i Azure HDInsight
description: Apache ZooKeeper servern kan inte bilda ett kvorum i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 41ac109e5c5379e6085dd57a3fcd8119915558fb
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133276"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper servern kan inte bilda ett kvorum i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Apache ZooKeeper servern är ohälsosam, kan symptom vara: både resurs hanterare/namnservrar är i vänte läge, enkla HDFS-åtgärder fungerar inte, `zkFailoverController` har stoppats och kan inte startas, garn/Spark/livy-jobb fungerar inte på grund av ZooKeeper-fel. Det kan också hända att LLAP daemon inte startar på säkra Spark-eller Interactive Hive-kluster. Du kan se ett fel meddelande som liknar:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

I Zookeeper-servern loggar in på en Zookeeper-värd\*vid/var/log/Zookeeper/Zookeeper-Zookeeper-Server-. out, kan du också se följande fel:

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>Orsak

När volymen av Snapshot-filer är skadad, kommer ZooKeeper-servern inte att kunna bilda ett kvorum, vilket medför att ZooKeeper relaterade tjänster inte är felfria. ZooKeeper-servern tar inte bort gamla Snapshot-filer från dess data katalog, i stället är det en regelbunden uppgift som ska utföras av användare för att underhålla healthiness för ZooKeeper. Mer information finns i [ZooKeeper-styrkor och begränsningar](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations).

## <a name="resolution"></a>Lösning

Kontrol lera ZooKeeper data `/hadoop/zookeeper/version-2` katalog `/hadoop/hdinsight-zookeeper/version-2` och ta reda på om ögonblicks bildens fil storlek är stor. Utför följande steg om det finns stora ögonblicks bilder:

1. Kontrol lera status för andra ZooKeeper-servrar i samma kvorum för att se till att de fungerar bra med kommandot "`echo stat | nc {zk_host_ip} 2181 (or 2182)`".  

1. Logga in på den problematiska ZooKeeper-värden, säkerhetskopiera ögonblicks `/hadoop/zookeeper/version-2` bilder `/hadoop/hdinsight-zookeeper/version-2`och transaktions loggar i och rensa sedan filerna i de två katalogerna. 

1. Starta om den problematiska ZooKeeper-servern i Ambari eller på ZooKeeper-värden. Starta sedan om tjänsten som har problem.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

- Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

- Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

- Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
