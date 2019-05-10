---
title: Kopiera data från Google Cloud Storage med Azure Data Factory | Microsoft Docs
description: Läs mer om hur du kopierar data från Google Cloud Storage till datalager för mottagare som stöds med hjälp av Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 9c9a4b41dbb9b9acc2982ae2af1f3a611f9d3beb
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228276"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Kopiera data från Google Cloud Storage med Azure Data Factory

Den här artikeln beskrivs hur du kopierar data från Google Cloud Storage. Läs om Azure Data Factory den [introduktionsartikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här anslutningen för Google Cloud Storage stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [källa/mottagare matris som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktiviteten](control-flow-get-metadata-activity.md)

Mer specifikt den här anslutningen för Google Cloud Storage stöder kopiera filer som-är eller parsningsfilerna med den [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>Kopiera data från Google Cloud Storage utnyttjar den [Amazon S3 connector](connector-amazon-simple-storage-service.md) ger S3-kompatibla samverkan med motsvarande anpassade S3-slutpunkt som Google Cloud Storage.

## <a name="required-permissions"></a>Nödvändiga behörigheter

Kopiera data från Google Cloud Storage genom att kontrollera att du har beviljats följande behörigheter:

- **För körning av kopiera aktivitet:**: `s3:GetObject` och `s3:GetObjectVersion` för objektet.
- **För redigering av Data Factory GUI**: `s3:ListAllMyBuckets` och `s3:ListBucket` / `s3:GetBucketLocation` för Bucket åtgärder behörigheter krävs dessutom för, till exempel Testa anslutning och bläddra/navigera filsökvägar. Om du inte vill att bevilja dessa behörigheter, hoppa över Testa anslutning i sidan för att skapa den länkade tjänsten och anger du sökvägen direkt i inställningarna för datauppsättningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Google Cloud Storage.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Google Cloud Storage-länkad tjänst:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till **AmazonS3**. | Ja |
| accessKeyId | ID för den hemliga åtkomstnyckeln. Du hittar åtkomstnyckeln och hemligheten genom att gå till **Google Cloud Storage** > **inställningar** > **samverkan**. |Ja |
| secretAccessKey | Den hemliga åtkomstnyckeln själva. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| serviceUrl | Anger den anpassade S3-slutpunkten som **`https://storage.googleapis.com`**. | Ja |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller lokal Integration Runtime (om ditt datalager finns i privat nätverk). Om den inte anges används standard Azure Integration Runtime. |Nej |

Här är ett exempel:

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "AmazonS3",
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

- För **Parquet och avgränsat textformat**, referera till [Parquet och avgränsad text formatera datauppsättning](#parquet-and-delimited-text-format-dataset) avsnittet.
- För andra format som **ORC/Avro/JSON/binära formatet**, referera till [andra format datauppsättning](#other-format-dataset) avsnittet.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet och avgränsad text format datauppsättning

Att kopiera data från Google Cloud Storage i **Parquet eller avgränsat textformat**, referera till [Parquet-format](format-parquet.md) och [avgränsat textformat](format-delimited-text.md) artikeln på format-baserade datauppsättning och inställningar som stöds. Följande egenskaper har stöd för Google Cloud Storage under `location` inställningar i formatet-baserade datauppsättning:

| Egenskap    | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Egenskapen type under `location` i datauppsättningen måste anges till **AmazonS3Location**. | Ja      |
| bucketName | S3-Bucketnamn.                                          | Ja      |
| folderPath | Sökvägen till mappen under en viss bucket. Om du vill använda jokertecken för att filtrera mappar hoppa över den här inställningen och ange i källinställningar för aktiviteten. | Nej       |
| fileName   | Filnamnet under den angivna bucket + folderPath. Om du vill använda jokertecken för att filtrera filerna hoppa över den här inställningen och ange i källinställningar för aktiviteten. | Nej       |

> [!NOTE]
> **AmazonS3Object** typ datauppsättning med Parquet-/ textformat som nämns i nästa avsnitt stöds fortfarande som – för kopiera/Lookup/GetMetadata-aktiviteten för bakåtkompatibilitet. Du rekommenderas för att använda den nya modellen framöver och ADF redigering Användargränssnittet har ändrats till att generera dessa nya typer.

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

### <a name="other-format-dataset"></a>Andra format-datauppsättning

Att kopiera data från Google Cloud Storage i **ORC/Avro/JSON/binära formatet**, stöds följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för datauppsättningen måste anges till: **AmazonS3Object** |Ja |
| bucketName | S3-Bucketnamn. Jokerteckenfilter stöds inte. |Ja för kopiera/Lookup-aktivitet, inte för GetMetadata-aktiviteten |
| key | Den **namn eller jokertecken-filtret** för S3 Objektnyckel under en angiven bucket. Gäller endast när egenskapen ”prefixet” inte har angetts. <br/><br/>Jokertecken-filtret har stöd för både mappen och filen Namndelen. Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken).<br/>– Exempel 1: `"key": "rootfolder/subfolder/*.csv"`<br/>– Exempel 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). Använd `^` att undvika om din faktiska mappen/filen namn innehåller jokertecken eller den här escape-tecken i. |Nej |
| prefix | Prefix för Objektnyckel S3. Objekt vars nycklar som börjar med prefixet är markerade. Gäller endast när egenskapen ”nyckeln” inte har angetts. |Nej |
| version | Versionen av S3-objektet, om S3 versionshantering är aktiverad. |Nej |
| modifiedDatetimeStart | Filter för filer baserat på attributet: Senast ändrades. Filerna markerade om deras tid för senaste ändring är inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br/><br/> Egenskaperna kan vara NULL vilket innebär att inga filfilter för attributet som ska användas för datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senaste ändrade attribut är större än eller lika med datum/tid-värde väljs.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filer vars senaste ändrade attributet är mindre än det markerade datetime-värde.| Nej |
| modifiedDatetimeEnd | Filter för filer baserat på attributet: Senast ändrades. Filerna markerade om deras tid för senaste ändring är inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br/><br/> Egenskaperna kan vara NULL vilket innebär att inga filfilter för attributet som ska användas för datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senaste ändrade attribut är större än eller lika med datum/tid-värde väljs.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filer vars senaste ändrade attributet är mindre än det markerade datetime-värde.| Nej |
| format | Om du vill **kopiera filer som – är** hoppa över avsnittet format i både inkommande och utgående datamängd definitioner mellan filbaserade (binär kopia).<br/><br/>Om du vill parsa eller generera filer med ett visst format stöds format för följande filtyper: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typ** egenskapen under format till ett av dessa värden. Mer information finns i [textformat](supported-file-formats-and-compression-codecs.md#text-format), [Json-Format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [Orc-Format](supported-file-formats-and-compression-codecs.md#orc-format), och [Parquet-Format](supported-file-formats-and-compression-codecs.md#parquet-format) avsnitt. |Nej (endast för binär kopia scenario) |
| compression | Ange typ och komprimeringsnivå för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Nivåer som stöds är: **Optimal** och **snabbaste**. |Nej |

>[!TIP]
>Kopiera alla filer i en mapp genom att ange **bucketName** för bucket och **prefix** för mappdelen.<br>Om du vill kopiera en enstaka fil med ett givet namn, ange **bucketName** för bucket och **nyckel** för en del plus filen mappnamn.<br>Om du vill kopiera en delmängd av filerna under en mapp, anger **bucketName** för bucket och **nyckel** för mappen del plus jokertecken-filtret.

**Exempel: med prefix**

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

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Google Cloud Storage källa.

### <a name="google-cloud-storage-as-source"></a>Google Cloud Storage as source

- För kopia från **Parquet och avgränsat textformat**, referera till [Parquet och avgränsad text format källa](#parquet-and-delimited-text-format-source) avsnittet.
- För kopia från andra format som **ORC/Avro/JSON/binära formatet**, referera till [annan format källa](#other-format-source) avsnittet.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet och avgränsad text format källa

Att kopiera data från Google Cloud Storage i **Parquet eller avgränsat textformat**, referera till [Parquet-format](format-parquet.md) och [avgränsat textformat](format-delimited-text.md) artikel om format-baserade Kopieringsaktivitet käll- och inställningar som stöds. Följande egenskaper har stöd för Google Cloud Storage under `storeSettings` inställningar i formatet-baserade kopieringskälla:

| Egenskap                  | Beskrivning                                                  | Krävs                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | Egenskapen type under `storeSettings` måste anges till **AmazonS3ReadSetting**. | Ja                                                         |
| recursive                | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. Observera att när rekursiv har angetts till true och mottagaren är en filbaserad store, en tom mapp eller undermapp inte kopieras eller skapat i mottagaren. Tillåtna värden är **SANT** (standard) och **FALSKT**. | Nej                                                          |
| prefix                   | Prefix för S3 objektsnyckeln under en viss bucket konfigurerats i datauppsättningen för att filtrera källobjekt. Objekt vars nycklar som börjar med prefixet är markerade. Gäller endast när `wildcardFolderPath` och `wildcardFileName` egenskaper har inte angetts. |                                                             |
| wildcardFolderPath       | Sökvägen till mappen med jokertecken under en viss bucket konfigurerats i datauppsättningen för att filtrera källa mappar. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken); Använd `^` att undvika om din faktiska mappnamn har jokertecken eller den här escape-tecken i. <br>Se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). | Nej                                                          |
| wildcardFileName         | Filnamn med jokertecken under viss bucket + folderPath/wildcardFolderPath till källfilerna för filtret. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken); Använd `^` att undvika om din faktiska mappnamn har jokertecken eller den här escape-tecken i.  Se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). | Ja om `fileName` i datauppsättningen och `prefix` har inte angetts |
| modifiedDatetimeStart    | Filter för filer baserat på attributet: Senast ändrades. Filerna markerade om deras tid för senaste ändring är inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br> Egenskaperna kan vara NULL vilket innebär att inga filfilter för attributet som ska användas för datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senaste ändrade attribut är större än eller lika med datum/tid-värde väljs.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filer vars senaste ändrade attributet är mindre än det markerade datetime-värde. | Nej                                                          |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Nej                                                          |
| maxConcurrentConnections | Antal anslutningar för att ansluta till storage store samtidigt. Ange bara när du vill begränsa samtidiga anslutningen till datalagret. | Nej                                                          |

> [!NOTE]
> För Parquet/avgränsat textformat **FileSystemSource** typen kopiera aktivitetskälla som nämns i nästa avsnitt stöds fortfarande som – är för bakåtkompatibilitet. Du rekommenderas för att använda den nya modellen framöver och ADF redigering Användargränssnittet har ändrats till att generera dessa nya typer.

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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSetting",
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

#### <a name="other-format-source"></a>Andra format-källa

Att kopiera data från Google Cloud Storage i **ORC/Avro/JSON/binära formatet**, följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **FileSystemSource** |Ja |
| recursive | Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. Obs när rekursiv har angetts till true och mottagare är filbaserade store, tom mapp/underanvändningsfall-folder kan inte kopieras/skapas vid mottagare.<br/>Tillåtna värden är: **SANT** (standard), **FALSKT** | Nej |
| maxConcurrentConnections | Antal anslutningar för att ansluta till storage store samtidigt. Ange bara när du vill begränsa samtidiga anslutningen till datalagret. | Nej |

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

### <a name="folder-and-file-filter-examples"></a>Mapp och fil filter-exempel

Det här avsnittet beskriver resultatet av sökvägen och namnet på mappen med jokertecken filter.

| Bucket | key | recursive | Käll-mappen struktur och filtrera resultatet (så hämtas filer i fetstil)|
|:--- |:--- |:--- |:--- |
| Bucket | `Folder*/*` | false | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| Bucket | `Folder*/*` | true | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| Bucket | `Folder*/*.csv` | false | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| Bucket | `Folder*/*.csv` | true | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
