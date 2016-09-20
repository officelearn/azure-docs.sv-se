<properties
    pageTitle="Fråga efter data i HDFS-kompatibla Blob Storage | Microsoft Azure"
    description="HDInsight använder Azure Blob Storage som stordataarkiv för HDFS. Lär dig mer om hur du frågar efter data från Blob Storage och lagrar resultatet av dina analyser."
    keywords="blob-lagring, hdf, strukturerade data, ostrukturerade data"
    services="hdinsight,storage"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/10/2016"
    ms.author="jgao"/>


# Använd HDFS-kompatibla Azure Blob Storage med Hadoop i HDInsight

Lär dig använda det prisvärda Azure Blob Storage med HDInsight, skapa ett Azure Storage-konto och en behållare för Blob Storage och hantera sedan de data som ingår.

Azure Blob Storage är en robust lagringslösning för allmänna ändamål som smidigt kan integreras med HDInsight. Genom ett gränssnitt för Hadoop-distribuerat filsystem (HDFS) kan alla komponenter i HDInsight tillämpas direkt på strukturerade eller ostrukturerade data i Blob Storage.

Om du lagrar data i Blob Storage kan du ta bort HDInsight-kluster som används för beräkning utan att förlora användardata.

> [AZURE.IMPORTANT] HDInsight stöder endast blockblobar. Det stöder inte sid- eller tilläggsblobar.

Information om hur du skapar ett HDInsight-kluster finns i [Komma igång med HDInsight][hdinsight-get-started] eller [Skapa HDInsight-kluster][hdinsight-creation].


## Lagringsarkitekturen i HDInsight
Följande diagram visar en abstrakt vy av lagringsarkitekturen i HDInsight:

![Hadoop-kluster använder HDFS API för att komma åt och lagra strukturerade och ostrukturerade data i Blob Storage.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "HDInsight Storage Architecture")

HDInsight ger tillgång till det distribuerade filsystemet som är lokalt anslutet till beräkningsnoderna. Detta filsystem kan nås med hjälp av den fullständigt kvalificerade URI-strängen, till exempel:

    hdfs://<namenodehost>/<path>

Dessutom ger HDInsight möjlighet att komma åt data som är lagrade i Azure Blob Storage. Syntax:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

> [AZURE.NOTE] I tidigare versioner av HDInsight än 3.0 användes `asv://` i stället för `wasb://`. `asv://` ska inte användas med HDInsight-kluster 3.0 eller senare, eftersom det kommer att orsaka fel.

Hadoop stöder begreppet standardfilsystem. Standardfilsystemet kräver att ett standardschema och en utfärdare används. Det kan också användas för att matcha relativa sökvägar. Under processen med att skapa HDInsight utses ett Azure Storage-konto och en specifik Azure Blob Storage-behållare från detta konto till standardfilsystem.

Förutom det här lagringskontot kan du lägga till ytterligare lagringskonton från samma Azure-prenumeration eller andra Azure-prenumerationer under skapandeprocessen. Mer information om hur du lägger till ytterligare lagringskonton finns i [Skapa HDInsight-kluster][hdinsight-creation].

- **Behållare på de lagringskonton som är anslutna till ett kluster:** Eftersom kontonamnet och nyckeln associeras med klustret när det skapas har du full tillgång till blobarna i dessa behållare.

- **Offentliga behållare eller offentliga blobar på lagringskonton som INTE är anslutna till något kluster:** Du har läsbehörighet till blobarna i dessa behållare.

    > [AZURE.NOTE]
        > Offentliga behållare låter dig hämta en lista över alla blobar som är tillgängliga i behållaren samt hämta metadata för behållaren. Du kan endast komma åt offentliga blobar om du känner till den exakta webbadressen. Mer information finns i <a href="http://msdn.microsoft.com/library/windowsazure/dd179354.aspx">Begränsa åtkomsten till behållare och blobar</a>.

