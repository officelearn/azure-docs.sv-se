---
title: Fel vid öppning av Java heap-utrymme vid försök att öppna Apache Spark historik server i Azure HDInsight
description: Apache livy-servern kan inte starta med Java. lang. OutOfMemoryError i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 5c2ae90bdca8512802c845a5ac58c3c4aeedd5b7
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667792"
---
# <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server-in-azure-hdinsight"></a>Scenario: Fel vid öppning av Java heap-utrymme vid försök att öppna Apache Spark historik server i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Du får följande fel meddelande när du öppnar händelser i Spark historik Server:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

## <a name="cause"></a>Orsak

Det här problemet orsakas ofta av brist på resurser när du öppnar stora Spark-Event-filer. Storleken på Spark-heapen är inställd på 1 GB som standard, men stora Spark-händelseloggar kan kräva mer än så.

Om du vill kontrol lera storleken på de filer som du försöker läsa in kan du utföra följande kommandon:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

## <a name="resolution"></a>Lösning

Du kan öka Spark historik serverns minne genom att redigera `SPARK_DAEMON_MEMORY` egenskapen i Spark-konfigurationen och starta om alla tjänster.

Det kan du göra i Ambari webb läsar gränssnitt genom att välja avsnittet Spark2/config/Advanced Spark2-miljö.

![Avsnittet avancerad spark2-miljö](./media/apache-spark-ts-outofmemory-heap-space/image01.png)

Lägg till följande egenskap för att ändra server minnet för Spark-historik från 1G till `SPARK_DAEMON_MEMORY=4g`4G:.

![Spark-egenskap](./media/apache-spark-ts-outofmemory-heap-space/image02.png)

Se till att starta om alla berörda tjänster från Ambari.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
