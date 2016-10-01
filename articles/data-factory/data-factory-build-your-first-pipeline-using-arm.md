<properties
    pageTitle="Skapa din första datafabrik (Resource Manager-mall) | Microsoft Azure"
    description="I de här självstudierna skapar du ett exempel på en Azure Data Factory-pipeline med hjälp av en Azure Resource Manager-mall."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/01/2016"
    ms.author="spelluru"/>


# Självstudie: Skapa din första Azure-datafabrik med hjälp av en Azure Resource Manager-mall
> [AZURE.SELECTOR]
- [Översikt och förutsättningar](data-factory-build-your-first-pipeline.md)
- [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Resource Manager-mall](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

I den här artikeln får du lära dig hur du använder en Azure Resource Manager-mall för att skapa din första Azure-datafabrik.

## Krav
- Läs igenom artikeln [Självstudier – översikt](data-factory-build-your-first-pipeline.md) och slutför de **nödvändiga** stegen.
- Följ instruktionerna i artikeln [Så här installerar och konfigurerar du Azure PowerShell](../powershell-install-configure.md) för att installera den senaste versionen av Azure PowerShell på datorn.
- Se [Redigera Azure Resource Manager-mallar](../resource-group-authoring-templates.md) om du vill lära dig mer om mallar i Azure Resource Manager. 

## Skapa en Azure Resource Manager-mall

I det här avsnittet kan du skapa följande Data Factory-enheter: 

1. En **datafabrik** med namnet **TutorialDataFactoryARM**. En datafabrik kan ha en eller flera pipelines. En pipeline kan innehålla en eller flera aktiviteter. Det kan till exempel vara en kopieringsaktivitet som kopierar data från en källa till ett måldatalager och en Hive-aktivitet för HDInsight som kör Hive-skript för att transformera indata. 
2. Två **länkade tjänster**: **StorageLinkedService** och **HDInsightOnDemandLinkedService**. Dessa länkade tjänster länkar ditt Azure-lagringskonto och ett Azure HDInsight-kluster på begäran till din datafabrik. In- och utdata för pipelinen i det här exemplet lagras i Azure Storage-kontot. En länkad HDInsight-tjänst används för att köra Hive-skriptet som anges i pipeline-aktiviteten i det här exemplet. Identifiera vilka datalager/beräkningstjänster som används i scenariot och länka dessa tjänster till datafabriken genom att skapa länkade tjänster. 
3. Två (indata/utdata) **datauppsättningar**: **AzureBlobInput** och **AzureBlobOutput**. Dessa datauppsättningar representerar in- och utdata för Hive-bearbetning. Dessa datauppsättningar finns i den **StorageLinkedService** som du skapade tidigare i självstudien. Den länkade tjänsten pekar på ett Azure-lagringskonto och datauppsättningarna anger behållare, mapp och filnamn i det lagringsutrymme som innehåller indata och utdata.   

Klicka på fliken **Använda Data Factory-redigeraren** för att växla till artikeln med information om de JSON-egenskaper som används i mallen.

Skapa en JSON-fil med namnet **ADFTutorialARM.json** i mappen **C:\ADFGetStarted** med följande innehåll:

> [AZURE.IMPORTANT] Ändra värdena för variablerna **storageAccountName** och **storageAccountKey**. Ändra **dataFactoryName** också eftersom namnet måste vara unikt.

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
        },
        "variables": {
            "dataFactoryName":  "TutorialDataFactoryARM",
            "storageAccountName":  "<AZURE STORAGE ACCOUNT NAME>" ,
            "storageAccountKey":  "<AZURE STORAGE ACCOUNT KEY>",
            "apiVersion": "2015-10-01",
            "storageLinkedServiceName": "AzureStorageLinkedService",
            "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
            "blobInputDataset": "AzureBlobInput",
            "blobOutputDataset": "AzureBlobOutput",
            "singleQuote": "'"
        },
        "resources": [
            {
                "name": "[variables('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "westus",
                "resources": [
                    {
                        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                        "type": "linkedservices",
                        "name": "[variables('storageLinkedServiceName')]",
                        "apiVersion": "[variables('apiVersion')]",
                        "properties": {
                            "type": "AzureStorage",
                            "typeProperties": {
                                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',variables('storageAccountKey'))]"
                            }
                        }
                    },
                    {
                        "dependsOn": [
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
                        ],
                        "type": "linkedservices",
                        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
                        "apiVersion": "[variables('apiVersion')]",
                        "properties": {
                            "type": "HDInsightOnDemand",
                            "typeProperties": {
                                "clusterSize": 4,
                                "version":  "3.2",
                                "timeToLive": "00:05:00",
                                "osType": "windows",
                                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                            }
                        }
                    },
                    {
                        "dependsOn": [
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
                        ],
                        "type": "datasets",
                        "name": "[variables('blobInputDataset')]",
                        "apiVersion": "[variables('apiVersion')]",
                            "properties": {
                                "type": "AzureBlob",
                                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                                "typeProperties": {
                                    "fileName": "input.log",
                                    "folderPath": "adfgetstarted/inputdata",
                                    "format": {
                                        "type": "TextFormat",
                                        "columnDelimiter": ","
                                    }
                                },
                                "availability": {
                                    "frequency": "Month",
                                    "interval": 1
                                },
                                "external": true,
                                "policy": {}
                            }
                        },
                    {
                        "dependsOn": [
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
                        ],
                        "type": "datasets",
                        "name": "[variables('blobOutputDataset')]",
                        "apiVersion": "[variables('apiVersion')]",
                            "properties": {
                                "published": false,
                                "type": "AzureBlob",
                                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                                "typeProperties": {
                                    "folderPath": "adfgetstarted/partitioneddata",
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
                            "dependsOn": [
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobInputDataset'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
                            ],
                            "type": "datapipelines",
                            "name": "[variables('dataFactoryName')]",
                            "apiVersion": "[variables('apiVersion')]",
                            "properties": {
                                "description": "My first Azure Data Factory pipeline",
                                "activities": [
                                    {
                                        "type": "HDInsightHive",
                                        "typeProperties": {
                                            "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                                            "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                                            "defines": {
                                                "inputtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                                                "partitionedtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
                                            }
                                        },
                                        "inputs": [
                                            {
                                                "name": "AzureBlobInput"
                                            }
                                        ],
                                        "outputs": [
                                            {
                                                "name": "AzureBlobOutput"
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
                                        "linkedServiceName": "HDInsightOnDemandLinkedService"
                                    }
                                ],
                                "start": "2016-04-01T00:00:00Z",
                                "end": "2016-04-02T00:00:00Z",
                                "isPaused": false
                            }
                        }
                ]
            }
        ]
    }

Klicka på fliken **Använda Data Factory-redigeraren** för att växla till artikeln med information om de JSON-egenskaper som används i mallen.

Observera följande: 

- Data Factory skapar ett **Windows-baserat** HDInsight-kluster med ovanstående JSON. Du hade också kunnat skapa ett **Linux-baserat** HDInsight-kluster. Se [HDInsight-länkad tjänst på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) för mer information. 
- Du kan använda **ditt eget HDInsight-kluster** i stället för att använda ett HDInsight-kluster på begäran. Se [HDInsight-länkad tjänst](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) för mer information.
- HDInsight-klustret skapar en **standardbehållare** i den blobblagring som du angav i JSON (**linkedServiceName**). HDInsight tar inte bort den här behållaren när klustret tas bort. Det här beteendet är avsiktligt. Med en HDInsight-länkad tjänst på begäran skapas ett HDInsight-kluster varje gång en sektor behöver bearbetas, såvida det inte finns ett befintligt livekluster (**timeToLive**). Det raderas när bearbetningen är klar.

    Allteftersom fler sektorer bearbetas kan du se mång behållare i ditt Azure Blob Storage. Om du inte behöver dem för att felsöka jobb, kan du ta bort dem för att minska lagringskostnaderna. Namnen på de här behållarna följer ett mönster: ”adf**datafabrikensnamn**-**denlänkadetjänstensnamn**-datumtidsstämpel”. Använd verktyg som [Microsoft Lagringsutforskaren](http://storageexplorer.com/) till att ta bort behållare i din Azure blobblagring.

Se [HDInsight-länkad tjänst på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) för mer information.

> [AZURE.NOTE] Du kan se ett annat exempel på en Resource Manager-mall för att skapa en Azure-datafabrik på [Github](https://github.com/Azure/azure-quickstart-templates/blob/master/101-data-factory-blob-to-sql/azuredeploy.json).  

## Skapa en datafabrik

1. Starta **Azure PowerShell** och kör följande kommando: 
    - Kör `Login-AzureRmAccount` och ange det användarnamn och lösenord som du använder för att logga in på Azure-portalen.  
    - Kör `Get-AzureRmSubscription` för att visa alla prenumerationer för det här kontot.
    - Kör `Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext` för att välja den prenumeration som du vill arbeta med. Den här prenumerationen ska vara samma som den du använde i Azure-portalen.
1. Kör följande kommando för att distribuera Data Factory-entiteter med hjälp av Resource Manager-mallen som du skapade i steg 1. 

        New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## Övervaka pipeline
 
1.  När du har loggat in på [Azure-portalen](https://portal.azure.com/) klickar du på **Bläddra** och väljer **Datafabriker**.
        ![Bläddra->Datafabriker](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.  På bladet **Datafabriker** klickar du på den datafabrik (**TutorialFactoryARM**) som du skapade.   
2.  På bladet **Data Factory** för din datafabrik klickar du på **Diagram**.
        ![Ikonen Diagram](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.  I **diagramvyn** visas en översikt över pipelines och datauppsättningar som används i den här självstudien.
    
    ![Diagramvy](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. Dubbelklicka på datauppsättningen **AzureBlobOutput** i diagramvyn. Den sektor som för närvarande bearbetas visas.

    ![Datauppsättning](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. När bearbetningen är klar visas sektorn med statusen **Klar**. Att skapa ett HDInsight-kluster på begäran kan ta lite längre tid (cirka 20 minuter). Förvänta dig därför att det tar **cirka 30 minuter** för pipelinen att bearbeta sektorn.

    ![Datauppsättning](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png) 
10. När sektorn har statusen **Klar**, kontrollerar du mappen **partitioneddata** i behållaren **adfgetstarted** i blobblagringen för utdatan.  

Se [Övervaka datauppsättningar och pipeline](data-factory-monitor-manage-pipelines.md) för instruktioner om hur du använder Azures portalblad till att övervaka pipelinen och datauppsättningar som du har skapat i den här självstudien.

Du kan också använda appen Övervaka och hantera till att övervaka dina datapipelines. Se [Övervaka och hantera Azure Data Factory-pipelines med övervakningsappen](data-factory-monitor-manage-app.md) för mer information om att använda programmet. 

> [AZURE.IMPORTANT] Indatafilen tas bort när sektorn har bearbetats. Om du vill köra sektorn eller gå igenom självstudien igen överför du därför indatafilen (input.log) till indatamappen i behållaren adfgetstarted.

## Resource Manger-mall för att skapa en gateway
Här är ett exempel på en Resource Manager-mall för att skapa en logisk gateway i bakgrunden. Installera en gateway på din lokala dator eller virtuella Azure IaaS-dator och registrera gatewayen med Data Factory-tjänsten med hjälp av en nyckel. Se [Flytta data mellan lokalt system och moln](data-factory-move-data-between-onprem-and-cloud.md) för mer information.

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

Den här mallen skapar en datafabrik som heter GatewayUsingArmDF med en gateway med namnet: GatewayUsingARM. 

## Se även
| Avsnitt | Beskrivning |
| :---- | :---- |
| [Datatransformeringsaktiviteter](data-factory-data-transformation-activities.md) | Den här artikeln innehåller en lista med de datatransformeringsaktiviteter (till exempel HDInsight Hive-transformeringen som du använde i självstudien) som stöds av Azure Data Factory. |
| [Schemaläggning och körning](data-factory-scheduling-and-execution.md) | I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. |
| [Pipelines](data-factory-create-pipelines.md) | I den här artikeln beskriver vi pipelines och aktiviteter i Azure Data Factory och hur du kan använda dem för att konstruera datadrivna arbetsflöden från slutpunkt till slutpunkt för ditt scenario eller ditt företag. |
| [Datauppsättningar](data-factory-create-datasets.md) | I den här artikeln förklaras hur datauppsättningar fungerar i Azure Data Factory.
| [Övervaka och hantera pipelines med övervakningsappen](data-factory-monitor-manage-app.md) | Den här artikeln beskriver hur du övervakar, hanterar och felsöker pipelines med övervaknings- och hanteringsappen. 

  




<!--HONumber=Sep16_HO3-->


