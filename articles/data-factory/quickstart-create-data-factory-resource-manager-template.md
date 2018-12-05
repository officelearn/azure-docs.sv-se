---
title: Skapa en Azure-datafabrik med hjälp av en Resource Manager-mall | Microsoft Docs
description: I de här självstudierna skapar du ett exempel på en Azure Data Factory-pipeline med hjälp av en Azure Resource Manager-mall.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/28/2018
ms.author: douglasl
ms.openlocfilehash: 2baadd0bcb5aba401e2dd6cec9a82ca401b3c9bd
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620497"
---
# <a name="tutorial-create-an-azure-data-factory-using-azure-resource-manager-template"></a>Självstudie: Skapa en Azure-datafabrik med hjälp av en Azure Resource Manager-mall
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Aktuell version](quickstart-create-data-factory-resource-manager-template.md) 

I den här snabbstarten får du se hur du skapar en Azure Data Factory med en Azure Resource Manager-mall. Den pipeline du skapar i den här datafabriken **kopierar** data från en mapp till en annan mapp i Azure Blob Storage. Om du vill se en självstudie som visar hur du **omvandlar** data med Azure Data Factory går du till [Tutorial: Transform data using Spark](transform-data-using-spark.md) (Självstudie: Omvandla data med Spark). 

> [!NOTE]
> Den här artikeln ger inte någon detaljerad introduktion till Azure Data Factory-tjänsten. En introduktion till Azure Data Factory-tjänsten finns i [Introduktion till Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="azure-powershell"></a>Azure PowerShell
Installera de senaste Azure PowerShell-modulerna enligt instruktionerna i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="resource-manager-templates"></a>Mallar för Resurshanteraren
Se [Skapa Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md) om du vill lära dig mer om Azure Resource Manager-mallar i allmänhet. 

Följande avsnitt innehåller den fullständiga Resource Manager-mallen för att definiera Data Factory-entiteter så att du snabbt kan gå igenom självstudien och testa mallen. Om du vill förstå hur varje Data Factory-entitet definieras kan du läsa avsnittet [Data Factory-entiteter i mallen](#data-factory-entities-in-the-template).

## <a name="data-factory-json"></a>Data Factory JSON 
Skapa en JSON-fil med namnet **ADFTutorialARM.json** i mappen **C:\ADFTutorial** med följande innehåll:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "dataFactoryName": {
            "type": "string",
            "metadata": {
                "description": "Name of the data factory. Must be globally unique."
            }
        },
        "dataFactoryLocation": {
            "type": "string",
            "allowedValues": [
                "East US",
                "East US 2",
                "West Europe"
            ],
            "defaultValue": "East US",
            "metadata": {
                "description": "Location of the data factory. Currently, only East US, East US 2, and West Europe are supported. "
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Azure storage account that contains the input/output data."
            }
        },
        "storageAccountKey": {
            "type": "securestring",
            "metadata": {
                "description": "Key for the Azure storage account."
            }
        },
        "blobContainer": {
            "type": "string",
            "metadata": {
                "description": "Name of the blob container in the Azure Storage account."
            }
        },
        "inputBlobFolder": {
            "type": "string",
            "metadata": {
                "description": "The folder in the blob container that has the input file."
            }
        },
        "inputBlobName": {
            "type": "string",
            "metadata": {
                "description": "Name of the input file/blob."
            }
        },
        "outputBlobFolder": {
            "type": "string",
            "metadata": {
                "description": "The folder in the blob container that will hold the transformed data."
            }
        },
        "outputBlobName": {
            "type": "string",
            "metadata": {
                "description": "Name of the output file/blob."
            }
        },
        "triggerStartTime": {
            "type": "string",
            "metadata": {
                "description": "Start time for the trigger."
            }
        },
        "triggerEndTime": {
            "type": "string",
            "metadata": {
                "description": "End time for the trigger."
            }
        }
    },
    "variables": {
        "azureStorageLinkedServiceName": "ArmtemplateStorageLinkedService",
        "inputDatasetName": "ArmtemplateTestDatasetIn",
        "outputDatasetName": "ArmtemplateTestDatasetOut",
        "pipelineName": "ArmtemplateSampleCopyPipeline",
        "triggerName": "ArmTemplateTestTrigger"
    },
    "resources": [{
        "name": "[parameters('dataFactoryName')]",
        "apiVersion": "2017-09-01-preview",
        "type": "Microsoft.DataFactory/factories",
        "location": "[parameters('dataFactoryLocation')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "resources": [{
                "type": "linkedservices",
                "name": "[variables('azureStorageLinkedServiceName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "AzureStorage",
                    "description": "Azure Storage linked service",
                    "typeProperties": {
                        "connectionString": {
                            "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]",
                            "type": "SecureString"
                        }
                    }
                }
            },
            {
                "type": "datasets",
                "name": "[variables('inputDatasetName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "AzureBlob",
                    "typeProperties": {
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'), '/')]",
                        "fileName": "[parameters('inputBlobName')]"
                    },
                    "linkedServiceName": {
                        "referenceName": "[variables('azureStorageLinkedServiceName')]",
                        "type": "LinkedServiceReference"
                    }
                }
            },
            {
                "type": "datasets",
                "name": "[variables('outputDatasetName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "AzureBlob",
                    "typeProperties": {
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'), '/')]",
                        "fileName": "[parameters('outputBlobName')]"
                    },
                    "linkedServiceName": {
                        "referenceName": "[variables('azureStorageLinkedServiceName')]",
                        "type": "LinkedServiceReference"
                    }
                }
            },
            {
                "type": "pipelines",
                "name": "[variables('pipelineName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]",
                    "[variables('inputDatasetName')]",
                    "[variables('outputDatasetName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "activities": [{
                        "type": "Copy",
                        "typeProperties": {
                            "source": {
                                "type": "BlobSource"
                            },
                            "sink": {
                                "type": "BlobSink"
                            }
                        },
                        "name": "MyCopyActivity",
                        "inputs": [{
                            "referenceName": "[variables('inputDatasetName')]",
                            "type": "DatasetReference"
                        }],
                        "outputs": [{
                            "referenceName": "[variables('outputDatasetName')]",
                            "type": "DatasetReference"
                        }]
                    }]
                }
            },
            {
                "type": "triggers",
                "name": "[variables('triggerName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]",
                    "[variables('inputDatasetName')]",
                    "[variables('outputDatasetName')]",
                    "[variables('pipelineName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "ScheduleTrigger",
                    "typeProperties": {
                        "recurrence": {
                            "frequency": "Hour",
                            "interval": 1,
                            "startTime": "[parameters('triggerStartTime')]",
                            "endTime": "[parameters('triggerEndTime')]",
                            "timeZone": "UTC"
                        }
                    },
                    "pipelines": [{
                        "pipelineReference": {
                            "type": "PipelineReference",
                            "referenceName": "ArmtemplateSampleCopyPipeline"
                        },
                        "parameters": {}
                    }]
                }
            }
        ]
    }]
}
```

## <a name="parameters-json"></a>JSON-parametrar
Skapa en JSON-fil med namnet **ADFTutorialARM-Parameters.json** som innehåller parametrar för Azure Resource Manager-mallen.  

> [!IMPORTANT]
> - Ange namn och nyckel för Azure Storage-kontot och parametrarna **storageAccountName** och **storageAccountKey** i den här parameterfilen. Du har skapat adftutorial-containern och laddat upp exempelfilen (emp.txt) till indatamappen i denna Azure Blob Storage. 
> - Ange ett globalt unikt namn för datafabriken för parametern **dataFactoryName**. Till exempel: ARMTutorialFactoryJohnDoe11282017. 
> - För **triggerStartTime** anger du aktuell dag i formatet: `2017-11-28T00:00:00`.
> - För **triggerEndTime** anger du nästa dag i formatet: `2017-11-29T00:00:00`. Du kan också kontrollera aktuell UTC-tid och ange nästa timma eller nästa två timmar som sluttid. Om UTC-tiden exempelvis nu är 1:32 anger du `2017-11-29:03:00:00` som sluttid. I det här fallet kör utlösaren pipelinen två gånger (vid 02:00 och 3:00).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataFactoryName": {
      "value": "<datafactoryname>"
    },    
    "dataFactoryLocation": {
      "value": "East US"
    },
    "storageAccountName": {
      "value": "<yourstroageaccountname>"
    },
    "storageAccountKey": {
      "value": "<yourstorageaccountkey>"
    },
    "blobContainer": {
      "value": "adftutorial"
    },
    "inputBlobFolder": {
      "value": "input"
    },
    "inputBlobName": {
      "value": "emp.txt"
    },
    "outputBlobFolder": {
      "value": "output"
    },
    "outputBlobName": {
      "value": "emp.txt"
    },
    "triggerStartTime": {
        "value": "2017-11-28T00:00:00. Set to today"
    },
    "triggerEndTime": {
        "value": "2017-11-29T00:00:00. Set to tomorrow"
    }
  }
}
```

