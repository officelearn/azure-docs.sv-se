---
title: Använda Azure Data Lake Storage Gen2 förhandsversion med Azure HDInsight-kluster
description: Lär dig mer om att fråga efter data från Azure Data Lake Storage Gen2 förhandsversion och lagrar resultatet av dina analyser.
author: jamesbak
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.openlocfilehash: 0b171c7ed13eab84d84bb797e154a3acec8fbac7
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633688"
---
# <a name="use-azure-data-lake-storage-gen2-preview-with-azure-hdinsight-clusters"></a>Använda Azure Data Lake Storage Gen2 förhandsversion med Azure HDInsight-kluster

Om du vill analysera data i ett HDInsight-kluster kan kan du lagra data i valfri kombination av Azure Blob Storage, Azure Blob Storage med Azure Data Lake Storage Gen2 förhandsgranskningen är aktiverad eller Azure Data Lake Storage Gen1. Alla lagringsalternativ för kan du ta bort HDInsight-kluster som används för beräkning utan att förlora användardata.

Hadoop stöder begreppet standardfilsystem. Standardfilsystemet kräver att ett standardschema och en utfärdare används. Det kan också användas för att matcha relativa sökvägar. Du kan ange en blobbehållare i Azure Storage eller hierarkiskt namnområde som erbjuds av Data Lake Storage Gen2 som standardfilsystemet under skapandeprocessen för HDInsight-kluster. Du kan också med HDInsight 3.5 kan kan du välja en behållare eller hierarkiskt namnområde som standardfilsystem med några undantag.

I den här artikeln får du lära dig hur Data Lake Storage Gen2 fungerar med HDInsight-kluster. Mer information om hur du skapar ett HDInsight-kluster finns i [konfigurera HDInsight-kluster med Azure Data Lake Storage med Hadoop, Spark, Kafka med mera](data-lake-storage-quickstart-create-connect-hdi-cluster.md).

Azure Storage är en robust lagringslösning för allmänna ändamål som smidigt kan integreras med HDInsight. HDInsight kan använda Azure Data Lake Storage som standardfilsystem för klustret. Genom ett distribuerat filsystem (HDFS) gränssnitt för Hadoop tillämpas en fullständig uppsättning komponenter i HDInsight direkt på filer i Azure Data Lake Storage.

Vi rekommenderar inte att du använder standardfilsystemet för att lagra affärsdata. Tar bort standardfilsystemet efter varje användning för att minska kostnaden för lagring är en bra idé. Observera att standardbehållaren innehåller program- och loggar. Se till att hämta loggarna innan du tar bort containern.

Dela ett filsystem för flera kluster stöds inte.

## <a name="hdinsight-storage-architecture"></a>Lagringsarkitekturen i HDInsight

Följande diagram visar en abstrakt vy av lagringsarkitekturen i HDInsight med Azure Storage:

![Hadoop-kluster använder HDFS API för att komma åt och lagra strukturerade och ostrukturerade data i Blob Storage.](./media/data-lake-storage-use-hdi-cluster/HDI.ABFS.Arch.png "HDInsight Storage-arkitektur")

HDInsight ger tillgång till det distribuerade filsystemet som är lokalt anslutet till beräkningsnoderna. Detta filsystem kan nås med hjälp av den fullständigt kvalificerade URI-strängen, till exempel:

    hdfs://<NAME_NODE_HOST>/<PATH>

Dessutom får HDInsight du åtkomst till data som lagras i Azure Data Lake Storage. Syntax:

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<path>

Följande är några saker när du använder ett Azure Storage-konto med HDInsight-kluster.

* **Files i lagringskonton som är anslutna till ett kluster** har kontonamnet och nyckeln som associeras med klustret när du skapar. Den här konfigurationen ger dig fullständig åtkomst till filer i filsystemet.

