---
title: Kopiera data till och från Azure SQL Database | Microsoft Docs
description: Lär dig hur du kopierar data till och från Azure SQL Database med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 13e00acaf287a9e153aaa8e5ce7d630f8d198f02
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330423"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Kopieringsdata till och från Azure SQL Database med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-azure-sql-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-sql-database.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [Azure SQL Database-anslutningsprogrammet i V2](../connector-azure-sql-database.md).

Den här artikeln förklarar hur du använder Kopieringsaktivitet i Azure Data Factory för att flytta data till och från Azure SQL Database. Den bygger på den [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel som anger en allmän översikt över dataförflyttning med kopieringsaktiviteten.

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från Azure SQL Database** till följande data lagras:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager **till Azure SQL Database**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Autentiseringstyp som stöds
Azure SQL Database-anslutningsapp stöder grundläggande autentisering.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data till/från en Azure SQL Database med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **Kopieringsguiden**. Se [självstudien: Skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md) en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och  **REST-API**. Se [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er kan utföra du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager:

1. Skapa en **datafabrik**. En datafabrik kan innehålla en eller flera pipelines.
2. Skapa **länkade tjänster** länka inkommande och utgående data du lagrar till din datafabrik. Till exempel om du kopierar data från Azure blob storage till en Azure SQL-databas skapa du två länkade tjänster för att länka ditt Azure storage-konto och Azure SQL-databas till datafabriken. Länkade tjänstegenskaper som är specifika för Azure SQL Database, se [länkade tjänstegenskaper](#linked-service-properties) avsnittet.
3. Skapa **datauppsättningar** som representerar inkommande och utgående data för kopieringen. I det tidigare exemplet i det sista steget, skapar du en datauppsättning för att ange blobbehållare och mapp som innehåller indata. Och skapar du en annan datauppsättning för att ange den SQL-tabellen i Azure SQL-databasen som innehåller de data som kopieras från blob storage. Egenskaper för datamängd som är specifika för Azure Data Lake Store, se [egenskaper för datamängd](#dataset-properties) avsnittet.
4. Skapa en **pipeline** med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. I exemplet som nämnts tidigare, använder du BlobSource som en källa och SqlSink som mottagare för kopieringsaktiviteten. På samma sätt, om du kopierar från Azure SQL Database till Azure Blob Storage, använder du SqlSource och BlobSink i kopieringsaktiviteten. Kopiera Aktivitetsegenskaper som är specifika för Azure SQL Database, se [kopiera Aktivitetsegenskaper](#copy-activity-properties) avsnittet. Mer information om hur du använder ett datalager som en källa eller mottagare klickar du på länken i föregående avsnitt för ditt datalager.

När du använder guiden skapas JSON-definitioner för dessa Data Factory-entiteter (länkade tjänster, datauppsättningar och pipeline) automatiskt åt dig. När du använder Verktyg/API: er (med undantag för .NET-API) kan definiera du dessa Data Factory-entiteter med hjälp av JSON-format. Exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från en Azure SQL Database finns [JSON-exempel](#json-examples-for-copying-data-to-and-from-sql-database) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för Azure SQL Database:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
En länkad Azure SQL-tjänsten länkar en Azure SQL-databas till datafabriken. Följande tabell innehåller en beskrivning för JSON-element som är specifika för Azure SQL-länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Type-egenskapen måste anges till: **AzureSqlDatabase** |Ja |
| connectionString |Ange information som behövs för att ansluta till Azure SQL Database-instans för connectionString-egenskapen. Endast grundläggande autentisering stöds. |Ja |

> [!IMPORTANT]
> Konfigurera [Azure SQL Database-brandvägg](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) databasserver och [ge Azure-tjänster åtkomst till servern](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Om du kopierar data till Azure SQL Database från utanför Azure, inklusive från lokala datakällor med data factory gateway måste du dessutom konfigurera lämplig IP-adressintervall för den dator som skickar data till Azure SQL Database.

## <a name="dataset-properties"></a>Egenskaper för datamängd
Om du vill ange en datauppsättning som representerar inkommande eller utgående data i en Azure SQL-databas, kan du ange egenskapen type på datauppsättningen till: **AzureSqlTable**. Ange den **linkedServiceName** egenskap med namnet på Azure SQL-länkad tjänst.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [skapar datauppsättningar](data-factory-create-datasets.md) artikeln. Avsnitt som struktur, tillgänglighet och princip av en datauppsättnings-JSON är liknande för alla datauppsättningstyper av (Azure SQL, Azure-blob, Azure-tabell osv.).

Avsnittet typeProperties är olika för varje typ av datauppsättning och tillhandahåller information om platsen för data i datalagret. Den **typeProperties** avsnittet för datauppsättningen av typen **AzureSqlTable** har följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen eller vyn i Azure SQL Database-instans som den länkade tjänsten refererar till. |Ja |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [skapa Pipelines](data-factory-create-pipelines.md) artikeln. Egenskaper, till exempel namn, beskrivning, indata och utdata tabeller och principen är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopieringsaktivitet tar bara en inmatning och ger endast en utdata.

Å andra sidan Egenskaper som är tillgängliga i den **typeProperties** avsnittet aktivitetens varierar med varje aktivitetstyp av. För kopieringsaktiviteten variera de beroende på vilka typer av källor och mottagare.

Om du flyttar data från en Azure SQL-databas måste du ange källtypen i kopieringsaktiviteten till **SqlSource**. På samma sätt om du flyttar data till en Azure SQL-databas måste du ange Mottagartyp i kopieringsaktiviteten till **SqlSink**. Det här avsnittet innehåller en lista över egenskaper som stöds av SqlSource och SqlSink.

### <a name="sqlsource"></a>SqlSource
I kopieringsaktiviteten när källan är av typen **SqlSource**, följande egenskaper är tillgängliga i **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd anpassad fråga för att läsa data. |SQL-sträng. Exempel: `select * from MyTable`. |Nej |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. |Namnet på den lagrade proceduren. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |

Om den **sqlReaderQuery** har angetts för SqlSource, Kopieringsaktivitet körs den här frågan mot Azure SQL Database-källan för att hämta data. Du kan också ange en lagrad procedur genom att ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar).

Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName de kolumner som definierats i avsnittet strukturen i datauppsättningen JSON används för att skapa en fråga (`select column1, column2 from mytable`) ska köras på Azure SQL-databasen. Om definitionen för datauppsättningen inte har strukturen, markeras alla kolumner från tabellen.

> [!NOTE]
> När du använder **sqlReaderStoredProcedureName**, behöver du fortfarande ange ett värde för den **tableName** egenskap i JSON-datauppsättningen. Det finns inga verifieringar utförs men mot den här tabellen.
>
>

### <a name="sqlsource-example"></a>SqlSource exempel

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

**Definitionen av lagrade proceduren:**

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

### <a name="sqlsink"></a>SqlSink
**SqlSink** har stöd för följande egenskaper:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| writeBatchTimeout |Väntetid för batch insert-åtgärden ska slutföras innan tidsgränsen uppnås. |Tidsintervall<br/><br/> Exempel: ”00: 30:00” (30 minuter). |Nej |
| WriteBatchSize |Infogar data i SQL-tabell när buffertstorleken når writeBatchSize. |Heltal (antal rader) |Nej (standard: 10000) |
| sqlWriterCleanupScript |Ange en fråga för Kopieringsaktiviteten till att köra så att data för en viss sektor rensas. Mer information finns i [upprepningsbara kopiera](#repeatable-copy). |Ett frågeuttryck. |Nej |
| sliceIdentifierColumnName |Ange ett kolumnnamn för Kopieringsaktiviteten vill fylla med automatiskt genererad sektorn identifierare som används för att rensa data för en viss sektor när köras på nytt. Mer information finns i [upprepningsbara kopiera](#repeatable-copy). |Kolumnnamnet på en kolumn med datatypen för binary(32). |Nej |
| sqlWriterStoredProcedureName |Namnet på den lagrade proceduren som definierar hur du använder källdata i måltabellen, t.ex. att göra upsertar eller transformering med egen affärslogik. <br/><br/>Observera att den här lagrade proceduren kommer att **anropas per batch**. Om du vill göra åtgärd som endast körs en gång och har inget att göra med källdata, t.ex. Ta bort/trunkera, Använd `sqlWriterCleanupScript` egenskapen. |Namnet på den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |
| sqlWriterTableType |Ange ett tabellnamn typ som ska användas i den lagrade proceduren. Kopieringsaktivitet tillhandahåller data som flyttas i en temporär tabell med den här tabellen. Lagrad procedur kod kan sedan sammanfoga data kopieras med befintliga data. |Ett namn för tabellen. |Nej |

#### <a name="sqlsink-example"></a>SqlSink example

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

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>JSON-exempel för att kopiera data till och från SQL-databas
I följande exempel får exempel JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från Azure SQL Database och Azure Blob Storage. Men du kan kopiera data **direkt** från källor till någon av de mottagare som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) använda Kopieringsaktivitet i Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Exempel: Kopiera data från Azure SQL Database till Azure Blob
Samma definierar följande Data Factory-entiteter:

1. En länkad tjänst av typen [AzureSqlDatabase](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [datauppsättning](data-factory-create-datasets.md) av typen [AzureSqlTable](#dataset-properties).
4. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [Azure Blob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [SqlSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar time series-data (varje timme, varje dag, osv) från en tabell i Azure SQL-databas till en blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**Länkad Azure SQL Database-tjänsten:**

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
Se den [länkad Azure SQL-tjänst](#linked-service) avsnittet finns en lista över egenskaper som stöds av den här länkade tjänsten.

**Azure Blob storage-länkade tjänst:**

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
Se den [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) artikeln finns en lista över egenskaper som stöds av den här länkade tjänsten.


**Indatauppsättning för Azure SQL:**

Exemplet förutsätter att du har skapat en tabell ”MyTable” i Azure SQL och den innehåller en kolumn med namnet ”timestampcolumn” för time series-data.

Ange ”external”: ”true” meddelar Azure Data Factory-tjänsten att datauppsättningen är extern till datafabriken och inte kommer från en aktivitet i data factory.

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

Se den [Typegenskaper för Azure SQL-datauppsättning](#dataset) avsnittet finns en lista över egenskaper som stöds av den här datauppsättningstypen.

**Utdatauppsättning för Azure Blob:**

Data skrivs till en ny blob varje timme (frequency: timme, intervall: 1). Sökvägen till mappen för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad, dag och timmar delar av starttiden.

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
Se den [Typegenskaper för Azure Blob-datauppsättning](data-factory-azure-blob-connector.md#dataset-properties) avsnittet finns en lista över egenskaper som stöds av den här datauppsättningstypen.

**En Kopieringsaktivitet i en pipeline med SQL-käll- och Blob-mottagare:**

Pipelinen innehåller en Kopieringsaktivitet som har konfigurerats för användning av in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **SqlSource** och **mottagare** är **BlobSink**. SQL-frågan som angetts för den **SqlReaderQuery** egenskapen väljer vilka data under den senaste timmen att kopiera.

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
I det här exemplet **sqlReaderQuery** har angetts för SqlSource. Kopieringsaktivitet körs den här frågan mot Azure SQL Database-källan för att hämta data. Du kan också ange en lagrad procedur genom att ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar).

Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName, används de kolumner som definierats i avsnittet strukturen i datauppsättnings-JSON för att skapa en fråga ska köras på Azure SQL-databasen. Till exempel: `select column1, column2 from mytable`. Om definitionen för datauppsättningen inte har strukturen, markeras alla kolumner från tabellen.

Se den [Sql-källans](#sqlsource) avsnittet och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) lista över egenskaper som stöds av SqlSource och BlobSink.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Exempel: Kopiera data från Azure-Blob till Azure SQL Database
Exemplet definierar följande Data Factory-entiteter:

1. En länkad tjänst av typen [AzureSqlDatabase](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureSqlTable](#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och [SqlSink](#copy-activity-properties).

Exempel-kopior time series-data (varje timme, varje dag, osv) från Azure-blobb till en tabell i Azure SQL-databas varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**Länkad Azure SQL-tjänst:**

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
Se den [länkad Azure SQL-tjänst](#linked-service) avsnittet finns en lista över egenskaper som stöds av den här länkade tjänsten.

**Azure Blob storage-länkade tjänst:**

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
Se den [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) artikeln finns en lista över egenskaper som stöds av den här länkade tjänsten.


**Indatauppsättning för Azure-Blobb:**

Data hämtas från en ny blob varje timme (frequency: timme, intervall: 1). Mappnamn för sökvägen och filnamnet för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad och dag för starttiden och filnamnet använder timme en del av starttiden. ”external”: ”true” inställningen informerar Data Factory-tjänsten att den här tabellen är extern till datafabriken och inte kommer från en aktivitet i data factory.

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
Se den [Typegenskaper för Azure Blob-datauppsättning](data-factory-azure-blob-connector.md#dataset-properties) avsnittet finns en lista över egenskaper som stöds av den här datauppsättningstypen.

**Utdatauppsättning för Azure SQL Database:**

Exemplet kopierar data till en tabell med namnet ”MyTable” i Azure SQL. Skapa tabell i Azure SQL med samma antal kolumner som du förväntar dig att Blob CSV-filen ska innehålla. Nya rader har lagts till i tabell varje timme.

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
Se den [Typegenskaper för Azure SQL-datauppsättning](#dataset) avsnittet finns en lista över egenskaper som stöds av den här datauppsättningstypen.

**En Kopieringsaktivitet i en pipeline med Blob-källan och SQL-mottagare:**

Pipelinen innehåller en Kopieringsaktivitet som har konfigurerats för användning av in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **BlobSource** och **mottagare** är **SqlSink**.

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
Se den [Sql mottagare](#sqlsink) avsnittet och [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) lista över egenskaper som stöds av SqlSink och BlobSource.

## <a name="identity-columns-in-the-target-database"></a>Identitetskolumner i måldatabasen
Det här avsnittet innehåller ett exempel för att kopiera data från en källa tabell utan en identity-kolumn till en måltabell med en identity-kolumn.

**Källtabellen:**

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

**Källa dataset JSON-definition**

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
**Mål dataset JSON-definition**

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

Observera att som din käll- och tabell har olika schema (målet har ytterligare en kolumn med identiteten). I det här scenariot måste du ange **struktur** -egenskapen i datauppsättningsdefinitionen mål som inte innehåller en identity-kolumn.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Anropa lagrade procedur från SQL-mottagare
Ett exempel på att anropa en lagrad procedur från SQL-mottagare i en Kopieringsaktivitet i en pipeline kan se [anropa lagrade proceduren för SQL-mottagaren i kopieringsaktiviteten](data-factory-invoke-stored-procedure-from-copy-activity.md) artikeln.

## <a name="type-mapping-for-azure-sql-database"></a>Mappning för Azure SQL Database
Som vi nämnde i den [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikeln Kopieringsaktivitet utför automatisk konverteringar från typer av datakällor till mottagare typer med följande metod i steg 2:

1. Konvertera från interna källtyper till .NET-typ
2. Konvertera från .NET-typ till interna mottagare

När du flyttar data till och från Azure SQL Database, används följande mappningar från SQL-typ till .NET-typ och vice versa. Mappningen är samma som SQL-Server-Datatypsmappningen för ADO.NET.

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

## <a name="map-source-to-sink-columns"></a>Kartkälla till kolumner för mottagare
Mer information om mappning av kolumner i datauppsättningen för källan till kolumner i datauppsättning för mottagare, se [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Upprepningsbara kopia
När du kopierar data till SQL Server-databasen, läggs kopieringsaktiviteten data till tabellen mottagare som standard. Om du vill utföra en UPSERT i stället Se [Repeatable skriva till SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) artikeln.

Kom ihåg att undvika oväntade resultat repeterbarhet när kopiera data från relationsdata lagras. I Azure Data Factory kan du köra en sektor manuellt. Du kan också konfigurera återförsöksprincipen för en datauppsättning så att en sektor som körs när ett fel uppstår. När ett segment ska köras på nytt på något sätt, måste du se till att samma data läses oavsett hur många gånger som en sektor körs. Se [Repeatable läsa från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se [kopiera aktivitet prestanda- och Justeringsguide](data-factory-copy-activity-performance.md) att lära dig om viktiga faktorer att påverka prestandan för dataförflyttning (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
