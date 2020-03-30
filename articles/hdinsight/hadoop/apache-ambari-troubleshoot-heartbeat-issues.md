---
title: Problem med pulsslag från Apache Ambari i Azure HDInsight
description: Granskning av olika orsaker till Apache Ambari pulsslagsproblem i Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: ab88f65d535be2aef5f0b26fa1171c03276466e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057081"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problem med pulsslag från Apache Ambari i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="scenario-high-cpu-utilization"></a>Scenario: Hög CPU-användning

### <a name="issue"></a>Problem

Ambari agent har hög CPU-användning, vilket resulterar i varningar från Ambari UI att för vissa noder Ambari agent hjärtslag går förlorad. Den förlorade aviseringen av pulsslag är vanligtvis övergående.

### <a name="cause"></a>Orsak

På grund av olika ambari-agent buggar, i sällsynta fall, din ambari-agent kan ha hög (nära 100) procent CPU-användning.

### <a name="resolution"></a>Lösning

1. Identifiera process-ID (pid) av ambari-agent:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Kör sedan följande kommando för att visa processoranvändningen:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Starta om ambari-agenten för att minska problemet:

    ```bash
    service ambari-agent restart
    ```

1. Om omstart inte fungerar, döda ambari-agentprocessen och starta den sedan:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Scenario: Ambari-agenten har inte startat

### <a name="issue"></a>Problem

Ambari-agenten har inte startat vilket resulterar i aviseringar från Ambari UI om att Ambari-agentens pulsslag går förlorade för vissa noder.

### <a name="cause"></a>Orsak

Aviseringarna orsakas av att Ambari-agenten inte körs.

### <a name="resolution"></a>Lösning

1. Bekräfta status för ambari-agent:

    ```bash
    service ambari-agent status
    ```

1. Bekräfta om redundanskontrolltjänsten körs:

    ```bash
    ps -ef | grep failover
    ```

    Om redundansstyrenhetstjänster inte körs beror det troligen på ett problem att hdinsight-agenten startar redundansstyrenheten. Kontrollera hdinsight-agent `/var/log/hdinsight-agent/hdinsight-agent.out` loggen från filen.

## <a name="scenario-heartbeat-lost-for-ambari"></a>Scenario: Hjärtslag förlorade för Ambari

### <a name="issue"></a>Problem

Ambari hjärtslag agent förlorades.

### <a name="cause"></a>Orsak

OMS-loggar orsakar hög CPU-användning.

### <a name="resolution"></a>Lösning

* Inaktivera Azure Monitor-loggning med hjälp av [Disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) PowerShell-cmdlet.
* Ta `mdsd.warn` bort loggfilen

---

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
