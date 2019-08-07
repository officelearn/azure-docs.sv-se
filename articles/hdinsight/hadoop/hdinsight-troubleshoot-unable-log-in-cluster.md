---
title: Det går inte att logga in på Azure HDInsight-kluster
description: Det går inte att logga in på Azure HDInsight-kluster
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/31/2019
ms.openlocfilehash: eb7249fc88f37e5c15447e5a8907468a851d2416
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737451"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Scenario: Det går inte att logga in på Azure HDInsight-kluster

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Det går inte att logga in på Azure HDInsight-kluster.

## <a name="cause"></a>Orsak

Anledningen kan variera. Tänk på att när du loggar in på kluster-eller app-instrumentpaneler använder du din "kluster inloggning" eller HTTP-autentiseringsuppgifter. När du fjärransluter använder du dina autentiseringsuppgifter för Secure Shell (SSH) eller fjärrskrivbordet.

## <a name="resolution"></a>Lösning

Prova ett eller flera av följande steg för att lösa vanliga problem.

* Försök att öppna instrument panelen för klustret i en ny webb läsar flik i sekretess läge.

* Om du inte kan återkalla dina SSH-autentiseringsuppgifter kan du [återställa autentiseringsuppgifterna i Ambari-användargränssnittet](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
