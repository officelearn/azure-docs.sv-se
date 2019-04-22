---
title: Kopiera data till Azure Data Lake Storage Gen2 använda DistCp | Microsoft Docs
description: Använd DistCp för att kopiera data till och från Data Lake Storage Gen2
services: storage
author: seguler
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: seguler
ms.openlocfilehash: 3b58dc8dabc55ba428ce6e35091a6947e5f4a824
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678394"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Använd DistCp för att kopiera data mellan Azure Storage-Blobbar och Azure Data Lake Storage Gen2

Du kan använda [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) att kopiera data mellan ett lagringskonto för generell användning V2 och ett lagringskonto för generell användning V2 med hierarkiskt namnområde aktiverat. Den här artikeln innehåller anvisningar om hur du använder verktyget DistCp.

DistCp erbjuder en mängd olika kommandoradsparametrar och rekommenderar vi att starkt du kan läsa den här artikeln för att optimera din användning av den. Den här artikeln visar grundläggande funktioner samtidigt som fokuserar på dess användning för att kopiera data till ett hierarkiskt namnområde aktiverat konto.

## <a name="prerequisites"></a>Nödvändiga komponenter

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett befintligt Azure Storage-konto utan att Data Lake Storage Gen2 möjliggörs (hierarkiskt namnområde)**.
* **Ett Azure Storage-konto med Data Lake Storage Gen2 funktionen aktiverad**. Anvisningar för hur du skapar ett finns i [skapa ett lagringskonto i Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md)
* **Ett filsystem** som har skapats i lagringskontot med hierarkiskt namnområde aktiverat.
* **Azure HDInsight-kluster** med åtkomst till ett lagringskonto med Data Lake Storage Gen2 aktiverat. Se [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Kontrollera att du aktivera Fjärrskrivbord för klustret.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Använd DistCp från ett kluster i HDInsight Linux

Ett HDInsight-kluster levereras med verktyget DistCp som kan användas för att kopiera data från olika källor till ett HDInsight-kluster. Om du har konfigurerat HDInsight-klustret för att använda Azure Blob Storage och Azure Data Lake Storage, kan verktyget DistCp vara används out-of the box kopiera data mellan samt. I det här avsnittet ska titta vi på hur du använder verktyget DistCp.

1. Skapa en SSH-session i HDI-klustret. Se [ansluta till ett Linux-baserade HDInsight-kluster](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Kontrollera om du har åtkomst till din befintliga generell användning V2-konto (utan hierarkiskt namnområde aktiverat).

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    Utdata bör ge en lista över innehållet i behållaren.

3. På samma sätt kan kontrollera om du kan komma åt lagringskontot med hierarkiskt namnområde aktiverat från klustret. Kör följande kommando:

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    Utdata bör ge en lista över filer/mappar i Data Lake Storage-kontot.

4. Använd DistCp för att kopiera data från WASB till ett Data Lake Storage-konto.

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    Kommandot kopierar innehållet i den **/exempel/data/gutenberg/** i Blob storage till **/myfolder** i Data Lake Storage-kontot.

5. På samma sätt, Använd DistCp för att kopiera data från Data Lake Storage-konto till Blob Storage (WASB).

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    Kommandot kopierar innehållet i **/myfolder** i Data Lake Store-konto till **/exempel/data/gutenberg/** mapp i WASB.

## <a name="performance-considerations-while-using-distcp"></a>Prestandaöverväganden när du använder DistCp

Eftersom Distcps lägsta Granulariteten är en enskild fil, är ange det maximala antalet samtidiga kopior parametern är viktigast för att optimera den mot Data Lake Storage. Antal samtidiga kopior är lika med antalet Mappningskomponenter (**m**) parametern på kommandoraden. Den här parametern anger det maximala antalet Mappningskomponenter som används för att kopiera data. Standardvärdet är 20.

**Exempel**

    hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Hur vet jag hur många Mappningskomponenter att använda?

Här är några riktlinjer som du kan använda.

* **Steg 1: Fastställa totalt minne som är tillgängliga för ”standard” YARN app kön** – det första steget är att fastställa det tillgängliga minnet för ”standard” YARN app kön. Den här informationen är tillgänglig i Ambari-portalen som är associerade med klustret. Gå till YARN och visa fliken konfigurationer om du vill visa YARN-minne till ”standard” app kön. Det här är det totala mängden tillgängligt minnet för DistCp jobbet (vilket är egentligen ett MapReduce-jobb).

* **Steg 2: Beräkna antalet Mappningskomponenter** -värdet för **m** motsvarar kvoten av den totala YARN minnet dividerat med YARN-behållarens storlek. YARN-behållare Storleksinformation finns i Ambari-portalen. Gå till YARN och visa fliken konfigurationer. Behållarstorlek YARN visas i det här fönstret. Formeln ska tas emot på hur många Mappningskomponenter (**m**) är

        m = (number of nodes * YARN memory for each node) / YARN container size

**Exempel**

Anta att du har ett 4 x D14v2s-kluster och du försöker överföra 10 TB data från 10 olika mappar. Var och en av mapparna innehåller olika mängder data och filstorlekar i varje App är olika.

* **Totalt minne YARN**: Från portalen Ambari bestämma du att YARN-minne är 96 GB för en D14-nod. Det är totala YARN-minnet i kluster med fyra noder: 

        YARN memory = 4 * 96GB = 384GB

* **Antal Mappningskomponenter**: Från portalen Ambari bestämma du att YARN behållarens storlek är 3 072 MB för en D14-klusternod. Därför är antalet Mappningskomponenter:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Om andra program använder minne, kan du välja att bara använda en del av ditt kluster YARN minne för DistCp.

### <a name="copying-large-datasets"></a>Kopiering av stora datauppsättningar

När storleken på datauppsättningen som ska flyttas är stor (till exempel > 1 TB) eller om du har många olika mappar, bör du använda flera DistCp jobb. Det är förmodligen inga prestandaökning, men det sprids ut jobb så att om något jobb misslyckas, behöver du bara att starta om specifika jobbet i stället för hela projektet.

### <a name="limitations"></a>Begränsningar

* DistCp försöker skapa Mappningskomponenter med liknande storlekar för att optimera prestanda. Öka antalet Mappningskomponenter kanske inte alltid ökar prestanda.

* DistCp är begränsad till endast en mappning per fil. Du bör därför inte ha fler Mappningskomponenter än vad du har filer. Eftersom DistCp kan endast tilldela en mappning till en fil, begränsar detta mängden samtidighet som kan användas för att kopiera stora filer.

* Om du har ett litet antal stora filer, bör du dela dem till 256 MB filsegment att ge dig större potentiella samtidighet.
