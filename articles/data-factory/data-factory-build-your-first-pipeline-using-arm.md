---
title: "Skapa din första datafabrik (Resource Manager-mall) | Microsoft Docs"
description: "I de här självstudierna skapar du ett exempel på en Azure Data Factory-pipeline med hjälp av en Azure Resource Manager-mall."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: eb9e70b9-a13a-4a27-8256-2759496be470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/17/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 9990e3ae2a978f0024c17f445ea07eae8aef5197
ms.openlocfilehash: 3f36162dd0826270a7e2a69e7d940ba5516ecef0


---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-resource-manager-template"></a>Självstudie: Skapa din första Azure-datafabrik med hjälp av en Azure Resource Manager-mall
> [!div class="op_single_selector"]
> * [Översikt och förutsättningar](data-factory-build-your-first-pipeline.md)
> * [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-mall](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)
> 
> 

I den här artikeln får du lära dig hur du använder en Azure Resource Manager-mall för att skapa din första Azure-datafabrik.

## <a name="prerequisites"></a>Krav
* Läs igenom artikeln [Självstudier – översikt](data-factory-build-your-first-pipeline.md) och slutför de **nödvändiga** stegen.
* Följ instruktionerna i artikeln [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azureps-cmdlets-docs) för att installera den senaste versionen av Azure PowerShell på datorn.
* Se [Redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md) om du vill lära dig mer om mallar i Azure Resource Manager. 

## <a name="in-this-tutorial"></a>I den här självstudien
| Entitet | Beskrivning |
| --- | --- |
| Länkad Azure-lagringstjänst |Länkar på begäran ditt Azure Storage-konto till datafabriken. In- och utdata för pipelinen i det här exemplet lagras i Azure Storage-kontot. |
| HDInsight on-demand linked service (Länkad tjänst för HDInsight på begäran) |Länkar på begäran HDInsight-klustret till datafabriken. Klustret skapas automatiskt för att bearbeta data och raderas när bearbetningen är klar. |
| Indatauppsättning för Azure-blobb |Hänvisar till den länkade Azure Storage-tjänsten. Den länkade tjänsten hänvisar till ett Azure Storage-konto och datauppsättningen för Azure-blobben anger behållaren, mappen och filnamnet i lagringsutrymmet som innehåller indata. |
| Utdatauppsättning för Azure-blobb |Hänvisar till den länkade Azure Storage-tjänsten. Den länkade tjänsten hänvisar till ett Azure Storage-konto och datauppsättningen för Azure-blobben anger behållaren, mappen och filnamnet i lagringsutrymmet som innehåller utdata. |
| Datapipeline |Pipelinen har en aktivitet av typen HDInsightHive som använder indatauppsättningen och skapar utdatauppsättningen. |

En datafabrik kan ha en eller flera pipelines. En pipeline kan innehålla en eller flera aktiviteter. Det finns två typer av aktiviteter: [dataflyttningsaktiviteter](data-factory-data-movement-activities.md) och [datatransformeringsaktiviteter](data-factory-data-transformation-activities.md). I den här självstudien får du skapa en pipeline i en aktivitet (kopieringsaktivitet).

