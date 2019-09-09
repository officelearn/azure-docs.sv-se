---
title: Det går inte att logga in på Azure HDInsight-kluster
description: Felsök varför det inte går att logga in på Apache Hadoop kluster i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/31/2019
ms.openlocfilehash: bd020576f6939ad195c73ebe390d370d0319b5f9
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810527"
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
