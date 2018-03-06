---
title: Azure Data Lake Store MapReduce prestandajustering riktlinjer | Microsoft Docs
description: Azure Data Lake Store MapReduce prestandajustering riktlinjer
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 522e03769a8f09acd88d92d72c4658407b86bd0b
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-store"></a>Prestandajustering för MapReduce på HDInsight och Azure Data Lake Store


## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Store-konto**. Anvisningar om hur du skapar en finns [Kom igång med Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Store-konto. Se [skapar ett HDInsight-kluster med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Kontrollera att du kan aktivera Fjärrskrivbord för klustret.
* **Använda MapReduce på HDInsight**.  Mer information finns i [använda MapReduce i Hadoop i HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)  
* **Prestandajustering riktlinjer för ADLS**.  Allmänna prestanda begrepp finns [Data Lake Store justera Prestandaråd](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)  

## <a name="parameters"></a>Parametrar

När du kör MapReduce-jobb, är här de viktigaste parametrarna som du kan konfigurera för att öka prestanda hos ADLS:

* **Mapreduce.Map.Memory.MB** – hur mycket minne som ska allokeras till varje mapper
* **Mapreduce.Job.Maps** – antal kartan uppgifter per jobb
* **Mapreduce.Reduce.Memory.MB** – hur mycket minne som ska allokeras till varje reducer
* **Mapreduce.Job.reduces** – antal minska uppgifter per jobb

**Mapreduce.Map.Memory / Mapreduce.reduce.memory** siffran ska justeras baserat på hur mycket minne som krävs för kartan och/eller minska aktivitet.  Standardvärdena för mapreduce.map.memory och mapreduce.reduce.memory kan visas i Ambari via Yarn-konfiguration.  Navigera till YARN i Ambari, och visar fliken konfigurationerna.  YARN-minne visas.  

**Mapreduce.Job.Maps / Mapreduce.job.reduces** det avgör det maximala antalet mappers eller förminskningsapparater ska skapas.  Antal delningar avgör hur många mappers kommer att skapas för MapReduce-jobb.  Därför kan du få mindre mappers du begärt om det finns mindre delningar än antalet mappers som begärdes.       

## <a name="guidance"></a>Riktlinjer

**Steg 1: Bestäm antalet jobb som körs** -standard MapReduce använder hela klustret på jobbet.  Du kan använda mindre klustret med mindre mappers än det finns tillgängliga behållare.  Riktlinjerna i det här dokumentet förutsätter att programmet endast program som körs på klustret.      

**Steg 2: Ange mapreduce.map.memory/mapreduce.reduce.memory** – storleken på minnet för kartan och minska uppgifter är beroende av ditt specifika jobb.  Du kan minska minnesstorleken om du vill öka parallellkörningen.  Antalet aktiviteter som körs samtidigt beror på hur många behållare.  Genom att minska mängden minne per mapper eller reducer kan fler behållare skapas, vilket aktiverar fler mappers eller förminskningsapparater körs samtidigt.  Minska mängden minne för mycket kanske vissa processer köras, slut på minne.  Om du får en heap-fel när du kör jobbet kan öka du minne per mapper eller reducer.  Du bör överväga att lägga till fler behållare lägger till extra administration för varje ytterligare behållare som potentiellt kan försämra prestanda.  Ett annat alternativ är att hämta mer minne med hjälp av ett kluster med större mängder minne eller öka antalet noder i klustret.  Mer minne aktiverar fler behållare som ska användas, vilket innebär att flera samtidiga.  

**Steg 3: Bestäm totala YARN minne** - om du vill justera mapreduce.job.maps/mapreduce.job.reduces, bör du mängden totala YARN minne som är tillgängliga för användning.  Den här informationen är tillgänglig i Ambari.  Navigera till YARN och visar fliken konfigurationerna.  YARN-minne visas i det här fönstret.  Du bör multiplicera YARN-minne med antalet noder i klustret för att hämta den totala mängden minnet på YARN.

    Total YARN memory = nodes * YARN memory per node
Om du använder ett tomt kluster kan minne vara den totala mängden minnet YARN för klustret.  Om andra program använder minne, kan du välja att bara använda en del av ditt kluster minne genom att minska antalet mappers eller förminskningsapparater antal behållare som du vill använda.  

**Steg 4: Beräkna antalet YARN behållare** – YARN-behållare kräver mängden samtidighet som är tillgängliga för projektet.  Ta totalt YARN-minne och delar av mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Steg 5: Ange mapreduce.job.maps/mapreduce.job.reduces** sägs mapreduce.job.maps/mapreduce.job.reduces minst antal tillgängliga behållare.  Du kan experimentera ytterligare genom att öka antalet mappers och förminskningsapparater för att se om du får bättre prestanda.  Tänk på att flera mappers har ytterligare kostnader så har för många mappers kan prestanda försämras.  

CPU-schemaläggning och CPU-isolering är inaktiverade som standard så att antalet YARN behållare är begränsad av minne.

## <a name="example-calculation"></a>Exempel beräkning

Anta att du har ett kluster som består av 8 D14 noder och du vill köra ett i/o-intensiva jobb.  Här följer de beräkningar som du bör göra:

**Steg 1: Bestäm antalet jobb som körs** -i vårt exempel antar vi att våra jobbet är den enda körs.  

**Steg 2: Ange mapreduce.map.memory/mapreduce.reduce.memory** – i vårt exempel du kör ett i/o-intensiva jobb och bestämmer 3 GB minne för kartan uppgifter är tillräckligt.

    mapreduce.map.memory = 3GB
**Steg 3: Bestäm totala YARN-minne**

    total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Steg 4: Beräkna antal YARN-behållare**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Steg 5: Ange mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="limitations"></a>Begränsningar

**ADLS-begränsning**

Som en tjänst med flera innehavare anger ADLS gränser för nivån bandbredd.  Om du träffar dessa gränser, startar du att se misslyckade aktiviteter. Det kan identifieras av sett bandbreddsbegränsning fel i loggarna för aktiviteten.  Om du behöver mer bandbredd för jobbet, kontaktar du oss.   

Om du vill kontrollera om du har komma begränsats, måste du Aktivera felsökningsloggning på klientsidan. Här visas hur du kan göra det:

1. Placera följande egenskap i log4j-egenskaper i Ambari > YARN > Config > avancerade yarn log4j: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Starta om alla noder/för konfigurationen ska börja gälla.

3. Om du har komma begränsats visas felkoden HTTP 429 i loggfilen YARN. YARN loggfilen finns i /tmp/&lt;användare&gt;/yarn.log

## <a name="examples-to-run"></a>Exempel för att köra

För att demonstrera hur MapReduce körs på Azure Data Lake Store, visas nedan några exempelkod som körs på ett kluster med följande inställningar:

* 16 noden D14v2
* Hadoop-kluster som kör HDI 3,6

Här är några exempel för att köra MapReduce Teragen, Terasort och Teravalidate för en startpunkt.  Du kan justera de här kommandona baserat på dina resurser.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
