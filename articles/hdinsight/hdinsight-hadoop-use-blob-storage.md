---
title: Fråga efter data i HDFS-kompatibelt Azure-lagringsutrymme – Azure HDInsight
description: Lär dig mer om hur du frågar efter data från Azure Storage och Azure Data Lake Store för att lagra resultatet av dina analyser.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 359cfd5b0eba25de25ce4200a61b0103a3d0fade
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384810"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Använda Azure-lagring med Azure HDInsight-kluster

Om du vill analysera data i HDInsight-kluster kan du lagra data i Azure Storage, [Azure Data Lake Storage Gen 1 / Azure Data Lake Storage Gen2] eller båda. Båda lagringsalternativen låter dig ta bort HDInsight-kluster som används för beräkning utan att förlora användardata.

Apache Hadoop stöder begreppet standardfilsystem. Standardfilsystemet kräver att ett standardschema och en utfärdare används. Det kan också användas för att matcha relativa sökvägar. Under skapandeprocessen för HDInsight-kluster kan du ange en blobbehållare i Azure Storage som standardfilsystemet eller med HDInsight 3.6, kan du välja antingen Azure Storage eller Azure Data Lake Storage Gen 1 / Azure Data Lake Store Gen 2 som standardfiler system med några undantag. Support för att använda Data Lake Storage Gen 1 som både standardwebbplatsen och den länkade storage, se [tillgänglighet för HDInsight-kluster](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

I den här artikeln får du lära dig hur Azure Storage fungerar med HDInsight-kluster. Läs hur Data Lake Storage Gen 1 fungerar med HDInsight-kluster i [Använd Azure Data Lake Store med Azure HDInsight-kluster](hdinsight-hadoop-use-data-lake-store.md). Mer information om hur du skapar ett HDInsight-kluster finns i [skapa Apache Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Azure Storage är en robust lagringslösning för allmänna ändamål som smidigt kan integreras med HDInsight. HDInsight kan använda en blobcontainer i Azure Storage som standardfilsystem för klustret. Genom ett gränssnitt för Hadoop-distribuerat filsystem (HDFS) kan alla komponenter i HDInsight tillämpas direkt på strukturerade eller ostrukturerade data som har lagrats som blobar.

> [!WARNING]  
> Det finns flera tillgängliga alternativ när du skapar ett Azure Storage-konto. I följande tabell finns information om vilka alternativ som stöds med HDInsight:

| Storage Account-typ | Tjänster som stöds | Stöds prestandanivåer | Stöds åtkomstnivåerna |
|----------------------|--------------------|-----------------------------|------------------------|
| Generell användning V2   | Blob               | Standard                    | Frekvent, lågfrekvent Archive3    |
| General-Purpose V1   | Blob               | Standard                    | Gäller inte                    |
| Blob Storage         | Blob               | Standard                    | Frekvent, lågfrekvent Archive3    |

Vi rekommenderar att du inte använder standardcontainern för att lagra företagsdata. Ta bort standardcontainern efter varje användning för att minska lagringskostnaden. Observera att standardcontainern innehåller program- och systemloggar. Se till att hämta loggarna innan du tar bort containern.

Det går inte att dela en blob-container som standardfilsystem över flera kluster.

## <a name="hdinsight-storage-architecture"></a>Lagringsarkitekturen i HDInsight
Följande diagram visar en abstrakt vy av lagringsarkitekturen i HDInsight med Azure Storage:

![Hadoop-kluster använder HDFS API för att komma åt och lagra strukturerade och ostrukturerade data i Blob Storage.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "HDInsight Storage-arkitektur")

HDInsight ger tillgång till det distribuerade filsystemet som är lokalt anslutet till beräkningsnoderna. Detta filsystem kan nås med hjälp av den fullständigt kvalificerade URI-strängen, till exempel:

    hdfs://<namenodehost>/<path>

Dessutom ger HDInsight möjlighet att komma åt data som är lagrade i Azure Storage. Syntax:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Här är några saker att tänka på när du använder Azure Storage-konton med HDInsight-kluster.

* **Behållare på lagringskonton som är anslutna till ett kluster:** Eftersom kontonamnet och nyckeln associeras med klustret när du skapar, har du fullständig åtkomst till blobarna i dessa behållare.

* **Offentliga behållare eller offentliga blobar i lagringskonton som inte är anslutna till ett kluster:** Du har läsbehörighet till blobarna i behållarna.
  
  > [!NOTE]  
  > Offentliga containrar låter dig hämta en lista över alla blobar som är tillgängliga i containern samt hämta metadata för containern. Du kan endast komma åt offentliga blobar om du känner till den exakta webbadressen. Mer information finns i <a href="https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources">hantera åtkomst till behållare och blobbar</a>.
  > 
  > 
* **Privata behållare på lagringskonton som inte är anslutna till ett kluster:** Du kan inte komma åt blobarna i behållarna om du inte definierar lagringskontot när du skickar WebHCat-jobben. Detta beskrivs senare i artikeln.

De lagringskonton som definieras under skapandeprocessen och deras nycklar lagras i %HADOOP_HOME%/conf/core-site.xml i klusternoderna. Standardbeteendet för HDInsight är att använda de lagringskonton som definieras i filen core-site.xml. Du kan ändra den här inställningen med hjälp av [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Flera WebHCat-jobb, inklusive Apache Hive, MapReduce, Apache Hadoop-strömmande och Apache Pig, kan innehålla en beskrivning av lagringskonton och metadata med dem. (För närvarande fungerar för Pig med lagringskonton, men inte för metadata.) Mer information finns i [Använda ett HDInsight-kluster med alternativa lagringskonton och metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Blobar kan användas för strukturerade och ostrukturerade data. I blobcontainrar förvaras data som nyckel/värde-par och det finns ingen kataloghierarki. Däremot kan snedstreck (/) användas i nyckelnamnet så att det visas som om det vore en fil som lagrats i en katalogstruktur. Nyckeln för en blob kan till exempel vara *input/log1.txt*. Det finns ingen faktisk katalog för *indata*, men eftersom det finns ett snedstreck i nyckelnamnet ser namnet ut som en filsökväg.

## <a id="benefits"></a>Fördelar med Azure Storage
Den medförande prestandakostnaden för att inte samplacera beräkningskluster och lagringsresurser minskar, på grund av sättet på vilket beräkningsklustren skapas nära lagringskontoresurserna i Azure-regionen, där höghastighetsnätverket gör att beräkningsnoderna effektivt kan komma åt data i Azure Storage.

Det finns flera fördelar med att lagra data i Azure Storage i stället för i HDFS:

* **Data återanvändning och delning av:** Data i HDFS lagras inuti beräkningsklustren. Endast de program som har åtkomst till beräkningsklustren kan använda dessa data med hjälp av HDFS-API:er. Data i Azure Storage kan antingen nås via HDFS-API:erna eller via [REST-API:erna för Blob Storage][blob-storage-restAPI]. Därmed kan en större grupp program (inklusive andra HDInsight-kluster) och verktyg användas för att skapa och använda data.
* **Dataarkivering:** Lagra data i Azure storage kan de HDInsight-kluster som används för beräkning tryggt tas bort utan att förlora användardata.
* **Kostnad för datalagring:** Långsiktigt att lagra data i DFS är dyrare än att lagra data i Azure storage eftersom kostnaden för ett beräkningskluster är högre än kostnaden för Azure storage. Eftersom data inte behöver läsas in på nytt för varje generation av beräkningskluster sparar du dessutom kostnader för datainläsning.
* **Elastisk utskalning:** Även om HDFS ger dig ett utskalat filsystem, bestäms skalan av antalet noder som du skapar för klustret. Att ändra skala med HDFS kan vara en mer komplicerad process än att använda de elastiska skalningsfunktionerna som du automatiskt har tillgång till i Azure Storage.
* **GEO-replikering:** Din Azure-lagring kan vara geo-replikerade. Även om detta ger dig geografisk återställning och dataredundans, innebär en växling till den geo-replikerade platsen en avsevärd försämring av prestandan och kan medföra ytterligare kostnader. Vår rekommendation är därför att tänka efter när du väljer geo-replikering och bara göra det om värdet i informationen motiverar den extra kostnaden.

Vissa MapReduce-jobb och -paket kan skapa mellanresultat som du inte egentligen vill lagra i Azure Storage. I så fall kan du välja att lagra data i ditt lokala HDFS. Faktum är att HDInsight använder DFS för flera av dessa mellanresultat i Hive-jobb och andra processer.

> [!NOTE]  
> De flesta HDFS-kommandon (till exempel <b>ls</b>, <b>copyFromLocal</b> och <b>mkdir</b>) fungerar fortfarande som förväntat. Endast de kommandon som är specifika för den interna HDFS-implementeringen (så kallade DFS-kommandon) som <b>fschk</b> och <b>dfsadmin</b> har ett avvikande beteende i Azure Storage.


## <a name="create-blob-containers"></a>Skapa blobcontainrar
Om du vill använda blobar måste du först skapa ett [Azure Storage-konto][azure-storage-create]. Som en del av detta anger du en Azure-region där lagringskontot ska skapas. Klustret och lagringskontot måste finnas i samma region. SQL Server-databas för Hive metastore och SQL Server-databasen för Apache Oozie metastore måste också finnas i samma region.

Oavsett var den finns tillhör varje blob som du skapar en container på ditt Azure Storage-konto. Den här containern kan vara en befintlig blob som skapats utanför HDInsight eller en container som skapats för ett HDInsight-kluster.

Standardcontainern lagrar klusterspecifik information, till exempel jobbhistorik och loggar. Låt inte flera HDInsight-kluster dela en standardblob-container. Detta kan skada jobbets historik. Du rekommenderas att använda en annan container för varje kluster och publicera delade data på ett konto för länkad lagring som anges vid distributionen av alla relevanta kluster snarare än på standardkontot för lagring. Mer information om hur du konfigurerar länkade lagringskonton finns i [Skapa HDInsight-kluster][hdinsight-creation]. Du kan emellertid återanvända en standardcontainer för lagring när det ursprungliga HDInsight-klustret har tagits bort. När det gäller HBase-kluster kan du faktiskt behålla HBase-tabellschemat och data genom att skapa en ny blobcontainer som standard som använts av ett HBase-kluster som har tagits bort.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>Använda Azure-portalen
När du skapar ett HDInsight-kluster från portalen kan du använda ett befintligt lagringskonto (enligt nedan) och ange uppgifterna för lagringskontot. Du kan även ange om du vill att ett konto med ytterligare lagringsutrymme som är associerade med klustret och välj i så fall från Data Lake Store eller en annan Azure Storage-blob som ytterligare lagringsutrymme.

![HDInsight hadoop, skapa datakälla](./media/hdinsight-hadoop-use-blob-storage/hdinsight.provision.data.source.png)

> [!WARNING]  
> Du kan inte använda ett annat lagringskonto på en annan plats än HDInsight-klustret.


### <a name="use-azure-powershell"></a>Använda Azure PowerShell
Om du har [installerat och konfigurerat Azure PowerShell][powershell-install] kan du använda följande från Azure PowerShell-prompten för att skapa ett lagringskonto och en container:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"

    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"

    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID

    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location

    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS 

    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

### <a name="use-azure-classic-cli"></a>Använda Azure klassiska CLI

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

Om du har [installerat och konfigurerat den klassiska Azure-CLI](../cli-install-nodejs.md), följande kommando kan användas för att ett lagringskonto och en behållare.

```cli
azure storage account create <storageaccountname> --type LRS
```

> [!NOTE]  
> Parametern `--type` anger hur lagringskontot kommer att replikeras. Mer information finns i [Azure Storage-replikering](../storage/storage-redundancy.md). Använd inte ZRS eftersom ZRS inte stöder sidblobbar, filer, tabeller eller köer.

Du kommer att bli ombedd att ange den geografiska region som lagringskontot kommer att finnas i. Du bör skapa lagringskontot i den region där du planerar att skapa ditt HDInsight-kluster.

När du har skapat lagringskontot använder du följande kommando för att hämta nycklar till lagringskontot:

```cli
azure storage account keys list <storageaccountname>
```

Om du vill skapa en container använder du följande kommando:

```cli
azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>
```

## <a name="address-files-in-azure-storage"></a>Adressera filer i Azure Storage
Följande URI-schema används för att komma åt filer i Azure Storage från HDInsight:

```config
wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

URI-schemat ger okrypterad åtkomst (med prefixet *wasb:*) och SSL-krypterad åtkomst (med *wasbs*). Vi rekommenderar att du använder *wasbs* när det är möjligt, även för åtkomst till data som finns i samma region i Azure.

&lt;BlobStorageContainerName&gt; identifierar namnet på blobbehållaren i Azure Storage.
&lt;StorageAccountName&gt; identifierar namnet på Azure Storage-kontot. Ett fullständigt kvalificerat domännamn (FQDN) krävs.

Om varken &lt;BlobStorageContainerName&gt; eller &lt;StorageAccountName&gt; har angetts används standardfilsystemet. För filer i filsystemet kan du använda en relativ sökväg eller en absolut sökväg. Till exempel kan du referera till den *hadoop-mapreduce-examples.jar*-fil som medföljer HDInsight-kluster på något av följande sätt:

```config
wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasb:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Filnamnet är <i>hadoop examples.jar</i> i HDInsight-kluster av version 2.1 och 1.6.

&lt;Sökvägen&gt; är filens eller katalogens HDFS-sökvägsnamn. Eftersom containrar i Azure Storage helt enkelt är lagringsplatser för nyckel-/värdepar finns det inte något faktiskt hierarkiskt filsystem. Ett snedstreck (/) i en blob-nyckel tolkas som en katalogavgränsare. Blob-namnet för *hadoop-mapreduce-examples.jar* är till exempel:

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> När du arbetar med blobar utanför HDInsight kan de flesta verktyg inte identifiera WASB-formatet utan förväntar sig i stället ett grundläggande sökvägsformat som `example/jars/hadoop-mapreduce-examples.jar`.

## <a name="access-blobs"></a>Få åtkomst till blobbar

### <a name="access-blobs-using-azure-powershell"></a>Använda Azure PowerShell

> [!NOTE]

> Kommandona i det här avsnittet ger grundläggande exempel på hur du använder PowerShell för att komma åt data som är lagrade i blobar. Om du vill ha ett mer komplett exempel som är anpassat för att arbeta med HDInsight, se [HDInsight Tools](https://github.com/Blackmist/hdinsight-tools).

Använd följande kommando för att visa en lista över blob-relaterade cmdlets:

```powershell 
Get-Command *blob*
```

![Lista över blob-relaterade PowerShell-cmdlets.][img-hdi-powershell-blobcommands]

#### <a name="upload-files"></a>Överföra filer

Mer information finns i [Överföra data till HDInsight][hdinsight-upload-data].

#### <a name="download-files"></a>Hämta filer

Följande skript hämtar en blockblob till den aktuella mappen. Innan du kör skriptet ändrar du katalogen till en mapp där du har skrivbehörighet.

```powershell
$resourceGroupName = "<AzureResourceGroupName>"
$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at creation.
$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
$blob = "example/data/sample.log" # The name of the blob to be downloaded.

# Use Add-AzureAccount if you haven't connected to your Azure subscription
Connect-AzureRmAccount 
Select-AzureRmSubscription -SubscriptionID "<Your Azure Subscription ID>"

Write-Host "Create a context object ... " -ForegroundColor Green
$storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

Write-Host "Download the blob ..." -ForegroundColor Green
Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

Write-Host "List the downloaded file ..." -ForegroundColor Green
cat "./$blob"
```

Om du anger resursgruppens namn och klusternamnet kan du använda följande kod:

```powershell
$resourceGroupName = "<AzureResourceGroupName>"
$clusterName = "<HDInsightClusterName>"
$blob = "example/data/sample.log" # The name of the blob to be downloaded.

$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
$defaultStorageContainer = $cluster.DefaultStorageContainer
$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 

Write-Host "Download the blob ..." -ForegroundColor Green
Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob $blob -Context $storageContext -Force
```

#### <a name="delete-files"></a>Ta bort filer

```powershell
Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob
```

#### <a name="list-files"></a>Lista filer

```powershell
Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix "example/data/"
```

#### <a name="run-hive-queries-using-an-undefined-storage-account"></a>Köra Hive-frågor med ett odefinierat lagringskonto

Det här exemplet visar hur du listar en mapp från lagringskontot som inte har definierats under skapandeprocessen.

```powershell
$clusterName = "<HDInsightClusterName>"

$undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
$undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

$undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

Use-AzureRmHDInsightCluster $clusterName

$defines = @{}
$defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

Invoke-AzureRmHDInsightHiveJob -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"
```

### <a name="use-azure-classic-cli"></a>Använda Azure klassiska CLI

Använd följande kommando för att visa en lista över blob-relaterade kommandon:

```cli
azure storage blob
```

**Exempel på att använda klassiska Azure-CLI för att överföra en fil**

```cli
azure storage blob upload <sourcefilename> <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>
```

**Exempel på att använda klassiska Azure-CLI för att hämta en fil**

```cli
azure storage blob download <containername> <blobname> <destinationfilename> --account-name <storageaccountname> --account-key <storageaccountkey>
```

**Exempel på klassiska Azure-CLI att ta bort en fil**

```cli
azure storage blob delete <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>
```

**Exempel på att använda klassiska Azure-CLI att lista filer**

```cli
azure storage blob list <containername> <blobname|prefix> --account-name <storageaccountname> --account-key <storageaccountkey>
```

## <a name="use-additional-storage-accounts"></a>Använda ytterligare lagringskonton

När du skapar ett HDInsight-kluster kan du ange ett Azure Storage-konto som du vill koppla det till. Förutom det här lagringskontot kan du lägga till ytterligare lagringskonton från samma Azure-prenumeration eller andra Azure-prenumerationer under skapandeprocessen eller efter att ett kluster har skapats. Mer information om hur du lägger till ytterligare lagringskonton finns i [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Du kan inte använda ett annat lagringskonto på en annan plats än HDInsight-klustret.

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du lära dig hur du använder det HDFS-kompatibla Azure Storage med HDInsight. Du kan skapa skalbara, långsiktiga lösningar för arkivering av insamlade data samt HDInsight för att få tillgång till informationen i lagrade strukturerade och ostrukturerade data.

Mer information finns i:

* [Kom igång med Azure HDInsight][hdinsight-get-started]
* [Kom igång med Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
* [Överföra data till HDInsight][hdinsight-upload-data]
* [Använda Apache Hive med HDInsight][hdinsight-use-hive]
* [Använda Apache Hive med HDInsight][hdinsight-use-pig]
* [Använda signaturer för delad åtkomst i Azure Storage för att begränsa åtkomsten till data med HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: https://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
