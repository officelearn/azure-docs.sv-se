---
title: 'Självstudier: Skapa på begäran Hadoop-kluster i Azure HDInsight med Data Factory | Microsoft Docs'
description: Lär dig hur du skapar på begäran Hadoop-kluster i HDInsight med hjälp av Azure Data Factory.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1f3b3a78-4d16-4d99-ba6e-06f7bb185d6a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: nitinme
ms.openlocfilehash: 53ff14e00b88f6d182579ba0d9df630fae9b3d78
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-create-on-demand-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Självstudier: Skapa på begäran Hadoop-kluster i HDInsight med Azure Data Factory
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

I den här artikeln får du lära dig hur du skapar ett Hadoop-kluster på begäran i Azure HDInsight med hjälp av Azure Data Factory. Du sedan använda data pipelines i Azure Data Factory att köra Hive-jobb och tar bort klustret. I slutet av den här kursen lär du dig att operationalisera ett jobb för stordata som kör om klustret har skapats, kör jobbet och klustret tas bort sker enligt ett schema.

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

* Azure PowerShell. Instruktioner finns i [installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

* Azure Active Directory tjänstens huvudnamn. När du har skapat tjänstens huvudnamn, måste du hämta den **program-ID** och **autentiseringsnyckel** med hjälp av anvisningarna i den länkade artikeln. Du måste dessa värden senare i den här kursen. Kontrollera också att tjänstens huvudnamn är medlem i den *deltagare* rollen för prenumerationen eller resursgrupp som klustret har skapats. Anvisningar att hämta nödvändiga värden och tilldela rätt roller finns [och skapa ett Azure Active Directory huvudnamn för tjänsten](../azure-resource-manager/resource-group-create-service-principal-portal.md).

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto

I det här avsnittet skapar du ett lagringskonto som ska användas som standardlagring för HDInsight-klustret som du skapar på begäran. Det här lagringskontot innehåller också HiveQL exempelskript (**hivescript.hql**) som används för att simulera ett Hive-jobb som körs på klustret.

Det här avsnittet använder ett Azure PowerShell-skript för att skapa lagringskontot och kopiera över filerna som krävs inom lagringskonto. Azure PowerShell-exempelskript i det här avsnittet utför följande uppgifter:

1. Loggfiler i Azure.
2. Skapar ett Azure-resursgrupp.
3. Konfigurerar ett Azure Storage-konto.
4. Skapar en Blob-behållare i storage-konto
5. Kopierar HiveQL exempelskript (**hivescript.hql**) Blob-behållaren. Skriptet finns på [ https://hditutorialdata.blob.core.windows.net/adfv2hiveactivity/hivescripts/hivescript.hql ](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql). Exempelskript finns redan i en annan offentlig Blob-behållare. PowerShell-skriptet nedan gör en kopia av dessa filer till Azure Storage-konto skapas.


**Skapa ett lagringskonto och kopiera filerna med hjälp av Azure PowerShell:**
> [!IMPORTANT]
> Ange namn för Azure-resursgrupp och Azure storage-konto som skapas av skriptet.
> Skriv ned **resursgruppnamn**, **lagringskontonamnet**, och **lagringskontonyckel** utdata av skriptet. Du behöver dem i nästa avsnitt.

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
<<<<<<< HEAD
Login-AzureRmAccount
=======
try{Get-AzureRmContext}
catch{Connect-AzureRmAccount}
>>>>>>> refs/remotes/MicrosoftDocs/release-build-hdinsight-2018
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

**Att verifiera lagring konto skapas**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **resursgrupper** i det vänstra fönstret.
3. Dubbelklicka på resursgruppens namn du skapade i PowerShell-skript. Med filtret om du har för många resursgrupper i listan.
4. På den **resurser** panelen måste du se en resurs i listan om du delar resursgruppen med andra projekt. Den här resursen är lagringskontot med det namn du angav tidigare. Välj namnet på lagringskontot.
5. Välj den **Blobbar** paneler.
6. Välj den **adfgetstarted** behållare. Du ser en mapp med namnet **hivescripts**.
7. Öppna mappen och kontrollera att den innehåller skriptet exempelfilen **hivescript.hql**.

## <a name="understand-the-azure-data-factory-activity"></a>Förstå Azure Data Factory-aktivitet

[Azure Data Factory](../data-factory/introduction.md) samordnar och automatiserar flytt och transformering av data. Azure Data Factory kan skapa en HDInsight Hadoop-kluster just-in-time för att bearbeta en inkommande datasektorn och tar bort klustret när bearbetningen är klar. 

En datafabrik kan ha en eller flera data pipelines i Azure Data Factory. En data-pipeline har en eller flera aktiviteter. Det finns två typer av aktiviteter:

* [Data Movement aktiviteter](../data-factory/copy-activity-overview.md) -du använder data movement aktiviteter för att flytta data från ett dataarkiv som källa till ett dataarkiv som mål.
* [Data Transformation aktiviteter](../data-factory/transform-data.md). Du kan använda data transformation aktiviteter för att transformera/bearbeta data. HDInsight Hive aktivitet är en omvandling av aktiviteter som stöds av Data Factory. Du använder Hive omvandling aktiviteten i den här kursen.

I den här artikeln konfigurerar du Hive-aktiviteten för att skapa en HDInsight Hadoop-kluster på begäran. När aktiviteten körs för bearbetning av data, är här vad som händer:

1. HDInsight Hadoop-kluster skapas automatiskt för du just-in-time bearbeta sektorn. 

2. Indata bearbetas av en HiveQL-skript körs i klustret. I den här självstudiekursen utför HiveQL-skript som är associerade med hive aktiviteten följande åtgärder:

    * Använder den befintliga tabellen (*hivesampletable*) att skapa en annan tabell **HiveSampleOut**.
    * Fyller på **HiveSampleOut** tabell med bara specifika kolumner från ursprungligt *hivesampletable*.

3. HDInsight Hadoop-kluster tas bort när bearbetningen är klar och klustret är inaktiv under den konfigurerade tidsperiod (timeToLive-inställning). Om nästa datasektorn är tillgängliga för bearbetning med i den här timeToLive väntetid används samma kluster att bearbeta sektorn.  

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj i Azure-portalen **skapar du en resurs** > **Data + analys** > **Data Factory**.

    ![Azure Data Factory på portalen](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Azure Data Factory på portalen")

2. Ange eller välj värdena som visas i följande skärmbild:

    ![Skapa Azure Data Factory med hjälp av Azure portal](./media/hdinsight-hadoop-create-linux-clusters-adf/create-data-factory-portal.png "skapa Azure Data Factory med hjälp av Azure portal")

    Ange eller välj följande värden:
    
    |Egenskap  |Beskrivning  |
    |---------|---------|
    |**Namn** |  Ange ett namn för data factory. Det här namnet måste vara globalt unika.|
    |**Prenumeration**     |  Välj din Azure-prenumeration. |
    |**Resursgrupp**     | Välj **använda befintliga** och välj sedan den resursgrupp som du skapat med hjälp av PowerShell-skript. |
    |**Version**     | Välj **V2 (förhandsgranskning)** |
    |**Plats**     | Platsen anges automatiskt till den plats du angav när du skapar resursgruppen tidigare. Den här självstudiekursen har platsen angetts **östra USA 2**. |
    

3. Välj **fäst på instrumentpanelen**, och välj sedan **skapa**. Du bör se en ny panel med titeln **skickar distribution** på portalens instrumentpanel. Skapa en datafabrik kan ta allt mellan 2-4 minuter.

    ![Mallen förlopp](./media/hdinsight-hadoop-create-linux-clusters-adf/deployment-progress-tile.png "mallen distribution pågår") 
 
4. När datafabriken har skapats visas på portalen översikt för data factory.

    ![Översikt över Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "översikt över Azure Data Factory")

5. Välj **författare & övervakaren** att starta Azure Data Factory redigering och övervakning av portalen.

## <a name="create-linked-services"></a>Skapa länkade tjänster

I det här avsnittet kan skapa du två länkade tjänster i din data factory.

- En **länkad Azure Storage-tjänst** som länkar ditt Azure Storage-konto till datafabriken. Den här lagringen används av HDInsight-kluster på begäran. Den innehåller också Hive-skript som körs på klustret.
- En **länkad HDInsight-tjänst på begäran**. Azure Data Factory automatiskt skapar ett HDInsight-kluster och kör Hive-skript. HDInsight-klustret tas bort när det har varit inaktivt under en förinställd tid.

###  <a name="create-an-azure-storage-linked-service"></a>Skapa en länkad Azure Storage-tjänst

1. I den vänstra rutan i den **nu sätter vi igång** väljer den **redigera** ikon.

    ![Skapa ett Azure Data Factory länkad tjänst](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "skapa ett Azure Data Factory länkad tjänst")

2. Välj **anslutningar** från det nedre vänstra hörnet i fönstret och välj sedan **+ ny**.

    ![Skapa anslutningar i Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "skapar i Azure Data Factory")

3. I den **ny länkade tjänst** dialogrutan **Azure Blob Storage** och välj sedan **Fortsätt**.

    ![Skapa Azure Storage länkade tjänsten för Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "skapa Azure Storage länkade tjänsten för Data Factory")

4. Ange ett namn för den länkade lagringstjänsten, Välj Azure Storage-konto som du har skapat som en del av PowerShell-skript och välj sedan **Slutför**.

    ![Ange namnet för Azure Storage länkade tjänsten](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "ange namnet för Azure Storage länkade tjänsten")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Skapa en på begäran länkad HDInsight-tjänst

1. Välj knappen **+Ny** igen för att skapa ytterligare en länkad tjänst.

2. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Compute** > **Azure HDInsight** och sedan **Fortsätt**.

    ![Skapa HDInsight länkade tjänsten för Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "skapa HDInsight länkade tjänsten för Azure Data Factory")

3. I den **ny länkade tjänst** fönstret anger nödvändiga värden.

    ![Ange värden för HDInsight länkade tjänsten](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "ange värden för HDInsight länkade tjänsten")

    Ange följande värden och lämna resten som standard.

    | Egenskap | Beskrivning |
    | --- | --- |
    | Namn | Ange ett namn för länkad HDInsight-tjänst |
    | Typ | Välj **HDInsight på begäran** |
    | Länkad Azure Storage-tjänst | Välj den länkade lagringstjänsten du skapade tidigare. |
    | Klustertyp | Välj **hadoop** |
    | Time to Live | Ange varaktigheten för vilken du vill HDInsight-klustret ska finnas innan de tas bort automatiskt.|
    | ID för tjänstens huvudnamn | Ange program-ID för Azure Active Directory-tjänstens huvudnamn som du har skapat som en del av förutsättningarna |
    | Huvudnyckeln för tjänsten | Ange autentiseringsnyckel för Azure Active Directory-tjänstens huvudnamn |
    | Namnprefix för kluster | Ange ett värde som ska föregås för alla klustertyper som skapas av datafabriken |
    | Resursgrupp | Markera den resursgrupp som du har skapat som en del av PowerShell-skript som du använde tidigare| 
    | Klustret SSH-användarnamn | Ange en SSH-användarnamn |
    | Klustret SSH-lösenord | Ange ett lösenord för SSH-användare |

    Välj **Slutför**.

## <a name="create-a-pipeline"></a>Skapa en pipeline

1. Välj knappen **+** (plus) och välj sedan **Pipeline**.

    ![Skapa en pipeline i Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "skapar en pipeline i Azure Data Factory")

2. I den **aktiviteter** verktygslådan Expandera **HDInsight**, och dra den **Hive** aktiviteten till designytan pipeline. I den **allmänna** fliken, ange ett namn för aktiviteten.

    ![Lägga till aktiviteter i Data Factory-pipelinen](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "lägger till aktiviteter i Data Factory-pipelinen")

3. Se till att du har Hive-aktiviteten som valts, Välj den **HDI-klustret** fliken och från den **länkad HDInsight-tjänst** listrutan, Välj den länkade tjänst som du skapade tidigare för HDInsight.

    ![Ange information om HDInsight-kluster för pipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "ger HDInsight klusterinformation för pipeline")

4. Välj den **skriptet** fliken och utför följande steg:

    a. För **skript länkade tjänsten**väljer **HDIStorageLinkedService**. Det här värdet är den länkade lagringstjänsten du skapade tidigare.

    b. För **filsökväg**väljer **Bläddra lagring** och navigera till den plats där Hive exempelskript är tillgänglig. Om du tidigare körde PowerShell-skriptet platsen bör vara `adfgetstarted/hivescripts/hivescript.hql`.

    ![Ange information för Hive-skript för pipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "ange Hive skriptdetaljer för pipeline")

    c. Under **Avancerat** > **parametrar**väljer **Autofyll från skriptet**. Det här alternativet söker efter alla parametrar i Hive-skript som kräver att värden vid körning. Skriptet som du använder (**hivescript.hql**) har en **utdata** parameter. Ange ett värde i formatet `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` att peka till en befintlig mapp på din Azure-lagring. Sökvägen är skiftlägeskänslig. Det här är den sökväg där utdata från skriptet ska lagras.

    ![Ange parametrar för Hive-skriptet](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "ange parametrar för Hive-skript")

5. Välj **verifiera** att validera pipeline. Klicka på knappen **>>** (högerpil) för att stänga verifieringsfönstret.

    ![Verifiera Azure Data Factory-pipelinen](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "Validera Azure Data Factory-pipelinen")

5. Välj slutligen **publicera alla** publicera artefakter till Azure Data Factory.

    ![Publicera Azure Data Factory-pipelinen](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "publicera Azure Data Factory-pipelinen")

## <a name="trigger-a-pipeline"></a>Utlös en pipeline

1. I verktygsfältet på designytan, Välj **utlösaren** > **utlösaren nu**.

    ![Aktivera Azure Data Factory-pipelinen](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "utlösa Azure Data Factory-pipelinen")

2. Välj **Slutför** i popup-sidorutan.

## <a name="monitor-a-pipeline"></a>Övervaka en pipeline

1. Växla till fliken **Övervaka** till vänster. En pipelinekörning visas i listan **Pipeline Runs** (Pipelinekörningar). Lägg märke till statusen för körning under den **Status** kolumn.

    ![Övervaka Azure Data Factory-pipelinen](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "övervaka Azure Data Factory-pipelinen")

2. Välj **Uppdatera** för att uppdatera statusen.

3. Du kan också välja den **visa aktiviteten körs** ikon för att se kör aktiviteten som associeras med pipeline. I skärmbilden nedan ser du endast en aktivitet som körs eftersom det finns bara en aktivitet i pipelinen som du skapade. Om du vill växla tillbaka till den föregående vyn, Välj **Pipelines** mot sidans överkant.

    ![Övervaka aktiviteten Azure Data Factory-pipelinen](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "övervaka aktiviteten Azure Data Factory-pipelinen")


## <a name="verify-the-output"></a>Verifiera utdata

1. Om du vill kontrollera utdata i Azure portal navigera till storage-konto som du använde för den här kursen. Du bör se följande mappar eller behållare:

    - Du ser en **adfgerstarted/outputfolder** som innehåller utdata från Hive-skript som kördes som en del av pipeline.

    - Du ser en **adfhdidatafactory -\<länkade tjänstnamnet >-\<tidsstämpel >** behållare. Den här behållaren är standardplatsen för lagring av HDInsight-kluster som har skapats som en del av pipeline-körningen.

    - Du ser en **adfjobs** loggar för behållaren som innehåller Azure Data Factory-jobbet.  

        ![Kontrollera utdata för Azure Data Factory-pipelinen](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "Kontrollera utdata för Azure Data Factory-pipelinen")


## <a name="clean-up-the-tutorial"></a>Rensa vägledningen

Med den på-deman HDInsight-kluster, du behöver inte ta bort HDInsight-klustret. Klustret tas bort baserat på konfigurationen som du angav när du skapar pipeline. Även efter att ta bort klustret fortsätta storage-konton som är associerade med klustret finns. Det här beteendet är avsiktligt så att du kan behålla dina data intakta. Om du inte vill bevara data kan du dock ta bort lagringskontot som du skapade.

Du kan också radera hela resursgruppen som du skapade för den här kursen. Detta tar bort lagringskontot och Azure Data Factory som du skapade.

### <a name="delete-the-resource-group"></a>Ta bort resursgruppen.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **resursgrupper** i det vänstra fönstret.
3. Välj resursgruppens namn du skapade i PowerShell-skript. Med filtret om du har för många resursgrupper i listan. Resursgruppen öppnas.
4. På den **resurser** sida vid sida, har du standardkontot för lagring och datafabriken visas om du delar resursgruppen med andra projekt.
5. Välj **Ta bort resursgrupp**. Detta tar bort lagringskontot och data som lagras i lagringskontot.

    ![Ta bort resursgruppen](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "ta bort resursgruppen.")

6. Ange resursgruppens namn att bekräfta borttagning och välj sedan **ta bort**.


## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du använder Azure Data Factory för att skapa HDInsight-kluster på begäran och köra Hive-jobb. Gå vidare till nästa artciel att lära dig hur du skapar HDInsight-kluster med anpassad konfiguration.

> [!div class="nextstepaction"]
>[Skapa Azure HDInsight-kluster med anpassad konfiguration](hdinsight-hadoop-provision-linux-clusters.md)


