---
title: Kopiera data från Google Cloud Storage med Azure Data Factory
description: Läs om hur du kopierar data från Google Cloud Storage till mottagar data lager som stöds med hjälp av Azure Data Factory.
services: data-factory
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: jingwang
ms.openlocfilehash: f6b488fc4817e360ed512441f67d2e00356e6134
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758613"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Kopiera data från Google Cloud Storage med Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du kopierar data från Google Cloud Storage (GC generationer). Läs den [inledande artikeln](introduction.md)om du vill veta mer om Azure Data Factory.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Google Cloud Storage-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)

Mer specifikt stöder den här Google Cloud Storage-anslutningen kopiering av filer som-är eller parsar filer med [fil format och komprimerings-codec som stöds](supported-file-formats-and-compression-codecs.md). Den använder GC GENERATIONERs S3-kompatibla interoperabilitet.

## <a name="prerequisites"></a>Krav

Följande konfiguration krävs på ditt Google Cloud Storage-konto:

1. Aktivera interoperabilitet för ditt Google Cloud Storage-konto
2. Ange standard projektet som innehåller de data som du vill kopiera från mål GC generationer Bucket
3. Skapa ett tjänst konto och definiera rätt behörighets nivåer med hjälp av Cloud IAM på GCP 
4. Generera åtkomst nycklar för det här tjänst kontot

