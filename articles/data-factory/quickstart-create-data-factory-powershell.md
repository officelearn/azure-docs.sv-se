---
title: "Kopiera data i Blob Storage med hjälp av Azure Data Factory | Microsoft Docs"
description: "Skapa en Azure-datafabrik för att kopiera data från en mapp till en annan mapp i Azure Blob Storage till en annan plats i samma Blob Storage."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: hero-article
ms.date: 11/16/2017
ms.author: jingwang
ms.openlocfilehash: 254dcb6642afc19f434df837c9073d2dd7314313
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2017
---
# <a name="create-an-azure-data-factory-using-powershell"></a>Skapa en Azure-datafabrik med hjälp av PowerShell 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version 2 – förhandsversion](quickstart-create-data-factory-powershell.md)

Den här snabbstarten beskriver hur du använder PowerShell till att skapa en Azure-datafabrik. Den pipeline du skapar i den här datafabriken **kopierar** data från en mapp till en annan mapp i Azure Blob Storage. Om du vill se en självstudie som visar hur du **omvandlar** data med Azure Data Factory går du till [Tutorial: Transform data using Spark](transform-data-using-spark.md) (Självstudie: Omvandla data med Spark). 

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Läs [get started with Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (kom igång med Data Factory version 1) om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig.
>
> Den här artikeln ger inte någon detaljerad introduktion till Azure Data Factory-tjänsten. En introduktion till Azure Data Factory-tjänsten finns i [Introduktion till Azure Data Factory](introduction.md).

## <a name="prerequisites"></a>Krav

### <a name="azure-subscription"></a>Azure-prenumeration
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

### <a name="azure-roles"></a>Azure-roller
Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna **deltagare** eller **ägare**, eller vara **administratör** för Azure-prenumerationen. I Azure-portalen klickar du på ditt **användarnamn** i det övre högra hörnet och väljer **Behörigheter** för att visa de behörigheter du har i prenumerationen. Om du har åtkomst till flera prenumerationer väljer du rätt prenumeration. För exempel på instruktioner om hur du lägger till en användare till en roll läser du artikeln [Lägg till roller](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="azure-storage-account"></a>Azure Storage-konto
I den här snabbstarten använder du ett allmänt Azure Storage-konto (Blob Storage, för att vara specifik) som datalager för både **källa** och **destination**. Om du inte har något allmänt Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account). 

#### <a name="get-storage-account-name-and-account-key"></a>Hämta lagringskontots namn och åtkomstnyckel
Du använder namnet och nyckeln för Azure Storage-kontot i den här snabbstarten. Följande procedur innehåller steg för att få fram namnet och nyckeln för ditt lagringskonto. 

1. Öppna webbläsaren och gå till [Azure Portal](https://portal.azure.com). Logga in med ditt Azure-användarnamn och lösenord. 
2. Klicka på **Fler tjänster >** i den vänstra menyn, filtrera på nyckelordet **Lagring** och välj **Lagringskonton**.

    ![Sök efter lagringskontot](media/quickstart-create-data-factory-powershell/search-storage-account.png)
3. Filtrera på ditt lagringskonto (om det behövs) i listan med lagringskonton och välj sedan **ditt lagringskonto**. 
4. Gå till sidan **Lagringskonto** väljer du **Åtkomstnycklar** i menyn.

    ![Hämta lagringskontots namn och nyckel](media/quickstart-create-data-factory-powershell/storage-account-name-key.png)
5. Kopiera värdena från fälten med **lagringskontots namn** och **nyckel 1** till Urklipp. Klistra in dem i Anteckningar eller något annat redigeringsprogram och spara.  

#### <a name="create-input-folder-and-files"></a>Skapa indatamapp och filer
Det här avsnittet förutsätter att du har en blobbehållare med namnet **adftutorial** i Azure Blob Storage. Skapa en mapp med namnet **input** i behållaren och ladda upp en exempelfil i indatamappen. 

1. På sidan **Lagringskonto** växlar du till **Översikt** och klickar på **Blobbar**. 

    ![Alternativet Välj blobar](media/quickstart-create-data-factory-powershell/select-blobs.png)
2. På sidan **Blob Service** klickar du på **+ Container** (+Behållare) i verktygsfältet. 

    ![Lägga till behållarknapp](media/quickstart-create-data-factory-powershell/add-container-button.png)    
3. I dialogrutan **Ny behållare** anger du **adftutorial** som namn och klickar på **OK**. 

    ![Ange namn på behållare](media/quickstart-create-data-factory-powershell/new-container-dialog.png)
4. Klicka på **adftutorial** i listan över behållare. 

    ![Välja behållaren](media/quickstart-create-data-factory-powershell/seelct-adftutorial-container.png)
1. På sidan **Behållare** klickar du på **Ladda upp** i verktygsfältet.  

    ![Knappen för överföring](media/quickstart-create-data-factory-powershell/upload-toolbar-button.png)
6. På sidan **Ladda upp blob** klickar du på **Avancerat**.

    ![Klicka på länken Avancerat](media/quickstart-create-data-factory-powershell/upload-blob-advanced.png)
7. Öppna **Anteckningar** och skapa en fil med namnet **emp.txt** med följande innehåll: Spara den i mappen **c:\ADFv2QuickStartPSH**: Skapa mappen **ADFv2QuickStartPSH** om den inte redan finns.
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. På sidan **Ladda upp blob** i Azure-portalen bläddrar du och väljer filen **emp.txt** för fältet **Filer**. 
9. Ange **input** som värdet som **Ladda upp till mapp** arkiverade. 

    ![Ladda upp blobinställningar](media/quickstart-create-data-factory-powershell/upload-blob-settings.png)    
10. Kontrollera att mappen är **input** och att filen är **emp.txt** och klicka på **Ladda upp**.
11. Du bör se filen **emp.txt** och uppladdningens status i listan. 
12. Stäng sidan **Ladda upp blob** genom att klicka på **X** i hörnet. 

    ![Stänga sidan Ladda upp blob](media/quickstart-create-data-factory-powershell/close-upload-blob.png)
1. Låt **behållarsidan** vara öppen. Du kommer att använda den för att bekräfta utdata i slutet av snabbstarten. 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Installera Azure PowerShell
Installera den senaste versionen av Azure PowerShell om du inte har den på din dator. 

1. Öppna webbläsaren och gå till sidan [Ladda ned Azure-SDK:er och verktyg](https://azure.microsoft.com/downloads/). 
2. Klicka på **Windows-installation** i avsnittet **Kommandoradsverktyg** -> **PowerShell**. 
3. Kör **MSI**-filen för att installera Azure PowerShell. 

Mer detaljerade anvisningar finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-azure-powershell"></a>Logga in på Azure PowerShell

1. Starta **PowerShell** på din dator. Låt Azure PowerShell vara öppet tills du är klar med snabbstarten. Om du stänger och öppnar det igen måste du köra kommandona en gång till.

    ![Starta PowerShell](media/quickstart-create-data-factory-powershell/search-powershell.png)
1. Kör följande kommando och ange användarnamnet och lösenordet som du använder för att logga in på Azure-portalen:
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Om du har flera Azure-prenumerationer kör du följande kommando för att visa alla prenumerationer kopplade till det här kontot:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Kör följande kommando för att välja den prenumeration som du vill arbeta med. Ersätt **SubscriptionId** med ID:t för din Azure-prenumeration:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Skapa en datafabrik
1. Definiera en variabel för resursgruppens namn som du kan använda senare i PowerShell-kommandon. Kopiera följande kommandotext till PowerShell, ange ett namn för [Azure-resursgruppen](../azure-resource-manager/resource-group-overview.md), sätt dubbla citattecken omkring namnet och kör sedan kommandot. Till exempel: `"adfrg"`.
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. Definiera en variabel för datafabrikens namn. 

    ```powershell
    $dataFactoryName = "<Specify a name for the data factory. It must be globally unique.>";
    ```
1. Definiera en variabel för datafabrikens plats: 

    ```powershell
    $location = "East US"
    ```
4. Kör följande kommando för att skapa en Azure-resursgrupp: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$resourceGroupName` ett annat värde och kör kommandot igen. 
5. Kör följande cmdlet av typen **Set-AzureRmDataFactoryV2** för att skapa en datafabrik: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna **deltagare** eller **ägare**, eller vara **administratör** för Azure-prenumerationen.
* För närvarande kan du endast skapa datafabriker i Data Factory version 2 i regionerna USA, östra, USA östra 2 och Europa, västra. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.

## <a name="create-a-linked-service"></a>Skapa en länkad tjänst

Skapa länkade tjänster i en datafabrik för att länka ditt datalager och beräkna datafabrik-tjänster. I den här snabbstarten skapar du en länkad Azure Storage-tjänst som lagrar både källa och mottagare. Den länkade tjänsten har anslutningsinformationen som Data Factory-tjänsten använder vid körning för att ansluta till den.

1. Skapa en JSON-fil med namnet **AzureStorageLinkedService.json** i mappen **C:\ADFv2QuickStartPSH** med följande innehåll: (skapa mappen ADFv2QuickStartPSH om den inte redan finns.). 

    > [!IMPORTANT]
    > Ersätt &lt;accountName&gt; och &lt;accountKey&gt; med namnet och nyckeln för ditt Azure-lagringskonto innan du sparar filen.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net",
                    "type": "SecureString"
                }
            }
        }
    }
    ```

2. I **Azure PowerShell** växlar du till mappen **ADFv2QuickStartPSH**.

3. Kör cmdleten **Set-AzureRmDataFactoryV2LinkedService** för att skapa den länkade tjänsten: **AzureStorageLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    Här är exempel på utdata:

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-a-dataset"></a>Skapa en datamängd
I det här steget definierar du en datamängd som representerar data som ska kopieras från en källa till en mottagare. Datamängden är av typen **AzureBlob**. Den refererar till den **Azure Storage-länkade tjänst** som du skapade i föregående steg. Det krävs en parameter för att konstruera egenskapen **folderPath**. För en inkommande datamängd skickar kopieringsaktiviteten i pipelinen den inkommande sökvägen som värde för parametern. För en utgående datamängd skickar kopieringsaktiviteten den utgående sökvägen som värde för parametern. 

1. Skapa en JSON-fil med namnet **BlobDataset.json** i mappen **C:\ADFv2QuickStartPSH** med följande innehåll:

    ```json
    {
        "name": "BlobDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": {
                    "value": "@{dataset().path}",
                    "type": "Expression"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "path": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Så här skapar du datauppsättningen: **BlobDataset**, kör cmdleten **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -DefinitionFile ".\BlobDataset.json"
    ```

    Här är exempel på utdata:

    ```
    DatasetName       : BlobDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>Skapa en pipeline
  
I den här snabbstarten skapar du en pipeline med en aktivitet som tar två parametrar – sökvägen till indata-blob och sökvägen till utdata-blob. Värdena för dessa parametrar anges när pipeline utlöses/körs. Kopieringsaktiviteten använder samma blobdatauppsättning som skapats i föregående steg som indata och utdata. När datauppsättningen används som indatauppsättning anges indatasökvägen. Och när datauppsättningen används som utdatauppsättning anges utdatasökvägen. 

1. Skapa en JSON-fil med namnet **Adfv2QuickStartPipeline.json** i mappen **C:\ADFv2QuickStartPSH** med följande innehåll:

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "inputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.inputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.outputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    }
                }
            ],
            "parameters": {
                "inputPath": {
                    "type": "String"
                },
                "outputPath": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Så här skapar du en pipeline: **Adfv2QuickStartPipeline**, kör cmdleten **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

    Här är exempel på utdata:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

## <a name="create-a-pipeline-run"></a>Skapa en pipelinekörning

I det här steget anger du värden för pipelineparametrarna:  **inputPath** och **outputPath** med faktiska värden för sökväg till käll- och mottagar-blob. Sedan skapar du en pipelinekörning med dessa argument. 

1. Skapa en JSON-fil med namnet **PipelineParameters.json** i mappen **C:\ADFv2QuickStartPSH** med följande innehåll:

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
    }
    ```
2. Kör cmdleten **Invoke-AzureRmDataFactoryV2Pipeline** för att skapa en pipelinekörning och ange parametervärdena. Cmdleten samlar även in pipelinekörningens ID för kommande övervakning.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Kör följande PowerShell-skript för att kontinuerligt kontrollera pipelinekörningens status tills kopieringen av data är klar. Kopiera/klistra in följande skript i PowerShell-fönstret och tryck på RETUR. 

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 10
    }
    ```

    Här är exempel på utdata för pipelinekörning:

    ```
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    RunId             : 0000000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 9/28/2017 8:28:38 PM
    Parameters        : {[inputPath, adftutorial/input], [outputPath, adftutorial/output]}
    RunStart          : 9/28/2017 8:28:14 PM
    RunEnd            : 9/28/2017 8:28:38 PM
    DurationInMs      : 24151
    Status            : Succeeded
    Message           :
    ```

    Om du ser felet:
    ```
    Activity CopyFromBlobToBlob failed: Failed to detect region of linked service 'AzureStorage' : 'AzureStorageLinkedService' with error '[Region Resolver] Azure Storage failed to get address for DNS. Warning: System.Net.Sockets.SocketException (0x80004005): No such host is known
    ```
    utför du följande steg: 
    1. I AzureStorageLinkedService.json bekräftar du att namnet och nyckeln för ditt Azure Storage Account stämmer. 
    2. Kontrollera att formatet på anslutningssträngen stämmer. Egenskaperna, till exempel AccountName och AccountKey separeras av tecknet semikolon (`;`). 
    3. Om du har hakparenteser kring kontonamnet och kontonyckeln ska du ta bort dem. 
    4. Här är en exempel-anslutningssträng: 

        ```json
        "connectionString": {
            "value": "DefaultEndpointsProtocol=https;AccountName=mystorageaccountname;AccountKey=mystorageacountkey;EndpointSuffix=core.windows.net",
            "type": "SecureString"
        }
        ```
    5. Återskapa den länkade tjänsten genom att följa stegen i avsnittet [Skapa en länkad tjänst](#create-a-linked-service). 
    6. Kör pipelinen igen via följande steg i avsnittet [Skapa en pipelinekörning](#create-a-pipeline-run). 
    7. Kör det aktuella övervakningskommandot igen för att övervaka den nya pipelinekörningen. 
1. Kör följande skript för att hämta körningsinformation för kopieringsaktiviteten, till exempel storleken på data som lästs/skrivits.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```
3. Bekräfta att du ser utdata som liknar följande exempelutdata för ett aktivitetskörningsresultat:

    ```json
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityRunStart  : 9/28/2017 8:28:18 PM
    ActivityRunEnd    : 9/28/2017 8:28:36 PM
    DurationInMs      : 18095
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 38
    "dataWritten": 38
    "copyDuration": 7
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    "usedCloudDataMovementUnits": 2
    "billedDuration": 14
    ```

## <a name="verify-the-output"></a>Verifiera utdata
Pipelinen skapar automatiskt utdatamappen i blobbehållaren adftutorial. Filen emp.txt kopieras från indatamappen till utdatamappen. 

1. På behållarsidan **adftutorial** i Azure-portalen klickar du på **Uppdatera** för att se utdatamappen. 
    
    ![Uppdatera](media/quickstart-create-data-factory-powershell/output-refresh.png)
2. Klicka på **output** i mapplistan. 
2. Kontrollera att **emp.txt** har kopierats till utdatamappen. 

    ![Uppdatera](media/quickstart-create-data-factory-powershell/output-file.png)

## <a name="clean-up-resources"></a>Rensa resurser
Du kan rensa de resurser som du skapade i snabbstarten på två sätt. Du kan ta bort den [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) som innehåller alla resurser i resursgruppen. Om du vill behålla de andra resurserna intakta ska du bara ta bort den datafabrik du har skapat i den här självstudiekursen.

När du tar bort en resursgrupp tas alla resurser inklusive datafabriker i den bort. Kör följande kommando om du vill ta bort hela resursgruppen: 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

Om du bara vill ta bort datafabriken och inte hela resursgruppen kör du följande kommando: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Nästa steg
Pipeline i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Gå igenom [självstudiekurserna](tutorial-copy-data-dot-net.md) om du vill lära dig hur du använder Data Factory i fler scenarier. 
