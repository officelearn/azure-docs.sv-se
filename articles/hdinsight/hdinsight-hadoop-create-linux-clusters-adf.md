---
title: "Skapa på begäran Hadoop-kluster med hjälp av Data Factory - Azure HDInsight | Microsoft Docs"
description: "Lär dig hur du skapar på begäran Hadoop-kluster i HDInsight med hjälp av Azure Data Factory."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: spelluru
manager: jhubbard
editor: cgronlun
ms.assetid: 1f3b3a78-4d16-4d99-ba6e-06f7bb185d6a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: spelluru
ms.openlocfilehash: b9b73f6691af957e42236ef9a223411a0296f96f
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="create-on-demand-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Skapa på begäran Hadoop-kluster i HDInsight med hjälp av Azure Data Factory
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

[Azure Data Factory](../data-factory/introduction.md) är en molnbaserad integration datatjänst som samordnar och automatiserar flytt och transformering av data. Det kan skapa en HDInsight Hadoop-kluster just-in-time för att bearbeta en inkommande datasektorn och tar bort klustret när bearbetningen är klar. Några av fördelarna med att använda ett HDInsight Hadoop-kluster på begäran är:

- Du endast betala för tid jobbet körs på HDInsight Hadoop-kluster (plus en kort konfigurerbara inaktivitetstid). Fakturering för HDInsight-kluster sker proportionerligt per minut, oavsett om du använder dem eller inte. När du använder en på-begäran länkad HDInsight-tjänst i Data Factory skapas kluster på begäran. Och kluster tas bort automatiskt när jobben har slutförts. Därför kan betalar du bara för jobbet kör tid och kort ledig tid (time to live-inställning).
- Du kan skapa ett arbetsflöde med Data Factory-pipelinen. Du kan till exempel ha rörledning för att kopiera data från en lokal SQL Server till ett Azure blob storage, bearbeta data genom att köra en Hive-skript och Pig-skriptet på en HDInsight Hadoop-kluster på begäran. Kopiera sedan Resultatdata till en Azure SQL Data Warehouse BI program ska använda.
- Du kan schemalägga arbetsflödet ska köras regelbundet (varje timme, varje dag, vecka, månad, etc.).

En datafabrik kan ha en eller flera data pipelines i Azure Data Factory. En data-pipeline har en eller flera aktiviteter. Det finns två typer av aktiviteter: [Data Movement aktiviteter](../data-factory/copy-activity-overview.md) och [Data Transformation aktiviteter](../data-factory/transform-data.md). Du kan använda data movement aktiviteter (för närvarande endast Kopieringsaktivitet) för att flytta data från ett dataarkiv som källa till ett dataarkiv som mål. Du kan använda data transformation aktiviteter för att transformera/bearbeta data. HDInsight Hive aktivitet är en omvandling av aktiviteter som stöds av Data Factory. Du använder Hive omvandling aktiviteten i den här kursen.

Du kan konfigurera en hive-aktivitet för att använda ditt eget HDInsight Hadoop-kluster eller en HDInsight Hadoop-kluster på begäran. I de här självstudierna har Hive-aktiviteten i data factory-pipelinen konfigurerats för att använda ett HDInsight-kluster på begäran. Därför när aktiviteten körs för att bearbeta en datasektorn är här vad som händer:

1. Ett HDInsight Hadoop-kluster skapas automatiskt för du just-in-time bearbeta sektorn.  
2. Indata bearbetas av en HiveQL-skript körs i klustret.
3. HDInsight Hadoop-kluster tas bort när bearbetningen är klar och klustret är inaktiv under den konfigurerade tidsperiod (timeToLive-inställning). Om nästa datasektorn är tillgängliga för bearbetning med i den här timeToLive väntetid används samma kluster att bearbeta sektorn.  

I den här självstudiekursen utför HiveQL-skript som är associerade med hive aktiviteten följande åtgärder:

1. Skapar en extern tabell som refererar till rå web logga data som lagras i ett Azure Blob storage.
2. Partitioner rådata per år och månad.
3. Lagrar den partitionerade data i Azure blob storage.

I den här självstudiekursen skapar HiveQL-skript som är associerad med aktiviteten hive en extern tabell som refererar till rå web logga data som lagras i Azure Blob Storage. Här följer exempel rader för varje månad i indatafilen.

