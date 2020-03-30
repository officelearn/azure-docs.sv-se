---
title: Kopiera data till Azure Data Lake Storage Gen2 med DistCp| Microsoft-dokument
description: Använda Verktyget DistCp för att kopiera data till och från DataSjölagring Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3c09a95309e001def306698bbba4f6d0a1a2804d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255540"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Använda DistCp för att kopiera data mellan Azure Storage Blobbar och Azure Data Lake Storage Gen2

Du kan använda [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) för att kopiera data mellan ett V2-lagringskonto för allmänt ändamål och ett V2-lagringskonto med hierarkiskt namnområde aktiverat. Den här artikeln innehåller instruktioner om hur du använder verktyget DistCp.

DistCp ger en mängd olika kommandoradsparametrar och vi rekommenderar starkt att du läser den här artikeln för att optimera din användning av den. Den här artikeln visar grundläggande funktioner samtidigt som du fokuserar på dess användning för att kopiera data till ett hierarkiskt namnområde aktiverat konto.

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett befintligt Azure Storage-konto utan Data Lake Storage Gen2-funktioner (hierarkiskt namnområde) aktiverat**.
* **Ett Azure Storage-konto med datasjölagringsgenm2-funktionen aktiverad**. Instruktioner om hur du skapar ett finns i [Skapa ett Azure Data Lake Storage Gen2-lagringskonto](data-lake-storage-quickstart-create-account.md)
* **Ett filsystem** som har skapats i lagringskontot med hierarkiskt namnområde aktiverat.
* **Azure HDInsight-kluster** med åtkomst till ett lagringskonto med Data Lake Storage Gen2 aktiverat. Se [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Se till att du aktiverar Fjärrskrivbord för klustret.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Använda DistCp från ett HDInsight Linux-kluster

Ett HDInsight-kluster levereras med distCp-verktyget, som kan användas för att kopiera data från olika källor till ett HDInsight-kluster. Om du har konfigurerat HDInsight-klustret för att använda Azure Blob Storage och Azure Data Lake Storage tillsammans, kan distCp-verktyget användas direkt för att kopiera data mellan. I det här avsnittet tittar vi på hur du använder distCp-verktyget.

1. Skapa en SSH-session i DITT HDI-kluster. Se [Anslut till ett Linux-baserat HDInsight-kluster](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Kontrollera om du kan komma åt ditt befintliga V2-konto för allmänt syfte (utan hierarkiskt namnområde aktiverat).

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    Utdata ska innehålla en lista över innehållet i behållaren.

3. Kontrollera på samma sätt om du kan komma åt lagringskontot med hierarkiskt namnområde aktiverat från klustret. Kör följande kommando:

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    Utdata bör innehålla en lista över filer/mappar i datasjölagringskontot.

4. Använd DistCp för att kopiera data från WASB till ett DataSjölagringskonto.

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    Kommandot kopierar innehållet i mappen **/example/data/gutenberg/** i Blob storage till **/myfolder** i datasjölagringskontot.

5. På samma sätt kan du använda DistCp för att kopiera data från datasjölagringskontot till Blob Storage (WASB).

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    Kommandot kopierar innehållet i **/myfolder** i datasjöarkivets konto till **mappen /example/data/gutenberg/** i WASB.

## <a name="performance-considerations-while-using-distcp"></a>Prestandaöverväganden när du använder DistCp

Eftersom DistCp:s lägsta granularitet är en enda fil är det viktigaste parametern för att optimera den mot DataSjölagring om du anger det maximala antalet samtidiga kopior. Antalet samtidiga kopior är lika med antalet mappers (**m**) parameter på kommandoraden. Den här parametern anger det maximala antalet mappers som används för att kopiera data. Standardvärdet är 20.

**Exempel**

    hadoop distcp -m 100 wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Hur avgör jag hur många mappers som ska användas?

Här är några riktlinjer som du kan använda.

* **Steg 1: Bestäm totalt minne som är tillgängligt för appkön "standard" YARN** - Det första steget är att bestämma det minne som är tillgängligt för appkön "standard". Den här informationen är tillgänglig i Ambari-portalen som är associerad med klustret. Navigera till YARN och visa fliken Configs för att se YARN-minnet som är tillgängligt för appkön "standard". Detta är det totala tillgängliga minnet för ditt DistCp-jobb (som faktiskt är ett MapReduce-jobb).

* **Steg 2: Beräkna antalet mappers** - Värdet **m** är lika med kvoten av totalt YARN-minne dividerat med YARN-behållarens storlek. YARN-containerstorleksinformationen finns även i Ambari-portalen. Navigera till YARN och visa fliken Configs. YARN-behållarstorleken visas i det här fönstret. Ekvationen för att komma fram till antalet mappers (**m**) är

        m = (number of nodes * YARN memory for each node) / YARN container size

**Exempel**

Anta att du har ett 4x D14v2s-kluster och du försöker överföra 10 TB data från 10 olika mappar. Var och en av mapparna innehåller varierande mängder data och filstorlekarna i varje mapp är olika.

* **Totalt GARN minne:** Från Ambari portalen du fastställa att YARN minnet är 96 GB för en D14-nod. Så totalt YARN-minne för fyra nodkluster är: 

        YARN memory = 4 * 96GB = 384GB

* **Antal mappers**: Från Ambari-portalen bestämmer du att YARN-behållarstorleken är 3 072 MB för en D14-klusternod. Så antalet mappers är:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Om andra program använder minne kan du välja att bara använda en del av klustrets YARN-minne för DistCp.

### <a name="copying-large-datasets"></a>Kopiera stora datauppsättningar

När storleken på den datauppsättning som ska flyttas är stor (till exempel >1 TB) eller om du har många olika mappar bör du överväga att använda flera DistCp-jobb. Det finns sannolikt ingen prestandavinst, men det sprider ut jobben så att om något jobb misslyckas behöver du bara starta om det specifika jobbet i stället för hela jobbet.

### <a name="limitations"></a>Begränsningar

* DistCp försöker skapa mappers som är lika stora för att optimera prestanda. Att öka antalet mappers kanske inte alltid ökar prestanda.

* DistCp är begränsat till endast en mapper per fil. Därför bör du inte ha fler mappers än du har filer. Eftersom DistCp bara kan tilldela en mapper till en fil, begränsar detta mängden samtidighet som kan användas för att kopiera stora filer.

* Om du har ett litet antal stora filer, så ska du dela upp dem i 256 MB filsegment för att ge dig mer potentiell samtidighet.
