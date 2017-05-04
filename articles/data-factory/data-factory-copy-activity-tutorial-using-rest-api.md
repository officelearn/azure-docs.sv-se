---
title: "Självstudiekurs: Skapa en pipeline med en kopieringsaktivitet med hjälp av REST-API:et | Microsoft Docs"
description: "I den här självstudiekursen ska du skapa en Azure Data Factory-pipeline med en kopieringsaktivitet med hjälp av REST-API:et."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1704cdf8-30ad-49bc-a71c-4057e26e7350
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/11/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: f4d2535e929996101f6e22ffbd0b2105e8fbf2a8
ms.lasthandoff: 04/27/2017


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-rest-api"></a>Självstudiekurs: Skapa en pipeline med en kopieringsaktivitet med hjälp av REST-API:et
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

Den här självstudiekursen visar hur du skapar och övervakar en Azure-datafabrik med hjälp av REST-API:et. Pipelinen i datafabriken använder en kopieringsaktivitet för att kopiera data från Azure Blob Storage till Azure SQL Database.

> [!NOTE]
> Den här artikeln beskriver inte hela REST-API:et för Data Factory. Omfattande dokumentation om Data Factory-cmdlets finns i [referensen för REST-API:et för Data Factory](/rest/api/datafactory/).
> 
> Datapipelinen i den här självstudien kopierar data från ett källdatalager till ett måldatalager. Det transformerar inte indata för att generera utdata. Om du vill se en självstudie som visar hur du omvandlar data med Azure Data Factory går du till [Tutorial: Build a pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Självstudie: Bygg en pipeline för att omvandla data med Hadoop-kluster).

