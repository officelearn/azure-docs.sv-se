---
title: "Kopiera data från SQL Server till Blob Storage med hjälp av Azure Data Factory | Microsoft Docs"
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
ms.date: 11/14/2017
ms.author: jingwang
ms.openlocfilehash: afd7735712d03110a67509a7e94d336219a65b34
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="tutorial-copy-data-from-on-premises-sql-server-to-azure-blob-storage"></a>Självstudier: Kopiera data från SQL Server lokalt till Azure Blob Storage
I den här självstudien använder du Azure PowerShell för att skapa en Data Factory-pipeline som kopierar data från en lokal SQL Server-databas till Azure Blob Storage. Du skapar en IR med egen värd i Azure Data Factory, som tillåter integrering av lokala datalager och molndatalager.  Läs mer om att använda andra verktyg/SDK:er för att skapa datafabriker i [Snabbstarter](quickstart-create-data-factory-dot-net.md).

Den här artikeln ger inte någon detaljerad introduktion till Azure Data Factory-tjänsten. En introduktion till Azure Data Factory-tjänsten finns i [Introduktion till Azure Data Factory](introduction.md). 

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen för Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

I den här självstudiekursen får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa Integration Runtime med egen värd.
> * Skapa länkade tjänster för SQL Server och Azure Storage. 
> * Skapa datauppsättningar för SQL Server och Azure Blob.
> * Skapa en pipeline med en kopieringsaktivitet för att flytta data.
> * Starta en pipelinekörning.
> * Övervaka pipelinekörningen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Krav

### <a name="sql-server-2014-or-2016"></a>SQL Server 2014 eller 2016. 
Du använder en lokal SQL Server-databas som **källdata** i den här självstudien. Skapa en tabell med namnet **emp** i SQL Server-databasen och infoga ett par exempelposter i tabellen.

