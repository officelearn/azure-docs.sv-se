---
title: Kopiera data från/till ett fil system med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från fil systemet till mottagar data lager (eller) från käll data lager som stöds till fil systemet med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 6b1e1dfec69d73b7fe2648a1eb9ead2ae4622bc5
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72897750"
---
# <a name="copy-data-to-or-from-a-file-system-by-using-azure-data-factory"></a>Kopiera data till eller från ett fil system med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-onprem-file-system-connector.md)
> * [Aktuell version](connector-file-system.md)

Den här artikeln beskriver hur du kopierar data till och från fil systemet. Läs den [inledande artikeln](introduction.md)om du vill veta mer om Azure Data Factory.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här fil Systems anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)

Mer specifikt stöder denna fil system anslutning:

- Kopiera filer från/till lokal dator eller nätverks fil resurs. Om du vill använda en Linux-filresurs installerar du [Samba](https://www.samba.org/) på Linux-servern.
- Kopiera filer med **Windows** -autentisering.
- Kopiera filer som-är eller parsa/generera filer med de [fil format och komprimerings-codec som stöds](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för fil system.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade fil system tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **fileserver**. | Ja |
| värd | Anger rot Sök vägen till den mapp som du vill kopiera. Använd escape-tecknet "\" för specialtecken i strängen. Se [exempel på länkade tjänst-och data uppsättnings definitioner](#sample-linked-service-and-dataset-definitions) . | Ja |
| userid | Ange ID: t för den användare som har åtkomst till servern. | Ja |
| lösenord | Ange lösen ordet för användaren (UserID). Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Läs mer från avsnittet [krav](#prerequisites) . Om inget värde anges används standard Azure Integration Runtime. |Nej |

### <a name="sample-linked-service-and-dataset-definitions"></a>Exempel på länkade tjänst-och data uppsättnings definitioner

| Scenario | "värd" i den länkade tjänst definitionen | "folderPath" i definition för data uppsättning |
|:--- |:--- |:--- |
| Lokal mapp på Integration Runtime dator: <br/><br/>Exempel: D:\\\* eller D:\folder\subfolder\\* |I JSON: `D:\\`<br/>I UI: `D:\` |I JSON: `.\\` eller `folder\\subfolder`<br>I UI: `.\` eller `folder\subfolder` |
| Fjärran sluten delad mapp: <br/><br/>Exempel: \\\\Server\\resurs\\\* eller \\\\Server\\dela\\mapp\\\\ |I JSON: `\\\\myserver\\share`<br/>I UI: `\\myserver\share` |I JSON: `.\\` eller `folder\\subfolder`<br/>I UI: `.\` eller `folder\subfolder` |

>[!NOTE]
>När du redigerar via användar gränssnittet behöver du inte ange dubbelt omvänt snedstreck (`\\`) för att undvika att du gör via JSON, anger enkelt omvänt snedstreck.

**Exempel:**

```json
{
    "name": "FileLinkedService",
    "properties": {
        "type": "FileServer",
        "typeProperties": {
            "host": "<host>",
            "userid": "<domain>\\<user>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för data mängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . 

- För **Parquet, avgränsade text-, JSON-, Avro-och binärformat**, se [Parquet, delimited text-, JSON-, Avro-och binära format-datauppsättning](#format-based-dataset) .
- Information om andra format som **Orc-format**finns i avsnittet [annan format data uppsättning](#other-format-dataset) .

### <a name="format-based-dataset"></a>Data uppsättning för Parquet, avgränsad text, JSON, Avro och binärt format

Om du vill kopiera data till och från **Parquet, avgränsade text-, JSON-, Avro-och binärformat**, se [Parquet format](format-parquet.md), [avgränsat text format](format-delimited-text.md), [Avro format](format-avro.md) och [binärt format](format-binary.md) artikel i format-baserad data uppsättning och inställningar som stöds . Följande egenskaper stöds för fil systemet under `location`-inställningar i format-baserad data mängd:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Egenskapen Type under `location` i dataset måste anges till **FileServerLocation**. | Ja      |
| folderPath | Sökvägen till mappen. Om du vill använda jokertecken för att filtrera mappar hoppar du över den här inställningen och anger i aktivitets källans inställningar. | Nej       |
| fileName   | Fil namnet under den aktuella folderPath. Om du vill använda jokertecken för att filtrera filer, hoppar du över den här inställningen och anger i aktivitets källans inställningar. | Nej       |

> [!NOTE]
> Data uppsättning för **fileshare** -typ med Parquet/text format som nämns i nästa avsnitt stöds fortfarande som-är för aktivitet för kopiering/sökning/getMetaData för bakåtkompatibilitet, men fungerar inte med data flödet för mappning. Du rekommenderas att använda den här nya modellen för att vidarebefordra och användar gränssnittet för ADF-redigering har växlat till att generera dessa nya typer.

**Exempel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<File system linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "FileServerLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Data uppsättning för andra format

Följande egenskaper stöds för att kopiera data till och från fil systemet i **Orc-format**:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **fileshare** |Ja |
| folderPath | Sökväg till mappen. Wildcard-filtret stöds, tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Använd `^` för att kringgå om det faktiska mappnamnet har jokertecken eller det här Escape-char-namnet inuti. <br/><br/>Exempel: RootFolder/undermapp/, se fler exempel i exempel [länkade tjänst-och data uppsättnings definitioner](#sample-linked-service-and-dataset-definitions) och [exempel på mapp-och fil filter](#folder-and-file-filter-examples). |Nej |
| fileName | **Namn eller Wildcard-filter** för filen/filerna under den angivna "folderPath". Om du inte anger ett värde för den här egenskapen pekar data uppsättningen på alla filer i mappen. <br/><br/>För filter är tillåtna jokertecken: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken).<br/>-Exempel 1: `"fileName": "*.csv"`<br/>-Exempel 2: `"fileName": "???20180427.txt"`<br/>Använd `^` för att kringgå om det faktiska fil namnet har jokertecken eller detta escape-tecken inuti.<br/><br/>När fil namnet inte har angetts för en data uppsättning för utdata och **preserveHierarchy** inte har angetts i aktivitets mottagaren genererar kopierings aktiviteten automatiskt fil namnet med följande mönster: "*data. [ aktivitetens körnings-ID GUID]. [GUID om FlattenHierarchy]. [format om det är konfigurerat]. [komprimering om konfigurerad]* ", t. ex." data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz "; Om du kopierar från tabell källa med tabell namn i stället för fråga, är namn mönstret " *[tabell namn]. [ format]. [komprimering om konfigurerad]* ", t. ex." Table. csv ". |Nej |
| modifiedDatetimeStart | Filter för filer baserat på attributet: senast ändrad. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Tänk på att den övergripande prestandan för data förflyttning påverkas genom att aktivera den här inställningen när du vill göra fil filter från enorma mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget filter för filattribut används för data uppsättningen.  När `modifiedDatetimeStart` har datetime-värdet men `modifiedDatetimeEnd` är NULL, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime väljs.  När `modifiedDatetimeEnd` har datetime-värdet men `modifiedDatetimeStart` är NULL, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime väljs.| Nej |
| modifiedDatetimeEnd | Filter för filer baserat på attributet: senast ändrad. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Tänk på att den övergripande prestandan för data förflyttning påverkas genom att aktivera den här inställningen när du vill göra fil filter från enorma mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget filter för filattribut används för data uppsättningen.  När `modifiedDatetimeStart` har datetime-värdet men `modifiedDatetimeEnd` är NULL, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime väljs.  När `modifiedDatetimeEnd` har datetime-värdet men `modifiedDatetimeStart` är NULL, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime väljs.| Nej |
| formatering | Om du vill **Kopiera filer som är** mellan filbaserade butiker (binär kopia), hoppar du över avsnittet format i definitionerna för in-och utdata-datauppsättningar.<br/><br/>Om du vill parsa eller generera filer med ett speciellt format stöds följande fil format **typer: text**format, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange egenskapen **Type** under format till något av dessa värden. Mer information finns i [text format](supported-file-formats-and-compression-codecs.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-format](supported-file-formats-and-compression-codecs.md#avro-format), Orc- [format](supported-file-formats-and-compression-codecs.md#orc-format)och [Parquet format](supported-file-formats-and-compression-codecs.md#parquet-format) -avsnitt. |Nej (endast för binär kopierings scenario) |
| komprimering | Ange typ och nivå för komprimeringen för data. Mer information finns i [fil format och komprimerings-codecar som stöds](supported-file-formats-and-compression-codecs.md#compression-support).<br/>De typer som stöds är: **gzip**, **DEFLATE**, **BZip2**och **ZipDeflate**.<br/>De nivåer som stöds är: **optimalt** och **snabbast**. |Nej |

>[!TIP]
>Om du vill kopiera alla filer under en mapp anger du endast **folderPath** .<br>Om du vill kopiera en enskild fil med ett visst namn anger du **folderPath** med en **mapp och ett fil namn** med fil namnet.<br>Om du vill kopiera en delmängd av filer under en mapp anger du **folderPath** med en mapp och ett **fil namns** filter med jokertecken.

>[!NOTE]
>Om du använder egenskapen "fileFilter" för fil filter, stöds den fortfarande som det är, medan du föreslås att använda den nya filter funktionen som har lagts till i "fileName".

**Exempel:**

```json
{
    "name": "FileSystemDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<file system linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av fil system källa och mottagare.

### <a name="file-system-as-source"></a>Fil system som källa

- Om du vill kopiera från **Parquet, avgränsade text-, JSON-, Avro-och binärformat**, se avsnittet [Parquet, avgränsad text, JSON, Avro och binärt format](#format-based-source) .
- Om du vill kopiera från andra format som **Orc-format**, se avsnittet [annan format källa](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, avgränsad text, JSON, Avro och binär format källa

Om du vill kopiera data från **Parquet, avgränsade text-, JSON-, Avro-och binärformat**, se [Parquet-format](format-parquet.md), [avgränsat text format](format-delimited-text.md), [Avro format](format-avro.md) och [binära format](format-binary.md) artikel för den formatbaserade kopierings aktivitets källan och stöds autentiseringsinställningar. Följande egenskaper stöds för fil systemet under `storeSettings`-inställningar i format-baserad kopierings Källa:

| Egenskap                 | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Egenskapen Type under `storeSettings` måste anges till **FileServerReadSetting**. | Ja                                           |
| rekursiva                | Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. Observera att när rekursivt har angetts till true och sinken är en filbaserad lagring, kopieras inte en tom mapp eller undermapp till mottagaren. Tillåtna värden är **True** (standard) och **false**. | Nej                                            |
| wildcardFolderPath       | Mappsökvägen med jokertecken för att filtrera källmappen. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Använd `^` för att kringgå om det faktiska mappnamnet har jokertecken eller det här Escape-char-namnet inuti. <br>Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Nej                                            |
| wildcardFileName         | Fil namnet med jokertecken under den aktuella folderPath/wildcardFolderPath för att filtrera källfiler. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Använd `^` för att kringgå om det faktiska mappnamnet har jokertecken eller det här Escape-char-namnet inuti.  Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). [Observera att detta kommer att åsidosätta ditt fil namns inställning] | Ja om `fileName` inte anges i data uppsättningen |
| modifiedDatetimeStart    | Filter för filer baserat på attributet: senast ändrad. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br> Egenskaperna kan vara NULL vilket innebär att inget attribut filter används för data uppsättningen.  När `modifiedDatetimeStart` har datetime-värdet men `modifiedDatetimeEnd` är NULL, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime väljs.  När `modifiedDatetimeEnd` har datetime-värdet men `modifiedDatetimeStart` är NULL, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime väljs. | Nej                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Nej                                            |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till lagrings lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej                                            |

> [!NOTE]
> För Parquet/avgränsat text format stöds **FileSystemSource** typ kopierings aktivitet som anges i nästa avsnitt fortfarande som-är för bakåtkompatibilitet. Du rekommenderas att använda den här nya modellen för att vidarebefordra och användar gränssnittet för ADF-redigering har växlat till att generera dessa nya typer.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
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
                    "type": "FileServerReadSetting",
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

#### <a name="other-format-source"></a>Annan format källa

Om du vill kopiera data från fil systemet i **Orc-format**, stöds följande egenskaper i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **FileSystemSource** |Ja |
| rekursiva | Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. OBS! om rekursivt har angetts till true och Sink är ett filbaserat Arkiv, kopieras inte den tomma mappen/undermappen till mottagaren.<br/>Tillåtna värden är: **Sant** (standard), **falskt** | Nej |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till lagrings lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<file system input dataset name>",
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

### <a name="file-system-as-sink"></a>Fil system som mottagare

- Om du vill kopiera till **Parquet, avgränsad text, JSON, Avro och binärt format**, se avsnittet [Parquet, avgränsad text, JSON, Avro och binärt format](#format-based-sink) .
- Om du vill kopiera till andra format som **Orc-format**, se avsnittet [annat format mottagare](#other-format-sink) .

#### <a name="format-based-sink"></a>Parquet, avgränsad text, JSON, Avro och binärt format mottagare

Om du vill kopiera data i **Parquet, avgränsade text-, JSON-, Avro-och binärformat**, se [Parquet-format](format-parquet.md), [avgränsat text format](format-delimited-text.md), [Avro format](format-avro.md) och [binärt format](format-binary.md) artikel med format-baserad kopierings aktivitet, Sink och stöd autentiseringsinställningar. Följande egenskaper stöds för fil systemet under `storeSettings`-inställningar i format-baserad kopierings mottagare:

| Egenskap                 | Beskrivning                                                  | Krävs |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | Egenskapen Type under `storeSettings` måste anges till **FileServerWriteSetting**. | Ja      |
| copyBehavior             | Definierar kopierings beteendet när källan är filer från ett filbaserat data lager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: bevarar filens hierarki i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><b>-FlattenHierarchy</b>: alla filer från källmappen är på den första nivån i målmappen. Filerna har automatiskt genererade namn. <br/><b>-MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om fil namnet anges, är det sammanslagna fil namnet det angivna namnet. Annars är det ett automatiskt genererat fil namn. | Nej       |
| maxConcurrentConnections | Antalet anslutningar som ska anslutas till data lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej       |

> [!NOTE]
> För Parquet/avgränsat text format stöds **FileSystemSink** typ kopierings aktivitet som anges i nästa avsnitt fortfarande som-är för bakåtkompatibilitet. Du rekommenderas att använda den här nya modellen för att vidarebefordra och användar gränssnittet för ADF-redigering har växlat till att generera dessa nya typer.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "FileServerWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Övrigt format mottagare

För att kopiera data till fil systemet i **Orc-format**stöds följande egenskaper i avsnittet **mottagare** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för kopierings aktivitetens Sink måste anges till: **FileSystemSink** |Ja |
| copyBehavior | Definierar kopierings beteendet när källan är filer från filbaserat data lager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: bevarar filens hierarki i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><b>-FlattenHierarchy</b>: alla filer från källmappen finns på den första nivån i målmappen. Målfilen har ett namn som skapats automatiskt. <br/><b>-MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om fil namnet har angetts blir det sammanslagna fil namnet det angivna namnet. Annars skapas fil namnet automatiskt. | Nej |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till lagrings lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<file system output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "FileSystemSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exempel på mapp-och fil filter

I det här avsnittet beskrivs det resulterande beteendet hos mappsökvägen och fil namnet med filter för jokertecken.

| folderPath | fileName | rekursiva | Källans mappstruktur och filter resultat (filer i **fetstil** hämtas)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (tom, Använd standard) | false | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**fil2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | (tom, Använd standard) | sant | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**fil2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | false | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | sant | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="recursive-and-copybehavior-examples"></a>rekursiva och copyBehavior-exempel

I det här avsnittet beskrivs det resulterande beteendet för kopierings åtgärden för olika kombinationer av rekursiva och copyBehavior värden.

| rekursiva | copyBehavior | Källans mappstruktur | Resulterande mål |
|:--- |:--- |:--- |:--- |
| sant |preserveHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målmappen Mapp1 skapas med samma struktur som källan:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| sant |flattenHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Mål-Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File5 |
| sant |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Mål-Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1 + Fil2 + File3 + File4 + innehåll i fil 5 sammanfogas till en fil med automatiskt genererat fil namn |
| false |preserveHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målmappen Mapp1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |
| false |flattenHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målmappen Mapp1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för Fil2<br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |
| false |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målmappen Mapp1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1 + Fil2-innehåll sammanfogas till en fil med automatiskt genererat fil namn. automatiskt genererat namn för fil1<br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Egenskaper för GetMetadata-aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [getMetaData-aktivitet](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Ta bort aktivitets egenskaper

Om du vill veta mer om egenskaperna kontrollerar du [ta bort aktivitet](delete-activity.md)

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
