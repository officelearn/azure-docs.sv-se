---
title: Apache Hive loggar som fyller disk utrymme – Azure HDInsight
description: Apache Hive loggar fyller upp disk utrymmet på huvudnoderna i Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 10/05/2020
ms.openlocfilehash: 5554a66927fc70f22ec552b938ae62038a04acb9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533027"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Scenario: Apache Hive loggar fyller upp disk utrymmet på Head-noderna i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar på problem som rör inte tillräckligt med disk utrymme på huvudnoderna i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

I ett Apache Hive/LLAP-kluster tar oönskade loggar upp hela disk utrymmet på huvudnoderna. På grund av vilken kan följande problem visas.

1. SSH-åtkomsten Miss lyckas på grund av att det inte finns utrymme kvar på Head-noden.
2. Ambari ger *http-fel: tjänsten 503 är inte tillgänglig* .
3. HiveServer2 interaktiva kan inte startas om.

`ambari-agent`Loggarna visar följande när problemet uppstår.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Orsak

I avancerade Hive-log4j konfigurationer anges det aktuella standard borttagnings schemat för filer som är äldre än 30 dagar baserat på senaste ändrings datum.

## <a name="resolution"></a>Lösning

1. Gå till sammanfattning av Hive-komponent på Ambari-portalen och klicka på `Configs` fliken.

2. Gå till `Advanced hive-log4j` avsnittet i avancerade inställningar.

3. Ange `appender.RFA.strategy.action.condition.age` en parameter till en ålder som du väljer. Exempel i 14 dagar: `appender.RFA.strategy.action.condition.age = 14D`

4. Om du inte ser några relaterade inställningar lägger du till följande inställningar.
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. Ange `hive.root.logger` `INFO,RFA` enligt följande. Standardinställningen är DEBUG, vilket gör att loggar blir mycket stora.

    ```
    # Define some default values that can be overridden by system properties
    hive.log.threshold=ALL
    hive.root.logger=INFO,RFA
    hive.log.dir=${java.io.tmpdir}/${user.name}
    hive.log.file=hive.log
    ```

6. Spara konfigurationerna och starta om de nödvändiga komponenterna.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).