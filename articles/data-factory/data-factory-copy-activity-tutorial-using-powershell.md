---
title: "Självstudiekurs: Skapa en pipeline med en kopieringsaktivitet med hjälp av Azure PowerShell | Microsoft Docs"
description: "I den här självstudiekursen ska du skapa en Azure Data Factory-pipeline med en kopieringsaktivitet genom att använda Azure PowerShell."
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
ms.date: 09/16/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: a4121f8857fa9eaeb1cf1bca70e29666f6a04f63
ms.openlocfilehash: 457a5c758923a0741ab0baeebd2f22c43930f71d


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-azure-powershell"></a>Självstudie: Skapa en pipeline med en kopieringsaktivitet med hjälp av Azure PowerShell
> [!div class="op_single_selector"]
> * [Översikt och förutsättningar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Guiden Kopiera](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-mall](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET-API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

I den här självstudiekursen ska du skapa och övervaka en Azure-datafabrik med hjälp av Azure PowerShell-cmdlets. Pipelinen i datafabriken som du skapar i den här självstudien använder en kopieringsaktivitet till att kopiera data från en Azure-blobb till en Azure SQL-databas.

Kopieringsaktiviteten utför dataflyttningen i Azure Data Factory. Aktiviteten drivs av en globalt tillgänglig tjänst som kan kopiera data mellan olika datalager på ett säkert, tillförlitligt och skalbart sätt. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten.   

> [!IMPORTANT]
> Läs igenom artikeln [Självstudier – översikt](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) och slutför de **nödvändiga** stegen innan du fortsätter med självstudierna.
> 
> Den här artikeln beskriver inte alla Data Factory-cmdletar. Se [Cmdlet-referens för Data Factory](https://msdn.microsoft.com/library/dn820234.aspx) för omfattande dokumentation om Data Factory-cmdletar.
> 
> 

## <a name="prerequisites"></a>Krav
Förutom de förutsättningar som anges i översiktsavsnittet för självstudierna, måste du installera **Azure PowerShell**. Följ instruktionerna i artikeln [Så här installerar och konfigurerar du Azure PowerShell](../powershell-install-configure.md) för att installera Azure PowerShell på datorn.

## <a name="in-this-tutorial"></a>I den här självstudien
Följande tabell innehåller de steg som du utför i självstudiekursen, med tillhörande beskrivningar. 

| Steg | Beskrivning |
| --- | --- |
| [Skapa en Azure Data Factory](#create-data-factory) |I det här steget ska du skapa en Azure-datafabrik med namnet **ADFTutorialDataFactoryPSH**. |
| [Skapa länkade tjänster](#create-linked-services) |I det här steget ska du skapa två länkade tjänster: **StorageLinkedService** och **AzureSqlLinkedService**. StorageLinkedService länkar en Azure-lagring och AzureSqlLinkedService länkar en Azure SQL-databas till ADFTutorialDataFactoryPSH. |
| [Skapa datauppsättningar för indata och utdata ](#create-datasets) |I det här steget ska du definiera två datauppsättningar (**EmpTableFromBlob** och **EmpSQLTable**). Dessa data används som in- och utdatatabeller för **kopieringsaktiviteten** i ADFTutorialPipeline som du skapar i nästa steg. |
| [Skapa och köra en pipeline](#create-pipeline) |I det här steget ska du skapa en pipeline med namnet **ADFTutorialPipeline** i datafabriken: **ADFTutorialDataFactoryPSH**. Pipelinen ska ha en **kopieringsaktivitet** som kopierar data från en Azure-blobb till utdata i en Azure-databastabell. |
| [Övervaka datauppsättningar och pipeline](#monitor-pipeline) |I det här steget ska du övervaka datauppsättningarna och pipelinen med hjälp av Azure PowerShell. |

## <a name="create-data-factory"></a>Skapa en datafabrik
I det här steget använder du Azure PowerShell till att skapa en Azure-datafabrik med namnet **ADFTutorialDataFactoryPSH**.

1. Starta **PowerShell**. Låt Azure PowerShell vara öppet tills du är klar med självstudien. Om du stänger och öppnar det igen måste du köra kommandona en gång till.
   
   1. Kör följande kommando och ange det användarnamn och lösenord som du använder för att logga in på Azure-portalen:
      
           Login-AzureRmAccount   
   2. Kör följande kommando för att visa alla prenumerationer för det här kontot:
      
           Get-AzureRmSubscription 
   3. Kör följande kommando för att välja den prenumeration som du vill arbeta med. Ersätt **&lt;NameOfAzureSubscription**&gt; med namnet på din Azure-prenumeration:
      
           Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
2. Skapa en Azure-resursgrupp med namnet: **ADFTutorialResourceGroup** genom att köra följande kommando:
   
        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
   
    Vissa av stegen i den här självstudien förutsätter att du använder resursgruppen med namnet **ADFTutorialResourceGroup**. Om du använder en annan resursgrupp måste du använda den i stället för ADFTutorialResourceGroup i den här självstudiekursen. 
3. Kör cmdleten **New-AzureRmDataFactory** för att skapa en datafabrik med namnet: **ADFTutorialDataFactoryPSH**:  
   
        New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"

Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande fel ändrar du namnet (till exempel dittnamnADFTutorialDataFactoryPSH). Använd det här namnet i stället för ADFTutorialFactoryPSH när du utför stegen i självstudien. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.
  
        Data factory name “ADFTutorialDataFactoryPSH” is not available
* Om du vill skapa Data Factory-instanser måste du vara deltagare/administratör för Azure-prenumerationen
* Namnet på datafabriken kan komma att registreras som ett DNS-namn i framtiden och blir då synligt offentligt.
* Om du får felet: ”**Den här prenumerationen har inte registrerats för användning av namnområdet Microsoft.DataFactory**” gör du något av följande och försöker att publicera igen: 
  
  * I Azure PowerShell kör du följande kommando för att registrera Data Factory-providern: 
    
          Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
      Du kan köra följande kommando om du vill kontrollera att Data Factory-providern är registrerad: 
    
          Get-AzureRmResourceProvider
  * Logga in med Azure-prenumerationen i [Azure-portalen](https://portal.azure.com) och navigera till ett Data Factory-blad (eller) skapa en datafabrik i Azure-portalen. Med den här åtgärden registreras providern automatiskt.

## <a name="create-linked-services"></a>Skapa länkade tjänster
Länkade tjänster länkar datalager eller beräkningstjänster till en Azure-datafabrik. Ett datalager kan vara Azure Storage-lagring, Azure SQL Database eller en lokal SQL Server-databas som innehåller indata eller som lagrar utdata för en Data Factory-pipeline. En beräkningstjänst är en tjänst som bearbetar indata och genererar utdata. 

I det här steget ska du skapa två länkade tjänster: **StorageLinkedService** och **AzureSqlLinkedService**. Den länkade StorageLinkedService-tjänsten länkar ett Azure-lagringskonto, och AzureSqlLinkedService länkar en Azure SQL-databas till datafabriken: **ADFTutorialDataFactoryPSH**. Senare i den här självstudien ska du skapa en pipeline som kopierar data från en blobbehållare i StorageLinkedService till en SQL-tabell i AzureSqlLinkedService.

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Skapa en länkad tjänst för ett Azure-lagringskonto
1. Skapa en JSON-fil med namnet **StorageLinkedService.json** i mappen **C:\ADFGetStartedPSH** med följande innehåll. Skapa mappen ADFGetStartedPSH om den inte redan finns.
   
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
2. Växla till mappen **ADFGetStartedPSH** i **Azure PowerShell**. 
3. Du kan använda cmdleten **New-AzureRmDataFactoryLinkedService** till att skapa en länkad tjänst. Med den här cmdleten och andra Data Factory-cmdlets som du använder i den här självstudien måste du ange värden för parametrarna **ResourceGroupName** och **DataFactoryName**. Du kan också använda **Get-AzureRmDataFactory** till att hämta ett DataFactory-objekt och skicka objektet utan att ange ResourceGroupName och DataFactoryName varje gång du kör en cmdlet. Kör följande kommando för att tilldela utdatan från cmdleten **Get-AzureRmDataFactory** till en variabel: **$df**: 
   
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

   Kontrollera att inställningen **Tillåt åtkomst till Azure-tjänster** är aktiverad för Azure SQL-servern. Gör så här för att kontrollera och aktivera den:
   
   1. Klicka på hubben **BLÄDDRA** till vänster och klicka på **SQL-servrar**.
   2. Välj server och klicka på **INSTÄLLNINGAR** på bladet SQL SERVER.
   3. På bladet **INSTÄLLNINGAR** klickar du på **Brandvägg**.
   4. På bladet **Brandväggsinställningar** klickar du på **På** för **Tillåt åtkomst till Azure-tjänster**.
   5. Klicka på hubben **AKTIV** till vänster om du vill växla till det **Data Factory**-blad du var på.

## <a name="create-datasets"></a>Skapa datauppsättningar
I föregående steg skapade du de länkade tjänsterna **StorageLinkedService** och **AzureSqlLinkedService** för att länka ett Azure-lagringskonto och en Azure SQL-databas till datafabriken: **ADFTutorialDataFactoryPSH**. I det här steget skapar du datauppsättningar som representerar indata och utdata för kopieringsaktiviteten i pipelinen som du skapar i nästa steg. 

En tabell är en rektangulär datauppsättning och är den enda typen av datauppsättning som stöds för tillfället. Indatatabellen i den här självstudiekursen refererar till en blobbehållare i Azure-lagring som StorageLinkedService pekar på. Utdatatabellen refererar till en SQL-tabell i Azure SQL-databasen som AzureSqlLinkedService pekar på.  

### <a name="prepare-azure-blob-storage-and-azure-sql-database-for-the-tutorial"></a>Förbereda Azure blobblagring och Azure SQL Database för självstudien
Hoppa över det här steget om du har gått igenom självstudiekursen från [Kopiera data från Blob Storage till SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) artikel. 

Förbered Azure-blobblagringen och Azure SQL-databasen för den här självstudiekursen genom att utföra följande steg. 

* Skapa en blobbehållare med namnet **adftutorial** i Azure blobb-lagringen som **StorageLinkedService** pekar på. 
* Skapa och ladda upp en textfil, **emp.txt**, som en blobb till behållaren **adftutorial**. 
* Skapa en tabell med namnet **emp** i Azure SQL Database i Azure SQL-databasen som **AzureSqlLinkedService** pekar på.

1. Starta Anteckningar, klistra in följande text och spara det som **emp.txt** i mappen **C:\ADFGetStartedPSH** på hårddisken. 
   
        John, Doe
        Jane, Doe
2. Använd verktyg som t.ex. [Azure Lagringsutforskaren](https://azurestorageexplorer.codeplex.com/) till att skapa behållaren **adftutorial** och för att ladda upp filen **emp.txt** till behållaren.
   
    ![Azure Lagringsutforskaren](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
3. Använd följande SQL-skript för att skapa tabellen **emp** i din Azure SQL Database.  

        CREATE TABLE dbo.emp 
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

    Om SQL Server 2014 är installerat på datorn följer du anvisningarna i artikeln [Steg 2: Ansluta till SQL Database i Hantera Azure SQL Database med SQL Server Management Studio](../sql-database/sql-database-manage-azure-ssms.md) för att ansluta till Azure SQL-servern och köra SQL-skriptet.

    Om klienten inte har åtkomst till Azure SQL-servern måste du konfigurera brandväggen för din Azure SQL-server och tillåta åtkomst från din dator (IP-adress). Anvisningar för hur du konfigurerar brandväggen för Azure SQL-servern finns i [den här artikeln](../sql-database/sql-database-configure-firewall-settings.md).

### <a name="create-input-dataset"></a>Skapa indatauppsättning
En tabell är en rektangulär datauppsättning med ett schema. I det här steget ska du skapa en tabell med namnet **EmpBlobTable** som pekar på en blobbehållare i Azure Storage som representeras av den länkade tjänsten **StorageLinkedService**. Den här blobbehållaren (**adftutorial**) innehåller indata i filen: **emp.txt**. 

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
   
   * datauppsättningens **typ** anges till **AzureBlob**.
   * **linkedServiceName** anges till **StorageLinkedService**. 
   * **folderPath** anges till behållaren **adftutorial**. 
   * **fileName** anges till **emp.txt**. Eftersom du inte anger namnet på blobben, anses data från alla blobbar i behållaren vara indata.  
   * formatet **typ** anges till **TextFormat**
   * Det finns två fält i textfilen – **FirstName** och **LastName** – som avgränsas med ett kommatecken (**columnDelimiter**)    
   * **availability** är inställt på **hourly** (**frequency** är inställt på **hour** och **interval** är inställt på **1**). Det betyder att Data Factory söker efter indata varje timme i rotmappen för blobbehållaren (**adftutorial**).
   
   Om du inte anger något **fileName** för en **indata****tabell**, anses alla filer/blobbar från indatamappen (**folderPath**) vara indata. Om du anger ett fileName i JSON, anses endast den angivna filen/blobben vara indata. 
   
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
   
   Se [Referens för JSON-skript](data-factory-data-movement-activities.md) för information om JSON-egenskaper.
2. Kör följande kommando för att skapa Data Factory-datauppsättningen.
   
```  
New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json
```

### <a name="create-output-dataset"></a>Skapa datauppsättning för utdata
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
   
   * datauppsättningens **typ** anges till **AzureSqlTable**.
   * **linkedServiceName** anges till **AzureSqlLinkedService**.
   * **tablename** anges till **emp**.
   * Det finns tre kolumner – **ID**, **FirstName** och **LastName** – i emp-tabellen i databasen. ID är en identitetskolumn, så du anger bara **FirstName** och **LastName** här.
   * **Tillgängligheten** anges till **varje timme** (**frekvens** inställd på **timme** och **intervall** inställd på **1**).  Data Factory-tjänsten genererar en utdatasektor varje timme i **emp**-tabellen i Azure SQL-databasen.
2. Kör följande kommando för att skapa Data Factory-datauppsättningen. 

```   
New-AzureRmDataFactoryDataset $df -File .\EmpSQLTable.json
```

## <a name="create-pipeline"></a>Skapa pipeline
I det här steget skapar du en pipeline med en **kopieringsaktivitet** som använder **EmpTableFromBlob** som indata och **EmpSQLTable** som utdata.

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
   * Indata för aktiviteten är inställd på **EmpTableFromBlob** och utdata för aktiviteten är inställd på **EmpSQLTable**.
   * I avsnittet för **transformering** har **BlobSource** angetts som källtyp och **SqlSink** har angetts som mottagartyp.
   
   Ersätt värdet i **start**egenskapen med den aktuella dagen och **slut**värdet med nästa dag. Både start- och slutdatum måste vara i [ISO-format](http://en.wikipedia.org/wiki/ISO_8601). Exempel: 2016-10-14T16:32:41Z. **Sluttiden** är valfri, men vi använder den i den här självstudiekursen. 
   
   Om du inte anger värdet för **slut**egenskapen, beräknas det som ”**start + 48 timmar**”. Om du vill köra pipelinen på obestämd tid, anger du **9/9/9999** som värde för **slut**egenskapen.
   
   I exemplet finns det 24 datasektorer eftersom varje datasektor skapas varje timme.
   
   Se [Referens för JSON-skript](data-factory-data-movement-activities.md) för information om JSON-egenskaper.
2. Kör följande kommando för att skapa Data Factory-tabellen. 

```   
New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
```

**Grattis!** Du har skapat en Azure-datafabrik, länkade tjänster, tabeller och en pipeline som du har schemalagt.

## <a name="monitor-pipeline"></a>Övervaka pipeline
I det här steget ska du använda Azure PowerShell för att övervaka vad som händer i en Azure-datafabrik.

1. Kör **Get-AzureRmDataFactory** och tilldela utdatan till en $df-variabel.

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

Se [Cmdlet-referens för Data Factory][cmdlet-reference] för omfattande dokumentation om Data Factory-cmdletar. 

## <a name="summary"></a>Sammanfattning
I den här självstudien har du skapat en Azure-datafabrik som kopierar data från en Azure-blobb till en Azure SQL-databas. Du använde PowerShell till att skapa datafabriken, länkade tjänster, datauppsättningar och en pipeline. Här är de avancerade steg som du utförde i självstudien:  

1. Du skapade en Azure **Data Factory**.
2. Du skapade **länkade tjänster**:
   1. En länkad **Azure-lagrings**tjänst som länkar Azure-lagringskontot som innehåller indata.     
   2. En länkad **Azure SQL**-tjänst som länkar din Azure SQL-databas som innehåller utdata. 
3. Du skapade **datauppsättningar** som beskriver indata och utdata för pipelines.
4. Du skapade en **pipeline** med en **kopieringsaktivitet** med **BlobSource** som källa och **SqlSink** som mottagare. 

## <a name="see-also"></a>Se även
| Avsnitt | Beskrivning |
|:--- |:--- |
| [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) |Den här artikeln innehåller detaljerad information om kopieringsaktiviteten som du använde i självstudien. |
| [Schemaläggning och körning](data-factory-scheduling-and-execution.md) |I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. |
| [Pipelines](data-factory-create-pipelines.md) |Den här artikeln beskriver pipelines och aktiviteter i Azure Data Factory |
| [Datauppsättningar](data-factory-create-datasets.md) |I den här artikeln förklaras hur datauppsättningar fungerar i Azure Data Factory. |
| [Övervaka och hantera pipelines med övervakningsappen](data-factory-monitor-manage-app.md) |Den här artikeln beskriver hur du övervakar, hanterar och felsöker pipelines med övervaknings- och hanteringsappen. |

[use-custom-activities]: data-factory-use-custom-activities.md
[Felsöka]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md



<!--HONumber=Nov16_HO2-->


