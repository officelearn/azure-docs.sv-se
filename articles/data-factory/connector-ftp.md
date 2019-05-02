---
title: Kopiera data från en FTP-server med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från en FTP-server till ett datalager för mottagare som stöds med en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: c500a744cd2c001d3d9d65363319d396f04e4626
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64876156"
---
# <a name="copy-data-from-ftp-server-by-using-azure-data-factory"></a>Kopiera data från FTP-servern med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
>
> * [Version 1](v1/data-factory-ftp-connector.md)
> * [Aktuell version](connector-ftp.md)

Den här artikeln beskrivs hur du kopierar data från FTP-servern. Läs om Azure Data Factory den [introduktionsartikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här FTP-anslutningsappen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [källa/mottagare matris som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktiviteten](control-flow-get-metadata-activity.md)

Mer specifikt stöder den här FTP-anslutningen:

- Kopiering av filer med hjälp av **grundläggande** eller **anonym** autentisering.
- Kopiera filer som-är eller parsningsfilerna med den [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för FTP.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för FTP-länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till: **FtpServer**. | Ja |
| värd | Ange namn eller IP-adressen för FTP-servern. | Ja |
| port | Ange den port som FTP-servern lyssnar.<br/>Tillåtna värden är: heltal, standardvärdet är **21**. | Nej |
| enableSsl | Ange om du använder FTP över SSL/TLS-kanalen.<br/>Tillåtna värden är: **SANT** (standard), **FALSKT**. | Nej |
| enableServerCertificateValidation | Ange om du vill aktivera serververifiering SSL-certifikat när du använder FTP över SSL/TLS-kanalen.<br/>Tillåtna värden är: **SANT** (standard), **FALSKT**. | Nej |
| authenticationType | Ange vilken autentiseringstyp.<br/>Tillåtna värden är: **Basic**, **Anonymous** | Ja |
| userName | Ange den användare som har åtkomst till FTP-servern. | Nej |
| password | Ange lösenordet för användaren (användarnamn). Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller lokal Integration Runtime (om ditt datalager finns i privat nätverk). Om den inte anges används standard Azure Integration Runtime. |Nej |

>[!NOTE]
>FTP-anslutningsappen stöder åtkomst till FTP-servern utan kryptering eller explicit SSL/TLS-kryptering; Det stöder inte implicit SSL/TLS-kryptering.

**Exempel 1: använder anonym autentisering**

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "<ftp server>",
            "port": 21,
            "enableSsl": true,
            "enableServerCertificateValidation": true,
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel 2: använder grundläggande autentisering**

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "<ftp server>",
            "port": 21,
            "enableSsl": true,
            "enableServerCertificateValidation": true,
            "authenticationType": "Basic",
            "userName": "<username>",
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

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. 

- För **Parquet och avgränsat textformat**, referera till [Parquet och avgränsad text formatera datauppsättning](#parquet-and-delimited-text-format-dataset) avsnittet.
- För andra format som **ORC/Avro/JSON/binära formatet**, referera till [andra format datauppsättning](#other-format-dataset) avsnittet.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet och avgränsad text format datauppsättning

Att kopiera data från FTP i **Parquet eller avgränsat textformat**, referera till [Parquet-format](format-parquet.md) och [avgränsat textformat](format-delimited-text.md) format-baserade datauppsättning och inställningar som stöds . Följande egenskaper har stöd för FTP under `location` inställningar i formatet-baserade datauppsättning:

| Egenskap    | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Egenskapen type under `location` i datauppsättningen måste anges till **FtpServerLocation**. | Ja      |
| folderPath | Sökvägen till mappen. Om du vill använda jokertecken för att filtrera mappar hoppa över den här inställningen och ange i källinställningar för aktiviteten. | Nej       |
| fileName   | Filnamnet under den angivna folderPath. Om du vill använda jokertecken för att filtrera filerna hoppa över den här inställningen och ange i källinställningar för aktiviteten. | Nej       |

> [!NOTE]
> **Filresursen** typ datauppsättning med Parquet-/ textformat som nämns i nästa avsnitt stöds fortfarande som – för kopiera/Lookup/GetMetadata-aktiviteten för bakåtkompatibilitet. Du rekommenderas för att använda den nya modellen framöver och ADF redigering Användargränssnittet har ändrats till att generera dessa nya typer.

**Exempel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<FTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "FtpServerLocation",
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

### <a name="other-format-dataset"></a>Andra format-datauppsättning

Att kopiera data från FTP i **ORC/Avro/JSON/binära formatet**, stöds följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för datauppsättningen måste anges till: **FileShare** |Ja |
| folderPath | Sökvägen till mappen. Jokerteckenfilter stöds, tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken); Använd `^` att undvika om din faktiska mappnamn har jokertecken eller den här escape-tecken i. <br/><br/>Exempel: rootfolder/undermappen/se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). |Ja |
| fileName | **Namn eller jokertecken-filtret** för den eller filerna under den angivna ”folderPath”. Om du inte anger ett värde för den här egenskapen datauppsättningen pekar på alla filer i mappen. <br/><br/>För filter tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken).<br/>– Exempel 1: `"fileName": "*.csv"`<br/>– Exempel 2: `"fileName": "???20180427.txt"`<br/>Använd `^` att undvika om din faktiska filnamnet har jokertecken eller den här escape-tecken i. |Nej |
| Format | Om du vill **kopiera filer som – är** hoppa över avsnittet format i både inkommande och utgående datamängd definitioner mellan filbaserade (binär kopia).<br/><br/>Om du vill parsa filer med ett visst format stöds format för följande filtyper: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typ** egenskapen under format till ett av dessa värden. Mer information finns i [textformat](supported-file-formats-and-compression-codecs.md#text-format), [Json-Format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [Orc-Format](supported-file-formats-and-compression-codecs.md#orc-format), och [Parquet-Format](supported-file-formats-and-compression-codecs.md#parquet-format) avsnitt. |Nej (endast för binär kopia scenario) |
| Komprimering | Ange typ och komprimeringsnivå för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Nivåer som stöds är: **Optimal** och **snabbaste**. |Nej |
| useBinaryTransfer | Ange om du vill använda binär överföringsläge. Värdena är sanna för binärt läge (standard) och FALSKT för ASCII. |Nej |

>[!TIP]
>Kopiera alla filer i en mapp genom att ange **folderPath** endast.<br>Om du vill kopiera en enstaka fil med ett givet namn, ange **folderPath** med mappdelen och **fileName** med filnamnet.<br>Om du vill kopiera en delmängd av filerna under en mapp, anger **folderPath** med mappdelen och **fileName** med jokertecken-filtret.

>[!NOTE]
>Om du använder ”fileFilter”-egenskapen för filtret, stöds det fortfarande som – är att medan du rekommenderas för att använda den nya funktionen för filter som lagts till i ”fileName” framöver.

**Exempel:**

```json
{
    "name": "FTPDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<FTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "myfile.csv.gz",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av FTP-källa.

### <a name="ftp-as-source"></a>FTP-som källa

- För kopia från **Parquet och avgränsat textformat**, referera till [Parquet och avgränsad text format källa](#parquet-and-delimited-text-format-source) avsnittet.
- För kopia från andra format som **ORC/Avro/JSON/binära formatet**, referera till [annan format källa](#other-format-source) avsnittet.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet och avgränsad text format källa

Att kopiera data från FTP i **Parquet eller avgränsat textformat**, referera till [Parquet-format](format-parquet.md) och [avgränsat textformat](format-delimited-text.md) artikel om format-baserade aktiviteten kopieringskälla och inställningar som stöds. Följande egenskaper har stöd för FTP under `storeSettings` inställningar i formatet-baserade kopieringskälla:

| Egenskap                  | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Egenskapen type under `storeSettings` måste anges till **FtpReadSetting**. | Ja                                           |
| rekursiv                | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. Observera att när rekursiv har angetts till true och mottagaren är en filbaserad store, en tom mapp eller undermapp inte kopieras eller skapat i mottagaren. Tillåtna värden är **SANT** (standard) och **FALSKT**. | Nej                                            |
| wildcardFolderPath       | Sökvägen till mappen med jokertecken för att filtrera källa mappar. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken); Använd `^` att undvika om din faktiska mappnamn har jokertecken eller den här escape-tecken i. <br>Se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). | Nej                                            |
| wildcardFileName         | Filnamn med jokertecken under den angivna folderPath/wildcardFolderPath filter källfilerna för. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken); Använd `^` att undvika om din faktiska mappnamn har jokertecken eller den här escape-tecken i.  Se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). | Ja om `fileName` har inte angetts i datauppsättningen |
| modifiedDatetimeStart    | Filter för filer baserat på attributet: Senast ändrades. Filerna markerade om deras tid för senaste ändring är inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br> Egenskaperna kan vara NULL vilket innebär att inga filfilter för attributet som ska användas för datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senaste ändrade attribut är större än eller lika med datum/tid-värde väljs.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filer vars senaste ändrade attributet är mindre än det markerade datetime-värde. | Nej                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Nej                                            |
| useBinaryTransfer        | Ange om du vill använda binär transfer-läge för FTP-butiker. Värdena är sanna för binärt läge (standard) och FALSKT för ASCII. | Nej                                            |
| maxConcurrentConnections | Antal anslutningar för att ansluta till storage store samtidigt. Ange bara när du vill begränsa samtidiga anslutningen till datalagret. | Nej                                            |

> [!NOTE]
> För Parquet/avgränsat textformat **FileSystemSource** typen kopiera aktivitetskälla som nämns i nästa avsnitt stöds fortfarande som-avser för bakåtkompatibilitet. Du rekommenderas för att använda den nya modellen framöver och ADF redigering Användargränssnittet har ändrats till att generera dessa nya typer.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromFTP",
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
                    "type": "FtpReadSetting",
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

Att kopiera data från FTP i **ORC/Avro/JSON/binära formatet**, följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **FileSystemSource** |Ja |
| rekursiv | Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. Obs när rekursiv har angetts till true och mottagare är filbaserade store, tom mapp/underanvändningsfall-folder kan inte kopieras/skapas vid mottagare.<br/>Tillåtna värden är: **SANT** (standard), **FALSKT** | Nej |
| maxConcurrentConnections | Antal anslutningar för att ansluta till storage store samtidigt. Ange bara när du vill begränsa samtidiga anslutningen till datalagret. | Nej |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<FTP input dataset name>",
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

| folderPath | fileName | rekursiv | Käll-mappen struktur och filtrera resultatet (filer i **fetstil** hämtas)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (tom, Använd standard) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (tom, Använd standard) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
