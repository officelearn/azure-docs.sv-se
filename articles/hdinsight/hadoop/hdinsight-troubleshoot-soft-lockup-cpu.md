---
title: Övervaknings fel i övervaknings enheten fel vid mjuk låsning från Azure HDInsight-kluster
description: Övervaknings-CPU fel för mjuk låsning visas i kernel-syslogs från Azure HDInsight-kluster
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 701e314ad2a3762b1e8ca022ce18d9435ce2db37
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75894112"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Scenario: "övervaknings enhet: fel: mjuk låsning – CPU" fel från ett Azure HDInsight-kluster

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Kernel-syslog innehåller fel meddelandet: `watchdog: BUG: soft lockup - CPU`.

## <a name="cause"></a>Orsak

En [bugg](https://bugzilla.kernel.org/show_bug.cgi?id=199437) i Linux-kernel orsakar mjuka CPU-låsningar.

## <a name="resolution"></a>Lösning

Tillämpa kernel-korrigering. Skriptet nedan uppgraderar Linux-kärnan och startar om datorerna vid olika tidpunkter under 24 timmar. Kör skript åtgärden i två batchar. Den första batchen finns på alla noder utom Head-noden. Den andra batchen finns på Head-noden. Kör inte på Head-noden och andra noder på samma tid.

1. Navigera till ditt HDInsight-kluster från Azure Portal.

1. Gå till skript åtgärder.

1. Välj **Skicka ny** och ange indatamängden enligt följande

    | Egenskap | Värde |
    | --- | --- |
    | Skript typ | – Anpassad |
    | Name |Korrigering för problem med tyst kernel-lås |
    | Bash-skript-URI |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Node-typ (er) |Arbetare, Zookeeper |
    | Parametrar |Ej tillämpligt |

    Välj **Spara den här skript åtgärden...** om du vill köra skriptet när nya noder läggs till.

1. Välj **Skapa**.

1. Vänta tills körningen har slutförts.

1. Kör skript åtgärden på Head-noden genom att följa samma steg som steg 3, men den här gången med Node Types: Head.

1. Vänta tills körningen har slutförts.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
