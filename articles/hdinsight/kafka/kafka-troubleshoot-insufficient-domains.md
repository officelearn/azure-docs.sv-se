---
title: Otillräckligt antal fel domäner i regions fel i Azure HDInsight
description: Det gick inte att skapa kluster på grund av otillräckliga fel domäner i region i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: defb0666a6537d62b22dca301f69a5163e887d3f
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241901"
---
# <a name="scenario-cluster-creation-failed-due-to-not-sufficient-fault-domains-in-region-in-azure-hdinsight"></a>Scenario: det gick inte att skapa kluster på grund av `not sufficient fault domains in region` i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Få ett fel meddelande som liknar `not sufficient fault domains in region` när du försöker skapa Apache Kafka-kluster.

## <a name="cause"></a>Orsak

En feldomän är en logisk gruppering av underliggande maskinvara i ett Azure-datacenter. Varje feldomän delar en gemensam strömkälla och nätverksbrytare. De virtuella datorer och hanterade diskar som implementerar noderna i ett HDInsight-kluster är fördelade mellan dessa feldomäner. Den här arkitekturen begränsar de potentiella problemen vid fysiska maskinvarufel.

Varje Azure-region har ett visst antal feldomäner. En lista över domäner och antalet fel domäner som de innehåller finns i dokumentationen om [tillgänglighets uppsättningar](../../virtual-machines/windows/manage-availability.md).

I HDInsight måste Kafka-kluster vara etablerade i en region med minst tre fel domäner.

## <a name="resolution"></a>Upplösning

Om den region som du vill skapa klustret inte har tillräckligt med fel domäner kan du kontakta produkt teamet för att tillåta etablering av klustret även om det inte finns tre fel domäner.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
