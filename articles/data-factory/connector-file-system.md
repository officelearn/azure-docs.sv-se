---
title: "Kopiera data från/till ett filsystem med hjälp av Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du kopierar data från filsystemet till stöds sink datalager (eller) från datalager för stöds källa för filsystem med hjälp av Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: jingwang
ms.openlocfilehash: 596121dec6ca24d71a5e1d5536ba22ce4c4283f0
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="copy-data-to-or-from-a-file-system-by-using-azure-data-factory"></a>Kopiera data till eller från ett filsystem med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-onprem-file-system-connector.md)
> * [Version 2 – förhandsversion](connector-file-system.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från och till filsystemet. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [filsystemet connector i V1](v1/data-factory-onprem-file-system-connector.md).

## <a name="supported-scenarios"></a>Scenarier som stöds

Du kan kopiera data från filsystemet till alla stöds sink-datalagret eller kopiera data från alla datalager stöds källa till filsystemet. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här filen system-anslutningen:

- Kopiera filer från/till lokala datorn eller nätverket filresurs. Om du vill använda en Linux-filresurs, installera [Samba](https://www.samba.org/) på Linux-servern.
- Kopiera filer med **Windows** autentisering.
- Filer som kopieras-är eller parsning/genererar filer med den [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Krav

Om du vill kopiera data från/till ett filsystem som inte är offentligt tillgänglig, måste du konfigurera en Self-hosted integrering Runtime. Se [Self-hosted integrering Runtime](create-self-hosted-integration-runtime.md) artikeln för information.

## <a name="getting-started"></a>Komma igång

Du kan skapa en pipeline med kopieringsaktiviteten använder .NET SDK, Python SDK, Azure PowerShell, REST-API eller Azure Resource Manager-mall. Se [kopiera aktivitet kursen](quickstart-create-data-factory-dot-net.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till filsystemet.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för filen system länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **filserver**. | Ja |
| värden | Anger rotsökvägen för den mapp som du vill kopiera. Använda escape-tecknet ”\" för specialtecken i strängen. Se [exempel länkad tjänst-och dataset](#sample-linked-service-and-dataset-definitions) exempel. | Ja |
| användar-ID | Ange ID för den användare som har åtkomst till servern. | Ja |
| lösenord | Ange lösenord för användaren (användar-ID). Markera det här fältet som SecureString. | Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Self-hosted integrering Runtime eller Azure Integration Runtime (om datalager är offentligt tillgänglig). Om inget anges används standard-Azure Integration Runtime. |Nej |

### <a name="sample-linked-service-and-dataset-definitions"></a>Exempel på länkad tjänst och dataset definitioner

| Scenario | ”värd” i länkade tjänstdefinitionen | ”folderPath” i datauppsättningsdefinitionen |
|:--- |:--- |:--- |
| Lokal mapp på Integration Runtime-datorn: <br/><br/>Exempel: D:\\ \* eller D:\folder\subfolder\\* |D:\\\\ |. \\ \\ eller mappen\\\\undermapp |
| Delad fjärrmapp: <br/><br/>Exempel: \\ \\minserver\\dela\\ \* eller \\ \\minserver\\dela\\mappen\\undermapp\\* |\\\\\\\\minserver\\\\dela |. \\ \\ eller mappen\\\\undermapp |

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

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av filen system dataset.

Ange typegenskapen för dataset för att kopiera data från/till filsystemet, **filresursen**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **filresursen** |Ja |
| folderPath | Sökvägen till mappen. Se [exempel länkad tjänst-och dataset](#sample-linked-service-and-dataset-definitions) exempel. |Ja |
| fileName | Ange namnet på filen i den **folderPath** om du vill kopiera till/från en viss fil. Om du inte anger något värde för den här egenskapen dataset pekar på alla filer i mappen som källa och genererar automatiskt filnamn.<br/><br/>**Filnamn automatisk generering för sink:** när filnamn har angetts för en datamängd för utdata och **preserveHierarchy** har inte angetts i aktiviteten sink kopieringsaktiviteten genererar filnamnet med följande mönster: <br/>- `Data_[activity run id]_[GUID].[format].[compression if configured]`. Exempel: `Data_0a405f8a-93ff-4c6f-b3be-f69616f1df7a_0d143eda-d5b8-44df-82ec-95c50895ff80.txt.gz` <br/>- eller `[Table name].[format].[compression if configured]` för relationella källa när frågan inte har angetts. Till exempel: MySourceTable.orc. |Nej |
| fileFilter | Ange ett filter som används för att välja en delmängd av filer i mappsökvägen i stället för alla filer. Gäller bara när filnamn har inte angetts. <br/><br/>Tillåtna jokertecken är: `*` (flera tecken) och `?` (valfritt tecken).<br/>-Exempel 1:`"fileFilter": "*.log"`<br/>-Exempel 2:`"fileFilter": 2017-09-??.txt"` |Nej |
| Format | Om du vill **kopiera filer som-är** mellan filbaserade butiker (binär kopia), hoppa över avsnittet format i både inkommande och utgående dataset-definitioner.<br/><br/>Om du vill att parsa eller generera filer med ett specifikt format format för följande filtyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [textformat](supported-file-formats-and-compression-codecs.md#text-format), [Json-Format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs.md#orc-format), och [parkettgolv Format](supported-file-formats-and-compression-codecs.md#parquet-format) avsnitt. |Nej (endast för binära kopiera scenario) |
| Komprimering | Ange typ och kompression för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Nivåer som stöds är: **Optimal** och **snabbast**. |Nej |


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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av system källa och mottagare.

### <a name="file-system-as-source"></a>Filsystem som källa

För att kopiera data från filsystemet, anger du datakällan i kopieringsaktiviteten till **FileSystemSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **FileSystemSource** |Ja |
| Rekursiva | Anger om data läses rekursivt från undermappar eller endast från den angivna mappen.<br/>Tillåtna värden är: **SANT** (standard), **FALSKT** | Nej |

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

### <a name="file-system-as-sink"></a>Filsystem som mottagare

Om du vill kopiera data till filsystemet, anger du sink i kopieringsaktiviteten till **FileSystemSink**. Följande egenskaper stöds i den **sink** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för kopiera aktivitet sink måste anges till: **FileSystemSink** |Ja |
| copyBehavior | Definierar beteendet kopia när källan är filer från filbaserad dataarkiv.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: bevarar fil hierarkin i målmappen. Den relativa sökvägen för källfilen till källmappen är identisk med den relativa sökvägen för filen till målmappen.<br/><b>-FlattenHierarchy</b>: alla filer från källmappen finns i den första nivån i målmappen. Mål-filer har genereras automatiskt namn. <br/><b>-MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om namnet på filen/Blob har angetts, är kopplade filnamnet det angivna namnet; annars skulle vara automatiskt genererade filnamn. | Nej |

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

### <a name="recursive-and-copybehavior-examples"></a>rekursiva och copyBehavior exempel

Det här avsnittet beskriver resultatet av kopieringen för olika kombinationer av värden rekursiv och copyBehavior.

| Rekursiva | copyBehavior | Mappstruktur för källa | Resulterande mål |
|:--- |:--- |:--- |:--- |
| SANT |preserveHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | målmappen Mapp1 skapas med samma struktur som källa:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| SANT |flattenHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File5 |
| SANT |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 + fil3 + File4 + filen 5 innehållet slås samman till en fil med automatiskt genererade namnet |
| FALSKT |preserveHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | målmappen Mapp1 har skapats med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/><br/>Subfolder1 med fil3, File4 och File5 har inte plockats. |
| FALSKT |flattenHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | målmappen Mapp1 har skapats med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil2<br/><br/>Subfolder1 med fil3, File4 och File5 har inte plockats. |
| FALSKT |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | målmappen Mapp1 har skapats med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 innehållet slås samman till en fil med automatiskt genererade namnet. automatiskt genererade namnet på File1<br/><br/>Subfolder1 med fil3, File4 och File5 har inte plockats. |

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).