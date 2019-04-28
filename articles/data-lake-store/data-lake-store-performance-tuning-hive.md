---
title: Azure Data Lake Storage Gen1 Hive prestandajustering riktlinjer | Microsoft Docs
description: Azure Data Lake Storage Gen1 Hive prestandajustering riktlinjer
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437284"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Prestandajusteringsvägledning för Hive på HDInsight och Azure Data Lake Storage Gen1

Standardinställningarna har ställts in för att tillhandahålla bra prestanda i många olika användningsfall.  För i/o-intensiva frågor, kan du ställa in Hive för att få bättre prestanda med Azure Data Lake Storage Gen1.  

## <a name="prerequisites"></a>Nödvändiga komponenter

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett konto för Data Lake Storage Gen1**. Anvisningar för hur du skapar ett finns i [Kom igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Storage Gen1-konto. Se [skapa ett HDInsight-kluster med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Kontrollera att du aktivera Fjärrskrivbord för klustret.
* **Köra Hive på HDInsight**.  Läs om hur du kör Hive-jobb på HDInsight i [använda Hive i HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Riktlinjer för Data Lake Storage Gen1 för prestandajustering**.  Allmänna prestanda begrepp, se [Data Lake Storage Gen1 justering Prestandavägledning](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parametrar

Här är de viktigaste inställningarna att justera för bättre prestanda för Data Lake Storage Gen1:

* **hive.tez.container.size** – mängden minne som används av varje uppgifter

* **tez.Grouping.min storlek** – minsta storleken på varje mapper

* **tez.Grouping.Max storlek** – det maximala storleken på varje mapper

* **hive.Exec.Reducer.bytes.per.Reducer** – storleken på varje reducer

**hive.tez.container.size** -behållarens storlek avgör hur mycket minne som är tillgänglig för varje aktivitet.  Det här är den viktigaste indata för att styra samtidighet i Hive.  

**tez.Grouping.min storlek** – den här parametern kan du ange den minsta storleken på varje mappning.  Om antalet Mappningskomponenter som Tez väljer är mindre än värdet för den här parametern kan använda Tez värdet som anges här.

**tez.Grouping.Max storlek** – parametern kan du ange den maximala storleken för varje mappning.  Om antalet Mappningskomponenter som Tez väljer är större än värdet för den här parametern kan använda Tez värdet som anges här.

**hive.Exec.Reducer.bytes.per.Reducer** – den här parametern anger storleken på varje reducer.  Som standard är varje reducer 256MB.  

## <a name="guidance"></a>Riktlinjer

**Ange hive.exec.reducer.bytes.per.reducer** – standardvärdet fungerar bra när data är okomprimerade.  För data som är komprimerade, ska du minska storleken på reducer.  

**Ange hive.tez.container.size** – i varje nod minne anges av yarn.nodemanager.resource.memory mb och korrekt ska ställas in på HDI-kluster som standard.  Mer information om hur minnet som är lämplig i YARN finns i den här [publicera](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

I/o-intensiva arbetsbelastningar kan dra nytta av flera parallellitet genom att minska Tez behållarens storlek. Detta ger användaren fler behållare, vilket ökar samtidighet.  Vissa Hive-frågor kräver dock en betydande mängd minne (t.ex. MapJoin).  Om aktiviteten inte har tillräckligt med minne, får du en out of undantag med otillräckligt minne under körning.  Om du får slut minne-undantag, bör du öka minnet.   

Samtidiga antalet aktiviteter som körs eller parallellitet ska begränsas av den totala mängden YARN-minnet.  Antalet YARN-behållare som bestämmer hur många samtidiga uppgifter kan köras.  För att hitta YARN-minne per nod går du till Ambari.  Gå till YARN och visa fliken konfigurationer.  YARN-minne visas i det här fönstret.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
Nyckeln till att förbättra prestanda med hjälp av Data Lake Storage Gen1 är att öka parallellkörningen så mycket som möjligt.  Tez beräknar automatiskt antalet uppgifter som ska skapas, så du inte behöver konfigurera den.   

## <a name="example-calculation"></a>Exempel-beräkning

Vi antar att du har ett 8 noder D14-kluster.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Begränsningar

**Data Lake Storage Gen1 begränsning** 

Om du stöter på gränserna för bandbredd som tillhandahålls av Data Lake Storage Gen1 börjar du se Aktivitetsfel. Det gick att identifiera får begränsningsfel i loggarna för uppgiften.  Du kan minska parallellitet genom att öka Tez behållarens storlek.  Om du behöver större samtidighet för jobbet kan du kontakta oss.

Om du vill kontrollera om du har komma begränsats, måste du Aktivera felsökningsloggning på klientsidan. Här är hur du kan göra det:

1. Placera följande egenskap i log4j-egenskaperna i Hive-konfigurationen. Detta kan göras från Ambari vyn: log4j.logger.com.microsoft.azure.datalake.store=DEBUG starta om alla noder/tjänsten för konfiguration ska börja gälla.

2. Om du är komma begränsad visas felkoden HTTP 429 i loggfilen hive. Hive-loggfilen finns i /tmp/&lt;användare&gt;/hive.log

## <a name="further-information-on-hive-tuning"></a>Mer information om finjustering av Hive

Här följer några bloggar som hjälper dig att justera dina Hive-frågor:
* [Optimera Hive-frågor för Hadoop i HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Felsökning av prestanda för Hive-frågor](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Ignite talk på optimera Hive på HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
