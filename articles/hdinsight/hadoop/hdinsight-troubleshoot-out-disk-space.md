---
title: Klusternoden tar slut på disk utrymme i Azure HDInsight
description: Fel sökning av problem med disk utrymmet Apache Hadoop klusternoden i Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/05/2019
ms.openlocfilehash: 139fb0e77f8f6960e7b13899f9586dd78bf46a92
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735850"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Scenario: Klusternoden tar slut på disk utrymme i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Ett jobb kan Miss lyckas med ett fel meddelande som liknar:`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Eller så kan du få Apache Ambari-aviseringar `local-dirs usable space is below configured utilization percentage`som liknar:.

## <a name="cause"></a>Orsak

Apache garn Application cache kan ha använt allt tillgängligt disk utrymme. Spark-programmet körs förmodligen ineffektivt.

## <a name="resolution"></a>Lösning

1. Använd Ambari UI för att avgöra vilken nod som håller på att ta slut på disk utrymme.

1. Bestäm vilken mapp i oroande-noden som bidrar till det mesta av disk utrymmet. Använd `df` först SSH till noden och sedan Visa disk användning för alla monteringar. Det är `/mnt` vanligt vis en temporär disk som används av oss. Du kan ange i en mapp och sedan skriva `sudo du -hs` för att Visa sammanfattade fil storlekar under en mapp. Om du ser en mapp liknande `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`innebär det att programmet fortfarande körs. Detta kan bero på RDD beständighet eller mellanliggande blandade filer.

1. Du kan åtgärda problemet genom att avsluta programmet, vilket frigör disk utrymme som används av programmet.

1. För att avsluta problemet kan du optimera ditt program.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
