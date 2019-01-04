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
ms.date: 11/06/2018
ms.openlocfilehash: a54c47c0f67052f2ce486a97e009293a118919d4
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994121"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Överföra data för Apache Hadoop-jobb i HDInsight

Azure HDInsight ger en fullständig Hadoop distributed file system (HDFS) över Azure Storage och Azure Data Lake Storage (Gen1 och Gen2). Azure Storage och Data lake Storage Gen1 och Gen2 är utformade som HDFS-tillägg för att ge kunderna en sömlös upplevelse. De gör en fullständig uppsättning komponenter i Hadoop-ekosystemet att arbeta direkt på de data som den hanterar. Azure Storage, Data Lake Storage Gen1 och Gen2 är distinkta filsystem som är optimerade för lagring av data och beräkningar på dessa data. Information om fördelarna med Azure Storage finns i [använda Azure Storage med HDInsight][hdinsight-storage], [Använd Data Lake Storage Gen1 med HDInsight](hdinsight-hadoop-use-data-lake-store.md) och [ Använda Data Lake Storage Gen2 med HDInsight](../storage/data-lake-storage/use-hdi-cluster.md).

## <a name="prerequisites"></a>Förutsättningar

Observera följande krav innan du börjar:

* Ett Azure HDInsight-kluster. Anvisningar finns i [Kom igång med Azure HDInsight] [ hdinsight-get-started] eller [skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).
* Kunskap om de två följande artiklarna:

    - [Använda Azure Storage med HDInsight][hdinsight-storage]
    - [Använda Data Lake Storage Gen1 med HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    - [Använda Data Lake Storage Gen2 med HDInsight](../storage/data-lake-storage/use-hdi-cluster.md)   

## <a name="upload-data-to-azure-storage"></a>Ladda upp data till Azure Storage

### <a name="command-line-utilities"></a>Kommandoradsverktyg
Microsoft tillhandahåller följande verktyg att arbeta med Azure Storage:

| Verktyget | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Klassisk Azure CLI][azurecli] |✔ |✔ |✔ |
| [Azure PowerShell][azure-powershell] | | |✔ |
| [AzCopy][azure-azcopy] |✔ | |✔ |
| [Hadoop-kommando](#commandline) |✔ |✔ |✔ |

> [!NOTE]  
> Den klassiska Azure-CLI, Azure PowerShell och AzCopy kan alla användas från platser utanför Azure, finns bara kommandot Hadoop på HDInsight-kluster. Och kommandot tillåter endast läsa in data från det lokala filsystemet i Azure Storage.
>
>

#### <a id="xplatcli"></a>Klassisk Azure CLI
Den klassiska Azure-CLI är ett plattformsoberoende verktyg som hjälper dig att hantera Azure-tjänster. Använd följande steg för att ladda upp data till Azure Storage:

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. [Installera och konfigurera den klassiska Azure-CLI för Mac, Linux och Windows](../cli-install-nodejs.md).
2. Öppna en kommandotolk, bash eller andra gränssnitt och Använd följande för att autentisera till din Azure-prenumeration.

    ```cli
    azure login
    ```

    När du uppmanas, anger du användarnamn och lösenord för din prenumeration.
3. Ange följande kommando för att lista lagringskonton för din prenumeration:

    ```cli
    azure storage account list
    ```

4. Välj det lagringskonto som innehåller den blob som du vill arbeta med, och sedan använder du följande kommando för att hämta nyckeln för det här kontot:

    ```cli
    azure storage account keys list <storage-account-name>
    ```

    Det här kommandot returnerar de **primära** och **sekundära** nycklar. Kopiera den **primära** nyckelvärdet eftersom det kommer att användas i nästa steg.
5. Använd följande kommando för att hämta en lista över blob-behållare i lagringskontot:

    ```cli
    azure storage container list -a <storage-account-name> -k <primary-key>
    ```

6. Använd följande kommandon för att ladda upp och ladda ned filer till blob:

   * Att överföra en fil:

        ```cli
        azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>
        ```

   * Hämta en fil:

        ```cli
        azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>
        ```
    
> [!NOTE]  
> Om du arbetar alltid med samma lagringskonto kan du ange följande miljövariabler i stället för att ange konto och nyckel för varje kommando:
>
> * **AZURE\_STORAGE\_KONTO**: Lagringskontonamn
> * **AZURE\_STORAGE\_ÅTKOMST\_NYCKEL**: Lagringskontonyckeln
>
>

#### <a id="powershell"></a>Azure PowerShell
Azure PowerShell är en skriptmiljö som du kan använda för att styra och automatisera distributionen och hanteringen av dina arbetsbelastningar i Azure. Information om hur du konfigurerar din arbetsstation för att köra Azure PowerShell finns i [installera och konfigurera Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Ladda upp en lokal fil till Azure Storage**

1. Öppna Azure PowerShell-konsol som finns beskrivet i [installera och konfigurera Azure PowerShell](/powershell/azure/overview).
2. Ange värdena för de fem första variablerna i skriptet nedan:

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
AzCopy är ett kommandoradsverktyg som förenklar uppgiften att överföra data till och från ett Azure Storage-konto. Du kan använda det som ett fristående verktyg eller lägga till det här verktyget i ett befintligt program. [Ladda ned AzCopy][azure-azcopy-download].

AzCopy-syntaxen är:

```command
AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]
```

Mer information finns i [AzCopy - överföra/hämtar filer för Azure-Blobar][azure-azcopy].

Azcopy i Linux förhandsgranskning är tillgänglig.  Se [presenterar AzCopy i Linux-förhandsvisningen](https://blogs.msdn.microsoft.com/windowsazurestorage/2017/05/16/announcing-azcopy-on-linux-preview/).

#### <a id="commandline"></a>Hadoop-kommandoraden
Hadoop-kommandoraden är bara användbara för att lagra data i Azure storage-blob när data finns redan på klustrets huvudnod.

För att kunna använda kommandot Hadoop, måste du först ansluta till huvudnoden med någon av följande metoder:

* **Windows-baserade HDInsight**: [Ansluta med hjälp av fjärrskrivbord](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* **Linux-baserade HDInsight**: Ansluta med hjälp av [SSH eller PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

När du är ansluten, kan du använda följande syntax för att överföra en fil till lagring.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

Till exempel, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Eftersom standardfilsystemet för HDInsight finns i Azure Storage, som /example/data.txt faktiskt finns i Azure Storage. Du kan också referera till filen som:

    wasb:///example/data/data.txt

eller

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

En lista över andra Hadoop-kommandon som fungerar med filer som finns i [https://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> På Apache HBase-kluster kan standard blockstorlek som används vid skrivning av data är 256 KB. Även om detta fungerar bra när du använder HBase APIs eller REST API: er med hjälp av den `hadoop` eller `hdfs dfs` kommandon för att skriva data som är större än ~ 12 GB resulterar i ett fel. Mer information finns i den [för skrivning till blob storage-undantag](#storageexception) i den här artikeln.

### <a name="graphical-clients"></a>Grafiska klienter
Det finns också flera program som ger ett grafiskt gränssnitt för att arbeta med Azure Storage. I följande tabell är en lista över några av dessa program:

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio-verktyg för HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](https://storageexplorer.com/) |✔ |✔ |✔ |
| [Cloud Storage Studio 2](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Azure Explorer](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

#### <a name="visual-studio-tools-for-hdinsight"></a>Visual Studio-verktyg för HDInsight
Mer information finns i [navigera till de länkade resurserna](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources).

#### <a id="storageexplorer"></a>Azure Storage Explorer
*Azure Storage Explorer* är användbart för att granska och ändra data i blobar. Det är en kostnadsfri öppen källkod-verktyg som kan laddas ned från [ https://storageexplorer.com/ ](https://storageexplorer.com/). Källkoden finns även den här länken.

Innan du använder verktyget, vet du ditt Azure storage-konto och kontonyckeln. Anvisningar om hur du får den här informationen finns i ”så här: Visa, kopiera, och återskapa åtkomstnycklar ”avsnittet av [skapa, hantera eller ta bort ett lagringskonto][azure-create-storage-account].

1. Kör Azure Storage Explorer. Om det är första gången du har kört Lagringsutforskaren, frågas du om den **_qos-kontonamn** och **lagringskontonyckel**. Om du har kört den innan du kan använda den **Lägg till** för att lägga till ett nytt lagringskontonamn och nyckel.

    Ange namn och nyckel för lagringskontot som används av ditt HDInsight-kluster och välj sedan **Spara & Öppna**.

    ![HDI.AzureStorageExplorer][image-azure-storage-explorer]
2. I listan över behållare till vänster om gränssnittet, klickar du på namnet på den behållare som är associerad med ditt HDInsight-kluster. Det är namnet på HDInsight-klustret som standard men kan skilja sig om du har angett ett visst namn när du skapar klustret.
3. Välj ikonen ladda upp från verktygsfältet.

    ![Verktygsfält med överför ikon markerat](./media/hdinsight-upload-data/toolbar.png)
4. Ange en fil för att ladda upp och klicka sedan på **öppna**. När du uppmanas, väljer **överför** att överföra filen till roten i lagringsbehållaren. Om du vill överföra filen till en specifik sökväg, anger du sökvägen i den **mål** fältet och välj sedan **överför**.

    ![Dialogrutan för filöverföring](./media/hdinsight-upload-data/fileupload.png)

    När filen har överförts, kan du använda den från jobb i HDInsight-klustret.

### <a name="mount-azure-storage-as-local-drive"></a>Montera Azure Storage som lokal enhet
Se [montera Azure Storage som lokala enhet](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

### <a name="upload-using-services"></a>Överför med hjälp av tjänster
#### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory-tjänsten är en fullständigt hanterad tjänst för att skapa lagring och bearbetning av data movement datatjänster i effektiva, skalbara och tillförlitliga produktion datapipelines.

Azure Data Factory kan användas för att flytta data till Azure Storage eller skapa datapipelines som använder HDInsight-funktioner, till exempel Hive och Pig direkt.

Mer information finns i den [Azure Data Factory-dokumentation](https://azure.microsoft.com/documentation/services/data-factory/).

#### <a id="sqoop"></a>Apache Sqoop
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

### <a name="troubleshooting"></a>Felsökning
#### <a id="storageexception"></a>Skrivning till blob Storage-undantag
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

**Orsak**: HBase på HDInsight-kluster standard till en blockstorlek på 256KB när du skriver till Azure storage. När den fungerar för HBase APIs eller REST API: er, det resulterar i ett fel när du använder den `hadoop` eller `hdfs dfs` kommandoradsverktyg.

**Lösning**: Använd `fs.azure.write.request.size` att ange en större blockstorlek. Du kan göra detta på basis av per användning med hjälp av den `-D` parametern. Följande kommando är ett exempel som använder den här parametern med det `hadoop` kommando:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Du kan också öka värdet för `fs.azure.write.request.size` globalt med hjälp av Apache Ambari. Följande steg kan användas för att ändra värdet i Ambari-Webbgränssnittet:

1. Gå till Ambari-Webbgränssnittet för ditt kluster i din webbläsare. Det här är https://CLUSTERNAME.azurehdinsight.net, där **CLUSTERNAME** är namnet på klustret.

    När du uppmanas, anger du namn på serveradministratör och lösenordet för klustret.
2. Till vänster på skärmen Välj **HDFS**, och välj sedan den **Peeringkonfigurationer** fliken.
3. I den **Filter...**  anger `fs.azure.write.request.size`. Då visas i fältet och det aktuella värdet i mitten på sidan.
4. Ändra värdet från 262144 (256KB) till det nya värdet. Till exempel 4194304 (4MB).

![Bild av ändra värdet via Ambari-Webbgränssnittet](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Läs mer om hur du använder Ambari [hantera HDInsight-kluster med hjälp av Apache Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Nästa steg
Nu när du vet hur du hämtar data till HDInsight kan du läsa följande artiklar för att lära dig att utföra analyser av:

* [Kom igång med Azure HDInsight][hdinsight-get-started]
* [Skicka Apache Hadoop-jobb programmässigt][hdinsight-submit-jobs]
* [Använda Apache Hive med HDInsight][hdinsight-use-hive]
* [Använda Apache Hive med HDInsight][hdinsight-use-pig]

[azure-management-portal]: https://porta.azure.com
[azure-powershell]: https://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]:../storage/common/storage-create-storage-account.md
[azure-azcopy-download]:../storage/common/storage-use-azcopy.md
[azure-azcopy]:../storage/common/storage-use-azcopy.md

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-adls-gen1]: hdinsight-hadoop-use-data-lake-store.md
[hdinsight-adls-gen2]: ../storage/data-lake-storage/use-hdi-cluster.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[azurecli]: ../cli-install-nodejs.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