![Hämta åtkomst nyckel för Google Cloud Storage](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>Nödvändiga behörigheter

Om du vill kopiera data från Google Cloud Storage kontrollerar du att du har beviljat de behörigheter som krävs. Behörigheterna som definierats i tjänst kontot kan `storage.buckets.get` innehålla `storage.buckets.list` , `storage.objects.get` för objekt åtgärder.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Google Cloud Storage.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten Google Cloud Storage:

| Egenskap | Beskrivning | Obligatorisk |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **GoogleCloudStorage**. | Ja |
| accessKeyId | ID för den hemliga åtkomst nyckeln. Se [krav](#prerequisites)för att hitta åtkomst nyckeln och hemligheten. |Ja |
| secretAccessKey | Den hemliga åtkomst nyckeln. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| serviceUrl | Ange den anpassade GC generationer-slutpunkten som **`https://storage.googleapis.com`** . | Ja |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime (om ditt data lager finns i privat nätverk). Om inget värde anges används standard Azure Integration Runtime. |Nej |

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

Följande egenskaper stöds för Google Cloud Storage under `location` Inställningar i format-baserad data mängd:

| Egenskap   | Beskrivning                                                  | Obligatorisk |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Typ egenskapen under `location` i data mängden måste anges till **GoogleCloudStorageLocation**. | Ja      |
| bucketName | GC generationer-Bucket-namn.                                          | Ja      |
| folderPath | Sökvägen till mappen under den angivna Bucket. Om du vill använda jokertecken för att filtrera mappar hoppar du över den här inställningen och anger i aktivitets källans inställningar. | Nej       |
| fileName   | Fil namnet under angivet Bucket + folderPath. Om du vill använda jokertecken för att filtrera filer, hoppar du över den här inställningen och anger i aktivitets källans inställningar. | Nej       |

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
                "type": "GoogleCloudStorageLocation",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Google Cloud Storage-källan.

### <a name="google-cloud-storage-as-source"></a>Google Cloud Storage som källa

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Google Cloud Storage under `storeSettings` Inställningar i format-baserad kopierings Källa:

| Egenskap                 | Beskrivning                                                  | Obligatorisk                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| typ                     | Typ egenskapen under `storeSettings` måste anges till **GoogleCloudStorageReadSettings**. | Ja                                                         |
| ***Leta upp de filer som ska kopieras:*** |  |  |
| ALTERNATIV 1: statisk sökväg<br> | Kopiera från den angivna Bucket-eller mappen/fil Sök vägen som anges i data uppsättningen. Om du vill kopiera alla filer från en Bucket/mapp, anger du också `wildcardFileName` som `*` . |  |
| ALTERNATIV 2: GC generationer-prefix<br>-prefix | Prefix för nyckel namnet GC generationer under den angivna Bucket som kon figurer ATS i data uppsättningen för att filtrera käll GC generationer-filer. GC generationer-nycklar vars namn börjar med `bucket_in_dataset/this_prefix` väljs. Den använder GC generationer service filter som ger bättre prestanda än filter med jokertecken. | Nej |
| ALTERNATIV 3: jokertecken<br>- wildcardFolderPath | Mappsökvägen med jokertecken under den angivna Bucket som kon figurer ATS i data uppsättningen för att filtrera källmappen för mappar. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller enstaka tecken). Använd `^` om du vill använda Escape om det faktiska mappnamnet har jokertecken eller detta escape-tecken inuti. <br>Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Nej                                            |
| ALTERNATIV 3: jokertecken<br>- wildcardFileName | Fil namnet med jokertecken under den aktuella Bucket + folderPath/wildcardFolderPath för att filtrera källfiler. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller enstaka tecken). Använd `^` om du vill använda Escape om det faktiska mappnamnet har jokertecken eller detta escape-tecken inuti.  Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Ja |
| ALTERNATIV 3: en lista över filer<br>- fileListPath | Anger om du vill kopiera en angiven fil uppsättning. Peka på en textfil som innehåller en lista över filer som du vill kopiera, en fil per rad som är den relativa sökvägen till den sökväg som kon figurer ATS i data uppsättningen.<br/>När du använder det här alternativet ska du inte ange fil namn i data uppsättning. Se fler exempel i [fil List exempel](#file-list-examples). |Nej |
| ***Ytterligare inställningar:*** |  | |
| rekursiva | Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. Observera att när rekursivt har angetts till true och sinken är en filbaserad lagring, kopieras inte en tom mapp eller undermapp till mottagaren. <br>Tillåtna värden är **True** (standard) och **false**.<br>Den här egenskapen gäller inte när du konfigurerar `fileListPath` . |Nej |
| modifiedDatetimeStart    | Filter för filer baserat på attributet: senast ändrad. <br>Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd` . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br> Egenskaperna kan vara NULL vilket innebär att inget attribut filter används för data uppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime väljs.  När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime väljs.<br/>Den här egenskapen gäller inte när du konfigurerar `fileListPath` . | Nej                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Nej                                                          |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till lagrings lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej                                                          |

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
                    "type": "GoogleCloudStorageReadSettings",
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

| Bucket | nyckel | rekursiva | Källans mappstruktur och filter resultat (filer i fetstil hämtas)|
|:--- |:--- |:--- |:--- |
| Bucket | `Folder*/*` | falskt | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fil2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| Bucket | `Folder*/*` | true | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fil2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| Bucket | `Folder*/*.csv` | falskt | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| Bucket | `Folder*/*.csv` | true | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="file-list-examples"></a>Exempel på fil listor

I det här avsnittet beskrivs det resulterande beteendet för att använda fil List Sök vägen i kopierings aktivitets källan.

Vi antar att du har följande mappstruktur och vill kopiera filerna i fetstil:

| Exempel på käll struktur                                      | Innehåll i FileListToCopy. txt                             | ADF-konfiguration                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadatatjänst<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy. txt | Fil1. csv<br>Subfolder1/File3. csv<br>Subfolder1/File5. csv | **I data uppsättning:**<br>Bucket`bucket`<br>– Mappsökväg:`FolderA`<br><br>**I kopierings aktivitets Källa:**<br>– Sökväg till fil lista:`bucket/Metadata/FileListToCopy.txt` <br><br>Sökvägen till fil listan pekar på en textfil i samma data lager som innehåller en lista över filer som du vill kopiera, en fil per rad med den relativa sökvägen till den sökväg som kon figurer ATS i data uppsättningen. |

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Egenskaper för GetMetadata-aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [getMetaData-aktivitet](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Ta bort aktivitets egenskaper

Om du vill veta mer om egenskaperna kontrollerar du [ta bort aktivitet](delete-activity.md)

## <a name="legacy-models"></a>Äldre modeller

Om du använde Amazon S3-koppling för att kopiera data från Google Cloud Storage, stöds det fortfarande som är för bakåtkompatibilitet. Du rekommenderar att du använder den nya modellen som anges ovan och fortsätter, och redigerings gränssnittet för ADF har växlat till att generera den nya modellen.

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
