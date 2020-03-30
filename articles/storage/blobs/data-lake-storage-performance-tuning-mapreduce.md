---
title: 'Tune-prestanda: MapReduce, HDInsight & Azure Data Lake Storage Gen2 | Microsoft-dokument'
description: Azure Data Lake Storage Gen2 MapReduce Prestandajusteringsriktlinjer
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a3ea6858355d6cb921f629bf36134d96371f6244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327918"
---
# <a name="tune-performance-mapreduce-hdinsight--azure-data-lake-storage-gen2"></a>Justera prestanda: MapReduce, HDInsight & Azure Data Lake Storage Gen2

Förstå de faktorer som du bör tänka på när du ställer in resultatet för map reduce-jobb. Den här artikeln innehåller en rad riktlinjer för prestandajustering.

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Storage Gen2-konto**. Instruktioner om hur du skapar ett finns i [Snabbstart: Skapa ett Azure Data Lake Storage Gen2-lagringskonto](data-lake-storage-quickstart-create-account.md).
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Storage Gen2-konto. Se [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **Använda MapReduce på HDInsight**.  Mer information finns i [Använd MapReduce i Hadoop på HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Riktlinjer för prestandajustering för Data Lake Storage Gen2**.  Allmänna prestandabegrepp finns i [Prestandajusteringsvägledning för Datasjölagring Gen2](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parametrar

När du kör MapReduce-jobb kan du här är de parametrar som du kan konfigurera för att öka prestanda på Data Lake Storage Gen2:

* **Mapreduce.map.memory.mb** – Mängden minne som ska allokeras till varje mapper
* **Mapreduce.job.maps** – Antalet kartuppgifter per jobb
* **Mapreduce.reduce.memory.mb** – Mängden minne som ska allokeras till varje reducer
* **Mapreduce.job.reduces** – Antalet färre uppgifter per jobb

**Mapreduce.map.memory / Mapreduce.reduce.memory Mapreduce.reduce.memory Mapreduce.map.memory / Mapreduce.reduce.memory Mapreduce.reduce.memory Mapred** Detta nummer bör justeras baserat på hur mycket minne som behövs för kartan och/eller minska uppgiften.  Standardvärdena för mapreduce.map.memory och mapreduce.reduce.memory kan visas i Ambari via yarn-konfigurationen.  I Ambari navigerar du till YARN och visar fliken Configs.  YARN-minnet visas.  

**Mapreduce.job.maps / Mapreduce.job.reduces** Detta avgör det maximala antalet mappers eller reducerare som ska skapas.  Antalet delningar avgör hur många mappare som ska skapas för MapReduce-jobbet.  Därför kan du få färre mappers än du begärde om det finns mindre delningar än antalet mappers som begärs.       

## <a name="guidance"></a>Riktlinjer

> [!NOTE]
> Vägledningen i det här dokumentet förutsätter att ditt program är det enda programmet som körs i klustret.

**Steg 1: Bestäm antalet jobb som körs**

Som standard använder MapReduce hela klustret för jobbet.  Du kan använda mindre av klustret med hjälp av färre mappers än det finns tillgängliga behållare.        

**Steg 2: Ställ in mapreduce.map.memory/mapreduce.reduce.memory**

Storleken på minnet för karta och minska uppgifter kommer att vara beroende av ditt specifika jobb.  Du kan minska minnesstorleken om du vill öka samtidigheten.  Antalet aktiviteter som körs samtidigt beror på antalet behållare.  Genom att minska mängden minne per mapper eller reducer, kan fler behållare skapas, vilket gör att fler mappers eller reducerare att köra samtidigt.  Om du minskar mängden minne för mycket kan vissa processer få på minne.  Om du får ett heap-fel när du kör jobbet bör du öka minnet per mapper eller reducer.  Du bör överväga att lägga till fler behållare kommer att lägga till extra omkostnader för varje ytterligare behållare, vilket potentiellt kan försämra prestanda.  Ett annat alternativ är att få mer minne med hjälp av ett kluster som har högre mängder minne eller öka antalet noder i klustret.  Mer minne gör det möjligt att använda fler behållare, vilket innebär mer samtidighet.  

**Steg 3: Bestäm totalt GARN-minne**

Om du vill stämma mapreduce.job.maps/mapreduce.job.reduces bör du ta hänsyn till hur mycket totalt YARN-minne som är tillgängligt för användning.  Denna information finns i Ambari.  Navigera till YARN och visa fliken Configs.  YARN-minnet visas i det här fönstret.  Du bör multiplicera YARN-minnet med antalet noder i klustret för att få det totala YARN-minnet.

    Total YARN memory = nodes * YARN memory per node

Om du använder ett tomt kluster kan minnet vara det totala YARN-minnet för klustret.  Om andra program använder minne kan du välja att bara använda en del av klustrets minne genom att minska antalet mappers eller reducer till antalet behållare som du vill använda.  

**Steg 4: Beräkna antalet YARN-behållare**

YARN-behållare dikterar mängden samtidighet som är tillgänglig för jobbet.  Ta totalt YARN minne och dela det med mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Steg 5: Ställ mapreduce.job.maps/mapreduce.job.reduces**

Ange mapreduce.job.maps/mapreduce.job.reduces till minst antalet tillgängliga behållare.  Du kan experimentera ytterligare genom att öka antalet mappers och reducerare för att se om du får bättre prestanda.  Tänk på att fler mappers kommer att ha ytterligare omkostnader så att ha för många mappers kan försämra prestanda.  

CPU-schemaläggning och CPU-isolering är inaktiverade som standard så att antalet YARN-behållare begränsas av minne.

## <a name="example-calculation"></a>Exempelberäkning

Låt oss anta att vi har ett kluster som består av 8 D14-noder, och vi vill köra ett I/O-intensivt jobb.  Här är beräkningarna du bör göra:

**Steg 1: Bestäm antalet jobb som körs**

I det här exemplet får vi anta att vårt jobb är det enda jobb som körs.  

**Steg 2: Ställ in mapreduce.map.memory/mapreduce.reduce.memory**

I det här exemplet kör vi ett I/O-intensivt jobb och bestämmer att 3 GB minne för kartaktiviteter ska räcka.

    mapreduce.map.memory = 3GB

**Steg 3: Bestäm totalt GARN-minne**

    Total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Steg 4: Beräkna # av YARN behållare**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Steg 5: Ställ mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Exempel att köra

För att visa hur MapReduce körs på Data Lake Storage Gen2 finns nedan några exempelkod som kördes i ett kluster med följande inställningar:

* 16 nod D14v2
* Hadoop-kluster som kör HDI 3.6

För en startpunkt, här är några exempel kommandon för att köra MapReduce Teragen, Terasort och Teravalidate.  Du kan justera dessa kommandon baserat på dina resurser.

**Teragen (på väg mot människor)**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input

**Terasort (olika år)**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output

**Teravalidate (olika)**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
