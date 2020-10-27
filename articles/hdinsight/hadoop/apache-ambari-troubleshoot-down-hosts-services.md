---
title: Apache Ambari-ANVÄNDARGRÄNSSNITTET visar värdar och tjänster i Azure HDInsight
description: Felsöka ett Apache Ambari UI-problem när du visar värdar och tjänster i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: e530d59feec2446679dbbfbc944e1bd0c53f3239
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546457"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Scenario: Apache Ambari UI visar värdar och tjänster i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Apache Ambari UI är tillgängligt, men användar gränssnittet visar att nästan alla tjänster är inaktiverade, alla värdar som visar pulsslag förlorat.

## <a name="cause"></a>Orsak

I de flesta fall är detta ett problem med att Ambari-servern inte körs på den aktiva huvudnoden. Kontrol lera vilken huvudnoden som är aktiv huvudnoden och se till att Ambari-servern körs på rätt skrivare. Starta inte Ambari manuellt-Server, låt tjänsten för redundans vara ansvarig för att starta Ambari-servern på rätt huvudnoden. Starta om den aktiva huvudnoden för att framtvinga en redundansväxling.

Nätverks problem kan också orsaka det här problemet. Se om du kan pinga från varje klusternod `headnodehost` . Det finns en sällsynt situation där ingen klusternod kan ansluta till `headnodehost` :

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Lösning

Vanligt vis minimerar du det här problemet genom att starta om den aktiva huvudnoden. Kontakta HDInsight-supporten om du inte vill.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).