---
title: Det går inte att lägga till noder i Azure HDInsight-kluster
description: Felsök varför det inte går att lägga till noder i Apache Hadoop kluster i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: b8e8bf0a8a0555b23799600c3958fbbfd5a510dd
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540575"
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

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).