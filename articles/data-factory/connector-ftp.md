---
title: "Kopiera data från en FTP-server med hjälp av Azure Data Factory | Microsoft Docs"
description: "Lär dig mer om att kopiera data från en FTP-server till ett datalager för mottagare som stöds med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen."
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
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 36466ffe31a728e3267ef5002e4e69f52889577c
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="copy-data-from-ftp-server-by-using-azure-data-factory"></a>Kopiera data från FTP-servern med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-ftp-connector.md)
> * [Version 2 – förhandsversion](connector-ftp.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från en FTP-server. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [FTP-anslutningen i V1](v1/data-factory-ftp-connector.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från FTP-servern till några stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här FTP-anslutningen:

- Kopiera filer med **grundläggande** eller **anonym** autentisering.
- Filer som kopieras-är eller parsning filer med den [filformat och komprimering codec stöds](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter för FTP.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för FTP-länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **FtpServer**. | Ja |
| värd | Ange namn eller IP-adressen till FTP-servern. | Ja |
| port | Ange den port som FTP-servern lyssnar.<br/>Tillåtna värden är: heltal, standardvärdet är **21**. | Nej |
| enableSsl | Ange om du använder FTP över SSL/TLS-kanalen.<br/>Tillåtna värden är: **SANT** (standard), **FALSKT**. | Nej |
| enableServerCertificateValidation | Ange om du vill aktivera server SSL-certifikatsverifiering när du använder FTP över SSL/TLS-kanalen.<br/>Tillåtna värden är: **SANT** (standard), **FALSKT**. | Nej |
| AuthenticationType | Ange vilken autentiseringstyp.<br/>Tillåtna värden är: **grundläggande**, **anonym** | Ja |
| userName | Ange den användare som har åtkomst till FTP-servern. | Nej |
| lösenord | Ange lösenord för användaren (användarnamn). Markera det här fältet som SecureString. | Nej |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime (om datalager finns i privat nätverk). Om inget anges används standard-Azure Integration Runtime. |Nej |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av FTP-datauppsättningen.

Ange egenskapen type för datauppsättningen till för att kopiera data från FTP **filresursen**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **filresursen** |Ja |
| folderPath | Sökvägen till mappen. Till exempel: mappen/undermappen / |Ja |
| fileName | Ange namnet på filen i den **folderPath** om du vill kopiera från en viss fil. Om du inte anger något värde för den här egenskapen dataset pekar på alla filer i mappen som källa. |Nej |
| fileFilter | Ange ett filter som används för att välja en delmängd av filer i mappsökvägen i stället för alla filer. Gäller bara när filnamn har inte angetts. <br/><br/>Tillåtna jokertecken är: `*` (flera tecken) och `?` (valfritt tecken).<br/>-Exempel 1:`"fileFilter": "*.log"`<br/>-Exempel 2:`"fileFilter": 2017-09-??.txt"` |Nej |
| format | Om du vill **kopiera filer som-är** mellan filbaserade butiker (binär kopia), hoppa över avsnittet format i både inkommande och utgående dataset-definitioner.<br/><br/>Om du vill tolka filer med ett specifikt format format för följande filtyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [textformat](supported-file-formats-and-compression-codecs.md#text-format), [Json-Format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs.md#orc-format), och [parkettgolv Format](supported-file-formats-and-compression-codecs.md#parquet-format) avsnitt. |Nej (endast för binära kopiera scenario) |
| Komprimering | Ange typ och kompression för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Nivåer som stöds är: **Optimal** och **snabbast**. |Nej |
| useBinaryTransfer | Ange om du vill använda binär överföringsläget. Värdena är true för en binär (standard) och FALSKT för ASCII. |Nej |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av FTP-källa.

### <a name="ftp-as-source"></a>FTP-som källa

Om du vill kopiera data från FTP anger källa i kopieringsaktiviteten till **FileSystemSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **FileSystemSource** |Ja |
| Rekursiva | Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. Obs när rekursiv är inställd på true och mottagare är filbaserad lagring, tom mapp/underåtgärder-folder kommer inte att kopieras/skapas på mottagare.<br/>Tillåtna värden är: **SANT** (standard), **FALSKT** | Nej |

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


## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).