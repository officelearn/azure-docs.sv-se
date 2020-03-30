---
title: Apache Ambari Tez View laddas långsamt i Azure HDInsight
description: Apache Ambari Tez View kan läsas in långsamt eller laddas inte alls i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: f4b1ffbc1e5b8147279d1e0320bd5f55aec90ea2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895095"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Scenario: Apache Ambari Tez View laddas långsamt i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du använder interaktiva frågekomponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Apache Ambari Tez View kan laddas långsamt eller laddas inte alls. När du läser in Ambari Tez View kan du se processer på Headnodes blir svarar inte.

## <a name="cause"></a>Orsak

Åtkomst till YARN ATS API:er kan ibland ha dåliga prestanda i kluster som skapats före oktober 2017 när klustret har ett stort antal Hive-jobb som körs på det.

## <a name="resolution"></a>Lösning

Detta är ett problem som har åtgärdats i oktober 2017. Om du återskapar klustret kan det inte stöta på det här problemet igen.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
