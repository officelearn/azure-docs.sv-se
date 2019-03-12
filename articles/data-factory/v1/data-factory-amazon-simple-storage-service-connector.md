---
title: Flytta data från Amazon Simple Storage Service med hjälp av Data Factory | Microsoft Docs
description: Läs mer om hur du flyttar data från Amazon Simple Storage Service (S3) med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1f5064cece32cfc38f149816961e5156ff20974a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57536716"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Flytta data från Amazon Simple Storage Service med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-amazon-simple-storage-service-connector.md)
> * [Version 2 (aktuell version)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [Amazon S3-anslutning i V2](../connector-amazon-simple-storage-service.md).

Den här artikeln förklarar hur du använder Kopieringsaktivitet i Azure Data Factory för att flytta data från Amazon Simple Storage Service (S3). Den bygger på den [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel som anger en allmän översikt över dataförflyttning med kopieringsaktiviteten.

Du kan kopiera data från Amazon S3 till alla datalager för mottagare som stöds. En lista över datalager som stöds som mottagare av Kopieringsaktivitet finns i den [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. Data Factory stöder för närvarande endast flyttar data från Amazon S3 till datalager, men inte flyttar data från andra data lagrar till Amazon S3.

## <a name="required-permissions"></a>Nödvändiga behörigheter
För att kopiera data från Amazon S3, kontrollera att du har beviljats följande behörigheter:

* `s3:GetObject` och `s3:GetObjectVersion` för Amazon S3-objektet.
* `s3:ListBucket` för Amazon S3-Bucket-åtgärder. Om du använder guiden Data Factory kopiera `s3:ListAllMyBuckets` krävs också.

Mer information om en fullständig lista över Amazon S3-behörigheter finns i [att ange behörigheter i en princip](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data från en källa för Amazon S3 med hjälp av olika verktyg eller API: er.

Det enklaste sättet att skapa en pipeline är att använda den **Kopieringsguiden**. En snabb genomgång finns i [självstudien: Skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md).

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och  **REST-API**. Stegvisa anvisningar om hur du skapar en pipeline med en Kopieringsaktivitet finns i den [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Om du använder verktyg eller API: er kan utföra du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager:

1. Skapa **länkade tjänster** länka inkommande och utgående data du lagrar till din datafabrik.
2. Skapa **datauppsättningar** som representerar inkommande och utgående data för kopieringen.
3. Skapa en **pipeline** med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa Data Factory-entiteter (länkade tjänster, datauppsättningar och pipeline) automatiskt åt dig. När du använder verktyg eller API: er (med undantag för .NET-API) kan definiera du dessa Data Factory-entiteter med hjälp av JSON-format. Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett datalager för Amazon S3, finns det [JSON-exempel: Kopiera data från Amazon S3 till Azure Blob](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) i den här artikeln.

> [!NOTE]
> Mer information om fil- och komprimeringsformat de format som stöds för en Kopieringsaktivitet finns [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för Amazon S3.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
En länkad tjänst länkar ett datalager till en data factory. Du skapar en länkad tjänst av typen **AwsAccessKey** att länka ditt Amazon S3-datalager till datafabriken. Följande tabell innehåller beskrivning för JSON-element som är specifika för Amazon S3 (AwsAccessKey) länkad tjänst.

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| accessKeyID |ID för den hemliga åtkomstnyckeln. |sträng |Ja |
| secretAccessKey |Den hemliga åtkomstnyckeln själva. |Krypterad hemlighet sträng |Ja |

>[!NOTE]
>Den här anslutningen kräver åtkomstnycklarna för IAM-konto för att kopiera data från Amazon S3. [Tillfällig autentiseringsuppgift](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) stöds inte.
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
Om du vill ange en datauppsättning som representerar indata i Azure Blob storage, ange typegenskapen på datauppsättningen till **AmazonS3**. Ange den **linkedServiceName** egenskap med namnet på Amazon S3 länkad tjänst. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [skapar datauppsättningar](data-factory-create-datasets.md). 

Avsnitt som struktur, tillgänglighet och princip är liknande för alla datauppsättningstyper av (till exempel SQL database, Azure-blob och Azure-tabell). Den **typeProperties** avsnittet är olika för varje typ av datauppsättning och tillhandahåller information om platsen för data i datalagret. Den **typeProperties** avsnittet för en datauppsättning av typen **AmazonS3** (som innehåller Amazon S3-datamängd) har följande egenskaper:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| bucketName |S3-Bucketnamn. |String |Ja |
| key |Objektnyckel S3. |String |Nej |
| prefix |Prefix för Objektnyckel S3. Objekt vars nycklar som börjar med prefixet är markerade. Gäller endast när nyckeln är tom. |String |Nej |
| version |Versionen av S3-objektet, om S3 versionshantering är aktiverad. |String |Nej |
| Format | Följande formattyper av stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typ** egenskapen under format till ett av dessa värden. Mer information finns i den [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-format](data-factory-supported-file-and-compression-formats.md#orc-format), och [Parquet-format ](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill kopiera filer som – är mellan filbaserade (binär kopia) att hoppa över avsnittet format i både inkommande och utgående datamängd definitioner. |Nej | |
| Komprimering | Ange typ och komprimeringsnivå för data. Typerna som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåerna som stöds är: **Optimal** och **snabbaste**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej | |


> [!NOTE]
> **bucketName + nyckeln** anger platsen för S3-objektet, där bucket är Rotbehållare för S3-objekt och nyckeln är den fullständiga sökvägen till S3-objektet.

### <a name="sample-dataset-with-prefix"></a>Exempeldatauppsättningen med prefix

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
### <a name="sample-dataset-with-version"></a>Exempeldatauppsättningen (med version)

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
I föregående exempel använder fasta värden för den **nyckel** och **bucketName** egenskaper i Amazon S3-datamängd.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Du kan ha Data Factory beräknar egenskaperna dynamiskt vid körning, med hjälp av systemvariabler, till exempel SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Du kan göra detsamma den **prefix** egenskapen för en Amazon S3-datamängd. En lista över funktioner som stöds och variabler finns i [Data Factory-funktioner och systemvariabler](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapa pipelines](data-factory-create-pipelines.md). Egenskaper, till exempel namn, beskrivning, indata och utdata tabeller och principer är tillgängliga för alla typer av aktiviteter. Egenskaper som är tillgängliga i den **typeProperties** avsnittet aktivitetens varierar med varje aktivitetstyp av. För kopieringsaktiviteten varierar egenskaper beroende på vilka typer av källor och mottagare. När en källa i kopieringsaktiviteten är av typen **FileSystemSource** (som innehåller Amazon S3), följande egenskap är tillgänglig i **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| rekursiv |Anger om rekursivt lista S3 objekt i katalogen. |SANT/FALSKT |Nej |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>JSON-exempel: Kopiera data från Amazon S3 till Azure Blob storage
Detta exempel visar hur du kopierar data från Amazon S3 till Azure Blob storage. Dock datan kan kopieras direkt till [någon mottagare som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopieringsaktiviteten i Data Factory.

Det innehåller JSON-definitioner för följande Data Factory-entiteter. Du kan använda dessa definitioner för att skapa en pipeline för att kopiera data från Amazon S3 till Blob storage med hjälp av den [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), eller [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).   

* En länkad tjänst av typen [AwsAccessKey](#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Indata [datauppsättning](data-factory-create-datasets.md) av typen [AmazonS3](#dataset-properties).
* Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [FileSystemSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från Amazon S3 till en Azure-blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

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

### <a name="amazon-s3-input-dataset"></a>Amazon S3 datauppsättningen för indata

Ange **”external”: true** informerar Data Factory-tjänsten att datauppsättningen är extern till datafabriken. Ange egenskapen till true på en inkommande datamängd som inte kommer från en aktivitet i pipelinen.

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

Data skrivs till en ny blob varje timme (frequency: timme, intervall: 1). Sökvägen till mappen för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad, dag och timmar delar av starttiden.

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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Kopiera aktivitet i en pipeline med en Amazon S3-källa och mottagare blob

Pipelinen innehåller en Kopieringsaktivitet som har konfigurerats för användning av in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **FileSystemSource**, och **mottagare** är **BlobSink**.

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
> Om du vill mappa kolumner från en källdatauppsättning till kolumner från en datauppsättning för mottagare, se [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

* Mer information om viktiga faktorer att påverka prestandan för dataflytt (Kopieringsaktivitet) i Data Factory och olika sätt att optimera den, finns det [kopiera aktivitet prestanda- och Justeringsguiden](data-factory-copy-activity-performance.md).

* Stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet finns i den [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
