---
title: Kopiera data från Amazon Simple Storage Service (S3)
description: Lär dig mer om hur du kopierar data från Amazon Simple Storage Service (S3) till sink-datalager som stöds med hjälp av Azure Data Factory.
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
ms.openlocfilehash: 56cc7425eea184cd26010cde48e42e38b27e68a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75893286"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Kopiera data från Amazon Simple Storage Service med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
>
> * [Version 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Aktuell version](connector-amazon-simple-storage-service.md)

Denna artikel beskriver hur man kopierar data från Amazon Simple Storage Service (Amazon S3). Mer information om Azure Data Factory finns i den [inledande artikeln](introduction.md).

>[!TIP]
>För scenario med datamigrering från Amazon S3 till Azure Storage kan du läsa mer från [Använd Azure Data Factory för att migrera data från Amazon S3 till Azure Storage](data-migration-guidance-s3-azure-storage.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Denna Amazon S3-kontakt stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)

Specifikt stöder denna Amazon S3-anslutning kopiera filer som det är eller tolka filer med [de filformat och komprimeringskodicer](supported-file-formats-and-compression-codecs.md)som stöds. Du kan också välja att [bevara filmetadata under kopieringen](#preserve-metadata-during-copy). Anslutningen använder [AWS Signature Version 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) för att autentisera begäranden till S3.

>[!TIP]
>Du kan använda den här Amazon S3-kontakten för att kopiera data från **alla S3-kompatibla lagringsleverantörer,** till exempel [Google Cloud Storage](connector-google-cloud-storage.md). Ange motsvarande tjänst-URL i den länkade tjänstkonfigurationen.

## <a name="required-permissions"></a>Nödvändiga behörigheter

Om du vill kopiera data från Amazon S3 kontrollerar du att du har beviljats följande behörigheter:

- **För kopiering av aktivitetskörning:**: `s3:GetObject` och `s3:GetObjectVersion` för Amazon S3 Object Operations.
- För Data Factory GUI `s3:ListAllMyBuckets` `s3:ListBucket` / `s3:GetBucketLocation` **redigering:** och för Amazon S3 Bucket Operations behörigheter krävs dessutom för åtgärder som testanslutning och bläddra / navigera filsökvägar. Om du inte vill bevilja dessa behörigheter hoppar du över testanslutningen på den länkade sidan för att skapa tjänster och ange sökvägen direkt i datauppsättningsinställningarna.

Mer information om den fullständiga listan över Behörigheter för Amazon S3 finns [i Ange behörigheter i en princip](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Amazon S3.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Amazon S3-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste ställas in på **AmazonS3**. | Ja |
| accessKeyId | ID för den hemliga åtkomstnyckeln. |Ja |
| secretAccessKey | Den hemliga åtkomstnyckeln själv. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| serviceUrl | Ange den anpassade S3-slutpunkten om du kopierar data från en S3-kompatibel lagringsleverantör än den officiella Amazon S3-tjänsten. Om du till exempel vill kopiera `https://storage.googleapis.com`data från Google Cloud Storage anger du . | Inga |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted Integration Runtime (om ditt datalager finns i privat nätverk). Om det inte anges används standardkörningen för Azure Integration. |Inga |

>[!TIP]
>Ange den anpassade S3-tjänstadressen om du kopierar data från en S3-kompatibel lagring än den officiella Amazon S3-tjänsten.

>[!NOTE]
>Den här anslutningen kräver åtkomstnycklar för IAM-konto för att kopiera data från Amazon S3. [Tillfällig säkerhetsautentisering](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) stöds inte.
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Datauppsättningar.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Amazon `location` S3 under inställningar i formatbaserad datauppsättning:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Egenskapen type `location` under in dataset måste anges till **AmazonS3Location**. | Ja      |
| bucketName (bucketName) | S3-hinknamnet.                                          | Ja      |
| folderPath | Sökvägen till mappen under den angivna bucketen. Om du vill använda jokertecken för att filtrera mappen hoppar du över den här inställningen och anger i inställningarna för aktivitetskällan. | Inga       |
| fileName   | Filnamnet under den angivna bucketen + folderPath. Om du vill använda jokertecken för att filtrera filer hoppar du över den här inställningen och anger i inställningarna för aktivitetskällan. | Inga       |
| version | Versionen av S3-objektet, om S3-versionshantering är aktiverad. Om inget anges hämtas den senaste versionen. |Inga |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Amazon S3-källa.

### <a name="amazon-s3-as-source"></a>Amazon S3 som källa

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Amazon `storeSettings` S3 under inställningar i formatbaserad kopieringskälla:

| Egenskap                 | Beskrivning                                                  | Krävs                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| typ                     | Egenskapen type `storeSettings` under måste ställas in på **AmazonS3ReadSettings**. | Ja                                                         |
| Rekursiv                | Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. Observera att när rekursiv är inställd på true och diskhon är ett filbaserat arkiv kopieras eller skapas inte en tom mapp eller undermapp i diskhon. Tillåtna värden är **sanna** (standard) och **falska**. | Inga                                                          |
| Prefix                   | Prefix för S3-objektnyckeln under den angivna bucketen som konfigurerats i datauppsättningen för att filtrera källobjekt. Objekt vars nycklar börjar med det här prefixet är markerade. <br>Gäller endast `wildcardFolderPath` `wildcardFileName` när och egenskaper inte har angetts. | Inga                                                          |
| jokerteckenMappspath       | Mappsökvägen med jokertecken under den angivna bucketen som konfigurerats i datauppsättningen för att filtrera källmappar. <br>Tillåtna jokertecken `*` är: (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken); används `^` för att fly om ditt faktiska mappnamn har jokertecken eller den här flyktteckenet inuti. <br>Se fler exempel i [exempel på mapp- och filfilter](#folder-and-file-filter-examples). | Inga                                                          |
| jokerteckenFileName         | Filnamnet med jokertecken under den angivna bucketen + folderPath/wildcardFolderPath för att filtrera källfiler. <br>Tillåtna jokertecken `*` är: (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken); används `^` för att fly om ditt faktiska mappnamn har jokertecken eller den här flyktteckenet inuti.  Se fler exempel i [exempel på mapp- och filfilter](#folder-and-file-filter-examples). | Ja `fileName` om i datauppsättning och `prefix` inte anges |
| modifiedDatetimeStart    | Filfilter baserat på attributet: Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` ligger `modifiedDatetimeEnd`inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br> Egenskaperna kan vara NULL, vilket innebär att inget filattributfilter tillämpas på datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` null betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet kommer att väljas.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` null betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet kommer att väljas. | Inga                                                          |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Inga                                                          |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till lagringsarkivet samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga                                                          |

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

### <a name="folder-and-file-filter-examples"></a>Exempel på mapp- och filfilter

I det här avsnittet beskrivs det resulterande beteendet för mappsökvägen och filnamnet med jokerteckenfilter.

| Hink | key | Rekursiv | Källmappstruktur och filterresultat (filer i fetstil hämtas)|
|:--- |:--- |:--- |:--- |
| Hink | `Folder*/*` | false | Hink<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| Hink | `Folder*/*` | true | Hink<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| Hink | `Folder*/*.csv` | false | Hink<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| Hink | `Folder*/*.csv` | true | Hink<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |

## <a name="preserve-metadata-during-copy"></a>Bevara metadata under kopiering

När du kopierar filer från Amazon S3 till Azure Data Lake Storage Gen2/Azure Blob kan du välja att bevara filmetadata tillsammans med data. Läs mer från [Bevara metadata](copy-activity-preserve-metadata.md#preserve-metadata).

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
| version | Versionen av S3-objektet, om S3-versionshantering är aktiverad. Om inget anges hämtas den senaste versionen. |Inga |
| modifiedDatetimeStart | Filfilter baserat på attributet: Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` ligger `modifiedDatetimeEnd`inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Tänk på att den övergripande prestandan för data förflyttning kommer att påverkas genom att aktivera den här inställningen när du vill göra filfilter från stora mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget filattributfilter tillämpas på datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` null betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet kommer att väljas.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` null betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet kommer att väljas.| Inga |
| modifiedDatetimeEnd | Filfilter baserat på attributet: Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` ligger `modifiedDatetimeEnd`inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Tänk på att den övergripande prestandan för data förflyttning kommer att påverkas genom att aktivera den här inställningen när du vill göra filfilter från stora mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget filattributfilter tillämpas på datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` null betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet kommer att väljas.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` null betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet kommer att väljas.| Inga |
| format | Om du vill **kopiera filer som de är** mellan filbaserade butiker (binär kopia) hoppar du över formatavsnittet i definitionerna för både in- och utdatadata.<br/><br/>Om du vill tolka eller generera filer med ett visst format stöds följande filformattyper: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange **type** typegenskapen under format till ett av dessa värden. Mer information finns i avsnitten [Textformat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Json Format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)och [Parquet Format.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Nej (endast för binärt kopieringsscenario) |
| komprimering | Ange typ och komprimeringsnivå för data. Mer information finns i [Filformat och komprimeringskodicer som stöds](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Typer som stöds är: **GZip**, **Deflate**, **BZip2**och **ZipDeflate**.<br/>Nivåer som stöds är: **Optimal** och **snabbaste**. |Inga |

>[!TIP]
>Om du vill kopiera alla filer under en mapp anger du **bucketName** för bucket och **prefix** för mappdel.<br>Om du vill kopiera en enskild fil med ett förnamn anger du **bucketName** för bucket och **key** för mappdel plus filnamn.<br>Om du vill kopiera en delmängd filer under en mapp anger du **bucketName** för bucket och **key** för mappdel plus jokerteckenfilter.

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

### <a name="legacy-copy-activity-source-model"></a>Källmodell för äldre kopieringsaktivitet

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste anges till: **FileSystemSource** |Ja |
| Rekursiv | Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. Observera att när rekursiv är inställd på true och sink är filbaserad butik, kopieras/skapas inte den tomma mappen/undermappen vid diskhon.<br/>Tillåtna värden är: **sant** (standard), **falskt** | Inga |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till datalagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga |

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
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
