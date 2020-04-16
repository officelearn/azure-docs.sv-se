---
title: Skapa en Azure-datafabrik med hjälp av Resource Manager-mallen
description: I de här självstudierna skapar du ett exempel på en Azure Data Factory-pipeline med hjälp av en Azure Resource Manager-mall.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 02/20/2019
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 5ea45346de5ea841867dd13dd4c9a0ed26647448
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419127"
---
# <a name="tutorial-create-an-azure-data-factory-using-azure-resource-manager-template"></a>Självstudie: Skapa en Azure-datafabrik med hjälp av en Azure Resource Manager-mall

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Aktuell version](quickstart-create-data-factory-resource-manager-template.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här snabbstarten får du se hur du skapar en Azure Data Factory med en Azure Resource Manager-mall. Den pipeline du skapar i den här datafabriken **kopierar** data från en mapp till en annan mapp i Azure Blob Storage. Om du vill se en självstudie som visar hur du **omvandlar** data med Azure Data Factory går du till [Tutorial: Transform data using Spark](transform-data-using-spark.md) (Självstudie: Omvandla data med Spark).

> [!NOTE]
> Den här artikeln ger inte någon detaljerad introduktion till Azure Data Factory-tjänsten. En introduktion till Azure Data Factory-tjänsten finns i [Introduktion till Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)]

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Installera de senaste Azure PowerShell-modulerna enligt instruktionerna i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

Se [Skapa Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md) om du vill lära dig mer om Azure Resource Manager-mallar i allmänhet.

