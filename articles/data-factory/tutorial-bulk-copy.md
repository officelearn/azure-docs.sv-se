---
title: Masskopiera data med Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder Azure Data Factory och kopieringsaktiviteten till att masskopiera data från ett källdatalager till ett måldatalager.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 65c40885a8dde2e07deb2a4f94a3dc6a05ef78d0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Kopiera flera tabeller i grupp med Azure Data Factory
I den här självstudien visas hur du **kopierar ett antal tabeller från Azure SQL Database till Azure SQL Data Warehouse**. Du kan även använda samma mönster i andra kopieringssituationer. Till exempel kan du kopiera tabeller från SQL Server/Oracle till Azure SQL Database/Data Warehouse/Azure Blob eller kopiera olika sökvägar från Blob till Azure SQL Database-tabeller.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen för Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Sett på en hög nivå ingår följande steg i självstudierna:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa länkade tjänster för Azure SQL Database, Azure SQL Data Warehouse och Azure Storage.
> * Skapa datauppsättningar för Azure SQL Database och Azure SQL Data Warehouse.
> * Skapa en pipeline för sökning av de tabeller som ska kopieras och en annan pipeline för den faktiska kopieringsåtgärden. 
> * Starta en pipelinekörning.
> * Övervaka pipelinen och aktivitetskörningarna.

I den här kursen används Azure PowerShell. Läs mer om att använda andra verktyg/SDK:er för att skapa en datafabrik i [Snabbstarter](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Arbetsflödet slutpunkt till slutpunkt
I det här scenariot har vi ett antal tabeller i Azure SQL Database som vi vill kopiera till SQL Data Warehouse. Här är den logiska ordningsföljden i arbetsflödet som sker i våra pipelines:

![Arbetsflöde](media/tutorial-bulk-copy/tutorial-copy-multiple-tables.png)

* Den första pipelinen letar rätt på listan med tabeller som ska kopieras till de mottagande datalagren.  Du kan istället underhålla en metadatatabell som innehåller alla tabeller som ska kopieras till de mottagande datalagren. Sedan utlöser pipelinen en annan pipeline, som itererar över varje tabell i databasen och utför själva datakopieringen.
* Den andra pipelinen utför den faktiska kopieringen. Den tar listan med tabeller som en parameter. För varje tabell i listan kopieras tabellen i Azure SQL Database till motsvarande tabell i SQL Data Warehouse, med hjälp av [mellanlagrad kopiering via Blob Storage och PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) för bästa möjliga prestanda. I det här exemplet skickar den första pipelinen listan med tabeller som värde för parametern. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* **Azure PowerShell**. Följ instruktionerna i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-azurerm-ps).
* **Azure Storage-konto**. Azure Storage-kontot används för mellanlagring för Blob Storage i masskopieringsåtgärden. 
* **Azure SQL Database**. Den här databasen innehåller källdata. 
* **Azure SQL Data Warehouse**. Det här datalagret innehåller de data som kopieras från SQL Database. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>Förbereda SQL Database och SQL Data Warehouse

**Förbered Azure SQL Database-källan**:

Skapa en Azure SQL Database med exempeldata för Adventure Works LT genom att följa anvisningarna i artikeln [Skapa en Azure SQL-databas](../sql-database/sql-database-get-started-portal.md). I den här självstudien kopieras alla tabeller från den här exempeldatabasen till SQL Data Warehouse.

**Förbered det mottagande Azure SQL Data Warehouse-datalagret**:

1. Om du inte har något Azure SQL Database Warehouse kan du läsa om att skapa ett i artikeln [Skapa ett Azure SQL Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).

