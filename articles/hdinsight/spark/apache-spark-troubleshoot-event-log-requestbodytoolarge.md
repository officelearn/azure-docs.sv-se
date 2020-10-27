---
title: RequestBodyTooLarge-fel från Apache Spark app – Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge visas i loggen för Apache Spark streaming-appen i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: f8f17eeb071c6e825d4397a639296f25fe9c19fd
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545658"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... RequestBodyTooLarge "visas i Apache Spark strömmande app-logg i HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Felet: `NativeAzureFileSystem ... RequestBodyTooLarge` visas i driv rutins loggen för en Apache Spark streaming-app.

## <a name="cause"></a>Orsak

Logg filen för Spark-händelseloggen når förmodligen fil längds gränsen för WASB.

I Spark 2,3 genererar varje spark-app en spark-händelseloggen. Händelse logg filen för Spark för en spark streaming-app fortsätter att växa medan appen körs. I dag är en fil på WASB en 50000 block gräns och standard block storleken är 4 MB. Så i standard konfiguration är den maximala fil storleken 195 GB. Azure Storage har dock ökat Max block storleken till 100 MB, vilket faktiskt gjorde gränsen för en enskild fil till 4,75 TB. Mer information finns i [skalbarhets-och prestanda mål för Blob Storage](../../storage/blobs/scalability-targets.md).

## <a name="resolution"></a>Lösning

Det finns tre lösningar som är tillgängliga för det här felet:

* Öka block storleken till upp till 100 MB. Ändra HDFS-konfigurations egenskapen `fs.azure.write.request.size` (eller skapa den i avsnittet) i AMBARI UI `Custom core-site` . Ange ett större värde för egenskapen, till exempel: 33554432. Spara den uppdaterade konfigurationen och starta om berörda komponenter.

* Stoppa regelbundet och skicka om Spark-streaming-jobbet.

* Använd HDFS för att lagra händelse loggar för Spark. Att använda HDFS för Storage kan leda till förlust av Spark-händelseloggen vid kluster skalning eller Azure-uppgraderingar.

    1. Gör ändringar i `spark.eventlog.dir` och `spark.history.fs.logDirectory` via AMBARI-gränssnittet:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Skapa kataloger i HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Starta om alla berörda tjänster via Ambari-ANVÄNDARGRÄNSSNITTET.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).