Följande avsnitt innehåller den fullständiga Resource Manager-mallen för att definiera Data Factory-entiteter så att du snabbt kan gå igenom självstudien och testa mallen. Om du vill förstå hur varje Data Factory-entitet definieras kan du läsa avsnittet [Data Factory-entiteter i mallen](#data-factory-entities-in-the-template).

Mer information om JSON-syntaxen och JSON-egenskaperna för Data Factory-resurser i en mall finns i [Microsoft.DataFactory-resurstyper](/azure/templates/microsoft.datafactory/allversions).

## <a name="data-factory-json"></a>Data Factory JSON

Skapa en JSON-fil med namnet **ADFTutorialARM.json** i **mappen C:\ADFTutorial** (Skapa mappen ADFTutorial om den inte redan finns) med följande innehåll:

```json
{  
    "$schema":"http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion":"1.0.0.0",
    "parameters":{  
        "dataFactoryName":{  
            "type":"string",
            "metadata":"Data Factory Name"
        },
        "dataFactoryLocation":{  
            "type":"string",
            "defaultValue":"East US",
            "metadata":{  
                "description":"Location of the data factory. Currently, only East US, East US 2, and West Europe are supported. "
            }
        },
        "storageAccountName":{  
            "type":"string",
            "metadata":{  
                "description":"Name of the Azure storage account that contains the input/output data."
            }
        },
        "storageAccountKey":{  
            "type":"securestring",
            "metadata":{  
                "description":"Key for the Azure storage account."
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
    "variables":{  
        "factoryId":"[concat('Microsoft.DataFactory/factories/', parameters('dataFactoryName'))]"
    },
    "resources":[  
        {  
            "name":"[parameters('dataFactoryName')]",
            "apiVersion":"2018-06-01",
            "type":"Microsoft.DataFactory/factories",
            "location":"[parameters('dataFactoryLocation')]",
            "identity":{  
                "type":"SystemAssigned"
            },
            "resources":[  
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateStorageLinkedService')]",
                    "type":"Microsoft.DataFactory/factories/linkedServices",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "annotations":[  

                        ],
                        "type":"AzureBlobStorage",
                        "typeProperties":{  
                            "connectionString":"[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetIn')]",
                    "type":"Microsoft.DataFactory/factories/datasets",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "linkedServiceName":{  
                            "referenceName":"ArmtemplateStorageLinkedService",
                            "type":"LinkedServiceReference"
                        },
                        "annotations":[  

                        ],
                        "type":"Binary",
                        "typeProperties":{  
                            "location":{  
                                "type":"AzureBlobStorageLocation",
                                "fileName":"emp.txt",
                                "folderPath":"input",
                                "container":"adftutorial"
                            }
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetOut')]",
                    "type":"Microsoft.DataFactory/factories/datasets",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "linkedServiceName":{  
                            "referenceName":"ArmtemplateStorageLinkedService",
                            "type":"LinkedServiceReference"
                        },
                        "annotations":[  

                        ],
                        "type":"Binary",
                        "typeProperties":{  
                            "location":{  
                                "type":"AzureBlobStorageLocation",
                                "folderPath":"output",
                                "container":"adftutorial"
                            }
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateSampleCopyPipeline')]",
                    "type":"Microsoft.DataFactory/factories/pipelines",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "activities":[  
                            {  
                                "name":"MyCopyActivity",
                                "type":"Copy",
                                "dependsOn":[  

                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  

                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"BinarySource",
                                        "storeSettings":{  
                                            "type":"AzureBlobStorageReadSettings",
                                            "recursive":true
                                        }
                                    },
                                    "sink":{  
                                        "type":"BinarySink",
                                        "storeSettings":{  
                                            "type":"AzureBlobStorageWriteSettings"
                                        }
                                    },
                                    "enableStaging":false
                                },
                                "inputs":[  
                                    {  
                                        "referenceName":"ArmtemplateTestDatasetIn",
                                        "type":"DatasetReference",
                                        "parameters":{  

                                        }
                                    }
                                ],
                                "outputs":[  
                                    {  
                                        "referenceName":"ArmtemplateTestDatasetOut",
                                        "type":"DatasetReference",
                                        "parameters":{  

                                        }
                                    }
                                ]
                            }
                        ],
                        "annotations":[  

                        ]
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetIn')]",
                        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetOut')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmTemplateTestTrigger')]",
                    "type":"Microsoft.DataFactory/factories/triggers",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "annotations":[  

                        ],
                        "runtimeState":"Started",
                        "pipelines":[  
                            {  
                                "pipelineReference":{  
                                    "referenceName":"ArmtemplateSampleCopyPipeline",
                                    "type":"PipelineReference"
                                },
                                "parameters":{  

                                }
                            }
                        ],
                        "type":"ScheduleTrigger",
                        "typeProperties":{  
                            "recurrence":{  
                                "frequency":"Hour",
                                "interval":1,
                                "startTime":"[parameters('triggerStartTime')]",
                                "endTime":"[parameters('triggerEndTime')]",
                                "timeZone":"UTC"
                            }
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/pipelines/ArmtemplateSampleCopyPipeline')]"
                    ]
                }
            ]
        }
    ]
}
```
## <a name="parameters-json"></a>JSON-parametrar

Skapa en JSON-fil med namnet **ADFTutorialARM-Parameters.json** som innehåller parametrar för Azure Resource Manager-mallen.

> [!IMPORTANT]
> - Ange namn och nyckel för Azure Storage-kontot och parametrarna **storageAccountName** och **storageAccountKey** i den här parameterfilen. Du har skapat adftutorial-containern och laddat upp exempelfilen (emp.txt) till indatamappen i denna Azure Blob Storage.
> - Ange ett globalt unikt namn för datafabriken för parametern **dataFactoryName**. Till exempel: ARMTutorialFactoryJohnDoe11282017.
> - För **triggerStartTime** anger du aktuell dag i formatet: `2019-09-08T00:00:00`.
> - För **triggerEndTime** anger du nästa dag i formatet: `2019-09-09T00:00:00`. Du kan också kontrollera aktuell UTC-tid och ange nästa timma eller nästa två timmar som sluttid. Om UTC-tiden exempelvis nu är 1:32 anger du `2019-09-09:03:00:00` som sluttid. I det här fallet kör utlösaren pipelinen två gånger (vid 02:00 och 3:00).

```json
{  
    "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":"1.0.0.0",
    "parameters":{  
        "dataFactoryName":{  
            "value":"<datafactoryname>"
        },
        "dataFactoryLocation":{  
            "value":"East US"
        },
        "storageAccountName":{  
            "value":"<yourstorageaccountname>"
        },
        "storageAccountKey":{  
            "value":"<yourstorageaccountkey>"
        },
        "triggerStartTime":{  
            "value":"2019-09-08T11:00:00"
        },
        "triggerEndTime":{  
            "value":"2019-09-08T14:00:00"
        }
    }
}
```

> [!IMPORTANT]
> Du kan ha separata JSON-filer med parametrar för utveckling, testning och produktionsmiljöer som du kan använda med samma Data Factory JSON-mall. Genom att använda ett Power Shell-skript kan du automatisera distributionen av Data Factory-entiteter i dessa miljöer.

## <a name="deploy-data-factory-entities"></a>Distribuera Data Factory-entiteter

I PowerShell kör du följande kommando för att distribuera Data Factory-entiteter i resursgruppen (i det här fallet ta ADFTutorialResourceGroup som exempel) med hjälp av den Resource Manager-mall som du skapade tidigare i den här snabbstarten.

```powershell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFTutorial\ADFTutorialARM.json -TemplateParameterFile C:\ADFTutorial\ADFTutorialARM-Parameters.json
```

Du ser utdata som liknar följande exempel:

```console
DeploymentName          : MyARMDeployment
ResourceGroupName       : ADFTutorialResourceGroup
ProvisioningState       : Succeeded
Timestamp               : 9/8/2019 10:52:29 AM
Mode                    : Incremental
TemplateLink            : 
Parameters              : 
                          Name                   Type                       Value     
                          =====================  =========================  ==========
                          dataFactoryName        String                     <data factory name>
                          dataFactoryLocation    String                     East US   
                          storageAccountName     String                     <storage account name>
                          storageAccountKey      SecureString                         
                          triggerStartTime       String                     9/8/2019 11:00:00 AM
                          triggerEndTime         String                     9/8/2019 2:00:00 PM
                          
Outputs                 : 
DeploymentDebugLogLevel : 
```

## <a name="start-the-trigger"></a>Starta utlösarna

Med mallen kan du distribuera följande Data Factory-entiteter:

- Länkad Azure-lagringstjänst
- Binära datamängder (indata och utdata)
- Pipeline med en kopieringsaktivitet
- Utlösare för att utlösa pipelinen

Den distribuerade utlösaren har stoppats. Ett sätt att starta utlösaren är att använda **Start-AzDataFactoryV2Trigger** PowerShell cmdlet. Följande procedur innehåller detaljerade anvisningar:

1. I PowerShell-fönstret skapar du en variabel som anger namnet på resursgruppen. Kopiera följande kommando till PowerShell-fönstret och tryck på RETUR. Om du har angett ett annat resursgruppsnamn för kommandot New-AzResourceGroupDeployment uppdaterar du värdet här.

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```
2. Skapa en variabel som innehåller namnet på datafabriken. Ange samma namn som du angav i filen ADFTutorialARM-Parameters.json.

    ```powershell
    $dataFactoryName = "<yourdatafactoryname>"
    ```
3. Ange en variabel för namnet på utlösaren. Namnet för utlösaren är hårdkodad i Resource Manager-mallfilen (ADFTutorialARM.json).

    ```powershell
    $triggerName = "ArmTemplateTestTrigger"
    ```
4. Hämta **utlösarens status** genom att köra följande PowerShell-kommando när du har angett namnet på din datafabrik och utlösare:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

    Här är exempel på utdata:

    ```json

    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFQuickstartsDataFactory0905
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Stopped
    ```
    
    Observera att körningstillståndet för utlösaren är **Stoppad**.
5. **Starta utlösaren**. Utlösaren kör pipelinen som definieras i mallen per timme. Om du utförde kommandot klockan 14:23 kör utlösaren pipelinen klockan 15 för första gången. Sedan körs pipelinen varje timme till sluttiden du angav för utlösaren.

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Här är exempel på utdata:
    
    ```console
    Confirm
    Are you sure you want to start trigger 'ArmTemplateTestTrigger' in data factory 'ARMFactory1128'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
    True
    ```
6. Bekräfta att utlösaren har startats genom att köra kommandot Get-AzDataFactoryV2Trigger igen.

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Här är exempel på utdata:
    
    ```console
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFQuickstartsDataFactory0905
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Started
    ```

## <a name="monitor-the-pipeline"></a>Övervaka pipeline

1. När du har loggat in på [Azure-portalen](https://portal.azure.com/) klickar du på **Alla tjänster**, söker med nyckelordet som **data fa** och väljer **Datafabriker**.

2. På sidan **Datafabriker** klickar du på datafabriken som du skapade. Om det behövs kan du filtrera listan med namnet på datafabriken.

3. Klicka på **Författare & bildskärm** på sidan Datafabrik.

4. På sidan **Kom igång** väljer du **fliken Bildskärm**.  ![Övervaka pipeline-körning](media/doc-common-process/get-started-page-monitor-button.png)

    > [!IMPORTANT]
    > Du ser att pipelines endast körs hel timme (till exempel: 4, 5, 6 osv.). Klicka på **Uppdatera** i verktygsfältet för att uppdatera listan när tiden når nästa timma.

5. Klicka på länken **Visa aktivitetskörningar** i kolumnen **Åtgärder.**

    ![Länk för pipelineåtgärder](media/quickstart-create-data-factory-resource-manager-template/pipeline-actions-link.png)

6. Du ser att aktiviteten som körs är associerad med pipelinekörningen. I den här snabbstarten har pipelinen endast en aktivitet av typen: kopiera. Därför kan du se en körning för den aktiviteten.

    ![Aktivitetskörningar](media/quickstart-create-data-factory-resource-manager-template/activity-runs.png)
7. Klicka på länken **Utdata** under Åtgärdskolumnen. Du ser utdata från kopieringsåtgärden i ett **utdatafönster**. Klicka på maximeringsknappen om du vill visa fullständiga utdata. Du kan stänga det maximerade utdatafönstret eller stänga det.

8. Stoppa utlösaren när du ser en lyckad/misslyckad körning. Utlösaren kör pipelinen en gång i timmen. Pipelinen kopierar samma fil från indatamappen till utdatamappen för varje körning. Kör följande kommando i PowerShell-fönstret om du vill stoppa utlösaren.
    
    ```powershell
    Stop-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)]

