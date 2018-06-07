---
title: Flytta data till/från Azure Table | Microsoft Docs
description: Lär dig mer om att flytta data till och från Azure Table Storage med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 07b046b1-7884-4e57-a613-337292416319
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 996b1e5cbc477bf8a67a8cbb118961aaedf151fd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621514"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Flytta data till och från Azure-tabellen med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-azure-table-connector.md)
> * [Version 2 – förhandsversion](../connector-azure-table-storage.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [Azure Table Storage connector i V2](../connector-azure-table-storage.md).

Den här artikeln förklarar hur du använder aktiviteten kopiera i Azure Data Factory för att flytta data till och från Azure Table Storage. Den bygger på den [Data Movement aktiviteter](data-factory-data-movement-activities.md) artikel som presenterar en allmän översikt över dataflyttning med copy-aktivitet. 

Du kan kopiera data från alla datalager för stöds källan till Azure Table Storage eller Azure Table Storage till alla stöds sink-datalagret. En lista över datakällor som stöds som datakällor eller sänkor av kopieringsaktiviteten, finns det [stöds datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. 

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data till och från en Azure Table Storage med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **guiden Kopiera**. Finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och **REST API**. Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. 

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare: 

1. Skapa **länkade tjänster** att länka inkommande och utgående data lagras till din data factory.
2. Skapa **datauppsättningar** att representera inkommande och utgående data för kopieringen. 
3. Skapa en **pipeline** med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata. 

När du använder guiden skapas JSON definitioner för dessa Data Factory-enheter (länkade tjänster, datauppsättningar och pipelinen) automatiskt för dig. När du använder Verktyg/API: er (utom .NET API), kan du definiera dessa Data Factory-enheter med hjälp av JSON-format.  Exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från en Azure-tabellagring finns [JSON-exempel](#json-examples) i den här artikeln. 

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter till Azure Table Storage: 

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
Det finns två typer av länkade tjänster som du kan använda för att länka en Azure-blobblagring till ett Azure data factory. De är: **AzureStorage** länkade tjänsten och **AzureStorageSas** länkade tjänsten. Länkad Azure Storage-tjänsten tillhandahåller data factory med global åtkomst till Azure Storage. Medan det Azure Storage SAS (signatur för delad åtkomst) länkade ger tjänsten data factory med begränsad/Tidsbundna åtkomst till Azure Storage. Det finns några skillnader mellan dessa två länkade tjänster. Välj den länkade tjänst som passar dina behov. Följande avsnitt innehåller mer information om dessa två länkade tjänster.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över egenskaper som är tillgängliga för att definiera datauppsättningarna & avsnitt finns i [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av dataset (Azure SQL Azure blob, Azure-tabellen, osv.).

Avsnittet typeProperties är olika för varje typ av dataset och ger information om placeringen av data i datalagret. Den **typeProperties** avsnittet för datauppsättningen av typen **AzureTable** har följande egenskaper.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Azure Table databasinstansen som den länkade tjänsten refererar till. |Ja. När ett tabellnamn har angetts utan ett azureTableSourceQuery, kopieras alla poster från tabellen till målet. Om en azureTableSourceQuery också anges kopieras poster från tabellen som uppfyller frågan till målet. |

### <a name="schema-by-data-factory"></a>Schema som Data Factory
För schemafria data butiker, till exempel Azure Table härleder Data Factory-tjänsten schemat på något av följande sätt:

1. Om du anger strukturen för data med hjälp av den **struktur** egenskap i datauppsättningsdefinitionen Data Factory-tjänsten godkänner den här strukturen som schema. I det här fallet ges en rad inte innehåller ett värde för en kolumn, ett null-värde för den.
2. Om du inte anger strukturen för data med hjälp av den **struktur** egenskap i datauppsättningsdefinitionen Data Factory härleder schemat med hjälp av den första raden i data. Om den första raden inte innehåller fullständig schemat missas i det här fallet vissa kolumner i resultatet av kopieringsåtgärden.

Därför för schemafria datakällor, det bästa sättet är att ange hur dina data med hjälp av den **struktur** egenskapen.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapar Pipelines](data-factory-create-pipelines.md) artikel. Egenskaper som namn, beskrivning, indata och utdata-datauppsättningar och -principer är tillgängliga för alla typer av aktiviteter.

Egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar å andra sidan beroende på varje aktivitetstyp. För Kopieringsaktivitet kan variera de beroende på vilka typer av datakällor och sänkor.

**AzureTableSource** stöder följande egenskaper i typeProperties avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| azureTableSourceQuery |Använd anpassad fråga för att läsa data. |Azure-tabellen frågesträngen. Se exemplen i nästa avsnitt. |Nej. När ett tabellnamn har angetts utan ett azureTableSourceQuery, kopieras alla poster från tabellen till målet. Om en azureTableSourceQuery också anges kopieras poster från tabellen som uppfyller frågan till målet. |
| azureTableSourceIgnoreTableNotFound |Ange om swallow undantag av tabellen inte finns. |SANT<br/>FALSKT |Nej |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery-exempel
Om Azure Table kolumnen är av strängtypen:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Om Azure Table kolumnen är av typen datetime:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** stöder följande egenskaper i typeProperties avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Standard partitionsnyckelvärde som kan användas av sink. |Ett strängvärde. |Nej |
| azureTablePartitionKeyName |Ange namnet på den kolumn som används som partitionsnycklar. Om inget anges används AzureTableDefaultPartitionKeyValue som partitionsnyckel. |Ett kolumnnamn. |Nej |
| azureTableRowKeyName |Ange namnet på den kolumn vars kolumnvärdena används som radnyckel. Om inget annat anges, kan du använda ett GUID för varje rad. |Ett kolumnnamn. |Nej |
| azureTableInsertType |Läget att infoga data i Azure-tabellen.<br/><br/>Den här egenskapen anger om befintliga rader i utdatatabellen med matchande partition och radnycklar få sina värden bytas ut eller samman. <br/><br/>Läs om hur dessa inställningar (dokument och Ersätt) fungerar i [Insert- eller Merge-entiteten](https://msdn.microsoft.com/library/azure/hh452241.aspx) och [infoga eller ersätta entiteten](https://msdn.microsoft.com/library/azure/hh452242.aspx) avsnitt. <br/><br> Den här inställningen gäller på radnivå inte tabellnivån, varken alternativet tar bort rader i utdatatabellen som inte finns i indata. |sammanfoga (standard)<br/>Ersätt |Nej |
| writeBatchSize |Infogar data i Azure-tabellen när writeBatchSize eller writeBatchTimeout namn. |Heltal (antalet rader) |Nej (standard: 10000) |
| writeBatchTimeout |Infogar data i Azure-tabellen när writeBatchSize eller writeBatchTimeout namn |TimeSpan<br/><br/>Exempel ”: 00: 20:00” (20 minuter) |Nej (för lagring klienten standardtimeout standardvärdet 90 sek) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Mappa en källkolumn till en målkolumn med översättare JSON-egenskapen innan du kan använda målkolumnen som azureTablePartitionKeyName.

I följande exempel källkolumnen DivisionID mappas till målkolumnen: DivisionID.  

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
Följande exempel ger exempel JSON definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från Azure Table Storage och Azure Blob-databas. Dock datan kan kopieras **direkt** från någon av källorna till någon av de stöds egenskaperna. Mer information finns i avsnittet ”stöds datalager och format” i [flytta data med hjälp av Kopieringsaktiviteten](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Exempel: Kopiera data från Azure Table till Azure-Blob
I följande exempel visas:

1. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (används för blob & tabell).
2. Indata [dataset](data-factory-create-datasets.md) av typen [AzureTable](#dataset-properties).
3. Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Den [pipeline](data-factory-create-pipelines.md) med kopieringsaktiviteten som använder [AzureTableSource](#activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data som hör till standardpartition i en Azure-tabell till en blobb varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

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
Azure Data Factory stöder två typer av länkad Azure Storage services: **AzureStorage** och **AzureStorageSas**. För den första, anger du den anslutningssträng som innehåller nyckeln konto och du ska ange Uri för delad åtkomst signatur (SAS) för det senare. Se [länkade tjänster](#linked-service-properties) information.  

**Azure Table inkommande datamängd:**

Exemplet förutsätter att du har skapat en tabell ”mytable” som prefix i Azure Table.

Inställningen ”externa”: ”true” informerar Data Factory-tjänsten att datamängden är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

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

**Azure Blob utdatauppsättningen:**

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Sökvägen till mappen för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad, dag och timmar delar av starttiden.

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

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **AzureTableSource** och **sink** är inställd på **BlobSink**. SQL-frågan som anges med **AzureTableSourceQuery** egenskapen väljer vilka data från standardpartition varje timme för att kopiera.

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

1. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (används för blob & tabell)
2. Indata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. Utdata [dataset](data-factory-create-datasets.md) av typen [AzureTable](#dataset-properties).
4. Den [pipeline](data-factory-create-pipelines.md) med kopieringsaktiviteten som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och [AzureTableSink](#copy-activity-properties).

Exempel kopiorna tidsserier data från ett Azure blob till en Azure tabellen varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**Länkad Azure storage (för både Azure Table & Blob)-tjänst:**

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

Azure Data Factory stöder två typer av länkad Azure Storage services: **AzureStorage** och **AzureStorageSas**. För den första, anger du den anslutningssträng som innehåller nyckeln konto och du ska ange Uri för delad åtkomst signatur (SAS) för det senare. Se [länkade tjänster](#linked-service-properties) information.

**Azure Blob inkommande datamängd:**

Data hämtas från en ny blob varje timme (frekvens: timme, intervall: 1). Mappen sökvägen och filnamnet för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad och som en dagdel av starttiden filnamn används i timmen som en del av starttiden. ”externa”: ”true” inställningen informerar Data Factory-tjänsten att datamängden är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

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

**Azure-tabellen utdatauppsättningen:**

Exemplet kopierar data till en tabell med namnet ”mytable” som prefix i Azure Table. Skapa en Azure-tabellen med samma antal kolumner som du förväntar dig att Blob CSV-filen ska innehålla. Nya rader läggs till i tabell varje timme.

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

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **BlobSource** och **sink** är inställd på **AzureTableSink**.

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
## <a name="type-mapping-for-azure-table"></a>Mappning för Azure-tabellen
Som anges i den [data movement aktiviteter](data-factory-data-movement-activities.md) artikeln kopieringsaktiviteten utför automatisk konverteringar från källtyper att registrera typer med följande metod i två steg.

1. Konvertera från interna källtyper till .NET-typ
2. Konvertera från .NET-typ till interna mottagare typ.

När du flyttar data till och från Azure Table följande [mappningar som definierats av Azure Table-tjänsten](https://msdn.microsoft.com/library/azure/dd179338.aspx) som används från Azure Table OData-typer till .NET-typ och vice versa.

| OData-datatyp | .NET-typ | Information |
| --- | --- | --- |
| Edm.Binary |byte |En matris med byte upp till 64 KB. |
| Edm.Boolean |bool |Ett booleskt värde. |
| Edm.DateTime |DateTime |En 64-bitars värdet uttrycks som Coordinated Universal Time (UTC). Det intervall som stöds för den DateTime som börjar från midnatt, 1 januari, 1601 e. kr. (C.E.) UTC. Intervallet slutar vid den 31 December 9999. |
| Edm.Double |double |En 64-bitars flytande punktvärdet. |
| Edm.Guid |GUID |En 128-bitars globalt unik identifierare. |
| Edm.Int32 |Int32 |En 32-bitars heltal. |
| Edm.Int64 |Int64 |En 64-bitars heltal. |
| Edm.String |Sträng |Ett värde för UTF-16-kodad. Strängvärden kan vara upp till 64 KB. |

### <a name="type-conversion-sample"></a>Exempel konvertering
I följande exempel är för att kopiera data från en Azure-Blob till Azure Table med typkonverteringar.

Anta att Blob-dataset i CSV-format och innehåller tre kolumner. En av dem är ett datetime-kolumn med en anpassad datetime-format med hjälp av förkortade franska namn för dag i veckan.

Definiera datauppsättningen Blob källa på följande sätt tillsammans med typdefinitioner för kolumner.

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
Få typmappningen från Azure Table OData-typ till .NET-typ, definierar du tabellen i Azure-tabellen med följande schema.

**Azure tabellschemat:**

| Kolumnnamn | Typ |
| --- | --- |
| användar-ID |Edm.Int64 |
| namn |Edm.String |
| lastlogindate |Edm.DateTime |

Därefter definiera Azure Table-datauppsättningen. Du behöver inte ange ”struktur” avsnittet med informationen eftersom informationen redan har angetts i den underliggande datalagringen.

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

I det här fallet Data Factory Skriv automatiskt konverteringar inklusive Datetime-fält med anpassade datetime-format med hjälp av ”fr-fr”-kulturen vid flytt av data från Blob till Azure Table.

> [!NOTE]
> Om du vill mappa kolumner från källan dataset till kolumner från sink dataset finns [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda och finjustering
Mer information om viktiga faktorer som påverkan prestanda för flytt av data (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera det finns [kopiera aktivitet prestanda och justera guiden](data-factory-copy-activity-performance.md).
