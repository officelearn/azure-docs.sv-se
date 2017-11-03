---
title: "Sökning aktivitet i Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du använder aktiviteten sökning för att söka efter ett värde från en extern källa. Dessa utdata kan vidare refereras av efterföljande aktiviteter."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: shlo
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: spelluru
ms.openlocfilehash: 30173f8eea2ccbbcd44018596cf34b3769a64b50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="lookup-activity-in-azure-data-factory"></a>Sökning aktivitet i Azure Data Factory
Lookup-aktiviteten kan användas till att läsa eller söka efter en post/ett tabellnamn/ett värde från valfri extern källa. Dessa utdata kan vidare refereras av efterföljande aktiviteter. 

Följande datakällor stöds för närvarande för sökning:
- JSON-fil i Azure Blob
- Lokala JSON-fil
- Azure SQL Database (JSON-data konverteras från fråga)
- Azure Table Storage (JSON-data konverteras från fråga)

Sökning aktivitet är användbar när du vill hämta en lista över filer dynamiskt / poster/tabeller från en fil eller en datakälla. Utdata från aktiviteten kan användas mer av andra aktiviteter för att utföra specifika bearbetning på endast dessa objekt.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Data Factory V1 dokumentationen](v1/data-factory-introduction.md).


## <a name="example"></a>Exempel
I det här exemplet kopierar kopieringsaktiviteten data från en SQLtabell i Azure SQL-databas till Azure Blob Storage. Namnet på SQL-tabell lagras i en JSON-fil i Blob Storage. Aktiviteten sökning letar upp tabellnamnet vid körning. Den här metoden möjliggör JSON som ska ändras dynamiskt utan att omdistribuera pipelines/datauppsättningar. 

### <a name="pipeline"></a>Pipeline
Den här pipelinen innehåller två aktiviteter: **Leta upp** och **kopiera**. 

- Sökning aktiviteten är konfigurerad för att använda LookupDataset som refererar till en plats i ett Azure Blob Storage. Aktiviteten sökning läser namnet på SQL-tabell från en JSON-fil på den här platsen. 
- Kopieringsaktiviteten använder utdata från aktiviteten sökning (namnet på SQL-tabell). Tabellnamn i källan datauppsättningen (SourceDataset) är konfigurerad för att använda utdata från aktiviteten sökning. Kopieringsaktiviteten kopierar data från SQL-tabell till en plats i Azure Blob Storage som anges av SinkDataset. 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
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
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.tableName}" 
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
Sökning datamängden refererar till filen sourcetable.json i mappen sökning i Azure Storage som anges av AzureStorageLinkedService. 

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
Käll-dataset använder utdata från aktiviteten sökning är namnet på SQL-tabellen. Kopieringsaktiviteten kopierar data från den här SQL-tabell till en plats i Azure Blob Storage som anges av sink-datauppsättningen. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>Sink dataset för kopieringsaktiviteten
Kopieringsaktiviteten kopierar data från SQL-tabellen i filebylookup.csv fil i csv-mappen i Azure Storage som anges av AzureStorageLinkedService. 

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

### <a name="azure-storage-linked-service"></a>Länkad Azure Storage-tjänst
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
Azure SQL-databasen innehåller data som ska kopieras till blob storage. 

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



## <a name="type-properties"></a>Typegenskaper
Namn | Beskrivning | Typ | Krävs
---- | ----------- | ---- | --------
DataSet | Dataset-attributet är att tillhandahålla dataset-referens för sökningen. För närvarande är stöds dataset-typer:<ul><li>FileShareDataset</li><li>AzureBlobDataset</li><li>AzureSqlTableDataset</li><li>AzureTableDataset</li> | nyckel/värde-par | Ja
Källa | Egenskaper för DataSet-specifik datakälla, samma som kopieringskälla för aktiviteten | Nyckel/värde-par | Nej
firstRowOnly | Returnerar första raden eller alla rader. | Booleskt värde | Nej

## <a name="next-steps"></a>Nästa steg
Se annan kontrollflödesaktiviteter som stöds av Data Factory: 

- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