Följande avsnitt innehåller den fullständiga Resource Manager-mallen för att definiera Data Factory-entiteter så att du snabbt kan gå igenom självstudien och testa mallen. Om du vill förstå hur varje Data Factory-entitet definieras kan du läsa avsnittet [Data Factory-entiteter i mallen](#data-factory-entities-in-the-template).

## <a name="data-factory-json-template"></a>Data Factory JSON-mall
Resource Manager-mallen på den högsta nivån för att definiera en datafabrik är: 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
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
Skapa en JSON-fil med namnet **ADFTutorialARM.json** i mappen **C:\ADFGetStarted** med följande innehåll:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the input/output data." } },
          "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
          "blobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
          "inputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that has the input file." } },
          "inputBlobName": { "type": "string", "metadata": { "description": "Name of the input file/blob." } },
          "outputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that will hold the transformed data." } },
          "hiveScriptFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that contains the Hive query file." } },
          "hiveScriptFile": { "type": "string", "metadata": { "description": "Name of the hive query (HQL) file." } }
    },
    "variables": {
          "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
          "azureStorageLinkedServiceName": "AzureStorageLinkedService",
          "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
          "blobInputDatasetName": "AzureBlobInput",
          "blobOutputDatasetName": "AzureBlobOutput",
          "pipelineName": "HiveTransformPipeline"
    },
    "resources": [
      {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US",
        "resources": [
          {
            "type": "linkedservices",
            "name": "[variables('azureStorageLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureStorage",
                  "description": "Azure Storage linked service",
                  "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                  }
            }
          },
          {
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "HDInsightOnDemand",
                  "typeProperties": {
                    "clusterSize": 1,
                    "version": "3.2",
                    "timeToLive": "00:05:00",
                    "osType": "windows",
                    "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
                  }
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobInputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "fileName": "[parameters('inputBlobName')]",
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
                    "format": {
                          "type": "TextFormat",
                          "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Month",
                    "interval": 1
                  },
                  "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobOutputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
                    "format": {
                          "type": "TextFormat",
                          "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Month",
                    "interval": 1
                  }
            }
          },
          {
            "type": "datapipelines",
            "name": "[variables('pipelineName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]",
                  "[variables('hdInsightOnDemandLinkedServiceName')]",
                  "[variables('blobInputDatasetName')]",
                  "[variables('blobOutputDatasetName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "description": "Pipeline that transforms data using Hive script.",
                  "activities": [
                {
                      "type": "HDInsightHive",
                      "typeProperties": {
                        "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                        "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                        "defines": {
                              "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                              "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                        }
                      },
                      "inputs": [
                        {
                              "name": "[variables('blobInputDatasetName')]"
                        }
                      ],
                      "outputs": [
                        {
                              "name": "[variables('blobOutputDatasetName')]"
                        }
                      ],
                      "policy": {
                        "concurrency": 1,
                        "retry": 3
                      },
                      "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                      },
                      "name": "RunSampleHiveActivity",
                      "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
                }
                  ],
                  "start": "2016-10-01T00:00:00Z",
                  "end": "2016-10-02T00:00:00Z",
                  "isPaused": false
              }
          }
        ]
      }
    ]
}
```

> [!NOTE]
> Du hittar ytterligare ett exempel på en Resource Manager-mall för att skapa en Azure-datafabrik i [Självstudier: skapa en pipeline med kopieringsaktivitet via en Azure Resource Manager-mall](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md).  
> 
> 

## <a name="parameters-json"></a>JSON-parametrar
Skapa en JSON-fil med namnet **ADFTutorialARM-Parameters.json** som innehåller parametrar för Azure Resource Manager-mallen.  

> [!IMPORTANT]
> Ange namn och nyckel för Azure Storage-kontot och parametrarna **storageAccountName** och **storageAccountKey** i den här parameterfilen. 
> 
> 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "value": "<Name of your Azure Storage account>"
        },
        "storageAccountKey": {
            "value": "<Key of your Azure Storage account>"
        },
        "blobContainer": {
            "value": "adfgetstarted"
        },
        "inputBlobFolder": {
            "value": "inputdata"
        },
        "inputBlobName": {
            "value": "input.log"
        },
        "outputBlobFolder": {
            "value": "partitioneddata"
        },
        "hiveScriptFolder": {
              "value": "script"
        },
        "hiveScriptFile": {
              "value": "partitionweblogs.hql"
        }
    }
}
```

> [!IMPORTANT]
> Du kan ha separata JSON-filer med parametrar för utveckling, testning och produktionsmiljöer som du kan använda med samma Data Factory JSON-mall. Genom att använda ett Power Shell-skript kan du automatisera distributionen av Data Factory-entiteter i dessa miljöer. 
> 
> 

## <a name="create-data-factory"></a>Skapa en datafabrik
1. Starta **Azure PowerShell** och kör följande kommando: 
   * Kör följande kommando och ange användarnamnet och lösenordet som du använder för att logga in på Azure-portalen.
    ```PowerShell
    Login-AzureRmAccount
    ```  
   * Kör följande kommando för att visa alla prenumerationer för det här kontot.
    ```PowerShell
    Get-AzureRmSubscription
    ``` 
   * Kör följande kommando för att välja den prenumeration som du vill arbeta med. Den här prenumerationen ska vara samma som den du använde i Azure-portalen.
    ```
    Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext
    ```   
