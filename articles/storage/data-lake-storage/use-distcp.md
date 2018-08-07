---
title: Kopiera data till Azure Data Lake Storage Gen2 förhandsversion använda Distcp | Microsoft Docs
description: Använd Distcp för att kopiera data till och från Data Lake Storage Gen2 förhandsversion
services: storage
author: seguler
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 06/27/2018
ms.author: seguler
ms.openlocfilehash: 065c4c4315bda209484cc1b2449980e55d4ac798
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522704"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-storage-gen2-preview"></a>Använd Distcp för att kopiera data mellan Azure Storage-Blobbar och Data Lake Storage Gen2 förhandsversion

Om du har ett HDInsight-kluster med åtkomst till Azure Data Lake Storage Gen2 förhandsversion kan du använda Hadoop-ekosystemet verktyg som [Distcp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) att kopiera data **till och från** ett HDInsight-kluster storage (WASB) i en Data Lake Storage Gen2 kompatibla konto. Den här artikeln innehåller anvisningar om hur du använder verktyget Distcp.

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Storage-konto med Azure Data Lake Storage (förhandsgranskning)-funktionen aktiverad**. Anvisningar för hur du skapar ett finns i [skapa ett lagringskonto i Azure Data Lake Storage Gen2 förhandsversion](quickstart-create-account.md)
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Storage-konto. Se [Använd Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](use-hdi-cluster.md). Kontrollera att du aktivera Fjärrskrivbord för klustret.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Använd Distcp från ett kluster i HDInsight Linux

Ett HDInsight-kluster levereras med verktyget Distcp som kan användas för att kopiera data från olika källor till ett HDInsight-kluster. Om du har konfigurerat HDInsight-klustret för att använda Azure Blob Storage och Azure Data Lake Storage, kan verktyget Distcp vara används out-of the box kopiera data mellan samt. I det här avsnittet ska titta vi på hur du använder verktyget Distcp.

1. Anslut till klustret via SSH från ditt skrivbord. Se [ansluta till ett Linux-baserade HDInsight-kluster](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Kör kommandon från SSH-prompten.

2. Kontrollera om du har åtkomst till Azure Storage BLOB-objekt (WASB). Kör följande kommando:

        hdfs dfs –ls wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    Utdata bör ge en lista över innehållet i blob storage.

3. På samma sätt kan kontrollera om du kan komma åt Data Lake Storage-konto från klustret. Kör följande kommando:

        hdfs dfs -ls abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    Utdata bör ge en lista över filer/mappar i Data Lake Storage-kontot.

4. Använd Distcp för att kopiera data från WASB till ett Data Lake Storage-konto.

        hadoop distcp wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    Kommandot kopierar innehållet i den **/exempel/data/gutenberg/** i Blob storage till **/myfolder** i Data Lake Storage-kontot.

5. På samma sätt, Använd Distcp för att kopiera data från Data Lake Storage-konto till Blob Storage (WASB).

        hadoop distcp abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    Kommandot kopierar innehållet i **/myfolder** i Data Lake Store-konto till **/exempel/data/gutenberg/** mapp i WASB.

## <a name="performance-considerations-while-using-distcp"></a>Prestandaöverväganden när du använder DistCp

Eftersom Distcps lägsta Granulariteten är en enskild fil, är ange det maximala antalet samtidiga kopior parametern är viktigast för att optimera den mot Data Lake Storage. Antal samtidiga kopior styrs genom att ange antalet Mappningskomponenter (**m**) parametern på kommandoraden. Den här parametern anger det maximala antalet Mappningskomponenter som används för att kopiera data. Standardvärdet är 20.

**Exempel**

    hadoop distcp wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Hur vet jag hur många Mappningskomponenter att använda?

Här är några riktlinjer som du kan använda.

* **Steg 1: Fastställ minne totalt YARN** – det första steget är att avgöra YARN-minne i klustret där du kör jobbet DistCp. Den här informationen är tillgänglig i Ambari-portalen som är associerade med klustret. Gå till YARN och visa fliken konfigurationer om du vill visa YARN-minne. Multiplicera YARN minne per nod med antalet noder som finns i klustret för att få den totala mängden YARN-minnet.

* **Steg 2: Beräkna antalet Mappningskomponenter** -värdet för **m** motsvarar kvoten av den totala YARN minnet dividerat med YARN-behållarens storlek. YARN-behållare Storleksinformation finns i Ambari-portalen. Gå till YARN och visa fliken konfigurationer. Behållarstorlek YARN visas i det här fönstret. Formeln ska tas emot på hur många Mappningskomponenter (**m**) är

        m = (number of nodes * YARN memory for each node) / YARN container size

**Exempel**

Anta att du har en 4 D14v2s-noder i klustret och du försöker överföra 10 TB data från 10 olika mappar. Var och en av mapparna innehåller olika mängder data och filstorlekar i varje App är olika.

* **Totalt minne YARN**: från Ambari portal YARN-minne ligger 96 GB för en D14-nod. Det är totala YARN-minnet i kluster med fyra noder: 

        YARN memory = 4 * 96GB = 384GB

* **Antal Mappningskomponenter**: YARN behållarens storlek ligger 3072 för klusternoder D14 från Ambari-portalen. Därför är antalet Mappningskomponenter:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Om andra program använder minne, kan du välja att bara använda en del av ditt kluster YARN minne för DistCp.

### <a name="copying-large-datasets"></a>Kopiering av stora datauppsättningar

När storleken på datauppsättningen som ska flyttas är stor (till exempel > 1 TB) eller om du har många olika mappar, bör du använda flera DistCp jobb. Det är förmodligen inga prestandaökning, men det sprids ut jobb så att om något jobb misslyckas, behöver du bara att starta om specifika jobbet i stället för hela projektet.

### <a name="limitations"></a>Begränsningar

* DistCp försöker skapa Mappningskomponenter med liknande storlekar för att optimera prestanda. Öka antalet Mappningskomponenter kanske inte alltid ökar prestanda.

* DistCp är begränsad till endast en mappning per fil. Du bör därför inte ha fler Mappningskomponenter än vad du har filer. Eftersom DistCp kan endast tilldela en mappning till en fil, begränsar detta mängden samtidighet som kan användas för att kopiera stora filer.

* Om du har ett litet antal stora filer, bör du dela dem till 256 MB filsegment att ge dig större potentiella samtidighet. 
