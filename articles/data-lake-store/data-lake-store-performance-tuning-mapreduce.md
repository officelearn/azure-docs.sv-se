---
title: Azure Data Lake Storage Gen1 MapReduce prestandajustering riktlinjer | Microsoft Docs
description: Azure Data Lake Storage Gen1 MapReduce prestandajustering riktlinjer
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: b9e5d034db4711384d2ac8a1083da5c93ea11900
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437250"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Prestandajusteringsvägledning för MapReduce på HDInsight och Azure Data Lake Storage Gen1

## <a name="prerequisites"></a>Nödvändiga komponenter

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett konto med Azure Data Lake Storage Gen1**. Anvisningar för hur du skapar ett finns i [Kom igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Storage Gen1-konto. Se [skapa ett HDInsight-kluster med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Kontrollera att du aktivera Fjärrskrivbord för klustret.
* **Använda MapReduce på HDInsight**.  Mer information finns i [använda MapReduce i Hadoop på HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Riktlinjer för Data Lake Storage Gen1 för prestandajustering**.  Allmänna prestanda begrepp, se [Data Lake Storage Gen1 justering Prestandavägledning](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parametrar

När du kör MapReduce-jobb, är här de viktigaste parametrar som du kan konfigurera för att öka prestanda på Data Lake Storage Gen1:

* **Mapreduce.Map.Memory.MB** – mängden minne som ska allokeras till varje mapper
* **Mapreduce.Job.Maps** – antalet kartan uppgifter per jobb
* **Mapreduce.Reduce.Memory.MB** – mängden minne som ska allokeras till varje reducer
* **Mapreduce.Job.reduces** – antalet minska uppgifter per jobb

**Mapreduce.Map.Memory / Mapreduce.reduce.memory** numret som ska justeras beroende på hur mycket minne som krävs för kartan och/eller minska uppgift.  Standardvärdena för mapreduce.map.memory och mapreduce.reduce.memory kan ses i Ambari via Yarn-konfiguration.  Ambari, navigera till YARN och visa fliken konfigurationer.  YARN-minne visas.  

**Mapreduce.Job.Maps / Mapreduce.job.reduces** valet bestämmer det maximala antalet Mappningskomponenter och reducerare som ska skapas.  Antalet delningar avgör hur många Mappningskomponenter skapas för MapReduce-jobb.  Därför kan du få mindre Mappningskomponenter du begärt om det finns mindre delningar än antalet Mappningskomponenter som begärts.       

## <a name="guidance"></a>Riktlinjer

**Steg 1: Bestämma antalet jobb som körs** -använder som standard MapReduce hela klustret för jobbet.  Du kan använda mindre i klustret med hjälp av mindre Mappningskomponenter än det finns tillgängliga behållare.  Riktlinjerna i det här dokumentet förutsätter att ditt program är det enda program som körs i klustret.      

**Steg 2: Ange mapreduce.map.memory/mapreduce.reduce.memory** – storleken på minnet för kartan och minska uppgifter är beroende av specifika jobbet.  Du kan minska minnesstorleken om du vill öka parallellkörningen.  Antal aktiviteter som körs samtidigt beror på antalet behållare.  Genom att minska mängden minne per mapper eller reducer kan fler behållare skapas, som aktiverar fler Mappningskomponenter och reducerare som körs samtidigt.  Minska mängden minne för mycket kan det orsaka vissa processer för att få slut på minne.  Om du får en heap-fel när du kör jobbet, bör du öka minne per mapper eller reducer.  Du bör överväga att lägga till fler behållare kommer att lägga till extra administration för varje ytterligare behållare som potentiellt kan försämra prestanda.  Ett annat alternativ är att få mer minne med hjälp av ett kluster som har större mängder minne eller öka antalet noder i klustret.  Mer minne kan fler behållare som ska användas, vilket innebär att större samtidighet.  

**Steg 3: Fastställa totala YARN minne** – om du vill justera mapreduce.job.maps/mapreduce.job.reduces, bör du överväga att mängden totala YARN minne som är tillgängliga för användning.  Den här informationen är tillgänglig i Ambari.  Gå till YARN och visa fliken konfigurationer.  YARN-minne visas i det här fönstret.  Du bör multiplicera YARN-minne med antalet noder i klustret för att hämta den totala mängden YARN-minnet.

    Total YARN memory = nodes * YARN memory per node
Om du använder ett tomt kluster kan minne vara den totala mängden minnet YARN för klustret.  Om andra program använder minne, kan du välja att bara använda en del av minnet för ditt kluster genom att minska antalet Mappningskomponenter och reducerare för hur många behållare som du vill använda.  

**Steg 4: Beräkna antalet YARN-behållare** – YARN-behållare bestämmer mängden samtidighet som är tillgängliga för projektet.  Ta totalt YARN-minne och dela som med mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Steg 5: Ange mapreduce.job.maps/mapreduce.job.reduces** ange mapreduce.job.maps/mapreduce.job.reduces du minst ett antal tillgängliga behållare.  Du kan experimentera ytterligare genom att öka antalet Mappningskomponenter och reducerare för att se om du får bättre prestanda.  Tänk på att fler Mappningskomponenter har ytterligare prestanda så att ha för många Mappningskomponenter kan försämra prestanda.  

Schemaläggning av CPU- och CPU-isolering är inaktiverade som standard så att antalet YARN-behållare är begränsad av minne.

## <a name="example-calculation"></a>Exempel-beräkning

Anta att du har ett kluster som består av 8 D14 noder och du vill köra ett i/o-intensiva jobb.  Här är beräkningar som du bör göra:

**Steg 1: Bestämma antalet jobb som körs** – i vårt exempel antar vi att vår jobbet är bara en körning.  

**Steg 2: Ange mapreduce.map.memory/mapreduce.reduce.memory** – i vårt exempel du kör ett i/o-intensiva jobb och bestämma att 3 GB minne för kartan uppgifter är tillräckliga.

    mapreduce.map.memory = 3GB
**Steg 3: Fastställa totala YARN-minne**

    total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Steg 4: Beräkna antal YARN-behållare**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Steg 5: Ange mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="limitations"></a>Begränsningar

**Data Lake Storage Gen1 begränsning**

Som en tjänst för flera innehavare anger Data Lake Storage Gen1 gränser för konto på bandbredd.  Om du stöter på dessa gränser, börjar du se Aktivitetsfel. Detta framgår av får begränsningsfel i loggarna för uppgiften.  Om du behöver mer bandbredd för jobbet kan du kontakta oss.   

Om du vill kontrollera om du har komma begränsats, måste du Aktivera felsökningsloggning på klientsidan. Här är hur du kan göra det:

1. Placera följande egenskap i log4j-egenskaper i Ambari > YARN > konfiguration > avancerade yarn-log4j: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Starta om alla noder/tjänsten för konfiguration ska börja gälla.

3. Om du är komma begränsad visas felkoden HTTP 429 i YARN-loggfilen. YARN-loggfilen finns i /tmp/&lt;användare&gt;/yarn.log

## <a name="examples-to-run"></a>Kör exempel

För att demonstrera hur MapReduce körs på Data Lake Storage Gen1, visas nedan viss exempelkod som kördes på ett kluster med följande inställningar:

* 16 noden D14v2
* Hadoop-kluster som kör HDI 3.6

Här följer några exempelkommandon för att köra MapReduce Teragen, Terasort och Teravalidate för en startpunkt.  Du kan justera de här kommandona baserat på dina resurser.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
