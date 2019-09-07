---
title: Hämtnings problem med JDBC/ODBC och Apache Thrift Framework – Azure HDInsight
description: Det går inte att hämta stora data mängder med JDBC/ODBC och Apache Thrift Software Framework i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 97aaec9d42baa3e94be72a748e82345d159f4583
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736246"
---
# <a name="scenario-unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-azure-hdinsight"></a>Scenario: Det går inte att hämta stora data mängder med JDBC/ODBC och Apache Thrift Software Framework i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

När du försöker hämta stora data mängder med JDBC/ODBC och Apache Thrift Software Framework i Azure HDInsight, får du ett fel meddelande som liknar följande:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Orsak

Detta undantag orsakas av att serialiserings processen försöker använda mer buffertutrymme än vad som tillåts. I Spark-2.0.0 används klassen `org.apache.spark.serializer.KryoSerializer` för att serialisera objekt när data nås via Apache Thrift Software Framework. En annan klass används för data som ska skickas via nätverket eller cachelagras i serialiserat format.

## <a name="resolution"></a>Lösning

Öka värdet `Kryoserializer` för bufferten. Lägg till en nyckel `spark.kryoserializer.buffer.max` med namnet och Ställ `2048` in den på i `Custom spark2-thrift-sparkconf`spark2 config.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