1. Starta **SQL Server Management Studio**. Om du använder SQL Server 2016 kan du behöva installera SQL Server Management Studio separat från [Download Center](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms). 
2. Anslut till SQL-servern med hjälp av dina autentiseringsuppgifter. 
3. Skapa en exempeldatabas. I trädvyn högerklickar du på **Databaser** och klicka på **Ny databas**. Gå till dialogrutan **Ny databas**, ange ett **namn** för databasen och klicka på **OK**. 
4. Kör följande frågeskript mot databasen, vilket skapar tabellen **emp**. I trädvyn högerklickar du på **databasen** du har skapat och klickar på **Ny fråga**. 

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
2. Kör följande kommandon mot databasen som infogar exempeldata i tabellen:

    ```sql
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="azure-storage-account"></a>Azure-lagringskonto
I den här självstudiekursen använder du ett allmänt Azure Storage-konto (Blob Storage, för att vara specifik) som datalager för **destination/mottagare**. Om du inte har något allmänt Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account).

#### <a name="get-storage-account-name-and-account-key"></a>Hämta lagringskontots namn och åtkomstnyckel
Du använder namnet och nyckeln för Azure Storage-kontot i den här snabbstarten. Följande procedur innehåller steg för att få fram namnet och nyckeln för ditt lagringskonto. 

1. Öppna webbläsaren och gå till [Azure Portal](https://portal.azure.com). Logga in med ditt Azure-användarnamn och lösenord. 
2. Klicka på **Fler tjänster >** i den vänstra menyn, filtrera på nyckelordet **Lagring** och välj **Lagringskonton**.

    ![Sök efter lagringskontot](media/tutorial-hybrid-copy-powershell/search-storage-account.png)
3. Filtrera på ditt lagringskonto (om det behövs) i listan med lagringskonton och välj sedan **ditt lagringskonto**. 
4. Gå till sidan **Lagringskonto** väljer du **Åtkomstnycklar** i menyn.

    ![Hämta lagringskontots namn och nyckel](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)
5. Kopiera värdena från fälten med **lagringskontots namn** och **nyckel 1** till Urklipp. Klistra in dem i Anteckningar eller något annat redigeringsprogram och spara. Du kan använda lagringskontots namn och nyckel i självstudiekursen. 

#### <a name="create-the-adftutorial-container"></a>Skapa behållaren adftutorial 
Det här avsnittet förutsätter att du har en blobbehållare med namnet adftutorial i Azure Blob Storage. 

1. Installera [lagringsutforskaren för Azure](https://azure.microsoft.com/features/storage-explorer/) om du inte har den på din dator. 
2. Starta **Azure Storage Explorer** på din dator.   
3. Gå till fönstret **Anslut till Azure Storage**, välj **Använd lagringskontonamn och nyckel** och klicka på **Nästa**. Om du inte ser fönstret **Anslut till Azure Storage** högerklickar du på **Lagringskonton** i trädvyn och klickar på **Anslut till Azure Storage**. 

    ![Anslut till Azure Storage](media/tutorial-hybrid-copy-powershell/storage-explorer-connect-azure-storage.png)
4. I fönstret **Bifoga namn och nyckel** klistrar du in det **kontonamn** och **kontonyckel** som du sparade i det förra steget. Klicka sedan på **Nästa**. 
5. I fönstret med **anslutningssammanfattningen** klickar du på **Anslut**.
6. Bekräfta att du ser ditt lagringskonto i trädvyn under **(Lokal och bifogad)** -> **Lagringskonton**. 
7. Expandera **Blobbehållare** och kontrollera att blobbehållaren **adftutorial** inte finns. Om den redan finns hoppar du över nästa steg för att skapa behållaren. 
8. Högerklicka på **Blobbehållare** och välj **Skapa blobbehållare**.

    ![Skapa blobbehållare](media/tutorial-hybrid-copy-powershell/stroage-explorer-create-blob-container-menu.png)
9. Ange **adftutorial** som namn och tryck på **RETUR**. 
10. Bekräfta att behållaren **adftutorial** är vald i trädvyn. Data Factory skapar automatiskt utdatamappen i den här behållaren, så du behöver inte skapa en. 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Installera Azure PowerShell
Installera den senaste versionen av Azure PowerShell om du inte redan har den på din dator. 

1. Öppna webbläsaren och gå till sidan [Ladda ned Azure-SDK:er och verktyg](https://azure.microsoft.com/downloads/). 
2. Klicka på **Windows-installation** i avsnittet **Kommandoradsverktyg** -> **PowerShell**. 
3. Kör **MSI**-filen för att installera Azure PowerShell. 

Mer detaljerade anvisningar finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-azure-powershell"></a>Logga in på Azure PowerShell
Starta **PowerShell** på din dator. Låt Azure PowerShell vara öppet tills du är klar med snabbstarten. Om du stänger och öppnar det igen måste du köra kommandona en gång till.

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

1. Definiera en variabel för resursgruppens namn som du kan använda senare i PowerShell-kommandon. Kopiera följande kommandotext till PowerShell, ange ett namn för [Azure-resursgruppen](../azure-resource-manager/resource-group-overview.md), sätt dubbla citattecken omkring namnet och kör sedan kommandot. 
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. Definiera en variabel för datafabrikens namn som du kan använda senare i PowerShell-kommandon. 

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

    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$resourceGroupName` ett annat värde och försök igen. Om du vill dela resursgruppen med andra går du vidare med nästa steg.  
5. Kör följande cmdlet av typen **Set-AzureRmDataFactoryV2** för att skapa en datafabrik: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* När du ska skapa Data Factory-instanser måste du vara **deltagare** eller **administratör** för Azure-prenumerationen.
* För närvarande kan du endast skapa datafabriker i Data Factory version 2 i regionerna USA, östra, USA östra 2 och Europa, västra. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.

## <a name="create-a-self-hosted-ir"></a>Skapa IR med egen värd

I det här avsnittet kan du skapa en Integration Runtime med egen värd och koppla den till en lokal nod (dator).

1. Skapa en variabel för namnet på din Integration Runtime. 

    ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
    ```
1. Skapa Integration Runtime med egen värd. Använd ett unikt namn om det finns en annan Integration Runtime med samma namn.

   ```powershell
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
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

