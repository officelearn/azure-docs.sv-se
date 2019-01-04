---
title: Flytta data till och från SQL Server | Microsoft Docs
description: Läs mer om hur du flyttar data till/från SQL Server-databas som finns lokalt eller i en Azure-dator med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a8283ebe135c5204dd64d8955295fdece38e0ebe
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54023506"
---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Flytta data till och från SQL Server lokalt eller på IaaS (Azure-VM) med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-sqlserver-connector.md)
> * [Version 2 (aktuell version)](../connector-sql-server.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [SQL Server-anslutningen i V2](../connector-sql-server.md).

Den här artikeln förklarar hur du använder Kopieringsaktivitet i Azure Data Factory för att flytta data till/från en lokal SQL Server-databas. Den bygger på den [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel som anger en allmän översikt över dataförflyttning med kopieringsaktiviteten. 

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från en SQL Server-databas** till följande data lagras:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager **till en SQL Server-databas**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>SQL Server-versioner som stöds
Den här anslutningen stöd för SQL Server kopierar data från/till följande versioner av-instans på lokala eller i Azure IaaS med både SQL-autentisering och Windows-autentisering: SQLServer 2016, SQLServer 2014, SQL Server 2012, SQL Server 2008 R2, SQLServer 2008, SQLServer 2005

## <a name="enabling-connectivity"></a>Aktivera anslutning
Koncept och steg som krävs för att ansluta med SQL Server finns lokalt eller i Azure IaaS (Infrastructure-as-a-Service) virtuella datorer är samma. I båda fallen kan behöva du använda Data Management Gateway för anslutning.

Se [flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md) du lär dig om Data Management Gateway och stegvisa instruktioner om hur du konfigurerar gatewayen. Hur du konfigurerar en gateway-instans är en förutsättning för att ansluta med SQL Server.

Medan du kan installera gatewayen på samma lokal dator eller molnet VM-instans som SQL Server för bättre prestanda, rekommenderar vi att du installerar dem på separata datorer. Att ha gateway och SQL Server på separata datorer minskar resurskonkurrens.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data till/från en lokal SQL Server-databas med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **Kopieringsguiden**. Se [självstudien: Skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md) en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och  **REST-API**. Se [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. 

Om du använder verktyg eller API: er kan utföra du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager: 

1. Skapa en **datafabrik**. En datafabrik kan innehålla en eller flera pipelines. 
2. Skapa **länkade tjänster** länka inkommande och utgående data du lagrar till din datafabrik. Till exempel om du kopierar data från en SQL Server-databas till Azure blob storage, skapa du två länkade tjänster för att länka din SQL Server-databasen och Azure storage-konto till datafabriken. Länkade tjänstegenskaper som är specifika för SQL Server-databas, se [länkade tjänstegenskaper](#linked-service-properties) avsnittet. 
3. Skapa **datauppsättningar** som representerar inkommande och utgående data för kopieringen. I det tidigare exemplet i det sista steget, skapar du en datauppsättning för att ange den SQL-tabellen i SQL Server-databasen som innehåller indata. Och skapar du en annan datauppsättning för att ange blob-behållaren och mappen som innehåller de data som kopieras från SQL Server-databasen. Egenskaper för datamängd som är specifika för SQL Server-databas, se [egenskaper för datamängd](#dataset-properties) avsnittet.
4. Skapa en **pipeline** med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. I exemplet som tidigare nämnts, använder du SqlSource som en källa och BlobSink som mottagare för kopieringsaktiviteten. På samma sätt, om du kopierar från Azure Blob Storage till SQL Server-databas, använder du BlobSource och SqlSink i kopieringsaktiviteten. Kopiera Aktivitetsegenskaper som är specifika för SQL Server-databas, se [kopiera Aktivitetsegenskaper](#copy-activity-properties) avsnittet. Mer information om hur du använder ett datalager som en källa eller mottagare klickar du på länken i föregående avsnitt för ditt datalager. 

När du använder guiden skapas JSON-definitioner för dessa Data Factory-entiteter (länkade tjänster, datauppsättningar och pipeline) automatiskt åt dig. När du använder Verktyg/API: er (med undantag för .NET-API) kan definiera du dessa Data Factory-entiteter med hjälp av JSON-format.  Exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från en lokal SQL Server-databas finns [JSON-exempel](#json-examples-for-copying-data-from-and-to-sql-server) i den här artikeln. 

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för SQL Server: 

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
Du skapar en länkad tjänst av typen **OnPremisesSqlServer** att länka en lokal SQL Server-databas till en data factory. Följande tabell innehåller en beskrivning för JSON-element som är specifika för den lokala SQL Server-länkade tjänst.

Följande tabell innehåller en beskrivning för JSON-element som är specifika för SQL Server-länkade tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Type-egenskapen ska anges till: **OnPremisesSqlServer**. |Ja |
| connectionString |Ange connectionString information som behövs för att ansluta till en lokal SQL Server-databasen med hjälp av SQL-autentisering eller Windows-autentisering. |Ja |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till en lokal SQL Server-databasen. |Ja |
| användarnamn |Ange användarnamnet om du använder Windows-autentisering. Exempel: **domainname\\användarnamn**. |Nej |
| lösenord |Ange lösenord för det användarkonto som du angav för användarnamnet. |Nej |

Du kan kryptera autentiseringsuppgifter med hjälp av den **New-AzureRmDataFactoryEncryptValue** cmdlet och Använd dem i anslutningssträngen som du ser i följande exempel (**EncryptedCredential** egenskapen):  

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Exempel
**JSON för att använda SQL-autentisering**

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
**JSON för att använda Windows-autentisering**

Data Management Gateway kommer att personifiera det angivna användarkontot för att ansluta till en lokal SQL Server-databasen. 

```json
{
     "Name": " MyOnPremisesSQLDB",
     "Properties":
     {
         "type": "OnPremisesSqlServer",
         "typeProperties": {
             "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
             "username": "<domain\\username>",
             "password": "<password>",
             "gatewayName": "<gateway name>"
        }
     }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd
I exemplen, har du använt en datauppsättning av typen **SqlServerTable** som representerar en tabell i SQL Server-databasen.  

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [skapar datauppsättningar](data-factory-create-datasets.md) artikeln. Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av datauppsättningen (SQL Server, Azure blob, Azure-tabellen, osv).

Avsnittet typeProperties är olika för varje typ av datauppsättning och tillhandahåller information om platsen för data i datalagret. Den **typeProperties** avsnittet för datauppsättningen av typen **SqlServerTable** har följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen eller vyn i SQL Server-databasinstansen som den länkade tjänsten refererar till. |Ja |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
Om du flyttar data från en SQL Server-databas måste du ange källtypen i kopieringsaktiviteten till **SqlSource**. På samma sätt om du flyttar data till en SQL Server-databas måste du ange Mottagartyp i kopieringsaktiviteten till **SqlSink**. Det här avsnittet innehåller en lista över egenskaper som stöds av SqlSource och SqlSink.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [skapa Pipelines](data-factory-create-pipelines.md) artikeln. Egenskaper, till exempel namn, beskrivning, indata och utdata tabeller och principer är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopieringsaktivitet tar bara en inmatning och ger endast en utdata.

Medan egenskaper som är tillgängliga i avsnittet typeProperties aktivitetens varierar med varje aktivitetstyp av. För kopieringsaktiviteten variera de beroende på vilka typer av källor och mottagare.

### <a name="sqlsource"></a>SqlSource
När källan i en Kopieringsaktivitet är av typen **SqlSource**, följande egenskaper är tillgängliga i **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: Välj * från MyTable. Kan referera till flera tabeller från databasen som refereras av datauppsättningen för indata. Om inte anges, SQL-instruktionen som körs: Välj ett värde från MyTable. |Nej |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. |Namnet på den lagrade proceduren. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |

Om den **sqlReaderQuery** har angetts för SqlSource, Kopieringsaktivitet körs den här frågan mot SQL Server Database-källan för att hämta data.

Du kan också ange en lagrad procedur genom att ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar).

Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName, används de kolumner som definierats i avsnittet struktur för att skapa en select-frågan ska köras mot SQL Server-databasen. Om definitionen för datauppsättningen inte har strukturen, markeras alla kolumner från tabellen.

> [!NOTE]
> När du använder **sqlReaderStoredProcedureName**, behöver du fortfarande ange ett värde för den **tableName** egenskap i JSON-datauppsättningen. Det finns inga verifieringar utförs men mot den här tabellen.

### <a name="sqlsink"></a>SqlSink
**SqlSink** har stöd för följande egenskaper:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| writeBatchTimeout |Väntetid för batch insert-åtgärden ska slutföras innan tidsgränsen uppnås. |Tidsintervall<br/><br/> Exempel: ”00: 30:00” (30 minuter). |Nej |
| WriteBatchSize |Infogar data i SQL-tabell när buffertstorleken når writeBatchSize. |Heltal (antal rader) |Nej (standard: 10000) |
| sqlWriterCleanupScript |Ange fråga för Kopieringsaktiviteten till att köra så att data för en viss sektor rensas. Mer information finns i [upprepningsbara kopia](#repeatable-copy) avsnittet. |Ett frågeuttryck. |Nej |
| sliceIdentifierColumnName |Ange kolumnnamnet för Kopieringsaktivitet för att fylla med automatiskt genererad sektorn identifierare som används för att rensa data för en viss sektor när köras på nytt. Mer information finns i [upprepningsbara kopia](#repeatable-copy) avsnittet. |Kolumnnamnet på en kolumn med datatypen för binary(32). |Nej |
| sqlWriterStoredProcedureName |Namnet på den lagrade proceduren som definierar hur du använder källdata i måltabellen, t.ex. att göra upsertar eller transformering med egen affärslogik. <br/><br/>Observera att den här lagrade proceduren kommer att **anropas per batch**. Om du vill göra åtgärd som endast körs en gång och har inget att göra med källdata, t.ex. Ta bort/trunkera, Använd `sqlWriterCleanupScript` egenskapen. |Namnet på den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |
| sqlWriterTableType |Ange tabellen typnamn som ska användas i den lagrade proceduren. Kopieringsaktivitet tillhandahåller data som flyttas i en temporär tabell med den här tabellen. Lagrad procedur kod kan sedan sammanfoga data kopieras med befintliga data. |Ett namn för tabellen. |Nej |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>JSON-exempel för att kopiera data från och till SQL Server
I följande exempel får exempel JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Följande exempel visar hur du kopierar data till och från SQL Server och Azure Blob Storage. Men du kan kopiera data **direkt** från källor till någon av de mottagare som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) använda Kopieringsaktivitet i Azure Data Factory.     

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Exempel: Kopiera data från SQL Server till Azure Blob
I följande exempel visas:

1. En länkad tjänst av typen [OnPremisesSqlServer](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [datauppsättning](data-factory-create-datasets.md) av typen [SqlServerTable](#dataset-properties).
4. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Den [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [SqlSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar time series-data från en SQL Server-tabell till en Azure-blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

Konfigurera data management gateway som ett första steg. Anvisningarna finns i den [flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md) artikeln.

**SQL Server-länkade tjänst**
```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Azure Blob storage-länkade tjänst**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Indatauppsättning för SQL Server**

Exemplet förutsätter att du har skapat en tabell ”MyTable” i SQL Server och den innehåller en kolumn med namnet ”timestampcolumn” för time series-data. Du kan fråga över flera tabeller inom samma databas med hjälp av en enda datauppsättning, men en enskild tabell måste användas för datauppsättningens tableName typeProperty.

Ange ”external”: ”true” informerar Data Factory-tjänsten att datauppsättningen är extern till datafabriken och inte kommer från en aktivitet i data factory.

```json
{
  "name": "SqlServerInput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Utdatauppsättning för Azure-blob**

Data skrivs till en ny blob varje timme (frequency: timme, intervall: 1). Sökvägen till mappen för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad, dag och timmar delar av starttiden.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Pipeline med en Kopieringsaktivitet**

Pipelinen innehåller en Kopieringsaktivitet som är konfigurerad för att använda dessa in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **SqlSource** och **mottagare** är **BlobSink**. SQL-frågan som angetts för den **SqlReaderQuery** egenskapen väljer vilka data under den senaste timmen att kopiera.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2016-06-01T18:00:00",
    "end":"2016-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "SqlServertoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```
I det här exemplet **sqlReaderQuery** har angetts för SqlSource. Kopieringsaktivitet körs den här frågan mot SQL Server Database-källan för att hämta data. Du kan också ange en lagrad procedur genom att ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar). SqlReaderQuery kan referera till flera tabeller i databasen refererar till datauppsättningen för indata. Det är inte begränsad till endast tabellen som datauppsättningens tableName typeProperty.

Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName, används de kolumner som definierats i avsnittet struktur för att skapa en select-frågan ska köras mot SQL Server-databasen. Om definitionen för datauppsättningen inte har strukturen, markeras alla kolumner från tabellen.

Se den [Sql-källans](#sqlsource) avsnittet och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) lista över egenskaper som stöds av SqlSource och BlobSink.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Exempel: Kopiera data från Azure Blob till SQL Server
I följande exempel visas:

1. Den länkade tjänsten av typen [OnPremisesSqlServer](#linked-service-properties).
2. Den länkade tjänsten av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. Den [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och [SqlSink](#sql-server-copy-activity-type-properties).

Exempel-kopior time series-data från en Azure-blobb till en SQL Server tabellen varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**SQL Server-länkade tjänst**

```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Azure Blob storage-länkade tjänst**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Indatauppsättning för Azure-Blobb**

Data hämtas från en ny blob varje timme (frequency: timme, intervall: 1). Mappnamn för sökvägen och filnamnet för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad och dag för starttiden och filnamnet använder timme en del av starttiden. ”external”: ”true” inställningen informerar Data Factory-tjänsten att datauppsättningen är extern till datafabriken och inte kommer från en aktivitet i data factory.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Utdatauppsättningen för SQL Server**

Exemplet kopierar data till en tabell med namnet ”MyTable” i SQL Server. Skapa tabell i SQL Server med samma antal kolumner som du förväntar dig att Blob CSV-filen ska innehålla. Nya rader har lagts till i tabell varje timme.

```json
{
  "name": "SqlServerOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Pipeline med en Kopieringsaktivitet**

Pipelinen innehåller en Kopieringsaktivitet som är konfigurerad för att använda dessa in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **BlobSource** och **mottagare** är **SqlSink**.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": " SqlServerOutput "
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

## <a name="troubleshooting-connection-issues"></a>Felsöka anslutningsproblem
1. Konfigurera din SQL Server för att acceptera fjärranslutningar. Starta **SQL Server Management Studio**, högerklicka på **server**, och klicka på **egenskaper**. Välj **anslutningar** i listan och kontrollera **Tillåt fjärranslutningar till servern**.

    ![Aktivera fjärranslutningar](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Se [konfigurerar fjärråtkomst Serverkonfigurationsalternativet](https://msdn.microsoft.com/library/ms191464.aspx) detaljerade anvisningar.
2. Starta **SQL Server Configuration Manager**. Expandera **nätverkskonfiguration för SQL Server** för den instans du vill och väljer **protokoll för MSSQLSERVER**. Du bör se protokoll i den högra rutan. Aktivera TCP/IP genom att högerklicka på **TCP/IP** och klicka på **aktivera**.

    ![Aktivera TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Se [aktivera eller inaktivera ett Server-nätverksprotokoll](https://msdn.microsoft.com/library/ms191294.aspx) information och alternativa sätt för att aktivera TCP/IP-protokollet.
3. I samma fönster dubbelklickar du på **TCP/IP** att starta **TCP/IP-egenskaper** fönster.
4. Växla till den **IP-adresser** fliken. Rulla ned för att se **IPAll** avsnittet. Anteckna den ** TCP-Port ** (standardvärdet är **1433**).
5. Skapa en **regeln för Windows-brandväggen** på datorn för att tillåta inkommande trafik via den här porten.  
6. **Verifiera anslutningen**: Anslut till SQL-servern med fullständigt kvalificerade namnet genom att använda SQL Server Management Studio från en annan dator. Till exempel: ”<machine>.<domain>.corp.<company>.com,1433.”

   > [!IMPORTANT]

   > Se [flytta data mellan lokala källor och molnet med Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) detaljerad information.
   >
   > Se [felsöka problem med gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tips om hur du felsöker anslutning/gateway-relaterade problem.
   >
   >


## <a name="identity-columns-in-the-target-database"></a>Identitetskolumner i måldatabasen
Det här avsnittet innehåller ett exempel som kopierar data från en källtabellen med någon identity-kolumn till en måltabell med en identity-kolumn.

**Källtabellen:**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```
**Måltabell:**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Observera att måltabellen har en identitetskolumn.

**Källa dataset JSON-definition**

```json
{
    "name": "SampleSource",
    "properties": {
        "published": false,
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**Mål dataset JSON-definition**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

Observera att som din käll- och tabell har olika schema (målet har ytterligare en kolumn med identiteten). I det här scenariot måste du ange **struktur** -egenskapen i datauppsättningsdefinitionen mål som inte innehåller en identity-kolumn.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Anropa lagrade procedur från SQL-mottagare
Se [anropa lagrade proceduren för SQL-mottagaren i kopieringsaktiviteten](data-factory-invoke-stored-procedure-from-copy-activity.md) artikeln ett exempel på att anropa en lagrad procedur från SQL-mottagare i en Kopieringsaktivitet i en pipeline.

## <a name="type-mapping-for-sql-server"></a>Mappning för SQLServer
Som vi nämnde i den [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikeln kopieringsaktiviteten utför automatisk konverteringar från typer av datakällor till mottagare typer med följande metod i steg 2:

1. Konvertera från interna källtyper till .NET-typ
2. Konvertera från .NET-typ till interna mottagare

När du flyttar data till och från SQLServer, används följande mappningar från SQL-typ till .NET-typ och vice versa.

Mappningen är samma som SQL-Server-Datatypsmappningen för ADO.NET.

| SQL Server Database Engine-typ | .NET framework-typ |
| --- | --- |
| bigint |Int64 |
| binär |Byte] |
| bitars |Boolesk |
| Char |Sträng, Char] |
| datum |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| decimaltal |decimaltal |
| FILESTREAM-attributet (varbinary(max)) |Byte] |
| Flyttal |Double-värde |
| image |Byte] |
| int |Int32 |
| pengar |decimaltal |
| nchar |Sträng, Char] |
| ntext |Sträng, Char] |
| numeriskt |decimaltal |
| nvarchar |Sträng, Char] |
| verkliga |Enkel |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |decimaltal |
| sql_variant |Objektet * |
| text |Sträng, Char] |
| time |Tidsintervall |
| tidsstämpel |Byte] |
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinary |Byte] |
| varchar |Sträng, Char] |
| xml |Xml |

## <a name="mapping-source-to-sink-columns"></a>Mappning av källa till kolumner för mottagare
Om du vill mappa kolumner från datauppsättningen för källan till kolumner från en datauppsättning för mottagare, se [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Upprepningsbara kopia
När du kopierar data till SQL Server-databasen, läggs kopieringsaktiviteten data till tabellen mottagare som standard. Om du vill utföra en UPSERT i stället Se [Repeatable skriva till SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) artikeln. 

Kom ihåg att undvika oväntade resultat repeterbarhet när kopiera data från relationsdata lagras. I Azure Data Factory kan du köra en sektor manuellt. Du kan också konfigurera återförsöksprincipen för en datauppsättning så att en sektor som körs när ett fel uppstår. När ett segment ska köras på nytt på något sätt, måste du se till att samma data läses oavsett hur många gånger som en sektor körs. Se [Repeatable läsa från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se [kopiera aktivitet prestanda- och Justeringsguide](data-factory-copy-activity-performance.md) att lära dig om viktiga faktorer att påverka prestandan för dataförflyttning (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
