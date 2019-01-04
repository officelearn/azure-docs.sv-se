---
title: Apache Hadoop-arkitektur – Azure HDInsight
description: Beskriver Apache Hadoop-lagring och bearbetning i HDInsight-kluster.
services: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 9f45b1603fe0e34bfdf6192fd85ecaf27311ae32
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718085"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>Apache Hadoop-arkitektur i HDInsight

[Apache Hadoop](https://hadoop.apache.org/) innehåller två kärnkomponenter: den [Apache Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) som tillhandahåller lagring, och [Apache Hadoop ännu en annan Resource Negotiator (YARN)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) som tillhandahåller bearbetning. Med lagring och bearbetning av funktioner, ett kluster blir kunna köra [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) program för att utföra den önskade databearbetningen.

> [!NOTE]  
> Ett HDFS vanligtvis inte har distribuerats i HDInsight-klustret för att tillhandahålla lagring. I stället används ett HDFS-kompatibla gränssnittsnivån av Hadoop-komponenter. Funktionen verkligt lagringsutrymme som tillhandahålls av Azure Storage eller Azure Data Lake Storage. För Hadoop, MapReduce-jobb som körs på HDInsight-klustret körs som om det fanns en HDFS och kräver inga ändringar till stöd för sina lagringsbehov. Lagring är utlagd i Hadoop på HDInsight, men YARN bearbetning förblir en central del. Mer information finns i [introduktion till Azure HDInsight](hadoop/apache-hadoop-introduction.md).

Den här artikeln introducerar YARN och hur den samordnar körning av program på HDInsight.

## <a name="apache-hadoop-yarn-basics"></a>Apache Hadoop YARN-grunderna 

YARN reglerar och styr databearbetning i Hadoop. YARN har två viktiga tjänster som körs som processer på noder i klustret: 

* ResourceManager 
* NodeManager

ResourceManager beviljar kluster beräkningsresurser till program som MapReduce-jobb. ResourceManager ger de här resurserna som behållare, där varje behållare består av en allokering av CPU-kärnor och RAM-minne. Om du kombineras alla resurser som är tillgängliga i ett kluster och sedan distribueras kärnor och minne i block, är en behållare i varje block på resurser. Varje nod i klustret har en kapacitet för ett visst antal behållare, därför klustret har en fast gräns för hur många behållare som är tillgängliga. Tilldelning av resurser i en behållare kan konfigureras. 

När ett MapReduce-program körs på ett kluster, ger ResourceManager programmet behållare där du vill köra. ResourceManager spårar statusen för att köra program, tillgängliga kapacitet och spårar program när de slutförts och släpp sina resurser. 

ResourceManager körs också en process som webbservern som tillhandahåller ett webbaserat användargränssnitt för att övervaka status för program.

När en användare skickar ett MapReduce-program ska köras på klustret, har programmet skickats till ResourceManager. I sin tur allokerar ResourceManager en behållare på tillgängliga NodeManager noder. NodeManager noderna finns där programmet faktiskt körs. Den första behållaren som allokerats körs ett särskilt program, kallat ApplicationMaster. Den här ApplicationMaster ansvarar för att hämta resurser, i form av efterföljande behållare som behövs för att köra skickat program. ApplicationMaster går igenom olika program, till exempel scenen kartan och minska scenen och faktorer i hur mycket data som behöver bearbetas. ApplicationMaster begär sedan (*förhandlar*) resurser från ResourceManager åt programmet. ResourceManager ger i sin tur resurser från NodeManagers i klustret till ApplicationMaster för det du använder vid körning av programmet. 

NodeManagers köra uppgifter som tillsammans bildar programmet sedan rapportera sina förlopp och status till ApplicationMaster. ApplicationMaster rapporterar i sin tur status för programmet till resurshanteraren. ResourceManager returnerar alla resultat till klienten.

## <a name="yarn-on-hdinsight"></a>YARN på HDInsight

Alla HDInsight-klustertyper distribuera YARN. ResourceManager distribueras för hög tillgänglighet med en primär och sekundär instans som körs på första och andra huvudnoder i klustret respektive. Endast en instans av ResourceManager är aktiv i taget. NodeManager instanserna körs tillgängliga noder i klustret.

![YARN på HDInsight](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>Nästa steg

* [Använda MapReduce i Apache Hadoop på HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Introduktion till Azure HDInsight](hadoop/apache-hadoop-introduction.md)
