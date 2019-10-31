---
title: Ladda upp data för Apache Hadoop jobb i HDInsight
description: 'Lär dig att överföra och komma åt data för Apache Hadoop jobb i HDInsight med hjälp av den klassiska Azure-CLI: t, Azure Storage Explorer, Azure PowerShell, Hadoop-kommandoraden eller Sqoop.'
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdiseo17may2017
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 7eb1f7e1ce02a30f84cb520438f60fcbcfa3a965
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100134"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Ladda upp data för Apache Hadoop jobb i HDInsight

Azure HDInsight tillhandahåller ett fullständigt aktuellt Hadoop Distributed File System (HDFS) över Azure Storage och Azure Data Lake Storage (gen1 och Gen2). Azure Storage och Data Lake Storage Gen1 och Gen2 har utformats som HDFS-tillägg för att ge en sömlös upplevelse för kunderna. De gör det möjligt för den fullständiga uppsättningen komponenter i Hadoop-eko systemet att arbeta direkt med de data som hanteras. Azure Storage, Data Lake Storage Gen1 och Gen2 är distinkta fil system som är optimerade för lagring av data och beräkningar av dessa data. Information om fördelarna med att använda Azure Storage finns i [använda Azure Storage med HDInsight](hdinsight-hadoop-use-blob-storage.md), [använda data Lake Storage gen1 med hdinsight](hdinsight-hadoop-use-data-lake-store.md)och [använda data Lake Storage Gen2 med HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Krav

Observera följande krav innan du börjar:

* Ett Azure HDInsight-kluster. Instruktioner finns i [komma igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) eller [skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).
* Kunskap om följande artiklar:
    * [Använda Azure Storage med HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Använda Data Lake Storage Gen1 med HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    * [Använda Data Lake Storage Gen2 med HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Ladda upp data till Azure Storage

## <a name="utilities"></a>Samhällsservice

Microsoft tillhandahåller följande verktyg för att arbeta med Azure Storage:

| Verktyg | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure-portalen](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop-kommando](#commandline) |✔ |✔ |✔ |

> [!NOTE]  
> Hadoop-kommandot är bara tillgängligt i HDInsight-klustret. Kommandot tillåter endast inläsning av data från det lokala fil systemet i Azure Storage.  

## <a id="commandline"></a>Hadoop-kommandorad

Hadoop-kommandoraden är bara användbar för att lagra data i Azure Storage BLOB när data redan finns på klustrets huvud nod.

Du måste först ansluta till huvudnoden med [SSH eller SparaTillFil](hdinsight-hadoop-linux-use-ssh-unix.md)för att kunna använda Hadoop-kommandot.

När du har anslutit kan du använda följande syntax för att ladda upp en fil till lagringen.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Till exempel, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Eftersom standard fil systemet för HDInsight är i Azure Storage är/example/data/data.txt faktiskt i Azure Storage. Du kan också referera till filen som:

    wasbs:///example/data/data.txt

eller

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

En lista över andra Hadoop-kommandon som fungerar med filer finns i [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> I Apache HBase-kluster är standard block storleken som används vid skrivning av data 256 KB. Även om det fungerar bra när du använder HBase-API: er eller REST-API: er med hjälp av `hadoop`-eller `hdfs dfs` kommandon för att skriva data som är större än ~ 12 GB resulterar i ett fel Mer information finns i avsnittet [lagrings undantag för skrivning i BLOB](#storageexception) i den här artikeln.

## <a name="graphical-clients"></a>Grafiska klienter

Det finns också flera program som tillhandahåller ett grafiskt gränssnitt för att arbeta med Azure Storage. Följande tabell är en lista över några av dessa program:

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools för HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer för Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Montera Azure Storage som lokal enhet

Se [montera Azure Storage som lokal enhet](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Ladda upp med tjänster

### <a name="azure-data-factory"></a>Azure Data Factory

Tjänsten Azure Data Factory är en fullständigt hanterad tjänst för att skapa data lagring, data behandling och tjänster för data förflyttning i strömlinjeformade, skalbara och tillförlitliga data produktions pipeliner.

|Lagringstyp|Dokumentation|
|----|----|
|Azure Blob Storage|[Kopiera data till eller från Azure Blob Storage med hjälp av Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Kopiera data till eller från Azure Data Lake Storage Gen1 med Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Läs in data i Azure Data Lake Storage Gen2 med Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a id="sqoop"></a>Apache Sqoop

Sqoop är ett verktyg som är utformat för att överföra data mellan Hadoop och Relations databaser. Du kan använda den för att importera data från ett relationellt databas hanterings system (RDBMS), till exempel SQL Server, MySQL eller Oracle i Hadoop Distributed File System (HDFS), transformera data i Hadoop med MapReduce eller Hive och sedan exportera data tillbaka till en RDBMS.

Mer information finns i [använda Sqoop med HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>Utvecklings-SDK: er

Azure Storage kan också nås med hjälp av en Azure SDK från följande programmeringsspråk:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Mer information om hur du installerar Azure SDK: er finns i [Azure downloads](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Felsöka

### <a id="storageexception"></a>Lagrings undantag för skrivning i BLOB

**Symptom**: när du använder `hadoop`-eller `hdfs dfs`-kommandon för att skriva filer som är ~ 12 GB eller större i ett HBase-kluster kan du stöta på följande fel:

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

**Orsak**: HBase på HDInsight-kluster standardvärdet är en block storlek på 256 KB vid skrivning till Azure Storage. Även om det fungerar för HBase-API: er eller REST-API: er, resulterar det i ett fel när du använder `hadoop` eller `hdfs dfs` kommando rads verktyg.

**Lösning**: Använd `fs.azure.write.request.size` om du vill ange en större block storlek. Du kan göra detta på varje användnings basis genom att använda `-D`-parametern. Följande kommando är ett exempel som använder den här parametern med kommandot `hadoop`:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Du kan också öka värdet för `fs.azure.write.request.size` globalt genom att använda Apache Ambari. Följande steg kan användas för att ändra värdet i Ambari-webbgränssnittet:

1. I webbläsaren går du till Ambari-webbgränssnittet för klustret. Detta är `https://CLUSTERNAME.azurehdinsight.net`, där `CLUSTERNAME` är namnet på klustret.

    När du uppmanas till det anger du administratörens namn och lösen ord för klustret.
2. Välj **HDFS**på vänster sida av skärmen och välj sedan fliken **konfigurationer** .
3. I fältet **filter...** anger du `fs.azure.write.request.size`. Detta visar fältet och det aktuella värdet mitt på sidan.
4. Ändra värdet från 262144 (256 KB) till det nya värdet. Till exempel 4194304 (4 MB).

    ![Bild av att ändra värdet via Ambari-webbgränssnittet](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Mer information om hur du använder Ambari finns i [Hantera HDInsight-kluster med hjälp av Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Nästa steg

Nu när du förstår hur du hämtar data till HDInsight kan du läsa följande artiklar för att lära dig hur du utför analyser:

* [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Skicka Apache Hadoop jobb program mässigt](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda Apache gris med HDInsight](hadoop/hdinsight-use-pig.md)