```
2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

HiveQL-skript partitionerar rådata per år och månad. Den skapar tre utdatamappar baserat på tidigare indata. Varje mapp innehåller en fil med poster från varje månad.

```
adfgetstarted/partitioneddata/year=2014/month=1/000000_0
adfgetstarted/partitioneddata/year=2014/month=2/000000_0
adfgetstarted/partitioneddata/year=2014/month=3/000000_0
```

En lista över Data Factory data transformation aktiviteter utöver Hive aktivitet finns [transformera och analysera med hjälp av Azure Data Factory](../data-factory/transform-data.md).

> [!NOTE]
> För närvarande kan du bara skapa HDInsight-kluster av version 3.2 från Azure Data Factory.

## <a name="prerequisites"></a>Krav
Innan du börjar med instruktionerna i den här artikeln måste du ha följande:

* [Azure-prenumeration](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell.

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="prepare-storage-account"></a>Förbereda storage-konto
Du kan använda upp till tre storage-konton i det här scenariot:

- standardkontot för lagring för HDInsight-kluster
- lagringskontot för indata
- lagringskontot för utdata

För att förenkla kursen, använder du ett lagringskonto för att de tre syften. Azure PowerShell-exempelskript finns i det här avsnittet utför följande uppgifter:

1. Logga in på Azure.
2. Skapa en Azure-resursgrupp.
3. Skapa ett Azure Storage-konto.
4. Skapa en blobbbehållare i storage-konto
5. Kopiera följande två filer till Blob-behållare:

   * Indatafilen: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
   * HiveQL-skript: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

     Filer som lagras i en offentlig Blob-behållare.


**Förbered lagring och kopiera filerna med hjälp av Azure PowerShell:**
> [!IMPORTANT]
> Ange namn för Azure-resursgrupp och Azure storage-konto som skapas av skriptet.
> Skriv ned **resursgruppnamn**, **lagringskontonamnet**, och **lagringskontonyckel** utdata av skriptet. Du behöver dem i nästa avsnitt.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfhiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Login-AzureRmAccount}
#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -type Standard_LRS `
    -Location $location

