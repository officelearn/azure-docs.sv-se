---
title: Det går inte att läsa Apache Yarn-loggen i Azure HDInsight
description: Felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776201"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Scenario: Det går inte att läsa Apache Yarn-loggen i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Apache-garnloggarna som hittades från lagringskontot kan inte läsas av människor. Filtolkaren fungerar inte och ger följande felmeddelande:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Orsak

Apache-garnloggen aggregeras till `IndexFile` format, vilket inte stöds av filtolkaren.

## <a name="resolution"></a>Lösning

1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net`, var `CLUSTERNAME` är namnet på klustret.

1. Från Ambari UI, navigera till **YARN** > **Configs** > **Advanced** > **Advanced garn-site**.

1. För WASB-lagring: Standardvärdet för `yarn.log-aggregation.file-formats` är `IndexedFormat,TFile`. Ändra värdet `TFile`till .

1. För ADLS-lagring: Standardvärdet för `yarn.nodemanager.log-aggregation.compression-type` är `gz`. Ändra värdet `none`till .

1. Spara ändringen och starta om alla tjänster som påverkas.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
