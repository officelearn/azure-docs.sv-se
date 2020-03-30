---
title: Apache Spark Streaming-programmet stoppas efter 24 dagar i Azure HDInsight
description: Ett Apache Spark Streaming-program stoppas efter att ha körts i 24 dagar och det finns inga fel i loggfilerna.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: ff410ea1b6c54d2f58babeb20c68fe95033e9728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894426"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Scenario: Apache Spark Streaming-programmet stoppas efter att ha körts i 24 dagar i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Ett Apache Spark Streaming-program stoppas efter att ha körts i 24 dagar och det finns inga fel i loggfilerna.

## <a name="cause"></a>Orsak

Värdet `livy.server.session.timeout` styr hur länge Apache Livy ska vänta på att en session ska slutföras. När sessionslängden `session.timeout` når värdet dödas Livy-sessionen och programmet automatiskt.

## <a name="resolution"></a>Lösning

För långvariga jobb ökar du `livy.server.session.timeout` värdet av att använda Ambari-användargränssnittet. Du kan komma åt Livy-konfigurationen från Ambari-användargränssnittet med hjälp av URL:en `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`.

Ersätt `<yourclustername>` med namnet på ditt HDInsight-kluster som visas i portalen.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
