---
title: Det går inte att logga in på Zeppelin i Azure HDInsight
description: Det går inte att logga in på Zeppelin i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/12/2019
ms.openlocfilehash: 14f4cd7588405dbf351124b1e469fd96f9b38274
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977409"
---
# <a name="scenario-unable-to-sign-in-to-apache-zeppelin-in-azure-hdinsight"></a>Scenario: Det går inte att logga in på Apache Zeppelin i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Det går inte att logga in på Apache Zeppelin när du har ändrat lösen ordet för tillägg i Active Directory.

## <a name="cause"></a>Orsak

Användaren som nämns i `activeDirectoryRealm.systemUsername` `shiro_ini` filen har ändrat Active Directory-lösenordet.

## <a name="resolution"></a>Lösning

1. Kontrol lera att det ändrade lösen ordet är rotor saken genom `activeDirectoryRealm.systemPassword = <new password>` att inkludera i `shiro_ini` Zeppelin-konfigurationen i Ambari. Ta bort `activeDirectoryRealm.hadoopSecurityCredentialPath` inställningen. Nedan är platsen för `shiro ini`.

    ![Shiro](./media/domain-joined-zeppelin-signin/shiro.png)

1. Om användarna nu kan logga in på Zeppelin efter steg 1, skapar du en `jceks` ny fil med det nya lösen ordet och `activeDirectoryRealm.hadoopSecurityCredentialPath` ersätter med den nya filen.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
