---
title: Förhandsgranska Azure Data Lake lagring Gen2 med Azure HDInsight-kluster
description: Lär dig hur du frågar efter data från Azure Data Lake lagring Gen2 Preview och lagrar resultatet av dina analyser.
keywords: hdfs, strukturerade data, Ostrukturerade data, datasjölager, Hadoop indata, Hadoop utdata, hadoop lagring, hdfs indata, utdata för hdfs, hdfs lagring, wasb azure
services: hdinsight,storage
documentationcenter: ''
tags: azure-portal
author: jamesbak
manager: jahogg
ms.component: data-lake-storage-gen2
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.openlocfilehash: d5b67d971c2261084857d6b512c8d011173884af
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113263"
---
# <a name="use-azure-data-lake-storage-gen2-preview-with-azure-hdinsight-clusters"></a>Förhandsgranska Azure Data Lake lagring Gen2 med Azure HDInsight-kluster

För att analysera data i HDInsight-kluster, kan du lagra data i en kombination av Azure Storage, Azure Data Lake lagring Gen1 eller Azure Data Lake lagring Gen2 Preview. Alla alternativ för lagring kan du ta bort HDInsight-kluster som används för beräkning utan att förlora användardata.

Hadoop stöder begreppet standardfilsystem. Standardfilsystemet kräver att ett standardschema och en utfärdare används. Det kan också användas för att matcha relativa sökvägar. Under skapandeprocessen för HDInsight-kluster kan du ange en blob-behållare i Azure Storage eller Azure Data Lake Storage som standard-filsystem. Alternativt med HDInsight 3.5, kan du välja Azure Storage eller Azure Data Lake Storage som standardfilsystem med några få undantag.

I den här artikeln lär du dig hur Azure Data Lake lagring Gen2 fungerar med HDInsight-kluster. Mer information om hur du skapar ett HDInsight-kluster finns [konfigurera HDInsight-kluster med Azure Data Lake Storage med Hadoop, Spark, Kafka och mer](quickstart-create-connect-hdi-cluster.md).

Azure Storage är en robust lagringslösning för allmänna ändamål som smidigt kan integreras med HDInsight. HDInsight kan använda Azure Data Lake Storage som standardfilsystem för klustret. En fullständig uppsättning komponenter i HDInsight tillämpas direkt på filer i Azure Data Lake Storage via Hadoop distributed file system (HDFS) gränssnitt.

Vi rekommenderar inte att du använder filsystemet för att lagra företagets data. Ta bort filsystemet efter varje används för att minska lagringskostnaden för är bra. Observera att standardbehållaren innehåller program- och loggar. Se till att hämta loggarna innan du tar bort behållaren.

Dela en filsystem för flera kluster stöds inte.

## <a name="hdinsight-storage-architecture"></a>Lagringsarkitekturen i HDInsight

Följande diagram visar en abstrakt vy av lagringsarkitekturen i HDInsight med Azure Storage:

![Hadoop-kluster använder HDFS API för att komma åt och lagra strukturerade och ostrukturerade data i Blob Storage.](./media/use-hdi-cluster/HDI.ABFS.Arch.png "HDInsight Storage-arkitektur")

HDInsight ger tillgång till det distribuerade filsystemet som är lokalt anslutet till beräkningsnoderna. Detta filsystem kan nås med hjälp av den fullständigt kvalificerade URI-strängen, till exempel:

    hdfs://<namenodehost>/<path>

Dessutom får HDInsight du åtkomst till data som lagras i Azure Data Lake. Syntax:

    abfs[s]://<file_system>@<accountname>.dfs.core.windows.net/<path>

Här är några saker att tänka på när du använder ett Azure Storage-konto med HDInsight-kluster.

* **Filer på de lagringskonton som är anslutna till ett kluster** har kontonamnet och nyckeln som associeras med klustret när du skapar. Den här konfigurationen ger dig fullständig åtkomst till filer i filsystemet.

