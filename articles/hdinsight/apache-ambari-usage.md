---
title: Apache Ambari-användning i Azure HDInsight
description: Diskussion om hur Apache Ambari används i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: e7c5ca1ce314c85162986591b2cd83907811bf72
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542394"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Apache Ambari-användning i Azure HDInsight

HDInsight använder Apache Ambari för kluster distribution och hantering. Ambari-agenter körs på varje nod (huvudnoden, arbetsnode, Zookeeper och edgenode om sådan finns). Ambari-servern körs bara på huvudnoden (hn0 eller HN1). Endast en instans av Ambari-servern ska köras samtidigt. Detta styrs av HDInsight-styrenheten för redundansväxling. När en av huvudnoderna är nere för omstart eller underhåll blir den andra huvudnoden aktiv och Ambari server på den andra huvudnoden startas.

All kluster konfiguration ska göras via Ambari- [användargränssnittet](./hdinsight-hadoop-manage-ambari.md), vilken lokal ändring som ska skrivas över när noden startas om.

## <a name="failover-controller-services"></a>Styrenhets tjänster för redundans

HDInsight-redundansväxlingen ansvarar också för att uppdatera IP-adressen för huvudnoden-värden, som pekar på den aktuella aktiva Head-noden. Alla Ambari-agenter har kon figurer ATS för att rapportera sitt tillstånd och pulsslag till huvudnoden-värden. Redundansväxlingen är en uppsättning tjänster som körs på varje nod i klustret, om de inte körs, kanske inte huvudnoden-redundansväxlingen fungerar som de ska och du kommer att få HTTP 502 när du försöker få åtkomst till Ambari-servern.

För att kontrol lera vilken huvudnoden som är aktiv, är ett sätt att använda SSH till en av noderna i klustret och sedan köra `ping headnodehost` och jämföra IP-adressen med den som finns i de två huvudnoderna.

Om tjänster för redundansväxling inte körs kanske huvudnoden redundans inte sker korrekt, vilket kan leda till att inte Ambari-servern körs. Så här kontrollerar du om styrenheter för redundans körs:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Loggar

På den aktiva huvudnoden kan du kontrol lera Ambari-serverns loggar på:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

På en nod i klustret kan du kontrol lera Ambari-agent loggarna på:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Start sekvenser för tjänsten

Detta är sekvensen av tjänst start vid start:

1. HDInsight-agenten startar reserv styrenhets tjänster.
1. Redundansväxlings styrenhets tjänster starta Ambari-agenten på varje nod och Ambari server på Active huvudnoden.

## <a name="ambari-database"></a>Ambari-databas

HDInsight skapar en databas i SQL Database under huven att fungera som databas för Ambari-servern. Standard [tjänst nivån är S0](../azure-sql/database/elastic-pool-scale.md).

För alla kluster med antalet arbetsnoder som är större än 16 när klustret skapas, är S2 databas tjänst nivån.

## <a name="takeaway-points"></a>Sak punkter

Starta/stoppa aldrig Ambari-Server-eller Ambari-Agent-tjänsterna manuellt, om du inte försöker starta om tjänsten för att lösa problemet. Om du vill framtvinga en redundansväxling kan du starta om den aktiva huvudnoden.

Ändra aldrig några konfigurationsfiler manuellt på alla klusternoder, låt Ambari-ANVÄNDARGRÄNSSNITTET göra jobbet åt dig.

## <a name="next-steps"></a>Nästa steg

* [Hantera HDInsight-kluster med hjälp av Apache Ambari-webbgränssnittet](hdinsight-hadoop-manage-ambari.md)
* [Hantera HDInsight-kluster med hjälp av Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).