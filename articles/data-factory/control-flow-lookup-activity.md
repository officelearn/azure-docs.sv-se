---
title: Lookup-aktiviteten i Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder Lookup-aktiviteten för att leta upp ett värde från en extern källa. Dessa utdata kan vidare refereras av efterföljande aktiviteter.
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
ms.openlocfilehash: e437e7b7d5298af325ae2a5e2ba689b417bad022
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002928"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Lookup-aktiviteten i Azure Data Factory

Lookup-aktiviteten kan hämta en datauppsättning från någon av datakällorna som stöds av Azure Data Factory. Du kan använda den i följande scenario:
- Dynamiskt kan bestämma vilka objekt som ska användas i en efterföljande aktivitet i stället för att hårdkoda objektnamnet. Några exempel på objekt är filer och tabeller.

Lookup-aktiviteten läser och returnerar innehållet i en konfigurationsfil eller tabell. Den också returnerar resultatet av att utföra en fråga eller en lagrad procedur. Utdata från Lookup-aktiviteten kan användas i en efterföljande kopia eller transformeringsaktivitet om det är en singleton-värde. Utdata kan användas i en ForEach-aktivitet om det är en matris med attribut.

## <a name="supported-capabilities"></a>Funktioner som stöds

Följande datakällor stöds för Lookup-aktiviteten. Det största antalet rader som kan returneras av Lookup-aktiviteten är 5 000, upp till 2 MB i storlek. För närvarande den längsta varaktigheten för sökning aktivitet innan timeout är en timme.

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
Datauppsättning | Innehåller datauppsättningen-referens för sökningen. Få information från den **egenskaper för datamängd** i varje motsvarande connector-artikeln. | Nyckel/värde-par | Ja
källa | Innehåller egenskaper för datauppsättningen-specifika datakälla, samma som Kopieringsaktivitet källa. Få information från den **Kopieringsaktiviteten egenskaper** i varje motsvarande connector-artikeln. | Nyckel/värde-par | Ja
firstRowOnly | Anger om du vill returnera endast den första raden eller alla rader. | Boolesk | Nej. Standardvärdet är `true`.

> [!NOTE]

> * Käll-kolumner med **ByteArray** typ stöds inte.
> * **Strukturen** stöds inte i datauppsättningen definitioner. Använd rubrikraden för att ange kolumnnamnet för textformat filer.
> * Om din sökning-källan är en JSON-fil i `jsonPathDefinition` inställningen for Omforma JSON-objekt inte stöds. Hela objekt hämtas.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Använd Lookup-aktivitet resultatet i en efterföljande aktivitet

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

* **När `firstRowOnly` är inställd på `false`** , utdataformat är enligt följande kod. En `count` fältet anger hur många poster som returneras. Detaljerad värden visas under en fast `value` matris. I detta fall är sökningsaktiviteten följs av en [Foreach-aktiviteten](control-flow-for-each-activity.md). Du skickar den `value` -matris på ForEach-aktiviteten `items` fältet genom att använda mönstret för `@activity('MyLookupActivity').output.value`. Åtkomst-element i den `value` kan använda följande syntax: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Ett exempel är `@{activity('lookupActivity').output.value[0].tablename}`.

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

### <a name="copy-activity-example"></a>Kopiera aktivitet exempel
I det här exemplet kopierar Kopieringsaktiviteten data från en SQL-tabell i din Azure SQL Database-instans till Azure Blob storage. Namnet på den SQL-tabellen lagras i en JSON-fil i Blob storage. Lookup-aktiviteten hämtar tabellnamnet vid körning. JSON ändras dynamiskt med hjälp av den här metoden. Du behöver inte distribuera om rörledningar eller datauppsättningar. 

Det här exemplet visar sökning efter den första raden. Sökningar för alla rader och att länka resultaten med ForEach-aktiviteten finns i exemplen i [kopiera flera tabeller i grupp med hjälp av Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Pipeline
Innehåller två aktiviteter i pipelinen: sökning och kopiera. 

- Lookup-aktiviteten är konfigurerad för att använda **LookupDataset**, som refererar till en plats i Azure Blob storage. Lookup-aktiviteten läser namnet på den SQL-tabellen från en JSON-fil på den här platsen. 
- Kopieringsaktivitet använder utdata från den Lookup-aktiviteten är namnet på den SQL-tabellen. Den **tableName** -egenskapen i den **SourceDataset** är konfigurerad för att använda utdata från Lookup-aktiviteten. Kopiera aktivitet kopierar data från SQL-tabell till en plats i Azure Blob storage. Platsen anges av den **SinkDataset** egenskapen. 

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
Den **lookup** datauppsättningen är den **sourcetable.json** filen i mappen Azure Storage lookup anges av den **AzureStorageLinkedService** typen. 

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

### <a name="source-dataset-for-copy-activity"></a>**Källan** datauppsättning för Kopieringsaktivitet
Den **källa** datauppsättningen använder utdata från den Lookup-aktiviteten är namnet på den SQL-tabellen. Kopiera aktivitet kopierar data från den här SQL-tabell till en plats i Azure Blob storage. Platsen anges av den **mottagare** datauppsättning. 

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

### <a name="sink-dataset-for-copy-activity"></a>**Mottagare** datauppsättning för Kopieringsaktivitet
Kopieringsaktiviteten kopierar data från SQL-tabell till den **filebylookup.csv** fil i den **csv** mapp i Azure Storage. Filen har angetts av den **AzureStorageLinkedService** egenskapen. 

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
- [ForEach-aktivitet](control-flow-for-each-activity.md)
- [GetMetadata-aktiviteten](control-flow-get-metadata-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
