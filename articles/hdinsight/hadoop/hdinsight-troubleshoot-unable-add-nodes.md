---
title: Det går inte att lägga till noder i Azure HDInsight-kluster
description: Felsök varför det inte går att lägga till noder i Apache Hadoop kluster i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 97d7f34fff324a9959292460e534c15110c3e532
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75894085"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Scenario: det går inte att lägga till noder i Azure HDInsight-kluster

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Det går inte att lägga till noder i Azure HDInsight-klustret.

## <a name="cause"></a>Orsak

Anledningen kan variera.

## <a name="resolution"></a>Lösning

Använd funktionen [kluster storlek](../hdinsight-scaling-best-practices.md) och beräkna antalet ytterligare kärnor som krävs för klustret. Detta baseras på det totala antalet kärnor i de nya arbetsnoderna. Prova sedan ett eller flera av följande steg:

* Kontrol lera om det finns några tillgängliga kärnor på klustrets plats.

* Ta en titt på hur många tillgängliga kärnor som finns på andra platser. Överväg att återskapa ditt kluster på en annan plats med tillräckligt många tillgängliga kärnor.

* Om du vill öka kärnkvoten för en specifik plats skapar du ett supportärende för en HDInsight-kärnkvotökning.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
