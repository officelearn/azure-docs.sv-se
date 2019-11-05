---
title: Fel meddelande visas inte i Apache Hive Visa – Azure HDInsight
description: Frågan Miss lyckas i Apache Hive View utan någon information om Azure HDInsight-kluster.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 38dd064fe8365e6661ce7ea7e1077a4e1e32dbf5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494263"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>Scenario: fråga om fel meddelande visas inte i Apache Hive visning i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem vid användning av interaktiva frågekomponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Fel meddelandet Apache Hive för att visa frågan ser ut ungefär så här, utan ytterligare information:

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>Orsak

Ibland kan fel meddelandet om ett frågefel vara för stort för att visas på huvud sidan för Hive-vyn.

## <a name="resolution"></a>Upplösning

Kontrol lera fliken meddelanden i det övre högra hörnet av Hive_view för att se hela stacktrace och fel meddelandet.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns i [”Skapa en supportförfrågan för Azure”](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Tillgång till support för prenumerationshantering och fakturering ingår i din Microsoft Azure-prenumeration och teknisk support ges via ett [supportavtal för Azure](https://azure.microsoft.com/support/plans/).