> [!IMPORTANT]
> Du kan ha separata JSON-filer med parametrar för utveckling, testning och produktionsmiljöer som du kan använda med samma Data Factory JSON-mall. Genom att använda ett Power Shell-skript kan du automatisera distributionen av Data Factory-entiteter i dessa miljöer. 

## <a name="deploy-data-factory-entities"></a>Distribuera Data Factory-entiteter 
Kör följande kommando i PowerShell för att distribuera Data Factory-entiteter med hjälp av Resource Manager-mallen som du skapade tidigare i den här snabbstarten. 

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFTutorial\ADFTutorialARM.json -TemplateParameterFile C:\ADFTutorial\ADFTutorialARM-Parameters.json
```

Du ser utdata som liknar följande exempel: 

```
DeploymentName          : MyARMDeployment
ResourceGroupName       : ADFTutorialResourceGroup
ProvisioningState       : Succeeded
Timestamp               : 11/29/2017 3:11:13 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                 Type            Value
                          ===============      ============    ==========
                          dataFactoryName      String          <data factory name>
                          dataFactoryLocation  String          East US
                          storageAccountName   String          <storage account name>
                          storageAccountKey    SecureString
                          blobContainer        String          adftutorial
                          inputBlobFolder      String          input
                          inputBlobName        String          emp.txt
                          outputBlobFolder     String          output
                          outputBlobName       String          emp.txt
                          triggerStartTime     String          11/29/2017 12:00:00 AM
                          triggerEndTime       String          11/29/2017 4:00:00 AM

