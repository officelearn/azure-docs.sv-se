---
title: Riktlinjer för prestandajustering av Azure Data Lake Storage Gen1 Hive - Microsoft-dokument
description: Riktlinjer för prestandajustering av Azure Data Lake Storage Gen1 Hive
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
ms.openlocfilehash: 433c6b7d70cea9406b67d65e23cc357939cb5aa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61437284"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Vägledning för prestandajustering för Hive på HDInsight och Azure Data Lake Storage Gen1

Standardinställningarna har ställts in för att ge bra prestanda i många olika användningsfall.  För I/O-intensiva frågor kan Hive justeras för att få bättre prestanda med Azure Data Lake Storage Gen1.  

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Data Lake Storage Gen1-konto**. Instruktioner om hur du skapar en finns i [Komma igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Storage Gen1-konto. Se [Skapa ett HDInsight-kluster med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Se till att du aktiverar Fjärrskrivbord för klustret.
* **Köra Hive på HDInsight**.  Mer information om hur du kör Hive-jobb på HDInsight finns i [Använda Hive på HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Riktlinjer för prestandajustering för DataSjölagring Gen1**.  Allmänna prestandabegrepp finns i [Prestandajusteringsvägledning för Datasjölagring Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parametrar

Här är de viktigaste inställningarna för att ställa in för förbättrad Data Lake Storage Gen1 prestanda:

* **hive.tez.container.size** – mängden minne som används av varje uppgift

* **tez.grouping.min-size** – minsta storlek på varje mapper

* **tez.grouping.max-size** – maximal storlek på varje mapper

* **hive.exec.reducer.bytes.per.reducer** – storleken på varje reducer

**hive.tez.container.size** - Behållarstorleken avgör hur mycket minne som är tillgängligt för varje aktivitet.  Detta är den viktigaste indata för att kontrollera samtidighet i Hive.  

**tez.grouping.min-size** – Med den här parametern kan du ställa in den minsta storleken på varje mapper.  Om antalet mappers som Tez väljer är mindre än värdet för den här parametern, kommer Tez att använda det värde som anges här.

**tez.grouping.max-size** – Parametern låter dig ställa in den maximala storleken på varje mapper.  Om antalet mappers som Tez väljer är större än värdet för den här parametern, kommer Tez att använda det värde som anges här.

**hive.exec.reducer.bytes.per.reducer** – Den här parametern anger storleken på varje reducer.  Som standard är varje reducer 256 MB.  

## <a name="guidance"></a>Riktlinjer

**Ange hive.exec.reducer.bytes.per.reducer** – Standardvärdet fungerar bra när data är okomprimerade.  För data som komprimeras bör du minska storleken på reduceraren.  

**Ange hive.tez.container.size** – I varje nod anges minnet av yarn.nodemanager.resource.memory-mb och bör vara korrekt inställt på HDI-kluster som standard.  Mer information om hur du ställer in lämpligt minne i YARN finns i det här [inlägget](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

I/O-intensiva arbetsbelastningar kan dra nytta av mer parallellism genom att minska Tez-behållarens storlek. Detta ger användaren fler behållare vilket ökar samtidigheten.  Vissa Hive-frågor kräver dock en betydande mängd minne (t.ex.  Om aktiviteten inte har tillräckligt med minne får du ett undantag från minnet under körning.  Om du får undantag från minnet bör du öka minnet.   

Det samtidiga antalet aktiviteter som körs eller parallellism kommer att begränsas av det totala YARN-minnet.  Antalet YARN-behållare avgör hur många samtidiga aktiviteter som kan köras.  För att hitta YARN-minnet per nod kan du gå till Ambari.  Navigera till YARN och visa fliken Configs.  YARN-minnet visas i det här fönstret.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
Nyckeln till att förbättra prestanda med hjälp av Data Lake Storage Gen1 är att öka samtidigheten så mycket som möjligt.  Tez beräknar automatiskt antalet aktiviteter som ska skapas så att du inte behöver ställa in den.   

## <a name="example-calculation"></a>Exempelberäkning

Anta att du har ett D14-kluster på 8 nod.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Begränsningar

**Data Lake Storage Gen1 begränsning** 

Om du når gränserna för bandbredd som tillhandahålls av Data Lake Storage Gen1, skulle du börja se aktivitetsfel. Detta kan identifieras genom att observera begränsningsfel i aktivitetsloggar.  Du kan minska parallellismen genom att öka Tez containerstorlek.  Om du behöver mer samtidighet för ditt jobb, vänligen kontakta oss.

För att kontrollera om du får strypt, måste du aktivera felsökningsloggning på klientsidan. Så här kan du göra det:

1. Placera följande egenskap i log4j-egenskaperna i Hive config. Detta kan göras från Ambari-vyn: log4j.logger.com.microsoft.azure.datalake.store=DEBUG Starta om alla noder/-tjänster för att konfigurationen ska börja gälla.

2. Om du får begränsat ser du FELKODEN HTTP 429 i registreringsfilen för registreringsdatafilen. Hive-loggfilen finns i /tmp/&lt;user&gt;/hive.log

## <a name="further-information-on-hive-tuning"></a>Ytterligare information om Hive tuning

Här är några bloggar som hjälper till att ställa in dina Hive frågor:
* [Optimera Hive-frågor för Hadoop i HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Felsökning av hive-frågeprestanda](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Antända prata om att optimera Hive på HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
