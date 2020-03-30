---
title: Watchdog BUG mjuk låsning CPU-fel från Azure HDInsight kluster
description: Watchdog BUG mjuk låsning CPU visas i kärnan syslogs från Azure HDInsight kluster
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 701e314ad2a3762b1e8ca022ce18d9435ce2db37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894112"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Scenario: "watchdog: BUG: soft lockup - CPU" fel från en Azure HDInsight kluster

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Kernel syslogs innehåller felmeddelandet: `watchdog: BUG: soft lockup - CPU`.

## <a name="cause"></a>Orsak

En [bugg](https://bugzilla.kernel.org/show_bug.cgi?id=199437) i Linux Kernel orsakar CPU mjuka låsningar.

## <a name="resolution"></a>Lösning

Använd kernel patch. Skriptet nedan uppgraderar Linux-kärnan och startar om maskinerna vid olika tidpunkter under 24 timmar. Kör skriptåtgärden i två batchar. Den första batchen finns på alla noder utom huvudnod. Den andra batchen är på huvudnod. Kör inte på huvudnod och andra noder samtidigt.

1. Navigera till ditt HDInsight-kluster från Azure-portalen.

1. Gå till skriptåtgärder.

1. Välj **Skicka nytt** och ange indata enligt följande

    | Egenskap | Värde |
    | --- | --- |
    | Skripttyp | -Anpassad |
    | Namn |Fix för kernel soft lock problem |
    | Bash skript URI |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Nodtyper |Arbetare, Zookeeper |
    | Parametrar |Ej tillämpligt |

    Välj **Förstå den här skriptåtgärden...** om du vill att skriptet ska köras när nya noder läggs till.

1. Välj **Skapa**.

1. Vänta tills körningen lyckas.

1. Kör skriptåtgärden på head-noden genom att följa samma steg som steg 3, men den här gången med nodtyper: Head.

1. Vänta tills körningen lyckas.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
