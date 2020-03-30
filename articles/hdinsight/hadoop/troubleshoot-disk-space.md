---
title: Hantera diskutrymme i Azure HDInsight
description: Felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 577bed7ce342be14a50077a3ffd841cd901b5b31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77473017"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Hantera diskutrymme i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="hive-log-configurations"></a>Hive-loggkonfigurationer

1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net`, var `CLUSTERNAME` är namnet på klustret.

1. Navigera till **Hive** > **Configs** > **Advanced** > Advanced**hive-log4j**. Läs följande inställningar:

    * `hive.root.logger=DEBUG,RFA`. Det här är standardvärdet, ändra `INFO` [loggnivån](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) så att du vill skriva ut färre loggposter.

    * `log4jhive.log.maxfilesize=1024MB`. Detta är standardvärdet, ändra som önskat.

    * `log4jhive.log.maxbackupindex=10`. Detta är standardvärdet, ändra som önskat. Om parametern har utelämnats kommer de genererade loggfilerna att vara oändliga.

## <a name="yarn-log-configurations"></a>Konfigurationer av garnlogg

Granska följande konfigurationer:

* Apache Ambari

    1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net`, var `CLUSTERNAME` är namnet på klustret.

    1. Navigera till **Hive** > **Configs** > **Advanced** > Resource**Manager**. Kontrollera att **aktivera loggaggregering** är markerad. Om det inaktiveras behåller namnnoderna loggarna lokalt och aggregeras inte i fjärrarkivet när programmet har slutförts eller avslutats.

* Kontrollera att klusterstorleken är lämplig för arbetsbelastningen. Arbetsbelastningen kan ha ändrats nyligen eller så kan klustret ha ändrats. [Skala upp](../hdinsight-scaling-best-practices.md) klustret så att det matchar en högre arbetsbelastning.

* `/mnt/resource`kan fyllas med överblivna filer (som i fallet med omstart av resurshanteraren). Om det behövs, `/mnt/resource/hadoop/yarn/log` `/mnt/resource/hadoop/yarn/local`manuellt rengöra och .

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
