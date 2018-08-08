---
title: Hadoop-arkitektur – Azure HDInsight
description: Beskriver Hadoop lagring och bearbetning i HDInsight-kluster.
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 754f4538f7c2a8de6286198094b38d40c466a15f
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599480"
---
# <a name="hadoop-architecture-in-hdinsight"></a>Hadoop-arkitektur i HDInsight

Hadoop innehåller två grundläggande komponenter, hög densitet File System (HDFS) som tillhandahåller lagring och ännu en annan Resource Negotiator (YARN) som tillhandahåller bearbetning. Med funktioner för lagring och bearbetning av blir ett kluster kan köra MapReduce-program för att utföra den önskade databearbetningen.

> [!NOTE]
> Ett HDFS vanligtvis inte har distribuerats i HDInsight-klustret för att tillhandahålla lagring. I stället används ett HDFS-kompatibla gränssnittsnivån av Hadoop-komponenter. Funktionen verkligt lagringsutrymme som tillhandahålls av Azure Storage eller Azure Data Lake Store. För Hadoop, MapReduce-jobb som körs på HDInsight-klustret körs som om det fanns en HDFS och kräver inga ändringar till stöd för sina lagringsbehov. Lagring är utlagd i Hadoop på HDInsight, men YARN bearbetning förblir en central del. Mer information finns i [introduktion till Azure HDInsight](hadoop/apache-hadoop-introduction.md).

Den här artikeln introducerar YARN och hur den samordnar körning av program på HDInsight.

## <a name="yarn-basics"></a>YARN-grunderna 

YARN reglerar och styr databearbetning i Hadoop. YARN har två viktiga tjänster som körs som processer på noder i klustret: 

* ResourceManager 
* NodeManager

ResourceManager beviljar kluster beräkningsresurser till program som MapReduce-jobb. ResourceManager ger de här resurserna som behållare, där varje behållare består av en allokering av CPU-kärnor och RAM-minne. Om du kombineras alla resurser som är tillgängliga i ett kluster och distribuerade dem i block om ett visst antal kärnor och minne, är en behållare i varje block på resurser. Varje nod i klustret har kapacitet för ett visst antal behållare och därför klustret har en fast gräns för hur många behållare som är tillgängliga. Tilldelning av resurser i en behållare kan konfigureras. 

När ett MapReduce-program körs på ett kluster, ger ResourceManager programmet behållare där du vill köra. ResourceManager spårar statusen för att köra program, tillgängliga kapacitet och spårar program när de slutförts och släpp sina resurser. 

ResourceManager körs också en process som webbservern som tillhandahåller ett webbaserat användargränssnitt som du kan använda för att övervaka status för program. 

När en användare skickar ett MapReduce-program ska köras på klustret, har programmet skickats till ResourceManager. I sin tur allokerar ResourceManager en behållare på tillgängliga NodeManager noder. NodeManager noderna finns där programmet faktiskt körs. Den första behållaren som allokerats körs ett särskilt program, kallat ApplicationMaster. Den här ApplicationMaster ansvarar för att hämta resurser, i form av efterföljande behållare som behövs för att köra skickat program. ApplicationMaster går igenom olika programmet (kartan mellanlagra och minska steget) och faktorer inom hur mycket data som behöver bearbetas. ApplicationMaster begär sedan (*förhandlar*) resurser från ResourceManager åt programmet. ResourceManager ger i sin tur resurser från NodeManagers i klustret till ApplicationMaster för det du använder vid körning av programmet. 

NodeManagers köra uppgifter som tillsammans bildar programmet sedan rapportera sina förlopp och status till ApplicationMaster. ApplicationMaster rapporterar i sin tur status för programmet till resurshanteraren. ResourceManager returnerar alla resultat till klienten.

## <a name="yarn-on-hdinsight"></a>YARN på HDInsight

Alla HDInsight-klustertyper distribuera YARN. ResourceManager distribueras för hög tillgänglighet med en primär och sekundär-instans som körs på första och andra huvudnoder i klustret respektive. Endast en instans av ResourceManager är aktiv i taget. NodeManager instanserna körs tillgängliga noder i klustret.

![YARN på HDInsight](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>Nästa steg

* [Använda MapReduce i Hadoop på HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Introduktion till Azure HDInsight](hadoop/apache-hadoop-introduction.md)
