---
title: Uppslagsaktivitet i Azure Data Factory
description: Lär dig hur du använder uppslagsaktivitet för att slå upp ett värde från en extern källa. Denna produktion kan refereras ytterligare genom efterföljande aktiviteter.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 08cc7ce8f306095a66bc0f8cf74dff8c8b551ecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440497"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Uppslagsaktivitet i Azure Data Factory

Uppslagsaktivitet kan hämta en datauppsättning från någon av de Azure Data Factory-stödda datakällor. Använd den i följande scenario:
- Kontrollera dynamiskt vilka objekt som ska användas i en efterföljande aktivitet, i stället för att koda objektnamnet. Några objektexempel är filer och tabeller.

Uppslagsaktivitet läser och returnerar innehållet i en konfigurationsfil eller tabell. Det returnerar också resultatet av att köra en fråga eller lagrad procedur. Utdata från uppslagsaktiviteten kan användas i en efterföljande kopierings- eller omvandlingsaktivitet om det är ett singleton-värde. Utdata kan användas i en ForEach-aktivitet om det är en matris med attribut.

## <a name="supported-capabilities"></a>Funktioner som stöds

Följande datakällor stöds för uppslagsaktivitet. Det största antalet rader som kan returneras av uppslagsaktivitet är 5 000, upp till 2 MB i storlek. För närvarande är den längsta varaktigheten för uppslagsaktivitet före tidsgräns en timme.

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

## <a name="type-properties"></a>Egenskaper för typ

Namn | Beskrivning | Typ | Krävs?
---- | ----------- | ---- | --------
Datamängd | Innehåller datauppsättningsreferensen för sökningen. Hämta information från avsnittet Egenskaper för **datauppsättning** i varje motsvarande kopplingsartikel. | Nyckel/värdepar | Ja
källa | Innehåller datauppsättningsspecifika källegenskaper, samma som källan Kopiera aktivitet. Hämta information från avsnittet **Kopiera aktivitetsegenskaper** i varje motsvarande kopplingsartikel. | Nyckel/värdepar | Ja
förstRowOnly | Anger om bara den första raden eller alla rader ska returneras. | Boolean | Nej. Standardvärdet är `true`.

> [!NOTE]
> 
> * Källkolumner med **Typen ByteArray** stöds inte.
> * **Struktur** stöds inte i datauppsättningsdefinitioner. För textformatfiler använder du rubrikraden för att ange kolumnnamnet.
> * Om uppslagskällan är en `jsonPathDefinition` JSON-fil stöds inte inställningen för att omforma JSON-objektet. Hela objekten hämtas.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Använd uppslagsaktiviteten i en efterföljande aktivitet

Uppslagsresultatet returneras `output` i avsnittet i resultatet för aktivitetskörning.

* **När `firstRowOnly` är `true` inställt på (standard)** är utdataformatet som visas i följande kod. Uppslagsresultatet ligger `firstRow` under en fast nyckel. Om du vill använda resultatet i `@{activity('MyLookupActivity').output.firstRow.TableName}`efterföljande aktivitet använder du mönstret för .

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **När `firstRowOnly` är `false`inställt på **är utdataformatet som visas i följande kod. Ett `count` fält anger hur många poster som returneras. Detaljerade värden visas under `value` en fast matris. I så fall följs uppslagsaktiviteten av en [Foreach-aktivitet](control-flow-for-each-activity.md). Du skickar `value` matrisen till aktivitetsfältet `items` ForEach `@activity('MyLookupActivity').output.value`med hjälp av mönstret . Använd följande syntax `value` för att komma `@{activity('lookupActivity').output.value[zero based index].propertyname}`åt element i matrisen: . Ett exempel är `@{activity('lookupActivity').output.value[0].tablename}`.

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

### <a name="copy-activity-example"></a>Exempel på kopiera aktivitet
I det här exemplet kopierar Kopiera aktivitet data från en SQL-tabell i Azure SQL Database-instansen till Azure Blob-lagring. Namnet på SQL-tabellen lagras i en JSON-fil i Blob-lagring. Uppslagsaktiviteten slår upp tabellnamnet vid körning. JSON ändras dynamiskt med hjälp av den här metoden. Du behöver inte distribuera om pipelines eller datauppsättningar. 

Det här exemplet visar endast uppslag efter den första raden. För sökning efter alla rader och för att kedja resultaten med ForEach-aktivitet, se exemplen i [Kopiera flera tabeller i grupp med hjälp av Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Pipeline
Den här pipelinen innehåller två aktiviteter: Uppslag och Kopiera. 

- Uppslagsaktiviteten är konfigurerad för att använda **LookupDataset**, som refererar till en plats i Azure Blob storage. Uppslagsaktiviteten läser namnet på SQL-tabellen från en JSON-fil på den här platsen. 
- Kopieringsaktivitet använder utdata från uppslagsaktiviteten, som är namnet på SQL-tabellen. Egenskapen **tableName** i **SourceDataset** är konfigurerad för att använda utdata från uppslagsaktiviteten. Kopiera aktivitet kopierar data från SQL-tabellen till en plats i Azure Blob-lagring. Platsen anges av egenskapen **SinkDataset.** 

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

### <a name="lookup-dataset"></a>Uppslagsdatauppsättning
Uppslagsdatauppsättningen är **filen sourcetable.json** i azure storage-sökningsmappen som anges av typen **AzureStorageLinkedService.** **lookup** 

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

### <a name="source-dataset-for-copy-activity"></a>**Källdatauppsättning** för kopieringsaktivitet
**Källdatauppsättningen** använder utdata från uppslagsaktiviteten, som är namnet på SQL-tabellen. Kopiera aktivitet kopierar data från den här SQL-tabellen till en plats i Azure Blob-lagring. Platsen anges av sink-datauppsättningen. **sink** 

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

### <a name="sink-dataset-for-copy-activity"></a>**Sink-datauppsättning** för kopieringsaktivitet
Kopiera aktivitet kopierar data från SQL-tabellen till **filen filebylookup.csv** i **csv-mappen** i Azure Storage. Filen anges av egenskapen **AzureStorageLinkedService.** 

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
Det här lagringskontot innehåller JSON-filen med namnen på SQL-tabellerna. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>"
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Länkad Azure SQL Database-tjänst
Den här Azure SQL Database-instansen innehåller de data som ska kopieras till Blob-lagring. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;"
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

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

## <a name="limitations-and-workarounds"></a>Begränsningar och lösningar

Här är några begränsningar för uppslagsaktiviteten och föreslagna lösningar.

| Begränsning | Lösning |
|---|---|
| Uppslagsaktiviteten har högst 5 000 rader och en maximal storlek på 2 MB. | Utforma en pipeline på två nivåer där den yttre pipelinen itererar över en inre pipeline, som hämtar data som inte överskrider de maximala raderna eller storleken. |
| | |

## <a name="next-steps"></a>Nästa steg
Se andra kontrollflödesaktiviteter som stöds av Data Factory: 

- [Kör pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [ForEach-aktivitet](control-flow-for-each-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
