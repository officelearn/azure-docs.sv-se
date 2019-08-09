---
title: Apache Phoenix anslutnings problem i Azure HDInsight
description: Apache Phoenix anslutnings problem i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/07/2019
ms.openlocfilehash: 641d622377bad7a1239efd526b93c6f0f0c08d4a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68887045"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Scenario: Apache Phoenix anslutnings problem i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Det går inte att ansluta till Apache HBase med Apache Phoenix. Anledningen kan variera.

## <a name="cause-incorrect-ip"></a>Orsak: Felaktig IP

Felaktig IP för aktiv Zookeeper-nod.

### <a name="resolution"></a>Lösning

IP-adressen för den aktiva Zookeeper-noden kan identifieras från Ambari UI genom att följa länkarna till **HBase-> Quick Links-> ZK***  **(Active)-> Zookeeper information**. Korrigera efter behov.

---

## <a name="cause-systemcatalog-table-offline"></a>Orsak: Säker. Katalog tabellen är offline

När du kör kommandon som `!tables`, får du ett fel meddelande som liknar:

```
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

När du kör kommandon som `count 'SYSTEM.CATALOG'`, får du ett fel meddelande som liknar:

```
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Lösning

Starta om HMaster-tjänsten på alla Zookeeper-noder från Ambari-ANVÄNDARGRÄNSSNITTET.

1. Gå till **HBase-> Active HBase Master-** länken i avsnittet Sammanfattning i HBase.

1. I avsnittet **komponenter** startar du om HBase master tjänsten.

1. Upprepa stegen ovan för återstående **vänte läges HBase Master** tjänster.

Det kan ta upp till 5 minuter för HBase Master tjänst att stabilisera och slutföra återställningen. `SYSTEM.CATALOG` När tabellen återgår till normal bör anslutnings problemet till Apache Phoenix lösas automatiskt.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
