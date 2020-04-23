---
title: Felsöka fel i resurs skapande i Azure HDInsight
description: Vanliga fel och lösningar för problem med kapacitets problem finns i den här artikeln.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/22/2020
ms.openlocfilehash: 871e6b1253b0fc9a5b6ae5d3389982cfdd6af3bd
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103183"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>Felsöka fel i resurs skapande i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight.

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Fel: distributionen skulle överskrida kvoten på 800

Azure har en kvot på 800 distributioner per resursgrupp. Olika kvoter tillämpas per resurs grupp, prenumeration, konto eller andra omfång. Din prenumeration kan till exempel konfigureras så att antalet kärnor för en region begränsas. Försök att distribuera en virtuell dator med fler kärnor än tillåtna resultat i fel meddelande som anger att kvoten överskreds.

Lös problemet genom att ta bort de distributioner som inte längre behövs med hjälp av Azure Portal, CLI eller PowerShell.

Mer information finns i [Åtgärda fel med resurskvoter](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Fel: den maximala noden överskred de tillgängliga kärnorna i den här regionen

Din prenumeration kanske har konfigurerats så att antalet kärnor för en region begränsas. Försök att distribuera en resurs med fler kärnor än tillåtet resultat i fel meddelande som anger att kvoten överskreds.

Följ dessa steg om du vill begära en kvotökning:

1. Gå till [Azure Portal](https://portal.azure.com)och välj **Hjälp + Support**.

1. Välj **ny supportbegäran**.

1. På fliken **grundläggande** på sidan **ny supportbegäran** anger du följande information:

   * **Typ av problem:** Välj **tjänst-och prenumerations gränser (kvoter)**.
   * **Prenumeration:** Välj den prenumeration som du vill ändra.
   * **Kvot typ:** Välj **HDInsight**.

Mer information finns i [Skapa ett supportärende för att öka antalet kärnor](hdinsight-capacity-planning.md#quotas).

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
