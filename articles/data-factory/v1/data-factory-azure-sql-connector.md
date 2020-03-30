---
title: Kopiera data till/från Azure SQL Database
description: Lär dig hur du kopierar data till/från Azure SQL Database med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7fc0b2822195d952c2a4f9c02bf3758c0e2b809a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260506"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Kopiera data till och från Azure SQL Database med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-azure-sql-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-sql-database.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Azure SQL Database-anslutningsappen i V2](../connector-azure-sql-database.md).

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data till och från Azure SQL Database. Den bygger på [datarörelseaktiviteter](data-factory-data-movement-activities.md) artikeln, som presenterar en allmän översikt över data förflyttning med kopian aktivitet.

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från Azure SQL Database** till följande datalager:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager **till Azure SQL Database:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Autentiseringstyp som stöds
Azure SQL Database-anslutningsappen stöder grundläggande autentisering.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data till/från en Azure SQL-databas med hjälp av olika verktyg/API:er.

Det enklaste sättet att skapa en pipeline är att använda **kopieringsguiden**. Se [självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång när du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-mall,** **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet.

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa en **datafabrik**. En datafabrik kan innehålla en eller flera pipelines.
2. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken. Om du till exempel kopierar data från en Azure-bloblagring till en Azure SQL-databas skapar du två länkade tjänster för att länka ditt Azure-lagringskonto och Azure SQL-databas till din datafabrik. För länkade tjänstegenskaper som är specifika för Azure SQL Database finns i avsnittet [länkade tjänstegenskaper.](#linked-service-properties)
3. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen. I exemplet som nämns i det sista steget skapar du en datauppsättning för att ange blob-behållaren och mappen som innehåller indata. Och du skapar en annan datauppsättning för att ange SQL-tabellen i Azure SQL-databasen som innehåller data som kopieras från blob-lagringen. För datauppsättningsegenskaper som är specifika för Azure Data Lake Store finns i avsnittet [egenskaper för datauppsättning.](#dataset-properties)
4. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. I det tidigare exemplet använder du BlobSource som källa och SqlSink som en mottagare för kopieringsaktiviteten. Om du kopierar från Azure SQL Database till Azure Blob Storage använder du sqlsource och blobSink i kopieringsaktiviteten. Information om egenskaper för kopieringsaktivitet som är specifika för Azure SQL Database finns i avsnittet [kopiera aktivitetsegenskaper.](#copy-activity-properties) Om du vill ha information om hur du använder ett datalager som källa eller en diskbänk klickar du på länken i föregående avsnitt för datalagret.

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet. För exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från en Azure SQL-databas finns i avsnittet [JSON-exempel](#json-examples-for-copying-data-to-and-from-sql-database) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för Azure SQL Database:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
En Azure SQL-länkad tjänst länkar en Azure SQL-databas till din datafabrik. Följande tabell innehåller en beskrivning av JSON-element som är specifika för Azure SQL-länkade tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type måste anges till: **AzureSqlDatabase** |Ja |
| Connectionstring |Ange information som behövs för att ansluta till Azure SQL Database-instansen för egenskapen connectionString. Endast grundläggande autentisering stöds. |Ja |

> [!IMPORTANT]
> Konfigurera [Azure SQL Database Firewall](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) databasservern så att Azure Services kan komma åt [servern](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Om du kopierar data till Azure SQL Database utanför Azure, inklusive från lokala datakällor med datafabriksgateway, konfigurerar du dessutom lämpligt IP-adressintervall för den dator som skickar data till Azure SQL Database.

## <a name="dataset-properties"></a>Egenskaper för datamängd
Om du vill ange en datauppsättning som ska representera indata eller utdata i en Azure SQL-databas anger du typegenskapen för datauppsättningen till: **AzureSqlTable**. Ange egenskapen **linkedServiceName** för datauppsättningen till namnet på azure SQL-länkade tjänsten.

En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper (Azure SQL, Azure blob, Azure-tabell osv.).

Avsnittet typeProperties är olika för varje typ av datauppsättning och ger information om platsen för data i datalagret. Avsnittet **typeProperties** för datauppsättningen av typen **AzureSqlTable** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen eller vyn i Azure SQL Database-instansen som länkade tjänsten refererar till. |Ja |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata- och utdatatabeller och princip är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopieringsaktiviteten tar bara en indata och producerar bara en utdata.

Medan egenskaper som är tillgängliga i avsnittet **typeProperties** i aktiviteten varierar med varje aktivitetstyp. För kopieringsaktivitet varierar de beroende på vilka typer av källor och sänkor som finns.

Om du flyttar data från en Azure SQL-databas anger du källtypen i kopieringsaktiviteten till **SqlSource**. Om du flyttar data till en Azure SQL-databas anger du på samma sätt sink-typen i kopieringsaktiviteten till **SqlSink**. Det här avsnittet innehåller en lista över egenskaper som stöds av SqlSource och SqlSink.

### <a name="sqlsource"></a>SqlSource (Påtkälla)
När källan är av typen **SqlSource**i kopieringsaktivitet är följande egenskaper tillgängliga i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempel: `select * from MyTable`. |Inga |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. |Namnet på den lagrade proceduren. Det senaste SQL-uttrycket måste vara en SELECT-sats i den lagrade proceduren. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värdepar. Namn och hölje av parametrar måste stämma överens med namn och hölje för de lagrade procedureparametrarna. |Inga |

Om **sqlReaderQuery** har angetts för SqlSource körs den här frågan mot Azure SQL Database-källan för att hämta data. Du kan också ange en lagrad procedur genom att ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar).

Om du inte anger antingen sqlReaderQuery eller sqlReaderStoredProcedureName används kolumnerna som definieras i strukturavsnittet`select column1, column2 from mytable`i datauppsättningen JSON för att skapa en fråga ( ) som ska köras mot Azure SQL Database. Om datauppsättningsdefinitionen inte har strukturen markeras alla kolumner i tabellen.

> [!NOTE]
> När du använder **sqlReaderStoredProcedureName**måste du fortfarande ange ett värde för **egenskapen tableName** i datauppsättningen JSON. Det finns inga valideringar som utförs mot den här tabellen dock.
>
>

### <a name="sqlsource-example"></a>SqlSource-exempel

```JSON
"source": {
    "type": "SqlSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```

**Den lagrade procedurens definition:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqlsink"></a>SqlSink (SqlSink)
**SqlSink** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| skriverBatchTimeout |Vänta på att batchinsatsen ska slutföras innan den har klarats upp. |Gått<br/><br/> Exempel: "00:30:00" (30 minuter). |Inga |
| skriverBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize. |Heltal (antal rader) |Nej (standard: 10000) |
| sqlWriterCleanupScript |Ange en fråga för Kopieringsaktivitet som ska köras så att data för ett visst segment rensas. Mer information finns i [repeterbar kopia](#repeatable-copy). |En frågesats. |Inga |
| sliceIdentifierColumnName |Ange ett kolumnnamn för Kopiera aktivitet som ska fyllas med automatisk genererad segmentidentifierare, som används för att rensa data från ett visst segment när du kör igen. Mer information finns i [repeterbar kopia](#repeatable-copy). |Kolumnnamnet på en kolumn med datatyp av binär(32). |Inga |
| sqlWriterStoredProcedureName |Namnet på den lagrade proceduren som definierar hur källdata ska användas i måltabellen, t.ex. <br/><br/>Observera att den här lagrade proceduren **anropas per batch**. Om du vill utföra åtgärder som bara körs en gång och inte har något att `sqlWriterCleanupScript` göra med källdata, t.ex. |Namnet på den lagrade proceduren. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värdepar. Namn och hölje av parametrar måste stämma överens med namn och hölje för de lagrade procedureparametrarna. |Inga |
| sqlWriterTableType |Ange ett tabelltypnamn som ska användas i den lagrade proceduren. Kopieringsaktivitet gör de data som flyttas tillgängliga i en temp-tabell med den här tabelltypen. Lagrad procedurkod kan sedan sammanfoga data som kopieras med befintliga data. |Ett tabelltypnamn. |Inga |

#### <a name="sqlsink-example"></a>Exempel på SqlSink

```JSON
"sink": {
    "type": "SqlSink",
    "writeBatchSize": 1000000,
    "writeBatchTimeout": "00:05:00",
    "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
    "sqlWriterTableType": "CopyTestTableType",
    "storedProcedureParameters": {
        "identifier": { "value": "1", "type": "Int" },
        "stringData": { "value": "str1" },
        "decimalData": { "value": "1", "type": "Decimal" }
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>JSON-exempel för kopiering av data till och från SQL Database
Följande exempel innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från Azure SQL Database och Azure Blob Storage. Data kan dock kopieras **direkt** från någon av källorna till någon av de diskhoar som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopieringsaktiviteten i Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Exempel: Kopiera data från Azure SQL Database till Azure Blob
Detsamma definierar följande datafabrikentiteter:

1. En länkad tjänst av typen [AzureSqlDatabase](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En [indatauppsättning](data-factory-create-datasets.md) av typen [AzureSqlTable](#dataset-properties).
4. En [utdatauppsättning](data-factory-create-datasets.md) av typen [Azure Blob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en kopieringsaktivitet som använder [SqlSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar tidsseriedata (per timme, dagligen osv.) från en tabell i Azure SQL-databas till en blob varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

**Azure SQL Database-länkad tjänst:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Se avsnittet Azure SQL Linked Service för listan över egenskaper som stöds av den här länkade tjänsten.

**Azure Blob storage-länkad tjänst:**

```JSON
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
Se azure [blob-artikeln](data-factory-azure-blob-connector.md#azure-storage-linked-service) för listan över egenskaper som stöds av den här länkade tjänsten.


**Azure SQL-indatauppsättning:**

Exemplet förutsätter att du har skapat en tabell "MyTable" i Azure SQL och den innehåller en kolumn som kallas "tidsstämpelpcolumn" för tidsseriedata.

Om du ställer in "extern": "true" informeras Azure Data Factory-tjänsten om att datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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

Se avsnittet Egenskaper för Azure SQL-datauppsättningstyp för listan över egenskaper som stöds av den här datauppsättningstypen.

**Azure Blob-utdatauppsättning:**

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder delar av starttiden för år, månad, dag och timmar.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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
Se avsnittet Egenskaper för [Azure Blob-datauppsättningstyp](data-factory-azure-blob-connector.md#dataset-properties) för listan över egenskaper som stöds av den här datauppsättningstypen.

**En kopieringsaktivitet i en pipeline med SQL-källa och Blob-mottagare:**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I pipeline-JSON-definitionen anges **källtypen** till **SqlSource** och **sink-typen** är inställd på **BlobSink**. Sql-frågan som angetts för egenskapen **SqlReaderQuery** väljer de data som ska kopieras under den senaste timmen.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
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
I exemplet anges **sqlReaderQuery** för SqlSource. Kopieringsaktiviteten kör den här frågan mot Azure SQL Database-källan för att hämta data. Du kan också ange en lagrad procedur genom att ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar).

Om du inte anger antingen sqlReaderQuery eller sqlReaderStoredProcedureName används kolumnerna som definieras i strukturavsnittet i datauppsättningen JSON för att skapa en fråga som ska köras mot Azure SQL Database. Till exempel: `select column1, column2 from mytable`. Om datauppsättningsdefinitionen inte har strukturen markeras alla kolumner i tabellen.

Se avsnittet [Sql Source](#sqlsource) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) för listan över egenskaper som stöds av SqlSource och BlobSink.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Exempel: Kopiera data från Azure Blob till Azure SQL Database
Exemplet definierar följande datafabrikentiteter:

1. En länkad tjänst av typen [AzureSqlDatabase](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En [indatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureSqlTable](#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopieringsaktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och [SqlSink](#copy-activity-properties).

Exemplet kopierar tidsseriedata (varje timme, dagligen osv.) från Azure-blob till en tabell i Azure SQL-databas varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

**Azure SQL-länkad tjänst:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Se avsnittet Azure SQL Linked Service för listan över egenskaper som stöds av den här länkade tjänsten.

**Azure Blob storage-länkad tjänst:**

```JSON
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
Se azure [blob-artikeln](data-factory-azure-blob-connector.md#azure-storage-linked-service) för listan över egenskaper som stöds av den här länkade tjänsten.


**Azure Blob-indatauppsättning:**

Data hämtas från en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen och filnamnet för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder år, månad och dag del av starttiden och filnamnet använder timdelen av starttiden. "extern": "true"-inställningen informerar datafabrikstjänsten om att den här tabellen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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
Se avsnittet Egenskaper för [Azure Blob-datauppsättningstyp](data-factory-azure-blob-connector.md#dataset-properties) för listan över egenskaper som stöds av den här datauppsättningstypen.

**Utdatauppsättning för Azure SQL Database:**

Exemplet kopierar data till en tabell med namnet "MyTable" i Azure SQL. Skapa tabellen i Azure SQL med samma antal kolumner som du förväntar dig att Blob CSV-filen ska innehålla. Nya rader läggs till i tabellen varje timme.

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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
Se avsnittet Egenskaper för Azure SQL-datauppsättningstyp för listan över egenskaper som stöds av den här datauppsättningstypen.

**En kopieringsaktivitet i en pipeline med Blob-källa och SQL-mottagare:**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I pipeline-JSON-definitionen anges **källtypen** till **BlobSource** och **sink-typen** är inställd på **SqlSink**.

```JSON
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
            "name": "AzureSqlOutput"
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
Se avsnittet [Sql Sink](#sqlsink) och [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) för listan över egenskaper som stöds av SqlSink och BlobSource.

## <a name="identity-columns-in-the-target-database"></a>Identitetskolumner i måldatabasen
Det här avsnittet innehåller ett exempel på hur du kopierar data från en källtabell utan identitetskolumn till en måltabell med en identitetskolumn.

**Källtabell:**

```SQL
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Måltabell:**

```SQL
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```
Observera att måltabellen har en identitetskolumn.

**JSON-definition av källdatauppsättning**

```JSON
{
    "name": "SampleSource",
    "properties": {
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

```JSON
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
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
Ett exempel på att anropa en lagrad procedur från SQL-mottagare i en kopieringsaktivitet för en pipeline finns [i Anropa lagrad procedur för SQL-mottagare i copy](data-factory-invoke-stored-procedure-from-copy-activity.md) activity-artikeln.

## <a name="type-mapping-for-azure-sql-database"></a>Typmappning för Azure SQL Database
Som nämnts i [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikeln Kopiera aktivitet utför automatiska typkonverteringar från källtyper till sinktyper med följande 2-stegsmetod:

1. Konvertera från ursprungliga källtyper till .NET-typ
2. Konvertera från .NET-typ till inbyggt handfattyp

När du flyttar data till och från Azure SQL Database används följande mappningar från SQL-typ till .NET-typ och vice versa. Mappningen är samma som SQL Server Data Type Mapping för ADO.NET.

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

## <a name="map-source-to-sink-columns"></a>Kartkälla för att sänka kolumner
Mer information om hur du mappar kolumner i källdatauppsättning till kolumner i sink-datauppsättning finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Repeterbar kopia
När data kopieras till SQL Server Database läggs data till i sink-tabellen som standard. Om du vill utföra en UPSERT i stället, Se [Repeterbar skriva till SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) artikel.

När du kopierar data från relationsdatalager bör du tänka på repeterbarhet för att undvika oavsiktliga resultat. I Azure Data Factory kan du köra ett segment manuellt igen. Du kan också konfigurera återförsöksprincipen för en datauppsättning så att ett segment körs igen när ett fel inträffar. När ett segment körs på något sätt måste du se till att samma data läss oavsett hur många gånger ett segment körs. Se [Repeterbar läsning från relationskällor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitetsprestanda & justeringsguide](data-factory-copy-activity-performance.md) om du vill veta mer om viktiga faktorer som påverkar prestanda för datarörelser (kopieringsaktivitet) i Azure Data Factory och olika sätt att optimera den.