* **Offentliga filer på lagringskonton som inte är ansluten till ett kluster** exponera läsbehörighet till filer i filsystemet.
  
  > [!NOTE]
  > Offentliga filsystem kan du hämta en lista över alla filer som är tillgängliga i filsystemet och komma åt metadata. Offentliga filsystem kan du komma åt filer endast om du känner till en exakt Webbadress. Mer information finns i [begränsa åtkomsten till behållare och blobbar](http://msdn.microsoft.com/library/windowsazure/dd179354.aspx) (regler för behållare och blobbar fungerar för samma filer och file system).
 
* **Privata filsystem på lagringskonton som inte är ansluten till ett kluster** tillåter inte åtkomst till filer i filsystemet såvida du inte definierar lagringskontot när du skickar WebHCat-jobben. Orsaker till den här begränsningen beskrivs senare i den här artikeln.

De lagringskonton som definieras i processen och deras nycklar lagras i *%HADOOP_HOME%/conf/core-site.xml* på klusternoderna. Standardbeteendet för HDInsight är att använda de lagringskonton som definieras i den *core-site.xml* fil. Du kan ändra den här inställningen med [Ambari](/hdinsight/hdinsight-hadoop-manage-ambari.md)

Flera WebHCat-jobb, inklusive Hive, MapReduce, Hadoop-strömning och Pig, kan ha med sig en beskrivning av lagringskonton och metadata. (Den här metoden för närvarande fungerar för Pig med lagringskonton, men inte för metadata.) Mer information finns i [Använda ett HDInsight-kluster med alternativa lagringskonton och metastores](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

## <a id="benefits"></a>Fördelar med Azure Storage

Den medförande prestandakostnaden för att inte samplacera beräkningskluster och lagringsresurser minskar, på grund av sättet på vilket beräkningsklustren skapas nära lagringskontoresurserna i Azure-regionen, där höghastighetsnätverket gör att beräkningsnoderna effektivt kan komma åt data i Azure Storage.

Det finns flera fördelar med att lagra data i Azure Storage i stället för i HDFS:

* **Återanvändning och delning av data:** Data i HDFS lagras inuti beräkningsklustren. Endast de program som har åtkomst till beräkningsklustren kan använda dessa data med hjälp av HDFS-API:er. Data i Azure Storage kan antingen nås via HDFS-API:erna eller via [REST-API:erna för Blob Storage][blob-storage-restAPI]. Därmed kan en större grupp program (inklusive andra HDInsight-kluster) och verktyg användas för att skapa och använda data.

* **Dataarkivering:** Om data lagras i Azure Storage kan de HDInsight-kluster som används för beräkning tryggt tas bort utan att användardata går förlorade.

* **Kostnad för datalagring:** lagrar data i den interna HDFS för på lång sikt är dyrare än att lagra data i Azure storage eftersom kostnaden för ett beräkningskluster är högre än kostnaden för Azure storage. Eftersom data inte behöver läsas in på nytt för varje generation av beräkningskluster sparar du dessutom kostnader för datainläsning.

* **Elastisk utskalning:** Även om HDFS ger dig ett utskalat filsystem bestäms skalan av antalet noder som du skapar för klustret. Att ändra skala med HDFS kan vara en mer komplicerad process än att använda de elastiska skalningsfunktionerna som du automatiskt har tillgång till i Azure Storage.

* **GEO-replikering:** Azure storage-data kan vara georeplikerad. Även om den här möjligheten ger dig geografisk återställning och dataredundans, stöder en växling till den geo-replikerade platsen kraftigt försämring av prestandan och kan medföra ytterligare kostnader. Därför väljer geo-replikering noggrant och bara om värdet av data är motiverar den extra kostnaden.

* **Livscykelhantering för data:** alla data i alla filsystem genomgår en egen livscykel. Börjar ofta är mycket användbart och ofta använda data, övergår till att mindre värdefulla och kräver mindre åtkomst och slutligen kräver arkivering eller radering. Azure Storage tillhandahåller data skiktning och livscykel principer för hantering som tjänstnivån data på lämpligt sätt för dess steg i livscykeln.

Vissa MapReduce-jobb och -paket kan skapa mellanresultat som du inte egentligen vill lagra i Azure Storage. I så fall kan du välja att lagra data i ditt lokala HDFS. Faktum är att HDInsight använder interna HDFS-implementeringen (som kallas DFS) för flera av dessa mellanresultat i Hive-jobb och andra processer.

> [!NOTE]
> De flesta HDFS-kommandon (till exempel `ls`, `copyFromLocal` och `mkdir`) fungerar fortfarande som förväntat. Endast de kommandon som är specifika för Distribuerade som `fschk` och `dfsadmin`, visa olika beteenden i Azure-lagring.

## <a name="create-an-data-lake-storage-file-system"></a>Skapa ett filsystem för lagring av Data Lake

Om du vill använda filsystemet måste du först skapa en [Azure Storage-konto][azure-storage-create]. Som en del av den här processen kan ange du en Azure-region där lagringskontot har skapats. Klustret och lagringskontot måste finnas i samma region. SQL Server-databasen för Hive metastore och SQL Server-databasen för Oozie metastore måste också finnas i samma region.

Oavsett var den finns tillhör varje blob som du skapar ett filsystem i ditt Azure Data Lake Storage-konto. 

Standardfilsystemet för lagring av Data Lake lagrar klusterspecifika information, till exempel jobbhistorik och loggar. Inte dela en standardfilsystem för lagring av Data Lake med flera HDInsight-kluster. Detta kan skada jobbets historik. Det rekommenderas att använda ett annat filsystem för varje kluster och publicera delade data på ett konto för länkad lagring som anges i distribution av alla relevanta kluster snarare än standardkontot för lagring. Mer information om hur du konfigurerar länkade lagringskonton finns i [Skapa HDInsight-kluster][hdinsight-creation]. Du kan återanvända en fil lagringssystemet när det ursprungliga HDInsight-klustret har tagits bort. För HBase-kluster, kan du behålla HBase-tabellschemat och data genom att skapa en ny HBase-kluster med hjälp av standardbehållaren som används av en borttagen HBase-kluster som har tagits bort.

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

När du skapar ett HDInsight-kluster från portalen kan behöva du alternativen (som visas i följande skärmbild) kontoinformationen för lagring. Du kan också ange om du vill att en ytterligare storage-konto som är associerade med klustret och i så fall, Välj någon av de tillgängliga lagringsalternativ för ytterligare lagringsutrymme.

![HDInsight hadoop, skapa datakälla](./media/use-hdi-cluster/create-storage-account.png)

> [!WARNING]
> Du kan inte använda ett annat lagringskonto på en annan plats än HDInsight-klustret.

### <a name="use-azure-powershell"></a>Använda Azure PowerShell

Om du [installerat och konfigurerat Azure PowerShell][powershell-install], du kan använda följande kod från Azure PowerShell-Kommandotolken för att skapa ett lagringskonto och en behållare:

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "WEST US 2"

    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"

    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID

    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location

    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
      -Name StorageAccountName `
      -Location $Location `
      -SkuName Standard_LRS `
      -Kind StorageV2 
      -HierarchialNamespace $True

    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

> [!NOTE]
> Skapa en behållare är synonymt med att skapa ett filsystem i Azure Data Lake Storage.

### <a name="use-azure-cli"></a>Använda Azure CLI

[!INCLUDE [use-latest-version](../../../includes/hdinsight-use-latest-cli.md)]

Om du har [installerat och konfigurerat Azure CLI](../../cli-install-nodejs.md) kan du använda följande kommando för att skapa ett lagringskonto och en behållare.

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
> Under förhandsversion av Data Lake lagring Gen2 endast `--sku Standard_LRS` stöds.

Du kommer att bli ombedd att ange den geografiska region som lagringskontot kommer att finnas i. Skapa lagringskontot i samma region som du planerar att skapa ditt HDInsight-kluster.

När du har skapat lagringskontot använder du följande kommando för att hämta nycklar till lagringskontot:

    azure storage account keys list <STORAGE_ACCOUNT_NAME>

Om du vill skapa en behållare använder du följande kommando:

    azure storage container create <CONTAINER_NAME> --account-name <STORAGE_ACCOUNT_NAME> --account-key <STORAGE_ACCOUNT_KEY>

> [!NOTE]
> Skapa en behållare är synonymt med att skapa ett filsystem i Azure Data Lake Storage.

## <a name="address-files-in-azure-storage"></a>Adressera filer i Azure Storage

Följande URI-schema används för att komma åt filer i Azure Storage från HDInsight:

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.widows.net/<PATH>

URI-schemat ger okrypterad åtkomst (med den *abfs:* prefix) och SSL-krypterad åtkomst (med *abfss*). Vi rekommenderar att du använder *abfss* där det är möjligt, även för åtkomst till data som finns i samma region i Azure.

* &lt;FILE_SYSTEM_NAME&gt; identifierar sökvägen för lagring av Azure Data Lake i filsystemet.
* &lt;Kontonamn&gt; identifierar namnet på Azure Storage-kontot. Ett fullständigt kvalificerat domännamn (FQDN) krävs.

    Om värden för &lt;FILE_SYSTEM_NAME&gt; eller &lt;kontonamn&gt; har angetts används standardfilsystemet. För filer i filsystemet kan du använda en relativ sökväg eller en absolut sökväg. Till exempel den *hadoop-mapreduce-examples.jar* fil som medföljer HDInsight-kluster kan refereras till genom att använda någon av följande sökvägar:
    
        abfs://myfilesystempath@myaccount.dfs.core.widows.net/example/jars/hadoop-mapreduce-examples.jar
        abfs:///example/jars/hadoop-mapreduce-examples.jar
        /example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> Filnamnet är *hadoop examples.jar* i HDInsight-kluster av version 2.1 och 1.6.

* Den &lt;sökväg&gt; är det fil eller katalog HDFS-sökvägsnamnet.

> [!NOTE]
> När du arbetar med filer utanför HDInsight kan de flesta verktyg inte känner igen ABFS formatera och förväntar sig i stället ett grundläggande sökvägsformat som `example/jars/hadoop-mapreduce-examples.jar`.
 
## <a name="use-additional-storage-accounts"></a>Använda ytterligare lagringskonton

När du skapar ett HDInsight-kluster kan du ange ett Azure Storage-konto som du vill koppla det till. Förutom det här lagringskontot kan du lägga till ytterligare lagringskonton från samma Azure-prenumeration eller andra Azure-prenumerationer under skapandeprocessen eller efter att ett kluster har skapats. Mer information om hur du lägger till ytterligare lagringskonton finns i [Skapa HDInsight-kluster](/hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]
> Du kan inte använda ett annat lagringskonto på en annan plats än HDInsight-klustret.

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du lära dig hur du använder det HDFS-kompatibla Azure Storage med HDInsight. Den här metoden kan du skapa skalbara, långsiktiga lösningar för datainsamling och använda HDInsight för att låsa upp informationen i lagrade strukturerade och Ostrukturerade data.

Mer information finns i:

* [Drivrutinen för Azure Data Lake lagring Gen2 ABFS Hadoop-filsystem](abfs-driver.md)
* [Introduktion till Azure Data Lake Storage](introduction.md)
* [Ställ in HDInsight-kluster med Azure Data Lake Storage med Hadoop, Spark, Kafka och mycket mer](quickstart-create-connect-hdi-cluster.md)
* [Mata in data i Azure Data Lake lagring med hjälp av distcp](use-distcp.md)

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: /hdinsight/hdinsight-hadoop-provision-linux-clusters.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: /storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/use-hdi-cluster/HDI.PowerShell.BlobCommands.png
