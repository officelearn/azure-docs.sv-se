---
title: Kopiera av lokala data till molnet med Azure Data Factory | Microsoft Docs
description: "Lär dig hur du kopierar data från ett lokalt datalager till Azure-molnet med hjälp av IR med egen värd i Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/14/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 60641ddfef7846f0e8b5d850e716b2652bf62367
ms.contentlocale: sv-se
ms.lasthandoff: 09/26/2017

---

# <a name="copy-data-between-on-premises-and-cloud"></a>Kopiera data mellan lokalt system och molnet
Azure Data Factory är en molnbaserad dataintegreringstjänst som gör att du kan skapa datadrivna arbetsflöden i molnet för att samordna och automatisera dataförflyttning och dataomvandling. Med Azure Data Factory kan du skapa och schemalägga datadrivna arbetsflöden (kallas pipelines) som kan föra in data från olika datalager, bearbeta/omvandla data med beräkningstjänster som Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics och Azure Machine Learning och publicera utgående data till datalager som Azure SQL Data Warehouse för BI-program (business intelligence) kan använda. 

I den här självstudien använder du Azure PowerShell för att skapa en Data Factory-pipeline som kopierar data från en lokal SQL Server-databas till Azure Blob Storage. Du skapar en IR med egen värd i Azure Data Factory, som tillåter integrering av lokala datalager och molndatalager.  Läs mer om att använda andra verktyg/SDK:er för att skapa datafabriker i [Snabbstarter](quickstart-create-data-factory-dot-net.md). 

I de här självstudierna går du igenom följande steg:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa Integration Runtime med egen värd.
> * Skapa och kryptera en länkad lokal SQL Server-tjänst på IR med egen värd.
> * Skapa en länkad Azure-lagringstjänst.
> * Skapa SQL Server- och Azure Storage-datauppsättningar.
> * Skapa en pipeline med en kopieringsaktivitet för att flytta data.
> * Starta en pipelinekörning.
> * Övervaka pipelinen och aktivitetskörningar.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Krav

* **SQL Server**. Du använder en lokal SQL Server-databas som **källdata** i den här självstudien. 
* **Azure Storage-konto**. Du använder Azure Blob Storage som ett **mål-/mottagardatalager** i den här självstudien. om du inte har ett Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account).
* **Azure PowerShell**. Följ instruktionerna i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-azurerm-ps).

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
    $df = Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Observera följande punkter:

    * Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.

        ```
        Data factory name "<data factory name>" is not available.
        ```

    * Om du vill skapa Data Factory-instanser måste du vara deltagare/administratör för Azure-prenumerationen.
    * För närvarande kan du endast skapa en datafabrik i Data Factory i USA, östra och USA, östra 2. Datalagren (Azure Storage, Azure SQL Database etc.) och beräkningarna (HDInsight etc.) som används av datafabriker kan finnas i andra regioner.

## <a name="create-a-self-hosted-ir"></a>Skapa IR med egen värd

I det här avsnittet kan du skapa en Integration Runtime med egen värd och koppla den till en lokal nod (dator).

1. Skapa Integration Runtime med egen värd. Använd ett unikt namn om det finns en annan Integration Runtime med samma namn.

   ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Här är exempel på utdata:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. Kör följande kommando för att hämta status för din skapade integration runtime.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Här är exempel på utdata:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption : 
   Version                   : 
   Capabilities              : {}
   ScheduledUpdateDate       : 
   UpdateDelayOffset         : 
   LocalTimeZoneOffset       : 
   AutoUpdate                : 
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. Kör följande kommando för att hämta autentiseringsnycklarna för att registrera Integration Runtime med tjänsten Data Factory i molnet. Kopiera någon av nycklarna för att registrera Integration Runtime med egen värd.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Här är exempel på utdata: 

   ```json
   {
       "AuthKey1":  "IR@8437c862-d6a9-4fb3-87dd-7d4865a9e845@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@8437c862-d6a9-4fb3-85dd-7d4865a9e845@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

4. [Ladda ned](https://www.microsoft.com/download/details.aspx?id=39717) Integration Runtime med egen värd på en lokal Windows-dator och använd autentiseringsnyckeln du fick i det föregående steget för att manuellt registrera Integration Runtime med egen värd. 

   ![Registrera Integration Runtime](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

   När integration runtime med egen värd har registrerats ser du följande meddelande: 

   ![Registered successfully (Registrerat)](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

   Du ser följande sida när noden är ansluten till molntjänsten: 
    
   ![Node is connected (Noden är ansluten)](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>Skapa länkade tjänster 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Skapa en länkad Azure Storage-tjänst (destination/mottagare)

1. Skapa en JSON-fil med namne **AzureStorageLinkedService.json** i mappen **C:\ADFv2Tutorial** med följande innehåll. Skapa mappen ADFv2Tutorial om den inte redan finns.  Ersätt &lt;accountname&gt; och &lt;accountkey&gt; med namnet och nyckeln för ditt Azure-lagringskonto.

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": { 
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

2. I **Azure PowerShell** växlar du till mappen **ADFv2Tutorial**.

   Kör cmdleten **Set-AzureRmDataFactoryV2LinkedService** för att skapa den länkade tjänsten: **AzureStorageLinkedService**. Cmdletarna som används i den här självstudien tar värden för parametrarna **ResourceGroupName** och **DataFactoryName**. Du kan också skicka **DataFactory**-objektet som returnerades av cmdleten Set-AzureRmDataFactoryV2 utan att ange ResourceGroupName och DataFactoryName varje gång du kör en cmdlet.

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Här är exempel på utdata:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Skapa och kryptera en SQL Server som är länkad tjänst (källa)

1. Skapa en JSON-fil med namnet **SqlServerLinkedService.json** i mappen **C:\ADFv2Tutorial** med följande innehåll: Ersätt **&lt;servername>**, **&lt;databasename>**, **&lt;username>**, **&lt;servername>** och **&lt;password>** med dina SQL Server-värden innan du sparar filen. Ersätt **&lt;integration** **runtime** **name>** med namnet på din Integration Runtime. 

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```
2. För att kryptera känsliga data från JSON-nyttolasten på den lokala IR med egen värd kan vi köra **New-AzureRmDataFactoryV2LinkedServiceEncryptCredential** och skicka vidare JSON-nyttolasten ovan. Den här krypteringen ser till att autentiseringsuppgifterna krypteras med Data Protection Application Programming Interface (DPAPI) och lagras på IR-noden med egen värd lokalt. Nyttolasten i utdata kan omdirigeras till en annan JSON-fil (i det här fallet ”encryptedLinkedService.json”) som innehåller krypterade autentiseringsuppgifter. 

    Ersätt **&lt;integration runtime name&gt;** med namnet på din Integration Runtime innan du kör kommandot.

   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName <integration runtime name> -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Kör följande kommando med hjälp av JSON-filen från föregående steg för att skapa **SqlServerLinkedService**:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json" 
   ```


## <a name="create-datasets"></a>Skapa datauppsättningar

### <a name="prepare-an-on-premises-sql-server-for-the-tutorial"></a>Förbereda en lokal SQL Server för självstudien

I det här steget kan du skapa indata och utdata datauppsättningar som representerar inkommande och utgående data för kopieringen (lokal SQL Server-databas = > Azure Blob Storage). Innan du skapar datauppsättningar gör du följande steg (detaljerade anvisningar kommer i listan):

- Skapa en tabell med namnet **emp** i SQL Server-databasen du la till som en länkad tjänst till datafabriken och infoga ett par exempelposter i tabellen.
- Skapa en blobbehållare med namnet **adftutorial** i Azure Blob Storage-kontot du la till som en länkad tjänst i datafabriken.


1. I databasen som du angav för den lokala länkade SQL Server-tjänsten (**SqlServerLinkedService**) använder du följande SQL-skript för att skapa tabellen **emp** i databasen.

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Infoga vissa exempel i tabellen:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```



