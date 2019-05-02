---
title: Azure Data Lake Storage Gen2 Hive prestandajustering riktlinjer | Microsoft Docs
description: Azure Data Lake Storage Gen2 Hive prestandajustering riktlinjer
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b44c615396fbd526efb687977d303facd5d5d607
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939437"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen2"></a>Prestandajusteringsvägledning för Hive på HDInsight och Azure Data Lake Storage Gen2

Standardinställningarna har ställts in för att tillhandahålla bra prestanda i många olika användningsfall.  För i/o-intensiva frågor, kan du ställa in Hive för att få bättre prestanda med Azure Data Lake Storage Gen2.  

## <a name="prerequisites"></a>Nödvändiga komponenter

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett konto för Data Lake Storage Gen2**. Anvisningar för hur du skapar ett finns i [snabbstarten: Skapa ett Azure Data Lake Storage Gen2-lagringskonto](data-lake-storage-quickstart-create-account.md)
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Storage Gen2-konto. Se [Använd Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **Köra Hive på HDInsight**.  Läs om hur du kör Hive-jobb på HDInsight i [använda Hive i HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Riktlinjer för Data Lake Storage Gen2 för prestandajustering**.  Allmänna prestanda begrepp, se [Data Lake Storage Gen2 justering Prestandavägledning](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parametrar

Här är de viktigaste inställningarna att justera för bättre prestanda för Data Lake Storage Gen2:

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
Nyckeln till att förbättra prestanda med hjälp av Data Lake Storage Gen2 är att öka parallellkörningen så mycket som möjligt.  Tez beräknar automatiskt antalet uppgifter som ska skapas, så du inte behöver konfigurera den.   

## <a name="example-calculation"></a>Exempel-beräkning

Vi antar att du har ett 8 noder D14-kluster.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="further-information-on-hive-tuning"></a>Mer information om finjustering av Hive

Här följer några bloggar som hjälper dig att justera dina Hive-frågor:
* [Optimera Hive-frågor för Hadoop i HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Felsökning av prestanda för Hive-frågor](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Ignite talk på optimera Hive på HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
