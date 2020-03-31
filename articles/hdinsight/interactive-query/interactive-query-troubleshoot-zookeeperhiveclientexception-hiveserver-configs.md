---
title: Apache Hive Zeppelin Tolkfel - Azure HDInsight
description: Apache Zeppelin Hive JDBC-tolken pekar på fel webbadress i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 20309babb9ece0ae20e7442543b0d378f9a51060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895063"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Scenario: Apache Hive Zeppelin Tolk ger en Zookeeper fel i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du använder interaktiva frågekomponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Zeppelin-tolken ger följande felmeddelande i ett Apache Hive LLAP-kluster:

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Orsak

Zeppelin Hive JDBC-tolken pekar på fel webbadress.

## <a name="resolution"></a>Lösning

1. Navigera till Bikupekomponentsammanfattningen och kopiera "Hive JDBC Url" till Urklipp.

1. Navigera till`https://clustername.azurehdinsight.net/zeppelin/#/interpreter`

1. Redigera JDBC-inställningarna: uppdatera hive.url-värdet till Hive JDBC-URL:en som kopierades i steg 1

1. Spara och försök sedan igen frågan

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
