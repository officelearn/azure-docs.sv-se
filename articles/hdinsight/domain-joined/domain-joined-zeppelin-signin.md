---
title: Det går inte att logga in på Zeppelin i Azure HDInsight
description: Det går inte att logga in på Zeppelin i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/12/2019
ms.openlocfilehash: e9a81d458d1bab68bf94e9e9d0ebd87fac4580c8
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896154"
---
# <a name="scenario-unable-to-sign-in-to-apache-zeppelin-in-azure-hdinsight"></a>Scenario: det går inte att logga in på Apache Zeppelin i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Det går inte att logga in på Apache Zeppelin när du har ändrat lösen ordet för tillägg i Active Directory.

## <a name="cause"></a>Orsak

Användaren som nämns i `activeDirectoryRealm.systemUsername` av `shiro_ini`s filen ändrade Active Directory-lösenordet.

## <a name="resolution"></a>Upplösning

1. Kontrol lera att det ändrade lösen ordet är rotor saken genom att inkludera `activeDirectoryRealm.systemPassword = <new password>` i Zeppelin `shiro_ini` config i Ambari. Ta bort inställningen `activeDirectoryRealm.hadoopSecurityCredentialPath`. Här är platsen för `shiro ini`.

    ![Shiro](./media/domain-joined-zeppelin-signin/shiro.png)

1. Om användarna nu kan logga in på Zeppelin efter steg 1, skapar du en ny `jceks`-fil med det nya lösen ordet och ersätter `activeDirectoryRealm.hadoopSecurityCredentialPath` med den nya filen.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
