---
title: BindException-adressen används redan i Azure HDInsight
description: BindException-adressen används redan i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/08/2019
ms.openlocfilehash: 8851a4dfb7deafab7ad77ef80619dd49ca46ed71
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947856"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Scenario: BindException-adressen används redan i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Omstarten på en Apache HBase region Server kan inte slutföras. `region-server.log` Från i `/var/log/hbase` -katalogen på arbetsnoder där regions servern startar Miss lyckas kan du se ett fel meddelande som liknar följande:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Orsak

Starta om HBase regions servrar under tung arbets belastnings aktivitet. Nedan visas vad som händer i bakgrunden när en användare startar omstarten på HBase regions serverns Ambari-gränssnitt:

1. Ambari-agenten kommer att skicka en stoppbegäran till region servern.

1. Ambari-agenten väntar sedan i 30 sekunder för region servern att stängas av på ett smidigt sätt.

1. Om programmet fortsätter att ansluta till region servern stängs det inte direkt och tids gränsen på 30 sekunder upphör snart att gälla.

1. Efter den 30 sekundernas utgång kommer Ambari-agenten att skicka ett tvångs stopp (kill-9) till region servern.

1. På grund av den här avstängningen kan den port som är associerad med processen inte frigöras, även om region Server processen avslutas, vilket `AddressBindException`slutligen leder till.

## <a name="resolution"></a>Lösning

Minska belastningen på HBase regions servrar innan du påbörjar en omstart.

Du kan också försöka med att starta om region servrar manuellt på arbetsnoderna med följande kommandon:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
