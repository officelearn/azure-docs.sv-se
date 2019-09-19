---
title: Fel vid nekad åtkomst vid försök att skapa en Apache Hive tabell i Azure HDInsight
description: Fel vid nekad åtkomst vid försök att skapa en Apache Hive tabell i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8440bbc1cf7019e77c6274cbb11c013fb4e3e69d
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091398"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>Scenario: Fel vid nekad åtkomst vid försök att skapa en Apache Hive tabell i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem vid användning av interaktiva frågekomponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Följande fel visas när du försöker skapa en tabell:

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

Ett liknande fel meddelande visas om du kör följande HDFS-lagrings kommando:

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>Orsak

Möjligheten att skapa en tabell i Apache Hive bestäms av de behörigheter som tillämpas på klustrets lagrings konto. Om klustrets lagrings konto behörigheter är felaktiga kommer du inte att kunna skapa tabeller. Det innebär att du kan ha rätt Ranger-principer för att skapa tabeller och fortfarande se "åtkomst nekad"-fel.

## <a name="resolution"></a>Lösning

Detta beror på otillräcklig behörighet på den lagrings behållare som används. Användaren som skapar Hive-tabellen måste ha Läs-, skriv-och körnings behörighet mot behållaren. Mer information finns i [metod tips för Hive-auktorisering med Apache Ranger i HDP 2,2](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/).

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
