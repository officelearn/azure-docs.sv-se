---
title: Hög tillgänglighet med Apache Kafka – Azure HDInsight
description: Lär dig mer om att säkerställa hög tillgänglighet med Apache Kafka på Azure HDInsight. Lär dig mer om att balansera om partitionsrepliker i Kafka så att de är inom olika feldomäner i Azure-regionen som innehåller HDInsight.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.openlocfilehash: bd3b02d54e0a65411e45f0422a0d245645d59096
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43049979"
---
# <a name="high-availability-of-your-data-with-apache-kafka-on-hdinsight"></a>Hög tillgänglighet för dina data med Apache Kafka på HDInsight

Lär dig hur du konfigurerar partitionsrepliker för Kafka-ämnen med hänsyn till underliggande rackkonfigurationer för maskinvara. Den här konfigurationen garanterar tillgängligheten för data som lagras i Apache Kafka på HDInsight.

## <a name="fault-and-update-domains-with-kafka"></a>Fel- och uppdateringsdomäner med Kafka

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

I dokumentet [Start with Kafka on HDInsight](apache-kafka-get-started.md) (Kom igång med Kafka i HDInsight) ges ett exempel på hur du skapar ämnen och ställer in replikeringsfaktorn.

## <a name="how-to-rebalance-partition-replicas"></a>Så balanserar du om partitionsrepliker

Använd [verktyget för ombalansering av Kafka-partitioner](https://github.com/hdinsight/hdinsight-kafka-tools) till att balansera om valda ämnen. Du måste köra det här verktyget från en SSH-session till huvudnoden för ditt Kafka-kluster.

Mer information om hur du ansluter till HDInsight via SSH finns i dokumentet [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

## <a name="next-steps"></a>Nästa steg

* [Skalbarhet för Kafka i HDInsight](apache-kafka-scalability.md)
* [Spegling med Kafka i HDInsight](apache-kafka-mirroring.md)
