---
title: Timeout med kommandot "HBase hbck" i Azure HDInsight
description: Timeout-fel med kommandot HBase hbck vid korrigering av region tilldelningar
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/01/2019
ms.openlocfilehash: 4f3f1c22fa1dc05a66a8b6bf0179903a44cef9b6
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737932"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Scenario: Timeout med kommandot "HBase hbck" i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Utlöser timeout med `hbase hbck` kommando när du korrigerar områdes tilldelningar.

## <a name="cause"></a>Orsak

Den potentiella orsaken kan vara flera regioner under läget "i över gång" under en längre tid. Dessa regioner kan ses som offline från Apache HBase Master UI. På grund av ett stort antal regioner som försöker över gången kan HBase Master tids gräns och de kommer inte att gå tillbaka till online-tillstånd.

## <a name="resolution"></a>Lösning

1. Logga in på HDInsight HBase-kluster med SSH.

1. Kör `hbase zkcli` kommando för att ansluta till Zookeeper-gränssnittet.

1. Kör `rmr /hbase/regions-in-transition` eller`rmr /hbase-unsecure/regions-in-transition` -kommando.

1. Stäng från `hbase zkcli` Shell med hjälp `exit` av kommandot.

1. Öppna Ambari-ANVÄNDARGRÄNSSNITTET och starta om Active HBase Master-tjänsten från Ambari.

1. Övervaka den HBase Master användar gränssnitts region "i över gång" som avsnittet för att se till att ingen region fastnar.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
