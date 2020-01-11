---
title: Problem med Apache Ambari-pulsslag i Azure HDInsight
description: Granskning av olika orsaker till problem med Apache Ambari-pulsslag i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 09/11/2019
ms.openlocfilehash: ae5cfcfcd394aab644b35ac66aafa213dc49dd42
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895392"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problem med Apache Ambari-pulsslag i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="scenario-high-cpu-utilization"></a>Scenario: hög processor användning

### <a name="issue"></a>Problem

Ambari-agenten har hög processor användning, vilket resulterar i aviseringar från Ambari-ANVÄNDARGRÄNSSNITTET som för vissa noder går Ambari agent-pulsslag förlorat. Aviseringen om förlorat pulsslag är vanligt vis tillfällig. 

### <a name="cause"></a>Orsak

På grund av olika Ambari-buggar, i sällsynta fall, kan din Ambari-agent ha hög (nära 100) processor användning i procent.

### <a name="resolution"></a>Upplösning

1. Identifiera process-ID (PID) för Ambari-agent:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Kör sedan följande kommando för att visa processoranvändningen:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Starta om Ambari – agent för att undvika problem:

    ```bash
    service ambari-agent restart
    ```

1. Om starta om inte fungerar, avsluta Ambari-agent-processen och starta sedan den:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Scenario: Ambari-agenten har inte startat

### <a name="issue"></a>Problem

Ambari-agenten har inte startat som resulterar i aviseringar från Ambari-gränssnittet för vissa noder. Ambari agent-pulsslag förloras.

### <a name="cause"></a>Orsak

Aviseringarna orsakas av att Ambari-agenten inte körs.

### <a name="resolution"></a>Upplösning

1. Bekräfta status för Ambari-agent:

    ```bash
    service ambari-agent status
    ```

1. Bekräfta om tjänster för redundans körs:

    ```bash
    ps -ef | grep failover
    ```

    Om tjänster för redundansväxling inte körs beror det förmodligen på ett problem som förhindrar att HDInsight-agenten startar redundansväxling. Kontrol lera HDInsight-agentens logg från `/var/log/hdinsight-agent/hdinsight-agent.out`-filen.

---

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
