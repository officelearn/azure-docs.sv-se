---
title: Azure Data Lake Store Spark prestandajustering riktlinjer | Microsoft Docs
description: Azure Data Lake Store Spark prestandajustering riktlinjer
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
ms.openlocfilehash: a807bea13063d2a0b3c1c71ddb6c98aa2d2568d3
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-store"></a>Prestandajustering för Spark i HDInsight och Azure Data Lake Store

När justera prestanda på Spark, måste du fundera över hur många appar som körs på klustret.  Som standard kan du köra 4 appar samtidigt på HDI-klustret (Obs: standardinställningen kan ändras).  Du kan välja att använda färre appar så att du kan åsidosätta standardinställningarna och använder flera av klustret för dessa appar.  

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Store-konto**. Anvisningar om hur du skapar en finns [Kom igång med Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Store-konto. Se [skapar ett HDInsight-kluster med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Kontrollera att du kan aktivera Fjärrskrivbord för klustret.
* **Köra Spark-kluster på Azure Data Lake Store**.  Mer information finns i [använda HDInsight Spark-klustret för att analysera data i Data Lake Store](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Prestandajustering riktlinjer för ADLS**.  Allmänna prestanda begrepp finns [Data Lake Store justera Prestandaråd](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance) 

## <a name="parameters"></a>Parametrar

När du kör Spark jobb, är här de viktigaste inställningar som kan anpassas för att öka prestanda hos ADLS:

* **NUM executors** -antalet samtidiga uppgifter som kan utföras.

* **Utföraren minne** -mängden minne som allokerats för varje utförare.

* **Utföraren kärnor** -antal kärnor som tilldelas varje utförare.                     

**NUM executors** Num executors anger det maximala antalet uppgifter som kan köras parallellt.  Det faktiska antalet uppgifter som kan köras parallellt begränsas av minne och processorresurser som är tillgängliga i klustret.

**Utföraren minne** detta är mängden minne som allokeras till varje utförare.  Det minne som krävs för varje utföraren är beroende av jobbet.  Minnet som måste vara högre för komplexa åtgärder.  För enkla åtgärder som att läsa och skriva vara minneskrav lägre.  Mängden minne för varje utförare kan visas i Ambari.  Navigera till Spark i Ambari, och visar fliken konfigurationerna.  

**Utföraren kärnor** Anger mängden kärnor används per utföraren som avgör hur många parallella trådar som kan köras per utförare.  Till exempel om utföraren kärnor = 2, sedan varje utförare kan köra 2 parallella aktiviteter i utföraren.  Utföraren-kärnor behövs är beroende av jobbet.  I/o tunga jobb kräver inte en stor mängd minne per aktivitet så att varje utförare kan hantera flera parallella aktiviteter.

Som standard definieras två virtuella YARN kärnor för varje fysiska kärnor när du kör Spark på HDInsight.  Det här antalet ger en bra balans mellan concurrecy och mängden kontexten växlar från flera trådar.  

## <a name="guidance"></a>Riktlinjer

När du kör Spark analytiska arbetsbelastningar för att arbeta med data i Data Lake Store, rekommenderar vi att du använder den senaste versionen av HDInsight för att få bästa prestanda med Data Lake Store. När jobbet är flera i/o-intensiva, kan vissa parametrar konfigureras för att förbättra prestanda.  Azure Data Lake Store är en mycket skalbar lagring-plattform som kan hantera högt genomflöde.  Om jobbet består i huvudsak av läsning eller skrivning, kan öka samtidighet för i/o till och från Azure Data Lake Store öka prestanda.

Det finns några allmänna metoder för att öka parallellkörningen för i/o-intensiva jobb.

**Steg 1: Ta reda på hur många appar använder om klustret** – du bör känna till hur många appar körs på klustret, inklusive aktuell.  Standardvärden för varje Spark inställningen förutsätter att det finns 4 appar som körs samtidigt.  Därför ska du bara ha 25% av klustret för varje app.  Du kan åsidosätta standardvärdena genom att ändra antalet Utförarna för att få bättre prestanda.  

**Steg 2: Ange utföraren minne** – det första du ska ange är utföraren-minne.  Minnet som är beroende av det jobb som du ska köra.  Du kan öka samtidighet genom att allokera mindre minne per utförare.  Om du ser slut på minne undantag när du kör jobbet, bör du öka värdet för den här parametern.  Ett alternativ är att hämta mer minne med hjälp av ett kluster med större mängder minne eller öka storleken på ditt kluster.  Mer minne kan flera executors som ska användas, vilket innebär att flera samtidiga.

**Steg 3: Ange utföraren kärnor** – för i/o-intensiv arbetsbelastning som inte har komplex, är det bra att börja med ett stort antal utföraren-kärnor för att öka antalet parallella aktiviteter per utförare.  Ange utförare kärnor 4 är en bra start.   

    executor-cores = 4
Öka antalet kärnor utföraren får du mer parallellitet så att du kan experimentera med olika utföraren-kärnor.  För jobb som har mer komplicerade åtgärder, bör du minska antalet kärnor per utförare.  Om utföraren kärnor är kan högre än 4, sedan skräpinsamling vara ineffektivt och försämra prestanda.

**Steg 4: Fastställ minnesmängden YARN i klustret** – den här informationen är tillgänglig i Ambari.  Navigera till YARN och visar fliken konfigurationerna.  YARN-minne visas i det här fönstret.  
Obs: när du arbetar i fönstret kan du också se standardstorleken YARN behållare.  YARN behållaren storleken är samma som minne per utföraren parameter.

    Total YARN memory = nodes * YARN memory per node
**Steg 5: Beräkna num executors**

**Beräkna minne begränsningen** -parametern num executors är begränsad genom minne eller CPU.  Minne-begränsningen bestäms av mängden tillgängligt minne i YARN för ditt program.  Du bör ta totalt YARN-minne och delar av utföraren minne.  Begränsningen måste vara Frigör skalade för antalet appar så att vi delar med antalet appar.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**Beräkna CPU-begränsningen** -begränsning av processor beräknas som de totala virtuella kärnor dividerat med antalet kärnor per utförare.  Det finns 2 virtuella kärnor för varje fysiska kärnor.  Liknar begränsningen minne, har vi division med antalet appar.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Ange num executors** – parametern num executors bestäms genom att göra minimum för den minne och CPU-begränsningen. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
Anger ett högre antal num executors öka inte nödvändigtvis prestanda.  Du bör överväga att lägga till flera executors lägger till extra administration för varje ytterligare utföraren som potentiellt kan försämra prestanda.  NUM executors är bundet klusterresurserna.    

## <a name="example-calculation"></a>Exempel beräkning

Anta att du har ett kluster som består av 8 D4v2 noder som kör 2 appar inklusive det som du ska köra.  

**Steg 1: Ta reda på hur många appar använder om klustret** – du vet att du har 2 appar på klustret, inklusive det som du ska köra.  

**Steg 2: Ange utföraren minne** – i det här exemplet bestämma att 6 GB minne utföraren räcka för i/o-intensiva jobbet.  

    executor-memory = 6GB
**Steg 3: Ange utföraren kärnor** – eftersom det är ett i/o-intensiva jobb vi kan ange antalet kärnor för varje utföraren till 4.  Ange kärnor per utföraren till större än 4 kan orsaka skräp samling problem.  

    executor-cores = 4
**Steg 4: Fastställ minnesmängden YARN i klustret** – vi gå till Ambari ta reda på att varje D4v2 har 25 GB YARN minne.  Eftersom det finns 8 noder, multipliceras det tillgängliga minnet YARN med 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**Steg 5: Beräkna num executors** – parametern num executors bestäms av tar minst begränsningen minne och CPU-begränsningen dividerat med antal appar som körs på Spark.    

**Beräkna minne begränsningen** – begränsningen minne beräknas som den totala mängden minne för YARN dividerat med minne per utförare.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**Beräkna CPU-begränsningen** -begränsning av processor beräknas som de totala yarn kärnor dividerat med antalet kärnor per utförare.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Ange num-executors**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    

