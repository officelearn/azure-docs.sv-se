---
title: Felmeddelande visas inte i Apache Hive View - Azure HDInsight
description: Frågan misslyckas i Apache Hive View utan information om Azure HDInsight-klustret.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 5aa03ac3537783daef87c9e7cb7d4ec58988ea9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895219"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>Scenario: Felmeddelandet Query visas inte i Apache Hive-vyn i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du använder interaktiva frågekomponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Felmeddelandet Apache Hive View-frågan ser ut ungefär så här, utan ytterligare information:

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>Orsak

Ibland kan felmeddelandet om ett frågefel vara för stort för att visas på huvudsidan för Hive View.

## <a name="resolution"></a>Lösning

Kontrollera fliken Meddelanden i det övre högra hörnet på Hive_view för att se hela Stacktrace och felmeddelande.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
