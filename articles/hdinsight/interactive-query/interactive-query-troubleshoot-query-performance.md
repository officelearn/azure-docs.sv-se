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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895130"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Scenario: Dåliga prestanda i Apache Hive LLAP-frågor i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du använder interaktiva frågekomponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Standardklusterkonfigurationerna är inte tillräckligt inställda för din arbetsbelastning. Frågor i Hive LLAP körs långsammare än förväntat.

## <a name="cause"></a>Orsak

Detta kan hända på grund av en mängd olika skäl.

## <a name="resolution"></a>Lösning

LLAP är optimerad för frågor som involverar kopplingar och aggregat. Frågor som följande fungerar inte bra i ett interaktivt Hive-kluster:

```
select * from table where column = "columnvalue"
```

Om du vill förbättra punktfrågeprestandan i Hive LLAP ställer du in följande konfigurationer:

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

Du kan också öka användningen av LLAP-cachen för att förbättra prestanda med följande konfigurationsändring:

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
