---
title: Flytta data från Amazon Simple Storage Service med hjälp av Data Factory
description: Lär dig mer om hur du flyttar data från Amazon Simple Storage Service (S3) med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b23be9901df7ca435f412d9f49e1a7ad88382ade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281657"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Flytta data från Amazon Simple Storage Service med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-amazon-simple-storage-service-connector.md)
> * [Version 2 (aktuell version)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Amazon S3-anslutning i V2](../connector-amazon-simple-storage-service.md).

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data från Amazon Simple Storage Service (S3). Den bygger på artikeln [Dataflyttningsaktiviteter,](data-factory-data-movement-activities.md) som ger en allmän översikt över dataflyttning med kopieringsaktiviteten.

Du kan kopiera data från Amazon S3 till alla diskbänksdatalager som stöds. En lista över datalager som stöds som mottagare av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Data Factory stöder för närvarande endast flytta data från Amazon S3 till andra datalager, men inte flytta data från andra datalager till Amazon S3.

## <a name="required-permissions"></a>Nödvändiga behörigheter
Om du vill kopiera data från Amazon S3 kontrollerar du att du har beviljats följande behörigheter:

* `s3:GetObject`och `s3:GetObjectVersion` för Amazon S3 Object Operations.
* `s3:ListBucket`för Amazon S3 Bucket Operations. Om du använder guiden Kopia `s3:ListAllMyBuckets` för datafabriker krävs också.

