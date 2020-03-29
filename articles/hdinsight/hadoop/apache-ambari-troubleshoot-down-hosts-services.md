---
title: Apache Ambari UI visar värdar och tjänster i Azure HDInsight
description: Felsöka ett Apache Ambari-gränssnittsproblem när det visar värdar och tjänster i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895641"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Scenario: Apache Ambari UI visar värdar och tjänster i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Apache Ambari UI är tillgänglig, men användargränssnittet visar nästan alla tjänster är nere, alla värdar som visar pulsslag förlorade.

## <a name="cause"></a>Orsak

I de flesta fall är detta ett problem med Ambari-servern som inte körs på den aktiva headnoden. Kontrollera vilken headnode som är den aktiva headnode och se till att din ambari-server körs på den rätta. Starta inte ambari-servern manuellt, låt redundansstyrenheten vara ansvarig för att starta ambari-servern på höger headnode. Starta om den aktiva headnoden för att tvinga fram en redundans.

Nätverksproblem kan också orsaka det här problemet. Från varje klusternod, se om `headnodehost`du kan pinga . Det finns en sällsynt situation där ingen `headnodehost`klusternod kan ansluta till:

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Lösning

Vanligtvis starta om den aktiva headnode kommer att minska detta problem. Om inte kontakta HDInsight supportteam.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
