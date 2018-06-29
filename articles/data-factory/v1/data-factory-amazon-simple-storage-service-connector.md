---
title: Flytta data från Amazon enkla Storage-tjänsten med hjälp av Data Factory | Microsoft Docs
description: Lär dig mer om hur du flyttar data från Amazon enkla lagringstjänst (S3) med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d895dcdf9eefac01790aab6dc3f36a3feb0a8b12
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051158"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Flytta data från Amazon enkla Storage-tjänsten med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-amazon-simple-storage-service-connector.md)
> * [Version 2 (aktuell version)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns [Amazon S3 connector i V2](../connector-amazon-simple-storage-service.md).

Den här artikeln förklarar hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data från Amazon enkla Storage-tjänst (S3). Den bygger på den [Data movement aktiviteter](data-factory-data-movement-activities.md) artikel som presenterar en allmän översikt över dataflyttning med copy-aktivitet.

Du kan kopiera data från Amazon S3 till alla stöds sink-datalagret. En lista över datakällor som stöds som sänkor av kopieringsaktiviteten, finns det [stöds datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. Data Factory stöder för närvarande endast flytta data från Amazon S3 till andra databaser, men inte flytta data från andra data lagras på Amazon S3.

## <a name="required-permissions"></a>Nödvändiga behörigheter
Om du vill kopiera data från Amazon S3, kontrollera att du har beviljats följande behörigheter:

* `s3:GetObject` och `s3:GetObjectVersion` för Amazon S3 objekt åtgärder.
* `s3:ListBucket` för Amazon S3 Bucket-åtgärder. Om du använder guiden Data Factory kopiera `s3:ListAllMyBuckets` krävs också.

Mer information om en fullständig lista över Amazon S3 behörigheter finns [att ange behörigheter i en princip](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data från en källa för Amazon S3 med hjälp av olika verktyg eller API: er.

Det enklaste sättet att skapa en pipeline är att använda den **guiden Kopiera**. För en snabb genomgång, se [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md).

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall** , **.NET API**, och **REST API**. Stegvisa instruktioner för att skapa en pipeline med en kopieringsaktiviteten, finns det [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare:

1. Skapa **länkade tjänster** att länka inkommande och utgående data lagras till din data factory.
2. Skapa **datauppsättningar** att representera inkommande och utgående data för kopieringen.
3. Skapa en **pipeline** med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata.

När du använder guiden skapas JSON definitioner för dessa Data Factory-enheter (länkade tjänster, datauppsättningar och pipelinen) automatiskt för dig. När du använder verktyg eller API: er (utom .NET API), kan du definiera dessa Data Factory-enheter med hjälp av JSON-format. Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett dataarkiv för Amazon S3 finns i [JSON-exempel: kopiera data från Amazon S3 till Azure Blob](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) i den här artikeln.

> [!NOTE]
> Mer information om fil- och komprimering de format som stöds för en kopieringsaktiviteten finns [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter för Amazon S3.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
En länkad tjänst länkar ett datalager till en data factory. Du skapar en länkad tjänst av typen **AwsAccessKey** länka Amazon S3 datalager till din data factory. Följande tabell innehåller beskrivning för JSON-element som är specifika för Amazon S3 (AwsAccessKey) länkade tjänsten.

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| accessKeyID |ID för den hemliga åtkomstnyckeln. |sträng |Ja |
| secretAccessKey |Hemlig själva åtkomstnyckeln. |Krypterad hemliga sträng |Ja |

>[!NOTE]
>Den här anslutningen kräver åtkomstnycklar för IAM-konto för att kopiera data från Amazon S3. [Tillfällig säkerhets-ID](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) stöds inte.
>

Här är ett exempel:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd
Om du vill ange en datamängd som representerar indata i Azure Blob storage ange egenskapen type för datauppsättningen till **AmazonS3**. Ange den **linkedServiceName** -egenskapen för datauppsättningen till namnet på Amazon S3 länkade tjänsten. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns [skapa datauppsättningar](data-factory-create-datasets.md). 

Avsnitt som struktur, tillgänglighet och principen är liknande för alla dataset-typer (till exempel SQL-databas, Azure blob och Azure-tabellen). Den **typeProperties** avsnitt är olika för varje typ av dataset och innehåller information om placeringen av data i datalagret. Den **typeProperties** avsnittet för en dataset av typen **AmazonS3** (som omfattar Amazon S3 dataset) har följande egenskaper:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| bucketName |S3-Bucketnamn. |Sträng |Ja |
| key |S3 objekt nyckeln. |Sträng |Nej |
| prefix |Prefix för nyckeln S3 objekt. Objekt vars nycklar som börjar med prefixet är markerade. Gäller endast när nyckeln är tom. |Sträng |Nej |
| version |Versionen av objektet S3 om S3 versionshantering är aktiverad. |Sträng |Nej |
| Format | Följande format stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc format](data-factory-supported-file-and-compression-formats.md#orc-format), och [parkettgolv format ](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill kopiera filer som-är mellan filbaserade butiker (binär kopia), hoppa över avsnittet format i både inkommande och utgående dataset-definitioner. |Nej | |
| komprimering | Ange typ och kompression för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåerna som stöds är: **Optimal** och **snabbast**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej | |


> [!NOTE]
> **bucketName + nyckeln** anger platsen för S3 objekt, där bucket är Rotbehållare för S3 objekt och nyckeln är den fullständiga sökvägen till S3 objekt.

### <a name="sample-dataset-with-prefix"></a>Exempeldatamängd med prefix

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "testbucket",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
### <a name="sample-dataset-with-version"></a>Exempeldatamängd (med version)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "testbucket",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="dynamic-paths-for-s3"></a>Dynamisk sökvägar för S3
Föregående exempel använder fasta värden för den **nyckeln** och **bucketName** egenskaper i Amazon S3 datamängden.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Du kan ha Data Factory beräkna egenskaperna dynamiskt vid körning, med hjälp av systemvariabler som SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Du kan göra samma för den **prefix** -egenskapen för en Amazon S3 datauppsättning. En lista över funktioner som stöds och variabler, se [Data Factory-funktioner och systemvariabler](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter, se [skapar pipelines](data-factory-create-pipelines.md). Egenskaper som namn, beskrivning, ingående och utgående tabeller och principer är tillgängliga för alla typer av aktiviteter. Egenskaper som är tillgängliga i den **typeProperties** avsnitt i aktiviteten varierar med varje aktivitetstyp. För kopieringsaktiviteten variera egenskaperna beroende på vilka typer av datakällor och sänkor. När en källa i kopieringsaktiviteten är av typen **FileSystemSource** (som omfattar Amazon S3), av följande egenskap är tillgänglig i **typeProperties** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| rekursiva |Anger om att rekursivt listan S3 objekt i katalogen. |SANT/FALSKT |Nej |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>JSON-exempel: kopiera data från Amazon S3 till Azure Blob storage
Det här exemplet visas hur du kopierar data från Amazon S3 till ett Azure Blob storage. Dock datan kan kopieras direkt till [någon sänkor som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopieringsaktiviteten i Data Factory.

Exemplet innehåller JSON definitioner för följande Data Factory-enheter. Du kan använda dessa definitioner för att skapa en pipeline för att kopiera data från Amazon S3 till Blob storage med hjälp av den [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), eller [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).   

* En länkad tjänst av typen [AwsAccessKey](#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Indata [dataset](data-factory-create-datasets.md) av typen [AmazonS3](#dataset-properties).
* Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med kopieringsaktiviteten som använder [FileSystemSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från Amazon S3 till en Azure blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

### <a name="amazon-s3-linked-service"></a>Amazon S3 länkad tjänst

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="amazon-s3-input-dataset"></a>Amazon S3 inkommande dataset

Ange **”externa”: true** informerar Data Factory-tjänsten att datamängden är extern till datafabriken. Ange den här egenskapen till true för en inkommande datauppsättningen som inte tillverkas av en aktivitet i pipelinen.

```json
    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }
```


### <a name="azure-blob-output-dataset"></a>Utdatauppsättning för Azure-blobb

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Sökvägen till mappen för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad, dag och timmar delar av starttiden.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Kopiera aktivitet i en pipeline med en källa för Amazon S3 och en blob-mottagare

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **FileSystemSource**, och **sink** är inställd på **BlobSink**.

```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource",
                        "recursive": true
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AmazonS3InputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AmazonS3ToBlob"
            }
        ],
        "start": "2014-08-08T18:00:00Z",
        "end": "2014-08-08T19:00:00Z"
    }
}
```
> [!NOTE]
> Om du vill mappa kolumner från en datamängd för källan till kolumner från en datamängd sink finns [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

* Mer information om viktiga faktorer som påverkan prestanda för flytt av data (kopieringsaktiviteten) i Data Factory och olika sätt att optimera den finns i [kopiera aktivitet prestanda och prestandajustering guiden](data-factory-copy-activity-performance.md).

* Stegvisa instruktioner för att skapa en pipeline med kopiera aktiviteter finns i [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
