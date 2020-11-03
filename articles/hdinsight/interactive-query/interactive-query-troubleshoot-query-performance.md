---
title: Dåliga prestanda i Apache Hive LLAP-frågor i Azure HDInsight
description: Frågor i Apache Hive LLAP körs långsammare än förväntat i Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: ee7ce401f889dd9c06b14860f4fc9674c5350b52
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288881"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Scenario: dåliga prestanda i Apache Hive LLAP-frågor i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem vid användning av interaktiva frågekomponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Standardkonfigurationerna för klustret är inte tillräckligt justerade för din arbets belastning. Frågor i Hive-LLAP körs långsammare än förväntat.

## <a name="cause"></a>Orsak

Detta kan bero på en mängd olika orsaker.

## <a name="resolution"></a>Lösning

LLAP är optimerat för frågor som involverar kopplingar och agg regeringar. Frågor som liknar följande fungerar inte bra i ett interaktivt Hive-kluster:

```
select * from table where column = "columnvalue"
```

Om du vill förbättra prestandan för punkt frågor i Hive-LLAP anger du följande konfigurationer:

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

Du kan också öka användningen av LLAP-cachen för att förbättra prestanda med följande konfigurations ändring:

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]