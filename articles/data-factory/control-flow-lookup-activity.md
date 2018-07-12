---
title: Lookup-aktiviteten i Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder lookup-aktiviteten för att leta upp ett värde från en extern källa. Dessa utdata kan vidare refereras av efterföljande aktiviteter.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: shlo
ms.openlocfilehash: 25ed439674fcf7136e29034eb97e0652ae9ba111
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38237840"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Lookup-aktiviteten i Azure Data Factory

Lookup-aktiviteten kan användas för att hämta en datauppsättning från någon av ADF-stöd-datakälla.  Det kan användas i följande scenario:
- Dynamiskt kan bestämma vilka objekt (filer, tabeller osv) ska användas i en efterföljande aktivitet i stället för att hårdkoda objektnamnet

Lookup-aktiviteten kan läsa och returnera innehållet i en konfigurationsfil, en konfigurationstabell eller resultatet av att utföra en fråga eller en lagrad procedur.  Utdata från Lookup-aktiviteten kan användas i en efterföljande kopia eller transformeringsaktivitet om det är en singleton-värde eller används i en ForEach-aktivitet om det är en matris med attribut.

## <a name="supported-capabilities"></a>Funktioner som stöds

Följande datakällor stöds för sökning. Det maximala antalet rader som kan returneras av Lookup-aktiviteten är **5000**, och upp till **2MB** i storlek. Och maximal varaktighet för Lookup-aktiviteten innan timeout är för närvarande en timme.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Syntax

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>Egenskaperna för anslutningstypen
Namn | Beskrivning | Typ | Krävs?
---- | ----------- | ---- | --------
datauppsättning | Innehåller datauppsättningen-referens för sökningen. Hämta information från avsnittet ”Egenskaper för datamängd” i varje motsvarande connector-artikel. | Nyckel/värde-par | Ja
källa | Innehåller egenskaper för datamängd-specifika datakälla, samma som kopieringskälla för aktiviteten. Hämta information från avsnittet ”Kopiera Aktivitetsegenskaper” i varje motsvarande connector-artikel. | Nyckel/värde-par | Ja
firstRowOnly | Anger om du vill returnera endast den första raden eller alla rader. | Boolesk | Nej. Standardvärdet är `true`.

**Observera följande:**

1. Källkolumnen med ByteArray typ stöds inte.
2. Strukturen stöds inte i definitionen av datauppsättningen. För textfiler mer specifikt kan använda du rubrikraden för att ange ett kolumnnamn.
3. Om din sökning-källan är en JSON-filerna i `jsonPathDefinition` inställningen för nytt forma JSON-objekt inte stöds för hela objekt hämtas.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Använd lookup-aktivitet resultatet i en efterföljande aktivitet

Lookup resultatet returneras i de `output` delen av den aktiviteten.

* **När `firstRowOnly` är inställd på `true` (standard)**, utdataformat är enligt följande kod. Lookup-resultatet är under en fast `firstRow` nyckel. Om du vill använda resultatet i efterföljande aktivitet använder du mönstret för `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **När `firstRowOnly` är inställd på `false`** , utdataformat är enligt följande kod. En `count` fältet anger hur många poster som returneras och detaljerad värden visas under en fast `value` matris. I detta fall är sökningsaktiviteten vanligtvis åtföljs av en [Foreach-aktiviteten](control-flow-for-each-activity.md). Du kan skicka den `value` -matris på ForEach-aktiviteten `items` fältet genom att använda mönstret för `@activity('MyLookupActivity').output.value`. Åtkomst-element i den `value` kan använda följande syntax: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Här är ett exempel: `@{activity('lookupActivity').output.value[0].tablename}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>Exempel
I det här exemplet kopierar kopieringsaktiviteten data från en SQL-tabell i din Azure SQL Database-instans till Azure Blob storage. Namnet på den SQL-tabellen lagras i en JSON-fil i Blob storage. Lookup-aktiviteten hämtar tabellnamnet vid körning. På så sätt kan JSON som ska ändras dynamiskt utan att behöva distribuera om rörledningar eller datauppsättningar. 

Det här exemplet visar sökning efter den första raden. Sökningar för alla rader och att länka resultaten med ForEach-aktiviteten finns i exemplen i [kopiera flera tabeller i grupp med hjälp av Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Pipeline
Innehåller två aktiviteter i pipelinen: *lookup* och *kopiera*. 

- Lookup-aktiviteten har konfigurerats för att använda LookupDataset som refererar till en plats i Azure Blob storage. Lookup-aktiviteten läser namnet på den SQL-tabellen från en JSON-fil på den här platsen. 
- Kopieringsaktivitet använder utdata från lookup-aktiviteten (namnet på den SQL-tabellen). Egenskapen tableName i datauppsättningen för källan (SourceDataset) är konfigurerad för att använda utdata från lookup-aktiviteten. Kopieringsaktiviteten kopierar data från SQL-tabell till en plats i Azure Blob storage som anges av egenskapen SinkDataset. 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>Lookup-datauppsättning
Lookup-datauppsättning refererar till den *sourcetable.json* filen i mappen Azure Storage-sökning som anges av typen AzureStorageLinkedService. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-the-copy-activity"></a>Källdatauppsättning för kopieringsaktiviteten
Datauppsättningen för källan använder utdata från den lookup-aktiviteten är namnet på den SQL-tabellen. Kopieringsaktiviteten kopierar data från den här SQL-tabell till en plats i Azure Blob storage som anges av datauppsättningen för mottagaren. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>Datauppsättning för kopieringsaktiviteten för mottagare
Kopieringsaktiviteten kopierar data från SQL-tabell till den *filebylookup.csv* fil i den *csv* mapp i Azure storage som anges av egenskapen AzureStorageLinkedService. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst
Det här lagringskontot innehåller JSON-fil med namnet på SQL-tabeller. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Länkad Azure SQL Database-tjänst
Den här Azure SQL Database-instansen innehåller data som ska kopieras till Blob storage. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>SourceTable.JSON

#### <a name="set-of-objects"></a>Uppsättning objekt

```json
{
  "Id": "1",
  "tableName": "Table1"
}
{
   "Id": "2",
  "tableName": "Table2"
}
```

#### <a name="array-of-objects"></a>Matris med objekt

```json
[ 
    {
        "Id": "1",
        "tableName": "Table1"
    },
    {
        "Id": "2",
        "tableName": "Table2"
    }
]
```

## <a name="next-steps"></a>Nästa steg
Se andra kontrollflödesaktiviteter som stöds av Data Factory: 

- [Kör Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [GetMetaData-aktivitet](control-flow-get-metadata-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
