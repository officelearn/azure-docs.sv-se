---
title: Optimera Spark-jobb för prestanda – Azure HDInsight
description: Visa vanliga strategier för bästa prestanda för Apache Spark kluster i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: f92a351087670ce0b37921a496eabfa883a3b1fc
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780112"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Optimera Apache Spark jobb i HDInsight

Den här artikeln innehåller en översikt över strategier för att optimera Apache Spark jobb på Azure HDInsight.

## <a name="overview"></a>Översikt

Prestanda för dina Apache Spark jobb beror på flera faktorer. Dessa prestanda faktorer är: hur dina data lagras, hur klustret konfigureras och vilka åtgärder som används vid bearbetning av data.

Vanliga utmaningar som du kan tänka på är minnes begränsningar som beror på felaktig storlek på körningar, långvariga åtgärder och uppgifter som resulterar i kartesiska-åtgärder.

Det finns också olika strategier som kan hjälpa dig att lösa dessa utmaningar, till exempel cachelagring, och att data skevas.

I var och en av följande artiklar kan du hitta vanliga utmaningar och lösningar för en annan aspekt av Spark-optimering.

* [Optimera data lagring](optimize-data-storage.md)
* [Optimera data bearbetning](optimize-data-processing.md)
* [Optimera minnes användning](optimize-memory-usage.md)
* [Optimera kluster konfigurationen](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Nästa steg

* [Felsöka Apache Spark-jobb som körs i Azure HDInsight](apache-spark-job-debugging.md)
* [Hantera resurser för ett Apache Spark kluster i HDInsight](apache-spark-resource-manager.md)
* [Konfigurera Apache Spark-inställningar](apache-spark-settings.md)