- **Privata behållare på lagringskonton som INTE är anslutna till något kluster:** Du kan inte komma åt blobarna i behållarna om du inte definierar lagringskontot när du skickar WebHCat-jobben. Detta beskrivs senare i artikeln.


De lagringskonton som definieras under skapandeprocessen och deras nycklar lagras i %HADOOP_HOME%/conf/core-site.xml i klusternoderna. Standardbeteendet för HDInsight är att använda de lagringskonton som definieras i filen core-site.xml. Vi rekommenderar inte att du redigerar filen core-site.xml eftersom klustrets huvudnod (master) kan bli föremål för en ny avbildning eller migreras när som helst, och då kommer alla ändringar i denna fil att gå förlorade.

Flera WebHCat-jobb, inklusive Hive, MapReduce, Hadoop-strömning och Pig, kan ha med sig en beskrivning av lagringskonton och metadata. (För närvarande fungerar för Pig med lagringskonton, men inte för metadata.) I avsnittet [Komma åt blobar med hjälp av Azure PowerShell](#powershell) i den här artikeln finns det ett exempel på den här funktionen. Mer information finns i [Använda ett HDInsight-kluster med alternativa lagringskonton och metastores](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Blob Storage kan användas för strukturerade och ostrukturerade data. I behållare för Blob Storage förvaras data som nyckel/värde-par och det finns ingen kataloghierarki. Däremot kan snedstreck (/) användas i nyckelnamnet så att det visas som om det vore en fil som lagrats i en katalogstruktur. Nyckeln för en blob kan till exempel vara *input/log1.txt*. Det finns ingen faktisk katalog för *indata*, men eftersom det finns ett snedstreck i nyckelnamnet ser namnet ut som en filsökväg.

###<a id="benefits"></a>Fördelar med Blob Storage
Den implicerade prestandakostnaden för att inte samplacera beräkningsklustren och lagringsresurserna minskar på grund av sättet på vilket beräkningsklustren skapas nära lagringskontoresurserna i Azure-regionen där höghastighetsnätverket gör att beräkningsnoderna kan komma åt data i Azure Blob Storage på ett mycket effektivt sätt.

Det finns flera fördelar med att lagra data i Azure Blob Storage i stället för i HDFS:

* **Återanvändning och delning av data:** Data i HDFS lagras inuti beräkningsklustren. Endast de program som har åtkomst till beräkningsklustren kan använda dessa data med hjälp av HDFS-API:er. Data i Azure Blob Storage kan antingen nås via HDFS-API:erna eller via [REST API för Blob Storage][blob-storage-restAPI]. Därmed kan en större grupp program (inklusive andra HDInsight-kluster) och verktyg användas för att skapa och använda data.
* **Dataarkivering:** Om data lagras i Azure Blob Storage kan de HDInsight-kluster som används för beräkning tryggt tas bort utan att användardata går förlorade.
* **Kostnad för datalagring:** Att lagra data långsiktigt i DFS är dyrare än att lagra data i Azure Blob Storage eftersom kostnaden för ett beräkningskluster är högre än kostnaden för en Azure Blob Storage-behållare. Eftersom data inte behöver läsas in på nytt för varje generation av beräkningskluster sparar du dessutom kostnader för datainläsning.
* **Elastisk utskalning:** Även om HDFS ger dig ett utskalat filsystem bestäms skalan av antalet noder som du skapar för klustret. Att ändra skala med HDFS kan vara en mer komplicerad process än att använda de elastiska skalningsfunktionerna som du automatiskt har tillgång till i Azure Blob Storage.
* **Geo-replikering:** Dina Azure Blob Storage-behållare kan geo-replikeras. Även om detta ger dig geografisk återställning och dataredundans, innebär en växling till den geo-replikerade platsen en avsevärd försämring av prestandan och kan medföra ytterligare kostnader. Vår rekommendation är därför att tänka efter när du väljer geo-replikering och bara göra det om värdet i informationen motiverar den extra kostnaden.

Vissa MapReduce-jobb och -paket kan skapa mellanresultat som du inte egentligen vill lagra i Azure Blob Storage. I så fall kan du välja att lagra data i ditt lokala HDFS. Faktum är att HDInsight använder DFS för flera av dessa mellanresultat i Hive-jobb och andra processer.

> [AZURE.NOTE] De flesta HDFS-kommandon (till exempel <b>ls</b>, <b>copyFromLocal</b> och <b>mkdir</b>) fungerar fortfarande som förväntat. Endast de kommandon som är specifika för den interna HDFS-implementeringen (så kallade DFS-kommandon) som <b>fschk</b> och <b>dfsadmin</b> har ett avvikande beteende i Azure Blob Storage.

## Skapa blob-behållare

För att använda blobar måste du först skapa ett [Azure Storage-konto][azure-storage-create]. Som en del av detta kan du ange en Azure-region där objekten som du skapar med det här kontot kommer att lagras. Klustret och lagringskontot måste finnas i samma region. SQL Server-databasen för Hive metastore och SQL Server-databasen för Oozie metastore måste också finnas i samma region.

Oavsett var den finns tillhör varje blob som du skapar en behållare på ditt Azure Storage-konto. Den här behållaren kan vara en befintlig blob som skapats utanför HDInsight eller en behållare som skapats för ett HDInsight-kluster.


Standardbehållaren lagrar klusterspecifik information, till exempel jobbhistorik och loggar. Låt inte flera HDInsight-kluster dela en standardblob-behållare. Detta kan förstöra jobbhistoriken och klustret kommer inte att fungera som det ska. Du rekommenderas att använda en annan behållare för varje kluster och publicera delade data på ett konto för länkad lagring som anges vid distributionen av alla relevanta kluster snarare än på standardkontot för lagring. Mer information om hur du konfigurerar länkade lagringskonton finns i [Skapa HDInsight-kluster][hdinsight-creation]. Du kan emellertid återanvända en standardbehållare för lagring när det ursprungliga HDInsight-klustret har tagits bort. När det gäller HBase-kluster kan du faktiskt behålla HBase-tabellschemat och data genom att skapa en ny Blob Storage som standard som använts av ett HBase-kluster som har tagits bort.


### Använda Azure-portalen

När du skapar ett HDInsight-kluster från portalen kan du använda ett befintligt lagringskonto eller skapa ett nytt lagringskonto:

![hdinsight hadoop, skapa datakälla](./media/hdinsight-hadoop-use-blob-storage/hdinsight.provision.data.source.png)

###Använda Azure CLI

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

Om du har [installerat och konfigurerat Azure CLI](../xplat-cli-install.md) kan du använda följande kommando för att skapa ett lagringskonto och en behållare.

    azure storage account create <storageaccountname> --type LRS

> [AZURE.NOTE] Parametern `--type` anger hur lagringskontot kommer att replikeras. Mer information finns i [Azure Storage-replikering](../storage/storage-redundancy.md). Använd inte ZRS eftersom ZRS inte stöder sidblobar, filer, tabeller eller köer.

Du kommer att bli ombedd att ange den geografiska region som lagringskontot kommer att finnas i. Du bör skapa lagringskontot i den region där du planerar att skapa ditt HDInsight-kluster.

När du har skapat lagringskontot använder du följande kommando för att hämta nycklar till lagringskontot:

    azure storage account keys list <storageaccountname>

Om du vill skapa en behållare använder du följande kommando:

    azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>

### Använda Azure PowerShell

Om du har [installerat och konfigurerat Azure PowerShell][powershell-install] kan du använda följande från Azure PowerShell-prompten för att skapa ett lagringskonto och en behållare:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

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

## Adressera filer i Blob Storage

Följande URI-schema används för att komma åt filer i Blob Storage från HDInsight:

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


URI-schemat ger okrypterad åtkomst (med prefixet *wasb:*) och SSL-krypterad åtkomst (med *wasbs*). Vi rekommenderar att du använder *wasbs* när det är möjligt, även för åtkomst till data som finns i samma region i Azure.

&lt;BlobStorageContainerName&gt; identifierar namnet på behållaren i Azure Blob Storage.
&lt;StorageAccountName&gt; identifierar namnet på Azure Storage-kontot. Ett fullständigt kvalificerat domännamn (FQDN) krävs.

Om varken &lt;BlobStorageContainerName&gt; eller &lt;StorageAccountName&gt; har angetts används standardfilsystemet. För filer i filsystemet kan du använda en relativ sökväg eller en absolut sökväg. Till exempel kan du referera till den *hadoop-mapreduce-examples.jar*-fil som medföljer HDInsight-kluster på något av följande sätt:

    wasbs://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasbs:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE] Filnamnet är <i>hadoop examples.jar</i> i HDInsight-kluster av version 2.1 och 1.6.


&lt;Sökvägen&gt; är filens eller katalogens HDFS-sökvägsnamn. Eftersom behållare i Azure Blob Storage helt enkelt är lagringsplatser för nyckel-/värdepar finns det inte något faktiskt hierarkiskt filsystem. Ett snedstreck (/) i en blob-nyckel tolkas som en katalogavgränsare. Blob-namnet för *hadoop-mapreduce-examples.jar* är till exempel:

    example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE] När du arbetar med blobar utanför HDInsight kan de flesta verktyg inte identifiera WASB-formatet utan förväntar sig i stället ett grundläggande sökvägsformat som `example/jars/hadoop-mapreduce-examples.jar`.

