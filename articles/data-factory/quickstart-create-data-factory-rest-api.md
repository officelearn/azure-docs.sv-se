---
title: Skapa en Azure-datafabrik med REST API | Microsoft Docs
description: Skapa en Azure-datafabrik för att kopiera data från en plats i Azure Blob Storage till en annan plats.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: rest-api
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 27754395e0be5bffab3c63b42fff9ddbf93cf338
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084617"
---
# <a name="create-an-azure-data-factory-and-pipeline-by-using-the-rest-api"></a>Skapa en Azure -datafabrik och pipeline med REST API
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuell version](quickstart-create-data-factory-rest-api.md)

Azure Data Factory är en molnbaserad dataintegreringstjänst som gör att du kan skapa datadrivna arbetsflöden i molnet för att samordna och automatisera dataförflyttning och dataomvandling. Med Azure Data Factory kan du skapa och schemalägga datadrivna arbetsflöden (kallas pipelines) som kan föra in data från olika datalager, bearbeta/omvandla data med beräkningstjänster som Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics och Azure Machine Learning och publicera utgående data till datalager som Azure SQL Data Warehouse för BI-program (business intelligence) kan använda. 

Den här snabbstarten beskriver hur du använder REST API till att skapa en Azure-datafabrik. Pipeline i den här datafabriken kopierar data från en plats till en annan i Azure Blob Storage.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration**. Om du inte har en prenumeration kan du skapa ett [kostnadsfritt utvärderingskonto](http://azure.microsoft.com/pricing/free-trial/).
* **Azure Storage-konto**. Du kan använda blob-lagringen som **källa** och **mottagare** för datalagringen. Om du inte har ett Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln om att [skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account) .
* Skapa en **blob-behållare** i Blob Storage, skapa en **indatamapp** i behållaren och ladda upp några filer till mappen. Du kan använda verktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att ansluta till Azure Blob Storage, skapa en blob-behållare, ladda upp en indatafil och verifiera utdatafilen.
* **Installera Azure PowerShell**. Följ instruktionerna i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-azurerm-ps). I den här snabbstarten används PowerShell för att göra REST API-anrop.
* **Skapa en app i Azure Active Directory** med hjälp av [den här instruktionen](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Notera följande värden som du använder i senare steg: **program-ID**, **autentiseringsnyckel** och **klient-ID**. Tilldela appen rollen **Deltagare**.

## <a name="set-global-variables"></a>Ange globala variabler

1. Starta **PowerShell**. Låt Azure PowerShell vara öppet tills du är klar med snabbstarten. Om du stänger och öppnar det igen måste du köra kommandona en gång till.

    Kör följande kommando och ange det användarnamn och lösenord som du använder för att logga in i Azure Portal:
        
    ```powershell
    Connect-AzureRmAccount
    ```        
    Kör följande kommando för att visa alla prenumerationer för det här kontot:

    ```powershell
    Get-AzureRmSubscription
    ```
    Kör följande kommando för att välja den prenumeration som du vill arbeta med. Ersätt **SubscriptionId** med ID:t för din Azure-prenumeration:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
2. Kör följande kommandon när du har ersatt platshållarna med dina egna värden, för att ange globala variabler som ska användas i senare steg.

    ```powershell
    $tenantID = "<your tenant ID>"
    $appId = "<your application ID>"
    $authKey = "<your authentication key for the application>"
    $subsId = "<your subscription ID to create the factory>"
    $resourceGroup = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $apiVersion = "2017-09-01-preview"
    ```

## <a name="authenticate-with-azure-ad"></a>Autentisera med Azure AD

Kör följande kommandon för att autentisera med Azure Active Directory (AAD):

```powershell
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]"https://login.microsoftonline.com/${tenantId}"
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($appId, $authKey)
$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
} 
```

## <a name="create-a-data-factory"></a>Skapa en datafabrik

Skapa en datafabrik genom att köra följande kommandon:

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}?api-version=${apiVersion}"
$body = @"
{
    "name": "$dataFactoryName",
    "location": "East US",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.

    ```
    Data factory name "ADFv2QuickStartDataFactory" is not available.
    ```
* Om du vill se en lista med Azure-regioner där Data Factory är tillgängligt för närvarande markerar du de regioner du är intresserad av på följande sida. Expandera sedan **Analytics** och leta rätt på **Data Factory**: [Tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/). Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.

Här är exempelsvaret:

```json

{
    "name":  "<dataFactoryName>",
    "tags": {

            },
    "properties":  {
        "provisioningState":  "Succeeded",
        "loggingStorageAccountKey":  "**********",
        "createTime":  "2017-09-14T06:22:59.9106216Z",
        "version":  "2017-09-01-preview"
    },
    "identity":  {
        "type":  "SystemAssigned",
        "principalId":  "<service principal ID>",
        "tenantId":  "<tenant ID>"
    },
    "id":  "dataFactoryName",
    "type":  "Microsoft.DataFactory/factories",
    "location":  "East US"
} 

```

## <a name="create-linked-services"></a>Skapa länkade tjänster

Du kan skapa länkade tjänster i en datafabrik för att länka ditt datalager och beräkna datafabrik-tjänster. I den här snabbstarten behöver du bara skapa en Azure Storage-länkad tjänst som både kopia på källa och mottagarlagring, med namnet "AzureStorageLinkedService" i exemplet.

Kör följande kommandon för att skapa en länkad tjänst med namnet **AzureStorageLinkedService**:

Ersätt &lt;accountName&gt; och &lt;accountKey&gt; med namnet och nyckeln för ditt Azure-lagringskonto innan du kör kommandona.

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/linkedservices/AzureStorageLinkedService?api-version=${apiVersion}"
$body = @"
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
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Här är exempel på utdata:

```json
{
    "id":  "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/linkedservices/AzureStorageLinkedService",
    "name":  "AzureStorageLinkedService",
    "properties":  {
                       "type":  "AzureStorage",
                       "typeProperties":  {
                                              "connectionString":  "@{value=**********; type=SecureString}"
                                          }
                   },
    "etag":  "0000c552-0000-0000-0000-59b1459c0000"
}
```

## <a name="create-datasets"></a>Skapa datauppsättningar

Du definierar en datauppsättning som representerar data som ska kopieras från en källa till en mottagare. I det här exemplet refererar denna blob-datauppsättning till den Azure Storage-länkade tjänst som du skapar i föregående steg. Datauppsättningen tar en parameter vars värde anges i en aktivitet som förbrukar datauppsättningen. Parametern används för att konstruera "folderPath" som pekar mot där data finns/lagras.

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/datasets/BlobDataset?api-version=${apiVersion}"
$body = @"
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
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Här är exempel på utdata:

```json
{
    "id":  "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/BlobDataset",
    "name":  "BlobDataset",
    "properties":  {
                       "type":  "AzureBlob",
                       "typeProperties":  {
                                              "folderPath":  "@{value=@{dataset().path}; type=Expression}"
                                          },
                       "linkedServiceName":  {
                                                 "referenceName":  "AzureStorageLinkedService",
                                                 "type":  "LinkedServiceReference"
                                             },
                       "parameters":  {
                                          "path":  "@{type=String}"
                                      }
                   },
    "etag":  "0000c752-0000-0000-0000-59b1459d0000"
}
```

## <a name="create-pipeline"></a>Skapa pipeline

I det här exemplet innehåller denna pipeline en aktivitet och tar två parametrar – sökvägen till indata-blob och sökvägen till utdata-blob. Värdena för dessa parametrar anges när pipeline utlöses/körs. Kopieringsaktiviteten refererar till samma blobdatauppsättning som skapats i föregående steg som indata och utdata. När datauppsättningen används som indatauppsättning anges indatasökvägen. Och när datauppsättningen används som utdatauppsättning anges utdatasökvägen. 

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelines/Adfv2QuickStartPipeline?api-version=${apiVersion}"
$body = @"
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
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Här är exempel på utdata:

```json
{
    "id":  "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/pipelines/Adfv2QuickStartPipeline",
    "name":  "Adfv2QuickStartPipeline",
    "properties":  {
                       "activities":  [
                                          "@{name=CopyFromBlobToBlob; type=Copy; inputs=System.Object[]; outputs=System.Object[]; typeProperties=}"
                                      ],
                       "parameters":  {
                                          "inputPath":  "@{type=String}",
                                          "outputPath":  "@{type=String}"
                                      }
                   },
    "etag":  "0000c852-0000-0000-0000-59b1459e0000"
}
```

## <a name="create-pipeline-run"></a>Skapa pipelinekörning

I det här steget anger du värden för parametrarna **inputPath** och **outputPath** som anges i pipeline med de faktiska värdena för sökvägarna för käll- och mottagar-blob och utlöser en pipelinekörning. Pipelinekörningens ID som returneras i svarstexten används i senare övervaknings-API.

Ersätt värdet för **inputPath** med **outputPath** med din sökväg till käll- och mottagar-blob att kopiera data från och till innan du sparar filen.


```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelines/Adfv2QuickStartPipeline/createRun?api-version=${apiVersion}"
$body = @"
{
    "inputPath": "<the path to existing blob(s) to copy data from, e.g. containername/path>",
    "outputPath": "<the blob path to copy data to, e.g. containername/path>"
}
"@
$response = Invoke-RestMethod -Method POST -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
$runId = $response.runId
```

Här är exempel på utdata:

```json
{
    "runId":  "2f26be35-c112-43fa-9eaa-8ba93ea57881"
}
```

## <a name="monitor-pipeline"></a>Övervaka pipeline

1. Kör följande skript för att kontinuerligt kontrollera pipelinekörningens status tills kopieringen av data är klar.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```

    Här är exempel på utdata:

    ```json
    {
        "key":  "000000000-0000-0000-0000-00000000000",
        "timestamp":  "2017-09-07T13:12:39.5561795Z",
        "runId":  "000000000-0000-0000-0000-000000000000",
        "dataFactoryName":  "<dataFactoryName>",
        "pipelineName":  "Adfv2QuickStartPipeline",
        "parameters":  [
                        "inputPath: <inputBlobPath>",
                        "outputPath: <outputBlobPath>"
                    ],
        "parametersCount":  2,
        "parameterNames":  [
                            "inputPath",
                            "outputPath"
                        ],
        "parameterNamesCount":  2,
        "parameterValues":  [
                                "<inputBlobPath>",
                                "<outputBlobPath>"
                            ],
        "parameterValuesCount":  2,
        "runStart":  "2017-09-07T13:12:00.3710792Z",
        "runEnd":  "2017-09-07T13:12:39.5561795Z",
        "durationInMs":  39185,
        "status":  "Succeeded",
        "message":  ""
    }
    ```

2. Kör följande skript för att hämta körningsinformation för kopieringsaktiviteten, till exempel storleken på data som lästs/skrivits.

    ```PowerShell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

    Här är exempel på utdata:

    ```json
    {
        "value":  [
                    {
                        "id":  "000000000-0000-0000-0000-00000000000",
                        "timestamp":  "2017-09-07T13:12:38.4780542Z",
                        "pipelineRunId":  "000000000-0000-00000-0000-0000000000000",
                        "pipelineName":  "Adfv2QuickStartPipeline",
                        "status":  "Succeeded",
                        "failureType":  "",
                        "linkedServiceName":  "",
                        "activityName":  "CopyFromBlobToBlob",
                        "activityType":  "Copy",
                        "activityStart":  "2017-09-07T13:12:02.3299261Z",
                        "activityEnd":  "2017-09-07T13:12:38.4780542Z",
                        "duration":  36148,
                        "input":  "@{source=; sink=}",
                        "output":  "@{dataRead=331452208; dataWritten=331452208; copyDuration=22; throughput=14712.9; errors=System.Object[]; effectiveIntegrationRuntime=DefaultIntegrationRuntime (West US); usedDataIntegrationUnits=2; billedDuration=22}",
                        "error":  "@{errorCode=; message=; failureType=; target=CopyFromBlobToBlob}"
                    }
                ]
    }
    ```

## <a name="verify-the-output"></a>Verifiera utdata

Använd Azure Storage Explorer till att kontrollera att bloben/blobarna kopieras till "outputBlobPath" från "inputBlobPath" som du angav när du skapade en pipelinekörning.

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