Outputs                 :
DeploymentDebugLogLevel :
```

## <a name="start-the-trigger"></a>Starta utlösarna

Med mallen kan du distribuera följande Data Factory-entiteter: 

- Länkad Azure-lagringstjänst
- Azure Blob-datamängder (indata och utdata)
- Pipeline med en kopieringsaktivitet
- Utlösare för att utlösa pipelinen

Den distribuerade utlösaren har stoppats. Ett sätt att starta utlösaren är att använda PowerShell-cmdleten **Start-AzureRmDataFactoryV2Trigger**. Följande procedur innehåller detaljerade anvisningar: 

1. I PowerShell-fönstret skapar du en variabel som anger namnet på resursgruppen. Kopiera följande kommando till PowerShell-fönstret och tryck på RETUR. Om du har angett ett annat resursgruppnamn för kommandot New-AzureRmResourceGroupDeployment uppdaterar du värdet här. 

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ``` 
1. Skapa en variabel som innehåller namnet på datafabriken. Ange samma namn som du angav i filen ADFTutorialARM-Parameters.json.   

    ```powershell
    $dataFactoryName = "<yourdatafactoryname>"
    ```
3. Ange en variabel för namnet på utlösaren. Namnet för utlösaren är hårdkodad i Resource Manager-mallfilen (ADFTutorialARM.json).

    ```powershell
    $triggerName = "ArmTemplateTestTrigger"
    ```
4. Hämta **utlösarens status** genom att köra följande PowerShell-kommando när du har angett namnet på din datafabrik och utlösare:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

    Här är exempel på utdata: 

    ```json
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ARMFactory1128
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Stopped
    ```
    
    Observera att körningstillståndet för utlösaren är **Stoppad**. 
5. **Starta utlösaren**. Utlösaren kör pipelinen som definieras i mallen per timme. Om du utförde kommandot klockan 14:23 kör utlösaren pipelinen klockan 15 för första gången. Sedan körs pipelinen varje timme till sluttiden du angav för utlösaren. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Här är exempel på utdata: 
    
    ```
    Confirm
    Are you sure you want to start trigger 'ArmTemplateTestTrigger' in data factory 'ARMFactory1128'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
    True
    ```
6. Bekräfta att utlösaren har startats genom att köra kommandot Get-AzureRmDataFactoryV2Trigger igen.  

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Här är exempel på utdata:
    
    ```
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ARMFactory1128
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Started
    ```