## <a name="install-integration-runtime"></a>Installera Integration Runtime
1. [Ladda ner](https://www.microsoft.com/download/details.aspx?id=39717) Integration Runtime med egen värd på en lokal Windows-dator och kör installationen. 
2. På **välkomstskärmen till installationsguiden för Microsoft Integration Runtime** klickar du på **Nästa**.  
3. På sidan med **licensavtalet** godkänner du villkoren och klickar på **Nästa**. 
4. På sidan **Målmapp** klickar du på **Nästa**. 
5. På sidan **Klar att installera Microsoft Integration Runtime** klickar du på **Installera**. 
6. Om du ser ett varningsmeddelande om att datorn håller på att konfigureras för att övergå i strömsparläge eller viloläge när den inte används, klickar du på **OK**. 
7. På sidan för att **installationsguiden för Microsoft Integration Runtime har slutförts** klickar du på **Slutför**.
8. Klistra in den nyckel som du sparade i föregående avsnitt på sidan **Registrera Integration Runtime (lokal)** och klicka på **Registrera**. 

   ![Registrera Integration Runtime](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)
2. När integration runtime med egen värd har registrerats ser du följande meddelande:

   ![Registered successfully (Registrerat)](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

3. På sidan **Ny nod för Integration Runtime (lokal)** klickar du på **Nästa**. 

    ![Sidan Ny nod för Integration Runtime](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)
4. På sidan **Kommunikationskanal för intranät** klickar du på **Hoppa över**. Du kan välja ett TLS/SSL-certifikat för att skydda kommunikationen mellan noderna i en miljö med flera noder för Integration Runtime. 

    ![Sidan Kommunikationskanal för intranät](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)
5. På sidan **Registrera Integration Runtime (lokal)** klickar du på **Starta Konfigurationshanteraren**. 
6. Du ser följande sida när noden är ansluten till molntjänsten:

   ![Node is connected (Noden är ansluten)](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>Skapa länkade tjänster

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Skapa en länkad Azure Storage-tjänst (destination/mottagare)

1. Skapa en JSON-fil med namnet **AzureStorageLinkedService.json** i mappen **C:\ADFv2Tutorial** med följande innehåll: skapa mappen ADFv2Tutorial om den inte redan finns.  

    > [!IMPORTANT]
    > Ersätt &lt;accountName&gt; och &lt;accountKey&gt; med namnet och nyckeln för ditt Azure Storage-konto innan du sparar filen.

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

1. Skapa en JSON-fil med namnet **SqlServerLinkedService.json** i mappen **C:\ADFv2Tutorial** med följande innehåll: Ersätt **&lt;servername>**, **&lt;databasename>**, **&lt;username>**, **&lt;servername>** och **&lt;password>** med dina SQL Server-värden innan du sparar filen. 

    > [!IMPORTANT]
    > Ersätt **&lt;integration** **runtime** **name>** med namnet på din Integration Runtime.

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
2. För att kryptera känsliga data från JSON-nyttolasten på en lokal IR med egen värd kör du **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** och skickar vidare JSON-nyttolasten ovan. Den här krypteringen ser till att autentiseringsuppgifterna krypteras med Data Protection Application Programming Interface (DPAPI). Krypterade autentiseringsuppgifter lagras lokalt på en IR-nod med egen värd (lokal dator). Nyttolasten i utdata kan omdirigeras till en annan JSON-fil (i det här fallet ”encryptedLinkedService.json”) som innehåller krypterade autentiseringsuppgifter.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Kör följande kommando med hjälp av JSON-filen från föregående steg för att skapa **SqlServerLinkedService**:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Skapa datauppsättningar
I det här steget kan du skapa indata och utdata datauppsättningar som representerar inkommande och utgående data för kopieringen (lokal SQL Server-databas = > Azure Blob Storage).

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

    Här är utdata från exempelkörningen:

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 3,
      "throughput": 0.01171875,
      "errors": [],
      "effectiveIntegrationRuntime": "MyIntegrationRuntime",
      "billedDuration": 3
    }
    ```
## <a name="verify-the-output"></a>Verifiera utdata
Pipelinen skapar automatiskt utdatamappen med namnet `fromonprem` i blobbehållaren `adftutorial`. Bekräfta att filen **dbo.emp.txt** finns i utdatamappen. Använd [lagringsutforskaren i Azure Storage](https://azure.microsoft.com/features/storage-explorer/) för att kontrollera att utdatafilen skapas. 

## <a name="next-steps"></a>Nästa steg
Pipeline i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa Integration Runtime med egen värd.
> * Skapa länkade tjänster för SQL Server och Azure Storage. 
> * Skapa datauppsättningar för SQL Server och Azure Blob.
> * Skapa en pipeline med en kopieringsaktivitet för att flytta data.
> * Starta en pipelinekörning.
> * Övervaka pipelinekörningen.

Se [Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) för att få en lista över datalager som stöds av Azure Data Factory.

Fortsätt till följande självstudie för att lära dig att masskopiera data från en källa till ett mål:

> [!div class="nextstepaction"]
>[Masskopiera data i molnet](tutorial-bulk-copy.md)
