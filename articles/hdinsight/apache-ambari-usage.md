---
title: Apache Ambari-användning i Azure HDInsight
description: Diskussion om hur Apache Ambari används i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 466c170985715be52a90d579c19ca23aefefe2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77067401"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Apache Ambari-användning i Azure HDInsight

HDInsight använder Apache Ambari för klusterdistribution och hantering. Ambari agenter körs på varje nod (headnode, arbetarnod, zookeeper och edgenode om det finns). Ambari-servern körs endast på headnode (hn0 eller hn1). Endast en instans av Ambari-servern ska köras samtidigt. Detta styrs av HDInsight redundansstyrenhet. När en av headnodes är nere för omstart eller underhåll, den andra headnode kommer att bli aktiv och Ambari server på den andra headnode kommer att startas.

All klusterkonfiguration bör göras via [Ambari-användargränssnittet,](./hdinsight-hadoop-manage-ambari.md)alla lokala ändringar skrivs över när noden startas om.

## <a name="failover-controller-services"></a>Redundansstyrenhetstjänster

HDInsight failover-styrenheten ansvarar också för att uppdatera IP-adressen för headnode-värden, vilket pekar på den aktuella aktiva huvudnoden. Alla Ambari-agenter är konfigurerade för att rapportera dess tillstånd och hjärtslag till headnodvärd. Redundansstyrenheten är en uppsättning tjänster som körs på varje nod i klustret, om de inte körs kanske den headnode-redundansen inte fungerar korrekt och du kommer att sluta med HTTP 502 när du försöker komma åt Ambari-servern.

För att kontrollera vilken headnode som är aktiv, är ett sätt att `ping headnodehost` ssh till en av noderna i klustret, sedan köra och jämföra IP med de två headnodes.

Om redundansstyrenhetstjänster inte körs kan det hända att redundansen headnode inte sker korrekt, vilket kan sluta med att Ambari-servern inte körs. Om du vill kontrollera om redundansstyrenhetstjänster körs kör du:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Loggar

På den aktiva headnode kan du kontrollera Ambari-serverns loggar på:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

På en nod i klustret kan du kontrollera Ambari-agentloggarna på:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Startsekvenser för tjänsten

Detta är sekvensen av tjänsten startar vid uppstart:

1. Hdinsight-agent startar redundansstyrenhetstjänster.
1. Redundansstyrenhetstjänster startar Ambari-agenten på varje nod och Ambari-server på aktiv headnode.

## <a name="ambari-database"></a>Ambari-databas

HDInsight skapar SQL Azure Database under huven för att fungera som databas för Ambari-servern. [Standardtjänstnivån är S0](../sql-database/sql-database-elastic-pool-scale.md).

För alla kluster med arbetarnodantal större än 16 när du skapar klustret är S2 databastjänstnivån.

## <a name="takeaway-points"></a>Takeaway poäng

Starta/stoppa aldrig ambari-server- eller ambari-agent-tjänster manuellt manuellt, såvida du inte försöker starta om tjänsten för att lösa ett problem. Om du vill tvinga fram en redundans kan du starta om den aktiva huvudnoden.

Aldrig manuellt ändra några konfigurationsfiler på någon klusternod, låt Ambari UI göra jobbet åt dig.

## <a name="next-steps"></a>Nästa steg

* [Hantera HDInsight-kluster med hjälp av Apache Ambari-webbgränssnittet](hdinsight-hadoop-manage-ambari.md)
* [Hantera HDInsight-kluster med hjälp av Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
