---
title: Apache Phoenix-anslutningsproblem i Azure HDInsight
description: Anslutningsproblem mellan Apache HBase och Apache Phoenix i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: b886f51bcb2bb7308c49c76563dcb70148bbc583
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887299"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Scenario: Apache Phoenix anslutningsproblem i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Det går inte att ansluta till Apache HBase med Apache Phoenix. Orsakerna kan variera.

## <a name="cause-incorrect-ip"></a>Orsak: Felaktig IP

Felaktig IP för aktiv Zookeeper-nod.

### <a name="resolution"></a>Lösning

IP för den aktiva Zookeeper noden kan identifieras från Ambari UI genom att följa länkarna till **HBase** > **Quick Links** > **ZK (Active)** > **Zookeeper Info**. Korrigera IP efter behov.

---

## <a name="cause-systemcatalog-table-offline"></a>Orsak: SYSTEM. KATALOG-tabellen offline

När du kör `!tables`kommandon som visas ett felmeddelande som liknar:

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

När du kör `count 'SYSTEM.CATALOG'`kommandon som visas ett felmeddelande som liknar:

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Lösning

Från Apache Ambari UI, slutföra följande steg för att starta om HMaster-tjänsten på alla ZooKeeper-noder:

1. Gå till **HBase** > **Active HBase Master**från avsnittet **Sammanfattning** i HBase .

1. Starta om HBase-huvudtjänsten i avsnittet **Komponenter.**

1. Upprepa dessa steg för alla återstående **Standby HBase** Master-tjänster.

Det kan ta upp till fem minuter för HBase Master-tjänsten att stabilisera och slutföra återställningen. När `SYSTEM.CATALOG` tabellen är tillbaka till det normala, anslutningsproblemet till Apache Phoenix bör få lösas automatiskt.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
