---
title: Azure Data Lake Storage Gen1 Spark prestandajustering riktlinjer | Microsoft Docs
description: Azure Data Lake Storage Gen1 Spark prestandajustering riktlinjer
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
ms.openlocfilehash: dc92e7d2fcc911aeb6d92b91dd2d430af3c502ad
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2019
ms.locfileid: "54401715"
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Prestandajusteringsvägledning för Spark på HDInsight och Azure Data Lake Storage Gen1

Du måste fundera över hur många appar som körs i klustret när du anpassar prestanda på Spark.  Som standard kan du köra 4 appar samtidigt på HDI-kluster (Obs: standardinställningen kan komma att ändras).  Du kan välja att använda färre appar så att du kan åsidosätta standardinställningarna och använda flera av klustret för dessa appar.  

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett konto med Azure Data Lake Storage Gen1**. Anvisningar för hur du skapar ett finns i [Kom igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Storage Gen1-konto. Se [skapa ett HDInsight-kluster med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Kontrollera att du aktivera Fjärrskrivbord för klustret.
* **Köra Spark-kluster på Data Lake Storage Gen1**.  Mer information finns i [använda HDInsight Spark-kluster för att analysera data i Data Lake Storage Gen1](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Riktlinjer för Data Lake Storage Gen1 för prestandajustering**.  Allmänna prestanda begrepp, se [Data Lake Storage Gen1 justering Prestandavägledning](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance) 

## <a name="parameters"></a>Parametrar

När du kör Spark-jobb, är här de viktigaste inställningar som kan anpassas för att öka prestanda på Data Lake Storage Gen1:

* **NUM executors** -antalet samtidiga aktiviteter som kan utföras.

* **Executor minnet** – mängden minne som allokerats till varje executor.

* **Executor kärnor** -antalet kärnor som tilldelas varje executor.                     

**NUM executors** Num executors anger det maximala antalet aktiviteter som kan köras parallellt.  Det faktiska antalet aktiviteter som kan köras parallellt begränsas av minne och CPU-resurser som är tillgängliga i klustret.

**Executor minnet** mängden minne som allokeras till varje executor.  Det minne som behövs för varje executor är beroende av jobbet.  Minnet som måste vara högre för komplexa åtgärder.  För enkla åtgärder som Läs- och skrivåtgärder blir minneskrav lägre.  Hur mycket minne för varje executor kan ses i Ambari.  Navigera till Spark i Ambari, och visar fliken konfigurationer.  

**Executor kärnor** detta anger mängden kärnor som används per executor som avgör hur många parallella trådar som kan köras per executor.  Till exempel om executor kärnor = 2, sedan varje executor kör 2 parallella uppgifter i executor.  Executor-kärnor behövs är beroende av jobbet.  I/o tunga jobb kräver inte en stor mängd minne per aktivitet så att varje executor kan hantera flera parallella uppgifter.

Som standard har två virtuella kärnor i YARN definierats för varje fysisk kärna när du kör Spark på HDInsight.  Det här talet ger en bra balans mellan samtidighet och mängden kontext som byter från flera trådar.  

## <a name="guidance"></a>Riktlinjer

När du kör Spark analytiska arbetsbelastningar för att arbeta med data i Data Lake Storage Gen1, rekommenderar vi att du använder den senaste versionen av HDInsight för att uppnå optimala prestanda med Data Lake Storage Gen1. När jobbet är flera i/o-intensiva, kan vissa parametrar konfigureras för att förbättra prestanda.  Data Lake Storage Gen1 är en mycket skalbar lagring-plattform som kan hantera stora dataflöden.  Om jobbet består främst av Läs- eller skrivåtgärder, kan ökad samtidighet för i/o till och från Data Lake Storage Gen1 öka prestanda.

Det finns några Allmänt sätt att öka parallellkörningen för i/o-intensiva jobb.

**Steg 1: Fastställa hur många appar körs i klustret** – du bör känna till hur många appar körs i klustret, inklusive den aktuella artikeln.  Standardvärden för varje Spark utgår ifrån att det finns 4 appar som körs samtidigt.  Därför kommer du bara ha 25% av klustret som är tillgängliga för varje app.  Du kan åsidosätta standardvärdena genom att ändra antalet executors för att få bättre prestanda.  

**Steg 2: Ange executor minne** – det första du ska ange är executor-minne.  Minnet som är beroende av det jobb som du ska köra.  Du kan öka samtidighet genom att allokera mindre minne per executor.  Om du ser ut från minne-undantag när du kör dina jobb, bör du öka värdet för den här parametern.  Ett alternativ är att få mer minne med hjälp av ett kluster som har större mängder minne eller öka storleken på ditt kluster.  Mer minne kan mer körare som ska användas, vilket innebär att större samtidighet.

**Steg 3: Ange executor kärnor** – för i/o-intensiva arbetsbelastningar som inte har komplexa åtgärder, det är bra att börja med ett stort antal executor-kärnor för att öka antalet parallella uppgifter per executor.  Ställa in executor kärnor 4 är en bra början.   

    executor-cores = 4
Öka antalet executor kärnor ger dig flera parallellitet så att du kan experimentera med olika executor-kärnor.  Du bör minska antalet kärnor per executor för jobb som har mer komplexa åtgärder.  Om executor-kärnor är kan högre än 4, sedan skräpinsamling bli ineffektiva och försämra prestanda.

**Steg 4: Fastställa YARN minne kluster** – den här informationen är tillgänglig i Ambari.  Gå till YARN och visa fliken konfigurationer.  YARN-minne visas i det här fönstret.  
Obs När du arbetar i fönstret kan du också se standardstorlek för YARN-behållare.  YARN-behållarens storlek är samma som minne per executor parametern.

    Total YARN memory = nodes * YARN memory per node
**Steg 5: Beräkna num executors**

**Beräkna minne begränsningen** -parametern num executors begränsas genom minne eller CPU.  Minne-begränsningen bestäms av mängden tillgängligt minne i YARN för ditt program.  Du bör ta totalt YARN-minne och delar av executor-minne.  Begränsningen måste vara ta bort skalade för antalet appar så att vi dela med antalet appar.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**Beräkna CPU-begränsning** – The CPU-begränsning beräknas som de totala virtuella kärnor dividerat med antalet kärnor per executor.  Det finns 2 virtuella kärnor för varje fysisk kärna.  Liknar begränsningen minne, har vi division med antalet appar.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Ange num executors** – parametern num executors bestäms genom att utföra minimum för den minne och CPU-begränsningen. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
Anger ett högre antal num executors öka inte nödvändigtvis prestanda.  Du bör överväga att lägga till fler executors lägger till extra administration för varje ytterligare executor som potentiellt kan försämra prestanda.  NUM executors är bundet klusterresurserna.    

## <a name="example-calculation"></a>Exempel-beräkning

Anta att du har ett kluster som består av 8 D4v2 noder med 2 appar, inklusive den som du ska köra.  

**Steg 1: Fastställa hur många appar körs i klustret** – du vet att du har 2 appar på ditt kluster, inklusive den som du ska köra.  

**Steg 2: Ange executor minne** – det här exemplet vi fastställer att 6 GB executor minne är tillräcklig för i/o-intensiva jobb.  

    executor-memory = 6GB
**Steg 3: Ange executor kärnor** – eftersom det här är ett i/o-intensiva jobb, vi kan ange antalet kärnor för varje executor till 4.  Att ställa in kärnor per executor större än 4 kan orsaka problem för skräpinsamling samling.  

    executor-cores = 4
**Steg 4: Fastställa YARN minne kluster** – vi navigerar till Ambari att ta reda på att varje D4v2 har 25 GB minne för YARN.  Eftersom det finns 8 noder är multipliceras det tillgängliga minnet i YARN med 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**Steg 5: Beräkna num executors** – parametern num executors bestäms genom att utföra minimum för begränsningen minne och CPU-begränsning dividerat med antal appar som körs på Spark.    

**Beräkna minne begränsningen** – begränsningen minne beräknas som den totala mängden minne för YARN dividerat med minne per executor.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**Beräkna CPU-begränsning** – The CPU-begränsning beräknas som de totala yarn-kärnor dividerat med antalet kärnor per executor.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Ange num-executors**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    

