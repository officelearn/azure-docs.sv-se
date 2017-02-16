---
title: "Självstudiekurs: Skapa en pipeline för att flytta data med hjälp av Azure PowerShell | Microsoft-dokument"
description: "I den här självstudiekursen kommer du att skapa en Azure Data Factory-pipeline med en kopieringsaktivitet genom att använda Azure PowerShell."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 71087349-9365-4e95-9847-170658216ed8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/02/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: fbf77e9848ce371fd8d02b83275eb553d950b0ff
ms.openlocfilehash: a95e65db804f1c6cc2927901216ee7a287a911ee


---
# <a name="tutorial-create-a-data-factory-pipeline-that-moves-data-by-using-azure-powershell"></a>Självstudiekurs: Skapa en Data Factory-pipeline som flyttar data med hjälp av Azure PowerShell
> [!div class="op_single_selector"]
> * [Översikt och förutsättningar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Guiden Kopiera](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-mall](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET-API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
>
>

I den här självstudiekursen kommer du att skapa och övervaka en instans av Azure Data Factory med hjälp av Azure PowerShell-cmdletar. Pipelinen i den datafabrik som du skapar i den här självstudien använder en kopieringsaktivitet för att kopiera data från en Azure-blob till en Azure SQL-databas.

Funktionen Kopiera aktivitet utför dataflyttningen i Data Factory. Aktiviteten drivs av en globalt tillgänglig tjänst som kan kopiera data mellan olika datalager på ett säkert, tillförlitligt och skalbart sätt. Mer information Kopiera aktivitet finns i [Aktiviteter för dataförflyttning](data-factory-data-movement-activities.md).   

> [!NOTE]
> Den här artikeln beskriver inte alla Data Factory-cmdletar. Se [Cmdlet-referens för Data Factory](/powershell/resourcemanager/azurerm.datafactories/v2.5.0/azurerm.datafactories) för omfattande dokumentation om dessa cmdletar.
>
> Datapipelinen i den här självstudien kopierar data från ett källdatalager till ett måldatalager. Det transformerar inte indata för att generera utdata. Om du vill se en självstudie som visar hur du omvandlar data med Azure Data Factory går du till [Tutorial: Build a pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Självstudie: Bygg en pipeline för att omvandla data med Hadoop-kluster).

## <a name="prerequisites"></a>Krav
- Gå igenom [Översikt och förutsättningar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för att få en översikt av självstudierna och slutför de **nödvändiga** stegen.
- Installera Azure PowerShell. Följ instruktionerna i [Så här installerar och konfigurerar du Azure PowerShell](../powershell-install-configure.md).

## <a name="in-this-tutorial"></a>I den här självstudien
I följande tabell visas de steg som du utför som en del av vägledningen.

| Steg | Beskrivning |
| --- | --- |
| [Skapa en Azure-datafabrik](#create-data-factory) |I det här steget ska du skapa en Azure-datafabrik med namnet **ADFTutorialDataFactoryPSH**. |
| [Skapa länkade tjänster](#create-linked-services) |I det här steget ska du skapa två länkade tjänster: **StorageLinkedService** och **AzureSqlLinkedService**. StorageLinkedService länkar en Azure Storage-tjänst och AzureSqlLinkedService länkar en Azure SQL-databas till ADFTutorialDataFactoryPSH. |
| [Skapa datauppsättningar för indata och utdata ](#create-datasets) |I det här steget definierar du två datauppsättningar (**EmpTableFromBlob** och **EmpSQLTable**). Dessa datauppsättningar används som in- och utdatatabeller för **kopieringsaktiviteten** i den ADFTutorialPipeline som du kommer att skapa i nästa steg. |
| [Skapa och köra en pipeline](#create-pipeline) |I det här steget skapar du en pipeline med namnet **ADFTutorialPipeline** i datafabriken ADFTutorialDataFactoryPSH. Pipelinen använder Kopiera aktivitet för att kopierar data från en Azure-blob till utdata i en Azure-databastabell. |
| [Övervaka datauppsättningar och pipeline](#monitor-pipeline) |I det här steget ska du övervaka datauppsättningarna och pipelinen med hjälp av Azure PowerShell. |

## <a name="create-a-data-factory"></a>Skapa en datafabrik
I det här steget använder du Azure PowerShell för att skapa en Azure-datafabrik med namnet **ADFTutorialDataFactoryPSH**.

1. Starta **PowerShell**. Låt Azure PowerShell vara öppet tills du är klar med självstudien. Om du stänger och öppnar det igen måste du köra kommandona en gång till.

   a. Kör följande kommando och ange det användarnamn och lösenord som du använder för att logga in i Azure Portal:

           Login-AzureRmAccount   
   b. Kör följande kommando för att visa alla prenumerationer för det här kontot:

           Get-AzureRmSubscription
   c. Kör följande kommando för att välja den prenumeration som du vill arbeta med. Ersätt **&lt;NameOfAzureSubscription**&gt; med namnet på din Azure-prenumeration:

           Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
2. Skapa en Azure-resursgrupp med namnet **ADFTutorialResourceGroup** genom att köra följande kommando:

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Vissa av stegen i den här självstudien förutsätter att du använder resursgruppen med namnet **ADFTutorialResourceGroup**. Om du använder en annan resursgrupp måste du använda den i stället för ADFTutorialResourceGroup i den här självstudiekursen.
3. Kör cmdleten **New-AzureRmDataFactory** och skapa en datafabrik med namnet: **ADFTutorialDataFactoryPSH**:  

        New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"

Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande fel ändrar du namnet (till exempel dittnamnADFTutorialDataFactoryPSH). Använd det här namnet i stället för ADFTutorialFactoryPSH när du utför stegen i självstudien. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för information om Data Factory-artefakter.

        Data factory name “ADFTutorialDataFactoryPSH” is not available
* Om du vill skapa Data Factory-instanser måste du vara deltagare/administratör för Azure-prenumerationen.
* Namnet på datafabriken kan registreras som ett DNS-namn i framtiden och blir då synligt offentligt.
* Följande fel kan visas: ”**Prenumerationen har inte registrerats för användning av namnområdet Microsoft.DataFactory.**” Gör något av följande och försök publicera igen:

  * I Azure PowerShell kör du följande kommando för att registrera Data Factory-providern:

          Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory

      Du kan köra följande kommando om du vill kontrollera att Data Factory-providern är registrerad:

          Get-AzureRmResourceProvider
  * Logga in i [Azure Portal](https://portal.azure.com) via Azure-prenumerationen. Gå till ett Data Factory-blad, eller skapa en datafabrik i Azure Portal. Med den här åtgärden registreras providern automatiskt.

## <a name="create-linked-services"></a>Skapa länkade tjänster
Länkade tjänster länkar datalager eller beräkningstjänster till en Azure-datafabrik. Ett datalager kan vara en Azure Storage-tjänst, Azure SQL Database-databas eller en lokal SQL Server-databas som innehåller indata eller som lagrar utdata för en Data Factory-pipeline. En beräkningstjänst är en tjänst som bearbetar indata och genererar utdata.

I det här steget ska du skapa två länkade tjänster: **StorageLinkedService** och **AzureSqlLinkedService**. StorageLinkedService länkar ett Azure-lagringskonto, och AzureSqlLinkedService länkar en Azure SQL-databas till datafabriken: **ADFTutorialDataFactoryPSH**. Senare i den här självstudien ska du skapa en pipeline som kopierar data från en blobbehållare i StorageLinkedService till en SQL-tabell i AzureSqlLinkedService.

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Skapa en länkad tjänst för ett Azure-lagringskonto
1. Skapa en JSON-fil med namnet **StorageLinkedService.json** i mappen **C:\ADFGetStartedPSH** med följande innehåll. (Skapa mappen ADFGetStartedPSH om den inte redan finns.)

         {
               "name": "StorageLinkedService",
               "properties": {
                 "type": "AzureStorage",
                 "typeProperties": {
                       "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                 }
               }
         }

   Ersätt **accountname** och **accountkey** med namnet och nyckeln för ditt Azure-lagringskonto.
2. I **Azure PowerShell** växlar du till appen **ADFGetStartedPSH**.
3. Du kan använda cmdleten **New-AzureRmDataFactoryLinkedService** till att skapa en länkad tjänst. Med den här cmdleten och andra Data Factory-cmdletar som du använder i den här självstudien måste du ange värden för parametrarna **ResourceGroupName** och **DataFactoryName**. Du kan också använda **Get-AzureRmDataFactory** för att hämta ett DataFactory-objekt och skicka objektet utan att ange ResourceGroupName och DataFactoryName varje gång du kör en cmdlet. Kör följande kommando för att tilldela utdatan från cmdleten **Get-AzureRmDataFactory** till en variabel: **$df**:

    ```   
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
    ```

4. Kör nu cmdleten **New-AzureRmDataFactoryLinkedService** för att skapa den länkade tjänsten: **StorageLinkedService**.

    ```
    New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json
    ```

    Om du inte hade kört cmdleten **Get-AzureRmDataFactory** och tilldelat utdatan till **$df**-variabeln, hade du behövt ange värden för parametrarna ResourceGroupName och DataFactoryName på följande sätt.   

    ```
    New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json
    ```

Om du stänger Azure PowerShell mitt i självstudiekursen måste du köra cmdleten Get-AzureRmDataFactory nästa gång du startar Azure PowerShell för att slutföra självstudien.

### <a name="create-a-linked-service-for-an-azure-sql-database"></a>Skapa en länkad tjänst för en Azure SQL Database
1. Skapa en JSON-fil med namnet AzureSqlLinkedService.json med följande innehåll:

         {
             "name": "AzureSqlLinkedService",
             "properties": {
                 "type": "AzureSqlDatabase",
                 "typeProperties": {
                       "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=<user>@<server>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                 }
               }
         }

   Ersätt **servername**, **databasename**, **username@servername** och **password** med namnen för Azure SQL-servern, databasen, användarkontot och lösenordet.
2. Kör följande kommando för att skapa en länkad tjänst:

    ```
    New-AzureRmDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json
    ```

   Bekräfta att inställningen **Tillåt åtkomst till Azure-tjänster** är aktiverad för SQL-databasservern. Gör så här för att kontrollera och aktivera den:

   1. Klicka på den hubben **BLÄDDRA** till vänster och klicka på **SQL-servrar**.
   2. Välj server och klicka på **INSTÄLLNINGAR** på bladet **SQL SERVER**.
   3. På bladet **INSTÄLLNINGAR** klickar du på **Brandvägg**.
   4. På bladet **Brandväggsinställningar** klickar du på **På** för **Tillåt åtkomst till Azure-tjänster**.
   5. Klicka på hubben **AKTIV** till vänster om du vill växla till det **Data Factory**-blad som du hade öppet.

## <a name="create-datasets"></a>Skapa datauppsättningar
I det föregående steget skapade du tjänster för att länka ett Azure-lagringskonto och en Azure SQL-databas till datafabriken. I det här steget skapar du datauppsättningar som representerar in- och utdata för kopieringsaktiviteten i pipelinen som du skapar i nästa steg.

En tabell är en rektangulär datauppsättning. Detta är för närvarande är den enda typ av datauppsättning som stöds. Indatatabellen i den här självstudiekursen refererar till en blobbehållare i Azure Storage. Utdatatabellen refererar till en SQL-tabell i Azure SQL-databasen.  

### <a name="prepare-azure-blob-storage-and-azure-sql-database-for-the-tutorial"></a>Förbereda Azure Blob Storage och Azure SQL Database för självstudien
Hoppa över det här steget om du har gått igenom självstudiekursen från [Kopiera data från Blob Storage till SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Förbered Blob Storage och SQL Database för den här självstudiekursen genom att utföra följande steg.

1. Skapa en blobbehållare med namnet **adftutorial** på den bloblagringsplats som **StorageLinkedService** pekar på.
2. Skapa och skicka en textfil med namnet **emp.txt** som en blob till **adftutorial**-behållaren.
3. Skapa en tabell med namnet **emp**i den SQL-databas som **AzureSqlLinkedService** pekar på.

4. Starta Anteckningar, klistra in följande text och spara det som **emp.txt** i mappen **C:\ADFGetStartedPSH** på hårddisken.

        John, Doe
        Jane, Doe
5. Använd verktyg som [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) för att skapa behållaren **adftutorial** och för att överföra filen **emp.txt** till behållaren.

    ![Azure Lagringsutforskaren](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
6. Använd följande SQL-skript för att skapa tabellen **emp** i din SQL-databas.  

        CREATE TABLE dbo.emp
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

    Om SQL Server 2014 är installerat på datorn följer du anvisningarna i artikeln [Step 2: Connect to SQL Database of the Managing Azure SQL Database using SQL Server Management Studio (Steg 2: Ansluta till SQL Database i Hantera Azure SQL Database med SQL Server Management Studio)](../sql-database/sql-database-manage-azure-ssms.md) för att ansluta till SQL-databasservern och köra SQL-skriptet.

    Om klienten inte har åtkomst till SQL-databasservern måste du konfigurera brandväggen för din SQL-databasserver och tillåta åtkomst från din dator (IP-adress). Stegen beskrivs i [den här artikeln](../sql-database/sql-database-configure-firewall-settings.md).

### <a name="create-an-input-dataset"></a>Skapa en indatauppsättning
En tabell är en rektangulär datauppsättning med ett schema. I det här steget skapar du en tabell med namnet **EmpBlobTable**. Den här tabellen pekar på en blobbehållare i Azure Storage som representeras av den länkade tjänsten **StorageLinkedService**. Den här blobbehållaren (**adftutorial**) innehåller indata i filen: **emp.txt**.

1. Skapa en JSON-fil med namnet **EmpBlobTable.json** i mappen **C:\ADFGetStartedPSH** med följande innehåll:

         {
           "name": "EmpTableFromBlob",
           "properties": {
             "structure": [
               {
                 "name": "FirstName",
                 "type": "String"
               },
               {
                 "name": "LastName",
                 "type": "String"
               }
             ],
             "type": "AzureBlob",
             "linkedServiceName": "StorageLinkedService",
             "typeProperties": {
               "fileName": "emp.txt",
               "folderPath": "adftutorial/",
               "format": {
                 "type": "TextFormat",
                 "columnDelimiter": ","
               }
             },
             "external": true,
             "availability": {
               "frequency": "Hour",
               "interval": 1
             }
           }
         }

   Observera följande punkter:

   * Datauppsättningen **type** anges till **AzureBlob**.
   * **linkedServiceName** anges till **StorageLinkedService**.
   * **folderPath** anges till behållaren **adftutorial**.
   * **fileName** anges till **emp.txt**. Eftersom du inte anger namnet på bloben anses data från alla blobar i behållaren vara indata.  
   * Formatet **type** har angetts till **TextFormat**.
   * Det finns två fält i textfilen: **FirstName** och **LastName**, som avgränsas med ett kommatecken (**columnDelimiter**).    
   * **availability** är inställt på **hourly** (**frequency** är inställt på **hour** och **interval** är inställt på **1**). Det betyder att Data Factory söker efter indata varje timme i rotmappen för blobbehållaren (**adftutorial**).

   Om du inte anger något **fileName** för en **indatatabell**, anses alla filer och blobar från indatamappen (**folderPath**) vara indata. Om du anger ett fileName i JSON betraktas endast den angivna filen/bloben som indata.

   Om du inte anger något **fileName** för en **utdatatabell**, genereras filerna i **folderPath** och namnges i följande format: Data.<Guid\>.txt (exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Ange **folderPath** och **fileName** dynamiskt utifrån **SliceStart**-tiden med hjälp av **partitionedBy**-egenskapen. I följande exempel använder folderPath Year, Month och Day från SliceStart (starttiden för den sektor som bearbetas) och fileName använder Hour från SliceStart. Om exempelvis en sektor produceras 2016-10-20T08:00:00, anges folderName till wikidatagateway/wikisampledataout/2016/10/20 och fileName anges till 08.csv.

         "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
         "fileName": "{Hour}.csv",
         "partitionedBy":
         [
             { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
             { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
             { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
             { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
         ],

   Mer information om JSON-egenskaperna finns i [JSON-skriptreferensen](data-factory-data-movement-activities.md).
2. Kör följande kommando för att skapa Data Factory-datauppsättningen.

    ```  
    New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json
    ```

### <a name="create-an-output-dataset"></a>Skapa en datauppsättning för utdata
I det här steget ska du skapa en utdatauppsättning med namnet **EmpSQLTable**. Den här datauppsättningen pekar på en SQL-tabell (**emp**) i Azure SQL-databasen som representeras av **AzureSqlLinkedService**. Pipelinen kopierar data från indatablobben till tabellen **emp**.

1. Skapa en JSON-fil med namnet **EmpSQLTable.json** i mappen **C:\ADFGetStartedPSH** med följande innehåll:

         {
           "name": "EmpSQLTable",
           "properties": {
             "structure": [
               {
                 "name": "FirstName",
                 "type": "String"
               },
               {
                 "name": "LastName",
                 "type": "String"
               }
             ],
             "type": "AzureSqlTable",
             "linkedServiceName": "AzureSqlLinkedService",
             "typeProperties": {
               "tableName": "emp"
             },
             "availability": {
               "frequency": "Hour",
               "interval": 1
             }
           }
         }

   Observera följande punkter:

   * Datauppsättningen **type** anges till **AzureSqlTable**.
   * **linkedServiceName** anges till **AzureSqlLinkedService**.
   * **tablename** anges till **emp**.
   * Det finns tre kolumner i emp-tabellen i databasen: **ID**, **FirstName** och **LastName**. ID är en identitetskolumn, så du anger bara **FirstName** och **LastName** här.
   * Parametern **availability** anges till **hourly** (**frequency** inställd på **hour** och **interval** anges till **1**). Data Factory-tjänsten genererar en utdatasektor varje timme i **emp**-tabellen i Azure SQL-databasen.
2. Skapa datafabriksdatauppsättningen genom att köra följande kommando.

    ```   
    New-AzureRmDataFactoryDataset $df -File .\EmpSQLTable.json
    ```

## <a name="create-a-pipeline"></a>Skapa en pipeline
I det här steget kan du skapa en pipeline med **Kopiera aktivitet**. Pipeline använder **EmpTableFromBlob** som indata, och **EmpSQLTable** som utdata.

1. Skapa en JSON-fil med namnet **ADFTutorialPipeline.json** i mappen **C:\ADFGetStartedPSH** med följande innehåll:

          {
           "name": "ADFTutorialPipeline",
           "properties": {
             "description": "Copy data from a blob to Azure SQL table",
             "activities": [
               {
                 "name": "CopyFromBlobToSQL",
                 "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
                 "type": "Copy",
                 "inputs": [
                   {
                     "name": "EmpTableFromBlob"
                   }
                 ],
                 "outputs": [
                   {
                     "name": "EmpSQLTable"
                   }
                 ],
                 "typeProperties": {
                   "source": {
                     "type": "BlobSource"
                   },
                   "sink": {
                     "type": "SqlSink"
                   }
                 },
                 "Policy": {
                   "concurrency": 1,
                   "executionPriorityOrder": "NewestFirst",
                   "style": "StartOfInterval",
                   "retry": 0,
                   "timeout": "01:00:00"
                 }
               }
             ],
             "start": "2016-08-09T00:00:00Z",
             "end": "2016-08-10T00:00:00Z",
             "isPaused": false
           }
         }

   Observera följande punkter:

   * I avsnittet Aktiviteter finns det bara en aktivitet vars **typ** anges till **Kopia**.
   * Indata för aktiviteten är inställt på **EmpTableFromBlob** och utdata för aktiviteten är inställt på **EmpSQLTable**.
   * I avsnittet **transformation** har **BlobSource** angetts som källtyp och **SqlSink** har angetts som mottagartyp.

   Ersätt värdet i egenskapen **start** med den aktuella dagen och värdet för egenskapen **end** med nästa dag. Både start- och slutdatum måste vara i [ISO-format](http://en.wikipedia.org/wiki/ISO_8601). Exempel: 2016-10-14T16:32:41Z. Den **end**-tid (sluttid) som används i den här handledningen, men det är valfritt.

   Om du inte anger värdet för **slut**egenskapen, beräknas det som ”**start + 48 timmar**”. Om du vill köra pipelinen på obestämd tid, anger du **9/9/9999** som värde för **slut**egenskapen.

   I exemplet finns det 24 datasektorer eftersom varje datasektor skapas varje timme.

   Mer information om JSON-egenskaper finns i [JSON-skriptreferens](data-factory-data-movement-activities.md).
2. Kör följande kommando för att skapa datafabrikstabellen.

    ```   
    New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
    ```

Grattis! Du har nu skapat en Azure-datafabrik, länkade tjänster, tabeller och en pipeline. Du har även schemalagt denna pipeline.

## <a name="monitor-the-pipeline"></a>Övervaka pipeline
I det här steget använder du Azure PowerShell till att övervaka vad som händer i en Azure Data Factory.

1. Kör **Get-AzureRmDataFactory** och tilldela en $df-variabel för utdata.

    ```  
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
    ```

2. Kör **Get-AzureRmDataFactorySlice** att få information om alla sektorer av **EmpSQLTable**, vilket är utdatatabellen för pipelinen.  

    ```   
    Get-AzureRmDataFactorySlice $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
    ```

   Ersätt år, månad och datum i parametern **StartDateTime** med aktuellt år, månad och datum. Den här inställningen måste matcha **Start**-värdet i pipelinens JSON.

   Du bör se 24 sektorer, en för varje timme från kl. 12:00 den aktuella dagen till 12:00 nästa dag.

   **Exempel på utdata:**

    ```   
     ResourceGroupName : ADFTutorialResourceGroup
     DataFactoryName   : ADFTutorialDataFactoryPSH
     TableName         : EmpSQLTable
     Start             : 8/9/2016 12:00:00 AM
     End               : 8/9/2016 1:00:00 AM
     RetryCount        : 0
     Status            : Waiting
     LatencyStatus     :
     LongRetryCount    : 0
    ```
3. Kör **Get-AzureRmDataFactoryRun** för att hämta information om aktiviteten som körs för en **viss** sektor. Ändra värdet för parametern **StartDateTime** så att det matchar **starttiden** för sektorn i utdata. Värdet för **StartDateTime** måste vara i [ISO-format](http://en.wikipedia.org/wiki/ISO_8601).

    ```  
    Get-AzureRmDataFactoryRun $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
    ```

   Du bör se utdata som liknar följande exempel:

    ```   
     Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
     ResourceGroupName   : ADFTutorialResourceGroup
     DataFactoryName     : ADFTutorialDataFactoryPSH
     TableName           : EmpSQLTable
     ProcessingStartTime : 8/9/2016 11:03:28 PM
     ProcessingEndTime   : 8/9/2016 11:04:36 PM
     PercentComplete     : 100
     DataSliceStart      : 8/9/2016 10:00:00 PM
     DataSliceEnd        : 8/9/2016 11:00:00 PM
     Status              : Succeeded
     Timestamp           : 8/9/2016 11:03:28 PM
     RetryAttempt        : 0
     Properties          : {}
     ErrorMessage        :
     ActivityName        : CopyFromBlobToSQL
     PipelineName        : ADFTutorialPipeline
     Type                : Copy
    ```

Omfattande dokumentation om Data Factory-cmdletar finns i [Cmdlet-referens för Data Factory][cmdlet-reference].

## <a name="summary"></a>Sammanfattning
I den här självstudien har du skapat en Azure-datafabrik som kopierar data från en Azure-blobb till en Azure SQL-databas. Du använde PowerShell till att skapa datafabriken, länkade tjänster, datauppsättningar och en pipeline. Här är de avancerade steg som du utförde i självstudien:  

1. Du skapade en Azure **Data Factory**.
2. Du skapade **länkade tjänster**:

   a. En länkad **Azure Storage**-tjänst som länkar Azure-lagringskontot som innehåller indata.     
   b. En länkad **Azure SQL**-tjänst som länkar den SQL-databas som innehåller utdata.
3. Du skapade **datauppsättningar** som beskriver indata och utdata för pipelines.
4. Du skapade en **pipeline** med **Kopiera aktivitet**, med **BlobSource** som källa och **SqlSink** som mottagare.

## <a name="see-also"></a>Se även
| Avsnitt | Beskrivning |
|:--- |:--- |
| [Cmdlet-referens för Data Factory](/powershell/resourcemanager/azurerm.datafactories/v2.5.0/azurerm.datafactories) | Det här avsnittet innehåller information om alla Data Factory-cmdlets |
| [Pipelines](data-factory-create-pipelines.md) |Den här artikeln beskriver pipelines och aktiviteter i Azure Data Factory. |
| [datauppsättningar](data-factory-create-datasets.md) |I den här artikeln förklaras hur datauppsättningar fungerar i Azure Data Factory. |
| [Schemaläggning och körning](data-factory-scheduling-and-execution.md) |I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. |

[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md



<!--HONumber=Feb17_HO1-->


