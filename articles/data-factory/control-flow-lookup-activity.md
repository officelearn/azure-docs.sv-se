---
title: Sökning aktivitet i Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder sökning aktiviteten för att leta upp ett värde från en extern källa. Dessa utdata kan vidare refereras av efterföljande aktiviteter.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: shlo
ms.openlocfilehash: 18748aafa2b70d349f9914e2a8afc1c7477ca26e
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="lookup-activity-in-azure-data-factory"></a>Sökning aktivitet i Azure Data Factory

Sökning aktiviteten kan användas för att hämta en datamängd från någon av ADF-stöd för datakällan.  Det kan användas i följande scenario:
- Dynamiskt bestämma vilka objekt (filer, tabeller osv) att använda i en efterföljande aktivitet i stället för att hårdkoda objektnamnet

Sökning aktivitet kan läsa och returnera innehållet i en konfigurationsfil, konfigurationstabell eller resultatet av att utföra en fråga eller en lagrad procedur.  Utdata från aktiviteten för sökning kan användas i en efterföljande kopia eller omvandling aktivitet om det är en singleton-värde eller användas i en ForEach-aktivitet om det är en matris med attribut.

> [!NOTE]
> Den här artikeln gäller för version 2 av Azure Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen om Data Factory version 1](v1/data-factory-introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Följande datakällor stöds för närvarande för sökning:

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

Det maximala antalet rader som returneras av sökning aktiviteten är **5000**, och upp till **2MB** i storlek.

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

## <a name="type-properties"></a>Typegenskaper
Namn | Beskrivning | Typ | Krävs?
---- | ----------- | ---- | --------
DataSet | Ger dataset-referens för sökningen. Hämta information från avsnittet ”Egenskaper för datamängd” i varje motsvarande connector-artikel. | Nyckel/värde-par | Ja
källa | Innehåller egenskaper för dataset-specifik datakälla, samma som kopieringskälla för aktiviteten. Hämta information från avsnittet ”Kopiera Aktivitetsegenskaper” i varje motsvarande connector-artikel. | Nyckel/värde-par | Ja
firstRowOnly | Anger om du vill returnera endast den första raden eller alla rader. | Boolesk | Nej. Standardvärdet är `true`.

Observera följande punkter:

1. Källkolumnen med ByteArray typ stöds inte.
2. Strukturen stöds inte i definitionen för datamängden. För textfiler specifikt, kan du använda rubrikraden för att ange ett kolumnnamn.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Använda sökning aktivitet resultat i en efterföljande aktivitet

Sökning resultatet returneras den `output` avsnittet för aktiviteten kör resultat.

* **När `firstRowOnly` är inställd på `true` (standard)**, utdataformatet är som visas i följande kod. Sökning resultatet är enligt ett fast `firstRow` nyckel. Om du vill använda resultatet i efterföljande aktiviteten använder mönstret för `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **När `firstRowOnly` är inställd på `false`** , utdataformatet är som visas i följande kod. En `count` fältet anger hur många poster returneras och detaljerad värden visas under ett fast `value` matris. I sådana fall aktiviteten sökning vanligtvis följt av en [Foreach-aktiviteten](control-flow-for-each-activity.md). Du kan skicka den `value` matris för ForEach-aktiviteten `items` fältet med hjälp av mönstret för `@activity('MyLookupActivity').output.value`. Åtkomst-element i den `value` kan använda följande syntax: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Här är ett exempel: `@{activity('lookupActivity').output.value[0].tablename}`.

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
I det här exemplet kopierar kopieringsaktiviteten data från en SQLtabell i Azure SQL Database-instans till Azure Blob storage. Namnet på SQL-tabell lagras i en JSON-fil i Blob storage. Aktiviteten sökning letar upp tabellnamnet vid körning. Den här metoden möjliggör JSON ändras dynamiskt utan att behöva distribuera pipelines eller datauppsättningar. 

Det här exemplet visar sökning efter den första raden. Sökning för alla rader och kedja resultatet med ForEach-aktiviteten finns exempel i [kopiera flera tabeller i grupp med hjälp av Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Pipeline
Den här pipelinen innehåller två aktiviteter: *sökning* och *kopiera*. 

- Sökning aktiviteten är konfigurerad för att använda LookupDataset som refererar till en plats i Azure Blob storage. Aktiviteten sökning läser namnet på SQL-tabell från en JSON-fil på den här platsen. 
- Kopieringsaktiviteten använder utdata från aktiviteten sökning (namnet på SQL-tabell). Egenskapen tableName i källan datauppsättningen (SourceDataset) är konfigurerad för att använda utdata från aktiviteten sökning. Kopieringsaktiviteten kopierar data från SQL-tabell till en plats i Azure Blob storage som anges av egenskapen SinkDataset. 


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

### <a name="lookup-dataset"></a>Datamängd för uppslag
Sökning datamängden refererar till den *sourcetable.json* filen i mappen Azure Storage sökning som anges av typen AzureStorageLinkedService. 

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

### <a name="source-dataset-for-the-copy-activity"></a>Källan dataset för kopieringsaktiviteten
Käll-dataset använder utdata från aktiviteten sökning är namnet på SQL-tabellen. Kopieringsaktiviteten kopierar data från den här SQL-tabell till en plats i Azure Blob storage som anges av sink-datauppsättningen. 

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

### <a name="sink-dataset-for-the-copy-activity"></a>Sink dataset för kopieringsaktiviteten
Kopieringsaktiviteten kopierar data från SQL-tabellen till den *filebylookup.csv* filen i den *csv* mapp i Azure-lagring som anges av egenskapen AzureStorageLinkedService. 

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
  "tableName": "Table1",
}
{
   "Id": "2",
  "tableName": "Table2",
}
```

#### <a name="array-of-objects"></a>Matris med-objekt

```json
[ 
    {
        "Id": "1",
          "tableName": "Table1",
    }
    {
        "Id": "2",
        "tableName": "Table2",
    }
]
```

## <a name="next-steps"></a>Nästa steg
Se annan kontrollflödesaktiviteter som stöds av Data Factory: 

- [Köra Pipeline-aktiviteten](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [Hämta Metadata för aktiviteten](control-flow-get-metadata-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
