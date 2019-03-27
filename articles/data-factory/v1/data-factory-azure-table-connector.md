---
title: Flytta data till och från Azure Table | Microsoft Docs
description: Lär dig mer om att flytta data till och från Azure Table Storage med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 07b046b1-7884-4e57-a613-337292416319
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: aed341c50332b424a1149c129629cd451a4e5133
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500091"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Flytta data till och från Azure-tabell med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-azure-table-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-table-storage.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [Azure Table Storage connector i V2](../connector-azure-table-storage.md).

Den här artikeln förklarar hur du använder Kopieringsaktivitet i Azure Data Factory för att flytta data till och från Azure Table Storage. Den bygger på den [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel som anger en allmän översikt över dataförflyttning med kopieringsaktiviteten. 

Du kan kopiera data från alla dataarkiv till Azure Table Storage eller Azure Table Storage till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet finns i den [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data till/från en Azure Table Storage med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **Kopieringsguiden**. Se [självstudien: Skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md) en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och  **REST-API**. Se [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. 

Om du använder verktyg eller API: er kan utföra du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager: 

1. Skapa **länkade tjänster** länka inkommande och utgående data du lagrar till din datafabrik.
2. Skapa **datauppsättningar** som representerar inkommande och utgående data för kopieringen. 
3. Skapa en **pipeline** med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. 

När du använder guiden skapas JSON-definitioner för dessa Data Factory-entiteter (länkade tjänster, datauppsättningar och pipeline) automatiskt åt dig. När du använder Verktyg/API: er (med undantag för .NET-API) kan definiera du dessa Data Factory-entiteter med hjälp av JSON-format. Exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från en Azure Table Storage finns [JSON-exempel](#json-examples) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för Azure Table Storage: 

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
Det finns två typer av länkade tjänster som du kan använda för att länka ett Azure blob storage till en Azure-datafabrik. De är: **AzureStorage** länkad tjänst och **AzureStorageSas** länkad tjänst. Länkad Azure Storage-tjänsten tillhandahåller data factory med global åtkomst till Azure Storage. Medan det Azure Storage SAS (Shared Access Signature) länkad tillhandahåller service data factory med begränsade/Tidsbundna åtkomst till Azure Storage. Det finns inga andra skillnader mellan dessa två länkade tjänster. Välj den länkade tjänst som passar dina behov. Följande avsnitt innehåller mer information om dessa två länkade tjänster.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [skapar datauppsättningar](data-factory-create-datasets.md) artikeln. Avsnitt som struktur, tillgänglighet och princip av en datauppsättnings-JSON är liknande för alla datauppsättningstyper av (Azure SQL, Azure-blob, Azure-tabell osv.).

Avsnittet typeProperties är olika för varje typ av datauppsättning och tillhandahåller information om platsen för data i datalagret. Den **typeProperties** avsnittet för datauppsättningen av typen **AzureTable** har följande egenskaper.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Azure Table-databasinstansen som den länkade tjänsten refererar till. |Ja. När du anger ett tabellnamn utan en azureTableSourceQuery kopieras alla poster från tabellen till målet. Om en azureTableSourceQuery anges också kopieras poster från den tabell som uppfyller villkoren i frågan till målet. |

### <a name="schema-by-data-factory"></a>Schemat av Data Factory
För schemafria datalager som Azure Table härleder scheman i Data Factory-tjänsten på något av följande sätt:

1. Om du anger strukturen för data med hjälp av den **struktur** -egenskapen i definitionen för datauppsättningen, Data Factory-tjänsten godkänner den här strukturen som schema. I det här fallet ges en rad inte innehåller ett värde för en kolumn, ett null-värde för den.
2. Om du inte anger strukturen för data med hjälp av den **struktur** -egenskapen i definitionen för datauppsättningen, Data Factory härleder scheman med hjälp av den första raden i data. I det här fallet, om den första raden inte innehåller fullständig schemat är vissa kolumner missats i resultatet för kopieringsåtgärden.

Därför för schemafria datakällor, bästa praxis är att ange strukturen för data med hjälp av den **struktur** egenskapen.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [skapa Pipelines](data-factory-create-pipelines.md) artikeln. Egenskaper, till exempel namn, beskrivning, indata och utdata datauppsättningar och principer är tillgängliga för alla typer av aktiviteter.

Egenskaper som är tillgängliga i avsnittet typeProperties aktivitetens variera å andra sidan med varje aktivitetstyp av. För kopieringsaktiviteten variera de beroende på vilka typer av källor och mottagare.

**AzureTableSource** stöder följande egenskaper i avsnittet typeProperties:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| azureTableSourceQuery |Använd anpassad fråga för att läsa data. |Frågesträng för Azure-tabell. Se exemplen i nästa avsnitt. |Nej. När du anger ett tabellnamn utan en azureTableSourceQuery kopieras alla poster från tabellen till målet. Om en azureTableSourceQuery anges också kopieras poster från den tabell som uppfyller villkoren i frågan till målet. |
| azureTableSourceIgnoreTableNotFound |Indikera om det inte finns swallow undantag av tabellen. |SANT<br/>FALSKT |Nej |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery exempel
Om Azure Table-kolumnen är av typen string:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Om Azure Table-kolumn är av datetime-typ:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** stöder följande egenskaper i avsnittet typeProperties:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Standard partitionsnyckelvärde som kan användas av mottagaren. |Ett strängvärde. |Nej |
| azureTablePartitionKeyName |Ange namnet på kolumnen vars värden används som partitionsnycklar. Om den inte anges används AzureTableDefaultPartitionKeyValue som partitionsnyckel. |Ett kolumnnamn. |Nej |
| azureTableRowKeyName |Ange namnet på den kolumn vars kolumnvärdena används som radnyckel. Om inte anges kan du använda ett GUID för varje rad. |Ett kolumnnamn. |Nej |
| azureTableInsertType |Läget för att infoga data i Azure-tabell.<br/><br/>Den här egenskapen styr om befintliga rader i utdatatabellen med matchande partition och radnycklar har sina värden ersättas eller samman. <br/><br/>Läs om hur dessa inställningar (merge och Ersätt) fungerar i [Insert- eller Merge-entitet](https://msdn.microsoft.com/library/azure/hh452241.aspx) och [infoga eller ersätta entitet](https://msdn.microsoft.com/library/azure/hh452242.aspx) ämnen. <br/><br> Den här inställningen gäller på radnivå, inte på tabellnivå, och varken alternativet tar bort rader i utdatatabellen som inte finns i aktuella indata. |Sammanfoga (standard)<br/>Ersätt |Nej |
| WriteBatchSize |Infogar data i Azure-tabellen när writeBatchSize eller writeBatchTimeout uppnås. |Heltal (antal rader) |Nej (standard: 10000) |
| writeBatchTimeout |Infogar data i Azure-tabellen när writeBatchSize eller writeBatchTimeout namn |Tidsintervall<br/><br/>Exempel: ”00: 20:00” (20 minuter) |Nej (standard storage klienten standardvärdet för timeout-värdet 90 sek) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Mappa en källkolumn till en målkolumn med translator JSON-egenskap innan du kan använda målkolumnen som azureTablePartitionKeyName.

I följande exempel mappas källkolumnen DivisionID till målkolumnen: DivisionID.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
DivisionID har angetts som partitionsnyckel.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>JSON-exempel
I följande exempel får exempel JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från Azure Table Storage och Azure Blob-databas. Men du kan kopiera data **direkt** från källor till någon av de angivna egenskaperna. Mer information finns i avsnittet ”datalager som stöds och format” i [flytta data med hjälp av Kopieringsaktiviteten](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Exempel: Kopiera data från Azure Table till Azure Blob
I följande exempel visas:

1. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (används för både tabell- och blob).
2. Indata [datauppsättning](data-factory-create-datasets.md) av typen [AzureTable](#dataset-properties).
3. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Den [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder AzureTableSource och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data som hör till standardpartition i en Azure-tabell till en blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**Länkad Azure storage-tjänst:**

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
Azure Data Factory stöder två typer av Azure Storage-länkade tjänster: **AzureStorage** och **AzureStorageSas**. För den första som du anger anslutningssträngen som innehåller kontonyckeln och för det senare anger du Uri för signatur för delad åtkomst (SAS). Se [länkade tjänster](#linked-service-properties) information.  

**Azure Table datauppsättningen för indata:**

Exemplet förutsätter att du har skapat en tabell ”MyTable” i Azure Table.

Ange ”external”: ”true” informerar Data Factory-tjänsten att datauppsättningen är extern till datafabriken och inte kommer från en aktivitet i data factory.

```JSON
{
  "name": "AzureTableInput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

**Utdatauppsättning för Azure Blob:**

Data skrivs till en ny blob varje timme (frequency: timme, intervall: 1). Sökvägen till mappen för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad, dag och timmar delar av starttiden.

```JSON
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

**Kopieringsaktivitet i en pipeline med AzureTableSource och BlobSink:**

Pipelinen innehåller en Kopieringsaktivitet som har konfigurerats för användning av in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **AzureTableSource** och **mottagare** är **BlobSink**. SQL-frågan som angetts med **AzureTableSourceQuery** egenskapen väljer data från standardpartition varje timme för att kopiera.

```JSON
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureTabletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureTableInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureTableSource",
                        "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Exempel: Kopiera data från Azure Blob till Azure Table
I följande exempel visas:

1. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (används för både tabell- och blob)
2. Indata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureTable](#dataset-properties).
4. Den [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och [AzureTableSink](#copy-activity-properties).

Exempel-kopior time series-data från en Azure-blobb till en Azure tabellen varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**Länkad Azure storage (för både Azure Table och Blob)-tjänst:**

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

Azure Data Factory stöder två typer av Azure Storage-länkade tjänster: **AzureStorage** och **AzureStorageSas**. För den första som du anger anslutningssträngen som innehåller kontonyckeln och för det senare anger du Uri för signatur för delad åtkomst (SAS). Se [länkade tjänster](#linked-service-properties) information.

**Indatauppsättning för Azure-Blobb:**

Data hämtas från en ny blob varje timme (frequency: timme, intervall: 1). Mappnamn för sökvägen och filnamnet för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad och dag för starttiden och filnamnet använder timme en del av starttiden. ”external”: ”true” inställningen informerar Data Factory-tjänsten att datauppsättningen är extern till datafabriken och inte kommer från en aktivitet i data factory.

```JSON
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

**Utdatauppsättning för Azure-tabell:**

Exemplet kopierar data till en tabell med namnet ”MyTable” i Azure Table. Skapa en Azure-tabell med samma antal kolumner som du förväntar dig att Blob CSV-filen ska innehålla. Nya rader har lagts till i tabell varje timme.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

**Kopieringsaktivitet i en pipeline med BlobSource och AzureTableSink:**

Pipelinen innehåller en Kopieringsaktivitet som har konfigurerats för användning av in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **BlobSource** och **mottagare** är **AzureTableSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoTable",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureTableOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "AzureTableSink",
            "writeBatchSize": 100,
            "writeBatchTimeout": "01:00:00"
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
## <a name="type-mapping-for-azure-table"></a>Mappning för Azure-tabell
Som vi nämnde i den [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikeln kopieringsaktiviteten utför automatisk konverteringar från typer av datakällor till mottagare typer med följande metod i två steg.

1. Konvertera från interna källtyper till .NET-typ
2. Konvertera från .NET-typ till interna mottagare

När du flyttar data till och från Azure Table, följande [mappningar som definieras av Azure Table service](https://msdn.microsoft.com/library/azure/dd179338.aspx) som används från Azure Table OData-typer till .NET-typ och vice versa.

| OData-datatypen | .NET-typ | Information |
| --- | --- | --- |
| Edm.Binary |byte |En matris med byte upp till 64 KB. |
| Edm.Boolean |bool |Ett booleskt värde. |
| Edm.DateTime |DateTime |A 64-bit value expressed as Coordinated Universal Time (UTC). Det tillåtna intervallet för DateTime börjar från midnatt, 1 januari, 1601 e. kr. (C.E.), UTC. Intervallet som slutar på den 31 December 9999. |
| Edm.Double |double |Ett 64-bitars flytande punktvärde. |
| Edm.Guid |Guid |En globalt unik identifierare för 128-bitars. |
| Edm.Int32 |Int32 |En 32-bitars heltal. |
| Edm.Int64 |Int64 |En 64-bitars heltal. |
| Edm.String |String |Ett UTF-16-kodade värde. Strängvärden kan vara upp till 64 KB. |

### <a name="type-conversion-sample"></a>Konvertering-datatyp, exempel
I följande exempel är för att kopiera data från en Azure-Blob till Azure Table med typkonverteringar.

Anta att Blob-datauppsättningen är CSV-format och innehåller tre kolumner. En av dem är en datetime-kolumn med en anpassad datetime-format med hjälp av förkortade franska namnen för dag i veckan.

Definiera Blob källdatauppsättningen på följande sätt tillsammans med typdefinitioner för kolumner.

```JSON
{
    "name": " AzureBlobInput",
    "properties":
    {
        "structure":
        [
            { "name": "userid", "type": "Int64"},
            { "name": "name", "type": "String"},
            { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1,
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
Få typmappningen från Azure Table OData-typ till .NET-typ, definierar du tabellen i Azure-tabell med följande schema.

**Azure Table-schema:**

| Kolumnnamn | Type |
| --- | --- |
| userid |Edm.Int64 |
| namn |Edm.String |
| lastlogindate |Edm.DateTime |

Sedan definiera Azure Table-datauppsättningen. Du behöver inte ange ”struktur”-avsnittet med informationen eftersom informationen har redan angetts i det underliggande datalagringen.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

I det här fallet Data Factory Skriv automatiskt konverteringar inklusive Datetime-fält med den anpassade datetime-format med hjälp av kulturen ”fr-fr” när du flyttar data från Blob till Azure Table.

> [!NOTE]
> Om du vill mappa kolumner från datauppsättningen för källan till kolumner från en datauppsättning för mottagare, se [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Mer information om viktiga faktorer att påverka prestandan för dataförflyttning (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den finns [kopiera aktivitet prestanda- och Justeringsguide](data-factory-copy-activity-performance.md).
