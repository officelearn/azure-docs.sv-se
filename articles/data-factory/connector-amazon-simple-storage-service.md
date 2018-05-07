---
title: Kopiera data från Amazon enkla lagringstjänsten med Azure Data Factory | Microsoft Docs
description: Läs mer om hur du kopierar data från Amazon enkla Storage-tjänst (S3) till stöds sink datalager med hjälp av Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 04/27/2018
ms.author: jingwang
ms.openlocfilehash: 3747b53af0be02fb33e0c4b97ff01aaf505066d3
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Kopiera data från Amazon enkla lagringstjänsten med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Version 2 – förhandsversion](connector-amazon-simple-storage-service.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data till och från Azure Blob Storage. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Amazon S3 connnector i V1](v1/data-factory-amazon-simple-storage-service-connector.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från alla datalager stöds källa till Azure Data Lake Store eller kopiera data från Azure Data Lake Store till alla stöds sink-datalagret. En lista över datalager som stöds som datakällor eller sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Amazon S3-anslutningen kopierar filer som-är eller parsning filer med den [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md).

## <a name="required-permissions"></a>Nödvändiga behörigheter

Om du vill kopiera data från Amazon S3, kontrollera att du har beviljats följande behörigheter:

- `s3:GetObject` och `s3:GetObjectVersion` för Amazon S3 objekt åtgärder.
- `s3:ListBucket` eller `s3:GetBucketLocation` för Amazon S3 Bucket-åtgärder. Om du använder guiden Data Factory kopiera `s3:ListAllMyBuckets` krävs också.

Mer information om en fullständig lista över Amazon S3 behörigheter finns [att ange behörigheter i en princip](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter för Amazon S3.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Amazon S3 länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till **AmazonS3**. | Ja |
| accessKeyId | ID för den hemliga åtkomstnyckeln. |Ja |
| secretAccessKey | Hemlig själva åtkomstnyckeln. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime (om datalager finns i privat nätverk). Om inget anges används standard-Azure Integration Runtime. |Nej |

>[!NOTE]
>Den här anslutningen kräver åtkomstnycklar för IAM-konto för att kopiera data från Amazon S3. [Tillfällig säkerhets-ID](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) stöds inte.
>

Här är ett exempel:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                    "type": "SecureString",
                    "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Amazon S3 dataset.

Ange typegenskapen för dataset för att kopiera data från Amazon S3, **AmazonS3Object**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **AmazonS3Object** |Ja |
| bucketName | S3-Bucketnamn. Wildcard-filter stöds inte. |Ja |
| key | Den **namn eller jokertecken-filtret** S3 objekt nyckel under en angiven bucket. Gäller bara när ”prefixet”-egenskap har inte angetts. <br/><br/>Wildcard-filtret har endast stöd för filen Namndelen men som inte finns i mappen. Tillåtna jokertecken är: `*` (flera tecken) och `?` (valfritt tecken).<br/>-Exempel 1: `"key": "rootfolder/subfolder/*.csv"`<br/>-Exempel 2: `"key": "rootfolder/subfolder/???20180427.txt"` |Nej |
| prefix | Prefix för nyckeln S3 objekt. Objekt vars nycklar som börjar med prefixet är markerade. Gäller endast när ”key”-egenskap inte har angetts. |Nej |
| version | Versionen av objektet S3 om S3 versionshantering är aktiverad. |Nej |
| Format | Om du vill **kopiera filer som-är** mellan filbaserade butiker (binär kopia), hoppa över avsnittet format i både inkommande och utgående dataset-definitioner.<br/><br/>Om du vill att parsa eller generera filer med ett specifikt format format för följande filtyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [textformat](supported-file-formats-and-compression-codecs.md#text-format), [Json-Format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs.md#orc-format), och [parkettgolv Format](supported-file-formats-and-compression-codecs.md#parquet-format) avsnitt. |Nej (endast för binära kopiera scenario) |
| Komprimering | Ange typ och kompression för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Nivåer som stöds är: **Optimal** och **snabbast**. |Nej |

>[!TIP]
>Kopiera alla filer i en mapp genom att ange **bucketName** för bucket och **prefix** för mappen.<br>Om du vill kopiera en enstaka fil med ett angivet namn, ange **bucketName** för bucket och **nyckeln** för en del plus filen mappnamn.<br>Om du vill kopiera en delmängd av filer på en mapp, ange **bucketName** för bucket och **nyckeln** för mappen del plus jokertecken-filtret.

**Exempel: med prefix**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

**Exempel: med hjälp av nyckeln och version (valfritt)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Data Lake källa och mottagare.

### <a name="amazon-s3-as-source"></a>Amazon S3 som källa

Om du vill kopiera data från Amazon S3, anger du datakällan i kopieringsaktiviteten till **FileSystemSource** (som omfattar Amazon S3). Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **FileSystemSource** |Ja |
| Rekursiva | Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. Obs när rekursiv är inställd på true och mottagare är filbaserad lagring, tom mapp/underåtgärder-folder kommer inte att kopieras/skapas på mottagare.<br/>Tillåtna värden är: **SANT** (standard), **FALSKT** | Nej |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).