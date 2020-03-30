---
title: Klusternoden får på diskutrymme i Azure HDInsight
description: Felsöka problem med Apache Hadoop-klusternoddiskutrymme i Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: fbfd82473b68f5032d19834ac809191d498a5a67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894132"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Scenario: Klusternoden får på diskutrymme i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Ett jobb kan misslyckas med felmeddelande som liknar:`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Eller så kan du få Apache `local-dirs usable space is below configured utilization percentage`Ambari varning som liknar: .

## <a name="cause"></a>Orsak

Apache Yarn-programcache kan ha förbrukat allt tillgängligt diskutrymme. Ditt Spark-program körs troligen ineffektivt.

## <a name="resolution"></a>Lösning

1. Använd Ambari UI för att avgöra vilken nod som håller på att ta på diskutrymme.

1. Bestäm vilken mapp i den oroande noden som bidrar till det mesta av diskutrymmet. SSH till noden först `df` och kör sedan för att lista diskanvändning för alla fästen. Vanligtvis är `/mnt` det som är en temp disk som används av OSS. Du kan skriva in i `sudo du -hs` en mapp och sedan skriva för att visa summerade filstorlekar under en mapp. Om du ser en `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`mapp som liknar betyder det att programmet fortfarande körs. Detta kan bero på RDD persistens eller mellanliggande shuffle-filer.

1. För att minska problemet, döda programmet, som kommer att frigöra diskutrymme som används av det programmet.

1. Optimera programmet för att lösa problemet i slutändan.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
