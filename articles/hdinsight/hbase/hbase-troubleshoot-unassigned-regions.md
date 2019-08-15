---
title: Problem med region servrar i Azure HDInsight
description: Problem med region servrar i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/07/2019
ms.openlocfilehash: e75f2fdd0530b92e8c8405b74c2a364ff9e9e28e
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935437"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Problem med region servrar i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="scenario-unassigned-regions"></a>Scenario: Otilldelade regioner

### <a name="issue"></a>Problem

När du `hbase hbck` kör kommandot visas ett fel meddelande som liknar:

```
multiple regions being unassigned or holes in the chain of regions
```

Från Apache HBase Master UI kan du se att antalet regioner är obalanserade över alla region servrar.

### <a name="cause"></a>Orsak

Hålen kan vara resultatet av offline-regioner.

### <a name="resolution"></a>Lösning

Korrigera tilldelningarna. Följ stegen nedan för att återställa de otilldelade områdena till normalt läge:

1. Logga in på HDInsight HBase-kluster med SSH.

1. Kör `hbase zkcli` kommando för att ansluta till Zookeeper-gränssnittet.

1. Kör `rmr /hbase/regions-in-transition` eller`rmr /hbase-unsecure/regions-in-transition` -kommando.

1. Avsluta Zookeeper-gränssnittet med `exit` hjälp av kommandot.

1. Öppna Ambari-ANVÄNDARGRÄNSSNITTET och starta om Active HBase Master-tjänsten från Ambari.

1. Kör `hbase hbck` kommandot igen (utan fler alternativ). Kontrol lera utdata och se till att alla regioner tilldelas.

---

## <a name="scenario-dead-region-servers"></a>Scenario: Servrar för död region

### <a name="issue"></a>Problem

Det gick inte att starta region servrarna.

### <a name="cause"></a>Orsak

Flera delnings WAL kataloger.

1. Hämta lista över aktuella Wals: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`.

1. `wals.out` Granska filen. Om det finns för många delnings kataloger (från och med * delning), fungerar inte region servern på grund av dessa kataloger.

### <a name="resolution"></a>Lösning

1. Stoppa HBase från Ambari-portalen.

1. Kör `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` för att hämta en ny lista över WALs.

1. Flytta *-delnings katalogerna till en tillfällig mapp, `splitWAL`och ta bort *-delnings katalogerna.

1. Kör `hbase zkcli` kommando för att ansluta till Zookeeper-gränssnittet.

1. Kör `rmr /hbase-unsecure/splitWAL`.

1. Starta om HBase-tjänsten.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
