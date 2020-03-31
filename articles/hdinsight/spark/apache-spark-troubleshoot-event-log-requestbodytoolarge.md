---
title: RequestBodyTooLarge-fel från Apache Spark-appen - Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge visas i loggen för Apache Spark streaming app i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 777d06670238a7625d190c92f78a55cd4794d226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894406"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... RequestBodyTooLarge" visas i Apache Spark streaming app logg i HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Felet: `NativeAzureFileSystem ... RequestBodyTooLarge` visas i drivrutinsloggen för en Apache Spark-streamingapp.

## <a name="cause"></a>Orsak

Din Spark-händelseloggfil når förmodligen fillängdsgränsen för WASB.

I Spark 2.3 genererar varje Spark-app en Spark-händelseloggfil. Spark-händelseloggfilen för en Spark-strömningsapp fortsätter att växa medan appen körs. Idag har en fil på WASB en blockgräns på 50000 och standardblockstorleken är 4 MB. Så i standardkonfiguration max filstorlek är 195 GB. Azure Storage har dock ökat maxblockstorleken till 100 MB, vilket effektivt förde den enda filgränsen till 4,75 TB. Mer information finns i [Skalbarhets- och prestandamål för Blob-lagring](../../storage/blobs/scalability-targets.md).

## <a name="resolution"></a>Lösning

Det finns tre lösningar för det här felet:

* Öka blockstorleken till upp till 100 MB. I Ambari UI ändrar `fs.azure.write.request.size` du HDFS-konfigurationsegenskapen (eller skapar den i `Custom core-site` avsnittet). Ange egenskapen till ett större värde, till exempel: 33554432. Spara den uppdaterade konfigurationen och starta om berörda komponenter.

* Stoppa och skicka regelbundet igång spark-streaming-jobbet.

* Använd HDFS för att lagra Spark-händelseloggar. Om du använder HDFS för lagring kan det leda till förlust av Spark-händelsedata under klusterskalning eller Azure-uppgraderingar.

    1. Gör ändringar `spark.eventlog.dir` `spark.history.fs.logDirectory` i och via Ambari UI:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Skapa kataloger på HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Starta om alla berörda tjänster via Ambari UI.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
