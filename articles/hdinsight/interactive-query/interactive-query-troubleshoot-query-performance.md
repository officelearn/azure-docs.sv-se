---
title: Dåliga prestanda i Apache Hive LLAP-frågor i Azure HDInsight
description: Frågor i Apache Hive LLAP körs långsammare än förväntat i Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8bd20849b15f8c8d5a14653f702f78c6404d82e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895130"
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

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
