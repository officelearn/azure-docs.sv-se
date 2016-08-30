<properties 
    pageTitle="Självstudie: Skapa en pipeline med en kopieringsaktivitet med hjälp av Azure PowerShell" 
    description="I den här självstudien skapar du en Azure Data Factory-pipeline med en kopieringsaktivitet genom att använda Azure PowerShell." 
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
    ms.topic="get-started-article" 
    ms.date="05/16/2016" 
    ms.author="spelluru"/>

# Självstudie: Skapa en pipeline med en kopieringsaktivitet med hjälp av Azure PowerShell
> [AZURE.SELECTOR]
- [Självstudier – översikt](data-factory-get-started.md)
- [Använda Data Factory-redigeraren](data-factory-get-started-using-editor.md)
- [Använda Visual Studio](data-factory-get-started-using-vs.md)
- [Använda PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Använda guiden Kopiera](data-factory-copy-data-wizard-tutorial.md)

Självstudien [Kom igång med Azure Data Factory][adf-get-started] visar hur du skapar och övervakar en Azure-datafabrik med [Azure Portal][azure-portal]. I den här självstudien skapar du och övervakar en Azure-datafabrik med hjälp av Azure PowerShell-cmdletar. Pipelinen i datafabriken som du skapar i den här självstudien använder en kopieringsaktivitet till att kopiera data från en Azure-blobb till en Azure SQL-databas.

Kopieringsaktiviteten utför dataförflyttningen i Azure Data Factory och aktiviteten drivs av en globalt tillgänglig tjänst som kan kopiera data mellan olika datalager på ett säkert, tillförlitligt och skalbart sätt. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten.   

