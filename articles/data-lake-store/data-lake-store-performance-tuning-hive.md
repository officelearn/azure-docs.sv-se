---
title: Azure Data Lake Store Hive prestandajustering riktlinjer | Microsoft Docs
description: Azure Data Lake Store Hive prestandajustering riktlinjer
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
ms.openlocfilehash: c46eb1b2da62d70337e60066ed0706c3a4fdedcf
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-store"></a>Prestandajustering för Hive i HDInsight och Azure Data Lake Store

Standardinställningarna har ställts in för att tillhandahålla goda prestanda i många olika användningsfall.  För i/o-intensiva frågor, kan du justera Hive för att få bättre prestanda med ADLS.  

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Store-konto**. Anvisningar om hur du skapar en finns [Kom igång med Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Store-konto. Se [skapar ett HDInsight-kluster med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Kontrollera att du kan aktivera Fjärrskrivbord för klustret.
* **Kör Hive i HDInsight**.  Läs om hur du kör Hive-jobb i HDInsight i () [använda Hive i HDInsight]https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Prestandajustering riktlinjer för ADLS**.  Allmänna prestanda begrepp finns [Data Lake Store justera Prestandaråd](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parametrar

Här är de viktigaste inställningarna att justera för bättre prestanda för ADLS:

* **hive.tez.container.size** – hur mycket minne som används av varje uppgifter

* **tez.Grouping.min storlek** – minsta storlek varje mapper

* **tez.Grouping.Max storlek** – maximal storlek för varje mapper

* **hive.Exec.Reducer.bytes.per.Reducer** – storlek varje reducer

**hive.tez.container.size** -behållaren storlek avgör hur mycket minne som är tillgänglig för varje aktivitet.  Det här är den huvudsakliga indatan för att styra samtidighet i Hive.  

**tez.Grouping.min storlek** – den här parametern kan du ange den minimala storleken för varje mappning.  Om antalet mappers som Tez väljer är mindre än värdet för den här parametern använder Tez värdet som anges här.

**tez.Grouping.Max storlek** – parametern kan du ange den maximala storleken för varje mappning.  Om antalet mappers som Tez väljer är större än värdet för den här parametern använder Tez värdet som anges här.

**hive.Exec.Reducer.bytes.per.Reducer** – den här parametern anger storleken på varje reducer.  Som standard är varje reducer 256MB.  

## <a name="guidance"></a>Riktlinjer

**Ange hive.exec.reducer.bytes.per.reducer** – standardvärdet fungerar bra när data är okomprimerade.  För data som komprimerats, bör du minska storleken på reducer.  

**Ange hive.tez.container.size** – på varje nod minne har angetts av yarn.nodemanager.resource.memory mb och korrekt ska anges på HDI-klustret som standard.  Mer information om hur lämpligt minne i YARN finns [efter](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

I/o-intensiva arbetsbelastningar kan utnyttja mer parallellitet genom att minska storleken för Tez-behållare. Detta ger användaren fler behållare, vilket ökar samtidighet.  Vissa Hive-frågor kräver dock en stor mängd minne (t.ex. MapJoin).  Om aktiviteten inte har tillräckligt med minne, får du en out-of minne undantag under körningen.  Om du får slut på minne undantag, bör du öka minnet.   

Samtidiga antalet aktiviteter som körs eller parallellitet kommer att begränsas av det totala minnet YARN.  Antal YARN behållare styr hur många samtidiga uppgifter kan köras.  Du kan gå till Ambari för att hitta YARN-minne per nod.  Navigera till YARN och visar fliken konfigurationerna.  YARN-minne visas i det här fönstret.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
Nyckeln till att förbättra prestanda med hjälp av ADLS är att öka parallellkörningen så mycket som möjligt.  Tez beräknar automatiskt antalet aktiviteter som bör skapas så att du inte behöver ange den.   

## <a name="example-calculation"></a>Exempel beräkning

Anta att du har en 8 noder D14 kluster.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Begränsningar

**ADLS-begränsning** 

UIf nådde gränserna för bandbredd som tillhandahålls av ADLS, börjar du vill se misslyckade aktiviteter. Det kan identifieras av sett bandbreddsbegränsning fel i loggarna för aktiviteten.  Du kan minska parallellitet genom att öka storleken på Tez-behållare.  Om du behöver mer samtidighet för jobbet, kontaktar du oss.

Om du vill kontrollera om du har komma begränsats, måste du Aktivera felsökningsloggning på klientsidan. Här visas hur du kan göra det:

1. Placera följande egenskap i log4j-egenskaper i Hive-config. Detta kan göras från Ambari vyn: log4j.logger.com.microsoft.azure.datalake.store=DEBUG starta om alla noder/för konfigurationen ska börja gälla.

2. Om du har komma begränsats visas felkoden HTTP 429 i loggfilen hive. Hive loggfilen finns i /tmp/&lt;användare&gt;/hive.log

## <a name="further-information-on-hive-tuning"></a>Mer information om Hive-inställning

Här följer några bloggar som hjälper dig att finjustera Hive-frågor:
* [Optimera Hive-frågor för Hadoop i HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Felsökning av Hive frågeprestanda](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Ignite prata på optimera Hive i HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
