---
title: Prestandajustering för Azure Data Lake Storage Gen1 – MapReduce
description: Azure Data Lake Storage Gen1 MapReduce Prestandajusteringsriktlinjer
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: a645049665bc1d51efa94a879b9d2e4e5529282f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904591"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Vägledning för prestandajustering för MapReduce på HDInsight och Azure Data Lake Storage Gen1

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Storage Gen1-konto**. Instruktioner om hur du skapar en finns i [Komma igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Storage Gen1-konto. Se [Skapa ett HDInsight-kluster med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Se till att du aktiverar Fjärrskrivbord för klustret.
* **Använda MapReduce på HDInsight**. Mer information finns i [Använd MapReduce i Hadoop på HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Granska riktlinjer för prestandajustering för Data Lake Storage Gen1**. Allmänna prestandabegrepp finns i [Prestandajusteringsvägledning för Datasjölagring Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parametrar

När du kör MapReduce-jobb är här de viktigaste parametrarna som du kan konfigurera för att öka prestanda på Data Lake Storage Gen1:

|Parameter      | Beskrivning  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  Mängden minne som ska allokeras till varje mapper.  |
|`Mapreduce.job.maps`     |  Antalet kartuppgifter per jobb.  |
|`Mapreduce.reduce.memory.mb`     |  Mängden minne som ska allokeras till varje reducer.  |
|`Mapreduce.job.reduces`    |   Antalet minska aktiviteter per jobb.  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>Mapreduce.map.memory / Mapreduce.reduce.memory

Justera det här numret baserat på hur mycket minne som behövs för kartan och/eller minska uppgiften. Du kan visa standardvärdena `mapreduce.map.memory` för och `mapreduce.reduce.memory` i Ambari via garnkonfigurationen. I Ambari navigerar du till YARN och visar fliken **Configs.** YARN-minnet visas.

### <a name="mapreducejobmaps--mapreducejobreduces"></a>Mapreduce.job.maps / Mapreduce.job.reduces

Detta bestämmer det maximala antalet mappers eller reducerare att skapa. Antalet delningar avgör hur många mappare som skapas för MapReduce-jobbet. Därför kan du få färre mappers än du begärde om det finns färre delningar än antalet mappers som begärs.

## <a name="guidance"></a>Riktlinjer

### <a name="step-1-determine-number-of-jobs-running"></a>Steg 1: Bestäm antalet jobb som körs

Som standard använder MapReduce hela klustret för jobbet. Du kan använda mindre av klustret med färre mappers än det finns tillgängliga behållare. Vägledningen i det här dokumentet förutsätter att ditt program är det enda programmet som körs i klustret.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Steg 2: Ställ in mapreduce.map.memory/mapreduce.reduce.memory

Storleken på minnet för karta och minska uppgifter kommer att vara beroende av ditt specifika jobb. Du kan minska minnesstorleken om du vill öka samtidigheten. Antalet aktiviteter som körs samtidigt beror på antalet behållare. Genom att minska mängden minne per mapper eller reducer, kan fler behållare skapas, vilket gör att fler mappers eller reducerare att köra samtidigt. Om du minskar mängden minne för mycket kan vissa processer få på minne. Om du får ett heap-fel när du kör jobbet ökar du minnet per mapper eller reducer. Överväg att lägga till fler behållare lägger till extra omkostnader för varje ytterligare behållare, vilket potentiellt kan försämra prestanda. Ett annat alternativ är att få mer minne med hjälp av ett kluster som har högre mängder minne eller öka antalet noder i klustret. Mer minne gör det möjligt att använda fler behållare, vilket innebär mer samtidighet.

### <a name="step-3-determine-total-yarn-memory"></a>Steg 3: Bestäm totalt GARN-minne

Om du vill stämma mapreduce.job.maps/mapreduce.job.reduces bör du ta hänsyn till mängden totalt YARN-minne som är tillgängligt för användning. Denna information finns i Ambari. Navigera till YARN och visa fliken **Configs.** YARN-minnet visas i det här fönstret. Multiplicera YARN-minnet med antalet noder i klustret för att få det totala YARN-minnet.

`Total YARN memory = nodes * YARN memory per node`

Om du använder ett tomt kluster kan minnet vara det totala YARN-minnet för klustret. Om andra program använder minne kan du välja att bara använda en del av klustrets minne genom att minska antalet mappers eller reducer till antalet behållare som du vill använda.

### <a name="step-4-calculate-number-of-yarn-containers"></a>Steg 4: Beräkna antalet YARN-behållare

YARN-behållare dikterar mängden samtidighet som är tillgänglig för jobbet. Ta totalt YARN minne och dela det med mapreduce.map.memory.

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Steg 5: Ställ mapreduce.job.maps/mapreduce.job.reduces

Ange mapreduce.job.maps/mapreduce.job.reduces till minst antalet tillgängliga behållare. Du kan experimentera ytterligare genom att öka antalet mappers och reducerare för att se om du får bättre prestanda. Tänk på att fler mappers kommer att ha ytterligare omkostnader så att ha för många mappers kan försämra prestanda.

CPU-schemaläggning och CPU-isolering är inaktiverade som standard så att antalet YARN-behållare begränsas av minne.

## <a name="example-calculation"></a>Exempelberäkning

Anta att du för närvarande har ett kluster som består av 8 D14-noder och vill köra ett I/O-intensivt jobb. Här är beräkningarna du bör göra:

### <a name="step-1-determine-number-of-jobs-running"></a>Steg 1: Bestäm antalet jobb som körs

Till vårt exempel antar vi att vårt jobb är det enda som körs.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Steg 2: Ställ in mapreduce.map.memory/mapreduce.reduce.memory

Till exempel kör du ett I/O-intensivjobb och bestämmer att 3 GB minne för kartuppgifter är tillräckligt.

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>Steg 3: Bestäm totalt GARN-minne

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>Steg 4: Beräkna # av YARN behållare

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Steg 5: Ställ mapreduce.job.maps/mapreduce.job.reduces

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>Begränsningar

**Data Lake Storage Gen1 begränsning**

Som en tjänst med flera innehavare anger Data Lake Storage Gen1 bandbreddsbegränsningar på kontonivå. Om du når dessa gränser börjar du se aktivitetsfel. Detta kan identifieras genom att observera begränsningsfel i aktivitetsloggar. Om du behöver mer bandbredd för ditt jobb, vänligen kontakta oss.

För att kontrollera om du får strypt, måste du aktivera felsökningsloggning på klientsidan. Så här kan du göra det:

1. Placera följande egenskap i log4j-egenskaperna i Ambari > YARN > Config > Advanced yarn-log4j: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Starta om alla noder/-tjänster för att konfigurationen ska börja gälla.

3. Om du får begränsat ser du HTTP 429-felkoden i YARN-loggfilen. YARN-loggfilen finns i /tmp/&lt;user/yarn.log&gt;

## <a name="examples-to-run"></a>Exempel att köra

För att visa hur MapReduce körs på Data Lake Storage Gen1, är följande en del exempelkod som kördes i ett kluster med följande inställningar:

* 16 nod D14v2
* Hadoop-kluster som kör HDI 3.6

För en startpunkt, här är några exempel kommandon för att köra MapReduce Teragen, Terasort och Teravalidate. Du kan justera dessa kommandon baserat på dina resurser.

### <a name="teragen"></a>Teragen (på väg mot människor)

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Terasort (olika år)

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teravalidate (olika)

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```