## <a name="json-definitions-for-entities"></a><a name="data-factory-entities-in-the-template"></a> JSON-definitioner för entiteter

Följande Data Factory-entiteter har definierats i JSON-mallen:

- [Azure Storage-länkad tjänst](#azure-storage-linked-service)
- [Binär indatauppsättning](#binary-input-dataset)
- [Binär datauppsättning](#binary-output-dataset)
- [Datapipeline med en kopieringsaktivitet](#data-pipeline)
- [Utlösare](#trigger)

#### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst

AzureStorageLinkedService länkar ditt Azure Storage-konto till datafabriken. Du har skapat en container och överfört data till det här lagringskontot som en del av förhandskraven. Du anger namnet och nyckeln för Azure Storage-kontot i det här avsnittet. Se [Länkad Azure Storage-tjänst](connector-azure-blob-storage.md#linked-service-properties) om du vill ha information om JSON-egenskaper som används för att definiera en länkad Azure Storage-tjänst.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateStorageLinkedService')]",
    "type":"Microsoft.DataFactory/factories/linkedServices",
    "apiVersion":"2018-06-01",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]"
    ]
}
```

connectionString använder parametrarna storageAccountName och storageAccountKey. Värdena för dessa parametrar skickades med hjälp av en konfigurationsfil. Definitionen använder också variablerna azureStorageLinkedService och dataFactoryName, som definieras i mallen.

#### <a name="binary-input-dataset"></a>Binär indatauppsättning

Den länkade Azure storage-tjänsten anger anslutningssträngen som Data Factory-tjänsten använder vid körning för att ansluta till ditt Azure storage-konto. I Definition av binär datauppsättning anger du namnen på blob-behållaren, mappen och filen som innehåller indata. Se [Binära datauppsättningsegenskaper](format-binary.md#dataset-properties) för information om JSON-egenskaper som används för att definiera en binär datauppsättning.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetIn')]",
    "type":"Microsoft.DataFactory/factories/datasets",
    "apiVersion":"2018-06-01",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"ArmtemplateStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "fileName":"emp.txt",
                "folderPath":"input",
                "container":"adftutorial"
            }
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
    ]
}
```

#### <a name="binary-output-dataset"></a>Binär datauppsättning

Du anger namnet på mappen i Azure Blob Storage som innehåller kopierade data från indatamappen. Se [Binära datauppsättningsegenskaper](format-binary.md#dataset-properties) för information om JSON-egenskaper som används för att definiera en binär datauppsättning.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetOut')]",
    "type":"Microsoft.DataFactory/factories/datasets",
    "apiVersion":"2018-06-01",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"ArmtemplateStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "folderPath":"output",
                "container":"adftutorial"
            }
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
    ]
}
```

#### <a name="data-pipeline"></a>Datapipeline

Du definierar en pipeline som kopierar data från en binär datauppsättning till en annan binär datauppsättning. Se [Pipeline-JSON](concepts-pipelines-activities.md#pipeline-json) för beskrivningar av JSON-element som används för att definiera en pipeline i det här exemplet.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateSampleCopyPipeline')]",
    "type":"Microsoft.DataFactory/factories/pipelines",
    "apiVersion":"2018-06-01",
    "properties":{  
        "activities":[  
            {  
                "name":"MyCopyActivity",
                "type":"Copy",
                "dependsOn":[  

                ],
                "policy":{  
                    "timeout":"7.00:00:00",
                    "retry":0,
                    "retryIntervalInSeconds":30,
                    "secureOutput":false,
                    "secureInput":false
                },
                "userProperties":[  

                ],
                "typeProperties":{  
                    "source":{  
                        "type":"BinarySource",
                        "storeSettings":{  
                            "type":"AzureBlobStorageReadSettings",
                            "recursive":true
                        }
                    },
                    "sink":{  
                        "type":"BinarySink",
                        "storeSettings":{  
                            "type":"AzureBlobStorageWriteSettings"
                        }
                    },
                    "enableStaging":false
                },
                "inputs":[  
                    {  
                        "referenceName":"ArmtemplateTestDatasetIn",
                        "type":"DatasetReference",
                        "parameters":{  

                        }
                    }
                ],
                "outputs":[  
                    {  
                        "referenceName":"ArmtemplateTestDatasetOut",
                        "type":"DatasetReference",
                        "parameters":{  

                        }
                    }
                ]
            }
        ],
        "annotations":[  

        ]
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetIn')]",
        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetOut')]"
    ]
}
```

#### <a name="trigger"></a>Utlösare

Du definierar en utlösare som kör pipelinen en gång per timme. Den distribuerade utlösaren har stoppats. Starta utlösaren med hjälp av **cmdleten Start-AzDataFactoryV2Trigger.** Mer information om utlösare finns i artikel [om pipelinekörning och utlösare.](concepts-pipeline-execution-triggers.md#trigger-execution)

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmTemplateTestTrigger')]",
    "type":"Microsoft.DataFactory/factories/triggers",
    "apiVersion":"2018-06-01",
    "properties":{  
        "annotations":[  

        ],
        "runtimeState":"Started",
        "pipelines":[  
            {  
                "pipelineReference":{  
                    "referenceName":"ArmtemplateSampleCopyPipeline",
                    "type":"PipelineReference"
                },
                "parameters":{  

                }
            }
        ],
        "type":"ScheduleTrigger",
        "typeProperties":{  
            "recurrence":{  
                "frequency":"Hour",
                "interval":1,
                "startTime":"[parameters('triggerStartTime')]",
                "endTime":"[parameters('triggerEndTime')]",
                "timeZone":"UTC"
            }
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/pipelines/ArmtemplateSampleCopyPipeline')]"
    ]
}
```

## <a name="reuse-the-template"></a>Återanvända mallen

I självstudien skapade du en mall för att definiera Data Factory-entiteter och en mall för att skicka värden för parametrar. Om du vill använda samma mall för att distribuera Data Factory-entiteter till olika miljöer skapar du en parameterfil för varje miljö och använder den när du distribuerar till den miljön.

Exempel:

```powershell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```

Observera att det första kommandot använder parameterfilen för utvecklingsmiljön, det andra för testmiljön och det tredje för produktionsmiljön.

Du kan även återanvända mallen för att utföra upprepade uppgifter. Skapa till exempel många datafabriker med en eller flera pipelines som implementerar samma logik, men alla datafabriker använder olika konton för Azure Storage. I det här scenariot använder du samma mall i samma miljö (utvecklings-, test- eller produktionsmiljö) med olika parameterfiler för att skapa datafabriker.

## <a name="next-steps"></a>Nästa steg

Pipeline i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Gå igenom [självstudiekurserna](tutorial-copy-data-dot-net.md) om du vill lära dig hur du använder Data Factory i fler scenarier.
