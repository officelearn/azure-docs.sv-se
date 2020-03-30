---
title: Ladda upp data för Apache Hadoop-jobb i HDInsight
description: Lär dig hur du laddar upp och får åtkomst till data för Apache Hadoop-jobb i HDInsight med hjälp av Azure classic CLI, Azure Storage Explorer, Azure PowerShell, Hadoop-kommandoraden eller Sqoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdiseo17may2017
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 7eb1f7e1ce02a30f84cb520438f60fcbcfa3a965
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73100134"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Ladda upp data för Apache Hadoop-jobb i HDInsight

Azure HDInsight tillhandahåller ett fullfjädrat Hadoop-distribuerat filsystem (HDFS) över Azure Storage och Azure Data Lake Storage (Gen1 och Gen2). Azure Storage och Data Lake Storage Gen1 och Gen2 är utformade som HDFS-tillägg för att ge kunderna en sömlös upplevelse. De gör det möjligt för den fullständiga uppsättningen komponenter i Hadoop-ekosystemet att fungera direkt på de data som hanteras. Azure Storage, Data Lake Storage Gen1 och Gen2 är distinkta filsystem som är optimerade för lagring av data och beräkningar på dessa data. Information om fördelarna med att använda Azure Storage finns i [Använda Azure Storage med HDInsight](hdinsight-hadoop-use-blob-storage.md), Använd Data Lake Storage [Gen1 med HDInsight](hdinsight-hadoop-use-data-lake-store.md)och [Använd Data Lake Storage Gen2 med HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Krav

Observera följande krav innan du börjar:

* Ett Azure HDInsight-kluster. Instruktioner finns [i Komma igång med Azure HDInsight-](hadoop/apache-hadoop-linux-tutorial-get-started.md) eller Create [HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).
* Kunskap om följande artiklar:
    * [Använda Azure Storage med HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Använda DataSjölagring Gen1 med HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    * [Använda DataSjölagring Gen2 med HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Ladda upp data till Azure Storage

## <a name="utilities"></a>Verktyg

Microsoft tillhandahåller följande verktyg för att arbeta med Azure Storage:

| Verktyg | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure-portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzKopia](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Kommandot Hadoop](#commandline) |✔ |✔ |✔ |

> [!NOTE]  
> Kommandot Hadoop är endast tillgängligt i HDInsight-klustret. Kommandot tillåter endast inläsning av data från det lokala filsystemet i Azure Storage.  

## <a name="hadoop-command-line"></a><a id="commandline"></a>Kommandoraden Hadoop

Hadoop-kommandoraden är bara användbar för att lagra data i Azure storage-blob när data redan finns på klusterhuvudnoden.

För att kunna använda kommandot Hadoop måste du först ansluta till headnoden med [SSH eller PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

När du är ansluten kan du använda följande syntax för att ladda upp en fil till lagring.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Till exempel, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Eftersom standardfilsystemet för HDInsight finns i Azure Storage, /example/data/data.txt är faktiskt i Azure Storage. Du kan också referera till filen som:

    wasbs:///example/data/data.txt

eller

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

En lista över andra Hadoop-kommandon som fungerar med filer finns i[https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> På Apache HBase-kluster är standardblockstorleken som används när data skrivs 256 KB. Även om detta fungerar bra när du använder HBase API:er eller REST API:er, resulterar det i ett fel att använda `hadoop` kommandona eller `hdfs dfs` kommandon för att skriva data som är större än ~12 GB. Mer information finns i [lagringsundantaget för att skriva på blob-avsnittet](#storageexception) i den här artikeln.

## <a name="graphical-clients"></a>Grafiska klienter

Det finns också flera program som tillhandahåller ett grafiskt gränssnitt för att arbeta med Azure Storage. Följande tabell är en lista över några av dessa program:

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio-verktyg för HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Utforskaren för Azure Storage](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea (av cerulea)](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer (molnighet)](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer för Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Montera Azure Storage som lokal enhet

Se [Montera Azure Storage som lokal enhet](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Ladda upp med hjälp av tjänster

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory-tjänsten är en fullständigt hanterad tjänst för att skriva datalagring, databehandling och dataförflyttningstjänster till strömlinjeformade, skalbara och tillförlitliga dataproduktionspipelpipelsar.

|Lagringstyp|Dokumentation|
|----|----|
|Azure Blob Storage|[Kopiera data till eller från Azure Blob-lagring med hjälp av Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Kopiera data till eller från Azure Data Lake Storage Gen1 med hjälp av Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Läsa in data i Azure Data Lake Storage Gen2 med Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a><a id="sqoop"></a>Apache Sqoop

Sqoop är ett verktyg för att överföra data mellan Hadoop och relationsdatabaser. Du kan använda den för att importera data från ett relationsdatabashanteringssystem (RDBMS), till exempel SQL Server, MySQL eller Oracle till det Hadoop-distribuerade filsystemet (HDFS), omvandla data i Hadoop med MapReduce eller Hive och sedan exportera tillbaka data till ett RDBMS.You can use it to import data from a relational database management system (RDBMS), such as SQL Server, such, such,

Mer information finns i [Använda Sqoop med HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>Utveckling SDK

Azure Storage kan också nås med hjälp av en Azure SDK från följande programmeringsspråk:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Mer information om hur du installerar Azure SDK:er finns i [Azure-nedladdningar](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Felsökning

### <a name="storage-exception-for-write-on-blob"></a><a id="storageexception"></a>Lagringsundantag för skrivning på blob

**Symptom:** När `hadoop` du `hdfs dfs` använder kommandona eller för att skriva filer som är ~12 GB eller större i ett HBase-kluster kan följande fel uppstå:

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**Orsak:** HBase på HDInsight-kluster som standard är en blockstorlek på 256 KB när du skriver till Azure-lagring. Även om det fungerar för HBase API:er eller REST-API:er uppstår ett fel när du använder verktygen eller `hadoop` `hdfs dfs` kommandoradsverktygen.

**Upplösning**: `fs.azure.write.request.size` Används för att ange en större blockstorlek. Du kan göra detta per användning med `-D` hjälp av parametern. Följande kommando är ett exempel som `hadoop` använder den här parametern med kommandot:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Du kan också öka `fs.azure.write.request.size` värdet på globalt genom att använda Apache Ambari. Följande steg kan användas för att ändra värdet i webbgränssnittet i Ambari:

1. Gå till Ambari Web UI för ditt kluster i webbläsaren. Det `https://CLUSTERNAME.azurehdinsight.net`här `CLUSTERNAME` är , där är namnet på klustret.

    Ange administratörsnamnet och lösenordet för klustret när du uppmanas att göra det.
2. Välj **HDFS**till vänster på skärmen och välj sedan fliken **Configs.**
3. I fältet **Filter...** `fs.azure.write.request.size`anger du . Då visas fältet och det aktuella värdet i mitten av sidan.
4. Ändra värdet från 262144 (256 KB) till det nya värdet. Till exempel 4194304 (4 MB).

    ![Bild av att ändra värdet via Ambari Web UI](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Mer information om hur du använder Ambari finns i [Hantera HDInsight-kluster med hjälp av Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Nästa steg

Nu när du förstår hur du får data till HDInsight läser du följande artiklar för att lära dig hur du utför analyser:

* [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Skicka Apache Hadoop jobb programmässigt](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-pig.md)
