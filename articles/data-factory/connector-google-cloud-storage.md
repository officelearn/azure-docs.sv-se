---
title: Kopiera data från Google Cloud Storage med Azure Data Factory
description: Lär dig mer om hur du kopierar data från Google Cloud Storage till sink-datalager som stöds med hjälp av Azure Data Factory.
services: data-factory
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 2ee0fc7f7b7e3ef465a43eed2bd47f33e87162b7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417309"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Kopiera data från Google Cloud Storage med Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du kopierar data från Google Cloud Storage. Mer information om Azure Data Factory finns i den [inledande artikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Google Cloud Storage-anslutningen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)

Specifikt stöder den här Google Cloud Storage-anslutningen att kopiera filer som de är eller tolka filer med [filformat och komprimeringskodicer som stöds](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>Genom att kopiera data från Google Cloud Storage utnyttjas [Amazon S3-kontakten](connector-amazon-simple-storage-service.md) med motsvarande anpassade S3-slutpunkt, eftersom Google Cloud Storage ger S3-kompatibel interoperabilitet.

## <a name="prerequisites"></a>Krav

Följande inställning krävs i ditt Google Cloud Storage-konto:

1. Aktivera interoperabilitet för ditt Google Cloud Storage-konto
2. Ange standardprojektet som innehåller de data som du vill kopiera
3. Skapa en åtkomstnyckel.

![Hämta åtkomstnyckel för Google Cloud Storage](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>Nödvändiga behörigheter

Om du vill kopiera data från Google Cloud Storage kontrollerar du att du har beviljats följande behörigheter:

- **För kopieringsaktivitetskörning:**: `s3:GetObject` och `s3:GetObjectVersion` för objektoperationer.
- **För datafabriks-GUI-redigering:** `s3:ListAllMyBuckets` och `s3:ListBucket` / `s3:GetBucketLocation` för bucket operations-behörigheter krävs dessutom för åtgärder som testanslutning och bläddra/navigera på filsökvägar. Om du inte vill bevilja dessa behörigheter hoppar du över testanslutningen på den länkade sidan för att skapa tjänster och ange sökvägen direkt i datauppsättningsinställningarna.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för Google Cloud Storage.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den länkade tjänsten Google Cloud Storage:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste vara inställd på **GoogleCloudStorage**. | Ja |
| accessKeyId | ID för den hemliga åtkomstnyckeln. Information om hur du hittar åtkomstnyckeln och hemligheten finns i [Förutsättningar](#prerequisites). |Ja |
| secretAccessKey | Den hemliga åtkomstnyckeln själv. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| serviceUrl | Ange den anpassade S3-ändpunkten som **`https://storage.googleapis.com`**. | Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted Integration Runtime (om ditt datalager finns i privat nätverk). Om det inte anges används standardkörningen för Azure Integration. |Inga |

Här är ett exempel:

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "GoogleCloudStorage",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Google `location` Cloud Storage under inställningar i formatbaserad datauppsättning:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Egenskapen type `location` under in dataset måste anges till **AmazonS3Location**. | Ja      |
| bucketName (bucketName) | S3-hinknamnet.                                          | Ja      |
| folderPath | Sökvägen till mappen under den angivna bucketen. Om du vill använda jokertecken för att filtrera mappen hoppar du över den här inställningen och anger i inställningarna för aktivitetskällan. | Inga       |
| fileName   | Filnamnet under den angivna bucketen + folderPath. Om du vill använda jokertecken för att filtrera filer hoppar du över den här inställningen och anger i inställningarna för aktivitetskällan. | Inga       |

**Exempel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Google Cloud Storage linked service name>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Google Cloud Storage-källa.

### <a name="google-cloud-storage-as-source"></a>Google Cloud Storage som källa

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Google `storeSettings` Cloud Storage under inställningar i formatbaserad kopieringskälla:

| Egenskap                 | Beskrivning                                                  | Krävs                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| typ                     | Egenskapen type `storeSettings` under måste ställas in på **AmazonS3ReadSettings**. | Ja                                                         |
| Rekursiv                | Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. Observera att när rekursiv är inställd på true och diskhon är ett filbaserat arkiv kopieras eller skapas inte en tom mapp eller undermapp i diskhon. Tillåtna värden är **sanna** (standard) och **falska**. | Inga                                                          |
| Prefix                   | Prefix för S3-objektnyckeln under den angivna bucketen som konfigurerats i datauppsättningen för att filtrera källobjekt. Objekt vars nycklar börjar med det här prefixet är markerade. Gäller endast `wildcardFolderPath` `wildcardFileName` när och egenskaper inte har angetts. |                                                             |
| jokerteckenMappspath       | Mappsökvägen med jokertecken under den angivna bucketen som konfigurerats i datauppsättningen för att filtrera källmappar. <br>Tillåtna jokertecken `*` är: (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken); används `^` för att fly om ditt faktiska mappnamn har jokertecken eller den här flyktteckenet inuti. <br>Se fler exempel i [exempel på mapp- och filfilter](#folder-and-file-filter-examples). | Inga                                                          |
| jokerteckenFileName         | Filnamnet med jokertecken under den angivna bucketen + folderPath/wildcardFolderPath för att filtrera källfiler. <br>Tillåtna jokertecken `*` är: (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken); används `^` för att fly om ditt faktiska mappnamn har jokertecken eller den här flyktteckenet inuti.  Se fler exempel i [exempel på mapp- och filfilter](#folder-and-file-filter-examples). | Ja `fileName` om i datauppsättning och `prefix` inte anges |
| modifiedDatetimeStart    | Filfilter baserat på attributet: Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` ligger `modifiedDatetimeEnd`inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br> Egenskaperna kan vara NULL, vilket innebär att inget filattributfilter tillämpas på datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` null betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet kommer att väljas.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` null betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet kommer att väljas. | Inga                                                          |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Inga                                                          |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till lagringsarkivet samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga                                                          |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
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

### <a name="folder-and-file-filter-examples"></a>Exempel på mapp- och filfilter

I det här avsnittet beskrivs det resulterande beteendet för mappsökvägen och filnamnet med jokerteckenfilter.

| Hink | key | Rekursiv | Källmappstruktur och filterresultat (filer i fetstil hämtas)|
|:--- |:--- |:--- |:--- |
| Hink | `Folder*/*` | false | Hink<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| Hink | `Folder*/*` | true | Hink<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| Hink | `Folder*/*.csv` | false | Hink<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| Hink | `Folder*/*.csv` | true | Hink<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Aktivitetsegenskaper för GetMetadata

Om du vill veta mer om egenskaperna kontrollerar du [GetMetadata-aktivitet](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Ta bort aktivitetsegenskaper

Om du vill veta mer om egenskaperna kontrollerar du [Ta bort aktivitet](delete-activity.md)

## <a name="legacy-models"></a>Äldre modeller

>[!NOTE]
>Följande modeller stöds fortfarande i sig för bakåtkompatibilitet. Du föreslås använda den nya modellen som nämns i ovanstående avsnitt framöver, och ADF-redigeringsgränssnittet har bytt till att generera den nya modellen.

### <a name="legacy-dataset-model"></a>Äldre datauppsättningsmodell

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen för datauppsättningen måste ställas in på: **AmazonS3Object** |Ja |
| bucketName (bucketName) | S3-hinknamnet. Jokerteckenfiltret stöds inte. |Ja för kopierings-/uppslagsaktivitet, Nej för GetMetadata-aktivitet |
| key | **Namn- eller jokerteckenfiltret för** S3-objektnyckeln under den angivna bucketen. Gäller endast när egenskapen Prefix inte har angetts. <br/><br/>Jokerteckenfiltret stöds för både mappdelen och filnamnsdelen. Tillåtna jokertecken `*` är: (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken).<br/>- Exempel 1:`"key": "rootfolder/subfolder/*.csv"`<br/>- Exempel 2:`"key": "rootfolder/subfolder/???20180427.txt"`<br/>Se fler exempel i [exempel på mapp- och filfilter](#folder-and-file-filter-examples). Används `^` för att fly om ditt faktiska mapp-/filnamn har jokertecken eller den här flykttecknet inuti. |Inga |
| Prefix | Prefix för S3-objektnyckeln. Objekt vars nycklar börjar med det här prefixet är markerade. Gäller endast när egenskapen "nyckel" inte har angetts. |Inga |
| version | Versionen av S3-objektet, om S3-versionshantering är aktiverad. |Inga |
| modifiedDatetimeStart | Filfilter baserat på attributet: Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` ligger `modifiedDatetimeEnd`inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget filattributfilter tillämpas på datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` null betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet kommer att väljas.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` null betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet kommer att väljas.| Inga |
| modifiedDatetimeEnd | Filfilter baserat på attributet: Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` ligger `modifiedDatetimeEnd`inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget filattributfilter tillämpas på datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` null betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet kommer att väljas.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` null betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet kommer att väljas.| Inga |
| format | Om du vill **kopiera filer som de är** mellan filbaserade butiker (binär kopia) hoppar du över formatavsnittet i definitionerna för både in- och utdatadata.<br/><br/>Om du vill tolka eller generera filer med ett visst format stöds följande filformattyper: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange **type** typegenskapen under format till ett av dessa värden. Mer information finns i avsnitten [Textformat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Json Format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)och [Parquet Format.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Nej (endast för binärt kopieringsscenario) |
| komprimering | Ange typ och komprimeringsnivå för data. Mer information finns i [Filformat och komprimeringskodicer som stöds](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Typer som stöds är: **GZip**, **Deflate**, **BZip2**och **ZipDeflate**.<br/>Nivåer som stöds är: **Optimal** och **snabbaste**. |Inga |

>[!TIP]
>Om du vill kopiera alla filer under en mapp anger du **bucketName** för bucket och **prefix** för mappdel.<br>Om du vill kopiera en enskild fil med ett förnamn anger du **bucketName** för bucket och **key** för mappdel plus filnamn.<br>Om du vill kopiera en delmängd filer under en mapp anger du **bucketName** för bucket och **key** för mappdel plus jokerteckenfilter.

**Exempel: använda prefix**

```json
{
    "name": "GoogleCloudStorageDataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<linked service name>",
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

### <a name="legacy-copy-activity-source-model"></a>Källmodell för äldre kopieringsaktivitet

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste anges till: **FileSystemSource** |Ja |
| Rekursiv | Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. Observera att när rekursiv är inställd på true och sink är filbaserad butik, kopieras/skapas inte den tomma mappen/undermappen vid diskhon.<br/>Tillåtna värden är: **sant** (standard), **falskt** | Inga |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till lagringsarkivet samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
