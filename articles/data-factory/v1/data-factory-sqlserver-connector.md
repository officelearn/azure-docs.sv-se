---
title: Flytta data till och från SQL Server
description: Lär dig mer om hur du flyttar data till/från SQL Server-databas som är lokalt eller i en Azure VM med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5e4bbe1e6bd944787d47c5e3ed98de582c088a52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265771"
---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Flytta data till och från SQL Server lokalt eller på IaaS (Azure VM) med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-sqlserver-connector.md)
> * [Version 2 (aktuell version)](../connector-sql-server.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [SQL Server-anslutningsappen i V2](../connector-sql-server.md).

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data till/från en lokal SQL Server-databas. Den bygger på [datarörelseaktiviteter](data-factory-data-movement-activities.md) artikeln, som presenterar en allmän översikt över data förflyttning med kopian aktivitet.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från en SQL Server-databas** till följande datalager:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager **till en SQL Server-databas:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>SQL Server-versioner som stöds
Den här SQL Server-anslutningen stöder kopiering av data från/till följande versioner av instanser som finns lokalt eller i Azure IaaS med både SQL-autentisering och Windows-autentisering: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Aktivera anslutning
De begrepp och steg som behövs för att ansluta till SQL Server som finns lokalt eller i virtuella Azure IaaS -datorer (Infrastruktur som en tjänst) är desamma. I båda fallen måste du använda Data Management Gateway för anslutning.

Se [flytta data mellan lokala platser och molnartikel](data-factory-move-data-between-onprem-and-cloud.md) om du vill veta mer om Data Management Gateway och steg-för-steg-instruktioner om hur du konfigurerar gatewayen. Att konfigurera en gateway-instans är en förutsättning för anslutning till SQL Server.

Även om du kan installera gateway på samma lokala dator- eller moln-VM-instans som SQL Server för bättre prestanda, rekommenderar vi att du installerar dem på separata datorer. Om gatewayen och SQL Server finns på separata datorer minskar resurskonkurrensen.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data till/från en lokal SQL Server-databas med hjälp av olika verktyg/API:er.

Det enklaste sättet att skapa en pipeline är att använda **kopieringsguiden**. Se [självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång när du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-mall,** **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet.

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa en **datafabrik**. En datafabrik kan innehålla en eller flera pipelines.
2. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken. Om du till exempel kopierar data från en SQL Server-databas till en Azure-blob-lagring skapar du två länkade tjänster för att länka din SQL Server-databas och Azure-lagringskonto till din datafabrik. För länkade tjänstegenskaper som är specifika för SQL Server-databasen finns i avsnittet [länkade tjänstegenskaper.](#linked-service-properties)
3. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen. I exemplet som nämns i det sista steget skapar du en datauppsättning för att ange SQL-tabellen i SQL Server-databasen som innehåller indata. Och du skapar en annan datauppsättning för att ange blob-behållaren och mappen som innehåller data som kopierats från SQL Server-databasen. Informationsuppsättningsegenskaper som är specifika för SQL Server-databasen finns i avsnittet [egenskaper för datauppsättningen.](#dataset-properties)
4. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. I det tidigare exemplet använder du SqlSource som källa och BlobSink som en mottagare för kopieringsaktiviteten. Om du kopierar från Azure Blob Storage till SQL Server Database använder du blobSource och SqlSink i kopieringsaktiviteten. Information om egenskaper för kopieringsaktivitet som är specifika för SQL Server-databasen finns i avsnittet [kopiera aktivitetsegenskaper.](#copy-activity-properties) Om du vill ha information om hur du använder ett datalager som källa eller en diskbänk klickar du på länken i föregående avsnitt för datalagret.

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet. Exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från en lokal SQL Server-databas finns i avsnittet [JSON-exempel](#json-examples-for-copying-data-from-and-to-sql-server) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera datafabrikentiteter som är specifika för SQL Server:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
Du skapar en länkad tjänst av typen **OnPremisesSqlServer** för att länka en lokal SQL Server-databas till en datafabrik. I följande tabell beskrivs beskrivning av JSON-element som är specifika för den lokala SQL Server-länkade tjänsten.

I följande tabell beskrivs beskrivning av JSON-element som är specifika för SQL Server-länkade tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type ska anges till: **OnPremisesSqlServer**. |Ja |
| Connectionstring |Ange anslutningStringsinformation som behövs för att ansluta till den lokala SQL Server-databasen med antingen SQL-autentisering eller Windows-autentisering. |Ja |
| gatewayName (gatewayName) |Namnet på den gateway som tjänsten Data Factory ska använda för att ansluta till den lokala SQL Server-databasen. |Ja |
| användarnamn |Ange användarnamn om du använder Windows-autentisering. Exempel: **användarnamn\\på domännamn**. |Inga |
| password |Ange lösenord för det användarkonto som du angav för användarnamnet. |Inga |

Du kan kryptera autentiseringsuppgifter med cmdleten **New-AzDataFactoryEncryptValue** och använda dem i anslutningssträngen enligt följande exempel (**Egenskapen EncryptedCredential):**

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

Data Management Gateway personifierar det angivna användarkontot för att ansluta till den lokala SQL Server-databasen.

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
I exemplen har du använt en datauppsättning av typen **SqlServerTable** för att representera en tabell i en SQL Server-databas.

En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper (SQL Server, Azure blob, Azure-tabell osv.).

Avsnittet typeProperties är olika för varje typ av datauppsättning och ger information om platsen för data i datalagret. Avsnittet **typeProperties** för datauppsättningen av typen **SqlServerTable** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen eller vyn i SQL Server Database-instansen som länkade tjänsten refererar till. |Ja |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
Om du flyttar data från en SQL Server-databas anger du källtypen i kopieringsaktiviteten till **SqlSource**. Om du flyttar data till en SQL Server-databas anger du på samma sätt sink-typen i kopieringsaktiviteten till **SqlSink**. Det här avsnittet innehåller en lista över egenskaper som stöds av SqlSource och SqlSink.

En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata- och utdatatabeller och principer är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopieringsaktiviteten tar bara en indata och producerar bara en utdata.

Medan egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar med varje aktivitetstyp. För kopieringsaktivitet varierar de beroende på vilka typer av källor och sänkor som finns.

### <a name="sqlsource"></a>SqlSource (Påtkälla)
När källan i en kopieringsaktivitet är av typen **SqlSource**är följande egenskaper tillgängliga i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Till exempel: välj * från MyTable. Kan referera till flera tabeller från databasen som refereras av indatauppsättningen. Om inget anges väljer SQL-uttrycket som körs: välj från MyTable. |Inga |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. |Namnet på den lagrade proceduren. Det senaste SQL-uttrycket måste vara en SELECT-sats i den lagrade proceduren. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värdepar. Namn och hölje av parametrar måste stämma överens med namn och hölje för de lagrade procedureparametrarna. |Inga |

Om **sqlReaderQuery** har angetts för SqlSource körs den här frågan mot SQL Server-databaskällan för att hämta data.

Du kan också ange en lagrad procedur genom att ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar).

Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName används kolumnerna som definieras i strukturavsnittet för att skapa en urvalsfråga som ska köras mot SQL Server-databasen. Om datauppsättningsdefinitionen inte har strukturen markeras alla kolumner i tabellen.

> [!NOTE]
> När du använder **sqlReaderStoredProcedureName**måste du fortfarande ange ett värde för **egenskapen tableName** i datauppsättningen JSON. Det finns inga valideringar som utförs mot den här tabellen dock.

### <a name="sqlsink"></a>SqlSink (SqlSink)
**SqlSink** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| skriverBatchTimeout |Vänta på att batchinsatsen ska slutföras innan den har klarats upp. |Gått<br/><br/> Exempel: "00:30:00" (30 minuter). |Inga |
| skriverBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize. |Heltal (antal rader) |Nej (standard: 10000) |
| sqlWriterCleanupScript |Ange fråga för Kopieringsaktivitet som ska köras så att data för ett visst segment rensas. Mer information finns i [avsnittet om repeterbar kopiering.](#repeatable-copy) |En frågesats. |Inga |
| sliceIdentifierColumnName |Ange kolumnnamn för Kopiera aktivitet som ska fyllas med automatisk genererad segmentidentifierare, som används för att rensa data från ett visst segment när den körs igen. Mer information finns i [avsnittet om repeterbar kopiering.](#repeatable-copy) |Kolumnnamnet på en kolumn med datatyp av binär(32). |Inga |
| sqlWriterStoredProcedureName |Namnet på den lagrade proceduren som definierar hur källdata ska användas i måltabellen, t.ex. <br/><br/>Observera att den här lagrade proceduren **anropas per batch**. Om du vill utföra åtgärder som bara körs en gång och inte har något att `sqlWriterCleanupScript` göra med källdata, t.ex. |Namnet på den lagrade proceduren. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värdepar. Namn och hölje av parametrar måste stämma överens med namn och hölje för de lagrade procedureparametrarna. |Inga |
| sqlWriterTableType |Ange tabelltypsnamn som ska användas i den lagrade proceduren. Kopieringsaktivitet gör de data som flyttas tillgängliga i en temp-tabell med den här tabelltypen. Lagrad procedurkod kan sedan sammanfoga data som kopieras med befintliga data. |Ett tabelltypnamn. |Inga |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>JSON-exempel för kopiering av data från och till SQL Server
Följande exempel innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Följande exempel visar hur du kopierar data till och från SQL Server och Azure Blob Storage. Data kan dock kopieras **direkt** från någon av källorna till någon av de diskhoar som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopieringsaktiviteten i Azure Data Factory.

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Exempel: Kopiera data från SQL Server till Azure Blob
Följande exempel visar:

1. En länkad tjänst av typen [OnPremisesSqlServer](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En [indatauppsättning](data-factory-create-datasets.md) av typen [SqlServerTable](#dataset-properties).
4. En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Pipelinen](data-factory-create-pipelines.md) med kopiera aktivitet som använder [SqlSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar tidsseriedata från en SQL Server-tabell till en Azure-blob varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

Som ett första steg konfigurerar du datahanteringsgatewayen. Instruktionerna finns i [flyttdata mellan lokala platser och molnartikel.](data-factory-move-data-between-onprem-and-cloud.md)

**SQL Server-länkad tjänst**
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
**Azure Blob lagringslänkade tjänst**

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
**SQL Server-indatauppsättning**

Exemplet förutsätter att du har skapat en tabell "MyTable" i SQL Server och den innehåller en kolumn som kallas "tidsstämpelpcolumn" för tidsseriedata. Du kan fråga över flera tabeller i samma databas med hjälp av en enda datauppsättning, men en enda tabell måste användas för datauppsättningens tabellNamnstypFel.

Inställningen "extern": "true" informerar Data Factory-tjänsten om att datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

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

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder delar av starttiden för år, månad, dag och timmar.

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
**Pipeline med kopieringsaktivitet**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda dessa in- och utdatauppsättningar och som är schemalagd att köras varje timme. I pipeline-JSON-definitionen anges **källtypen** till **SqlSource** och **sink-typen** är inställd på **BlobSink**. Sql-frågan som angetts för egenskapen **SqlReaderQuery** väljer de data som ska kopieras under den senaste timmen.

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
I det här exemplet anges **sqlReaderQuery** för SqlSource. Kopieringsaktiviteten kör den här frågan mot SQL Server-databaskällan för att hämta data. Du kan också ange en lagrad procedur genom att ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar). SqlReaderQuery kan referera till flera tabeller i databasen som refereras av indatauppsättningen. Den är inte begränsad till endast den tabell som har angetts som datauppsättningens tableName typeProperty.

Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName används kolumnerna som definieras i strukturavsnittet för att skapa en urvalsfråga som ska köras mot SQL Server-databasen. Om datauppsättningsdefinitionen inte har strukturen markeras alla kolumner i tabellen.

Se avsnittet [Sql Source](#sqlsource) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) för listan över egenskaper som stöds av SqlSource och BlobSink.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Exempel: Kopiera data från Azure Blob till SQL Server
Följande exempel visar:

1. Den länkade tjänsten av typen [OnPremisesSqlServer](#linked-service-properties).
2. Den länkade tjänsten av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En [indatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. En [utdatauppsättning](data-factory-create-datasets.md) av typen [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. [Pipelinen](data-factory-create-pipelines.md) med kopiera aktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och SqlSink.

Exemplet kopierar tidsseriedata från en Azure-blob till en SQL Server-tabell varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

**SQL Server-länkad tjänst**

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
**Azure Blob lagringslänkade tjänst**

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
**Indatauppsättning för Azure-blob**

Data hämtas från en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen och filnamnet för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder år, månad och dag del av starttiden och filnamnet använder timdelen av starttiden. "extern": "true"-inställningen informerar datafabrikstjänsten om att datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

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
**SQL Server-utdatauppsättning**

Exemplet kopierar data till en tabell med namnet "MyTable" i SQL Server. Skapa tabellen i SQL Server med samma antal kolumner som du förväntar dig att Blob CSV-filen ska innehålla. Nya rader läggs till i tabellen varje timme.

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
**Pipeline med kopieringsaktivitet**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda dessa in- och utdatauppsättningar och som är schemalagd att köras varje timme. I pipeline-JSON-definitionen anges **källtypen** till **BlobSource** och **sink-typen** är inställd på **SqlSink**.

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
1. Konfigurera SQL Server för att acceptera fjärranslutningar. Starta **SQL Server Management Studio**, högerklicka på **servern**och klicka på **Egenskaper**. Välj **Anslutningar** i listan och markera **Tillåt fjärranslutningar till servern**.

    ![Aktivera fjärranslutningar](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Mer information finns [i Konfigurera serverkonfigurationsalternativet](https://msdn.microsoft.com/library/ms191464.aspx) för fjärråtkomst för detaljerade steg.
2. Starta **SQL Server Configuration Manager**. Expandera **SQL Server-nätverkskonfigurationen** för den instans du vill använda och välj **Protokoll för MSSQLSERVER**. Du bör se protokoll i den högra rutan. Aktivera TCP/IP genom att högerklicka på **TCP/IP** och klicka på **Aktivera**.

    ![Aktivera TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Mer information och alternativa sätt att aktivera TCP/IP-protokoll finns i [Aktivera eller inaktivera ett servernätverksprotokoll.](https://msdn.microsoft.com/library/ms191294.aspx)
3. I samma fönster dubbelklickar du på **TCP/IP** för att starta **TCP/IP-egenskapsfönstret.**
4. Växla till fliken **IP-adresser.** **IPAll** Notera ner **TCP-porten**(standard är **1433).**
5. Skapa en **regel för Windows-brandväggen** på datorn för att tillåta inkommande trafik via den här porten.
6. **Verifiera anslutning:** Om du vill ansluta till SQL Server med fullständigt kvalificerat namn använder du SQL Server Management Studio från en annan dator. Till exempel:\<\>" maskin . \<domän\>\<.corp.\>företaget .com,1433."

   > [!IMPORTANT]
   > 
   > Mer information finns [i Flytta data mellan lokala källor och molnet med Data Management Gateway.](data-factory-move-data-between-onprem-and-cloud.md)
   > 
   > Se [Felsöka gatewayproblem](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) för tips om felsökning av anslutnings-/gatewayrelaterade problem.


## <a name="identity-columns-in-the-target-database"></a>Identitetskolumner i måldatabasen
Det här avsnittet innehåller ett exempel som kopierar data från en källtabell utan identitetskolumn till en måltabell med en identitetskolumn.

**Källtabell:**

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

**JSON-definition av källdatauppsättning**

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
**JSON-definition av måldatauppsättning**

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

Observera att eftersom käll- och måltabellen har ett annat schema (målet har ytterligare en kolumn med identitet). I det här fallet måste du ange **egenskapen struktur** i måldatauppsättningsdefinitionen, som inte innehåller identitetskolumnen.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Anropa lagrad procedur från SQL-diskho
Se [Anropa lagrad procedur för SQL-mottagare i copy-aktivitetsartikeln,](data-factory-invoke-stored-procedure-from-copy-activity.md) ett exempel på att anropa en lagrad procedur från SQL-diskbänk i en kopieringsaktivitet för en pipeline.

## <a name="type-mapping-for-sql-server"></a>Typmappning för SQL-server
Som nämns i artikeln [för dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) utför aktiviteten Kopiera automatiska typkonverteringar från källtyper till sinktyper med följande tvåstegsmetod:

1. Konvertera från ursprungliga källtyper till .NET-typ
2. Konvertera från .NET-typ till inbyggt handfattyp

När data flyttas till & från SQL-servern används följande mappningar från SQL-typ till .NET-typ och vice versa.

Mappningen är samma som SQL Server Data Type Mapping för ADO.NET.

| Sql Server-databasmotortyp | .NET-ramtyp |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |Sträng, Röding[] |
| date |DateTime |
| Datumtid |DateTime |
| datetime2 |DateTime |
| Datumtidsdatum |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM-attribut (varbinary(max)) |Byte[] |
| Float (Flyttal) |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |Sträng, Röding[] |
| ntext |Sträng, Röding[] |
| numeric |Decimal |
| nvarchar |Sträng, Röding[] |
| real |Enkel |
| Rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 (int16) |
| smallmoney |Decimal |
| Sql_variant |Objekt * |
| text |Sträng, Röding[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |Sträng, Röding[] |
| xml |Xml |

## <a name="mapping-source-to-sink-columns"></a>Mappa källa till sinkkolumner
Information om hur du mappar kolumner från källdatauppsättning till kolumner från sink-datauppsättning finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Repeterbar kopia
När data kopieras till SQL Server Database läggs data till i sink-tabellen som standard. Om du vill utföra en UPSERT i stället, Se [Repeterbar skriva till SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) artikel.

När du kopierar data från relationsdatalager bör du tänka på repeterbarhet för att undvika oavsiktliga resultat. I Azure Data Factory kan du köra ett segment manuellt igen. Du kan också konfigurera återförsöksprincipen för en datauppsättning så att ett segment körs igen när ett fel inträffar. När ett segment körs på något sätt måste du se till att samma data läss oavsett hur många gånger ett segment körs. Se [Repeterbar läsning från relationskällor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitetsprestanda & justeringsguide](data-factory-copy-activity-performance.md) om du vill veta mer om viktiga faktorer som påverkar prestanda för datarörelser (kopieringsaktivitet) i Azure Data Factory och olika sätt att optimera den.