* **Offentliga filer i storage-konton som inte är anslutna till ett kluster** exponera läsbehörighet till filer i filsystemet.
  
  > [!NOTE]
  > Offentliga filsystem kan du hämta en lista över alla filer som är tillgängliga i filsystemet och få åtkomst till metadata. Offentliga filsystem kan du komma åt filer endast om du vet den exakta Webbadressen. Mer information finns i [begränsa åtkomsten till behållare och blobbar](https://msdn.microsoft.com/library/windowsazure/dd179354.aspx) (regler för behållare och blobbar fungerar samma för filer och file system).
 
* **Privata filsystem i storage-konton som inte är anslutna till ett kluster** tillåter inte åtkomst till filer i filsystemet såvida du inte definierar lagringskontot när du skickar WebHCat-jobben. Orsaker till den här begränsningen beskrivs senare i den här artikeln.

De lagringskonton som definieras under skapandeprocessen och deras nycklar lagras i *%HADOOP_HOME%/conf/core-site.xml* på klusternoderna. Standardbeteendet för HDInsight är att använda de lagringskonton som definieras i den *core-site.xml* fil. Du kan ändra den här inställningen med [Ambari](../../hdinsight/hdinsight-hadoop-manage-ambari.md)

Flera WebHCat-jobb, inklusive Hive, MapReduce, Hadoop-strömning och Pig, kan ha med sig en beskrivning av lagringskonton och metadata. (Den här metoden för närvarande fungerar för Pig med lagringskonton, men inte för metadata.) Mer information finns i [Använda ett HDInsight-kluster med alternativa lagringskonton och metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

## <a id="benefits"></a>Fördelar med Azure Storage

Den medförande prestandakostnaden för att inte samplacera beräkningskluster och lagringsresurser minskar, på grund av sättet på vilket beräkningsklustren skapas nära lagringskontoresurserna i Azure-regionen, där höghastighetsnätverket gör att beräkningsnoderna effektivt kan komma åt data i Azure Storage.

Det finns flera fördelar med att lagra data i Azure Storage i stället för i HDFS:

* **Data återanvändning och delning av:** Data i HDFS lagras inuti beräkningsklustren. Endast de program som har åtkomst till beräkningsklustren kan använda dessa data med hjälp av HDFS-API:er. Data i Azure Storage kan antingen nås via HDFS-API:erna eller via [REST-API:erna för Blob Storage][blob-storage-restAPI]. Därmed kan en större grupp program (inklusive andra HDInsight-kluster) och verktyg användas för att skapa och använda data.

* **Dataarkivering:** Lagra data i Azure storage kan de HDInsight-kluster som används för beräkning tryggt tas bort utan att förlora användardata.

* **Kostnad för datalagring:** Lagra data i den interna HDFS långsiktigt är dyrare än att lagra data i Azure storage eftersom kostnaden för ett beräkningskluster är högre än kostnaden för Azure storage. Eftersom data inte behöver läsas in på nytt för varje generation av beräkningskluster sparar du dessutom kostnader för datainläsning.

* **Elastisk utskalning:** Även om HDFS ger dig ett utskalat filsystem, bestäms skalan av antalet noder som du skapar för klustret. Att ändra skala med HDFS kan vara en mer komplicerad process än att använda de elastiska skalningsfunktionerna som du automatiskt har tillgång till i Azure Storage.

* **GEO-replikering:** Azure storage-data kan vara geo-replikerade. Även om den här möjligheten ger dig geografisk återställning och dataredundans, stöder en växling till den geo-replikerade platsen kraftigt försämring av prestandan och kan resultera i ytterligare kostnader. Därför väljer geo-replikering noggrant och bara om värdet av data är den extra kostnaden.

* **Hanteringen av datalivscykeln:** Alla data i alla filsystem går igenom en egen livscykel. Data börjar ofta uppskattar och används ofta, övergår till att mindre värdefulla och kräver mindre åtkomst och slutligen kräver Arkiv eller tas bort. Azure Storage tillhandahåller data lagringsnivåer och livscykelhantering hanteringsprinciper som delar data på rätt sätt under dess livscykelfas.

Vissa MapReduce-jobb och -paket kan skapa mellanresultat som du inte egentligen vill lagra i Azure Storage. I så fall kan du välja att lagra data i ditt lokala HDFS. I själva verket använder HDInsight den interna HDFS-implementeringen (som kallas DFS) för flera av dessa mellanresultat i Hive-jobb och andra processer.

> [!NOTE]
> De flesta HDFS-kommandon (till exempel `ls`, `copyFromLocal` och `mkdir`) fortfarande fungerar som förväntat. Endast de kommandon som är specifika för DFS, till exempel `fschk` och `dfsadmin`, visa olika beteenden i Azure storage.

## <a name="create-a-data-lake-storage-file-system"></a>Skapa ett Data Lake Storage-filsystem

Om du vill använda filsystemet måste du först skapa en [Azure Storage-konto][azure-storage-create]. Som en del av den här processen kan ange du en Azure-region där lagringskontot skapas. Klustret och lagringskontot måste finnas i samma region. SQL Server-databasen för Hive metastore och SQL Server-databasen för Oozie metastore måste också finnas i samma region.

Oavsett var den finns tillhör varje blob som du skapar ett filsystem i ditt storage-konto.

Standardfilsystemet för Data Lake Storage Gen2 lagrar klusterspecifik information, till exempel jobbhistorik och loggar. Dela inte ett standard Data Lake Storage Gen2 filsystem med flera HDInsight-kluster. Detta kan skada jobbets historik. Det rekommenderas att använda ett annat filsystem för varje kluster och publicera delade data på ett länkat lagringskonto som anges vid distributionen av alla relevanta kluster snarare än standardkontot för lagring. Mer information om hur du konfigurerar länkade lagringskonton finns i [Skapa HDInsight-kluster][hdinsight-creation]. Du kan återanvända en standardfilsystemet för lagring när det ursprungliga HDInsight-klustret har tagits bort. HBase-kluster kan behålla du HBase-tabellschemat och data genom att skapa en ny HBase-kluster med hjälp av standardbehållaren som används av en borttagen HBase-kluster som har tagits bort.

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

När du skapar ett HDInsight-kluster från portalen kan behöva du alternativen (som visas i följande skärmbild) anger du kontouppgifterna för lagring. Du kan också ange om du vill att ett annat lagringskonto som är associerade med klustret och i så fall, Välj från någon av de tillgängliga lagringsalternativ för ytterligare lagringsutrymme.

![HDInsight hadoop, skapa datakälla](./media/data-lake-storage-use-hdi-cluster/create-storage-account.png)

> [!WARNING]
> Du kan inte använda ett annat lagringskonto på en annan plats än HDInsight-klustret.

### <a name="use-azure-powershell"></a>Använda Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du [installerat och konfigurerat Azure PowerShell][powershell-install], du kan använda följande kod från Azure PowerShell-prompten för att skapa ett lagringskonto och en behållare:

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

```azurepowershell
$SubscriptionID = "<Your Azure Subscription ID>"
$ResourceGroupName = "<New Azure Resource Group Name>"
$Location = "WEST US 2"

$StorageAccountName = "<New Azure Storage Account Name>"
$containerName = "<New Azure Blob Container Name>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $SubscriptionID

# Create resource group
New-AzResourceGroup -name $ResourceGroupName -Location $Location

# Create default storage account
New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
  -Name StorageAccountName `
  -Location $Location `
  -SkuName Standard_LRS `
  -Kind StorageV2 
  -HierarchialNamespace $True

# Create default blob containers
$storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
$destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
New-AzStorageContainer -Name $containerName -Context $destContext
```

> [!NOTE]
> Skapa en behållare är synonyma med att skapa ett filsystem i Data Lake Storage Gen2.

### <a name="use-azure-cli"></a>Använda Azure CLI

[!INCLUDE [use-latest-version](../../../includes/hdinsight-use-latest-cli.md)]

Om du har [installerat och konfigurerat Azure CLI](../../cli-install-nodejs.md) kan du använda följande kommando för att skapa ett lagringskonto och en container.

```bash
az storage account create \
    --name <STORAGE_ACCOUNT_NAME> \
    --resource-group <RESOURCE_GROUP_NAME> \
    --location westus2 \
    --sku Standard_LRS \
    --kind StorageV2 \
    --Enable-hierarchical-namespace true
```

> [!NOTE]
> Den offentliga förhandsversionen av Data Lake Storage Gen2 endast `--sku Standard_LRS` stöds.

Du kommer att bli ombedd att ange den geografiska region som lagringskontot kommer att finnas i. Skapa lagringskontot i samma region som du vill skapa ditt HDInsight-kluster.

När du har skapat lagringskontot använder du följande kommando för att hämta nycklar till lagringskontot:

    azure storage account keys list <STORAGE_ACCOUNT_NAME>

Om du vill skapa en container använder du följande kommando:

    azure storage container create <CONTAINER_NAME> --account-name <STORAGE_ACCOUNT_NAME> --account-key <STORAGE_ACCOUNT_KEY>

> [!NOTE]
> Skapa en behållare är synonyma med att skapa ett filsystem i Data Lake Storage Gen2.

## <a name="address-files-in-azure-storage"></a>Adressera filer i Azure Storage

Följande URI-schema används för att komma åt filer i Azure Storage från HDInsight:

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>

URI-schemat ger okrypterad åtkomst (med den *abfs:* prefix) och SSL-krypterad åtkomst (med *abfss*). Vi rekommenderar att du använder *abfss* möjligt, även om åtkomst till data som finns i samma region i Azure.

* &lt;FILE_SYSTEM_NAME&gt; identifierar sökvägen till filsystemet Data Lake Storage Gen2.
* &lt;ACCOUNT_NAME&gt; identifierar namnet på Azure Storage-kontot. Ett fullständigt kvalificerat domännamn (FQDN) krävs.

    Om värden för &lt;FILE_SYSTEM_NAME&gt; eller &lt;ACCOUNT_NAME&gt; har angetts används standardfilsystemet. För filer i filsystemet kan du använda en relativ sökväg eller en absolut sökväg. Till exempel den *hadoop-mapreduce-examples.jar* -fil som medföljer HDInsight-kluster kan du referera till något av följande sökvägar:
    
        abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
        abfs:///example/jars/hadoop-mapreduce-examples.jar
        /example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> Filnamnet är *hadoop examples.jar* i HDInsight-kluster av version 2.1 och 1.6.

* Den &lt;sökväg&gt; är det fil eller katalog HDFS-sökvägsnamnet.

> [!NOTE]
> När du arbetar med filer utanför HDInsight kan de flesta verktyg inte kan identifiera ABFS formatera och förväntar sig i stället ett grundläggande sökvägsformat som `example/jars/hadoop-mapreduce-examples.jar`.
 
## <a name="use-additional-storage-accounts"></a>Använda ytterligare lagringskonton

När du skapar ett HDInsight-kluster kan du ange ett Azure Storage-konto som du vill koppla det till. Förutom det här lagringskontot kan du lägga till ytterligare lagringskonton från samma Azure-prenumeration eller andra Azure-prenumerationer under skapandeprocessen eller efter att ett kluster har skapats. Mer information om hur du lägger till ytterligare lagringskonton finns i [Skapa HDInsight-kluster](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]
> Du kan inte använda ett annat lagringskonto på en annan plats än HDInsight-klustret.

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du lära dig hur du använder det HDFS-kompatibla Azure Storage med HDInsight. Den här metoden kan du skapa lösningar för skalbara, långsiktiga data samt HDInsight att få tillgång till informationen i lagrade strukturerade och Ostrukturerade data.

Mer information finns i:

* [Drivrutinen ABFS Hadoop-filsystem för Azure Data Lake Storage Gen2](data-lake-storage-abfs-driver.md)
* [Introduktion till Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
* [Ställ in HDInsight-kluster med hjälp av Azure Data Lake Storage Gen2 med Hadoop, Spark, Kafka med mera](data-lake-storage-quickstart-create-connect-hdi-cluster.md)
* [Mata in data till Azure Data Lake Storage Gen2 använda distcp](data-lake-storage-use-distcp.md)

[powershell-install]: /powershell/azure/install-az-ps
[hdinsight-creation]: ../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/data-lake-storage-use-hdi-cluster/HDI.PowerShell.BlobCommands.png
