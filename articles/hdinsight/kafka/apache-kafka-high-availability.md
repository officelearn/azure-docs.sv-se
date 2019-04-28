---
title: Hög tillgänglighet med Apache Kafka – Azure HDInsight
description: Lär dig mer om att säkerställa hög tillgänglighet med Apache Kafka på Azure HDInsight. Lär dig mer om att balansera om partitionsrepliker i Kafka så att de är inom olika feldomäner i Azure-regionen som innehåller HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.openlocfilehash: 70843c368b0446a7c0e09559fa759a3cd51912d4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115236"
---
# <a name="high-availability-of-your-data-with-apache-kafka-on-hdinsight"></a>Hög tillgänglighet för dina data med Apache Kafka på HDInsight

Lär dig hur du konfigurerar partitionsrepliker för Apache Kafka-ämnen att dra nytta av underliggande rackkonfigurationer för maskinvara. Den här konfigurationen garanterar tillgängligheten för data som lagras i Apache Kafka på HDInsight.

## <a name="fault-and-update-domains-with-apache-kafka"></a>Fel- och uppdateringsdomäner med Apache Kafka

En feldomän är en logisk gruppering av underliggande maskinvara i ett Azure-datacenter. Varje feldomän delar en gemensam strömkälla och nätverksbrytare. De virtuella datorer och hanterade diskar som implementerar noderna i ett HDInsight-kluster är fördelade mellan dessa feldomäner. Den här arkitekturen begränsar de potentiella problemen vid fysiska maskinvarufel.

Varje Azure-region har ett visst antal feldomäner. En lista med domäner och antalet feldomäner de innehåller finns i dokumentationen av [tillgänglighetsuppsättningar](../../virtual-machines/windows/regions-and-availability.md#availability-sets).

> [!IMPORTANT]  
> Kafka har ingen information om feldomäner. När du skapar ett ämne i Kafka kan det lagra alla partitionsrepliker i samma feldomän. HDInsight har [verktyget för ombalansering av Kafka-partitioner](https://github.com/hdinsight/hdinsight-kafka-tools) som lösning på det här problemet.

## <a name="when-to-rebalance-partition-replicas"></a>När ska du balansera om partitionsrepliker

Du får bästa möjliga tillgänglighet för dina Kafka-data om du balanserar om partitionsreplikerna för ditt ämne vid följande tidpunkter:

* När du skapar ett nytt ämne eller en ny partition

* När du skalar upp ett kluster

## <a name="replication-factor"></a>Replikeringsfaktor

> [!IMPORTANT]  
> Vi rekommenderar att du använder en Azure-region som innehåller tre feldomäner, och använder replikeringsfaktorn 3.

Om du måste använda en region som bara har två feldomäner ska du använda replikeringsfaktorn 4, så att replikerna fördelas jämnt mellan de två feldomänerna.

Ett exempel på hur du skapar ämnen och ställer in replikeringsfaktorn finns i den [Kom igång med Apache Kafka på HDInsight](apache-kafka-get-started.md) dokumentet.

## <a name="how-to-rebalance-partition-replicas"></a>Så balanserar du om partitionsrepliker

Använd den [verktyget för ombalansering av Apache Kafka-partitioner](https://github.com/hdinsight/hdinsight-kafka-tools) att balansera om valda ämnen. Du måste köra det här verktyget från en SSH-session till huvudnoden för ditt Kafka-kluster.

Mer information om hur du ansluter till HDInsight via SSH finns i dokumentet [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

## <a name="next-steps"></a>Nästa steg

* [Skalbarhet för Apache Kafka på HDInsight](apache-kafka-scalability.md)
* [Spegling med Apache Kafka på HDInsight](apache-kafka-mirroring.md)
