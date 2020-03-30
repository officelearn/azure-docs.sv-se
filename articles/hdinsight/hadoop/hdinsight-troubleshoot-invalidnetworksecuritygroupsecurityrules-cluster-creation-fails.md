---
title: InvalidNetworkSecurityGroupSecurityRules-fel - Azure HDInsight
description: Klusterskapande misslyckas med ErrorCode InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: a73e1e9f7a9c017dd29b627a24c25ae2e064c0a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894150"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Scenario: InvalidNetworkSecurityGroupSecurityRules – klusterskapande misslyckas i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Felkod `InvalidNetworkSecurityGroupSecurityRules` med en beskrivning som liknar "Säkerhetsreglerna i nätverkssäkerhetsgruppen som konfigurerats med undernät tillåter inte obligatorisk inkommande och/eller utgående anslutning".

## <a name="cause"></a>Orsak

Sannolikt ett problem med de regler för inkommande [nätverkssäkerhetsgruppen](../../virtual-network/virtual-network-vnet-plan-design-arm.md) som konfigurerats för klustret.

## <a name="resolution"></a>Lösning

Gå till Azure-portalen och identifiera NSG som är associerad med undernätet där klustret distribueras. I avsnittet **Inkommande säkerhetsregler** kontrollerar du att reglerna tillåter inkommande åtkomst till port 443 för IP-adresser som nämns [här](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip).

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
