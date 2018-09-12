---
title: Kopiera data till och från WASB i Azure Data Lake Storage Gen1 använda Distcp | Microsoft Docs
description: Använd Distcp för att kopiera data till och från Azure Storage BLOB till Azure Data Lake Storage Gen1
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
ms.openlocfilehash: 9740de34fe7cf7d06af1803cc6d77d7e89bbb73f
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391529"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Använd Distcp för att kopiera data mellan Azure Storage-Blobbar och Azure Data Lake Storage Gen1
> [!div class="op_single_selector"]
> * [Använda DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Använda AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Om du har ett HDInsight-kluster med åtkomst till Azure Data Lake Storage Gen1, du kan använda Hadoop-ekosystemet verktyg som Distcp för att kopiera data **till och från** ett HDInsight-kluster storage (WASB) till ett Data Lake Storage Gen1-konto. Den här artikeln innehåller anvisningar om hur du använder verktyget Distcp.

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett konto med Azure Data Lake Storage Gen1**. Anvisningar för hur du skapar ett finns i [Kom igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Storage Gen1-konto. Se [skapa ett HDInsight-kluster med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Kontrollera att du aktivera Fjärrskrivbord för klustret.

## <a name="do-you-learn-fast-with-videos"></a>Lär du dig snabbt med videor?
[Den här videon](https://mix.office.com/watch/1liuojvdx6sie) om hur du kopierar data mellan Azure Storage-Blobbar och Data Lake Storage Gen1 använda DistCp.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Använd Distcp från ett kluster i HDInsight Linux

Ett HDInsight-kluster levereras med verktyget Distcp som kan användas för att kopiera data från olika källor till ett HDInsight-kluster. Om du har konfigurerat HDInsight-kluster för att använda Data Lake Storage Gen1 som ytterligare lagringsutrymme, kan verktyget Distcp vara används out-of the box att kopiera data till och från ett Data Lake Storage Gen1-konto. I det här avsnittet ska titta vi på hur du använder verktyget Distcp.

1. Anslut till klustret via SSH från ditt skrivbord. Se [ansluta till ett Linux-baserade HDInsight-kluster](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Kör kommandon från SSH-prompten.

2. Kontrollera om du har åtkomst till Azure Storage BLOB-objekt (WASB). Kör följande kommando:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Utdata bör ge en lista över innehållet i blob storage.

3. På samma sätt kan kontrollera om du kan komma åt kontot Data Lake Storage Gen1 från klustret. Kör följande kommando:

        hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/

    Utdata bör ge en lista över filer/mappar i Data Lake Storage Gen1-konto.

4. Använd Distcp för att kopiera data från WASB till ett Data Lake Storage Gen1-konto.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder

    Kommandot kopierar innehållet i den **/exempel/data/gutenberg/** mapp i WASB till **/myfolder** i Data Lake Storage Gen1-konto.

5. På samma sätt, Använd Distcp för att kopiera data från Data Lake Storage Gen1 konto till WASB.

        hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Kommandot kopierar innehållet i **/myfolder** i Data Lake Storage Gen1 kontot **/exempel/data/gutenberg/** mapp i WASB.

## <a name="performance-considerations-while-using-distcp"></a>Prestandaöverväganden när du använder DistCp

Eftersom Distcps lägsta Granulariteten är en enskild fil, är ange det maximala antalet samtidiga kopior parametern är viktigast för att optimera den mot Data Lake Storage Gen1. Antal samtidiga kopior styrs genom att ange antalet Mappningskomponenter ('M ') parametern på kommandoraden. Den här parametern anger det maximala antalet Mappningskomponenter som används för att kopiera data. Standardvärdet är 20.

**Exempel**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Hur vet jag hur många Mappningskomponenter att använda?

Här är några riktlinjer som du kan använda.

* **Steg 1: Fastställ minne totalt YARN** – det första steget är att avgöra YARN-minne i klustret där du kör jobbet DistCp. Den här informationen är tillgänglig i Ambari-portalen som är associerade med klustret. Gå till YARN och visa fliken konfigurationer om du vill visa YARN-minne. Multiplicera YARN minne per nod med antalet noder som finns i klustret för att få den totala mängden YARN-minnet.

* **Steg 2: Beräkna antalet Mappningskomponenter** -värdet för **m** motsvarar kvoten av den totala YARN minnet dividerat med YARN-behållarens storlek. YARN-behållare Storleksinformation finns i Ambari-portalen. Gå till YARN och visa fliken konfigurationer. Behållarstorlek YARN visas i det här fönstret. Formeln ska tas emot på hur många Mappningskomponenter (**m**) är

        m = (number of nodes * YARN memory for each node) / YARN container size

**Exempel**

Anta att du har en 4 D14v2s-noder i klustret och du försöker överföra 10 TB data från 10 olika mappar. Var och en av mapparna innehåller olika mängder data och filstorlekar i varje App är olika.

* Total YARN minne – från Ambari portal YARN-minne ligger 96 GB för en D14-nod. Det är totala YARN-minnet i kluster med fyra noder: 

        YARN memory = 4 * 96GB = 384GB

* Antal Mappningskomponenter - behållarstorlek YARN ligger 3072 för klusternoder D14 från Ambari-portalen. Därför är antalet Mappningskomponenter:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Om andra program använder minne, kan du välja att bara använda en del av ditt kluster YARN minne för DistCp.

### <a name="copying-large-datasets"></a>Kopiering av stora datauppsättningar

När storleken på datauppsättningen som ska flyttas är stor (till exempel > 1 TB) eller om du har många olika mappar, bör du använda flera DistCp jobb. Det är förmodligen inga prestandaökning, men det sprids ut jobb så att om något jobb misslyckas, behöver du bara att starta om specifika jobbet i stället för hela projektet.

### <a name="limitations"></a>Begränsningar

* DistCp försöker skapa Mappningskomponenter med liknande storlekar för att optimera prestanda. Öka antalet Mappningskomponenter kanske inte alltid ökar prestanda.

* DistCp är begränsad till endast en mappning per fil. Du bör därför inte ha fler Mappningskomponenter än vad du har filer. Eftersom DistCp kan endast tilldela en mappning till en fil, begränsar detta mängden samtidighet som kan användas för att kopiera stora filer.

* Om du har ett litet antal stora filer, bör du dela dem till 256 MB filsegment att ge dig större potentiella samtidighet. 
 
* Om du kopierar från en Azure Blob Storage-konto, att kopiera-projekt begränsas på blob storage-sida. Detta försämrar prestandan för dina kopieringsjobb. Mer information om gränserna för Azure Blob Storage finns gränser för Azure Storage i [Azure-prenumeration och tjänstbegränsningar](../azure-subscription-service-limits.md).

## <a name="see-also"></a>Se också
* [Kopiera data från Azure Storage BLOB till Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
