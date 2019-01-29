---
title: Kopiera data i Blob Storage med hjälp av Azure Data Factory | Microsoft Docs
description: Skapa en Azure-datafabrik för att kopiera data från en plats i Azure Blob Storage till en annan plats.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: quickstart
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: fc2feff700220f57f82e8ac0a310843dd9b0cae6
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54436611"
---
# <a name="create-an-azure-data-factory-using-powershell"></a>Skapa en Azure-datafabrik med hjälp av PowerShell 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuell version](quickstart-create-data-factory-powershell.md)

Den här snabbstarten beskriver hur du använder PowerShell till att skapa en Azure-datafabrik. Den pipeline du skapar i den här datafabriken **kopierar** data från en mapp till en annan mapp i Azure Blob Storage. En självstudie om hur du **transformerar** data med hjälp av Azure Data Factory finns i [Självstudie: Transformera data med Spark](transform-data-using-spark.md). 

> [!NOTE]
> Den här artikeln ger inte någon detaljerad introduktion till Azure Data Factory-tjänsten. En introduktion till Azure Data Factory-tjänsten finns i [Introduktion till Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="azure-powershell"></a>Azure PowerShell
Installera de senaste Azure PowerShell-modulerna enligt instruktionerna i [Installera och konfigurera Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

#### <a name="log-in-to-powershell"></a>Logga in på PowerShell

1. Starta **PowerShell** på din dator. Låt PowerShell vara öppet tills du är klar med snabbstarten. Om du stänger och öppnar det igen måste du köra kommandona en gång till.
2. Kör följande kommando och ange användarnamnet och lösenordet som du använder för att logga in på Azure-portalen:
       
    ```powershell
    Connect-AzureRmAccount
    ```        
3. Kör följande kommando för att visa alla prenumerationer för det här kontot:

    ```powershell
    Get-AzureRmSubscription
    ```
4. Om du har flera Azure-prenumerationer associerade till ditt konto kör du följande kommando för att välja den prenumeration du vill arbeta med. Ersätt **SubscriptionId** med ID:t för din Azure-prenumeration:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Skapa en datafabrik
1. Definiera en variabel för resursgruppens namn som du kan använda senare i PowerShell-kommandon. Kopiera följande kommandotext till PowerShell, ange ett namn för [Azure-resursgruppen](../azure-resource-manager/resource-group-overview.md), sätt dubbla citattecken omkring namnet och kör sedan kommandot. Till exempel: `"ADFQuickStartRG"`. 
   
     ```powershell
    $resourceGroupName = "ADFQuickStartRG";
    ```

    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$ResourceGroupName` ett annat värde och kör kommandot igen
2. Kör följande kommando för att skapa en Azure-resursgrupp: 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'East US'
    ``` 
    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$ResourceGroupName` ett annat värde och kör kommandot igen. 
3. Definiera en variabel för datafabrikens namn. 

    > [!IMPORTANT]
    >  Uppdateringen av datafabrikens namn måste vara unikt globalt. Till exempel ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFQuickStartFactory";
    ```

5. För att skapa datafabriken kör du följande **Set-AzureRmDataFactoryV2**-cmdlet med hjälp av platsen och egenskapen ResourceGroupName från variabeln $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna **deltagare** eller **ägare**, eller vara **administratör** för Azure-prenumerationen.
* Om du vill se en lista med Azure-regioner där Data Factory är tillgängligt för närvarande markerar du de regioner du är intresserad av på följande sida. Expandera sedan **Analytics** och leta rätt på **Data Factory**: [Produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/). Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.

## <a name="create-a-linked-service"></a>Skapa en länkad tjänst

Skapa länkade tjänster i en datafabrik för att länka ditt datalager och beräkna datafabrik-tjänster. I den här snabbstarten skapar du en länkad Azure Storage-tjänst som lagrar både källa och mottagare. Den länkade tjänsten har anslutningsinformationen som Data Factory-tjänsten använder vid körning för att ansluta till den.

1. Skapa en JSON-fil med namnet **AzureStorageLinkedService.json** i mappen **C:\ADFv2QuickStartPSH** med följande innehåll: (Skapa mappen ADFv2QuickStartPSH om den inte redan finns.) 

    > [!IMPORTANT]
    > Ersätt &lt;accountName&gt; och &lt;accountKey&gt; med namnet och nyckeln för ditt Azure Storage-konto innan du sparar filen.

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
    Om du använder Anteckningar ska du välja **Alla filer** för det **filformat** som anges i dialogrutan **Spara som**. Annars kan tillägget `.txt` läggas till för filen. Till exempel `AzureStorageLinkedService.json.txt`. Om du skapar en fil i Utforskaren innan du öppnar den i Anteckningar kanske du inte ser tillägget `.txt` eftersom alternativet för att **dölja tillägg för alla kända filtyper** är valt som standard. Ta bort tillägget `.txt` innan du fortsätter till nästa steg.
2. I **PowerShell** växlar du till mappen **ADFv2QuickStartPSH**.

    ```powershell
    Set-Location 'C:\ADFv2QuickStartPSH'
    ```
3. Kör cmdleten **Set-AzureRmDataFactoryV2LinkedService** för att skapa den länkade tjänsten: **AzureStorageLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile ".\AzureStorageLinkedService.json"
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
                "folderPath": "@{dataset().path}"
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

2. Så här skapar du datamängden: **BlobDataset**, kör cmdleten **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "BlobDataset" -DefinitionFile ".\BlobDataset.json"
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

2. Så här skapar du pipelinen: **Adfv2QuickStartPipeline**, kör cmdleten **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile ".\Adfv2QuickStartPipeline.json"
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
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Kör följande PowerShell-skript för att kontinuerligt kontrollera pipelinekörningens status tills kopieringen av data är klar. Kopiera/klistra in följande skript i PowerShell-fönstret och tryck på RETUR. 

    ```powershell
    while ($True) {
        $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

        if ($Run) {
            if ($run.Status -ne 'InProgress') {
                Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
                $Run
                break
            }
            Write-Output  "Pipeline is running...status: InProgress"
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
    Write-Output "Activity run details:"
    $Result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineRunId $RunId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $Result

    Write-Output "Activity 'Output' section:"
    $Result.Output -join "`r`n"

    Write-Output "Activity 'Error' section:"
    $Result.Error -join "`r`n"
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
    "usedDataIntegrationUnits": 2
    "billedDuration": 14
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)] 

## <a name="next-steps"></a>Nästa steg
Pipeline i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Gå igenom [självstudiekurserna](tutorial-copy-data-dot-net.md) om du vill lära dig hur du använder Data Factory i fler scenarier. 
