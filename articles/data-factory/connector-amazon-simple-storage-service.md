---
title: Kopiera data från Amazon Simple Storage Service (S3)
description: Läs om hur du kopierar data från Amazon Simple Storage Service (S3) till mottagar data lager som stöds med hjälp av Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.openlocfilehash: 6975c876bb31e36b7b6268d284f747fd51803d63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415458"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Kopiera data från Amazon Simple Storage-tjänsten med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
>
> * [Version 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Aktuell version](connector-amazon-simple-storage-service.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du kopierar data från Amazon Simple Storage Service (Amazon S3). Läs den [inledande artikeln](introduction.md)om du vill veta mer om Azure Data Factory.

>[!TIP]
>Om du vill använda ett scenario för datamigrering från Amazon S3 till Azure Storage, lär du dig att [använda Azure Data Factory för att migrera data från Amazon S3 till Azure Storage](data-migration-guidance-s3-azure-storage.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Amazon S3-kopplingen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)

Mer specifikt stöder den här Amazon S3-anslutaren kopiering av filer som-är eller parsar filer med [fil format och komprimerings-codec som stöds](supported-file-formats-and-compression-codecs.md). Du kan också välja att [bevara fil-metadata under kopieringen](#preserve-metadata-during-copy). Anslutningen använder [AWS Signature version 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) för att autentisera begär anden till S3.

>[!TIP]
>Du kan använda den här Amazon S3-anslutningen för att kopiera data från **alla S3-kompatibla lagringsmatriser** , t. ex. [Google Cloud Storage](connector-google-cloud-storage.md). Ange motsvarande tjänst-URL i den länkade tjänst konfigurationen.

## <a name="required-permissions"></a>Nödvändiga behörigheter

Om du vill kopiera data från Amazon S3 ser du till att du har beviljats följande behörigheter:

- **För kopierings aktivitets körning:** `s3:GetObject` : `s3:GetObjectVersion` och för Amazon S3-objekt åtgärder.
- **För Data Factory GUI-redigering**: `s3:ListAllMyBuckets` och `s3:ListBucket` / `s3:GetBucketLocation` för Amazon S3-Bucket-åtgärder krävs dessutom även för åtgärder som test anslutning och bläddra/navigera fil Sök vägar. Om du inte vill bevilja behörigheterna hoppar du över testa anslutning på sidan för att skapa länkade tjänster och anger sökvägen direkt i inställningar för data uppsättning.

Mer information om den fullständiga listan över Amazon S3-behörigheter finns i [Ange behörigheter i en princip](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Amazon S3.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för Amazon S3-länkade tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **AmazonS3**. | Ja |
| accessKeyId | ID för den hemliga åtkomst nyckeln. |Ja |
| secretAccessKey | Den hemliga åtkomst nyckeln. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| serviceUrl | Ange den anpassade S3-slutpunkten om du kopierar data från en S3-kompatibel lagringsprovider än den officiella Amazon S3-tjänsten. Om du till exempel vill kopiera data från Google Cloud Storage anger `https://storage.googleapis.com`du. | Nej |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime (om ditt data lager finns i privat nätverk). Om inget värde anges används standard Azure Integration Runtime. |Nej |

>[!TIP]
>Ange URL: en för den anpassade S3-tjänsten om du kopierar data från en S3-kompatibel lagrings plats än den officiella Amazon S3-tjänsten.

>[!NOTE]
>Den här anslutningen kräver åtkomst nycklar för IAM-kontot för att kopiera data från Amazon S3. [Tillfälliga säkerhets referenser](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) stöds inte.
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Amazon S3 under `location` inställningar i format-baserad data mängd:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Typ egenskapen under `location` i data mängden måste anges till **AmazonS3Location**. | Ja      |
| bucketName | S3-Bucket-namn.                                          | Ja      |
| folderPath | Sökvägen till mappen under den angivna Bucket. Om du vill använda jokertecken för att filtrera mappar hoppar du över den här inställningen och anger i aktivitets källans inställningar. | Nej       |
| fileName   | Fil namnet under angivet Bucket + folderPath. Om du vill använda jokertecken för att filtrera filer, hoppar du över den här inställningen och anger i aktivitets källans inställningar. | Nej       |
| version | Versionen av S3-objektet, om S3-versioner är aktive rad. Om detta inte anges kommer den senaste versionen att hämtas. |Nej |

**Exempel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Amazon S3-källan.

### <a name="amazon-s3-as-source"></a>Amazon S3 som källa

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Amazon S3 under `storeSettings` inställningar i format-baserad kopierings Källa:

| Egenskap                 | Beskrivning                                                  | Krävs                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| typ                     | Typ egenskapen under `storeSettings` måste anges till **AmazonS3ReadSettings**. | Ja                                                         |
| rekursiva                | Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. Observera att när rekursivt har angetts till true och sinken är en filbaserad lagring, kopieras inte en tom mapp eller undermapp till mottagaren. Tillåtna värden är **True** (standard) och **false**. | Nej                                                          |
| protokollprefixet                   | Prefix för S3-objekt nyckeln under den angivna Bucket som kon figurer ATS i data uppsättningen för att filtrera käll objekt. Objekt vars nycklar börjar med det här prefixet väljs. <br>Gäller endast när `wildcardFolderPath` och `wildcardFileName` egenskaper inte anges. | Nej                                                          |
| wildcardFolderPath       | Mappsökvägen med jokertecken under den angivna Bucket som kon figurer ATS i data uppsättningen för att filtrera källmappen för mappar. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller enstaka tecken). används `^` för att kringgå om det faktiska mappnamnet har jokertecken eller detta escape-tecken inuti. <br>Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Nej                                                          |
| wildcardFileName         | Fil namnet med jokertecken under den aktuella Bucket + folderPath/wildcardFolderPath för att filtrera källfiler. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller enstaka tecken). används `^` för att kringgå om det faktiska mappnamnet har jokertecken eller detta escape-tecken inuti.  Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Ja om `fileName` i data uppsättning `prefix` och inte har angetts |
| modifiedDatetimeStart    | Filter för filer baserat på attributet: senast ändrad. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och. `modifiedDatetimeEnd` Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br> Egenskaperna kan vara NULL vilket innebär att inget attribut filter används för data uppsättningen.  När `modifiedDatetimeStart` har datetime-värde `modifiedDatetimeEnd` men är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime väljs.  När `modifiedDatetimeEnd` har ett datetime- `modifiedDatetimeStart` värde men är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime väljs. | Nej                                                          |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Nej                                                          |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till lagrings lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej                                                          |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exempel på mapp-och fil filter

I det här avsnittet beskrivs det resulterande beteendet hos mappsökvägen och fil namnet med filter för jokertecken.

| Bucket | key | rekursiva | Källans mappstruktur och filter resultat (filer i fetstil hämtas)|
|:--- |:--- |:--- |:--- |
| Bucket | `Folder*/*` | falskt | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fil2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| Bucket | `Folder*/*` | true | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fil2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| Bucket | `Folder*/*.csv` | falskt | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| Bucket | `Folder*/*.csv` | true | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

## <a name="preserve-metadata-during-copy"></a>Bevara metadata under kopiering

När du kopierar filer från Amazon S3 till Azure Data Lake Storage Gen2/Azure-Blob kan du välja att bevara filens metadata tillsammans med data. Läs mer om att [bevara metadata](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Egenskaper för GetMetadata-aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [getMetaData-aktivitet](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Ta bort aktivitets egenskaper

Om du vill veta mer om egenskaperna kontrollerar du [ta bort aktivitet](delete-activity.md)

## <a name="legacy-models"></a>Äldre modeller

>[!NOTE]
>Följande modeller stöds fortfarande för bakåtkompatibilitet. Du rekommenderar att du använder den nya modellen som anges ovan och fortsätter, och redigerings gränssnittet för ADF har växlat till att generera den nya modellen.

### <a name="legacy-dataset-model"></a>Äldre data uppsättnings modell

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **AmazonS3Object** |Ja |
| bucketName | S3-Bucket-namn. Wildcard-filtret stöds inte. |Ja för kopiera/Sök-aktivitet, nej för GetMetadata-aktivitet |
| key | **Namnet eller wildcard-filtret** för S3-objekt nyckeln under den angivna Bucket. Gäller endast om egenskapen prefix inte har angetts. <br/><br/>Wildcard-filtret stöds för både del-och fil namns delen. Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken).<br/>– Exempel 1:`"key": "rootfolder/subfolder/*.csv"`<br/>– Exempel 2:`"key": "rootfolder/subfolder/???20180427.txt"`<br/>Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). Används `^` för att kringgå om din faktiska mapp/fil namn har jokertecken eller detta escape-tecken inuti. |Nej |
| protokollprefixet | Prefix för S3-objekt nyckeln. Objekt vars nycklar börjar med det här prefixet väljs. Gäller endast när egenskapen "nyckel" inte har angetts. |Nej |
| version | Versionen av S3-objektet, om S3-versioner är aktive rad. Om detta inte anges kommer den senaste versionen att hämtas. |Nej |
| modifiedDatetimeStart | Filter för filer baserat på attributet: senast ändrad. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och. `modifiedDatetimeEnd` Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Tänk på att den övergripande prestandan för data förflyttning påverkas genom att aktivera den här inställningen när du vill göra fil filter från enorma mängder filer. <br/><br/> Egenskaperna kan vara NULL vilket innebär att inget attribut filter används för data uppsättningen.  När `modifiedDatetimeStart` har datetime-värde `modifiedDatetimeEnd` men är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime väljs.  När `modifiedDatetimeEnd` har ett datetime- `modifiedDatetimeStart` värde men är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime väljs.| Nej |
| modifiedDatetimeEnd | Filter för filer baserat på attributet: senast ändrad. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och. `modifiedDatetimeEnd` Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Tänk på att den övergripande prestandan för data förflyttning påverkas genom att aktivera den här inställningen när du vill göra fil filter från enorma mängder filer. <br/><br/> Egenskaperna kan vara NULL vilket innebär att inget attribut filter används för data uppsättningen.  När `modifiedDatetimeStart` har datetime-värde `modifiedDatetimeEnd` men är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime väljs.  När `modifiedDatetimeEnd` har ett datetime- `modifiedDatetimeStart` värde men är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime väljs.| Nej |
| format | Om du vill **Kopiera filer som är** mellan filbaserade butiker (binär kopia), hoppar du över avsnittet format i definitionerna för in-och utdata-datauppsättningar.<br/><br/>Om du vill parsa eller generera filer med ett speciellt format stöds följande fil format **typer: text**format, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange egenskapen **Type** under format till något av dessa värden. Mer information finns i [text format](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro-format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), Orc- [format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)och [Parquet format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) -avsnitt. |Nej (endast för binär kopierings scenario) |
| komprimering | Ange typ och nivå för komprimeringen för data. Mer information finns i [fil format och komprimerings-codecar som stöds](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>De typer som stöds är: **gzip**, **DEFLATE**, **BZip2**och **ZipDeflate**.<br/>De nivåer som stöds är: **optimalt** och **snabbast**. |Nej |

>[!TIP]
>Om du vill kopiera alla filer under en mapp anger du **bucketName** för Bucket och **prefixet** för en mapp.<br>Om du vill kopiera en enstaka fil med ett visst namn anger du **bucketName** för Bucket och **Key** för mapp del plus fil namn.<br>Om du vill kopiera en delmängd av filerna under en mapp, anger du **bucketName** för Bucket och **Key** för mapp del plus filter för jokertecken.

**Exempel: använda prefix**

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
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
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

**Exempel: använda nyckel och version (valfritt)**

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

### <a name="legacy-copy-activity-source-model"></a>Käll modell för äldre kopierings aktiviteter

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **FileSystemSource** |Ja |
| rekursiva | Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. OBS! om rekursivt har angetts till true och Sink är ett filbaserat Arkiv, kopieras inte den tomma mappen/undermappen till mottagaren.<br/>Tillåtna värden är: **Sant** (standard), **falskt** | Nej |
| maxConcurrentConnections | Antalet anslutningar som ska anslutas till data lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej |

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
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