## <a name="prerequisites"></a>Krav
* Gå igenom [Självstudier – översikt](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) och slutför de **nödvändiga** stegen.
* Installera [Curl](https://curl.haxx.se/dlwiz/) på din dator. Du kan använda verktyget Curl med REST-kommandon för att skapa en datafabrik. 
* Gör följande genom att följa anvisningarna i [den här artikeln](../azure-resource-manager/resource-group-create-service-principal-portal.md): 
  1. Skapa en webbapp med namnet **ADFCopyTutorialApp** i Azure Active Directory.
  2. Hämta ett **klient-ID** och en **hemlig nyckel**. 
  3. Hämta ett **klientorganisations-ID**. 
  4. Tilldela **ADFCopyTutorialApp**-programmet rollen som **Data Factory-deltagare**.  
* [Installera Azure PowerShell](/powershell/azure/overview).  
* Starta **PowerShell** och kör följande kommando. Låt Azure PowerShell vara öppet tills du är klar med självstudien. Om du stänger och öppnar det igen måste du köra kommandona en gång till.
  
  1. Kör följande kommando och ange det användarnamn och lösenord som du använder för att logga in på Azure-portalen:
    
    ```PowerShell 
    Login-AzureRmAccount
    ```   
  2. Kör följande kommando för att visa alla prenumerationer för det här kontot:

    ```PowerShell     
    Get-AzureRmSubscription
    ``` 
  3. Kör följande kommando för att välja den prenumeration som du vill arbeta med. Ersätt **&lt;NameOfAzureSubscription**&gt; med namnet på din Azure-prenumeration. 
     
    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    ```
  4. Skapa en Azure-resursgrupp med namnet **ADFTutorialResourceGroup** genom att köra följande kommando i PowerShell:  

    ```PowerShell     
      New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
     
      Om resursgruppen redan finns anger du om du vill uppdatera den (Y) eller lämna den som den är (N). 
     
      Vissa av stegen i den här självstudien förutsätter att du använder resursgruppen med namnet ADFTutorialResourceGroup. Om du använder en annan resursgrupp måste du använda namnet på din resursgrupp i stället för ADFTutorialResourceGroup i den här självstudiekursen.

## <a name="create-json-definitions"></a>Skapa JSON-definitioner
Skapa följande JSON-filer i mappen som curl.exe finns i. 

### <a name="datafactoryjson"></a>datafactory.json
> [!IMPORTANT]
> Namnet måste vara globalt unikt, så du kan behöva lägga till ett prefix eller suffix till ADFCopyTutorialDF för att göra det unikt. 
> 
> 

```JSON
{  
    "name": "ADFCopyTutorialDF",  
    "location": "WestUS"
}  
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> Ersätt **accountname** och **accountkey** med namnet och nyckeln för ditt Azure-lagringskonto. Information om hur du hämtar din lagringsåtkomstnyckel finns i [Visa, kopiera och återskapa lagringsåtkomstnycklar](../storage/storage-create-storage-account.md#manage-your-storage-access-keys).
> 
> 

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="azuersqllinkedservicejson"></a>azuersqllinkedservice.json
> [!IMPORTANT]
> Ersätt **servername**, **databasename**, **username** och **password** med namnet på din Azure SQL-server, namnet på SQL-databasen, användarkontot och lösenordet för kontot.  
> 
> 

```JSON
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

### <a name="inputdatasetjson"></a>inputdataset.json

```JSON
{
  "name": "AzureBlobInput",
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
    "linkedServiceName": "AzureStorageLinkedService",
    "typeProperties": {
      "folderPath": "adftutorial/",
      "fileName": "emp.txt",
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
```

JSON-definitionen definierar en datauppsättning med namnet **AzureBlobInput**, som representerar indata för en aktivitet i pipelinen. Dessutom anger den att indata finns i filen **emp.txt** som finns i blobbehållaren **adftutorial**. 

 Observera följande punkter: 

* datauppsättningens **typ** anges till **AzureBlob**.
* **linkedServiceName** anges till **AzureStorageLinkedService**. 
* **folderPath** är **adftutorial**-behållaren och **filename** är **emp.txt**.  
* formatet **typ** anges till **TextFormat**
* Det finns två fält i textfilen – **FirstName** och **LastName** – som avgränsas med ett kommatecken (columnDelimiter)    
* **availability** är inställt på **hourly** (frekvensen är inställd på timme och intervallet på 1). Det betyder att Data Factory söker efter indata varje timme i rotmappen för den angivna blobbehållaren (adftutorial). 

Om du inte anger något **fileName** för en indatauppsättning betraktas alla filer/blobbar från indatamappen (folderPath) som indata. Om du anger ett fileName i JSON betraktas endast den angivna filen/blobben som indata.

Om du inte anger något **fileName** för en **utdatatabell** namnges filerna som genereras i **folderPath** med följande format: Data.&lt;Guid&gt;.txt (exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

Ange **folderPath** och **fileName** dynamiskt utifrån **SliceStart**-tiden med hjälp av **partitionedBy**-egenskapen. I följande exempel använder folderPath Year, Month och Day från SliceStart (starttiden för den sektor som bearbetas) och fileName använder Hour från SliceStart. Om exempelvis en sektor produceras 2014-10-20T08:00:00, anges folderName till wikidatagateway/wikisampledataout/2014/10/20 och fileName anges till 08.csv. 

```JSON
  "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy": 
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
],
```

### <a name="outputdatasetjson"></a>outputdataset.json

```JSON
{
  "name": "AzureSqlOutput",
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
```

JSON-definitionen definierar en datauppsättning med namnet **AzureSqlOutput**, som representerar utdata för en aktivitet i pipelinen. Dessutom anger den att resultaten lagras i tabellen: **emp** i databasen som representeras av AzureSqlLinkedService. Avsnittet **availability** anger att utdatauppsättningen genereras varje timme (frequency: hour och interval: 1).

Observera följande punkter: 

* datauppsättningens **typ** anges till **AzureSQLTable**.
* **linkedServiceName** anges till **AzureSqlLinkedService**.
* **tablename** anges till **emp**.
* Det finns tre kolumner – **ID**, **FirstName** och **LastName** – i emp-tabellen i databasen. ID är en identitetskolumn, så du anger bara **FirstName** och **LastName** här.
* **Tillgängligheten** anges till **varje timme** (frekvens inställd på timme och intervall inställd på 1).  Data Factory-tjänsten genererar en utdatasektor varje timme i **emp**-tabellen i Azure SQL-databasen.

### <a name="pipelinejson"></a>pipeline.json

```JSON
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
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-08-12T00:00:00Z",
    "end": "2016-08-13T00:00:00Z"
  }
}
```

Observera följande punkter:

* I avsnittet Aktiviteter finns det bara en aktivitet vars **typ** anges till **CopyActivity**.
* Indata för aktiviteten är inställd på **AzureBlobInput** och utdata för aktiviteten är inställd på **AzureSqlOutput**.
* I avsnittet för **transformering** har **BlobSource** angetts som källtyp och **SqlSink** har angetts som mottagartyp.

Ersätt värdet i **start**egenskapen med den aktuella dagen och **slut**värdet med nästa dag. Du kan ange endast datumdelen och hoppa över tidsvärdet. Till exempel ”2015-02-03” som motsvarar ”2015-02-03T00:00:00Z”

Både start- och slutdatum måste vara i [ISO-format](http://en.wikipedia.org/wiki/ISO_8601). Exempel: 2014-10-14T16:32:41Z. **Sluttiden** är valfri, men vi använder den i den här självstudiekursen. 

Om du inte anger värdet för **slut**egenskapen, beräknas det som ”**start + 48 timmar**”. Om du vill köra pipelinen på obestämd tid, anger du **9999-09-09** som värde för **slut**egenskapen.

I exemplet finns det 24 datasektorer eftersom varje datasektor skapas varje timme.

> [!NOTE]
> Mer information om JSON-egenskaper som används i exemplet ovan finns i [Uppbyggnad av en pipeline](data-factory-create-pipelines.md).
> 
> 

## <a name="set-global-variables"></a>Ange globala variabler
När du har ersatt värdena med dina egna kör du följande kommandon i Azure PowerShell:

> [!IMPORTANT]
> Anvisningar för hur du hämtar klient-ID, klienthemlighet, klientorganisations-ID och prenumerations-ID finns i [kravavsnittet](#prerequisites).   
> 
> 

```JSON
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
$adf = "ADFCopyTutorialDF"
```

## <a name="authenticate-with-aad"></a>Autentisera med AAD
Kör följande kommando för att autentisera med Azure Active Directory (AAD): 

```PowerShell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken) 
```

## <a name="create-data-factory"></a>Skapa en datafabrik
I det här steget ska du skapa en Azure Data Factory med namnet **ADFCopyTutorialDF**. En datafabrik kan ha en eller flera pipelines. En pipeline kan innehålla en eller flera aktiviteter. Till exempel en kopieringsaktivitet som kopierar data från ett källdatalager till ett måldatalager. En Hive-aktivitet för HDInsight för att köra Hive-skript som transformerar indata till produktutdata. Skapa datafabriken genom att köra följande kommandon: 

1. Tilldela kommandot till variabeln med namnet **cmd**. 
   
    Bekräfta att namnet på datafabriken som du anger här (ADFCopyTutorialDF) matchar namnet i **datafactory.json**. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF?api-version=2015-10-01};
    ```
2. Kör kommandot med **Invoke-Command**.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Granska resultaten. Om datafabriken har skapats korrekt visas JSON för datafabriken i **resultatet**. Annars visas ett felmeddelande.  
   
    ```
    Write-Host $results
    ```

Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om felet **Datafabriksnamnet ”ADFCopyTutorialDF” är inte tillgängligt** returneras går du igenom följande steg:  
  
  1. Ändra namnet (till exempel dittnamnADFCopyTutorialDF) i filen **datafactory.json**.
  2. I det första kommandot där variabeln **$cmd** tilldelas ett värde ersätter du ADFCopyTutorialDF med det nya namnet och kör kommandot. 
  3. Anropa REST-API:et genom att köra de följande två kommandona för att skapa datafabriken och skriva ut resultatet av åtgärden. 
     
     Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.
* Om du vill skapa Data Factory-instanser måste du vara deltagare/administratör för Azure-prenumerationen
* Namnet på datafabriken kan registreras som ett DNS-namn i framtiden och blir då synligt offentligt.
* Om du får felet: ”**Den här prenumerationen har inte registrerats för användning av namnområdet Microsoft.DataFactory**” gör du något av följande och försöker att publicera igen: 
  
  * I Azure PowerShell kör du följande kommando för att registrera Data Factory-providern: 

    ```PowerShell    
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    Du kan köra följande kommando om du vill kontrollera att Data Factory-providern är registrerad. 
    
    ```PowerShell
    Get-AzureRmResourceProvider
    ```
  * Logga in med Azure-prenumerationen i [Azure Portal](https://portal.azure.com) och navigera till ett Data Factory-blad (eller) skapa en datafabrik i Azure Portal. Med den här åtgärden registreras providern automatiskt.

Du måste först skapa några Data Factory-entiteter innan du skapar en pipeline. Först skapar du länkade tjänster för att länka käll- och måldatalager till ditt datalager. Sedan definierar du in- och utdatauppsättningar för att representera data i länkade datalager. Slutligen skapar du pipelinen med en aktivitet som använder dessa datauppsättningar.

## <a name="create-linked-services"></a>Skapa länkade tjänster
Länkade tjänster länkar datalager eller beräkningstjänster till en Azure-datafabrik. Ett datalager kan vara Azure Storage-lagring, Azure SQL Database eller en lokal SQL Server-databas som innehåller indata eller som lagrar utdata för en Data Factory-pipeline. En beräkningstjänst är en tjänst som bearbetar indata och genererar utdata. 

I det här steget skapar du två länkade tjänster: **AzureStorageLinkedService** och **AzureSqlLinkedService**. Den länkade AzureStorageLinkedService-tjänsten länkar ett Azure Storage-konto och AzureSqlLinkedService länkar en Azure SQL-databas till datafabriken: **ADFCopyTutorialDF**. Senare i den här självstudien, som beskriver hur du kopierar data från en blobbehållare i AzureStorageLinkedService till en SQL-tabell i AzureSqlLinkedService, ska du skapa en pipeline.

### <a name="create-azure-storage-linked-service"></a>Skapa en länkad Azure-lagringstjänst
I det här steget länkar du ditt Azure-lagringskonto till datafabriken. I den här självstudiekursen använder du Azure Storage-kontot för att lagra indata. 

1. Tilldela kommandot till variabeln med namnet **cmd**. 

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. Kör kommandot med **Invoke-Command**.
    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Granska resultaten. Om den länkade tjänsten har skapats korrekt visas JSON för den länkade tjänsten i **resultatet**. Annars visas ett felmeddelande.

    ```PowerShell   
    Write-Host $results
    ```

### <a name="create-azure-sql-linked-service"></a>Skapa en länkad Azure SQL-tjänst
I det här steget länkar du Azure SQL-databasen till din datafabrik. I den här självstudiekursen använder du samma Azure SQL-databas för att lagra utdata.

1. Tilldela kommandot till variabeln med namnet **cmd**. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azuresqllinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
    ```
2. Kör kommandot med **Invoke-Command**.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Granska resultaten. Om den länkade tjänsten har skapats korrekt visas JSON för den länkade tjänsten i **resultatet**. Annars visas ett felmeddelande.
   
    ```PowerShell
    Write-Host $results
    ```

## <a name="create-datasets"></a>Skapa datauppsättningar
I föregående steg skapade du de länkade tjänsterna **AzureStorageLinkedService** och **AzureSqlLinkedService** för att länka ett Azure-lagringskonto och en Azure SQL-databas till datafabriken: **ADFCopyTutorialDF**. I det här steget skapar du datauppsättningar som representerar indata och utdata för kopieringsaktiviteten i pipelinen som du skapar i nästa steg. 

Den inkommande datauppsättningen i den här självstudiekursen refererar till en blobbehållare i Azure Storage som AzureStorageLinkedService pekar på. Utdatauppsättningen refererar till en SQL-tabell i Azure SQL-databasen som AzureSqlLinkedService pekar på.  

### <a name="prepare-azure-blob-storage-and-azure-sql-database-for-the-tutorial"></a>Förbereda Azure blobblagring och Azure SQL Database för självstudien
Förbered Azure-blobblagringen och Azure SQL-databasen för den här självstudiekursen genom att utföra följande steg. 

* Skapa en blobbehållare med namnet **adftutorial** i Azure Blob Storage som **AzureStorageLinkedService** pekar på. 
* Skapa och ladda upp en textfil, **emp.txt**, som en blobb till behållaren **adftutorial**. 
* Skapa en tabell med namnet **emp** i Azure SQL Database i Azure SQL-databasen som **AzureSqlLinkedService** pekar på.

1. Öppna Anteckningar. Kopiera följande text och spara det som **emp.txt** i mappen **C:\ADFGetStartedPSH** på hårddisken. 

    ```   
    John, Doe
    Jane, Doe
    ```
2. Använd verktyg som t.ex. [Azure Lagringsutforskaren](https://azurestorageexplorer.codeplex.com/) till att skapa behållaren **adftutorial** och för att ladda upp filen **emp.txt** till behållaren.
   
    ![Azure Lagringsutforskaren](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
3. Använd följande SQL-skript för att skapa tabellen **emp** i din Azure SQL Database.  

    ```SQL
    CREATE TABLE dbo.emp 
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 
    ```

    Om SQL Server 2014 är installerat på datorn följer du anvisningarna i artikeln [Steg 2: Ansluta till SQL Database i Hantera Azure SQL Database med SQL Server Management Studio][sql-management-studio] för att ansluta till Azure SQL-servern och köra SQL-skriptet.

    Om klienten inte har åtkomst till Azure SQL-servern måste du konfigurera brandväggen för din Azure SQL-server och tillåta åtkomst från din dator (IP-adress). Anvisningar för hur du konfigurerar brandväggen för Azure SQL-servern finns i [den här artikeln](../sql-database/sql-database-configure-firewall-settings.md).

### <a name="create-input-dataset"></a>Skapa indatauppsättning
I det här steget skapar du en datauppsättning med namnet **AzureBlobInput** som pekar på en blobbehållare i Azure Storage som representeras av den länkade tjänsten **AzureStorageLinkedService**. Den här blobbehållaren (adftutorial) innehåller indata i filen: **emp.txt**. 

1. Tilldela kommandot till variabeln med namnet **cmd**. 

    ```PowerSHell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
    ```
2. Kör kommandot med **Invoke-Command**.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Granska resultaten. Om datauppsättningen har skapats korrekt visas JSON för datauppsättningen i **resultatet**. Annars visas ett felmeddelande.
   
    ```PowerShell
    Write-Host $results
    ```

### <a name="create-output-dataset"></a>Skapa datauppsättning för utdata
I det här steget ska du skapa en tabell med namnet **AzureSqlOutput**. Den här datauppsättningen pekar på en SQL-tabell (emp) i Azure SQL-databasen som representeras av **AzureSqlLinkedService**. Pipelinen kopierar data från indatablobben till tabellen **emp**. 

1. Tilldela kommandot till variabeln med namnet **cmd**.

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
    ```
2. Kör kommandot med **Invoke-Command**.
    
    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Granska resultaten. Om datauppsättningen har skapats korrekt visas JSON för datauppsättningen i **resultatet**. Annars visas ett felmeddelande.
   
    ```PowerShell
    Write-Host $results
    ``` 

## <a name="create-pipeline"></a>Skapa pipeline
I det här steget ska du skapa en pipeline med en **kopieringsaktivitet** som använder **AzureBlobInput** som indata och **AzureSqlOutput** som utdata.

1. Tilldela kommandot till variabeln med namnet **cmd**.

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
    ```
2. Kör kommandot med **Invoke-Command**.

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Granska resultaten. Om datauppsättningen har skapats korrekt visas JSON för datauppsättningen i **resultatet**. Annars visas ett felmeddelande.  

    ```PowerShell   
    Write-Host $results
    ```

**Grattis!** Du har skapat en Azure-datafabrik, med en pipeline som kopierar data från Azure Blob Storage till en Azure SQL-databas.

## <a name="monitor-pipeline"></a>Övervaka pipeline
I det här steget ska du använda REST-API:et för Data Factory för att övervaka sektorer som genereras av pipelinen.

```PowerShell
$ds ="AzureSqlOutput"
```

```PowerShell
$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};
```

```PowerShell
$results2 = Invoke-Command -scriptblock $cmd;
```

```PowerShell
IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

Kör dessa kommandon tills du ser en sektor med statusen **Ready** eller **Failed**. När sektorn har statusen Ready tittar du efter utdata i tabellen **emp** i Azure SQL-databasen. 

För varje sektor kopieras två rader med data från källfilen till emp-tabellen i Azure SQL-databasen. Därför finns det 24 nya poster i emp-tabellen när alla sektorer har bearbetats (statusen Ready). 

## <a name="summary"></a>Sammanfattning
I den här självstudiekursen använde du REST-API:et för att skapa en Azure-datafabrik och kopiera data från ett Azure-blobb till en Azure SQL-databas. Här är de avancerade steg som du utförde i självstudien:  

1. Du skapade en Azure **Data Factory**.
2. Du skapade **länkade tjänster**:
   1. En länkad Azure Storage-tjänst för att länka Azure Storage-kontot som innehåller indata.     
   2. En länkad Azure SQL-tjänst för att länka Azure SQL-databasen som innehåller utdata. 
3. Du skapade **datauppsättningar** som beskriver indata och utdata för pipelines.
4. Du skapade en **pipeline** med en kopieringsaktivitet med BlobSource som källa och SqlSink som mottagare. 

## <a name="see-also"></a>Se även
| Avsnitt | Beskrivning |
|:--- |:--- |
| [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) |Den här artikeln innehåller detaljerad information om kopieringsaktiviteten som du använde i självstudien. |
| [Schemaläggning och körning](data-factory-scheduling-and-execution.md) |I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. |
| [Pipelines](data-factory-create-pipelines.md) |I den här artikeln beskriver vi pipelines och aktiviteter i Azure Data Factory och hur du kan använda dem för att konstruera datadrivna arbetsflöden från slutpunkt till slutpunkt för ditt scenario eller ditt företag. |
| [Datauppsättningar](data-factory-create-datasets.md) |I den här artikeln förklaras hur datauppsättningar fungerar i Azure Data Factory. |
| [Övervaka och hantera pipelines med övervakningsappen](data-factory-monitor-manage-app.md) |Den här artikeln beskriver hur du övervakar, hanterar och felsöker pipelines med övervaknings- och hanteringsappen. |

[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: /powershell/azureps-cmdlets-docs
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md

