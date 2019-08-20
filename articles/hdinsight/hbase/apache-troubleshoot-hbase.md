---
title: Felsöka HBase med Azure HDInsight
description: Få svar på vanliga frågor om att arbeta med HBase och Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 595acdc46aca76a86038acebdb9a7581c51e3688
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69573955"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Felsöka Apache HBase med hjälp av Azure HDInsight

Lär dig mer om de vanligaste problemen och deras lösningar när du arbetar med Apache HBase-nyttolaster i Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Hur gör jag för att köra kommando rapporter för hbck med flera otilldelade regioner?

Ett vanligt fel meddelande som kan visas när du kör `hbase hbck` kommandot är "flera regioner som inte är tilldelade eller hål i kedjan av regioner".

I HBase Master användar gränssnitt kan du se antalet regioner som inte är balanserade över alla region servrar. Sedan kan du köra `hbase hbck` kommandot för att se hålen i region kedjan.

Hålen kan ha orsakats av offline-regionerna, så åtgärda tilldelningarna först. 

Utför följande steg för att ta tillbaka de otilldelade regionerna till ett normalt tillstånd:

1. Logga in på HDInsight HBase-klustret med hjälp av SSH.
2. Kör `hbase zkcli` kommandot för att ansluta till Apache ZooKeeper Shell.
3. `rmr /hbase/regions-in-transition` Kör kommandot`rmr /hbase-unsecure/regions-in-transition` eller kommandot.
4. Om du vill avsluta `hbase zkcli` gränssnittet använder du `exit` kommandot.
5. Öppna Apache Ambari-ANVÄNDARGRÄNSSNITTET och starta om tjänsten Active HBase Master.
6. `hbase hbck` Kör kommandot igen (utan några alternativ). Kontrol lera utdata för kommandot för att säkerställa att alla regioner tilldelas.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Hur gör jag för att åtgärda timeout-problem när du använder hbck-kommandon för områdes tilldelningar?

### <a name="issue"></a>Problem

En möjlig orsak till timeout-problem när du använder `hbck` kommandot kan vara att flera regioner är i läget "i över gång" under en längre tid. Du kan se dessa regioner som offline i HBase Master gränssnittet. Eftersom ett stort antal regioner försöker över gången kan HBase Master tids gräns och inte kunna ta tillbaka dessa regioner online igen.

### <a name="resolution-steps"></a>Lösningsanvisningar

1. Logga in på HDInsight HBase-klustret med hjälp av SSH.
2. Kör `hbase zkcli` kommandot för att ansluta till Apache ZooKeeper Shell.
3. `rmr /hbase/regions-in-transition` Kör kommandot`rmr /hbase-unsecure/regions-in-transition` eller.
4. Om du vill `hbase zkcli` avsluta gränssnittet använder du `exit` kommandot.
5. Starta om tjänsten Active HBase Master i Ambari-ANVÄNDARGRÄNSSNITTET.
6. `hbase hbck -fixAssignments` Kör kommandot igen.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
