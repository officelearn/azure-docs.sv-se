---
title: InvalidNetworkSecurityGroupSecurityRules-fel – Azure HDInsight
description: Det går inte att skapa kluster med felkoden InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: a73e1e9f7a9c017dd29b627a24c25ae2e064c0a9
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894150"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Scenario: InvalidNetworkSecurityGroupSecurityRules-kluster kan inte skapas i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Du får felkoden `InvalidNetworkSecurityGroupSecurityRules` med en beskrivning som liknar "säkerhets reglerna i den nätverks säkerhets grupp som kon figurer ATS med undernät tillåter inte obligatorisk inkommande och/eller utgående anslutning."

## <a name="cause"></a>Orsak

Troligen ett problem med de regler för inkommande [nätverks säkerhets grupper](../../virtual-network/virtual-network-vnet-plan-design-arm.md) som kon figurer ATS för klustret.

## <a name="resolution"></a>Upplösning

Gå till Azure Portal och identifiera den NSG som är associerad med under nätet där klustret distribueras. I avsnittet **inkommande säkerhets regler** kontrollerar du att reglerna tillåter inkommande åtkomst till port 443 för de IP-adresser som anges [här](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip).

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
