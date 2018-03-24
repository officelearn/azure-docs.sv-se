---
title: Kopiera data till/från Azure SQL Database | Microsoft Docs
description: Lär dig mer om att kopiera data till och från Azure SQL Database med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: bf47ad3f401abced4e74260dc9180413d69268c3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Kopiera data till och från Azure SQL Database med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-azure-sql-connector.md)
> * [Version 2 – förhandsversion](../connector-azure-sql-database.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [Azure SQL Database-anslutningen i V2](../connector-azure-sql-database.md).

Den här artikeln förklarar hur du använder aktiviteten kopiera i Azure Data Factory för att flytta data till och från Azure SQL Database. Den bygger på den [Data Movement aktiviteter](data-factory-data-movement-activities.md) artikel som presenterar en allmän översikt över dataflyttning med copy-aktivitet.  

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från Azure SQL Database** till följande data lagras:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager **till Azure SQL Database**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Autentiseringstypen som stöds
Azure SQL Database-anslutningen har stöd för grundläggande autentisering.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data till eller från en Azure SQL Database med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **guiden Kopiera**. Finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och **REST API**. Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. 

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare: 

1. Skapa en **datafabriken**. En datafabrik kan innehålla en eller flera pipelines. 
2. Skapa **länkade tjänster** att länka inkommande och utgående data lagras till din data factory. Om du kopierar data från ett Azure blob storage till en Azure SQL database, skapa till exempel två länkade tjänster för att länka ditt Azure storage-konto och Azure SQL database till din data factory. Länkad tjänstegenskaper som är specifika för Azure SQL Database, se [länkade tjänstegenskaper](#linked-service-properties) avsnitt. 
3. Skapa **datauppsättningar** att representera inkommande och utgående data för kopieringen. I det tidigare exemplet i det sista steget, skapar du en datauppsättning för att ange blob-behållaren och mappen som innehåller indata. Och du skapar en annan dataset för att ange SQL-tabellen i Azure SQL-databas som innehåller de data som kopieras från blob storage. Egenskaper för datamängd som är specifika för Azure Data Lake Store, se [egenskaper för datamängd](#dataset-properties) avsnitt.
4. Skapa en **pipeline** med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata. I exemplet ovan kan använda du BlobSource som en källa och SqlSink som en mottagare för kopieringsaktiviteten. På samma sätt om du kopierar från Azure SQL-databas till Azure Blob Storage, använder du SqlSource och BlobSink i kopieringsaktiviteten. Kopiera Aktivitetsegenskaper som är specifika för Azure SQL Database, se [kopiera Aktivitetsegenskaper](#copy-activity-properties) avsnitt. Mer information om hur du använder ett dataarkiv som en källa eller en mottagare klickar du på länken i föregående avsnitt för datalager.

När du använder guiden skapas JSON definitioner för dessa Data Factory-enheter (länkade tjänster, datauppsättningar och pipelinen) automatiskt för dig. När du använder Verktyg/API: er (utom .NET API), kan du definiera dessa Data Factory-enheter med hjälp av JSON-format.  Exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från en Azure SQL Database finns [JSON-exempel](#json-examples-for-copying-data-to-and-from-sql-database) i den här artikeln. 

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter till Azure SQL Database: 

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
En Azure SQL länkade tjänsten länkar en Azure SQL database till din data factory. Följande tabell innehåller en beskrivning för JSON-element som är specifika för Azure SQL-länkade tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen type måste anges till: **AzureSqlDatabase** |Ja |
| connectionString |Ange information som behövs för att ansluta till Azure SQL Database-instans för egenskapen connectionString. Endast grundläggande autentisering stöds. |Ja |

> [!IMPORTANT]
> Konfigurera [Azure SQL Database-brandvägg](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) i databasservern [ge Azure-tjänster åtkomst till servern](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Om du kopierar data till Azure SQL Database från utanför Azure inklusive från lokala datakällor med data factory-gateway måste du dessutom konfigurera rätt IP-adressintervall för den dator som skickar data till Azure SQL Database.

## <a name="dataset-properties"></a>Egenskaper för datamängd
Om du vill ange en datamängd som representerar inkommande eller utgående data i en Azure SQL database, anger du egenskapen typen på datamängden som ska: **AzureSqlTable**. Ange den **linkedServiceName** länkad egenskap på dataset till namnet på Azure SQL-tjänsten.  

En fullständig lista över egenskaper som är tillgängliga för att definiera datauppsättningarna & avsnitt finns i [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av dataset (Azure SQL Azure blob, Azure-tabellen, osv.).

Avsnittet typeProperties är olika för varje typ av dataset och ger information om placeringen av data i datalagret. Den **typeProperties** avsnittet för datauppsättningen av typen **AzureSqlTable** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på den tabell eller vy i Azure SQL Database-instans som den länkade tjänsten refererar till. |Ja |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapar Pipelines](data-factory-create-pipelines.md) artikel. Egenskaper som namn, beskrivning, ingående och utgående tabeller och principen är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopieringsaktiviteten tar endast en inmatning och ger en enda utdata.

Medan egenskaper som är tillgängliga i den **typeProperties** avsnitt i aktiviteten varierar med varje aktivitetstyp. För Kopieringsaktivitet kan variera de beroende på vilka typer av datakällor och sänkor.

Om du flyttar data från en Azure SQL database du ange typen av datakälla i kopieringsaktiviteten till **SqlSource**. På samma sätt om du flyttar data till en Azure SQL database, du anger sink i kopieringsaktiviteten till **SqlSink**. Det här avsnittet innehåller en lista över egenskaper som stöds av SqlSource och SqlSink.

### <a name="sqlsource"></a>SqlSource
I en Kopieringsaktivitet när källan är av typen **SqlSource**, följande egenskaper är tillgängliga i **typeProperties** avsnitt:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd anpassad fråga för att läsa data. |SQL-sträng. Exempel: `select * from MyTable`. |Nej |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. |Namnet på den lagrade proceduren. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och skiftläge parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |

Om den **sqlReaderQuery** har angetts för SqlSource, kopiera-aktivitet körs den här frågan mot Azure SQL Database-källa för att hämta data. Du kan också ange en lagrad procedur genom att ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren har parametrar).

Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName kolumner som har definierats i avsnittet strukturen i datauppsättningen JSON används för att skapa en fråga (`select column1, column2 from mytable`) att köra mot Azure SQL-databasen. Om datauppsättningsdefinitionen saknar strukturen, markeras alla kolumner från tabellen.

> [!NOTE]
> När du använder **sqlReaderStoredProcedureName**, du måste ange ett värde för den **tableName** egenskap i datauppsättningen JSON. Det finns inga verifieringar utföras om mot den här tabellen.
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

**Den lagrade proceduren definitionen:**

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
**SqlSink** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| writeBatchTimeout |Vänta tills batch insert-åtgärden ska slutföras innan tidsgränsen uppnås. |TimeSpan<br/><br/> Exempel ”: 00: 30:00” (30 minuter). |Nej |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize. |Heltal (antalet rader) |Nej (standard: 10000) |
| sqlWriterCleanupScript |Ange en fråga för Kopieringsaktiviteten att köra så att data för ett visst segment har rensats bort. Mer information finns i [repeterbara kopiera](#repeatable-copy). |En frågesats. |Nej |
| sliceIdentifierColumnName |Ange ett kolumnnamn för Kopieringsaktiviteten med genereras automatiskt segment-ID, som används för att rensa data för ett visst segment när köras på nytt. Mer information finns i [repeterbara kopiera](#repeatable-copy). |Kolumnnamnet för en kolumn med datatypen för binary(32). |Nej |
| sqlWriterStoredProcedureName |Namnet på den lagrade proceduren som definierar hur ska gälla källdata i måltabellen, t.ex. vill upserts eller transformera med hjälp av egna affärslogik. <br/><br/>Observera den här lagrade proceduren kommer att **anropas per batch**. Om du vill göra åtgärden som endast körs en gång och har ingenting att göra med källdata som t.ex. Ta bort/trunkera använder `sqlWriterCleanupScript` egenskapen. |Namnet på den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och skiftläge parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |
| sqlWriterTableType |Ange ett tabell-typnamn som ska användas i den lagrade proceduren. Kopieringsaktiviteten tillhandahåller data flyttas i en temporär tabell med den här tabellen. Lagrade procedurer kan sedan koppla data kopieras med befintliga data. |Ett namn för tabellen. |Nej |

#### <a name="sqlsink-example"></a>SqlSink-exempel

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
Följande exempel ger exempel JSON definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från Azure SQL Database och Azure Blob Storage. Dock datan kan kopieras **direkt** från alla källor till någon av sänkor anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av aktiviteten kopiera i Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Exempel: Kopieringsdata från Azure SQL-databas till Azure-Blob
Samma definierar följande Data Factory-enheter:

1. En länkad tjänst av typen [AzureSqlDatabase](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [dataset](data-factory-create-datasets.md) av typen [AzureSqlTable](#dataset-properties).
4. Utdata [dataset](data-factory-create-datasets.md) av typen [Azure Blob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en kopia-aktivitet som använder [SqlSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar time series-data (varje timme, varje dag, etc.) från en tabell i Azure SQL-databas till en blobb varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.  

**Azure SQL Database länkade tjänsten:**

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
Finns det [länkad Azure SQL-tjänst](#linked-service) avsnitt innehåller en lista över egenskaper som stöds av den här länkade tjänsten.

**Azure Blob storage länkade tjänsten:**

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
Finns det [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) artikel för listan över egenskaper som stöds av den här länkade tjänsten.


**Azure SQL inkommande datamängd:**

Exemplet förutsätter att du har skapat en tabell ”mytable” som prefix i Azure SQL och innehåller en kolumn med namnet ”timestampcolumn” för tid series-data.

Inställningen ”externa”: ”true” informerar Azure Data Factory-tjänsten att datamängden är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

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

Finns det [Azure SQL dataset Typegenskaper](#dataset) avsnittet innehåller en lista över egenskaper som stöds av den här typen av datauppsättning.  

**Azure Blob utdatauppsättningen:**

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Sökvägen till mappen för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad, dag och timmar delar av starttiden.

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
Finns det [Azure Blob dataset Typegenskaper](data-factory-azure-blob-connector.md#dataset-properties) avsnittet innehåller en lista över egenskaper som stöds av den här typen av datauppsättning.  

**Kopieringsaktiviteten i en pipeline med SQL-källa och mottagare för Blob:**

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **SqlSource** och **sink** är inställd på **BlobSink**. SQL-frågan som angetts för den **SqlReaderQuery** egenskapen väljer vilka data under den senaste timmen att kopiera.

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
I det här exemplet **sqlReaderQuery** har angetts för SqlSource. Kopieringsaktiviteten körs den här frågan mot Azure SQL Database-källa för att hämta data. Du kan också ange en lagrad procedur genom att ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren har parametrar).

Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName används kolumner som har definierats i avsnittet strukturen i datauppsättningen JSON för att skapa en fråga som körs mot Azure SQL-databasen. Till exempel: `select column1, column2 from mytable`. Om datauppsättningsdefinitionen saknar strukturen, markeras alla kolumner från tabellen.

Finns det [Sql-källans](#sqlsource) avsnitt och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) lista över egenskaper som stöds av SqlSource och BlobSink.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Exempel: Kopiera data från Azure Blob till Azure SQL Database
Exemplet definierar följande Data Factory-enheter:  

1. En länkad tjänst av typen [AzureSqlDatabase](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Utdata [dataset](data-factory-create-datasets.md) av typen [AzureSqlTable](#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopieringsaktiviteten som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och [SqlSink](#copy-activity-properties).

Exempel kopiorna tidsserier data (varje timme, varje dag, etc.) från Azure blob till en tabell i Azure SQL-databasen varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**Azure SQL länkade tjänsten:**

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
Finns det [länkad Azure SQL-tjänst](#linked-service) avsnitt innehåller en lista över egenskaper som stöds av den här länkade tjänsten.

**Azure Blob storage länkade tjänsten:**

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
Finns det [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) artikel för listan över egenskaper som stöds av den här länkade tjänsten.


**Azure Blob inkommande datamängd:**

Data hämtas från en ny blob varje timme (frekvens: timme, intervall: 1). Mappen sökvägen och filnamnet för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad och som en dagdel av starttiden filnamn används i timmen som en del av starttiden. ”externa”: ”true” inställningen informerar Data Factory-tjänsten att den här tabellen är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

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
Finns det [Azure Blob dataset Typegenskaper](data-factory-azure-blob-connector.md#dataset-properties) avsnittet innehåller en lista över egenskaper som stöds av den här typen av datauppsättning.

**Azure SQL Database utdatauppsättningen:**

Exemplet kopierar data till en tabell med namnet ”mytable” som prefix i SQL Azure. Skapa tabellen i Azure SQL med samma antal kolumner som du förväntar dig att Blob CSV-filen ska innehålla. Nya rader läggs till i tabell varje timme.

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
Finns det [Azure SQL dataset Typegenskaper](#dataset) avsnittet innehåller en lista över egenskaper som stöds av den här typen av datauppsättning.

**Kopieringsaktiviteten i en pipeline med Blob källa och mottagare för SQL:**

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **BlobSource** och **sink** är inställd på **SqlSink**.

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
Finns det [Sql Sink](#sqlsink) avsnitt och [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) lista över egenskaper som stöds av SqlSink och BlobSource.

## <a name="identity-columns-in-the-target-database"></a>Identitetskolumner i måldatabasen
Det här avsnittet innehåller ett exempel för att kopiera data från en källtabell utan en identity-kolumn till en måltabell med en identitetskolumn.

**Källtabellen:**

```SQL
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```
**Måltabellen:**

```SQL
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```
Observera att måltabellen har en identitetskolumn.

**Källa JSON-definitionen för datauppsättning**

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
**Mål JSON-definitionen för datauppsättning**

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

Observera att som käll- och tabellen har olika schema (målet har en ytterligare kolumn med identity). I det här scenariot måste du ange **struktur** egenskap i datauppsättningsdefinitionen mål som inte innehåller en identity-kolumn.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Anropa lagrade procedur från SQL-mottagare
Ett exempel för att anropa en lagrad procedur från SQL-mottagare i en Kopieringsaktivitet för en pipeline finns [anropa lagrad procedur för SQL-mottagare i en Kopieringsaktivitet](data-factory-invoke-stored-procedure-from-copy-activity.md) artikel. 

## <a name="type-mapping-for-azure-sql-database"></a>Mappning för Azure SQL Database
Som anges i den [data movement aktiviteter](data-factory-data-movement-activities.md) artikel kopieringsaktiviteten utför automatisk konverteringar från källtyper att registrera typer med följande metod i steg 2:

1. Konvertera från interna källtyper till .NET-typ
2. Konvertera från .NET-typ till interna mottagare typ.

När data flyttas till och från Azure SQL Database, används följande mappning från SQL-typen till .NET-typ och vice versa. Mappningen är densamma som SQL-Server-Datatypsmappningen för ADO.NET.

| SQL Server Database Engine-typ | .NET framework-typ |
| --- | --- |
| bigint |Int64 |
| Binär |Byte[] |
| bitar |Boolesk |
| Char |Sträng, Char] |
| datum |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM-attributet (varbinary(max)) |Byte[] |
| flyttal |Dubbel |
| Bild |Byte[] |
| int |Int32 |
| Money |Decimal |
| nchar |Sträng, Char] |
| ntext |Sträng, Char] |
| numeriskt |Decimal |
| nvarchar |Sträng, Char] |
| Verklig |Ogift |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Objektet * |
| Text |Sträng, Char] |
| time |TimeSpan |
| tidsstämpel |Byte[] |
| tinyint |Mottagna byte |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |Sträng, Char] |
| xml |Xml |

## <a name="map-source-to-sink-columns"></a>Karta källan till mottagare för kolumner
Mer information om mappning kolumner i datauppsättningen källan till kolumner i datauppsättning mottagare, se [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Repeterbara kopia
När du kopierar data till SQL Server-databas, lägger kopieringsaktiviteten data till tabellen mottagare som standard. För att genomföra en UPSERT i stället Se [Repeatable skriva till SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) artikel. 

Tänk på att undvika oväntade resultat repeterbarhet när kopiering av data från relationella data lagras. I Azure Data Factory, kan du köra en sektor manuellt. Du kan också konfigurera i principen för en dataset så att ett segment som körs när ett fel uppstår. När ett segment körs på något sätt, måste du kontrollera att samma data läses oavsett hur många gånger ett segment körs. Se [Repeatable läsa från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda och finjustering
Se [kopiera aktivitet prestanda och justera guiden](data-factory-copy-activity-performance.md) vill veta mer om viktiga faktorer som påverkan prestanda för flytt av data (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
