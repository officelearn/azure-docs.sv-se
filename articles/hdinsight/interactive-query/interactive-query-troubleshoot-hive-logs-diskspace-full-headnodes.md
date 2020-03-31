---
title: Apache Hive Loggar fylla upp diskutrymme - Azure HDInsight
description: Apache Hive-loggarna fyller diskutrymmet på huvudnoderna i Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943971"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Scenario: Apache Hive-loggar fyller upp diskutrymmet på head-noderna i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem som inte är relaterade till tillräckligt med diskutrymme på huvudnoderna i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

På ett Apache Hive/LLAP-kluster tar oönskade loggar upp hela diskutrymmet på huvudnoderna. På grund av vilka, följande frågor kunde ses.

1. SSH-åtkomst misslyckas på grund av att inget utrymme finns kvar på huvudnoden.
2. Ambari ger *HTTP-fel: 503 Tjänsten är inte tillgänglig*.

Loggarna `ambari-agent` visar följande när problemet inträffar.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Orsak

I avancerade Hive-log4j-konfigurationer utelämnas parametern *log4j.appender.RFA.MaxBackupIndex.* Det orsakar oändlig generering av loggfiler.

## <a name="resolution"></a>Lösning

1. Navigera till Hive-komponentsammanfattningen på `Configs` Ambari-portalen och klicka på fliken .

2. Gå till `Advanced hive-log4j` avsnittet i Avancerade inställningar.

3. Ange `log4j.appender.RFA` parameter som RollingFileAppender. 

4. Ställ `log4j.appender.RFA.MaxFileSize` `log4j.appender.RFA.MaxBackupIndex` in och enligt följande.

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
5. Ställ `hive.root.logger` `INFO,RFA` in på följande. Standardinställningen är DEBUG, vilket gör att loggar blir mycket stora.

```
# Define some default values that can be overridden by system properties
hive.log.threshold=ALL
hive.root.logger=INFO,RFA
hive.log.dir=${java.io.tmpdir}/${user.name}
hive.log.file=hive.log
```

6. Spara konfigurationerna och starta om de nödvändiga komponenterna.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
