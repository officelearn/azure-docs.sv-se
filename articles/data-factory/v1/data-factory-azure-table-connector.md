---
title: Flytta data till/från Azure-tabell
description: Lär dig hur du flyttar data till/från Azure Table Storage med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 07b046b1-7884-4e57-a613-337292416319
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 462d54a9d89d6f03aed5e221fa02609da786c8c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260454"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Flytta data till och från Azure-tabell med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-azure-table-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-table-storage.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Azure Table Storage-anslutningsappen i V2](../connector-azure-table-storage.md).

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data till/från Azure Table Storage. Den bygger på [datarörelseaktiviteter](data-factory-data-movement-activities.md) artikeln, som presenterar en allmän översikt över data förflyttning med kopian aktivitet. 

Du kan kopiera data från alla källdatalagringar som stöds till Azure Table Storage eller från Azure Table Storage till alla sink-datalager som stöds. En lista över datalager som stöds som källor eller sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data till/från en Azure Table Storage med hjälp av olika verktyg/API:er.

Det enklaste sättet att skapa en pipeline är att använda **kopieringsguiden**. Se [självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång när du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-mall,** **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet. 

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager: 

1. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken.
2. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen. 
3. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. 

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet. För exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från en Azure Table Storage finns i avsnittet [JSON-exempel](#json-examples) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera datafabrikentiteter som är specifika för Azure Table Storage: 

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
Det finns två typer av länkade tjänster som du kan använda för att länka en Azure-bloblagring till en Azure-datafabrik. De är: **AzureStorage** länkad tjänst och **AzureStorageSas** länkade tjänst. Den Azure Storage-länkade tjänsten ger datafabriken global åtkomst till Azure Storage. Azure Storage SAS-tjänsten (Shared Access Signature) ger datafabriken begränsad/tidsbunden åtkomst till Azure Storage. Det finns inga andra skillnader mellan dessa två länkade tjänster. Välj den länkade tjänsten som passar dina behov. Följande avsnitt innehåller mer information om dessa två länkade tjänster.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper (Azure SQL, Azure blob, Azure-tabell osv.).

Avsnittet typeProperties är olika för varje typ av datauppsättning och ger information om platsen för data i datalagret. Avsnittet **typeProperties** för datauppsättningen av typen **AzureTable** har följande egenskaper.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i azure table database-instansen som länkade tjänsten refererar till. |Ja. När ett tabellnamn anges utan en azureTableSourceQuery kopieras alla poster från tabellen till målet. Om en azureTableSourceQuery också anges kopieras poster från tabellen som uppfyller frågan till målet. |

### <a name="schema-by-data-factory"></a>Schema efter datafabrik
För schemafria datalager som Azure Table härleder datafabrikstjänsten schemat på något av följande sätt:

1. Om du anger datastrukturen med hjälp av **egenskapen struktur** i datauppsättningsdefinitionen, respekterar datafabrikstjänsten den här strukturen som schema. I det här fallet, om en rad inte innehåller ett värde för en kolumn, anges ett null-värde för den.
2. Om du inte anger datastrukturen med hjälp av **egenskapen struktur** i datauppsättningsdefinitionen, drar Data Factory in schemat genom att använda den första raden i data. I det här fallet, om den första raden inte innehåller hela schemat, saknas vissa kolumner i resultatet av kopieringen.

För schemafria datakällor är därför bästa praxis att ange strukturen för data med hjälp av **egenskapen struktur.**

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata- och utdatauppsättningar och principer är tillgängliga för alla typer av aktiviteter.

Egenskaper som är tillgängliga i avsnittet typEgenskaper i aktiviteten å andra sidan varierar beroende på varje aktivitetstyp. För kopieringsaktivitet varierar de beroende på vilka typer av källor och sänkor som finns.

**AzureTableSource** stöder följande egenskaper i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| azureTableSourceQuery |Använd den anpassade frågan för att läsa data. |Azure-tabellfrågesträng. Se exempel i nästa avsnitt. |Nej. När ett tabellnamn anges utan en azureTableSourceQuery kopieras alla poster från tabellen till målet. Om en azureTableSourceQuery också anges kopieras poster från tabellen som uppfyller frågan till målet. |
| azureTableSourceIgnoreTableNotFound |Ange om det inte finns något undantag för tabellen. |TRUE<br/>FALSE |Inga |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery exempel
Om kolumnen Azure Table är av strängtyp:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Om kolumnen Azure Tabell är av datetime-typ:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** stöder följande egenskaper i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Standardpartitionsnyckelvärde som kan användas av diskhon. |Ett strängvärde. |Inga |
| azureTablePartitionKeyName |Ange namnet på den kolumn vars värden används som partitionsnycklar. Om inget anges används AzureTableDefaultPartitionKeyValue som partitionsnyckel. |Ett kolumnnamn. |Inga |
| azureTableRowKeyName |Ange namnet på den kolumn vars kolumnvärden används som radnyckel. Om inget anges använder du ett GUID för varje rad. |Ett kolumnnamn. |Inga |
| azureTableInsertType |Läget för att infoga data i Azure-tabellen.<br/><br/>Den här egenskapen styr om befintliga rader i utdatatabellen med matchande partitions- och radnycklar har sina värden ersatta eller sammanfogade. <br/><br/>Mer information om hur dessa inställningar (sammanfoga och ersätta) fungerar finns i [Infoga eller sammanfoga entitetsavsnitt](https://msdn.microsoft.com/library/azure/hh452241.aspx) och [Infoga eller ersätta entitetsavsnitt.](https://msdn.microsoft.com/library/azure/hh452242.aspx) <br/><br> Den här inställningen gäller på radnivå, inte tabellnivån, och inget av alternativen tar bort rader i utdatatabellen som inte finns i indata. |sammanfoga (standard)<br/>Ersätta |Inga |
| skriverBatchSize |Infogar data i Azure-tabellen när writeBatchSize eller writeBatchTimeout träffas. |Heltal (antal rader) |Nej (standard: 10000) |
| skriverBatchTimeout |Infogar data i Azure-tabellen när writeBatchSize eller writeBatchTimeout träffas |Gått<br/><br/>Exempel: "00:20:00" (20 minuter) |Nej (Standard för standardtidsgränsen för lagringsklient 90 sek) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Mappa en källkolumn till en målkolumn med egenskapen translator JSON innan du kan använda målkolumnen som azureTablePartitionKeyName.

I följande exempel mappas källkolumnen DivisionID till målkolumnen: DivisionID.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
DivisionID anges som partitionsnyckel.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>JSON exempel
Följande exempel innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från Azure Table Storage och Azure Blob Database. Data kan dock kopieras **direkt** från någon av källorna till någon av de diskhoar som stöds. Mer information finns i avsnittet "Datalager och format som stöds" i [Flytta data med hjälp av Kopiera aktivitet](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Exempel: Kopiera data från Azure-tabell till Azure Blob
Följande exempel visar:

1. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (används för båda & blob).
2. En [indatauppsättning](data-factory-create-datasets.md) av typen [AzureTable](#dataset-properties).
3. En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. [Pipelinen](data-factory-create-pipelines.md) med kopiera aktivitet som använder AzureTableSource och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data som tillhör standardpartitionen i en Azure-tabell till en blob varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

**Azure-lagringslänkade tjänst:**

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
Azure Data Factory stöder två typer av Azure Storage-länkade tjänster: **AzureStorage** och **AzureStorageSas**. För den första anger du anslutningssträngen som innehåller kontonyckeln och för den senare anger du SAS-uri (Shared Access Signature). Mer information finns i avsnittet [Länkade tjänster.](#linked-service-properties)  

**Azure Table indatauppsättning:**

Exemplet förutsätter att du har skapat en tabell "MyTable" i Azure Table.

Inställningen "extern": "true" informerar datafabrikstjänsten om att datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

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

**Azure Blob-utdatauppsättning:**

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder delar av starttiden för år, månad, dag och timmar.

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

**Kopiera aktivitet i en pipeline med AzureTableSource och BlobSink:**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I JSON-definitionen för pipelinen anges **källtypen** till **AzureTableSource** och **sink-typen** är inställd på **BlobSink**. SQL-frågan som anges med egenskapen **AzureTableSourceQuery** väljer data från standardpartitionen varje timme att kopiera.

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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Exempel: Kopiera data från Azure Blob till Azure-tabell
Följande exempel visar:

1. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (används för båda & blob)
2. En [indatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureTable](#dataset-properties).
4. [Pipelinen](data-factory-create-pipelines.md) med kopiera aktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och [AzureTableSink](#copy-activity-properties).

Exemplet kopierar tidsseriedata från en Azure-blob till en Azure-tabell varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

**Azure storage (för både Azure Table & Blob) länkad tjänst:**

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

Azure Data Factory stöder två typer av Azure Storage-länkade tjänster: **AzureStorage** och **AzureStorageSas**. För den första anger du anslutningssträngen som innehåller kontonyckeln och för den senare anger du SAS-uri (Shared Access Signature). Mer information finns i avsnittet [Länkade tjänster.](#linked-service-properties)

**Azure Blob-indatauppsättning:**

Data hämtas från en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen och filnamnet för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder år, månad och dag del av starttiden och filnamnet använder timdelen av starttiden. "extern": "true"-inställningen informerar datafabrikstjänsten om att datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

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

**Utdatauppsättning för Azure Table-utdata:**

Exemplet kopierar data till en tabell med namnet "MyTable" i Azure Table. Skapa en Azure-tabell med samma antal kolumner som du förväntar dig att Blob CSV-filen ska innehålla. Nya rader läggs till i tabellen varje timme.

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

**Kopiera aktivitet i en pipeline med BlobSource och AzureTableSink:**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I JSON-definitionen för pipelinen anges **källtypen** till **BlobSource** och **sink-typen** är inställd på **AzureTableSink**.

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
## <a name="type-mapping-for-azure-table"></a>Typmappning för Azure-tabell
Som nämns i artikeln [för dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) utför copy-aktivitet automatiska typkonverteringar från källtyper till sinktyper med följande tvåstegsmetod.

1. Konvertera från ursprungliga källtyper till .NET-typ
2. Konvertera från .NET-typ till inbyggt handfattyp

När du flyttar data till & från Azure Table används följande [mappningar som definieras av Azure Table-tjänsten](https://msdn.microsoft.com/library/azure/dd179338.aspx) från Azure Table OData-typer till .NET-typ och vice versa.

| OData-datatyp | .NET-typ | Information |
| --- | --- | --- |
| Edm.Binary (Edm.Binary) |byte[] |En matris med byte upp till 64 kB. |
| Edm.Boolean |bool |Ett booleskt värde. |
| Edm.DateTime |DateTime |Ett 64-bitarsvärde uttryckt som Coordinated Universal Time (UTC). DateTime-intervallet som stöds börjar från 12:00 midnatt den 1 januari 1601 e.Kr. (C.E.), UTC. Intervallet slutar den 31 december 9999. |
| Edm.Double |double |Ett 64-bitars flyttalsvärde. |
| Edm.Guid (edm.guid) |GUID |En 128-bitars globalt unik identifierare. |
| Edm.Int32 |Int32 |Ett 32-bitars heltal. |
| Edm.Int64 |Int64 |Ett 64-bitars heltal. |
| Edm.String |String |Ett UTF-16-kodat värde. Strängvärden kan vara upp till 64 kB. |

### <a name="type-conversion-sample"></a>Exempel på konvertering av typ
Följande exempel är för att kopiera data från en Azure Blob till Azure Table med typkonverteringar.

Anta att Blob-datauppsättningen är i CSV-format och innehåller tre kolumner. En av dem är en datetimekolumn med ett anpassat datetimeformat med förkortade franska namn för veckodag.

Definiera datauppsättningen Blob Source enligt följande tillsammans med typdefinitioner för kolumnerna.

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
Med tanke på typmappningen från Azure Table OData-typ till .NET-typ definierar du tabellen i Azure-tabellen med följande schema.

**Azure Tabell schema:**

| Kolumnnamn | Typ |
| --- | --- |
| userid |Edm.Int64 |
| namn |Edm.String |
| sistalogindate |Edm.DateTime |

Definiera sedan Azure Table-datauppsättningen enligt följande. Du behöver inte ange avsnittet "struktur" med typinformationen eftersom typinformationen redan har angetts i det underliggande datalagret.

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

I det här fallet skriver Data Factory automatiskt konverteringar inklusive datetime-fältet med det anpassade datetime-formatet med hjälp av "fr-fr"-kulturen när data flyttas från Blob till Azure-tabell.

> [!NOTE]
> Information om hur du mappar kolumner från källdatauppsättning till kolumner från sink-datauppsättning finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda och justering
Mer information om viktiga faktorer som påverkar prestanda för datarörelser (kopiera aktivitet) i Azure Data Factory och olika sätt att optimera den finns i [Kopiera aktivitetsprestanda & tuning guide](data-factory-copy-activity-performance.md).