2. Skapa motsvarande tabellscheman i SQL Data Warehouse. Du kan använda [migreringsverktyget](https://www.microsoft.com/download/details.aspx?id=49100) till att **migrera scheman** från Azure SQL Database till Azure SQL Data Warehouse. Du kommer att använda Azure Data Factory till att migrera/kopiera data i ett senare steg.

## <a name="azure-services-to-access-sql-server"></a>Azure-tjänster för åtkomst till SQL-servern

Ge Azure-tjänster åtkomst till SQL-servern för både SQL Database och SQL Data Warehouse. Se till att inställningen **Tillåt åtkomst till Azure-tjänster** är **aktiverad** för Azure SQL-servern. Den här inställningen gör att Data Factory-tjänsten kan läsa data från Azure SQL Database och skriva data till Azure SQL Data Warehouse. Gör så här för att kontrollera och aktivera den här inställningen:

1. Klicka på **Alla tjänster** till vänster och klicka på **SQL-servrar**.
2. Välj din server och klicka på **Brandvägg** under **INSTÄLLNINGAR**.
3. På sidan **Brandväggsinställningar** väljer du **På** för **Tillåt åtkomst till Azure-tjänster**.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta **PowerShell**. Låt Azure PowerShell vara öppet tills du är klar med självstudien. Om du stänger och öppnar det igen måste du köra kommandona en gång till.

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
2. Kör cmdleten **Set-AzureRmDataFactoryV2** för att skapa en datafabrik. Ersätt platshållarna med egna värden innan du kör kommandot. 

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Observera följande punkter:

    * Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * När du ska skapa Data Factory-instanser måste du vara deltagare/administratör för Azure-prenumerationen.
    * För närvarande kan du endast skapa datafabriker i Data Factory V2 i regionerna USA, östra; USA; östra 2 och Europa, västra. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.

## <a name="create-linked-services"></a>Skapa länkade tjänster

I den här självstudien skapar du tre länkade tjänster för käll-, mottagar- och mellanlagringsbloben, som innehåller anslutningar till dina datalager:

### <a name="create-the-source-azure-sql-database-linked-service"></a>Skapa den länkade tjänsten för Azure SQL Database-källan

1. Skapa en JSON-fil med namnet **AzureSqlDatabaseLinkedService.json** i mappen **C:\ADFv2TutorialBulkCopy** med följande innehåll: (skapa mappen ADFv2TutorialBulkCopy om den inte redan finns.)

    > [!IMPORTANT]
    > Ersätt &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; och &lt;password&gt; med värdena för din Azure SQL Database innan du sparar filen.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. I **Azure PowerShell** växlar du till mappen **ADFv2TutorialBulkCopy**.

3. Kör cmdleten **Set-AzureRmDataFactoryV2LinkedService** för att skapa den länkade tjänsten **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

    Här är exempel på utdata:

    ```json
    LinkedServiceName : AzureSqlDatabaseLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>Skapa den länkade tjänsten för Azure SQL Data Warehouse-mottagaren

1. Skapa en JSON-fil med namnet **AzureSqlDWLinkedService.json** i mappen **C:\ADFv2TutorialBulkCopy** med följande innehåll:

    > [!IMPORTANT]
    > Ersätt &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; och &lt;password&gt; med värdena för din Azure SQL Database innan du sparar filen.

    ```json
    {
        "name": "AzureSqlDWLinkedService",
        "properties": {
            "type": "AzureSqlDW",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
            }
        }
    }
    ```

2. Kör cmdleten **Set-AzureRmDataFactoryV2LinkedService** för att skapa den länkade tjänsten **AzureSqlDWLinkedService**.

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWLinkedService" -File ".\AzureSqlDWLinkedService.json"
    ```

    Här är exempel på utdata:

    ```json
    LinkedServiceName : AzureSqlDWLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDWLinkedService
    ```

### <a name="create-the-staging-azure-storage-linked-service"></a>Skapa den länkade tjänsten för Azure Storage-mellanlagringen

I den här självstudien använder du Azure Blob Storage som ett mellanlagringsutrymme så att PolyBase aktiveras och ger kopieringen bättre prestanda.

