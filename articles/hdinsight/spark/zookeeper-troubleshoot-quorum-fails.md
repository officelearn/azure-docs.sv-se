---
title: Apache ZooKeeper-servern kan inte skapa ett kvorum i Azure HDInsight
description: Apache ZooKeeper-servern kan inte skapa ett kvorum i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 4e46efaf17ae9bad5df6f1f61f401d3e6de58a85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250243"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper-servern kan inte skapa ett kvorum i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Apache ZooKeeper server är ohälsosamt, symptom kan omfatta: både Resource Managers / Name Noder är i vänteläge, enkla HDFS-åtgärder fungerar inte, `zkFailoverController` stoppas och kan inte startas, Yarn / Spark / Livy jobb misslyckas på grund av Zookeeper fel. LLAP Daemons kan också misslyckas med att starta på Secure Spark eller Interactive Hive kluster. Ett felmeddelande kan visas som liknar:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

I Zookeeper Server loggar på någon Zookeeper värd på / var / log /\*zookeeper / zookeeper-zookeeper-server- .out, kan du också se följande fel:

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>Orsak

När volymen av ögonblicksbildfiler är stora eller ögonblicksbildfiler är skadade, kommer ZooKeeper-servern inte att bilda ett kvorum, vilket gör att ZooKeeper-relaterade tjänster är felaktiga. ZooKeeper-servern tar inte bort gamla ögonblicksbildfiler från datakatalogen, utan det är en periodisk uppgift som ska utföras av användare för att upprätthålla ZooKeepers sundhet. Mer information finns i [ZooKeeper-styrkor och begränsningar](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations).

## <a name="resolution"></a>Lösning

Kontrollera ZooKeeper `/hadoop/zookeeper/version-2` datakatalog och `/hadoop/hdinsight-zookeeper/version-2` ta reda på om ögonblicksbilder filstorleken är stor. Gör så här om det finns stora ögonblicksbilder:

1. Säkerhetskopiera ögonblicksbilder `/hadoop/zookeeper/version-2` `/hadoop/hdinsight-zookeeper/version-2`i och .

1. Rensa ögonblicksbilder `/hadoop/zookeeper/version-2` i `/hadoop/hdinsight-zookeeper/version-2`och .

1. Starta om alla ZooKeeper-servrar från Apache Ambari UI.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

- Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

- Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

- Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
