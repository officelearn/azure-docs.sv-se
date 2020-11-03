---
title: Fel vid nekad åtkomst med Apache Hive tabell i Azure HDInsight
description: Fel vid nekad åtkomst vid försök att skapa en Apache Hive tabell i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: b6e71f5f2c389926a2e6267fadeedebdce1c51d9
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288898"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>Scenario: fel vid nekad åtkomst vid försök att skapa en Apache Hive tabell i Azure HDInsight

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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]