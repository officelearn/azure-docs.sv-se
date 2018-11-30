---
title: 'Självstudie: Skapa Apache Hadoop-kluster på begäran i Azure HDInsight med Data Factory '
description: Lär dig hur du skapar på begäran Apache Hadoop-kluster i HDInsight med Azure Data Factory.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: hrasheed
ms.openlocfilehash: 1e55da981daf29aca491c480d58f399bc681fd27
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52499551"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Självstudie: Skapa på begäran Apache Hadoop-kluster i HDInsight med Azure Data Factory
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

I den här artikeln får du lära dig hur du skapar en [Apache Hadoop](https://hadoop.apache.org/) kluster på begäran i Azure HDInsight med Azure Data Factory. Du sedan använda datapipelines i Azure Data Factory att köra Hive-jobb och ta bort klustret. I slutet av den här självstudiekursen, kan du se hur att utföra åtgärder för ett jobb för stordata som kör där skapa kluster, kör jobb och klustret borttagning utförs enligt ett schema.

Den här självstudien omfattar följande uppgifter: 

> [!div class="checklist"]
> * Skapa ett Azure-lagringskonto
> * Förstå Azure Data Factory-aktivitet
> * Skapa en datafabrik med hjälp av Azure portal
> * Skapa länkade tjänster
> * Skapa en pipeline
> * Utlös en pipeline
> * Övervaka en pipeline
> * Verifiera utdata

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- Azure PowerShell. Anvisningar finns i [installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

- Ett Azure Active Directory-tjänstobjekt. När du har skapat tjänstens huvudnamn, måste du hämta den **program-ID** och **autentiseringsnyckeln** med hjälp av anvisningarna i den länkade artikeln. Du behöver dessa värden senare i den här självstudien. Kontrollera också att tjänstens huvudnamn är medlem i den *deltagare* rollen för prenumerationen eller resursgruppen som klustret har skapats. Anvisningar att hämta värdena som krävs och tilldela rätt roller finns i [och skapa en Azure Active Directory-tjänstens huvudnamn](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto

I det här avsnittet skapar du ett lagringskonto som ska användas som standardlagringen för HDInsight-klustret som du skapar på begäran. Det här lagringskontot innehåller också exemplet [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) skript (**hivescript.hql**) att du använder för att simulera ett exempel [Apache Hive](https://hive.apache.org/) jobb som körs på klustret.

Det här avsnittet använder en Azure PowerShell-skript för att skapa storage-konto och kopiera över filerna som krävs i lagringskontot. Azure PowerShell-exempelskriptet i det här avsnittet utför följande uppgifter:

1. Loggar i till Azure.
1. Skapar en Azure-resursgrupp.
1. Konfigurerar ett Azure Storage-konto.
1. Skapar en blobbehållare i lagringskontot
1. Kopierar HiveQL exempelskriptet (**hivescript.hql**) Blob-behållaren. Skriptet finns på [ https://hditutorialdata.blob.core.windows.net/adfv2hiveactivity/hivescripts/hivescript.hql ](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql). Exempelskriptet finns redan i en annan offentlig Blob-behållare. PowerShell-skriptet nedan skapar en kopia av dessa filer till Azure Storage-kontot skapas.


**Skapa ett storage-konto och kopiera filerna med hjälp av Azure PowerShell:**
> [!IMPORTANT]
> Ange namn för Azure-resursgrupp och Azure storage-kontot som skapas av skriptet.
> Anteckna **Resursgruppsnamn**, **lagringskontonamn**, och **lagringskontonyckel** utdata av skriptet. Du behöver dem i nästa avsnitt.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
Login-AzureRmAccount
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

**Verifiera det lagringskontot har skapandet**

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj **resursgrupper** i det vänstra fönstret.
1. Dubbelklicka på resursgruppens namn du skapade i PowerShell-skript. Med filtret om du har för många resursgrupper som visas.
1. På den **resurser** sida vid sida, visas en resurs i listan om du inte delar resursgruppen med andra projekt. Den här resursen är lagringskontot med det namn du angav tidigare. Välj namnet på lagringskontot.
1. Välj den **Blobar** paneler.
1. Välj den **adfgetstarted** behållare. Du ser en mapp med namnet **hivescripts**.
1. Öppna mappen och kontrollera att den innehåller skript-exempelfilen **hivescript.hql**.

## <a name="understand-the-azure-data-factory-activity"></a>Förstå Azure Data Factory-aktivitet

[Azure Data Factory](../data-factory/introduction.md) samordnar och automatiserar förflyttning och transformering av data. Azure Data Factory kan skapa ett HDInsight Hadoop-kluster just-in-time för att bearbeta ett indata-segment och ta bort klustret när bearbetningen är klar. 

En datafabrik kan ha en eller flera datapipelines i Azure Data Factory. En datapipeline har en eller flera aktiviteter. Det finns två typer av aktiviteter:

- [Dataförflyttningsaktiviteter](../data-factory/copy-activity-overview.md) -du använda dataförflyttningsaktiviteter för att flytta data från ett källdatalager till ett måldatalager.
- [Datatransformeringsaktiviteter](../data-factory/transform-data.md). Du kan använda datatransformeringsaktiviteter för att omvandla/bearbeta data. HDInsight Hive-aktivitet är en av transformeringsaktiviteter som stöds av Data Factory. Du kan använda omvandling Hive-aktiviteten i den här självstudien.

I den här artikeln får konfigurera du Hive-aktiviteten för att skapa ett HDInsight Hadoop-kluster på begäran. När aktiviteten körs för att bearbeta data, är här vad som händer:

1. Ett HDInsight Hadoop-kluster skapas automatiskt för du just-in-time att bearbeta sektorn. 

1. Indata har bearbetats genom att köra en HiveQL-skript på klustret. I de här självstudierna utför HiveQL-skript som är associerade med hive-aktiviteten följande åtgärder:
    
    - Använder den befintliga tabellen (*hivesampletable*) att skapa en annan tabell **HiveSampleOut**.
    - Fyller den **HiveSampleOut** tabell med endast vissa kolumner från ursprungligt *hivesampletable*.
    
1. HDInsight Hadoop-klustret tas bort när bearbetningen är klar och den har varit inaktivt under en angiven tidsperiod (timeToLive-inställning). Om nästa datasektorn är tillgängliga för bearbetning med i den här timeToLive väntetid, används samma kluster att bearbeta sektorn.  

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. I Azure-portalen väljer du **skapa en resurs** > **Data och analys** > **Data Factory**.

    ![Azure Data Factory på portalen](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Azure Data Factory på portalen")

1. Ange eller välj värdena som visas i följande skärmbild:

    ![Skapa Azure Data Factory med hjälp av Azure portal](./media/hdinsight-hadoop-create-linux-clusters-adf/create-data-factory-portal.png "skapa Azure Data Factory med hjälp av Azure portal")

    Ange eller välj följande värden:
    
    |Egenskap   |Beskrivning  |
    |---------|---------|
    |**Namn** |  Ange ett namn för data factory. Det här namnet måste vara globalt unikt.|
    |**Prenumeration**     |  Välj din Azure-prenumeration. |
    |**Resursgrupp**     | Välj **Använd befintlig** och välj sedan den resursgrupp du skapade med hjälp av PowerShell-skriptet. |
    |**Version**     | Välj **V2 (förhandsversion)** |
    |**Plats**     | Platsen anges automatiskt till den plats du angav när du skapar resursgruppen tidigare. Den här självstudien platsen anges till **östra USA 2**. |
    

1. Välj **fäst på instrumentpanelen**, och välj sedan **skapa**. Du ser en ny panel som heter **Skicka distribution** på portalens instrumentpanel. Skapa en datafabrik kan ta allt mellan 2 till 4 minuter.

    ![Mallen förloppsindikator](./media/hdinsight-hadoop-create-linux-clusters-adf/deployment-progress-tile.png "mall förloppsindikator") 
 
1. När datafabriken har skapats, visar portalen översikt för data factory.

    ![Översikt över Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Azure Data Factory-översikt")

1. Välj **författare och Övervakare** att starta Azure Data Factory skapande och övervakning av portalen.

## <a name="create-linked-services"></a>Skapa länkade tjänster

I det här avsnittet skapar du två länkade tjänster i din datafabrik.

- En **länkad Azure Storage-tjänst** som länkar ditt Azure Storage-konto till datafabriken. Den här lagringen används av HDInsight-kluster på begäran. Den innehåller också Hive-skriptet som körs i klustret.
- En **länkad HDInsight-tjänst på begäran**. Azure Data Factory skapar ett HDInsight-kluster och kör Hive-skript automatiskt. HDInsight-klustret tas bort när det har varit inaktivt under en förinställd tid.

###  <a name="create-an-azure-storage-linked-service"></a>Skapa en länkad Azure Storage-tjänst

1. Från den vänstra rutan i den **nu sätter vi igång** väljer den **redigera** ikon.

    ![Skapa en Azure Data Factory-länkade tjänst](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "skapa en Azure Data Factory-länkade tjänst")

1. Välj **anslutningar** från det nedre vänstra hörnet i fönstret och välj sedan **+ ny**.

    ![Skapa anslutningar i Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "skapar anslutningar i Azure Data Factory")

1. I den **ny länkad tjänst** dialogrutan **Azure Blob Storage** och välj sedan **Fortsätt**.

    ![Skapa Azure Storage-länkad tjänst för Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "skapa Azure Storage-länkad tjänst för Data Factory")

1. Ange ett namn för den länkade storage-tjänsten, Välj Azure Storage-konto som du har skapat som en del av PowerShell-skriptet och därefter **Slutför**.

    ![Ange namnet för Azure Storage-länkade tjänst](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "ange namnet för Azure Storage-länkad tjänst")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Skapa en på begäran länkad HDInsight-tjänst

1. Välj knappen **+Ny** igen för att skapa ytterligare en länkad tjänst.

1. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Compute** > **Azure HDInsight** och sedan **Fortsätt**.

    ![Skapa HDInsight-länkad tjänst för Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "skapa HDInsight-länkad tjänst för Azure Data Factory")

1. I den **ny länkad tjänst** fönstret anger nödvändiga värden.

    ![Ange värden för HDInsight-länkad tjänst](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "ange värden för HDInsight-länkad tjänst")

    Ange följande värden och lämna resten som standard.

    | Egenskap  | Beskrivning |
    | --- | --- |
    | Namn | Ange ett namn för den länkade HDInsight-tjänsten |
    | Typ | Välj **på begäran HDInsight** |
    | Länkad Azure Storage-tjänst | Välj länkad Storage-tjänst som du skapade tidigare. |
    | Klustertyp | Välj **hadoop** |
    | Time to live | Ange den varaktighet som du vill att HDInsight-klustret ska vara tillgängliga innan de tas bort automatiskt.|
    | ID för tjänstens huvudnamn | Ange program-ID för Azure Active Directory-tjänstens huvudnamn som du har skapat som en del av förutsättningarna |
    | Nyckel för tjänstens huvudnamn | Ange autentiseringsnyckeln för Azure Active Directory-tjänstens huvudnamn |
    | Kluster-namnprefix | Ange ett värde som ska ha prefixet till samtliga klustertyper som skapas av data factory |
    | Resursgrupp | Välj den resursgrupp som du har skapat som en del av PowerShell-skriptet som du använde tidigare| 
    | Klustret SSH-användarnamn | Ange ett SSH-användarnamn |
    | Klustret SSH-lösenord | Ange ett lösenord för SSH-användaren |

    Välj **Slutför**.

## <a name="create-a-pipeline"></a>Skapa en pipeline

1. Välj knappen **+** (plus) och välj sedan **Pipeline**.

    ![Skapa en pipeline i Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "skapa en pipeline i Azure Data Factory")

1. I den **aktiviteter** verktygslådan Expandera **HDInsight**, och dra den **Hive** aktiviteten till pipelinedesignytan. I den **Allmänt** fliken, ange ett namn för aktiviteten.

    ![Lägga till aktiviteter i Data Factory-pipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "lägga till aktiviteter i Data Factory-pipeline")

1. Kontrollera att du har valt Hive-aktiviteten, väljer den **HDI-kluster** fliken och från den **HDInsight-länkad tjänst** listrutan, Välj den länkade tjänsten som du skapade tidigare för HDInsight.

    ![Ange information om HDInsight-kluster för pipelinen](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "ge HDInsight klusterinformation för pipelinen")

1. Välj den **skriptet** fliken och utför följande steg:
    
    1. För **skriptet länkade tjänsten**väljer **HDIStorageLinkedService**. Det här värdet är den länkade storage-tjänst som du skapade tidigare.
    
    1. För **filsökväg**väljer **Bläddra i lagring** och navigera till den plats där exempel Hive-skriptet är tillgänglig. Om du har kört PowerShell-skriptet tidigare den här platsen måste vara `adfgetstarted/hivescripts/hivescript.hql`.
    
        ![Ange information för Hive-skript för pipelinen](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "ange Hive skriptinformation för pipelinen")
    
    1. Under **Avancerat** > **parametrar**väljer **Autofyll från skript**. Det här alternativet söker efter alla parametrar i Hive-skriptet som du kräver värden vid körning. Skriptet som du använder (**hivescript.hql**) har en **utdata** parametern. Ange ett värde i formatet `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` så att den pekar till en befintlig mapp i Azure Storage. Sökvägen är skiftlägeskänslig. Det här är den sökväg där utdata från skriptet kommer att lagras.
    
        ![Ange parametrar för Hive-skriptet](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "ange parametrar för Hive-skript")

1. Välj **verifiera** att verifiera pipelinen. Klicka på knappen **>>** (högerpil) för att stänga verifieringsfönstret.

    ![Verifiera Azure Data Factory-pipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "verifiera Azure Data Factory-pipeline")

1. Välj slutligen **publicera alla** att publicera artefakter till Azure Data Factory.

    ![Publicera Azure Data Factory-pipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "publicera Azure Data Factory-pipeline")

## <a name="trigger-a-pipeline"></a>Utlös en pipeline

1. I verktygsfältet på designytan väljer **utlösaren** > **Utlös nu**.

    ![Utlösa Azure Data Factory-pipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "utlösa Azure Data Factory-pipeline")

1. Välj **Slutför** i popup-sida-fältet.

## <a name="monitor-a-pipeline"></a>Övervaka en pipeline

1. Växla till fliken **Övervaka** till vänster. En pipelinekörning visas i listan **Pipeline Runs** (Pipelinekörningar). Lägg märke till statusen för körning under den **Status** kolumn.

    ![Övervaka Azure Data Factory-pipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "övervaka Azure Data Factory-pipeline")

1. Välj **Uppdatera** för att uppdatera statusen.

1. Du kan också välja den **visa Aktivitetskörningar** ikonen om du vill se aktivitetskörningar som är associerad med pipelinen. I skärmbilden nedan ser du bara en aktivitetskörning eftersom det inte finns bara en aktivitet i pipelinen som du skapade. Om du vill växla tillbaka till den föregående vyn väljer **Pipelines** överst på sidan.

    ![Övervaka Azure Data Factory-pipeline-aktivitet](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "övervaka Azure Data Factory-pipeline-aktivitet")


## <a name="verify-the-output"></a>Verifiera utdata

1. För att bekräfta utdata i Azure-portalen går du till lagringskontot som du använde för den här självstudien. Du bör se följande mappar eller behållare:

    - Du ser en **adfgerstarted/outputfolder** som innehåller utdata för Hive-skript som kördes som en del av pipelinen.

    - Du ser en **adfhdidatafactory -\<länkade tjänstnamnet >-\<timestamp >** behållare. Den här behållaren är standardplatsen för lagring av HDInsight-kluster som har skapats som en del av pipeline-körning.

    - Du ser en **adfjobs** loggar för behållaren som innehåller Azure Data Factory-jobbet.  

        ![Verifiera utdata för Azure Data Factory-pipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "verifiera utdata för Azure Data Factory-pipeline")


## <a name="clean-up-the-tutorial"></a>Rensa vägledningen

Med den på-deman skapa för HDInsight-kluster, du behöver inte uttryckligen tar bort HDInsight-klustret. Klustret tas bort baserat på konfigurationen som du angav när du skapar pipelinen. Även när klustret tas bort, fortsätter de lagringskonton som är kopplat till klustret finns. Det här beteendet är avsiktligt så att du kan hålla dina data intakta. Om du inte vill spara data kan du dock ta bort det lagringskonto du skapade.

Du kan också ta bort hela resursgruppen som du skapade för den här självstudien. Detta tar bort lagringskontot och Azure Data Factory som du skapade.

### <a name="delete-the-resource-group"></a>Ta bort resursgruppen

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj **resursgrupper** i det vänstra fönstret.
1. Välj resursgruppens namn du skapade i PowerShell-skript. Med filtret om du har för många resursgrupper som visas. Resursgruppen öppnas.
1. På den **resurser** panel, ska du har standardkontot för lagring och data factory om du inte delar resursgruppen med andra projekt i listan.
1. Välj **Ta bort resursgrupp**. Detta tar bort lagringskontot och de data som lagras i lagringskontot.

    ![Ta bort resursgruppen](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "ta bort resursgrupp")

1. Ange resursgruppens namn att bekräfta borttagning och välj sedan **ta bort**.


## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du använder Azure Data Factory för att skapa på begäran HDInsight-kluster och kör [Apache Hive](https://hive.apache.org/) jobb. Gå vidare till nästa artciel att lära dig hur du skapar HDInsight-kluster med anpassad konfiguration.

> [!div class="nextstepaction"]
>[Skapa Azure HDInsight-kluster med anpassad konfiguration](hdinsight-hadoop-provision-linux-clusters.md)