## <a name="monitor-the-pipeline"></a>Övervaka pipeline
1. När du har loggat in på [Azure-portalen](https://portal.azure.com/) klickar du på **Alla tjänster**, söker med nyckelordet som **data fa** och väljer **Datafabriker**.

    ![Bläddra igenom datafabriksmenyerna](media/quickstart-create-data-factory-resource-manager-template/browse-data-factories-menu.png)
2. På sidan **Datafabriker** klickar du på datafabriken som du skapade. Om det behövs kan du filtrera listan med namnet på datafabriken.  

    ![Välja datafabrik](media/quickstart-create-data-factory-resource-manager-template/select-data-factory.png)
3. På sidan Datafabrik klickar du på panelen **Övervaka och hantera**. 

    ![Övervaka och hantera panel](media/quickstart-create-data-factory-resource-manager-template/monitor-manage-tile.png)
4. **Dataintegrationsprogrammet** ska öppnas i en separat flik i webbläsaren. Om övervakningsfliken inte är aktiv växlar du till **övervakningsfliken**. Lägg märke till att pipelinen som kördes utlöstes av en **Scheduler-utlösare**. 

    ![Övervaka pipelinekörning](media/quickstart-create-data-factory-resource-manager-template/monitor-pipeline-run.png)    

    > [!IMPORTANT]
    > Du ser att pipelines endast körs hel timme (till exempel: 4, 5, 6 osv.). Klicka på **Uppdatera** i verktygsfältet för att uppdatera listan när tiden når nästa timma. 
5. Klicka på länken i kolumnerna **Åtgärder**. 

    ![Länk för pipelineåtgärder](media/quickstart-create-data-factory-resource-manager-template/pipeline-actions-link.png)
6. Du ser att aktiviteten som körs är associerad med pipelinekörningen. I den här snabbstarten har pipelinen endast en aktivitet av typen: kopiera. Därför kan du se en körning för den aktiviteten. 

    ![Aktivitetskörningar](media/quickstart-create-data-factory-resource-manager-template/activity-runs.png)
1. Klicka på länken under **utdatakolumnen**. Du ser utdata från kopieringsåtgärden i ett **utdatafönster**. Klicka på maximeringsknappen om du vill visa fullständiga utdata. Du kan stänga det maximerade utdatafönstret eller stänga det. 

    ![Utdatafönster](media/quickstart-create-data-factory-resource-manager-template/output-window.png)
7. Stoppa utlösaren när du ser en lyckad/misslyckad körning. Utlösaren kör pipelinen en gång i timmen. Pipelinen kopierar samma fil från indatamappen till utdatamappen för varje körning. Kör följande kommando i PowerShell-fönstret om du vill stoppa utlösaren. 
    
    ```powershell
    Stop-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)] 

## <a name="data-factory-entities-in-the-template"></a> JSON-definitioner för entiteter
Följande Data Factory-entiteter har definierats i JSON-mallen: 

- [Länkad Azure Storage-tjänst](#azure-storage-linked-service)
- [Indatauppsättning för Azure-blobb](#azure-blob-input-dataset)
- [Utdatauppsättning för Azure-blob](#azure-blob-output-dataset)
- [Datapipeline med en kopieringsaktivitet](#data-pipeline)
- [Utlösare](#trigger)

#### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst
AzureStorageLinkedService länkar ditt Azure Storage-konto till datafabriken. Du har skapat en container och överfört data till det här lagringskontot som en del av förhandskraven. Du anger namnet och nyckeln för Azure Storage-kontot i det här avsnittet. Se [Länkad Azure Storage-tjänst](connector-azure-blob-storage.md#linked-service-properties) om du vill ha information om JSON-egenskaper som används för att definiera en länkad Azure Storage-tjänst. 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[parameters('dataFactoryName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": {
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]",
                "type": "SecureString"
            }
        }
    }
}
```

connectionString använder parametrarna storageAccountName och storageAccountKey. Värdena för dessa parametrar skickades med hjälp av en konfigurationsfil. Definitionen använder också variablerna azureStorageLinkedService och dataFactoryName, som definieras i mallen. 

#### <a name="azure-blob-input-dataset"></a>Indatauppsättning för Azure-blobb
Den länkade Azure storage-tjänsten anger anslutningssträngen som Data Factory-tjänsten använder vid körning för att ansluta till ditt Azure storage-konto. Du anger namnen på blobcontainer, mapp och fil som innehåller indata i Azure-blobuppsättningsdefinitionen. Se [Egenskaper för Azure-blobbdatauppsättning](connector-azure-blob-storage.md#dataset-properties) om du vill ha information om JSON-egenskaper som används för att definiera en Azure-blobbdatauppsättning. 

```json
{
    "type": "datasets",
    "name": "[variables('inputDatasetName')]",
    "dependsOn": [
    "[parameters('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'), '/')]",
        "fileName": "[parameters('inputBlobName')]"
    },
    "linkedServiceName": {
        "referenceName": "[variables('azureStorageLinkedServiceName')]",
        "type": "LinkedServiceReference"
    }
    }
},

```

#### <a name="azure-blob-output-dataset"></a>Utdatauppsättning för Azure-blob
Du anger namnet på mappen i Azure Blob Storage som innehåller kopierade data från indatamappen. Se [Egenskaper för Azure-blobbdatauppsättning](connector-azure-blob-storage.md#dataset-properties) om du vill ha information om JSON-egenskaper som används för att definiera en Azure-blobbdatauppsättning. 

```json
{
    "type": "datasets",
    "name": "[variables('outputDatasetName')]",
    "dependsOn": [
    "[parameters('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'), '/')]",
        "fileName": "[parameters('outputBlobName')]"
    },
    "linkedServiceName": {
        "referenceName": "[variables('azureStorageLinkedServiceName')]",
        "type": "LinkedServiceReference"
    }
    }
}
```

#### <a name="data-pipeline"></a>Datapipeline
Du definierar en pipeline som kopierar data från Azure-blobdatamängden till Azure Blob-datamängden. Se [Pipeline-JSON](concepts-pipelines-activities.md#pipeline-json) för beskrivningar av JSON-element som används för att definiera en pipeline i det här exemplet. 

```json
{
    "type": "pipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
    "[parameters('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]",
    "[variables('inputDatasetName')]",
    "[variables('outputDatasetName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
    "activities": [
        {
        "type": "Copy",
        "typeProperties": {
            "source": {
            "type": "BlobSource"
            },
            "sink": {
            "type": "BlobSink"
            }
        },
        "name": "MyCopyActivity",
        "inputs": [
            {
            "referenceName": "[variables('inputDatasetName')]",
            "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
            "referenceName": "[variables('outputDatasetName')]",
            "type": "DatasetReference"
            }
        ]
        }
    ]
    }
}
```

#### <a name="trigger"></a>Utlösare
Du definierar en utlösare som kör pipelinen en gång per timme. Den distribuerade utlösaren har stoppats. Starta utlösaren med cmdleten **Start-AzureRmDataFactoryV2Trigger**. Mer information om utlösare finns i artikeln om [pipelinekörning och utlösare](concepts-pipeline-execution-triggers.md#triggers). 

```json
{
    "type": "triggers",
    "name": "[variables('triggerName')]",
    "dependsOn": [
        "[parameters('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('inputDatasetName')]",
        "[variables('outputDatasetName')]",
        "[variables('pipelineName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-28T00:00:00",
                "endTime": "2017-11-29T00:00:00",
                "timeZone": "UTC"               
            }
        },
        "pipelines": [{
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "ArmtemplateSampleCopyPipeline"
            },
            "parameters": {}
        }]
    }
}
```

## <a name="reuse-the-template"></a>Återanvända mallen
I självstudien skapade du en mall för att definiera Data Factory-entiteter och en mall för att skicka värden för parametrar. Om du vill använda samma mall för att distribuera Data Factory-entiteter till olika miljöer skapar du en parameterfil för varje miljö och använder den när du distribuerar till den miljön.     

Exempel:  

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```
Observera att det första kommandot använder parameterfilen för utvecklingsmiljön, det andra för testmiljön och det tredje för produktionsmiljön.  

Du kan även återanvända mallen för att utföra upprepade uppgifter. Skapa till exempel många datafabriker med en eller flera pipelines som implementerar samma logik, men alla datafabriker använder olika konton för Azure Storage. I det här scenariot använder du samma mall i samma miljö (utvecklings-, test- eller produktionsmiljö) med olika parameterfiler för att skapa datafabriker. 


## <a name="next-steps"></a>Nästa steg
Pipeline i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Gå igenom [självstudiekurserna](tutorial-copy-data-dot-net.md) om du vill lära dig hur du använder Data Factory i fler scenarier. 
