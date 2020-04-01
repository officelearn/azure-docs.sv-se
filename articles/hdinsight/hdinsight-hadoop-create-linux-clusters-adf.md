---
title: 'Självstudiekurs: Kluster på begäran i Azure HDInsight med Data Factory'
description: Självstudiekurs – Lär dig hur du skapar Apache Hadoop-kluster på begäran i HDInsight med Hjälp av Azure Data Factory.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/18/2020
ms.openlocfilehash: b184a42c52384440445181ac44c616c3139e064f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80130698"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Självstudiekurs: Skapa Apache Hadoop-kluster på begäran i HDInsight med Azure Data Factory

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

I den här självstudien får du lära dig hur du skapar ett [Apache Hadoop-kluster,](./hadoop/apache-hadoop-introduction.md) på begäran, i Azure HDInsight med Azure Data Factory. Du använder sedan datapipelpipelor i Azure Data Factory för att köra Hive-jobb och ta bort klustret. I slutet av den här självstudien får du lära dig hur du kan operationalisera en jobbkörning för stordata där klusterskapande, jobbkörning och klusterborttagning utförs enligt ett schema.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa ett Azure-lagringskonto
> * Förstå Azure Data Factory-aktivitet
> * Skapa en datafabrik med Azure-portalen
> * Skapa länkade tjänster
> * Skapa en pipeline
> * Utlösa en pipeline
> * Övervaka en pipeline
> * Verifiera utdata

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