1. Skapa en JSON-fil med namnet **AzureStorageLinkedService.json** i mappen **C:\ADFv2TutorialBulkCopy** med följande innehåll:

    > [!IMPORTANT]
    > Ersätt &lt;accountName&gt; och &lt;accountKey&gt; med namnet och nyckeln för ditt Azure Storage-konto innan du sparar filen.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
                }
            }
        }
    }
    ```

2. Kör cmdleten **Set-AzureRmDataFactoryV2LinkedService** för att skapa den länkade tjänsten **AzureStorageLinkedService**.

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Här är exempel på utdata:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-datasets"></a>Skapa datauppsättningar

I den här självstudien skapar du datauppsättningar för källa och mottagare som anger var data lagras:

### <a name="create-a-dataset-for-source-sql-database"></a>Skapa en datauppsättning för SQL Database-källan

1. Skapa en JSON-fil med namnet **AzureSqlDatabaseDataset.json** i mappen **C:\ADFv2TutorialBulkCopy** med följande innehåll: ”tableName” är en platshållare eftersom du senare använder SQL-frågan i kopieringsaktiviteten till att hämta data.

    ```json
    {
        "name": "AzureSqlDatabaseDataset",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": "dummy"
            }
        }
    }
    ```

2. Kör cmdleten **Set-AzureRmDataFactoryV2Dataset** för att skapa datauppsättningen **AzureSqlDatabaseDataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseDataset" -File ".\AzureSqlDatabaseDataset.json"
    ```

    Här är exempel på utdata:

    ```json
    DatasetName       : AzureSqlDatabaseDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>Skapa en datauppsättning för SQL Data Warehouse-mottagaren

1. Skapa en JSON-fil med namnet **AzureSqlDWDataset.json** i mappen **C:\ADFv2TutorialBulkCopy** med följande innehåll: ”tableName” anges som en parameter, senare skickar kopieringsaktiviteten som refererar till den här datauppsättningen det faktiska värdet till datauppsättningen.

    ```json
    {
        "name": "AzureSqlDWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDWLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().DWTableName}",
                    "type": "Expression"
                }
            },
            "parameters":{
                "DWTableName":{
                    "type":"String"
                }
            }
        }
    }
    ```

2. Kör cmdleten **Set-AzureRmDataFactoryV2Dataset** för att skapa datauppsättningen **AzureSqlDWDataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWDataset" -File ".\AzureSqlDWDataset.json"
    ```

    Här är exempel på utdata:

    ```json
    DatasetName       : AzureSqlDWDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDwTableDataset
    ```

## <a name="create-pipelines"></a>Skapa pipelines

I den här självstudien skapar du två pipelines:

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Skapa pipelinen ”IterateAndCopySQLTables”

Den här pipelinen tar en lista med tabeller som en parameter. För varje tabell i listan kopieras data från tabellen i Azure SQL Database till Azure SQL Data Warehouse med hjälp av mellanlagrad kopiering och PolyBase.

1. Skapa en JSON-fil med namnet **IterateAndCopySQLTables.json** i mappen **C:\ADFv2TutorialBulkCopy** med följande innehåll:

    ```json
    {
        "name": "IterateAndCopySQLTables",
        "properties": {
            "activities": [
                {
                    "name": "IterateSQLTables",
                    "type": "ForEach",
                    "typeProperties": {
                        "isSequential": "false",
                        "items": {
                            "value": "@pipeline().parameters.tableList",
                            "type": "Expression"
                        },
                        "activities": [
                            {
                                "name": "CopyData",
                                "description": "Copy data from SQL database to SQL DW",
                                "type": "Copy",
                                "inputs": [
                                    {
                                        "referenceName": "AzureSqlDatabaseDataset",
                                        "type": "DatasetReference"
                                    }
                                ],
                                "outputs": [
                                    {
                                        "referenceName": "AzureSqlDWDataset",
                                        "type": "DatasetReference",
                                        "parameters": {
                                            "DWTableName": "[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                        }
                                    }
                                ],
                                "typeProperties": {
                                    "source": {
                                        "type": "SqlSource",
                                        "sqlReaderQuery": "SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                    },
                                    "sink": {
                                        "type": "SqlDWSink",
                                        "preCopyScript": "TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]",
                                        "allowPolyBase": true
                                    },
                                    "enableStaging": true,
                                    "stagingSettings": {
                                        "linkedServiceName": {
                                            "referenceName": "AzureStorageLinkedService",
                                            "type": "LinkedServiceReference"
                                        }
                                    }
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```

2. Kör cmdleten **Set-AzureRmDataFactoryV2Pipeline** för att skapa pipelinen **SQLServerToBlobPipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IterateAndCopySQLTables" -File ".\IterateAndCopySQLTables.json"
    ```

    Här är exempel på utdata:

    ```json
    PipelineName      : IterateAndCopySQLTables
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Skapa pipelinen ”GetTableListAndTriggerCopyData”

Den här pipelinen utför två steg:

* den söker i Azure SQL Database-systemtabellen för att få fram listan med tabeller som ska kopieras
* den utlöser pipelinen ”IterateAndCopySQLTables” för att utföra den faktiska kopieringen.

