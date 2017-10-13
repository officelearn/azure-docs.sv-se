---
title: Skapa en Azure-datafabrik med PowerShell | Microsoft Docs
description: "Skapa en Azure-datafabrik för att kopiera data från en plats i Azure Blob Storage till en annan plats i samma Blob Storage."
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
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: 1e9109581a1943a77e91e7fa034873dc2a15a5e6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-data-factory-and-pipeline-using-powershell"></a>Skapa en datafabrik och pipeline med PowerShell
Azure Data Factory är en molnbaserad dataintegreringstjänst som gör att du kan skapa datadrivna arbetsflöden i molnet för att samordna och automatisera dataförflyttning och dataomvandling. Med Azure Data Factory kan du skapa och schemalägga datadrivna arbetsflöden (kallas pipelines) som kan föra in data från olika datalager, bearbeta/omvandla data med beräkningstjänster som Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics och Azure Machine Learning och publicera utgående data till datalager som Azure SQL Data Warehouse för BI-program (business intelligence) kan använda. 

Den här snabbstarten beskriver hur du använder PowerShell till att skapa en Azure-datafabrik. Pipeline i den här datafabriken kopierar data från en plats till en annan i Azure Blob Storage.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Krav

* **Azure Storage-konto**. Du kan använda blob-lagringen både som **källa** och **mottagare** för datalagringen. Om du inte har något Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account). 
* Skapa en **blob-behållare** i Blob Storage, skapa en **indatamapp** i behållaren och ladda upp några filer till mappen. Du kan använda verktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att ansluta till Azure Blob Storage, skapa en blob-behållare, ladda upp en indatafil och verifiera utdatafilen.
* **Azure PowerShell**. Följ instruktionerna i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta **PowerShell**. Låt Azure PowerShell vara öppet tills du är klar med snabbstarten. Om du stänger och öppnar det igen måste du köra kommandona en gång till.

    Kör följande kommando och ange det användarnamn och lösenord som du använder för att logga in i Azure Portal:
        
    ```powershell
    Login-AzureRmAccount
    ```        
    Kör följande kommando för att visa alla prenumerationer för det här kontot:

    ```powershell
    Get-AzureRmSubscription
    ```
    Kör följande kommando för att välja den prenumeration som du vill arbeta med. Ersätt **SubscriptionId** med ID:t för din Azure-prenumeration:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
2. Kör cmdleten **Set-AzureRmDataFactoryV2** för att skapa en datafabrik. Ersätt platshållare med egna värden innan kommandot körs. Ersätt **platshållare** med dina egna värden. 

    Definiera en variabel för resursgruppens namn som du kan använda senare i PowerShell-kommandon. 
    ```powershell
    $resourceGroupName = "<your resource group to create the factory>";
    ```

    Definiera en variabel för datafabrikens namn som du kan använda senare i PowerShell-kommandon. 

    ```powershell
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    ```

    Skapa en datafabrik genom att köra följande kommandon. 
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

    Observera följande punkter:

    * Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * Om du vill skapa Data Factory-instanser måste du vara deltagare/administratör för Azure-prenumerationen.
    * För närvarande kan du endast skapa en datafabrik i Data Factory V2 i USA, östra. Datalagren (Azure Storage, Azure SQL Database etc.) och beräkningarna (HDInsight etc.) som används av datafabriker kan finnas i andra regioner.

## <a name="create-a-linked-service"></a>Skapa en länkad tjänst

Skapa länkade tjänster i en datafabrik för att länka ditt datalager och beräkna datafabrik-tjänster. I den här snabbstarten behöver du bara skapa en Azure Storage-länkad tjänst som ska användas som både kopia på källa och mottagarlagring, med namnet "AzureStorageLinkedService" i det här exemplet.

1. Skapa en JSON-fil med namnet **AzureStorageLinkedService.json** i mappen **C:\ADFv2QuickStartPSH** med följande innehåll: (skapa mappen ADFv2QuickStartPSH om den inte redan finns.). Ersätt &lt;accountName&gt; och &lt;accountKey&gt; med namnet och nyckeln för ditt Azure-lagringskonto innan du sparar filen.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
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

Du definierar en datauppsättning som representerar data som ska kopieras från en källa till en mottagare. I det här exemplet refererar denna blob-datauppsättning till den Azure Storage-länkade tjänst som du skapar i föregående steg. Datauppsättningen tar en parameter vars värde anges i en aktivitet som förbrukar datauppsättningen. Parametern används för att konstruera "folderPath" som pekar mot där data finns/lagras.

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

I det här exemplet innehåller denna pipeline en aktivitet och tar två parametrar – sökvägen till indata-blob och sökvägen till utdata-blob. Värdena för dessa parametrar anges när pipeline utlöses/körs. Kopieringsaktiviteten refererar till samma blobdatauppsättning som skapats i föregående steg som indata och utdata. När datauppsättningen används som indatauppsättning anges indatasökvägen. Och när datauppsättningen används som utdatauppsättning anges utdatasökvägen. 

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

    Ersätt värdet för ”inputPath” med ”outputPath” med din sökväg till käll- och mottagar-blob för att kopiera data från och till innan du sparar filen.

    ```json
    {
        "inputPath": "<the path to existing blob(s) to copy data from, e.g. containername/foldername>",
        "outputPath": "<the blob path to copy data to, e.g. containername/foldername>"
    }
    ```

2. Kör cmdleten **Invoke-AzureRmDataFactoryV2Pipeline** för att skapa en pipelinekörning och ange parametervärdena. Den samlar även in pipelinekörningens ID för kommande övervakning.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-a-pipeline-run"></a>Övervaka en pipelinekörning

1. Kör följande skript för att kontinuerligt kontrollera pipelinekörningens status tills kopieringen av data är klar.

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

        Start-Sleep -Seconds 30
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

2. Kör följande skript för att hämta körningsinformation för kopieringsaktiviteten, till exempel storleken på data som lästs/skrivits.

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
Använd verktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att kontrollera att bloben i inputBlobPath har kopierats till outputBlobPath.

## <a name="clean-up-resources"></a>Rensa resurser
Du kan rensa de resurser som du skapade i snabbstarten på två sätt. Du kan ta bort den [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) som innehåller alla resurser i resursgruppen. Om du vill behålla de andra resurserna intakta ska du bara ta bort den datafabrik du har skapat i den här självstudiekursen.

Kör följande kommando om du vill ta bort hela resursgruppen: 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

Kör följande kommando om du bara vill ta bort datafabriken: 

```powershell
Remove-AzureRmDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>Nästa steg
Pipeline i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Gå igenom [självstudiekurserna](tutorial-copy-data-dot-net.md) om du vill lära dig hur du använder Data Factory i fler scenarier. 