2. Kör följande kommando för att distribuera Data Factory-entiteter med hjälp av Resource Manager-mallen som du skapade i steg 1. 

    ```PowerShell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>Övervaka pipeline
1. När du har loggat in på [Azure-portalen](https://portal.azure.com/) klickar du på **Bläddra** och väljer **Datafabriker**.
     ![Bläddra igenom datafabrikerna](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2. På bladet **Datafabriker** klickar du på datafabriken (**TutorialFactoryARM**) som du skapade.    
3. På bladet **Data Factory** för din datafabrik klickar du på **Diagram**.

     ![Ikonen Diagram](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4. I **diagramvyn** visas en översikt över pipelines och datauppsättningar som används i den här självstudien.
   
   ![Diagramvy](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
5. Dubbelklicka på datauppsättningen **AzureBlobOutput** i diagramvyn. Den sektor som för närvarande bearbetas visas.
   
    ![Datauppsättning](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
6. När bearbetningen är klar visas sektorn med statusen **Klar**. Att skapa ett HDInsight-kluster på begäran kan ta lite längre tid (cirka 20 minuter). Förvänta dig därför att det tar **cirka 30 minuter** för pipelinen att bearbeta sektorn.
   
    ![Datauppsättning](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)    
7. När sektorn har statusen **Klar**, kontrollerar du mappen **partitioneddata** i behållaren **adfgetstarted** i blobblagringen för utdata.  

Se [Övervaka datauppsättningar och pipeline](data-factory-monitor-manage-pipelines.md) för instruktioner om hur du använder Azures portalblad till att övervaka pipelinen och datauppsättningar som du har skapat i den här självstudien.

Du kan också använda appen Övervaka och hantera till att övervaka dina datapipelines. Se [Övervaka och hantera Azure Data Factory-pipelines med övervakningsappen](data-factory-monitor-manage-app.md) för mer information om att använda programmet. 

> [!IMPORTANT]
> Indatafilen tas bort när sektorn har bearbetats. Om du vill köra sektorn eller gå igenom självstudien igen överför du därför indatafilen (input.log) till indatamappen i behållaren adfgetstarted.
> 
> 

## <a name="data-factory-entities-in-the-template"></a>Data Factory-entiteter i mallen
### <a name="define-data-factory"></a>Definiera en datafabrik
Du definierar en datafabrik i Resource Manager-mallen enligt följande exempel:  

```json
"resources": [
{
    "name": "[variables('dataFactoryName')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "West US"
}
```
DataFactoryName definieras som: 

```json
"dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
```
Det är en unik sträng som baseras på resursgruppens ID.  

### <a name="defining-data-factory-entities"></a>Definiera Data Factory-entiteter
Följande Data Factory-entiteter har definierats i JSON-mallen: 

* [Länkad Azure Storage-tjänst](#azure-storage-linked-service)
* [Länkad tjänst för HDInsight på begäran](#hdinsight-on-demand-linked-service)
* [Indatauppsättning för Azure-blobb](#azure-blob-input-dataset)
* [Utdatauppsättning för Azure-blobb](#azure-blob-output-dataset)
* [Datapipeline med en kopieringsaktivitet](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst
Du anger namnet och nyckeln för Azure Storage-kontot i det här avsnittet. Se [Länkad Azure Storage-tjänst](data-factory-azure-blob-connector.md#azure-storage-linked-service) om du vill ha information om JSON-egenskaper som används för att definiera en länkad Azure Storage-tjänst. 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
**connectionString** använder parametrarna storageAccountName och storageAccountKey. Värdena för dessa parametrar skickades med hjälp av en konfigurationsfil. Definitionen använder också variablerna azureStorageLinkedService och dataFactoryName, som definieras i mallen. 

#### <a name="hdinsight-on-demand-linked-service"></a>HDInsight on-demand linked service (Länkad tjänst för HDInsight på begäran)
Läs mer i artikeln [Beräkna länkade tjänster](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) om JSON-egenskaper som används för att definiera en länkad tjänst för HDInsight på begäran.  

```json
{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "clusterSize": 1,
            "version": "3.2",
            "timeToLive": "00:05:00",
            "osType": "windows",
            "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
        }
    }
}
```
Observera följande punkter: 

* Data Factory skapar ett **Windows-baserat** HDInsight-kluster med ovanstående JSON. Du hade också kunnat skapa ett **Linux-baserat** HDInsight-kluster. Se [HDInsight-länkad tjänst på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) för mer information. 
* Du kan använda **ditt eget HDInsight-kluster** i stället för att använda ett HDInsight-kluster på begäran. Se [HDInsight-länkad tjänst](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) för mer information.
* HDInsight-klustret skapar en **standardbehållare** i den blobblagring som du angav i JSON (**linkedServiceName**). HDInsight tar inte bort den här behållaren när klustret tas bort. Det här beteendet är avsiktligt. Med en HDInsight-länkad tjänst på begäran skapas ett HDInsight-kluster varje gång en sektor behöver bearbetas, såvida det inte finns ett befintligt livekluster (**timeToLive**). Det raderas när bearbetningen är klar.
  
    Allteftersom fler sektorer bearbetas kan du se många behållare i ditt Azure Blob Storage. Om du inte behöver dem för att felsöka jobb, kan du ta bort dem för att minska lagringskostnaderna. Namnen på de här behållarna följer ett mönster: ”adf**datafabrikensnamn**-**denlänkadetjänstensnamn**-datumtidsstämpel”. Använd verktyg som [Microsoft Lagringsutforskaren](http://storageexplorer.com/) till att ta bort behållare i din Azure Blob-lagring.

Se [HDInsight-länkad tjänst på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) för mer information.

#### <a name="azure-blob-input-dataset"></a>Indatauppsättning för Azure-blobb
Du anger namnen på en blobbehållare, mapp och fil som innehåller dina indata. Se [Egenskaper för Azure-blobbdatauppsättning](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) om du vill ha information om JSON-egenskaper som används för att definiera en Azure-blobbdatauppsättning. 

```json
{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "[parameters('inputBlobName')]",
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true
    }
}
```
Den här definitionen använder följande parametrar som definierats i parameter-mallen: blobContainer, inputBlobFolder och inputBlobName. 

#### <a name="azure-blob-output-dataset"></a>Utdatauppsättning för Azure-blobb
Du kan ange namnen på blobbehållaren och mappen som innehåller utdata. Se [Egenskaper för Azure-blobbdatauppsättning](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) om du vill ha information om JSON-egenskaper som används för att definiera en Azure-blobbdatauppsättning.  

```json
{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        }
    }
}
```

Den här definitionen använder följande parametrar som definierats i parameter-mallen: blobContainer och outputBlobFolder. 

#### <a name="data-pipeline"></a>Datapipeline
Du definierar en pipeline som transformerar data genom att köra ett registreringsdatafilskript på ett Azure HD Insight-kluster på begäran. Se [Pipeline-JSON](data-factory-create-pipelines.md#pipeline-json) för beskrivningar av JSON-element som används för att definiera en pipeline i det här exemplet. 

```json
{
    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('hdInsightOnDemandLinkedServiceName')]",
        "[variables('blobInputDatasetName')]",
        "[variables('blobOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "description": "Pipeline that transforms data using Hive script.",
        "activities": [
        {
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                "defines": {
                    "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                    "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                }
            },
            "inputs": [
            {
                "name": "[variables('blobInputDatasetName')]"
            }
            ],
            "outputs": [
            {
                "name": "[variables('blobOutputDatasetName')]"
            }
            ],
            "policy": {
                "concurrency": 1,
                "retry": 3
            },
            "scheduler": {
                "frequency": "Month",
                "interval": 1
            },
            "name": "RunSampleHiveActivity",
            "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
        }
        ],
        "start": "2016-10-01T00:00:00Z",
        "end": "2016-10-02T00:00:00Z",
        "isPaused": false
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

