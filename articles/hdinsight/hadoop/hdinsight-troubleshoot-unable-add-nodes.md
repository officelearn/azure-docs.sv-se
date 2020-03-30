---
title: Det går inte att lägga till noder i Azure HDInsight-klustret
description: Felsöka varför det inte går att lägga till noder i Apache Hadoop-kluster i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 97d7f34fff324a9959292460e534c15110c3e532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894085"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Scenario: Det går inte att lägga till noder i Azure HDInsight-klustret

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Det går inte att lägga till noder i Azure HDInsight-klustret.

## <a name="cause"></a>Orsak

Orsakerna kan variera.

## <a name="resolution"></a>Lösning

Beräkna antalet ytterligare kärnor som behövs för klustret med hjälp av funktionen [Klusterstorlek.](../hdinsight-scaling-best-practices.md) Detta baseras på det totala antalet kärnor i de nya arbetsnoderna. Försök sedan med ett eller flera av följande steg:

* Kontrollera om det finns några kärnor tillgängliga på klustrets plats.

* Ta en titt på hur många tillgängliga kärnor som finns på andra platser. Överväg att återskapa ditt kluster på en annan plats med tillräckligt många tillgängliga kärnor.

* Om du vill öka kärnkvoten för en specifik plats skapar du ett supportärende för en HDInsight-kärnkvotökning.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
