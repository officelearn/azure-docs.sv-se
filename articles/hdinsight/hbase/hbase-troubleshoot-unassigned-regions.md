---
title: Problem med regionservrar i Azure HDInsight
description: Problem med regionservrar i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 85aeafb2c4461b50d399e40d9abff2ac04b677c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272765"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Problem med regionservrar i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="scenario-unassigned-regions"></a>Scenario: Regioner som inte tilldelats

### <a name="issue"></a>Problem

När `hbase hbck` du kör kommandot visas ett felmeddelande som liknar:

```
multiple regions being unassigned or holes in the chain of regions
```

Från Apache HBase Master UI kan du se antalet regioner som är obalanserade på alla regionservrar. Sedan kan du `hbase hbck` köra kommandot för att se hål i regionkedjan.

### <a name="cause"></a>Orsak

Hål kan vara resultatet av offlineregioner.

### <a name="resolution"></a>Lösning

Åtgärda tilldelningarna. Följ stegen nedan för att få de otilldelade regionerna tillbaka till normalt tillstånd:

1. Logga in på HDInsight HBase-klustret med SSH.

1. Kör `hbase zkcli` kommandot för att ansluta till ZooKeeper-skalet.

1. Kör `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` eller kommandot.

1. Avsluta zookeeper skal `exit` med hjälp av kommandot.

1. Öppna Apache Ambari-användargränssnittet och starta sedan om active hbase-huvudtjänsten.

1. Kör `hbase hbck` kommandot igen (utan några ytterligare alternativ). Kontrollera utdata och se till att alla regioner tilldelas.

---

## <a name="scenario-dead-region-servers"></a>Scenario: Servrar i död region

### <a name="issue"></a>Problem

Regionservrar kan inte starta.

### <a name="cause"></a>Orsak

Flera dela WAL kataloger.

1. Hämta lista över aktuella WALs: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`.

1. Kontrollera `wals.out` filen. Om det finns för många delningskataloger (som börjar med *-delning) misslyckas förmodligen regionservern på grund av dessa kataloger.

### <a name="resolution"></a>Lösning

1. Stoppa HBase från Ambari-portalen.

1. Kör `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` för att få en ny lista över WALs.

1. Flytta *-delningskatalogerna till `splitWAL`en tillfällig mapp och ta bort *-delningskatalogerna.

1. Kör `hbase zkcli` kommando för att ansluta till zookeeper skal.

1. Kör `rmr /hbase-unsecure/splitWAL`.

1. Starta om HBase-tjänsten.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