$destStorageAccountKey = (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzureStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous
$destContext = New-AzureStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzureStorageContainer -Name $destContainerName -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzureStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName

$blobs|Start-AzureStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzureStorageBlob -Context $destContext -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

Om du behöver hjälp med PowerShell-skript finns [med hjälp av Azure PowerShell med Azure Storage](../storage/common/storage-powershell-guide-full.md). Om du vill använda Azure CLI i stället se den [bilaga](#appendix) avsnittet för Azure CLI-skript.

**Granska lagringskontot och innehållet**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **resursgrupper** i det vänstra fönstret.
3. Dubbelklicka på resursgruppens namn du skapade i PowerShell-skript. Med filtret om du har för många resursgrupper i listan.
4. På den **resurser** sida vid sida, har du en resurs i listan om du delar resursgruppen med andra projekt. Den här resursen är lagringskontot med det namn du angav tidigare. Klicka på namnet på lagringskontot.
5. Klicka på den **Blobbar** paneler.
6. Klicka på den **adfgetstarted** behållare. Du ser två mappar: **inputdata** och **skriptet**.
7. Öppna mappen och kontrollera filer i mappar. Inputdata innehåller input.log filen med indata och mappen skript innehåller filen HiveQL-skript.

## <a name="create-a-data-factory-using-resource-manager-template"></a>Skapa en datafabrik med hjälp av Resource Manager-mall
När lagringskontot indata och HiveQL-skript som har förberetts, är du redo att skapa ett Azure data factory. Det finns flera metoder för att skapa datafabriken. I kursen får skapa du en datafabrik genom att distribuera en Azure Resource Manager-mallen med hjälp av Azure-portalen. Du kan också distribuera en Resource Manager-mall med hjälp av [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) och [Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md#deploy-local-template). Andra data factory-metoder, se [Självstudier: skapa din första data factory](../data-factory/quickstart-create-data-factory-dot-net.md).

1. Klicka på följande bild för att logga in på Azure och öppna Resource Manager-mallen i Azure Portal. Mallen finns i https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json. Finns det [Data Factory-entiteter i mallen](#data-factory-entities-in-the-template) finns detaljerad information om enheter som definierats i mallen. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-adf/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Välj **Använd befintliga** för den **resursgruppen** inställningen och välj namnet på resursgruppen som du skapade i föregående steg (med PowerShell-skript).
3. Ange ett namn för data factory (**Datafabriksnamnet**). Det här namnet måste vara globalt unika.
4. Ange den **lagringskontonamnet** och **lagringskontonyckel** du noterade i föregående steg.
5. Välj **jag samtycker till villkoren** anges ovan när du har läst **villkor**.
6. Välj **fäst på instrumentpanelen** alternativet.
6. Klicka på **inköp/skapa**. Du ser en panel på instrumentpanelen kallas **skicka malldistribution**. Vänta tills den **resursgruppen** öppnas bladet för resursgruppen. Du kan också klicka på panelen med titeln som din resursgruppens namn att öppna bladet för resursgruppen.
6. Klicka på panelen om du vill öppna resursgruppen om bladet för resursgruppen inte är redan öppen. Du bör nu se en mer data factory-resurs visas förutom kontot lagringsresurs.
7. Klicka på namnet på din data factory (värde som du angav för den **Datafabriksnamnet** parametern).
8. I bladet Data Factory klickar du på den **Diagram** panelen. Diagrammet visar en aktivitet med en inkommande datauppsättning och en datamängd för utdata:

    ![Azure Data Factory HDInsight på begäran Hive aktivitet pipeline diagram](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)

    Namn har definierats i Resource Manager-mallen.
9. Dubbelklicka på **AzureBlobOutput**.
10. På den **senaste uppdaterade segment**, visas ett segment. Om statusen är **pågår**, vänta tills den har ändrats till **klar**. Det tar vanligtvis om **20 minuter** att skapa ett HDInsight-kluster.

### <a name="check-the-data-factory-output"></a>Kontrollera data factory-utdata

1. Använd samma procedur på den senaste sessionen för att söka behållaren adfgetstarted behållare. Det finns två nya behållare förutom **adfgetsarted**:

   * En behållare med namn som följer mönstret: `adf<yourdatafactoryname>-linkedservicename-datetimestamp`. Den här behållaren är standardbehållaren för HDInsight-kluster.
   * adfjobs: den här behållaren är behållare för ADF jobbloggar.

     Data factory-utdata är lagrad i **afgetstarted** som du konfigurerade i Resource Manager-mallen.
2. Klicka på **adfgetstarted**.
3. Dubbelklicka på **partitioneddata**. Du ser en **år 2014 =** mappen eftersom alla webbloggar funktionsdokumentationen år 2014.

    ![Azure Data Factory HDInsight på begäran Hive aktivitet pipeline utdata](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    Om du detaljnivån i listan visas tre mappar för januari, februari och mars. Och det finns en logg för varje månad.

    ![Azure Data Factory HDInsight på begäran Hive aktivitet pipeline utdata](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

## <a name="data-factory-entities-in-the-template"></a>Data Factory-entiteter i mallen
Här är att den översta Resource Manager-mallen för en datafabrik som ser ut som:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```

### <a name="define-data-factory"></a>Definiera en datafabrik
Du definierar en datafabrik i Resource Manager-mallen enligt följande exempel:  

```json
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
}
```
DataFactoryName är namnet på datafabriken som du anger när du distribuerar mallen. Datafabriken är för närvarande stöds endast i östra USA, västra USA och Norra Europa regioner.

### <a name="defining-entities-within-the-data-factory"></a>Definiera enheter inom datafabriken
Följande Data Factory-entiteter har definierats i JSON-mallen:

* [Länkad Azure Storage-tjänst](#azure-storage-linked-service)
* [Länkad tjänst för HDInsight på begäran](#hdinsight-on-demand-linked-service)
* [Indatauppsättning för Azure-blobb](#azure-blob-input-dataset)
* [Utdatauppsättning för Azure-blobb](#azure-blob-output-dataset)
* [Datapipeline med en kopieringsaktivitet](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst
AzureStorageLinkedService länkar ditt Azure Storage-konto till datafabriken. I den här kursen används samma lagringskonto som HDInsight standardkontot för lagring, lagring av indata och utdata datalagring. Därför kan definiera du endast en Azure Storage länkade tjänsten. Länkad tjänst-definition anger du namn och nyckeln för ditt Azure storage-konto. Se [Länkad Azure Storage-tjänst](../data-factory/connector-azure-blob-storage.md) om du vill ha information om JSON-egenskaper som används för att definiera en länkad Azure Storage-tjänst.

```json
{
    "name": "[variables('storageLinkedServiceName')]",
    "type": "linkedservices",
    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
**connectionString** använder parametrarna storageAccountName och storageAccountKey. Du kan ange värden för dessa parametrar när mallen distribueras.  

#### <a name="hdinsight-on-demand-linked-service"></a>HDInsight on-demand linked service (Länkad tjänst för HDInsight på begäran)
I tjänstdefinitionen på begäran länkad HDInsight kan du ange värden för konfigurationsparametrar som används av tjänsten Data Factory för att skapa ett HDInsight Hadoop-kluster vid körning. Läs mer i artikeln [Beräkna länkade tjänster](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service) om JSON-egenskaper som används för att definiera en länkad tjänst för HDInsight på begäran.  

```json

{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "sshUserName": "myuser",                            
            "sshPassword": "MyPassword!",
            "linkedServiceName": "[variables('storageLinkedServiceName')]"
        }
    }
}
```
Observera följande punkter:

* Datafabriken skapar en **Linux-baserade** HDInsight-kluster.
* HDInsight Hadoop-kluster skapas i samma region som lagringskontot.
* Observera den *timeToLive* inställningen. Datafabriken tar bort klustret automatiskt när klustret är att ha varit inaktiv i 30 minuter.
* HDInsight-klustret skapar en **standardbehållare** i den blobblagring som du angav i JSON (**linkedServiceName**). HDInsight tar inte bort den här behållaren när klustret tas bort. Det här beteendet är avsiktligt. Med en HDInsight-länkad tjänst på begäran skapas ett HDInsight-kluster varje gång en sektor behöver bearbetas, såvida det inte finns ett befintligt livekluster (**timeToLive**). Det raderas när bearbetningen är klar.

Se [HDInsight-länkad tjänst på begäran](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service) för mer information.

> [!IMPORTANT]
> Allteftersom fler sektorer bearbetas kan du se många behållare i ditt Azure Blob Storage. Om du inte behöver dem för att felsöka jobb, kan du ta bort dem för att minska lagringskostnaderna. Namnen på de här behållarna följer ett mönster: ”adf**datafabrikensnamn**-**denlänkadetjänstensnamn**-datumtidsstämpel”. Använd verktyg som [Microsoft Lagringsutforskaren](http://storageexplorer.com/) till att ta bort behållare i din Azure Blob-lagring.

#### <a name="azure-blob-input-dataset"></a>Indatauppsättning för Azure-blobb
Ange namnen på blob-behållare, mapp och fil som innehåller indata i definitionen för inkommande datamängden. Se [Egenskaper för Azure-blobbdatauppsättning](../data-factory/connector-azure-blob-storage.md) om du vill ha information om JSON-egenskaper som används för att definiera en Azure-blobbdatauppsättning.

```json

{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}

```

Observera följande specifika inställningar i JSON-definitionen:

```json
"fileName": "input.log",
"folderPath": "adfgetstarted/inputdata",
```

#### <a name="azure-blob-output-dataset"></a>Utdatauppsättning för Azure-blobb
Ange namnen på blob-behållaren och mappen som innehåller utdata i datauppsättningsdefinitionen utdata. Se [Egenskaper för Azure-blobbdatauppsättning](../data-factory/connector-azure-blob-storage.md) om du vill ha information om JSON-egenskaper som används för att definiera en Azure-blobbdatauppsättning.  

```json

{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "adfgetstarted/partitioneddata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        }
    }
}
```

Mappsökvägen anger sökvägen till den mapp som innehåller utdata:

```json
"folderPath": "adfgetstarted/partitioneddata",
```

Den [dataset tillgänglighet](../data-factory/concepts-datasets-linked-services.md) inställningen är följande:

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "style": "EndOfInterval"
},
```

I Azure Data Factory enheter utdata dataset tillgänglighet pipeline. I det här exemplet producerade sektorn månad på den sista dagen i månaden (EndOfInterval). 

#### <a name="data-pipeline"></a>Datapipeline
Du kan definiera en pipeline som omvandlar data genom att köra Hive-skript på en Azure HDInsight-kluster på begäran. Se [Pipeline-JSON](../data-factory/concepts-pipelines-activities.md) för beskrivningar av JSON-element som används för att definiera en pipeline i det här exemplet.

```json
{
    "type": "datapipelines",
    "name": "[parameters('dataFactoryName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobInputDatasetName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobOutputDatasetName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-01-01T00:00:00Z",
        "end": "2016-01-31T00:00:00Z",
        "isPaused": false
    }
}
```

Pipelinen innehåller en aktivitet, HDInsightHive aktivitet. Som både start- och slutdatum är i januari 2016 data för endast en månad (ett segment) har bearbetats. Båda *starta* och *end* för aktiviteten har ett tidigare datum, så Data Factory bearbetar data i månad omedelbart. Om slutet är ett datum i framtiden, skapar ett annat segment i datafabriken när dessa. Mer information finns i [Data Factory schemaläggning och körning av](../data-factory/v1/data-factory-scheduling-and-execution.md).

## <a name="clean-up-the-tutorial"></a>Rensa vägledningen

### <a name="delete-the-blob-containers-created-by-on-demand-hdinsight-cluster"></a>Ta bort blob-behållare som skapats av HDInsight-kluster på begäran
Med länkad HDInsight-tjänsten på begäran skapas ett HDInsight-kluster varje gång ett segment måste bearbetas såvida det inte finns ett befintligt live kluster (timeToLive) och klustret tas bort när bearbetningen är klar. För varje kluster skapar Azure Data Factory blob-behållaren i Azure blob storage som används som standard stroage för klustret. Även om HDInsight-kluster har tagits bort, standard blob storage-behållare och det associerade lagringskontot tas inte bort. Det här beteendet är avsiktligt. Allteftersom fler sektorer bearbetas kan du se många behållare i ditt Azure Blob Storage. Om du inte behöver dem för att felsöka jobb, kan du ta bort dem för att minska lagringskostnaderna. Namnen på de här behållarna följer ett mönster: `adfyourdatafactoryname-linkedservicename-datetimestamp`.

Ta bort den **adfjobs** och **adfyourdatafactoryname-linkedservicename-datetimestamp** mappar. Behållaren adfjobs innehåller jobbloggar från Azure Data Factory.

### <a name="delete-the-resource-group"></a>Ta bort resursgruppen.
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) används för att distribuera, hantera och övervaka din lösning som en grupp.  Tar bort en resursgrupp alla komponenter inuti gruppen.  

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **resursgrupper** i det vänstra fönstret.
3. Klicka på resursgruppens namn du skapade i PowerShell-skript. Med filtret om du har för många resursgrupper i listan. Resursgruppen öppnas i ett nytt blad.
4. På den **resurser** sida vid sida, har du standardkontot för lagring och datafabriken visas om du delar resursgruppen med andra projekt.
5. Klicka på **ta bort** överst på bladet. Detta tar bort lagringskontot och data som lagras i lagringskontot.
6. Ange Resursgruppnamnet Bekräfta borttagning och klicka sedan på **ta bort**.

Om du inte vill ta bort lagringskontot när du tar bort resursgruppen, Överväg följande arkitekturen genom att avgränsa affärsdata från standardkontot för lagring. I det här fallet du har en resursgruppen för lagringskontot med affärsdata och andra resursgruppen för standardkontot för lagring för HDInsight länkade tjänst och datafabriken. När du tar bort andra resursgruppen påverkar inte data storage-konto för företag. Gör så här:

* Lägg till följande översta resursgruppen tillsammans med Microsoft.DataFactory/datafactories resurs i Resource Manager-mall. Den skapar ett lagringskonto:

    ```json
    {
        "name": "[parameters('defaultStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": {

        },
        "properties": {
            "accountType": "Standard_LRS"
        }
    },
    ```
* Lägg till en ny länkade tjänst i det nya lagringskontot:

    ```json
    {
        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
        "type": "linkedservices",
        "name": "[variables('defaultStorageLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
            }
        }
    },
    ```
* Konfigurera HDInsight ondemand länkad tjänst med en ytterligare dependsOn och en additionalLinkedServiceNames:

    ```json
    {
        "dependsOn": [
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"

        ],
        "type": "linkedservices",
        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "sshUserName": "myuser",                            
                "sshPassword": "MyPassword!",
                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
            }
        }
    },            
    ```
## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du använder Azure Data Factory för att skapa HDInsight-kluster på begäran för att bearbeta Hive-jobb. Mer information:

* [Hadoop-vägledning: komma igång med Linux-baserade Hadoop i HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Skapa Linux-baserade Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight-dokumentation](https://azure.microsoft.com/documentation/services/hdinsight/)
* [Data factory-dokumentation](https://azure.microsoft.com/documentation/services/data-factory/)

## <a name="appendix"></a>Bilaga

### <a name="azure-cli-script"></a>Azure CLI-skript
Du kan använda Azure CLI i stället för att använda Azure PowerShell för att göra kursen. Om du vill använda Azure CLI, installera Azure CLI enligt följande anvisningar:

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

#### <a name="use-azure-cli-to-prepare-the-storage-and-copy-the-files"></a>Använda Azure CLI för att förbereda lagring och kopiera filer

```
azure login

azure config mode arm

azure group create --name "<Azure Resource Group Name>" --location "East US 2"

azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
```

Behållarens namn är *adfgetstarted*. Se till att den som den är. Annat behöver du uppdatera Resource Manager-mallen. Om du behöver hjälp med skriptet CLI, se [med hjälp av Azure CLI med Azure Storage](../storage/common/storage-azure-cli.md).
