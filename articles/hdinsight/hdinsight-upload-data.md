---
title: "Överföra data för Hadoop-jobb i HDInsight | Microsoft Docs"
description: "Lär dig mer om att överföra och komma åt data för Hadoop-jobb i HDInsight med hjälp av Azure CLI, Azure Lagringsutforskaren, Azure PowerShell, kommandorad för Hadoop eller Sqoop."
keywords: "etl-hadoop, hämta data till hadoop, Läs in data för hadoop"
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 56b913ee-0f9a-4e9f-9eaf-c571f8603dd6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: jgao
ms.openlocfilehash: 9cdf41bd5c15abca3570a08584dc0958497848a7
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>Ladda upp data för Hadoop-jobb i HDInsight

Azure HDInsight ger en komplett Hadoop distributed file system (HDFS) över Azure Storage och Azure Data Lake Store. Azure Storage och Data lake Store är utformade som ett HDFS-tillägg att förse kunder en sömlös upplevelse. De ger en fullständig uppsättning komponenter i Hadoop-ekosystemet att arbeta direkt med de data som den hanterar. Azure Storage och Data Lake Store är distinkt filsystem som är optimerade för lagring av data och beräkningar på dessa data. Information om fördelarna med att använda Azure Storage finns [använda Azure Storage med HDInsight] [ hdinsight-storage] och [Använd Data Lake Store med HDInsight](hdinsight-hadoop-use-data-lake-store.md).

## <a name="prerequisites"></a>Krav

Observera följande krav innan du börjar:

* Ett Azure HDInsight-kluster. Instruktioner finns i [komma igång med Azure HDInsight] [ hdinsight-get-started] eller [skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).
* Informationen i följande två artiklar:

    - [Använda Azure Storage med HDInsight][hdinsight-storage]
    - [Använd Data Lake Store med HDInsight](hdinsight-hadoop-use-data-lake-store.md)

## <a name="upload-data-to-azure-storage"></a>Ladda upp data till Azure Storage

### <a name="command-line-utilities"></a>Kommandoradsverktyg
Microsoft tillhandahåller följande verktyg för att arbeta med Azure Storage:

| Verktyget | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure-kommandoradsgränssnittet][azurecli] |✔ |✔ |✔ |
| [Azure PowerShell][azure-powershell] | | |✔ |
| [AzCopy][azure-azcopy] |✔ | |✔ |
| [Hadoop-kommando](#commandline) |✔ |✔ |✔ |

> [!NOTE]
> Azure CLI, Azure PowerShell och AzCopy kan alla användas från utanför Azure, är Hadoop-kommandot bara tillgängligt på HDInsight-klustret. Och kommandot kan bara läsa in data från det lokala filsystemet i Azure Storage.
>
>

#### <a id="xplatcli"></a>Azure CLI
Azure CLI är ett plattformsoberoende verktyg som hjälper dig att hantera Azure-tjänster. Använd följande steg för att överföra data till Azure Storage:

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Installera och konfigurera Azure CLI för Mac, Linux och Windows](../cli-install-nodejs.md).
2. Öppna en kommandotolk, bash eller andra gränssnitt och Använd följande för att autentisera till Azure-prenumeration.

    ```cli
    azure login
    ```

    När du uppmanas, anger du användarnamn och lösenord för din prenumeration.
3. Ange följande kommando för att visa en lista med lagringskonton för din prenumeration:

    ```cli
    azure storage account list
    ```

4. Välj lagringskonto som innehåller blob som du vill arbeta med och sedan använder du följande kommando för att hämta nyckeln för det här kontot:

    ```cli
    azure storage account keys list <storage-account-name>
    ```

    Det här kommandot returnerar den **primära** och **sekundära** nycklar. Kopiera den **primära** nyckelvärdet eftersom den används i nästa steg.
5. Använd följande kommando för att hämta en lista över blobbbehållare i storage-konto:

    ```cli
    azure storage container list -a <storage-account-name> -k <primary-key>
    ```

6. Ladda upp och laddar ned filer till blob med hjälp av följande kommandon:

   * Att överföra en fil:

        ```cli
        azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>
        ```

   * Att hämta en fil:

        ```cli
        azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>
        ```
    
> [!NOTE]
> Om du arbetar alltid med samma lagringskonto kan du ställa in följande miljövariabler i stället för att ange konto och nyckel för varje kommando:
>
> * **AZURE\_lagring\_konto**: lagringskontonamnet
> * **AZURE\_lagring\_åtkomst\_NYCKELN**: lagringskontots åtkomstnyckel
>
>