> [AZURE.IMPORTANT] Läs igenom artikeln [Självstudier – översikt](data-factory-get-started.md) och slutför nödvändiga steg innan du fortsätter med självstudien.
>   
> Den här artikeln beskriver inte alla Data Factory-cmdletar. Se [Cmdlet-referens för Data Factory](https://msdn.microsoft.com/library/dn820234.aspx) för omfattande dokumentation om Data Factory-cmdletar.
  

##Förutsättningar
Förutom de förutsättningar som anges i översiktsavsnittet för självstudien, måste du installera följande:

- **Azure PowerShell**. Följ instruktionerna i artikeln [Så här installerar och konfigurerar du Azure PowerShell](../powershell-install-configure.md) för att installera Azure PowerShell på datorn.

Om du använder Azure PowerShell **version < 1.0**, måste du använda cmdletar som dokumenteras [här][old-cmdlet-reference]. Du behöver också köra följande kommandon innan du använder Data Factory-cmdletarna: 

1. Starta Azure PowerShell och kör följande kommandon. Låt Azure PowerShell vara öppet tills du är klar med självstudien. Om du stänger och öppnar det igen måste du köra kommandona en gång till.
    1. Kör **Add-AzureAccount** och ange det användarnamn och lösenord som användes för att logga in på Azure Portal.
    2. Kör **Get-AzureSubscription** för att visa alla prenumerationer för det här kontot.
    3. Kör **Select-AzureSubscription** för att välja den prenumeration som du vill arbeta med. Den här prenumerationen ska vara samma som den du använde i Azure-portalen.
4. Växla till AzureResourceManager-läge eftersom Azure Data Factory-cmdletarna är tillgängliga i det här läget: **Switch-AzureMode AzureResourceManager**.
  

##I den här självstudien
I följande tabell visas de steg som du ska utföra som en del av självstudien, samt deras beskrivningar. 

Steg | Beskrivning
-----| -----------
[Skapa en Azure Data Factory](#create-data-factory) | I det här steget skapar du en Azure-datafabrik med namnet **ADFTutorialDataFactoryPSH**. 
[Skapa länkade tjänster](#create-linked-services) | I det här steget skapar du två länkade tjänster: **StorageLinkedService** och **AzureSqlLinkedService**. StorageLinkedService länkar en Azure-lagring och AzureSqlLinkedService länkar en Azure SQL-databas till ADFTutorialDataFactoryPSH.
[Skapa datauppsättningar för indata och utdata ](#create-datasets) | I det här steget definierar du två datauppsättningar (**EmpTableFromBlob** och **EmpSQLTable**) som används som indata- och utdatatabeller för den **kopieringsaktivitet** i ADFTutorialPipeline som du skapar i nästa steg.
[Skapa och köra en pipeline](#create-pipeline) | I det här steget skapar du en pipeline med namnet **ADFTutorialPipeline** i datafabriken: **ADFTutorialDataFactoryPSH**. . Pipelinen har en **kopieringsaktivitet** som kopierar data från en Azure-blobb till utdata i en Azure-databastabell.
[Övervaka datauppsättningar och pipeline](#monitor-pipeline) | I det här steget övervakar du datauppsättningarna och pipelinen med hjälp av Azure PowerShell.

## Skapa en datafabrik
I det här steget använder du Azure PowerShell till att skapa en Azure-datafabrik med namnet **ADFTutorialDataFactoryPSH**.

1. Starta Azure PowerShell och kör följande kommando. Låt Azure PowerShell vara öppet tills du är klar med självstudien. Om du stänger och öppnar det igen måste du köra kommandona en gång till.
    - Kör **Login-AzureRmAccount** och ange det användarnamn och lösenord som användes för att logga in på Azure Portal.  
    - Kör **Get-AzureSubscription** för att visa alla prenumerationer för det här kontot.
    - Kör **Select-AzureSubscription<Name of the subscription>** för att välja den prenumeration som du vill arbeta med. Den här prenumerationen ska vara samma som den du använde i Azure-portalen.
3. Skapa en Azure-resursgrupp med namnet: **ADFTutorialResourceGroup** genom att köra följande kommando.
   
        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Vissa av stegen i den här självstudien förutsätter att du använder resursgruppen med namnet **ADFTutorialResourceGroup**. Om du använder en annan resursgrupp måste du använda den i stället för ADFTutorialResourceGroup i självstudien. 
4. Kör cmdleten **New-AzureRmDataFactory** för att skapa en datafabrik med namnet: **ADFTutorialDataFactoryPSH**.  

        New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"

    
Observera följande:
 
- Namnet på Azure Data Factory måste vara globalt unikt. Om du får felmeddelandet: **Datafabriksnamnet ”ADFTutorialDataFactoryPSH” är inte tillgängligt** ändrar du namnet (exempelvis till yournameADFTutorialDataFactoryPSH). Använd det här namnet i stället för ADFTutorialFactoryPSH när du utför stegen i självstudien. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.
- Om du vill skapa Data Factory-instanser måste du vara deltagare/administratör för Azure-prenumerationen
- Namnet på datafabriken kan komma att registreras som ett DNS-namn i framtiden och blir då synligt offentligt.
- Om du får felet: ”**Den här prenumerationen har inte registrerats för användning av namnområdet Microsoft.DataFactory**” gör du något av följande och försöker att publicera igen: 

    - I Azure PowerShell kör du följande kommando för att registrera Data Factory-providern. 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Du kan köra följande kommando för att kontrollera att Data Factory-providern är registrerad. 
    
            Get-AzureRmResourceProvider
    - Logga in med Azure-prenumerationen i [Azure-portalen](https://portal.azure.com) och navigera till ett Data Factory-blad (eller) skapa en datafabrik i Azure-portalen. Detta registrerar automatiskt providern åt dig.

## Skapa länkade tjänster
Länkade tjänster länkar datalager eller beräkningstjänster till en Azure-datafabrik. Ett datalager kan vara en Azure-lagring, en Azure SQL Database eller en lokal SQL Server-databas som innehåller indata eller lagrar utdata för en Data Factory-pipeline. En beräkningstjänst är den tjänst som bearbetar indata och producerar utdata. 

I det här steget skapar du två länkade tjänster: **StorageLinkedService** och **AzureSqlLinkedService**. Den länkade StorageLinkedService-tjänsten länkar ett Azure-lagringskonto, och AzureSqlLinkedService länkar en Azure SQL-databas till datafabriken: **ADFTutorialDataFactoryPSH**. Senare i den här självstudien skapar du en pipeline som kopierar data från en blobbehållare i StorageLinkedService till en SQL-tabell i AzureSqlLinkedService.

### Skapa en länkad tjänst för ett Azure-lagringskonto
1.  Skapa en JSON-fil med namnet **StorageLinkedService.json** i mappen **C:\ADFGetStartedPSH** med följande innehåll. Skapa mappen ADFGetStartedPSH om den inte redan finns.

            {
                "name": "StorageLinkedService",
                "properties": {
                    "type": "AzureStorage",
                    "typeProperties": {
                        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                    }
                }
            }

    Ersätt **accountname** med namnet på ditt lagringskonto och **accountkey** med nyckeln till Azure-lagringskontot.
2.  Växla till mappen **ADFGetStartedPSH** i **Azure PowerShell**. 
3.  Du kan använda cmdleten **New-AzureRmDataFactoryLinkedService** till att skapa en länkad tjänst. Denna cmdlet och andra Data Factory-cmdletar som du använder i den här självstudien kräver att du anger värden för parametrarna **ResourceGroupName** och **DataFactoryName**. Du kan också använda **Get-AzureRmDataFactory** till att hämta ett DataFactory-objekt och skicka objektet utan att ange ResourceGroupName och DataFactoryName varje gång du kör en cmdlet. Kör följande kommando för att tilldela utdatan från cmdleten **Get-AzureRmDataFactory** till en variabel: **$df**. 

        $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH

4.  Kör nu cmdleten **New-AzureRmDataFactoryLinkedService** för att skapa den länkade tjänsten: **StorageLinkedService**. 

        New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

    Om du inte hade kört cmdleten **Get-AzureRmDataFactory** och tilldelat utdatan till **$df**-variabeln, hade du behövt ange värden för parametrarna ResourceGroupName och DataFactoryName på följande sätt.   
        
        New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

    Om du stänger Azure PowerShell mitt i självstudien, måste du köra cmdleten Get-AzureRmDataFactory nästa gång du startar Azure PowerShell för att slutföra självstudien.

### Skapa en länkad tjänst för en Azure SQL Database
1.  Skapa en JSON-fil med namnet AzureSqlLinkedService.json med följande innehåll.

            {
                "name": "AzureSqlLinkedService",
                "properties": {
                    "type": "AzureSqlDatabase",
                    "typeProperties": {
                        "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=user@server;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                    }
                }
            }

    Ersätt **servername**, **databasename**, **username@servername** och **password** med namnen på din Azure SQL-server, databas, användarkonto och lösenord.

2.  Kör följande kommando för att skapa en länkad tjänst. 
    
        New-AzureRmDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

    Kontrollera att inställningen **Tillåt åtkomst till Azure-tjänster** är aktiverad för Azure SQL-servern. Gör så här för att kontrollera och aktivera:

    1. Klicka på hubben **BLÄDDRA** till vänster och klicka på **SQL-servrar**.
    2. Välj server och klicka på **INSTÄLLNINGAR** på bladet SQL SERVER.
    3. På bladet **INSTÄLLNINGAR** klickar du på **Brandvägg**.
    4. På bladet **Brandväggsinställningar** klickar du på **PÅ** för **Tillåt åtkomst till Azure-tjänster**.
    5. Klicka på hubben **AKTIV** till vänster om du vill växla till det **Data Factory**-blad du var på.
    

## Skapa datauppsättningar

I föregående steg skapade du de länkade tjänsterna **StorageLinkedService** och **AzureSqlLinkedService** för att länka ett Azure-lagringskonto och en Azure SQL-databas till datafabriken: **ADFTutorialDataFactoryPSH**. I det här steget ska du skapa datauppsättningar som visar indata och utdata för kopieringsaktiviteten i den pipeline som du skapar i nästa steg. 

En tabell är en rektangulär datauppsättning och är den enda typen av datauppsättning som stöds för tillfället. Indatatabellen i den här självstudien refererar till en blobbehållare i Azure-lagringen som StorageLinkedService pekar på och utdatatabellen refererar till en SQL-tabell i Azure SQL-databasen som AzureSqlLinkedService pekar på.  

### Förbereda Azure blobblagring och Azure SQL Database för självstudien
Hoppa över det här steget om du har gått igenom självstudien från artikeln [Kom igång med Azure Data Factory][adf-get-started]. 

Du måste utföra följande steg för att förbereda Azure-blobblagringen och Azure SQL-databasen för den här självstudien. 
 
* Skapa en blobbehållare med namnet **adftutorial** i Azure blobb-lagringen som **StorageLinkedService** pekar på. 
* Skapa och ladda upp en textfil, **emp.txt**, som en blobb till behållaren **adftutorial**. 
* Skapa en tabell med namnet **emp** i Azure SQL Database i Azure SQL-databasen som **AzureSqlLinkedService** pekar på.


1. Starta Anteckningar, klistra in följande text och spara det som **emp.txt** i mappen **C:\ADFGetStartedPSH** på hårddisken. 

        John, Doe
        Jane, Doe
                
2. Använd verktyg som t.ex. [Azure Lagringsutforskaren](https://azurestorageexplorer.codeplex.com/) till att skapa behållaren **adftutorial** och för att ladda upp filen **emp.txt** till behållaren.

    ![Azure Lagringsutforskaren][image-data-factory-get-started-storage-explorer]
3. Använd följande SQL-skript för att skapa tabellen **emp** i din Azure SQL Database.  


        CREATE TABLE dbo.emp 
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

    Om du har installerat SQL Server 2014 på datorn: följ anvisningarna i artikeln [Steg 2: Ansluta till SQL Database i Hantera Azure SQL Database med SQL Server Management Studio][sql-management-studio] för att ansluta till din Azure SQL-server och köra SQL-skriptet.

    Om du har Visual Studio 2013 installerat på datorn: I Azure Portal ([http://portal.azure.com](http://portal.sazure.com)) klickar du på hubben **BLÄDDRA** till vänster och sedan på **SQL-servrar**. Markera databasen och klicka på knappen **Öppna i Visual Studio** i verktygsfältet för att ansluta till din Azure SQL-server och köra skriptet. Om klienten inte har åtkomst till Azure SQL-servern behöver du konfigurera brandväggen för din Azure SQL-server och tillåta åtkomst från din dator (IP-adress). Se i artikeln ovan hur du konfigurerar brandväggen för Azure SQL-servern.
        
### Skapa indatauppsättning 
En tabell är en rektangulär datauppsättning med ett schema. I det här steget skapar du en tabell med namnet **EmpBlobTable** som pekar på en blobbehållare i Azure-lagringen som representeras av den länkade tjänsten **StorageLinkedService**. Den här blobbehållaren (**adftutorial**) innehåller indata i filen: **emp.txt**. 

1.  Skapa en JSON-fil med namnet **EmpBlobTable.json** i mappen **C:\ADFGetStartedPSH** med följande innehåll:

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
    
    Observera följande: 
    
    - datauppsättningens **typ** anges till **AzureBlob**.
    - **linkedServiceName** anges till **StorageLinkedService**. 
    - **folderPath** anges till behållaren **adftutorial**. 
    - **fileName** anges till **emp.txt**. Eftersom du inte anger namnet på blobben, anses data från alla blobbar i behållaren vara indata.  
    - formatet **typ** anges till **TextFormat**
    - Det finns två fält i textfilen – **FirstName** och **LastName** – som avgränsas med ett kommatecken (**columnDelimiter**) 
    - **Tillgängligheten** anges till **varje timme** (**frekvens** anges till **timme** och **intervall** är inställd på **1** ), vilket innebär att Data Factory-tjänsten letar efter indata en gång i timmen i rotmappen i blobbehållaren (**adftutorial**) som du har angett.

    Om du inte anger något **fileName** för en **indata****tabell**, anses alla filer/blobbar från indatamappen (**folderPath**) vara indata. Om du anger ett fileName i JSON, anses endast den angivna filen/blobben vara indata. 
 
    Om du inte anger något **fileName** för en **utdatatabell**, genereras filerna i **folderPath** och namnges i följande format: Data.<Guid\>.txt (exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

    Ange **folderPath** och **fileName** dynamiskt utifrån **SliceStart**-tiden med hjälp av **partitionedBy**-egenskapen. I följande exempel använder folderPath År, Månad och Dag från SliceStart (starttiden för den sektor som bearbetas) och fileName använder Timme från SliceStart. Om exempelvis en sektor produceras 2014-10-20T08:00:00, anges folderName till wikidatagateway/wikisampledataout/2014/10/20 och fileName anges till 08.csv. 

            "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": 
            [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
            ],

    Se [Referens för JSON-skript](http://go.microsoft.com/fwlink/?LinkId=516971) för information om JSON-egenskaper.

2.  Kör följande kommando för att skapa Data Factory-datauppsättningen.

        New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json

### Skapa datauppsättning för utdata
I den här delen av steget skapar du en utdatatabell med namnet **EmpSQLTable** som pekar på en SQL-tabell (**emp**) i Azure SQL-databasen, som representeras av den länkade tjänsten **AzureSqlLinkedService**. Pipelinen kopierar data från indatablobben till tabellen **emp**. 

1.  Skapa en JSON-fil med namnet **EmpSQLTable.json** i mappen **C:\ADFGetStartedPSH** med följande innehåll.
        
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

     Observera följande: 
    
    * datauppsättningens **typ** anges till **AzureSqlTable**.
    * **linkedServiceName** anges till **AzureSqlLinkedService**.
    * **tablename** anges till **emp**.
    * Det finns tre kolumner – **ID**, **FirstName** och **LastName** – i emp-tabellen i databasen, men ID är en identitetskolumn så du behöver bara ange **FirstName** och **LastName** här.
    * **Tillgängligheten** anges till **varje timme** (**frekvens** inställd på **timme** och **intervall** inställd på **1**).  Data Factory-tjänsten genererar en utdatasektor varje timme i **emp**-tabellen i Azure SQL-databasen.

2.  Kör följande kommando för att skapa Data Factory-datauppsättningen. 
    
        New-AzureRmDataFactoryDataset $df -File .\EmpSQLTable.json


## Skapa pipeline
I det här steget skapar du en pipeline med en **kopieringsaktivitet** som använder **EmpTableFromBlob** som indata och **EmpSQLTable** som utdata.

1.  Skapa en JSON-fil med namnet **ADFTutorialPipeline.json** i mappen **C:\ADFGetStartedPSH** med följande innehåll: 
    
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
                "start": "2015-12-09T00:00:00Z",
                "end": "2015-12-10T00:00:00Z",
                "isPaused": false
              }
            }

    Observera följande:

    - I avsnittet Aktiviteter finns det bara en aktivitet vars **typ** anges till **Kopia**.
    - Indata för aktiviteten är inställd på **EmpTableFromBlob** och utdata för aktiviteten är inställd på **EmpSQLTable**.
    - I avsnittet för **transformering** har **BlobSource** angetts som källtyp och **SqlSink** har angetts som mottagartyp.

    Ersätt värdet i **start**egenskapen med den aktuella dagen och **slut**värdet med nästa dag. Både start- och slutdatum måste vara i [ISO-format](http://en.wikipedia.org/wiki/ISO_8601). Exempel: 2014-10-14T16:32:41Z. **Slut**tid är valfritt, men vi kommer att använda det i den här självstudien. 
    
    Om du inte anger värdet för **slut**egenskapen, beräknas det som ”**start + 48 timmar**”. Om du vill köra pipelinen på obestämd tid, anger du **9/9/9999** som värde för **slut**egenskapen.
    
    I exemplet ovan finns det 24 datasektorer eftersom det skapas en datasektor i timmen.
    
    Se [Referens för JSON-skript](http://go.microsoft.com/fwlink/?LinkId=516971) för information om JSON-egenskaper.
2.  Kör följande kommando för att skapa Data Factory-tabellen. 
        
        New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**Grattis!** Du har skapat en Azure-datafabrik, länkade tjänster, tabeller och en pipeline som du har schemalagt.

## Övervaka pipeline
I det här steget ska du använda Azure PowerShell till att övervaka vad som händer i en Azure-datafabrik.

1.  Kör **Get-AzureRmDataFactory** och tilldela utdatan till en $df-variabel.

        $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
 
2.  Kör **Get-AzureRmDataFactorySlice** att få information om alla sektorer av **EmpSQLTable**, vilket är utdatatabellen för pipelinen.  

        Get-AzureRmDataFactorySlice $df -DatasetName EmpSQLTable -StartDateTime 2015-03-03T00:00:00

    Ersätt år, månad och datum i parametern **StartDateTime** med aktuellt år, månad och datum. Detta bör matcha värdet **Start** i pipelinens JSON. 

    Du bör se 24 sektorer, en för varje timme från kl. 12:00 den aktuella dagen till 12:00 nästa dag. 
    
    **Första sektorn:**

        ResourceGroupName : ADFTutorialResourceGroup
        DataFactoryName   : ADFTutorialDataFactoryPSH
        TableName         : EmpSQLTable
        Start             : 3/3/2015 12:00:00 AM
        End               : 3/3/2015 1:00:00 AM
        RetryCount        : 0
        Status            : Waiting
        LatencyStatus     :
        LongRetryCount    : 0

    **Sista sektorn:**

        ResourceGroupName : ADFTutorialResourceGroup
        DataFactoryName   : ADFTutorialDataFactoryPSH
        TableName         : EmpSQLTable
        Start             : 3/4/2015 11:00:00 PM
        End               : 3/4/2015 12:00:00 AM
        RetryCount        : 0
        Status            : Waiting
        LatencyStatus     : 
        LongRetryCount    : 0

3.  Kör **Get-AzureRmDataFactoryRun** för att hämta information om aktiviteten som körs för en **viss** sektor. Ändra värdet för parametern **StartDateTime** så att den matchar **start**tiden i sektorn från utdatan ovan. Värdet för **StartDateTime** måste vara i [ISO-format](http://en.wikipedia.org/wiki/ISO_8601). Exempel: 2014-03-03T22:00:00Z.

        Get-AzureRmDataFactoryRun $df -DatasetName EmpSQLTable -StartDateTime 2015-03-03T22:00:00

    Du bör se utdata som liknar följande:

        Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
        ResourceGroupName   : ADFTutorialResourceGroup
        DataFactoryName     : ADFTutorialDataFactoryPSH
        TableName           : EmpSQLTable
        ProcessingStartTime : 3/3/2015 11:03:28 PM
        ProcessingEndTime   : 3/3/2015 11:04:36 PM
        PercentComplete     : 100
        DataSliceStart      : 3/8/2015 10:00:00 PM
        DataSliceEnd        : 3/8/2015 11:00:00 PM
        Status              : Succeeded
        Timestamp           : 3/8/2015 11:03:28 PM
        RetryAttempt        : 0
        Properties          : {}
        ErrorMessage        :
        ActivityName        : CopyFromBlobToSQL
        PipelineName        : ADFTutorialPipeline
        Type                : Copy

Se [Cmdlet-referens för Data Factory][cmdlet-reference] för omfattande dokumentation om Data Factory-cmdletar. 

## Sammanfattning
I den här självstudien har du skapat en Azure-datafabrik som kopierar data från en Azure-blobb till en Azure SQL-databas. Du använde PowerShell till att skapa datafabriken, länkade tjänster, datauppsättningar och en pipeline. Här är de avancerade steg som du utförde i självstudien:  

1.  Du skapade en Azure **Data Factory**.
2.  Du skapade **länkade tjänster**:
    1. En länkad **Azure-lagrings**tjänst som länkar Azure-lagringskontot som innehåller indata.    
    2. En länkad **Azure SQL**-tjänst som länkar din Azure SQL-databas som innehåller utdata. 
3.  Du skapade **datauppsättningar** som beskriver indata och utdata för pipelines.
4.  Du skapade en **pipeline** med en **kopieringsaktivitet** med **BlobSource** som källa och **SqlSink** som mottagare. 

## Se även
| Avsnitt | Beskrivning |
| :---- | :---- |
| [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) | Den här artikeln innehåller detaljerad information om kopieringsaktiviteten som du använde i självstudien. |
| [Schemaläggning och körning](data-factory-scheduling-and-execution.md) | I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. |
| [Pipelines](data-factory-create-pipelines.md) | Den här artikeln hjälper dig förstå pipelines och aktiviteter i Azure Data Factory och hur du kan använda dem för att konstruera datadrivna arbetsflöden från slutpunkt till slutpunkt för ditt scenario eller ditt företag. |
| [Datauppsättningar](data-factory-create-datasets.md) | Den här artikeln hjälper dig att förstå datauppsättningar i Azure Data Factory.
| [Övervaka och hantera pipelines med övervakningsappen](data-factory-monitor-manage-app.md) | Den här artikeln beskriver hur du övervakar, hanterar och felsöker pipelines med övervaknings- och hanteringsappen. 



[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[adf-get-started]: data-factory-get-started.md
[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-monitor-manage-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md
 


<!--HONumber=jun16_HO2-->


