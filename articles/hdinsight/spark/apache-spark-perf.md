---
title: Optimera Spark-jobb för prestanda – Azure HDInsight
description: Visa vanliga strategier för bästa prestanda för Apache Spark kluster i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 0b152f7882c7d7a3bab762253da0febc0257ceae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84117967"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Optimera Apache Spark jobb i HDInsight

Den här artikeln innehåller en översikt över strategier för att optimera Apache Spark jobb på Azure HDInsight.

## <a name="overview"></a>Översikt

Prestanda för dina Apache Spark jobb beror på flera faktorer. Dessa prestanda faktorer är: hur dina data lagras, hur klustret konfigureras och vilka åtgärder som används vid bearbetning av data.

Vanliga utmaningar som du kan tänka på är: minnes begränsningar på grund av felaktigt storleksändrade körningar, långvariga åtgärder och uppgifter som resulterar i kartesiska-åtgärder.

Det finns också många optimeringar som kan hjälpa dig att lösa dessa utmaningar, till exempel cachelagring, och att data skevas.

I var och en av följande artiklar finns information om olika aspekter av Spark-optimering.

* [Optimera data lagring för Apache Spark](optimize-data-storage.md)
* [Optimera data bearbetning för Apache Spark](optimize-data-processing.md)
* [Optimera minnes användning för Apache Spark](optimize-memory-usage.md)
* [Optimera konfiguration av HDInsight-kluster för Apache Spark](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Nästa steg

* [Felsöka Apache Spark-jobb som körs i Azure HDInsight](apache-spark-job-debugging.md)
* [Hantera resurser för ett Apache Spark kluster i HDInsight](apache-spark-resource-manager.md)
* [Konfigurera Apache Spark-inställningar](apache-spark-settings.md)