Du kan även återanvända mallen för att utföra upprepade uppgifter. Du behöver till exempel skapa många datafabriker med en eller flera pipelines som implementerar samma logik, men alla datafabriker använder olika konton för Azure Storage och Azure SQL Database. I det här scenariot använder du samma mall i samma miljö (utvecklings-, test- eller produktionsmiljö) med olika parameterfiler för att skapa datafabriker. 

## <a name="resource-manager-template-for-creating-a-gateway"></a>Resource Manager-mall för att skapa en gateway
Här är ett exempel på en Resource Manager-mall för att skapa en logisk gateway i bakgrunden. Installera en gateway på din lokala dator eller virtuella Azure IaaS-dator och registrera gatewayen med Data Factory-tjänsten med hjälp av en nyckel. Se [Flytta data mellan lokalt system och moln](data-factory-move-data-between-onprem-and-cloud.md) för mer information.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
    },
    "variables": {
        "dataFactoryName":  "GatewayUsingArmDF",
        "apiVersion": "2015-10-01",
        "singleQuote": "'"
    },
    "resources": [
        {
            "name": "[variables('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "eastus",
            "resources": [
                {
                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                    "type": "gateways",
                    "apiVersion": "[variables('apiVersion')]",
                    "name": "GatewayUsingARM",
                    "properties": {
                        "description": "my gateway"
                    }
                }            
            ]
        }
    ]
}
```
Den här mallen skapar en datafabrik som heter GatewayUsingArmDF med en gateway med namnet GatewayUsingARM. 

## <a name="see-also"></a>Se även
| Avsnitt | Beskrivning |
|:--- |:--- |
| [Datatransformeringsaktiviteter](data-factory-data-transformation-activities.md) |Den här artikeln innehåller en lista med de datatransformeringsaktiviteter (till exempel HDInsight Hive-transformeringen som du använde i självstudien) som stöds av Azure Data Factory. |
| [Schemaläggning och körning](data-factory-scheduling-and-execution.md) |I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. |
| [Pipelines](data-factory-create-pipelines.md) |I den här artikeln beskriver vi pipelines och aktiviteter i Azure Data Factory och hur du kan använda dem för att konstruera datadrivna arbetsflöden från slutpunkt till slutpunkt för ditt scenario eller ditt företag. |
| [Datauppsättningar](data-factory-create-datasets.md) |I den här artikeln förklaras hur datauppsättningar fungerar i Azure Data Factory. |
| [Övervaka och hantera pipelines med övervakningsappen](data-factory-monitor-manage-app.md) |Den här artikeln beskriver hur du övervakar, hanterar och felsöker pipelines med övervaknings- och hanteringsappen. |




<!--HONumber=Dec16_HO4-->


