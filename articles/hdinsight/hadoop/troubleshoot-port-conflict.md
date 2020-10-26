---
title: Port konflikt vid start av tjänster i Azure HDInsight
description: Fel söknings steg och möjliga lösningar på problem med port konflikter när du interagerar med Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: c07cddb0999e6f3424527828be4b10ed0168a2ff
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533333"
---
# <a name="scenario-port-conflict-when-starting-services-in-azure-hdinsight"></a>Scenario: port konflikt vid start av tjänster i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Det går inte att starta en tjänst.

## <a name="cause"></a>Orsak

Det finns en port konflikt.

## <a name="resolution"></a>Lösning

### <a name="method-1"></a>Metod 1

Använd följande kommandon för att hämta/avsluta alla processer som körs, vilka påverkas av port problemet.

```bash
netstat -lntp | grep <port>
ps -ef | grep <service>
kill -9 <service>
```

Starta sedan tjänsten.

### <a name="method-2"></a>Metod 2

Starta om noden.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).