* PowerShell [Az-modulen](https://docs.microsoft.com/powershell/azure/overview) är installerad.

* Ett huvudnamn för Tjänsten Azure Active Directory. När du har skapat tjänstens huvudnamn måste du hämta **program-ID: et** och **autentiseringsnyckeln** med hjälp av instruktionerna i den länkade artikeln. Du behöver dessa värden senare i den här självstudien. Kontrollera också att tjänstens huvudnamn är medlem i *rollen Deltagare* för prenumerationen eller resursgruppen där klustret skapas. Instruktioner för hur du hämtar de värden som krävs och tilldelar rätt roller finns i [Skapa ett Azure Active Directory-tjänsthuvudnamn](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-preliminary-azure-objects"></a>Skapa preliminära Azure-objekt

I det här avsnittet skapar du olika objekt som ska användas för HDInsight-klustret som du skapar på begäran. Det skapade lagringskontot innehåller exempelet `partitionweblogs.hql` [HiveQL-skript,](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) som du använder för att simulera ett exempel på Apache Hive-jobb som körs i klustret.

Det här avsnittet använder ett Azure PowerShell-skript för att skapa lagringskontot och kopiera över de nödvändiga filerna i lagringskontot. Exempelskriptet för Azure PowerShell i det här avsnittet utför följande uppgifter:

1. Loggar in på Azure.
2. Skapar en Azure-resursgrupp.
3. Konfigurerar ett Azure Storage-konto.
4. Skapar en Blob-behållare i lagringskontot
5. Kopierar exemplet HiveQL-skript (**partitionweblogs.hql**) Blob-behållaren. Skriptet finns [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)på . Exempelskriptet är redan tillgängligt i en annan offentlig Blob-behållare. PowerShell-skriptet nedan gör en kopia av dessa filer till Azure Storage-kontot som skapas.

### <a name="create-storage-account-and-copy-files"></a>Skapa lagringskonto och kopiera filer

> [!IMPORTANT]  
> Ange namn för Azure-resursgruppen och Azure-lagringskontot som ska skapas av skriptet.
> Skriv ned **resursgruppsnamn,** **lagringskontonamn**och **lagringskontonyckel** som utdata från skriptet. Du behöver dem i nästa avsnitt.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $location

New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -Kind StorageV2 `
    -Location $location `
    -SkuName Standard_LRS `
    -EnableHttpsTrafficOnly 1

$destStorageAccountKey = (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous

$destContext = New-AzStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzStorageContainer `
    -Name $destContainerName `
    -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName `
    -Blob "hivescripts\hivescript.hql"

$blobs|Start-AzStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName `
    -DestBlob "hivescripts\partitionweblogs.hql"

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzStorageBlob `
    -Context $destContext `
    -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

### <a name="verify-storage-account"></a>Verifiera lagringskonto

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Från vänster navigerar du till **Alla tjänster** > **Allmänna** > **resursgrupper**.
1. Välj det resursgruppsnamn som du skapade i PowerShell-skriptet. Använd filtret om du har för många resursgrupper i listan.
1. I **översiktsvyn** visas en resurs i listan om du inte delar resursgruppen med andra projekt. Den resursen är lagringskontot med det namn som du angav tidigare. Välj namnet på lagringskontot.
1. Markera panelen **Behållare.**
1. Markera **behållaren för adfgetstarted.** Du ser en mapp som heter **hivescripts**.
1. Öppna mappen och se till att den innehåller exempelskriptfilen, **partitionweblogs.hql**.

## <a name="understand-the-azure-data-factory-activity"></a>Förstå Azure Data Factory-aktiviteten

[Azure Data Factory](../data-factory/introduction.md) dirigerar och automatiserar förflyttning och omvandling av data. Azure Data Factory kan skapa ett HDInsight Hadoop-kluster just-in-time för att bearbeta en indatasegment och ta bort klustret när bearbetningen är klar.

I Azure Data Factory kan en datafabrik ha en eller flera datapipeldrar. En datapipeline har en eller flera aktiviteter. Det finns två typer av aktiviteter:

* [Data rörelseaktiviteter](../data-factory/copy-activity-overview.md). Du använder datarörelseaktiviteter för att flytta data från ett källdatalager till ett måldatalager.
* [Dataomvandlingsaktiviteter](../data-factory/transform-data.md). Du använder dataomvandlingsaktiviteter för att omvandla/bearbeta data. HDInsight Hive-aktivitet är en av de omvandlingsaktiviteter som stöds av Data Factory. Du använder hive-omvandlingsaktiviteten i den här självstudien.

I den här artikeln konfigurerar du Hive-aktiviteten för att skapa ett HDInsight Hadoop-kluster på begäran. När aktiviteten körs för att bearbeta data, här är vad som händer:

1. Ett HDInsight Hadoop-kluster skapas automatiskt för att du ska kunna bearbeta segmentet.

2. Indata bearbetas genom att köra ett HiveQL-skript i klustret. I den här självstudien utför HiveQL-skriptet som är associerat med hive-aktiviteten följande åtgärder:

    * Använder den befintliga tabellen (*hivesampletable)* för att skapa en annan tabell **HiveSampleOut**.
    * Fyller i tabellen **HiveSampleOut** med endast specifika kolumner från det ursprungliga *hivesampletable.*

3. HDInsight Hadoop-klustret tas bort när bearbetningen är klar och klustret är inaktivt för den konfigurerade tiden (timeToLive-inställningen). Om nästa datasegment är tillgängligt för bearbetning med i den här tidenToLive inaktiv tid, används samma kluster för att bearbeta segmentet.  

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. På den vänstra menyn navigerar du till **+ Skapa en resurs** > **Analytics** > **Data Factory**.

    ![Azure Data Factory på portalen](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Azure Data Factory på portalen")

3. Ange eller välj följande värden för panelen **Ny datafabrik:**

    |Egenskap  |Värde  |
    |---------|---------|
    |Namn | Ange ett namn på datafabriken. Det här namnet måste vara globalt unikt.|
    |Version | Lämna på **V2**. |
    |Prenumeration | Välj din Azure-prenumeration. |
    |Resursgrupp | Markera den resursgrupp som du skapade med PowerShell-skriptet. |
    |Location | Platsen ställs automatiskt in på den plats som du angav när resursgruppen skapas tidigare. För den här självstudien är platsen inställd på **Östra USA**. |
    |Aktivera GIT|Avmarkera den här rutan.|

    ![Skapa Azure Data Factory med Azure-portal](./media/hdinsight-hadoop-create-linux-clusters-adf/azure-portal-create-data-factory.png "Skapa Azure Data Factory med Azure-portal")

4. Välj **Skapa**. Det kan ta mellan 2 till 4 minuter att skapa en datafabrik.

5. När datafabriken har skapats får du ett **meddelande om distribution** med knappen Gå till **resurs.**  Välj **Gå till resurs** för att öppna standardvyn Data Factory.

6. Välj **Författare & Övervakare** om du vill starta azure data factory-redigerings- och övervakningsportalen.

    ![Översikt över Azure Data Factory-portalen](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Översikt över Azure Data Factory")

## <a name="create-linked-services"></a>Skapa länkade tjänster

I det här avsnittet skapar du två länkade tjänster i datafabriken.

* En **Azure Storage-länkad tjänst** som länkar ett Azure-lagringskonto till datafabriken. Den här lagringen används av HDInsight-kluster på begäran. Den innehåller också Hive-skriptet som körs i klustret.
* En **hdinsight-länkad tjänst på begäran**. Azure Data Factory skapar automatiskt ett HDInsight-kluster och kör Hive-skriptet. HDInsight-klustret tas bort när det har varit inaktivt under en förinställd tid.

### <a name="create-an-azure-storage-linked-service"></a>Skapa en länkad Azure Storage-tjänst

1. Välj ikonen **Författare** i den vänstra rutan på sidan **Låt oss komma igång.**

    ![Skapa en Azure Data Factory-länkad tjänst](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "Skapa en Azure Data Factory-länkad tjänst")

2. Välj **Anslutningar** längst ned till vänster i fönstret och välj sedan **+Nytt**.

    ![Skapa anslutningar i Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "Skapa anslutningar i Azure Data Factory")

3. I dialogrutan **Ny länkad tjänst** väljer du **Azure Blob Storage** och väljer sedan **Fortsätt**.

    ![Skapa Azure Storage-länkad tjänst för Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "Skapa Azure Storage-länkad tjänst för Data Factory")

4. Ange följande värden för den lagringslänkade tjänsten:

    |Egenskap |Värde |
    |---|---|
    |Namn |Ange `HDIStorageLinkedService`.|
    |Azure-prenumeration |Välj din prenumeration i listrutan.|
    |Lagringskontots namn |Välj det Azure Storage-konto som du skapade som en del av PowerShell-skriptet.|

    Välj **Testanslutning** och välj sedan **Skapa**.

    ![Ange namn för Azure Storage-länkad tjänst](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "Ange namn för Azure Storage-länkad tjänst")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Skapa en på begäran länkad HDInsight-tjänst

1. Välj knappen **+Ny** igen för att skapa ytterligare en länkad tjänst.

2. Välj fliken **Beräkna** i fönstret **Ny länkad tjänst.**

3. Välj **Azure HDInsight**och välj sedan **Fortsätt**.

    ![Skapa HDInsight-länkad tjänst för Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "Skapa HDInsight-länkad tjänst för Azure Data Factory")

4. I fönstret **Ny länkad tjänst** anger du följande värden och lämnar resten som standard:

    | Egenskap | Värde |
    | --- | --- |
    | Namn | Ange `HDInsightLinkedService`.|
    | Typ | Välj **HDInsight på begäran**. |
    | Länkad Azure Storage-tjänst | Välj `HDIStorageLinkedService`. |
    | Klustertyp | Välj **hadoop** |
    | Time to live | Ange hur länge du vill att HDInsight-klustret ska vara tillgängligt innan det tas bort automatiskt.|
    | Huvud-ID för tjänsten | Ange program-ID:et för azure Active Directory-tjänstens huvudnamn som du skapade som en del av förutsättningarna. |
    | Huvudnyckel för tjänsten | Ange autentiseringsnyckeln för Azure Active Directory-tjänstens huvudnamn. |
    | Prefix för klusternamn | Ange ett värde som ska föregås av alla klustertyper som skapas av datafabriken. |
    |Prenumeration |Välj din prenumeration i listrutan.|
    | Välj resursgrupp | Markera den resursgrupp som du skapade som en del av Det PowerShell-skript som du använde tidigare.|
    | OS-typ/Cluster SSH-användarnamn | Ange ett SSH-användarnamn, `sshuser`vanligen . |
    | OS-typ/Cluster SSH-lösenord | Ange ett lösenord för SSH-användaren |
    | Os-typ/klusteranvändarnamn | Ange ett klusteranvändarnamn, `admin`vanligen . |
    | OS-typ/klusterlösenord | Ange ett lösenord för klusteranvändaren. |

    Välj sedan **Skapa**.

    ![Ange värden för HDInsight-länkad tjänst](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "Ange värden för HDInsight-länkad tjänst")

## <a name="create-a-pipeline"></a>Skapa en pipeline

1. Välj **+** knappen (plus) och välj sedan **Pipeline**.

    ![Skapa en pipeline i Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "Skapa en pipeline i Azure Data Factory")

1. Expandera **HDInsight**i **verktygslådan Aktiviteter** och dra **Hive-aktiviteten** till pipelinedesignerns yta. Ange ett namn för aktiviteten på fliken **Allmänt.**

    ![Lägga till aktiviteter i Data Factory-pipelinen](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "Lägga till aktiviteter i Data Factory-pipelinen")

1. Kontrollera att du har valt Hive-aktivitet, välj fliken **HDI-kluster** och välj den länkade tjänsten som du skapade tidigare, **HDInsightLinkedService,** för HDInsight i listrutan **HDInsight.**

    ![Ange HDInsight-klusterinformation för pipelinen](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "Ange HDInsight-klusterinformation för pipelinen")

1. Välj fliken **Skript** och gör så här:

    1. För **skriptlänkad tjänst**väljer du **HDIStorageLinkedService** i listrutan. Det här värdet är den lagringslänkade tjänst som du skapade tidigare.

    1. För **Sökväg**för fil väljer du **Bläddra lagring** och navigerar till den plats där exempeldatafilen är tillgänglig. Om du körde PowerShell-skriptet tidigare `adfgetstarted/hivescripts/partitionweblogs.hql`ska den här platsen vara .

        ![Ange information om Hive-skript för pipelinen](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "Ange information om Hive-skript för pipelinen")

    1. Under **Avancerade** > **parametrar**väljer du Fyll automatiskt **från skriptet**. Det här alternativet söker efter alla parametrar i Hive-skriptet som kräver värden vid körning.

    1. Lägg **value** till den befintliga mappen i formatet `wasbs://adfgetstarted@<StorageAccount>.blob.core.windows.net/outputfolder/`i textrutan värde . Sökvägen är skiftlägeskänslig. Det här är sökvägen där utskriften av skriptet ska lagras. Schemat `wasbs` är nödvändigt eftersom lagringskonton nu har säker överföring krävs aktiverat som standard.

        ![Ange parametrar för Hive-skriptet](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "Ange parametrar för Hive-skriptet")

1. Välj **Validera** om du vill validera pipelinen. Markera **>>** knappen (högerpil) för att stänga valideringsfönstret.

    ![Verifiera Azure Data Factory-pipelinen](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "Verifiera Azure Data Factory-pipelinen")

1. Slutligen väljer du **Publicera alla** för att publicera artefakterna till Azure Data Factory.

    ![Publicera Azure Data Factory-pipelinen](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "Publicera Azure Data Factory-pipelinen")

## <a name="trigger-a-pipeline"></a>Utlösa en pipeline

1. Välj **Lägg till utlösare** > **utlösare nu**i verktygsfältet på designerytan .

    ![Utlösa Azure Data Factory-pipelinen](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "Utlösa Azure Data Factory-pipelinen")

2. Välj **OK** i popup-sidofältet.

## <a name="monitor-a-pipeline"></a>Övervaka en pipeline

1. Växla till fliken **Övervaka** till vänster. En pipelinekörning visas i listan **Pipeline Runs** (Pipelinekörningar). Lägg märke till körningens status under kolumnen **Status.**

    ![Övervaka Azure Data Factory-pipelinen](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "Övervaka Azure Data Factory-pipelinen")

1. Välj **Uppdatera** för att uppdatera statusen.

1. Du kan också välja ikonen **Visa aktivitetskörning** om du vill visa den aktivitetskörning som är associerad med pipelinen. I skärmbilden nedan ser du bara en aktivitetskörning eftersom det bara finns en aktivitet i pipelinen som du skapade. Om du vill växla tillbaka till föregående vy väljer du **Pipelines** högst upp på sidan.

    ![Övervaka pipeline-aktiviteten i Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "Övervaka pipeline-aktiviteten i Azure Data Factory")

## <a name="verify-the-output"></a>Verifiera utdata

1. För att verifiera utdata navigerar du i Azure-portalen till det lagringskonto som du använde för den här självstudien. Följande mappar eller behållare bör visas:

    * Du ser en **adfgerstarted/outputfolder** som innehåller utdata från Hive-skriptet som kördes som en del av pipelinen.

    * Du ser en **adfhdidatafactory-\<länkad-tjänst-namn\<>- tidsstämpel>** behållare. Den här behållaren är standardlagringsplatsen för HDInsight-klustret som skapades som en del av pipeline-körningen.

    * Du ser en **adfjobs-behållare** som har jobbloggarna för Azure Data Factory.  

        ![Verifiera Azure Data Factory-pipelineutdata](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "Verifiera Azure Data Factory-pipelineutdata")

## <a name="clean-up-resources"></a>Rensa resurser

När HDInsight-klustret skapas på begäran behöver du inte uttryckligen ta bort HDInsight-klustret. Klustret tas bort baserat på den konfiguration som du angav när pipelinen skapas. Även efter att klustret har tagits bort fortsätter lagringskontona som är associerade med klustret att existera. Det här beteendet är avsiktligt så att du kan behålla dina data intakta. Om du inte vill spara data kan du dock ta bort lagringskontot som du skapade.

Du kan också ta bort hela resursgruppen som du skapade för den här självstudien. Detta tar bort lagringskontot och Azure Data Factory som du skapade.

### <a name="delete-the-resource-group"></a>Ta bort resursgruppen

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj **Resursgrupper** i den vänstra rutan.
1. Välj det resursgruppsnamn som du skapade i PowerShell-skriptet. Använd filtret om du har för många resursgrupper i listan. Resursgruppen öppnas.
1. På panelen **Resurser** ska du ha standardlagringskontot och datafabriken i listan om du inte delar resursgruppen med andra projekt.
1. Välj **Ta bort resursgrupp**. Om du gör det tas lagringskontot och de data som lagras i lagringskontot bort.

    ![Resursgrupp för ta bort Azure-portal](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "Ta bort resursgrupp")

1. Ange resursgruppsnamnet för att bekräfta borttagningen och välj sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig hur du använder Azure Data Factory för att skapa HDInsight-kluster på begäran och köra [Apache Hive-jobb.](https://hive.apache.org/) Gå vidare till nästa artikel för att lära dig hur du skapar HDInsight-kluster med anpassad konfiguration.

> [!div class="nextstepaction"]
> [Skapa Azure HDInsight-kluster med anpassad konfiguration](hdinsight-hadoop-provision-linux-clusters.md)
