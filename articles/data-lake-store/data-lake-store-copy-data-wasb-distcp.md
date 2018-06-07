---
title: Kopiera data till och från WASB till Data Lake Store med Distcp | Microsoft Docs
description: Använd Distcp för att kopiera data till och från Azure Storage-Blobbar till Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: d6f4d1f7b974a3cd44e7cb9ffc2c63548f0bc321
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624404"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>Använd Distcp för att kopiera data mellan Azure Storage-blobbar och Data Lake Store
> [!div class="op_single_selector"]
> * [Använda DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Använda AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Om du har ett HDInsight-kluster med åtkomst till Data Lake Store kan du använda Hadoop-ekosystemet verktyg som Distcp för att kopiera data **till och från** ett HDInsight-kluster storage (WASB) till ett Data Lake Store-konto. Den här artikeln innehåller instruktioner om hur du använder verktyget Distcp.

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Store-konto**. Anvisningar om hur du skapar en finns [Kom igång med Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Store-konto. Se [skapar ett HDInsight-kluster med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Kontrollera att du kan aktivera Fjärrskrivbord för klustret.

## <a name="do-you-learn-fast-with-videos"></a>Lär du dig snabbt med videor?
[Det här videoklippet](https://mix.office.com/watch/1liuojvdx6sie) om hur du kopierar data mellan Azure Storage-Blobbar och Data Lake Store med hjälp av DistCp.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Använd Distcp från ett kluster i HDInsight Linux

Ett HDInsight-kluster medföljer verktyget Distcp som kan användas för att kopiera data från olika källor till ett HDInsight-kluster. Om du har konfigurerat HDInsight-klustret för att använda Data Lake Store som ytterligare lagringsutrymme, att verktyget Distcp använda out-of-the-box att kopiera data till och från ett Data Lake Store-konto. I det här avsnittet ska titta vi på hur du använder verktyget Distcp.

1. Använd SSH för att ansluta till klustret från ditt skrivbord. Se [Anslut till ett Linux-baserat HDInsight-kluster](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Kör kommandon från SSH-prompten.

2. Kontrollera om du har åtkomst till Azure Storage BLOB (WASB). Kör följande kommando:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Resultatet bör ge en lista över innehållet i blob storage.

3. På liknande sätt kan kontrollera om du har åtkomst till Data Lake Store-konto från klustret. Kör följande kommando:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    Resultatet bör ge en lista över filer/mappar i Data Lake Store-konto.

4. Använd Distcp för att kopiera data från WASB till ett Data Lake Store-konto.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    Kommandot kopierar innehållet på den **/exempel/data/gutenberg/** mapp i WASB till **/myfolder** i Data Lake Store-konto.

5. Använd Distcp på samma sätt för att kopiera data från Data Lake Store-konto till WASB.

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Kommandot kopierar innehållet på **/myfolder** Data Lake Store-konto som **/exempel/data/gutenberg/** mapp i WASB.

## <a name="performance-considerations-while-using-distcp"></a>Prestandaöverväganden när du använder DistCp

Eftersom Distcps lägsta Granularitet är en enskild fil, är ange det maximala antalet samtidiga kopior den viktigaste parametern att optimera mot Data Lake Store. Antal samtidiga kopior styrs genom att ange antalet mappers ('M ') parametern på kommandoraden. Den här parametern anger det maximala antalet mappers som används för att kopiera data. Standardvärdet är 20.

**Exempel**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Hur tar jag reda på antalet mappers ska användas?

Här är några riktlinjer som du kan använda.

* **Steg 1: Välj den totala YARN minnet** -det första steget är att avgöra YARN-minne i klustret där du kör jobbet DistCp. Den här informationen är tillgänglig i Ambari-portalen som associeras med klustret. Navigera till YARN och visar fliken konfigurationerna för att visa YARN-minne. För att få det totala minnet YARN, multiplicera YARN minne per nod med antalet noder som du har i klustret.

* **Steg 2: Beräkna antalet mappers** -värdet för **m** motsvarar kvoten av totalt YARN minne delat med storleken för YARN-behållare. YARN behållaren Storleksinformation finns i Ambari-portalen. Navigera till YARN och visar fliken konfigurationerna. YARN behållaren storleken visas i det här fönstret. Formeln att få fram antalet mappers (**m**) är

        m = (number of nodes * YARN memory for each node) / YARN container size

**Exempel**

Anta att du har en 4 D14v2s-noder i klustret och du försöker överföra 10 TB data från 10 olika mappar. Var och en av mapparna innehåller olika mängder data och filstorlekarna i mappen är olika.

* Total YARN-minne, från Ambari portal YARN-minne ligger 96 GB för en D14-nod. Så är den totala YARN minnet för kluster med fyra noder: 

        YARN memory = 4 * 96GB = 384GB

* Antal mappers - från Ambari portal du bestämma att YARN behållare storleken är 3072 för D14 klusternoder. Det är antal mappers:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Om andra program använder minne, kan du välja att bara använda en del av ditt kluster YARN minne för DistCp.

### <a name="copying-large-datasets"></a>Kopiera stora datauppsättningar

När storleken på datamängden som ska flyttas är stor (till exempel > 1 TB) eller om du har många olika mappar bör du använda flera DistCp jobb. Det finns troligen inga prestandafördelar, men det sprids ut jobb så att om något jobb misslyckas, behöver du bara att starta om det specifika jobbet i stället för hela jobbet.

### <a name="limitations"></a>Begränsningar

* DistCp försöker skapa mappers med liknande storlek för att optimera prestanda. Öka antalet mappers kanske inte alltid att öka prestanda.

* DistCp är begränsad till endast en mappning per fil. Du bör därför inte ha flera mappers än vad du har filer. Eftersom DistCp kan bara tilldela en mappning till en fil, begränsar detta mängden samtidighet som kan användas för att kopiera stora filer.

* Om du har ett litet antal stora filer, bör du dela dem i 256 MB filsegment att ge mer potentiella samtidighet. 
 
* Om du kopierar från ett Azure Blob Storage-konto, att kopiera-projekt begränsas på blob storage-sida. Detta försämrar prestanda för Kopiera projekt. Mer information om gränserna för Azure Blob Storage finns Azure Lagringsgränser på [Azure-prenumeration och tjänstbegränsningarna](../azure-subscription-service-limits.md).

## <a name="see-also"></a>Se också
* [Kopiera data från Azure Storage-Blobbar till Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
* [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
