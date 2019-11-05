---
title: Apache Hive Visa tids gränsen från frågeresultatet – Azure HDInsight
description: Apache Hive Visa tids gränsen vid hämtning av frågeresultat i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 86523a7564220191de252b362e45569116ff3111
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494191"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>Scenario: Apache Hive Visa tids gränsen vid hämtning av frågeresultat i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem vid användning av interaktiva frågekomponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

När du kör vissa frågor från vyn Apache Hive kan följande fel uppstå:

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>Orsak

Det är inte säkert att standardvärdet för timeout-värdet är lämpligt för den fråga som du kör. Den angivna tids perioden är för kort för att Hive-vyn ska hämta frågeresultatet.

## <a name="resolution"></a>Upplösning

Öka Ambari för vyn för Apache-datahive genom att ange följande egenskaper i `/etc/ambari-server/conf/ambari.properties`.

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

Värdet för `HIVE_VIEW_INSTANCE_NAME` är tillgängligt i slutet av URL: en för Hive-vyn.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns i [”Skapa en supportförfrågan för Azure”](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Tillgång till support för prenumerationshantering och fakturering ingår i din Microsoft Azure-prenumeration och teknisk support ges via ett [supportavtal för Azure](https://azure.microsoft.com/support/plans/).
