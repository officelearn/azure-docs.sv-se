---
title: Överföra data för Apache Hadoop-jobb i HDInsight
description: Lär dig hur du laddar upp och komma åt data för Apache Hadoop-jobb i HDInsight med hjälp av den klassiska Azure CLI, Azure Storage Explorer, Azure PowerShell, Hadoop-kommandoraden eller Sqoop.
keywords: etl-hadoop, hämta data till hadoop, hadoop Läs in data
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/08/2019
ms.openlocfilehash: 820bb22fee75e5c1159ba90f23b478339f6345b5
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201933"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Överföra data för Apache Hadoop-jobb i HDInsight

Azure HDInsight ger en fullständig Hadoop distributed file system (HDFS) över Azure Storage och Azure Data Lake Storage (Gen1 och Gen2). Azure Storage och Data Lake Storage Gen1 och Gen2 är utformade som HDFS-tillägg för att ge kunderna en sömlös upplevelse. De gör en fullständig uppsättning komponenter i Hadoop-ekosystemet att arbeta direkt på de data som den hanterar. Azure Storage, Data Lake Storage Gen1 och Gen2 är distinkta filsystem som är optimerade för lagring av data och beräkningar på dessa data. Information om fördelarna med Azure Storage finns i [använda Azure Storage med HDInsight][hdinsight-storage], [Använd Data Lake Storage Gen1 med HDInsight](hdinsight-hadoop-use-data-lake-store.md), och [ Använda Data Lake Storage Gen2 med HDInsight](../storage/blobs/data-lake-storage-use-hdi-cluster.md).

## <a name="prerequisites"></a>Förutsättningar

Observera följande krav innan du börjar:

* Ett Azure HDInsight-kluster. Anvisningar finns i [Kom igång med Azure HDInsight] [ hdinsight-get-started] eller [skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).
* Kunskap om följande artiklar:

    - [Använda Azure Storage med HDInsight][hdinsight-storage]
    - [Använda Data Lake Storage Gen1 med HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    - [Använda Data Lake Storage Gen2 med HDInsight](../storage/blobs/data-lake-storage-use-hdi-cluster.md)  

## <a name="upload-data-to-azure-storage"></a>Ladda upp data till Azure Storage

## <a name="utilities"></a>Samhällsservice
Microsoft tillhandahåller följande verktyg att arbeta med Azure Storage:

| Verktyg | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop-kommando](#commandline) |✔ |✔ |✔ |


> [!NOTE]  
> Hadoop-kommandot är endast tillgängligt i HDInsight-klustret. Kommandot tillåter endast att läsa in data från det lokala filsystemet i Azure Storage.  


## <a id="commandline"></a>Hadoop-kommandoraden
Hadoop-kommandoraden är bara användbara för att lagra data i Azure storage-blob när data finns redan på klustrets huvudnod.

För att kunna använda kommandot Hadoop, måste du först ansluta till huvudnoden med [SSH eller PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

När du är ansluten, kan du använda följande syntax för att överföra en fil till lagring.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

Till exempel, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Eftersom standardfilsystemet för HDInsight finns i Azure Storage, som /example/data.txt faktiskt finns i Azure Storage. Du kan också referera till filen som:

    wasb:///example/data/data.txt

eller

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

En lista över andra Hadoop-kommandon som fungerar med filer som finns i [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> På Apache HBase-kluster kan standard blockstorlek som används vid skrivning av data är 256 KB. Även om detta fungerar bra när du använder HBase APIs eller REST API: er med hjälp av den `hadoop` eller `hdfs dfs` kommandon för att skriva data som är större än ~ 12 GB resulterar i ett fel. Mer information finns i den [för skrivning till blob storage-undantag](#storageexception) i den här artikeln.

## <a name="graphical-clients"></a>Grafiska klienter
Det finns också flera program som ger ett grafiskt gränssnitt för att arbeta med Azure Storage. I följande tabell är en lista över några av dessa program:

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio-verktyg för HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer för Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |


## <a name="mount-azure-storage-as-local-drive"></a>Montera Azure Storage som lokal enhet
Se [montera Azure Storage som lokala enhet](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Överför med hjälp av tjänster
### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory-tjänsten är en fullständigt hanterad tjänst för att skapa lagring och bearbetning av data movement datatjänster i effektiva, skalbara och tillförlitliga produktion datapipelines.

|Lagringstyp|Dokumentation|
|----|----|
|Azure Blob Storage|[Kopiera data till och från Azure Blob storage med hjälp av Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Kopiera data till och från Azure Data Lake Storage Gen1 med hjälp av Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Läs in data till Azure Data Lake Storage Gen2 med Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a id="sqoop"></a>Apache Sqoop
Sqoop är ett verktyg som utformats för att överföra data mellan Hadoop och relationsdatabaser. Du kan använda den för att importera data från ett hanteringssystem för relationsdatabaser (RDBMS), som SQL Server, MySQL eller Oracle i Hadoop distributed file system (HDFS), transformera data i Hadoop MapReduce eller Hive och sedan exportera data tillbaka till en RDBMS.

Mer information finns i [Använd Sqoop med HDInsight][hdinsight-use-sqoop].

### <a name="development-sdks"></a>SDK: er för utveckling
Azure Storage kan även nås med hjälp av ett Azure SDK från följande programmeringsspråk:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Mer information om hur du installerar Azure SDK: er finns i [nedladdningsbara filer för Azure](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Felsökning
### <a id="storageexception"></a>Skrivning till blob Storage-undantag
**Symptom**: När du använder den `hadoop` eller `hdfs dfs` kommandon för att skriva filer som är ~ 12 GB eller större på ett HBase-kluster kan följande fel uppstå:

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

**Orsak**: HBase på HDInsight-kluster standard till en blockstorlek på 256 KB när du skriver till Azure storage. När den fungerar för HBase APIs eller REST API: er, det resulterar i ett fel när du använder den `hadoop` eller `hdfs dfs` kommandoradsverktyg.

**Upplösning**: Använd `fs.azure.write.request.size` att ange en större blockstorlek. Du kan göra detta på basis av per användning med hjälp av den `-D` parametern. Följande kommando är ett exempel som använder den här parametern med det `hadoop` kommando:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Du kan också öka värdet för `fs.azure.write.request.size` globalt med hjälp av Apache Ambari. Följande steg kan användas för att ändra värdet i Ambari-Webbgränssnittet:

1. Gå till Ambari-Webbgränssnittet för ditt kluster i din webbläsare. Det här är https://CLUSTERNAME.azurehdinsight.net, där **CLUSTERNAME** är namnet på klustret.

    När du uppmanas, anger du namn på serveradministratör och lösenordet för klustret.
2. Till vänster på skärmen Välj **HDFS**, och välj sedan den **Peeringkonfigurationer** fliken.
3. I den **Filter...**  anger `fs.azure.write.request.size`. Då visas i fältet och det aktuella värdet i mitten på sidan.
4. Ändra värdet från 262144 (256 KB) till det nya värdet. Till exempel 4194304 (4 MB).

![Bild av ändra värdet via Ambari-Webbgränssnittet](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Läs mer om hur du använder Ambari [hantera HDInsight-kluster med hjälp av Apache Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Nästa steg
Nu när du vet hur du hämtar data till HDInsight kan du läsa följande artiklar för att lära dig att utföra analyser av:

* [Kom igång med Azure HDInsight][hdinsight-get-started]
* [Skicka Apache Hadoop-jobb programmässigt][hdinsight-submit-jobs]
* [Använda Apache Hive med HDInsight][hdinsight-use-hive]
* [Använda Apache Hive med HDInsight][hdinsight-use-pig]

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
