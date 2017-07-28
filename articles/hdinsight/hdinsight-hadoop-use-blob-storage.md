---
title: "Fråga efter data i HDFS-kompatibelt Azure-lagringsutrymme - Azure HDInsight | Microsoft Docs"
description: "Lär dig mer om hur du frågar efter data från Azure Storage och Azure Data Lake Store för att lagra resultatet av dina analyser."
keywords: blob storage,hdfs,structured data,unstructured data,data lake store,Hadoop input,Hadoop output, hadoop storage, hdfs input,hdfs output,hdfs storage,wasb azure
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 1d2e65f2-16de-449e-915f-3ffbc230f815
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/09/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: 4a46c7d9a030adb9c0407fda622ccd787212b030
ms.contentlocale: sv-se
ms.lasthandoff: 06/10/2017

---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Använda Azure-lagring med Azure HDInsight-kluster

För att analysera data i HDInsight-klustret kan du lagra data i antingen Azure Storage, Azure Data Lake Store eller bådadera. Båda lagringsalternativen låter dig ta bort HDInsight-kluster som används för beräkning utan att förlora användardata.

Hadoop stöder begreppet standardfilsystem. Standardfilsystemet kräver att ett standardschema och en utfärdare används. Det kan också användas för att matcha relativa sökvägar. Du kan ange en blobbehållare i Azure Storage som standardfilsystemet när du skapar HDInsight-kluster. Med HDInsight 3.5 kan du välja antingen Azure Storage eller Azure Data Lake Store som standardfilsystem med ett fåtal undantag. Support för att använda Data Lake Store som både standardwebbplatsen och den länkade storage finns [tillgången för HDInsight-kluster](#availabilities-for-hdinsight-clusters]).

I den här artikeln får du lära dig hur Azure Storage fungerar med HDInsight-kluster. Läs mer om hur Data Lake Store fungerar med HDInsight-kluster i [Använd Azure Data Lake Store med Azure HDInsight-kluster](hdinsight-hadoop-use-data-lake-store.md). Mer information om hur du skapar ett HDInsight-kluster finns i [Create Hadoop clusters in HDInsight](hdinsight-hadoop-provision-linux-clusters.md) (Skapa Hadoop-kluster i HDInsight).

Azure Storage är en robust lagringslösning för allmänna ändamål som smidigt kan integreras med HDInsight. HDInsight kan använda en blobbehållare i Azure Storage som standardfilsystem för klustret. Genom ett gränssnitt för Hadoop-distribuerat filsystem (HDFS) kan alla komponenter i HDInsight tillämpas direkt på strukturerade eller ostrukturerade data som har lagrats som blobar.

> [!WARNING]
> Det finns flera tillgängliga alternativ när du skapar ett Azure Storage-konto. I följande tabell finns information om vilka alternativ som stöds med HDInsight:
> 
> | Storage Account-typ | Lagringsnivå | Stöds av HDInsight |
> | ------- | ------- | ------- |
> | Allmänna lagringskonton | Standard | __Ja__ |
> | &nbsp; | Premium | Nej |
> | Blob Storage-konto | Frekvent | Nej |
> | &nbsp; | Lågfrekvent | Nej |

Vi rekommenderar att du inte använder standardbehållaren för att lagra företagsdata. Ta bort standardbehållaren efter varje användning för att minska lagringskostnaden. Observera att standardbehållaren innehåller program- och systemloggar. Se till att hämta loggarna innan du tar bort behållaren.

Det går inte att dela en blobbehållare över flera kluster.

## <a name="hdinsight-storage-architecture"></a>Lagringsarkitekturen i HDInsight
Följande diagram visar en abstrakt vy av lagringsarkitekturen i HDInsight med Azure Storage:

![Hadoop-kluster använder HDFS API för att komma åt och lagra strukturerade och ostrukturerade data i Blob Storage.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "HDInsight Storage-arkitektur")

HDInsight ger tillgång till det distribuerade filsystemet som är lokalt anslutet till beräkningsnoderna. Detta filsystem kan nås med hjälp av den fullständigt kvalificerade URI-strängen, till exempel:

    hdfs://<namenodehost>/<path>

Dessutom ger HDInsight möjlighet att komma åt data som är lagrade i Azure Storage. Syntax:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Här är några saker att tänka på när du använder Azure Storage-konton med HDInsight-kluster.

* **Behållare på de lagringskonton som är anslutna till ett kluster:** Eftersom kontonamnet och nyckeln associeras med klustret när det skapas har du full tillgång till blobarna i dessa behållare.

* **Offentliga behållare eller offentliga blobar på lagringskonton som INTE är anslutna till något kluster:** Du har läsbehörighet till blobarna i dessa behållare.
  
  > [!NOTE]
  > Offentliga behållare låter dig hämta en lista över alla blobar som är tillgängliga i behållaren samt hämta metadata för behållaren. Du kan endast komma åt offentliga blobar om du känner till den exakta webbadressen. Mer information finns i <a href="http://msdn.microsoft.com/library/windowsazure/dd179354.aspx">Begränsa åtkomsten till behållare och blobar</a>.
  > 
  > 
* **Privata behållare på lagringskonton som INTE är anslutna till något kluster:** Du kan inte komma åt blobarna i behållarna om du inte definierar lagringskontot när du skickar WebHCat-jobben. Detta beskrivs senare i artikeln.

De lagringskonton som definieras under skapandeprocessen och deras nycklar lagras i %HADOOP_HOME%/conf/core-site.xml i klusternoderna. Standardbeteendet för HDInsight är att använda de lagringskonton som definieras i filen core-site.xml. Vi rekommenderar inte att du redigerar filen core-site.xml direkt eftersom klustrets huvudnod (master) kan bli föremål för en ny avbildning eller migreras när som helst, och då kommer alla ändringar i denna fil att gå förlorade.

Flera WebHCat-jobb, inklusive Hive, MapReduce, Hadoop-strömning och Pig, kan ha med sig en beskrivning av lagringskonton och metadata. (För närvarande fungerar för Pig med lagringskonton, men inte för metadata.) I avsnittet [Komma åt blobar med hjälp av Azure PowerShell](#powershell) i den här artikeln finns det ett exempel på den här funktionen. Mer information finns i [Använda ett HDInsight-kluster med alternativa lagringskonton och metastores](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Blobar kan användas för strukturerade och ostrukturerade data. I blob-behållare förvaras data som nyckel/värde-par och det finns ingen kataloghierarki. Däremot kan snedstreck (/) användas i nyckelnamnet så att det visas som om det vore en fil som lagrats i en katalogstruktur. Nyckeln för en blob kan till exempel vara *input/log1.txt*. Det finns ingen faktisk katalog för *indata*, men eftersom det finns ett snedstreck i nyckelnamnet ser namnet ut som en filsökväg.

## <a id="benefits"></a>Fördelar med Azure Storage
Den implicerade prestandakostnaden för att inte samplacera beräkningsklustren och lagringsresurserna minskar på grund av sättet på vilket beräkningsklustren skapas nära lagringskontoresurserna i Azure-regionen där höghastighetsnätverket gör att beräkningsnoderna kan komma åt data i Azure Storage på ett mycket effektivt sätt.

Det finns flera fördelar med att lagra data i Azure Storage i stället för i HDFS:

* **Återanvändning och delning av data:** Data i HDFS lagras inuti beräkningsklustren. Endast de program som har åtkomst till beräkningsklustren kan använda dessa data med hjälp av HDFS-API:er. Data i Azure Storage kan antingen nås via HDFS-API:erna eller via [REST-API:erna för Blob Storage][blob-storage-restAPI]. Därmed kan en större grupp program (inklusive andra HDInsight-kluster) och verktyg användas för att skapa och använda data.
* **Dataarkivering:** Om data lagras i Azure Storage kan de HDInsight-kluster som används för beräkning tryggt tas bort utan att användardata går förlorade.
* **Kostnad för datalagring:** Att lagra data långsiktigt i DFS är dyrare än att lagra data i Azure Storage eftersom kostnaden för ett beräkningskluster är högre än kostnaden för Azure Storage. Eftersom data inte behöver läsas in på nytt för varje generation av beräkningskluster sparar du dessutom kostnader för datainläsning.
* **Elastisk utskalning:** Även om HDFS ger dig ett utskalat filsystem bestäms skalan av antalet noder som du skapar för klustret. Att ändra skala med HDFS kan vara en mer komplicerad process än att använda de elastiska skalningsfunktionerna som du automatiskt har tillgång till i Azure Storage.
* **Geo-replikering:** Din Azure Storage kan geo-replikeras. Även om detta ger dig geografisk återställning och dataredundans, innebär en växling till den geo-replikerade platsen en avsevärd försämring av prestandan och kan medföra ytterligare kostnader. Vår rekommendation är därför att tänka efter när du väljer geo-replikering och bara göra det om värdet i informationen motiverar den extra kostnaden.

Vissa MapReduce-jobb och -paket kan skapa mellanresultat som du inte egentligen vill lagra i Azure Storage. I så fall kan du välja att lagra data i ditt lokala HDFS. Faktum är att HDInsight använder DFS för flera av dessa mellanresultat i Hive-jobb och andra processer.

> [!NOTE]
> De flesta HDFS-kommandon (till exempel <b>ls</b>, <b>copyFromLocal</b> och <b>mkdir</b>) fungerar fortfarande som förväntat. Endast de kommandon som är specifika för den interna HDFS-implementeringen (så kallade DFS-kommandon) som <b>fschk</b> och <b>dfsadmin</b> har ett avvikande beteende i Azure Storage.
> 
> 

## <a name="create-blob-containers"></a>Skapa blob-behållare
Om du vill använda blobar måste du först skapa ett [Azure Storage-konto][azure-storage-create]. Som en del av detta anger du en Azure-region där lagringskontot ska skapas. Klustret och lagringskontot måste finnas i samma region. SQL Server-databasen för Hive metastore och SQL Server-databasen för Oozie metastore måste också finnas i samma region.

Oavsett var den finns tillhör varje blob som du skapar en behållare på ditt Azure Storage-konto. Den här behållaren kan vara en befintlig blob som skapats utanför HDInsight eller en behållare som skapats för ett HDInsight-kluster.

Standardbehållaren lagrar klusterspecifik information, till exempel jobbhistorik och loggar. Låt inte flera HDInsight-kluster dela en standardblob-behållare. Detta kan skada jobbets historik. Du rekommenderas att använda en annan behållare för varje kluster och publicera delade data på ett konto för länkad lagring som anges vid distributionen av alla relevanta kluster snarare än på standardkontot för lagring. Mer information om hur du konfigurerar länkade lagringskonton finns i [Skapa HDInsight-kluster][hdinsight-creation]. Du kan emellertid återanvända en standardbehållare för lagring när det ursprungliga HDInsight-klustret har tagits bort. När det gäller HBase-kluster kan du faktiskt behålla HBase-tabellschemat och data genom att skapa en ny blob-behållare som standard som använts av ett HBase-kluster som har tagits bort.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>Använda Azure-portalen
När du skapar ett HDInsight-kluster från portalen kan du använda ett befintligt lagringskonto (enligt nedan) och ange uppgifterna för lagringskontot. Du kan även ange om du vill att ett konto med ytterligare lagringsutrymme som är associerade med klustret och välj i så fall från Data Lake Store eller en annan Azure Storage-blob som ytterligare lagringsutrymme.

![HDInsight hadoop, skapa datakälla](./media/hdinsight-hadoop-use-blob-storage/hdinsight.provision.data.source.png)

> [!WARNING]
> Du kan inte använda ett annat lagringskonto på en annan plats än HDInsight-klustret.


### <a name="use-azure-powershell"></a>Använda Azure PowerShell
Om du har [installerat och konfigurerat Azure PowerShell][powershell-install] kan du använda följande från Azure PowerShell-prompten för att skapa ett lagringskonto och en behållare:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"

    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"

    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID

    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location

    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS 

    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

### <a name="use-azure-cli"></a>Använda Azure CLI

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

Om du har [installerat och konfigurerat Azure CLI](../cli-install-nodejs.md) kan du använda följande kommando för att skapa ett lagringskonto och en behållare.

    azure storage account create <storageaccountname> --type LRS

> [!NOTE]
> Parametern `--type` anger hur lagringskontot kommer att replikeras. Mer information finns i [Azure Storage-replikering](../storage/storage-redundancy.md). Använd inte ZRS eftersom ZRS inte stöder sidblobar, filer, tabeller eller köer.
> 
> 

Du kommer att bli ombedd att ange den geografiska region som lagringskontot kommer att finnas i. Du bör skapa lagringskontot i den region där du planerar att skapa ditt HDInsight-kluster.

När du har skapat lagringskontot använder du följande kommando för att hämta nycklar till lagringskontot:

    azure storage account keys list <storageaccountname>

Om du vill skapa en behållare använder du följande kommando:

    azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>

## <a name="address-files-in-azure-storage"></a>Adressera filer i Azure Storage
Följande URI-schema används för att komma åt filer i Azure Storage från HDInsight:

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>

URI-schemat ger okrypterad åtkomst (med prefixet *wasb:*) och SSL-krypterad åtkomst (med *wasbs*). Vi rekommenderar att du använder *wasbs* när det är möjligt, även för åtkomst till data som finns i samma region i Azure.

&lt;BlobStorageContainerName&gt; identifierar namnet på blobbehållaren i Azure Storage.
&lt;StorageAccountName&gt; identifierar namnet på Azure Storage-kontot. Ett fullständigt kvalificerat domännamn (FQDN) krävs.

Om varken &lt;BlobStorageContainerName&gt; eller &lt;StorageAccountName&gt; har angetts används standardfilsystemet. För filer i filsystemet kan du använda en relativ sökväg eller en absolut sökväg. Till exempel kan du referera till den *hadoop-mapreduce-examples.jar*-fil som medföljer HDInsight-kluster på något av följande sätt:

    wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasb:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> Filnamnet är <i>hadoop examples.jar</i> i HDInsight-kluster av version 2.1 och 1.6.
> 
> 

&lt;Sökvägen&gt; är filens eller katalogens HDFS-sökvägsnamn. Eftersom behållare i Azure Storage helt enkelt är lagringsplatser för nyckel-/värdepar finns det inte något faktiskt hierarkiskt filsystem. Ett snedstreck (/) i en blob-nyckel tolkas som en katalogavgränsare. Blob-namnet för *hadoop-mapreduce-examples.jar* är till exempel:

    example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> När du arbetar med blobar utanför HDInsight kan de flesta verktyg inte identifiera WASB-formatet utan förväntar sig i stället ett grundläggande sökvägsformat som `example/jars/hadoop-mapreduce-examples.jar`.
> 
> 

## <a name="access-blobs"></a>Få åtkomst till blobbar 


### <a name="access-blobs-using-azure-powershell"></a>Använda Azure PowerShell
> [!NOTE]
> Kommandona i det här avsnittet ger grundläggande exempel på hur du använder PowerShell för att komma åt data som är lagrade i blobar. Om du vill ha ett mer komplett exempel som är anpassat för att arbeta med HDInsight, se [HDInsight Tools](https://github.com/Blackmist/hdinsight-tools).
> 
> 

Använd följande kommando för att visa en lista över blob-relaterade cmdlets:

    Get-Command *blob*

![Lista över blob-relaterade PowerShell-cmdlets.][img-hdi-powershell-blobcommands]

#### <a name="upload-files"></a>Överföra filer
Mer information finns i [Överföra data till HDInsight][hdinsight-upload-data].

#### <a name="download-files"></a>Hämta filer
Följande skript hämtar en blockblob till den aktuella mappen. Innan du kör skriptet ändrar du katalogen till en mapp där du har skrivbehörighet.

    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at creation.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.

    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    Login-AzureRmAccount 
    Select-AzureRmSubscription -SubscriptionID "<Your Azure Subscription ID>"

    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

    Write-Host "List the downloaded file ..." -ForegroundColor Green
    cat "./$blob"

Om du anger resursgruppens namn och klusternamnet kan du använda följande kod:

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


#### <a name="delete-files"></a>Ta bort filer
    Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob

#### <a name="list-files"></a>Lista filer
    Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix "example/data/"

#### <a name="run-hive-queries-using-an-undefined-storage-account"></a>Köra Hive-frågor med ett odefinierat lagringskonto
Det här exemplet visar hur du listar en mapp från lagringskontot som inte har definierats under skapandeprocessen.
$clusterName = "<HDInsightClusterName>"

    $undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
    $undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

    $undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

    Use-AzureRmHDInsightCluster $clusterName

    $defines = @{}
    $defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

    Invoke-AzureRmHDInsightHiveJob -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"

### <a name="use-azure-cli"></a>Använda Azure CLI
Använd följande kommando för att visa en lista över blob-relaterade kommandon:

    azure storage blob

**Exempel på hur du använder Azure CLI för att överföra en fil**

    azure storage blob upload <sourcefilename> <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**Exempel på hur du använder Azure CLI för att hämta en fil**

    azure storage blob download <containername> <blobname> <destinationfilename> --account-name <storageaccountname> --account-key <storageaccountkey>

**Exempel på hur du använder Azure CLI för att ta bort en fil**

    azure storage blob delete <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**Exempel på hur du använder Azure CLI för att lista filer**

    azure storage blob list <containername> <blobname|prefix> --account-name <storageaccountname> --account-key <storageaccountkey>

## <a name="use-additional-storage-accounts"></a>Använda ytterligare lagringskonton

När du skapar ett HDInsight-kluster kan du ange ett Azure Storage-konto som du vill koppla till det. Förutom det här lagringskontot kan du lägga till ytterligare lagringskonton från samma Azure-prenumeration eller andra Azure-prenumerationer under skapandeprocessen eller efter att ett kluster har skapats. Mer information om hur du lägger till ytterligare lagringskonton finns i [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]
> Du kan inte använda ett annat lagringskonto på en annan plats än HDInsight-klustret.

## <a name="next-steps"></a>Nästa steg
I den här artikeln fick du lära dig hur du använder det HDFS-kompatibla Azure Storage med HDInsight. Du kan skapa skalbara, långsiktiga lösningar för arkivering av insamlade data samt HDInsight för att få tillgång till informationen i lagrade strukturerade och ostrukturerade data.

Mer information finns i:

* [Kom igång med Azure HDInsight][hdinsight-get-started]
* [Kom igång med Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
* [Överföra data till HDInsight][hdinsight-upload-data]
* [Använda Hive med HDInsight][hdinsight-use-hive]
* [Använda Pig med HDInsight][hdinsight-use-pig]
* [Använda signaturer för delad åtkomst i Azure Storage för att begränsa åtkomsten till data med HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  

