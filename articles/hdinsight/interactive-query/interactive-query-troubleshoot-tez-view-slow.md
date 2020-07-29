---
title: Apache Ambari Tez View läses långsamt i Azure HDInsight
description: Apache Ambari Tez View kan läsas in långsamt eller kan inte läsas in alls i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: f4b1ffbc1e5b8147279d1e0320bd5f55aec90ea2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75895095"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Scenario: Apache Ambari Tez View läses långsamt i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem vid användning av interaktiva frågekomponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Apache Ambari Tez-vyn kan läsas in långsamt eller kan inte läsas in alls. När du läser in Ambari Tez-vyn kan du se att processer på huvudnoderna slutar svara.

## <a name="cause"></a>Orsak

Åtkomst till garn ATS-API: er kan ibland ha dåliga prestanda på kluster som skapats före okt 2017 när klustret har ett stort antal Hive-jobb som körs på det.

## <a name="resolution"></a>Lösning

Detta är ett problem som har åtgärd ATS i okt 2017. Om du återskapar klustret kommer det inte att köras i det här problemet igen.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