Mer information om den fullständiga listan över Behörigheter för Amazon S3 finns [i Ange behörigheter i en princip](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data från en Amazon S3-källa med hjälp av olika verktyg eller API:er.

Det enklaste sättet att skapa en pipeline är att använda **kopieringsguiden**. En snabb genomgång finns i [Självstudiekurs: Skapa en pipeline med hjälp av kopiera guide](data-factory-copy-data-wizard-tutorial.md).

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-mall,** **.NET API**och REST **API**. Steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet finns i [självstudiekursen Kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Oavsett om du använder verktyg eller API:er utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken.
2. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen.
3. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg eller API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet. Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett Amazon S3-datalager finns i [JSON-exemplet: Kopiera data från Amazon S3 till Azure Blob](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) i den här artikeln.

> [!NOTE]
> Mer information om fil- och komprimeringsformat som stöds för en [kopieringsaktivitet finns i Fil- och komprimeringsformat i Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för Amazon S3.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
En länkad tjänst länkar ett datalager till en datafabrik. Du skapar en länkad tjänst av typen **AwsAccessKey** för att länka ditt Amazon S3-datalager till din datafabrik. Följande tabell innehåller en beskrivning av JSON-element som är specifika för Amazon S3 (AwsAccessKey) länkade tjänst.

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| accessKeyID |ID för den hemliga åtkomstnyckeln. |sträng |Ja |
| secretAccessKey |Den hemliga åtkomstnyckeln själv. |Krypterad hemlig sträng |Ja |

>[!NOTE]
>Den här anslutningen kräver åtkomstnycklar för IAM-konto för att kopiera data från Amazon S3. [Tillfällig säkerhetsautentisering](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) stöds inte.
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
Om du vill ange en datauppsättning som ska representera indata i Azure Blob-lagring anger du typegenskapen för datauppsättningen till **AmazonS3**. Ange egenskapen **linkedServiceName** för datauppsättningen till namnet på den Amazon S3-länkade tjänsten. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [Skapa datauppsättningar](data-factory-create-datasets.md). 

Avsnitt som struktur, tillgänglighet och princip är liknande för alla datauppsättningstyper (till exempel SQL-databas, Azure-blob och Azure-tabell). Avsnittet **typeProperties** är olika för varje typ av datauppsättning och innehåller information om platsen för data i datalagret. Avsnittet **typeProperties** för en datauppsättning av typen **AmazonS3** (som innehåller Amazon S3-datauppsättningen) har följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| bucketName (bucketName) |S3-hinknamnet. |String |Ja |
| key |S3-objektnyckeln. |String |Inga |
| Prefix |Prefix för S3-objektnyckeln. Objekt vars nycklar börjar med det här prefixet är markerade. Gäller endast när nyckeln är tom. |String |Inga |
| version |Versionen av S3-objektet, om S3-versionshantering är aktiverad. |String |Inga |
| format | Följande formattyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange **type** typegenskapen under format till ett av dessa värden. Mer information finns i avsnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parkettformat.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Om du vill kopiera filer som de är mellan filbaserade butiker (binär kopia) hoppar du över formatavsnittet i definitionerna för både in- och utdatadata. | |Inga |
| komprimering | Ange typ och komprimeringsnivå för data. De typer som stöds är: **GZip**, **Deflate**, **BZip2**och **ZipDeflate**. Nivåerna som stöds är: **Optimal** och **Snabbast**. Mer information finns [i Fil- och komprimeringsformat i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). | |Inga |


> [!NOTE]
> **bucketName + nyckel** anger platsen för S3-objektet, där bucket är rotbehållaren för S3-objekt, och nyckeln är den fullständiga sökvägen till S3-objektet.

### <a name="sample-dataset-with-prefix"></a>Exempel på datauppsättning med prefix

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
### <a name="sample-dataset-with-version"></a>Exempel på datauppsättning (med version)

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

### <a name="dynamic-paths-for-s3"></a>Dynamiska banor för S3
I föregående exempel används fasta värden för **egenskaperna key** och **bucketName** i Amazon S3-datauppsättningen.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Du kan låta Data Factory beräkna dessa egenskaper dynamiskt vid körning, med hjälp av systemvariabler som SegmentStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Du kan göra samma sak för **prefixet** egenskapen för en Amazon S3-datauppsättning. En lista över funktioner och variabler som stöds finns i [Data Factorys funktioner och systemvariabler](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Skapa pipelines](data-factory-create-pipelines.md). Egenskaper som namn, beskrivning, indata- och utdatatabeller och principer är tillgängliga för alla typer av aktiviteter. Egenskaper som är tillgängliga i avsnittet **typEgenskaper** i aktiviteten varierar beroende på varje aktivitetstyp. För kopieringsaktiviteten varierar egenskaperna beroende på vilka typer av källor och sänkor som finns. När en källa i kopieringsaktiviteten är av typen **FileSystemSource** (som inkluderar Amazon S3) är följande egenskap tillgänglig i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| Rekursiv |Anger om S3-objekt ska anges rekursivt under katalogen. |sant/falskt |Inga |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>JSON exempel: Kopiera data från Amazon S3 till Azure Blob lagring
Det här exemplet visar hur du kopierar data från Amazon S3 till en Azure Blob-lagring. Data kan dock kopieras direkt till [någon av de diskhoar som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopieringsaktiviteten i Data Factory.

Exemplet innehåller JSON-definitioner för följande Data Factory-entiteter. Du kan använda dessa definitioner för att skapa en pipeline för att kopiera data från Amazon S3 till Blob-lagring med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).   

* En länkad tjänst av typen [AwsAccessKey](#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* En [indatauppsättning](data-factory-create-datasets.md) av typen [AmazonS3](#dataset-properties).
* En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med kopieringsaktivitet som använder [FileSystemSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från Amazon S3 till en Azure-blob varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

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

### <a name="amazon-s3-input-dataset"></a>Amazon S3 indatauppsättning

Inställningen **"extern": true** informerar datafabrikstjänsten om att datauppsättningen är extern till datafabriken. Ange den här egenskapen till true på en indatauppsättning som inte produceras av en aktivitet i pipelinen.

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

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder delar av starttiden för år, månad, dag och timmar.

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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Kopiera aktivitet i en pipeline med en Amazon S3-källa och en blob-diskho

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I JSON-definitionen för pipelinen anges **källtypen** till **FileSystemSource**och **sink-typen** är inställd på **BlobSink**.

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
> Information om hur du mappar kolumner från en källdatauppsättning till kolumner från en sink-datauppsättning finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

* Mer information om viktiga faktorer som påverkar prestanda för datarörelser (kopieringsaktivitet) i Data Factory och olika sätt att optimera den finns i [guiden Kopiera aktivitetsprestanda och justering](data-factory-copy-activity-performance.md).

* Stegvisa instruktioner för hur du skapar en pipeline med en kopieringsaktivitet finns i [självstudiekursen Kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