## Komma åt blobar med hjälp av Azure CLI

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

## Komma åt blobar med hjälp av Azure Powershell

> [AZURE.NOTE] Kommandona i det här avsnittet ger grundläggande exempel på hur du använder PowerShell för att komma åt data som är lagrade i blobar. Om du vill ha ett mer komplett exempel som är anpassat för att arbeta med HDInsight, se [HDInsight Tools](https://github.com/Blackmist/hdinsight-tools).

Använd följande kommando för att visa en lista över blob-relaterade cmdlets:

    Get-Command *blob*

![Lista över blob-relaterade PowerShell-cmdlets.][img-hdi-powershell-blobcommands]

###Överföra filer

Se [Överföra data till HDInsight][hdinsight-upload-data].

###Hämta filer

Följande skript hämtar en block-blob till den aktuella mappen. Innan du kör skriptet ändrar du katalogen till en mapp där du har skrivbehörighet.

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

###Ta bort filer


    Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob

###Lista filer

    Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix "example/data/"

###Köra Hive-frågor med ett odefinierat lagringskonto

Det här exemplet visar hur du listar en mapp från lagringskontot som inte har definierats under skapandeprocessen.
$clusterName = "<HDInsightClusterName>"

    $undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
    $undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

    $undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

    Use-AzureRmHDInsightCluster $clusterName

    $defines = @{}
    $defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

    Invoke-AzureRmHDInsightHiveJob -Defines $defines -Query "dfs -ls wasbs://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"

## Nästa steg

I den här artikeln har du lärt dig hur du använder HDFS-kompatibla Azure Blob Storage med HDInsight och att Azure Blob Storage är en grundläggande komponent i HDInsight. Du kan skapa skalbara, långsiktiga lösningar för datainsamling med Azure Blob Storage och använda HDInsight för att få tillgång till informationen i lagrade strukturerade och ostrukturerade data.

Mer information finns i:

* [Komma igång med Azure HDInsight][hdinsight-get-started]
* [Överföra data till HDInsight][hdinsight-upload-data].
* [Använda Hive med HDInsight][hdinsight-use-hive]
* [Använda Pig med HDInsight][hdinsight-use-pig]
* [Använda signaturer för delad åtkomst i Azure för att begränsa åtkomsten till data med HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: ../powershell-install-configure.md
[hdinsight-creation]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  



<!--HONumber=sep16_HO1-->


