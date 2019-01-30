---
title: Azure Data Lake Storage Gen2 MapReduce prestandajustering riktlinjer | Microsoft Docs
description: Azure Data Lake Storage Gen2 MapReduce prestandajustering riktlinjer
services: storage
author: swums
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: stewu
ms.openlocfilehash: 0cd7ecdc6ee7b6ccd66c9d0d88ebef0a1220a40a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249120"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen2"></a>Prestandajusteringsvägledning för MapReduce på HDInsight och Azure Data Lake Storage Gen2

Förstå de faktorer som du bör tänka på när du finjustera prestanda för Map Reduce-jobb. Den här artikeln innehåller en uppsättning riktlinjer för prestandajustering.

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett konto med Azure Data Lake Storage Gen2**. Anvisningar för hur du skapar ett finns i [snabbstarten: Skapa ett lagringskonto i Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Storage Gen2-konto. Kontrollera att du aktivera Fjärrskrivbord för klustret.
* **Använda MapReduce på HDInsight**.  Mer information finns i [använda MapReduce i Hadoop på HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Riktlinjer för Data Lake Storage Gen2 för prestandajustering**.  Allmänna prestanda begrepp, se [Data Lake Storage Gen2 justering Prestandavägledning](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parametrar

När du kör MapReduce-jobb, följer du de parametrar som du kan konfigurera för att öka prestanda på Data Lake Storage Gen2:

* **Mapreduce.Map.Memory.MB** – mängden minne som ska allokeras till varje mapper
* **Mapreduce.Job.Maps** – antalet kartan uppgifter per jobb
* **Mapreduce.Reduce.Memory.MB** – mängden minne som ska allokeras till varje reducer
* **Mapreduce.Job.reduces** – antalet minska uppgifter per jobb

**Mapreduce.Map.Memory / Mapreduce.reduce.memory** numret som ska justeras beroende på hur mycket minne som krävs för kartan och/eller minska uppgift.  Standardvärdena för mapreduce.map.memory och mapreduce.reduce.memory kan ses i Ambari via Yarn-konfiguration.  Ambari, navigera till YARN och visa fliken konfigurationer.  YARN-minne visas.  

**Mapreduce.Job.Maps / Mapreduce.job.reduces** valet bestämmer det maximala antalet Mappningskomponenter och reducerare som ska skapas.  Antalet delningar avgör hur många Mappningskomponenter skapas för MapReduce-jobb.  Därför kan du få mindre Mappningskomponenter du begärt om det finns mindre delningar än antalet Mappningskomponenter som begärts.       

## <a name="guidance"></a>Riktlinjer

> [!NOTE]
> Riktlinjerna i det här dokumentet förutsätter att ditt program är det enda program som körs i klustret.

**Steg 1: Bestämma antalet jobb som körs**

Som standard använder MapReduce hela klustret för jobbet.  Du kan använda mindre i klustret med hjälp av mindre Mappningskomponenter än det finns tillgängliga behållare.        

**Steg 2: Ange mapreduce.map.memory/mapreduce.reduce.memory**

Storleken på minnet för kartan och minska uppgifter är beroende av specifika jobbet.  Du kan minska minnesstorleken om du vill öka parallellkörningen.  Antal aktiviteter som körs samtidigt beror på antalet behållare.  Genom att minska mängden minne per mapper eller reducer kan fler behållare skapas, som aktiverar fler Mappningskomponenter och reducerare som körs samtidigt.  Minska mängden minne för mycket kan det orsaka vissa processer för att få slut på minne.  Om du får en heap-fel när du kör jobbet, bör du öka minne per mapper eller reducer.  Du bör överväga att lägga till fler behållare kommer att lägga till extra administration för varje ytterligare behållare som potentiellt kan försämra prestanda.  Ett annat alternativ är att få mer minne med hjälp av ett kluster som har större mängder minne eller öka antalet noder i klustret.  Mer minne kan fler behållare som ska användas, vilket innebär att större samtidighet.  

**Steg 3: Fastställa totala YARN-minne**

För att justera mapreduce.job.maps/mapreduce.job.reduces, bör du överväga hur mycket av det totala YARN minnet som är tillgängliga för användning.  Den här informationen är tillgänglig i Ambari.  Gå till YARN och visa fliken konfigurationer.  YARN-minne visas i det här fönstret.  Du bör multiplicera YARN-minne med antalet noder i klustret för att hämta den totala mängden YARN-minnet.

    Total YARN memory = nodes * YARN memory per node

Om du använder ett tomt kluster kan minne vara den totala mängden minnet YARN för klustret.  Om andra program använder minne, kan du välja att bara använda en del av minnet för ditt kluster genom att minska antalet Mappningskomponenter och reducerare för hur många behållare som du vill använda.  

**Steg 4: Beräkna antalet YARN-behållare**

YARN-behållare bestämmer mängden samtidighet som är tillgängliga för projektet.  Ta totalt YARN-minne och dela som med mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Steg 5: Ange mapreduce.job.maps/mapreduce.job.reduces**

Ange mapreduce.job.maps/mapreduce.job.reduces du minst ett antal tillgängliga behållare.  Du kan experimentera ytterligare genom att öka antalet Mappningskomponenter och reducerare för att se om du får bättre prestanda.  Tänk på att fler Mappningskomponenter har ytterligare prestanda så att ha för många Mappningskomponenter kan försämra prestanda.  

Schemaläggning av CPU- och CPU-isolering är inaktiverade som standard så att antalet YARN-behållare är begränsad av minne.

## <a name="example-calculation"></a>Exempel-beräkning

Anta att vi har ett kluster som består av 8 D14 noder och vi vill köra ett i/o-intensiva jobb.  Här är beräkningar som du bör göra:

**Steg 1: Bestämma antalet jobb som körs**

I det här exemplet antar vi att vår jobbet är det enda jobb som körs.  

**Steg 2: Ange mapreduce.map.memory/mapreduce.reduce.memory**

I det här exemplet vi använder ett i/o-intensiva jobb och bestämma att 3GB minne för kartan uppgifter är tillräckliga.

    mapreduce.map.memory = 3GB

**Steg 3: Fastställa totala YARN-minne**

    Total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Steg 4: Beräkna antal YARN-behållare**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Steg 5: Ange mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Exempel för att köra

För att demonstrera hur MapReduce körs på Data Lake Storage Gen2, visas nedan viss exempelkod som kördes på ett kluster med följande inställningar:

* 16 noden D14v2
* Hadoop-kluster som kör HDI 3.6

Här följer några exempelkommandon för att köra MapReduce Teragen, Terasort och Teravalidate för en startpunkt.  Du kan justera de här kommandona baserat på dina resurser.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
