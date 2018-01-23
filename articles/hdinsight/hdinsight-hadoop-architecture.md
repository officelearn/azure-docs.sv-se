---
title: "Arkitektur för Hadoop - Azure HDInsight | Microsoft Docs"
description: Beskriver bearbetning och lagring av Hadoop i HDInsight-kluster.
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 85383cc32e67db1f7e6964dc0b55bf3977311d40
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2018
---
# <a name="hadoop-architecture-in-hdinsight"></a>Arkitektur för Hadoop i HDInsight

Hadoop innehåller två grundläggande komponenter, hög densitet File System (HDFS) som tillhandahåller lagring och ännu en annan resurs förhandlare (YARN) som tillhandahåller bearbetning. Med funktioner för lagring och bearbetning blir ett kluster kan köra MapReduce program för att utföra den önskade databearbetningen.

> [!NOTE]
> Ett HDFS vanligtvis inte har distribuerats i HDInsight-klustret för att tillhandahålla lagring. I stället används ett HDFS-kompatibla gränssnitt lager av Hadoop-komponenter. Den faktiska lagringskapacitet tillhandahålls av Azure Storage eller Azure Data Lake Store. För Hadoop, MapReduce-jobb som körs på HDInsight-kluster som kör som om det fanns en HDFS och kräver inga ändringar att stödja sina lagringsbehov. Lagring utlagd i Hadoop i HDInsight, men YARN bearbetning förblir en central del. 

<!--   As described in [HDInsight architecture](hdinsight-architecture.md)  -->

Den här artikeln beskriver YARN och hur den samordnar körningen av program på HDInsight.

## <a name="yarn-basics"></a>YARN-grunderna 

YARN styr och samordnar databehandling i Hadoop. YARN har två grundläggande tjänster som körs som processer på noder i klustret: 

* ResourceManager 
* NodeManager

Resurshanteraren beviljar klustret beräkningsresurser till program som MapReduce-jobb. Resurshanteraren ger dessa resurser som behållare, där varje behållare består av en allokering av CPU-kärnor och RAM-minne. Om du kombineras alla resurser som är tillgängliga i ett kluster och sedan distribueras dem i block om ett visst antal kärnor och minne, är en behållare i varje block på resurser. Varje nod i klustret har kapacitet för antal behållare och därför klustret har en fast gräns för antalet behållare som är tillgängliga. Tilldelning av resurser i en behållare kan konfigureras. 

När ett MapReduce-program körs på ett kluster, ger resurshanteraren programmet behållare där du vill köra. Resurshanteraren spårar tillståndet för program som körs, tillgängliga klustret kapacitet och spårar program som de fullständiga och släpper resurserna. 

Resurshanteraren kör också en process som webbservern som tillhandahåller en webbaserat användargränssnitt som du kan använda för att övervaka status för program. 

När en användare skickar ett MapReduce-program att köra på klustret, skickas programmet till resurshanteraren. I sin tur allokerar resurshanteraren en behållare på tillgängliga NodeManager noder. NodeManager noderna är där det faktiskt kör programmet. Första behållaren allokerade körs ett specialprogram kallas ApplicationMaster. Den här ApplicationMaster ansvarar för hämtning av resurser i form av efterföljande behållare som behövs för att köra ansökan. ApplicationMaster undersöker led i programmet (kartan mellanlagra och minska steget) och faktorer i hur mycket data som behöver bearbetas. ApplicationMaster sedan begär (*förhandlar*) resurser från resurshanteraren för programmet. Resurshanteraren ger i sin tur resurser från NodeManagers i klustret att ApplicationMaster för att använda vid körning av programmet. 

NodeManagers kör aktiviteter som utgör programmet, sedan rapportera sina förlopp och status till ApplicationMaster. ApplicationMaster rapporter i sin tur status för programmet till resurshanteraren. Resurshanteraren returnerar alla resultat till klienten.

## <a name="yarn-on-hdinsight"></a>YARN i HDInsight

Alla typer av HDInsight-kluster distribuera YARN. Resurshanteraren distribueras för hög tillgänglighet med en primär och sekundär-instans som körs på de första och andra head noderna i klustret respektive. Endast en instans av resurshanteraren är aktiv i taget. NodeManager-instanser körs över tillgängliga noder i klustret.

![YARN i HDInsight](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="see-also"></a>Se också

* [Använda MapReduce i Hadoop i HDInsight](hadoop/hdinsight-use-mapreduce.md)

<!--  * [HDInsight Architecture](hdinsight-architecture.md)  -->
