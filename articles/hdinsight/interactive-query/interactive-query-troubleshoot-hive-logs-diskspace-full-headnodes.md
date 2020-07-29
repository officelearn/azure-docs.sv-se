---
title: Apache Hive loggar som fyller disk utrymme – Azure HDInsight
description: Apache Hive loggar fyller upp disk utrymmet på huvudnoderna i Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "78943971"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Scenario: Apache Hive loggar fyller upp disk utrymmet på Head-noderna i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar på problem som rör inte tillräckligt med disk utrymme på huvudnoderna i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

I ett Apache Hive/LLAP-kluster tar oönskade loggar upp hela disk utrymmet på huvudnoderna. På grund av vilken kan följande problem visas.

1. SSH-åtkomsten Miss lyckas på grund av att det inte finns utrymme kvar på Head-noden.
2. Ambari ger *http-fel: tjänsten 503 är inte tillgänglig*.

`ambari-agent`Loggarna visar följande när problemet uppstår.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Orsak

I avancerade Hive-log4j konfigurationer utelämnas parametern *log4j. appendor. rfa. MaxBackupIndex* . Det orsakar en oändlig generering av loggfiler.

## <a name="resolution"></a>Lösning

1. Gå till sammanfattning av Hive-komponent på Ambari-portalen och klicka på `Configs` fliken.

2. Gå till `Advanced hive-log4j` avsnittet i avancerade inställningar.

3. Ange `log4j.appender.RFA` parametern som RollingFileAppender. 

4. Ange `log4j.appender.RFA.MaxFileSize` och `log4j.appender.RFA.MaxBackupIndex` enligt följande.

```
log4jhive.log.maxfilesize=1024MB
log4jhive.log.maxbackupindex=10

log4j.appender.RFA=org.apache.log4j.RollingFileAppender
log4j.appender.RFA.File=${hive.log.dir}/${hive.log.file}
log4j.appender.RFA.MaxFileSize=${log4jhive.log.maxfilesize}
log4j.appender.RFA.MaxBackupIndex=${log4jhive.log.maxbackupindex}
log4j.appender.RFA.layout=org.apache.log4j.PatternLayout
log4j.appender.RFA.layout.ConversionPattern=%d{ISO8601} %-5p [%t] %c{2}: %m%n
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

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