### <a name="create-a-dataset-for-source-sql-database"></a>Skapa en datauppsättning för SQL Database-källan

1. Skapa en JSON-fil med namnet **SqlServerDataset.json** i mappen **C:\ADFv2Tutorial** med följande innehåll:  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. Så här skapar du datauppsättningen: **SqlServerDataset**, kör cmdleten **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Här är exempel på utdata:

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-blob-storage"></a>Skapa en datauppsättning för Azure Blob Storage-mottagaren

1. Skapa en JSON-fil med namnet **AzureBlobDataset.json** i mappen **C:\ADFv2Tutorial** med följande innehåll: 

    > [!IMPORTANT]
    > Den här exempelkoden förutsätter att du har en behållare med namnet **adftutorial** i Azure Blob Storage.

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. Så här skapar du datauppsättningen: **AzureBlobDataset**, kör cmdleten **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Här är exempel på utdata:

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-pipelines"></a>Skapa pipelines

### <a name="create-the-pipeline-sqlservertoblobpipeline"></a>Skapa pipelinen "SqlServerToBlobPipeline"

1. Skapa en JSON-fil med namnet **SqlServerToBlobPipeline.json** i mappen **C:\ADFv2Tutorial** med följande innehåll:

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Så här skapar du en pipeline: **SQLServerToBlobPipeline**, kör cmdleten **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Här är exempel på utdata:

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```



## <a name="start-and-monitor-a-pipeline-run"></a>Starta och övervaka en pipelinekörning

1. Starta en pipelinekörning för "SQLServerToBlobPipeline" och avbilda pipelinekörningens ID för framtida övervakning.  

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
    ```

2. Kör följande skript för att kontrollera körningsstatusen kontinuerligt för pipelinen "**SQLServerToBlobPipeline**", och skriv ut slutresultatet.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    } 
    ```

    Här är utdata för exempelkörningen:

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName : 
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Du kan få körnings-ID för pipelinen "**SQLServerToBlobPipeline**", och kontrollera aktivitetens detaljerade körningsresultat enligt följande.

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Här är utdata för exempelkörningen:

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 4,
      "throughput": 0.01,
      "errors": []
    }
    ```
4. Anslut till ditt Azure Blob Storage-mottagare och bekräfta att data har kopierats från Azure SQL Database ordentligt.

## <a name="next-steps"></a>Nästa steg
Pipeline i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Du har lärt dig att: 

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa Integration Runtime med egen värd
> * Skapa och kryptera en länkad lokal SQL Server-tjänst på IR med egen värd
> * Skapa en länkad Azure-lagringstjänst.
> * Skapa SQL Server- och Azure Storage-datauppsättningar.
> * Skapa en pipeline med en kopieringsaktivitet för att flytta data.
> * Starta en pipelinekörning.
> * Övervaka pipelinen och aktivitetskörningar.

I artikeln [Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) finns en lista över datalager som stöds av Azure Data Factory som källor och mottagare.

Fortsätt till följande självstudie för att lära dig att masskopiera data från en källa till ett mål:

> [!div class="nextstepaction"]
>[Masskopiera data i molnet](tutorial-bulk-copy.md)