#### <a id="powershell"></a>Azure PowerShell
Azure PowerShell är en skriptmiljö som du kan använda för att styra och automatisera distributionen och hanteringen av dina arbetsbelastningar i Azure. Information om hur du konfigurerar din arbetsstation för att köra Azure PowerShell finns i [installera och konfigurera Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Att överföra en lokal fil till Azure Storage**

1. Öppna Azure PowerShell-konsol som finns beskrivet i [installera och konfigurera Azure PowerShell](/powershell/azure/overview).
2. Ange värden för de första fem variablerna i skriptet med följande:

    ```powershell
    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<StorageAccountName>"
    $containerName = "<ContainerName>"

    $fileName ="<LocalFileName>"
    $blobName = "<BlobName>"

    # Get the storage account key
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    # Create the storage context object
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

    # Copy the file from local workstation to the Blob container
    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
    ```

3. Klistra in skriptet i Azure PowerShell-konsolen för att köra den att kopiera filen.

Till exempel PowerShell-skript som skapats för att arbeta med HDInsight, se [HDInsight tools](https://github.com/blackmist/hdinsight-tools).

#### <a id="azcopy"></a>AzCopy
AzCopy är ett kommandoradsverktyg som förenklar uppgiften att överföra data till och från ett Azure Storage-konto. Du kan använda den som ett fristående verktyg eller innehålla verktyget i ett befintligt program. [Hämta AzCopy][azure-azcopy-download].

AzCopy-syntaxen är:

```command
AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]
```

Mer information finns i [AzCopy - överföring/hämta filer för Azure-BLOB][azure-azcopy].

Azcopy på Linux preview är tillgänglig.  Se [om AzCopy på Linux Preview](https://blogs.msdn.microsoft.com/windowsazurestorage/2017/05/16/announcing-azcopy-on-linux-preview/).

#### <a id="commandline"></a>Hadoop-kommandorad
Hadoop-kommandoraden är bara användbara för att lagra data i Azure storage blob när data finns redan på klustrets huvudnod.

För att kunna använda kommandot Hadoop, måste du först ansluta till headnode med någon av följande metoder:

* **Windows-baserade HDInsight**: [ansluta med hjälp av fjärrskrivbord](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* **Linux-baserat HDInsight**: ansluta med [SSH eller PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

När du är ansluten, kan du använda följande syntax för att överföra en fil till lagring.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

Till exempel, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Eftersom filsystemet för HDInsight i Azure Storage är /example/data.txt i Azure Storage. Du kan också gå till filen som:

    wasb:///example/data/data.txt

eller

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

En lista med andra Hadoop-kommandon som fungerar med filer, finns [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]
> I HBase-kluster standard blockstorlek som används när data skrivs är 256 KB. När det här fungerar bra när du använder HBase APIs eller REST API: er med hjälp av den `hadoop` eller `hdfs dfs` kommandon för att skriva data som är större än ~ 12 GB resulterar i ett fel. Mer information finns i [skrivning till blob storage-undantag](#storageexception) i den här artikeln.
>
>

### <a name="graphical-clients"></a>Grafisk klienter
Det finns också flera program som ger ett grafiskt gränssnitt för att arbeta med Azure Storage. Följande tabell är en lista över några av dessa program:

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio-verktygen för HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](http://storageexplorer.com/) |✔ |✔ |✔ |
| [Molnet lagring Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

#### <a name="visual-studio-tools-for-hdinsight"></a>Visual Studio-verktygen för HDInsight
Mer information finns i [navigera de länkade resurserna](hadoop/apache-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources).

#### <a id="storageexplorer"></a>Azure Lagringsutforskaren
*Azure Lagringsutforskaren* är användbart för att kontrollera och ändra data i BLOB. Det är ett kostnadsfritt, Öppna källa verktyg som kan hämtas från [http://storageexplorer.com/](http://storageexplorer.com/). Källkoden är tillgänglig från den här länken samt.

Innan du använder verktyget, måste du känna till din Azure storage-konto och nyckel. Anvisningar om hur du får den här informationen finns i ”så här: visa, kopiera och generera lagring åtkomstnycklar” avsnitt i [skapa, hantera eller ta bort ett lagringskonto][azure-create-storage-account].

1. Kör Azure Lagringsutforskaren. Om det är första gången du har kört Lagringsutforskaren, uppmanas du i **_Storage kontonamn** och **lagringskontonyckel**. Om du har kört den innan du använder den **Lägg till** för att lägga till ett nytt lagringskontonamn och nyckel.

    Ange namnet och nyckeln för storage-konto som används av ditt HDInsight-kluster och välj sedan **Spara & Öppna**.

    ![HDI. AzureStorageExplorer][image-azure-storage-explorer]
2. I listan över behållare till vänster om gränssnittet, klickar du på namnet på behållaren som är kopplad till ditt HDInsight-kluster. Den är namnet på HDInsight-klustret som standard men kan skilja sig om du har angett ett specifikt namn när du skapar klustret.
3. I verktygsfältet, väljer du ikonen överföringen.

    ![Verktygsfält med överför ikonen markerat](./media/hdinsight-upload-data/toolbar.png)
4. Ange en fil för att ladda upp och klicka sedan på **öppna**. När du uppmanas, Välj **överför** att överföra filen till roten på lagringsbehållaren. Om du vill överföra filen till en specifik sökväg anger du sökvägen i den **mål** fältet och välj sedan **överför**.

    ![Överför fildialogruta](./media/hdinsight-upload-data/fileupload.png)

    När filen har överförts, kan du använda den från jobb i HDInsight-klustret.

### <a name="mount-azure-storage-as-local-drive"></a>Montera Azure Storage som lokal enhet
Se [montera Azure Storage som lokala enhet](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

### <a name="upload-using-services"></a>Ladda upp med hjälp av tjänster
#### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory-tjänsten är en helt hanterad tjänst för att skapa lagring, databehandling och data movement datatjänster till produktion pipeline-effektiviserad, skalbara och tillförlitliga data.

Azure Data Factory kan användas för att flytta data till Azure Storage eller för att skapa data pipelines som använder HDInsight-funktioner, till exempel Hive och Pig direkt.

Mer information finns i [dokumentation för Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

#### <a id="sqoop"></a>Apache Sqoop
Sqoop är ett verktyg som utformats för att överföra data mellan Hadoop och relationsdatabaser. Du kan använda den för att importera data från ett relationella databashanteringssystem (RDBMS), exempelvis SQL Server, MySQL eller Oracle i Hadoop distributed file system (HDFS) Transformera data i Hadoop med MapReduce eller Hive och exportera data till en RDBMS.

Mer information finns i [använda Sqoop med HDInsight][hdinsight-use-sqoop].

### <a name="development-sdks"></a>SDK: er för utveckling
Azure-lagring kan även nås med ett Azure SDK från följande programmeringsspråk:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Mer information om hur du installerar Azure SDK: erna finns [hämtar Azure](https://azure.microsoft.com/downloads/)

### <a name="troubleshooting"></a>Felsökning
#### <a id="storageexception"></a>Skrivning till blob Storage-undantag
**Symptom**: när du använder den `hadoop` eller `hdfs dfs` kommandon för att skriva filer som är ~ 12 GB eller större på ett HBase-kluster kan du stöta på följande fel:

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

**Orsak**: HBase på HDInsight-kluster standard till en blockstorlek på 256 KB när du skriver till Azure-lagring. När den fungerar för HBase APIs eller REST API: er den resulterar i ett fel när du använder den `hadoop` eller `hdfs dfs` kommandoradsverktyg.

**Lösning**: Använd `fs.azure.write.request.size` att ange en större blockstorlek. Du kan göra detta på grundval av per användning med hjälp av den `-D` parameter. Följande kommando är ett exempel som använder den här parametern med det `hadoop` kommando:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Du kan också öka värdet för `fs.azure.write.request.size` globalt med Ambari. Följande steg kan användas för att ändra värdet i Ambari-Webbgränssnittet:

1. Gå till Ambari-Webbgränssnittet för klustret i din webbläsare. Detta är https://CLUSTERNAME.azurehdinsight.net, där **KLUSTERNAMN** är namnet på klustret.

    När du uppmanas, anger du admin namn och lösenord för klustret.
2. Vänster sida av skärmen, Välj **HDFS**, och välj sedan den **konfigurationerna** fliken.
3. I den **Filter...**  anger `fs.azure.write.request.size`. Då visas i fältet och det aktuella värdet i mitten på sidan.
4. Ändra värdet från 262144 (256KB) till det nya värdet. Till exempel 4194304 (4MB).

![Bild av ändra värdet via Ambari-Webbgränssnittet](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Läs mer om hur du använder Ambari [hantera HDInsight-kluster med Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Nästa steg
Nu när du vet hur du hämta data till HDInsight kan du läsa följande artiklar om du vill lära dig att utföra analyser av:

* [Kom igång med Azure HDInsight][hdinsight-get-started]
* [Skicka Hadoop-jobb via programmering][hdinsight-submit-jobs]
* [Använda Hive med HDInsight][hdinsight-use-hive]
* [Använda Pig med HDInsight][hdinsight-use-pig]

[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]:../storage/common/storage-create-storage-account.md
[azure-azcopy-download]:../storage/common/storage-use-azcopy.md
[azure-azcopy]:../storage/common/storage-use-azcopy.md

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[azurecli]: ../cli-install-nodejs.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
