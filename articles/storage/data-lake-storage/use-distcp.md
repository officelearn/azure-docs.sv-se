---
title: Kopiera data till Azure Data Lake lagring Gen2 Preview med Distcp | Microsoft Docs
description: Använd Distcp för att kopiera data till och från Data Lake lagring Gen2 Preview
services: storage
documentationcenter: ''
author: seguler
manager: jahogg
editor: seguler
ms.component: data-lake-storage-gen2
ms.service: storage
ms.devlang: na
ms.topic: how-to
ms.date: 06/27/2018
ms.author: seguler
ms.openlocfilehash: 073d81baca7e174872806301236f547329836c45
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113484"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-storage-gen2-preview"></a>Använd Distcp för att kopiera data mellan Azure Storage-Blobbar och Data Lake lagring Gen2 Preview

Om du har ett HDInsight-kluster med åtkomst till Azure Data Lake lagring Gen2 Preview kan du använda Hadoop-ekosystemet verktyg som [Distcp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) att kopiera data **till och från** ett HDInsight-kluster storage (WASB) i ett dataobjekt Lake lagring Gen2 kompatibla konto. Den här artikeln innehåller instruktioner om hur du använder verktyget Distcp.

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Storage-konto med Azure Data Lake Storage (förhandsgranskning)-funktionen aktiverad**. Anvisningar om hur du skapar en finns [skapa ett Azure Data Lake lagring Gen2 Preview storage-konto](quickstart-create-account.md)
* **Azure HDInsight-kluster** med åtkomst till Data Lake-standardlagringskontot. Se [Använd Azure Data Lake lagring Gen2 med Azure HDInsight-kluster](use-hdi-cluster.md). Kontrollera att du kan aktivera Fjärrskrivbord för klustret.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Använd Distcp från ett kluster i HDInsight Linux

Ett HDInsight-kluster medföljer verktyget Distcp som kan användas för att kopiera data från olika källor till ett HDInsight-kluster. Om du har konfigurerat HDInsight-klustret för att använda Azure Blob Storage och lagring av Azure Data Lake att verktyget Distcp använda out-of-the-box att kopiera data mellan samt. I det här avsnittet ska titta vi på hur du använder verktyget Distcp.

1. Använd SSH för att ansluta till klustret från ditt skrivbord. Se [Anslut till ett Linux-baserat HDInsight-kluster](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Kör kommandon från SSH-prompten.

2. Kontrollera om du har åtkomst till Azure Storage BLOB (WASB). Kör följande kommando:

        hdfs dfs –ls wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    Resultatet bör ge en lista över innehållet i blob storage.

3. På liknande sätt kan kontrollera om du kan komma åt Data Lake-standardlagringskontot från klustret. Kör följande kommando:

        hdfs dfs -ls abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    Resultatet bör ge en lista över filer/mappar i Data Lake-standardlagringskontot.

4. Använd Distcp för att kopiera data från WASB till ett Data Lake Storage-konto.

        hadoop distcp wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    Kommandot kopierar innehållet på den **/exempel/data/gutenberg/** mapp i Blob storage till **/myfolder** i Data Lake-standardlagringskontot.

5. Använd Distcp på samma sätt för att kopiera data från Data Lake-standardlagringskontot Blob Storage (WASB).

        hadoop distcp abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    Kommandot kopierar innehållet på **/myfolder** Data Lake Store-konto som **/exempel/data/gutenberg/** mapp i WASB.

## <a name="performance-considerations-while-using-distcp"></a>Prestandaöverväganden när du använder DistCp

Eftersom Distcps lägsta Granularitet är en enskild fil, är ange det maximala antalet samtidiga kopior den viktigaste parametern att optimera mot lagring av Data Lake. Antal samtidiga kopior styrs genom att ange antalet mappers (**m**) parametern på kommandoraden. Den här parametern anger det maximala antalet mappers som används för att kopiera data. Standardvärdet är 20.

**Exempel**

    hadoop distcp wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Hur tar jag reda på antalet mappers ska användas?

Här är några riktlinjer som du kan använda.

* **Steg 1: Välj den totala YARN minnet** -det första steget är att avgöra YARN-minne i klustret där du kör jobbet DistCp. Den här informationen är tillgänglig i Ambari-portalen som associeras med klustret. Navigera till YARN och visar fliken konfigurationerna för att visa YARN-minne. För att få det totala minnet YARN, multiplicera YARN minne per nod med antalet noder som du har i klustret.

* **Steg 2: Beräkna antalet mappers** -värdet för **m** motsvarar kvoten av totalt YARN minne delat med storleken för YARN-behållare. YARN behållaren Storleksinformation finns i Ambari-portalen. Navigera till YARN och visar fliken konfigurationerna. YARN behållaren storleken visas i det här fönstret. Formeln att få fram antalet mappers (**m**) är

        m = (number of nodes * YARN memory for each node) / YARN container size

**Exempel**

Anta att du har en 4 D14v2s-noder i klustret och du försöker överföra 10 TB data från 10 olika mappar. Var och en av mapparna innehåller olika mängder data och filstorlekarna i mappen är olika.

* **Totalt YARN minne**: YARN-minne ligger 96 GB för en nod D14 från Ambari-portalen. Så är den totala YARN minnet för kluster med fyra noder: 

        YARN memory = 4 * 96GB = 384GB

* **Antal mappers**: från Ambari portal du bestämma att YARN behållare storleken är 3072 för D14 klusternoder. Det är antal mappers:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Om andra program använder minne, kan du välja att bara använda en del av ditt kluster YARN minne för DistCp.

### <a name="copying-large-datasets"></a>Kopiera stora datauppsättningar

När storleken på datamängden som ska flyttas är stor (till exempel > 1 TB) eller om du har många olika mappar bör du använda flera DistCp jobb. Det finns troligen inga prestandafördelar, men det sprids ut jobb så att om något jobb misslyckas, behöver du bara att starta om det specifika jobbet i stället för hela jobbet.

### <a name="limitations"></a>Begränsningar

* DistCp försöker skapa mappers med liknande storlek för att optimera prestanda. Öka antalet mappers kanske inte alltid att öka prestanda.

* DistCp är begränsad till endast en mappning per fil. Du bör därför inte ha flera mappers än vad du har filer. Eftersom DistCp kan bara tilldela en mappning till en fil, begränsar detta mängden samtidighet som kan användas för att kopiera stora filer.

* Om du har ett litet antal stora filer, bör du dela dem i 256 MB filsegment att ge mer potentiella samtidighet. 
