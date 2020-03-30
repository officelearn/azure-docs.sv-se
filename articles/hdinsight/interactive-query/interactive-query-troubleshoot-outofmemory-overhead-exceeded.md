---
title: Kopplingar i Apache Hive leder till OutOfMemory-fel - Azure HDInsight
description: Hantering av OutOfMemory fel "GC overhead gräns överskridit fel"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: ab334dfb15044fd0734a107c12003ca2c1f86906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895177"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Scenario: Kopplingar i Apache Hive leder till ett OutOfMemory-fel i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du använder interaktiva frågekomponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Standardbeteendet för Apache Hive-kopplingar är att läsa in hela innehållet i en tabell i minnet så att en koppling kan utföras utan att behöva utföra ett steg för karta/minska. Om Hive-tabellen är för stor för att få plats i minnet kan frågan misslyckas.

## <a name="cause"></a>Orsak

NÃ¤ndevÃ¤ndevÃ¤ndevÃ¤ndevÃ¤ndevÃ¤ndevÃ¤ndevÃ¤ndevÃ¤ndes i datafilen av

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Lösning

Förhindra att Hive läser in tabeller i minnet på kopplingar (i stället utför ett map/reduce-steg) genom att ange följande Hive-konfigurationsvärde:

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>Nästa steg

Om det här värdet inte löste problemet besöker du något av följande...

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