1. Skapa en JSON-fil med namnet **GetTableListAndTriggerCopyData.json** i mappen **C:\ADFv2TutorialBulkCopy** med följande innehåll:

    ```json
    {
        "name":"GetTableListAndTriggerCopyData",
        "properties":{
            "activities":[
                { 
                    "name": "LookupTableList",
                    "description": "Retrieve the table list from Azure SQL dataabse",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'"
                        },
                        "dataset": {
                            "referenceName": "AzureSqlDatabaseDataset",
                            "type": "DatasetReference"
                        },
                        "firstRowOnly": false
                    }
                },
                {
                    "name": "TriggerCopy",
                    "type": "ExecutePipeline",
                    "typeProperties": {
                        "parameters": {
                            "tableList": {
                                "value": "@activity('LookupTableList').output.value",
                                "type": "Expression"
                            }
                        },
                        "pipeline": {
                            "referenceName": "IterateAndCopySQLTables",
                            "type": "PipelineReference"
                        },
                        "waitOnCompletion": true
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupTableList",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Kör cmdleten **Set-AzureRmDataFactoryV2Pipeline** för att skapa pipelinen **GetTableListAndTriggerCopyData**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "GetTableListAndTriggerCopyData" -File ".\GetTableListAndTriggerCopyData.json"
    ```

    Här är exempel på utdata:

    ```json
    PipelineName      : GetTableListAndTriggerCopyData
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {LookupTableList, TriggerCopy}
    Parameters        :
    ```

## <a name="start-and-monitor-pipeline-run"></a>Starta och övervaka en pipelinekörning

1. Starta en pipelinekörning för huvudpipelinen ”GetTableListAndTriggerCopyData” och spara pipelinekörningens ID för framtida övervakning. Därefter utlöses körningen av pipelinen ”IterateAndCopySQLTables” som anges i aktiviteten ExecutePipeline.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'GetTableListAndTriggerCopyData'
    ```

2.  Kör följande skript som kontinuerligt kontrollerar körningsstatus för pipelinen **GetTableListAndTriggerCopyData** och skriver ut slutresultatet för pipelinekörningen och aktiviteten.

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                Write-Host "Pipeline run details:" -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 15
    }

    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result
    ```

    Här är utdata från exempelkörningen:

    ```json
    Pipeline run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    RunId             : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    LastUpdated       : 9/18/2017 4:08:15 PM
    Parameters        : {}
    RunStart          : 9/18/2017 4:06:44 PM
    RunEnd            : 9/18/2017 4:08:15 PM
    DurationInMs      : 90637
    Status            : Succeeded
    Message           : 

    Activity run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : LookupTableList
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {source, dataset, firstRowOnly}
    Output            : {count, value, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:06:46 PM
    ActivityRunEnd    : 9/18/2017 4:07:09 PM
    DurationInMs      : 22995
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : TriggerCopy
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {pipeline, parameters, waitOnCompletion}
    Output            : {pipelineRunId}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:07:11 PM
    ActivityRunEnd    : 9/18/2017 4:08:14 PM
    DurationInMs      : 62581
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Du kan få fram körnings-ID:t för pipelinen ”**IterateAndCopySQLTables**” och se detaljerade resultat från aktivitetskörningen genom att köra följande:

    ```powershell
    Write-Host "Pipeline 'IterateAndCopySQLTables' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "TriggerCopy"}).Output.ToString()
    ```

    Här är utdata från exempelkörningen:

    ```json
    {
        "pipelineRunId": "7514d165-14bf-41fb-b5fb-789bea6c9e58"
    }
    ```

    ```powershell
    $result2 = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId <copy above run ID> -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result2
    ```

3. Anslut till Azure SQL Data Warehouse-mottagaren och bekräfta att data har kopierats från Azure SQL Database.

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du fått: 

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa länkade tjänster för Azure SQL Database, Azure SQL Data Warehouse och Azure Storage.
> * Skapa datauppsättningar för Azure SQL Database och Azure SQL Data Warehouse.
> * Skapa en pipeline för sökning av de tabeller som ska kopieras och en annan pipeline för den faktiska kopieringsåtgärden. 
> * Starta en pipelinekörning.
> * Övervaka pipelinen och aktivitetskörningarna.

Fortsätt till följande självstudiekurs om du vill lära dig att kopiera data stegvis från en källa till ett mål:
> [!div class="nextstepaction"]
>[Kopiera data stegvis](tutorial-incremental-copy-powershell.md)