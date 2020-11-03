---
title: IllegalArgumentException-fel för Apache Spark – Azure HDInsight
description: IllegalArgumentException för Apache Spark-aktivitet i Azure HDInsight för Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 61190a932389b429040d6b643db2dd2732b2c41d
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287905"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Scenario: IllegalArgumentException för Apache Spark-aktivitet i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Du får följande undantag när du försöker köra en spark-aktivitet i en Azure Data Factory pipeline:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Orsak

Ett Spark-jobb kommer att Miss förväntas om programmets jar-fil inte finns i Spark-klustrets standard-/primär lagring.

Detta är ett känt problem med Spark-ramverket med öppen källkod som spåras i det här felet: [Spark-jobb Miss lyckas om FS. defaultFS och Application jar är olika URL: er](https://issues.apache.org/jira/browse/SPARK-22587).

Det här problemet har lösts i Spark 2.3.0.

## <a name="resolution"></a>Lösning

Se till att programmet jar är lagrat på standard-/primär lagrings platsen för HDInsight-klustret. Om Azure Data Factory ska du kontrol lera att den länkade ADF-tjänsten pekar mot standard behållaren för HDInsight i stället för en sekundär behållare.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]