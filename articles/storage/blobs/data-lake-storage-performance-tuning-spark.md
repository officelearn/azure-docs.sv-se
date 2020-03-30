---
title: 'Justera prestanda: Spark, HDInsight & Azure Data Lake Storage Gen2 | Microsoft-dokument'
description: Azure Data Lake Storage Gen2 Spark Prestandajustering Riktlinjer
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a70b8112af201a49e7eece8b689e75102ec55880
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327553"
---
# <a name="tune-performance-spark-hdinsight--azure-data-lake-storage-gen2"></a>Justera prestanda: Spark, HDInsight & Azure Data Lake Storage Gen2

När du justerar prestanda på Spark måste du ta hänsyn till antalet appar som körs i klustret.  Som standard kan du köra 4 appar samtidigt i HDI-klustret (Obs: standardinställningen kan komma att ändras).  Du kan välja att använda färre appar så att du kan åsidosätta standardinställningarna och använda mer av klustret för dessa appar.  

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Storage Gen2-konto**. Instruktioner om hur du skapar ett finns i [Snabbstart: Skapa ett Azure Data Lake Storage Gen2-lagringskonto](data-lake-storage-quickstart-create-account.md).
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Storage Gen2-konto. Se [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Se till att du aktiverar Fjärrskrivbord för klustret.
* **Köra Spark-kluster på Data Lake Storage Gen2**.  Mer information finns i [Använda HDInsight Spark-kluster för att analysera data i Gen2 för lagring av datasjö](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Riktlinjer för prestandajustering för Data Lake Storage Gen2**.  Allmänna prestandabegrepp finns i [Prestandajusteringsvägledning för Datasjölagring Gen2](data-lake-storage-performance-tuning-guidance.md) 

## <a name="parameters"></a>Parametrar

När du kör Spark-jobb är här de viktigaste inställningarna som kan justeras för att öka prestanda på Data Lake Storage Gen2:

* **Num-executors** - Antalet samtidiga aktiviteter som kan utföras.

* **Executor-minne** - Mängden minne som allokeras till varje executor.

* **Executor-cores** - Antalet kärnor som allokerats till varje utförare.                     

**Num-utförare** Num-runor anger det maximala antalet aktiviteter som kan köras parallellt.  Det faktiska antalet aktiviteter som kan köras parallellt avgränsas av de minnes- och PROCESSOR-resurser som är tillgängliga i klustret.

**Executor-minne** Detta är den mängd minne som allokeras till varje utförare.  Det minne som behövs för varje utförare är beroende av jobbet.  För komplexa åtgärder måste minnet vara högre.  För enkla åtgärder som att läsa och skriva, kommer minneskraven att vara lägre.  Mängden minne för varje utförare kan visas i Ambari.  I Ambari navigerar du till Spark och visar fliken Configs.  

**Executor-kärnor** Detta anger antalet kärnor som används per executor, som bestämmer antalet parallella trådar som kan köras per utförare.  Om exempelvis executor-cores = 2, kan varje utförare köra 2 parallella uppgifter i utföraren.  De executor-kärnor som behövs kommer att vara beroende av jobbet.  I/O-tunga jobb kräver inte en stor mängd minne per uppgift så att varje utförare kan hantera fler parallella uppgifter.

Som standard definieras två virtuella YARN-kärnor för varje fysisk kärna när spark körs på HDInsight.  Detta nummer ger en bra balans av samtidighet och mängd kontextväxling från flera trådar.  

## <a name="guidance"></a>Riktlinjer

När du kör Spark-analytiska arbetsbelastningar för att arbeta med data i Data Lake Storage Gen2 rekommenderar vi att du använder den senaste HDInsight-versionen för att få bästa prestanda med Data Lake Storage Gen2. När jobbet är mer I/O-intensivt kan vissa parametrar konfigureras för att förbättra prestanda.  Data Lake Storage Gen2 är en mycket skalbar lagringsplattform som kan hantera högt dataflöde.  Om jobbet huvudsakligen består av läsning eller skrivningar, kan ökad samtidighet för I/O till och från Data Lake Storage Gen2 öka prestanda.

Det finns några allmänna sätt att öka samtidigheten för I/O-intensiva jobb.

**Steg 1: Bestäm hur många appar som körs i klustret** – Du bör veta hur många appar som körs i klustret, inklusive den aktuella.  Standardvärdena för varje Spark-inställning förutsätter att det finns 4 appar som körs samtidigt.  Därför har du bara 25 % av klustret tillgängligt för varje app.  För att få bättre prestanda kan du åsidosätta standardvärdena genom att ändra antalet utförare.  

**Steg 2: Ställ in executor-minne** - Det första du ska ställa in är executor-minne.  Minnet kommer att vara beroende av det jobb som du ska köra.  Du kan öka samtidigheten genom att allokera mindre minne per utförare.  Om du ser på minnesundantag när du kör jobbet bör du öka värdet för den här parametern.  Ett alternativ är att få mer minne med hjälp av ett kluster som har högre mängder minne eller öka storleken på klustret.  Mer minne gör det möjligt att använda fler utförare, vilket innebär mer samtidighet.

**Steg 3: Ange executor-kärnor** – För I/O-intensiva arbetsbelastningar som inte har komplexa åtgärder är det bra att börja med ett stort antal executor-kärnor för att öka antalet parallella aktiviteter per utförare.  Att ställa in executor-kärnor till 4 är en bra början.   

    executor-cores = 4
Öka antalet executor-kärnor kommer att ge dig mer parallellism så att du kan experimentera med olika executor-kärnor.  För jobb som har mer komplexa åtgärder bör du minska antalet kärnor per utförare.  Om executor-kärnor är inställda högre än 4, då skräpinsamling kan bli ineffektiv och försämra prestanda.

**Steg 4: Bestäm mängden YARN-minne i klustret** – Den här informationen är tillgänglig i Ambari.  Navigera till YARN och visa fliken Configs.  YARN-minnet visas i det här fönstret.  
Observera medan du är i fönstret kan du också se standardvärdet för YARN-behållaren.  YARN-behållarstorleken är samma som parametern minne per executor.

    Total YARN memory = nodes * YARN memory per node
**Steg 5: Beräkna antal executors**

**Beräkna minnesbegränsning** - parametern num-executors begränsas antingen av minne eller av CPU.  Minnesbegränsningen bestäms av mängden tillgängligt YARN-minne för ditt program.  Du bör ta totalt YARN minne och dividera det med executor-minne.  Begränsningen måste skalas av för antalet appar så att vi dividerar med antalet appar.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**Beräkna CPU-begränsning** - CPU-begränsningen beräknas som den totala virtuella kärnor dividerat med antalet kärnor per executor.  Det finns 2 virtuella kärnor för varje fysisk kärna.  I likhet med minnesbegränsningen måste vi dividera med antalet appar.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Ange num-executors** – Parametern num-executors bestäms genom att ta ett minimum av minnesbegränsningen och CPU-begränsningen. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
Om du anger ett större antal num-executors ökar inte nödvändigtvis prestanda.  Du bör överväga att lägga till fler utförare kommer att lägga till extra omkostnader för varje ytterligare executor, vilket potentiellt kan försämra prestanda.  Num-executors avgränsas av klusterresurserna.    

## <a name="example-calculation"></a>Exempelberäkning

Anta att du för närvarande har ett kluster som består av 8 D4v2-noder som kör 2 appar, inklusive den du ska köra.  

**Steg 1: Bestäm hur många appar som körs i klustret** – du vet att du har två appar i klustret, inklusive den du ska köra.  

**Steg 2: Ange executor-minne** – i det här exemplet bestämmer vi att 6 GB executor-minne kommer att vara tillräckligt för I/O-intensivt jobb.  

    executor-memory = 6GB
**Steg 3: Ange executor-kärnor** – Eftersom detta är ett I/O-intensivt jobb kan vi ställa in antalet kärnor för varje utförare till 4.  Om du ställer in kärnor per utförare på större än 4 kan det orsaka problem med skräpinsamlingen.  

    executor-cores = 4
**Steg 4: Bestäm mängden YARN-minne i klustret** – Vi navigerar till Ambari för att ta reda på att varje D4v2 har 25 GB YARN-minne.  Eftersom det finns 8 noder multipliceras det tillgängliga YARN-minnet med 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**Steg 5: Beräkna num-executors** – Parametern num-executors bestäms genom att ta ett minimum av minnesbegränsningen och CPU-begränsningen dividerat med # för appar som körs på Spark.    

**Beräkna minnesbegränsning** – Minnesbegränsningen beräknas som det totala YARN-minnet delat med minnet per utförare.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**Beräkna CPU-begränsning** - CPU-begränsningen beräknas som den totala garnkärnorna dividerat med antalet kärnor per utförare.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Ange num-executors**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    

