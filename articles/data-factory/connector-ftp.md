---
title: Kopiera data från en FTP-server med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig mer om att kopiera data från en FTP-server till ett datalager för mottagare som stöds med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
ms.openlocfilehash: 7a3d776acb06d2aa55f71dafb0ddccbc307f394e
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050530"
---
# <a name="copy-data-from-ftp-server-by-using-azure-data-factory"></a>Kopiera data från FTP-servern med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-ftp-connector.md)
> * [Aktuell version](connector-ftp.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från en FTP-server. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

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

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **FtpServer**. | Ja |
| värd | Ange namn eller IP-adressen till FTP-servern. | Ja |
| port | Ange den port som FTP-servern lyssnar.<br/>Tillåtna värden är: heltal, standardvärdet är **21**. | Nej |
| enableSsl | Ange om du använder FTP över SSL/TLS-kanalen.<br/>Tillåtna värden är: **SANT** (standard), **FALSKT**. | Nej |
| enableServerCertificateValidation | Ange om du vill aktivera server SSL-certifikatsverifiering när du använder FTP över SSL/TLS-kanalen.<br/>Tillåtna värden är: **SANT** (standard), **FALSKT**. | Nej |
| authenticationType | Ange vilken autentiseringstyp.<br/>Tillåtna värden är: **grundläggande**, **anonym** | Ja |
| Användarnamn | Ange den användare som har åtkomst till FTP-servern. | Nej |
| lösenord | Ange lösenord för användaren (användarnamn). Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime (om datalager finns i privat nätverk). Om inget anges används standard-Azure Integration Runtime. |Nej |

>[!NOTE]
>FTP-anslutningen har stöd för att komma åt FTP-server utan kryptering eller explicit SSL/TLS-kryptering; Det stöder inte implicit SSL/TLS-kryptering.

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

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **filresursen** |Ja |
| folderPath | Sökvägen till mappen. Wildcard-filter stöds inte. Till exempel: mappen/undermappen / |Ja |
| fileName | **Namn eller jokertecken-filtret** för alla filer under den angivna ”folderPath”. Om du inte anger ett värde för den här egenskapen dataset pekar på alla filer i mappen. <br/><br/>För filter som tillåts är jokertecken: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken).<br/>-Exempel 1: `"fileName": "*.csv"`<br/>-Exempel 2: `"fileName": "???20180427.txt"`<br/>Använd `^` att undanta om din faktiska filnamnet innehåller jokertecken eller den här escape-tecken i. |Nej |
| Format | Om du vill **kopiera filer som-är** mellan filbaserade butiker (binär kopia), hoppa över avsnittet format i både inkommande och utgående dataset-definitioner.<br/><br/>Om du vill tolka filer med ett specifikt format format för följande filtyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [textformat](supported-file-formats-and-compression-codecs.md#text-format), [Json-Format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs.md#orc-format), och [parkettgolv Format](supported-file-formats-and-compression-codecs.md#parquet-format) avsnitt. |Nej (endast för binära kopiera scenario) |
| komprimering | Ange typ och kompression för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Nivåer som stöds är: **Optimal** och **snabbast**. |Nej |
| useBinaryTransfer | Ange om du vill använda binär överföringsläget. Värdena är true för en binär (standard) och FALSKT för ASCII. |Nej |

>[!TIP]
>Kopiera alla filer i en mapp genom att ange **folderPath** endast.<br>Om du vill kopiera en enstaka fil med ett angivet namn, ange **folderPath** med Mappdel och **fileName** med filnamn.<br>Om du vill kopiera en delmängd av filer på en mapp, ange **folderPath** med Mappdel och **fileName** med jokerteckenfiltret.

>[!NOTE]
>Om du använde ”fileFilter”-egenskapen för filfilter fortfarande stöds som-är, medan du rekommenderas för att använda den nya filter kapaciteten som lagts till ”fileName” framöver.

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

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **FileSystemSource** |Ja |
| rekursiva | Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. Obs när rekursiv är inställd på true och mottagare är filbaserad lagring, tom mapp/underåtgärder-folder kommer inte att kopieras/skapas på mottagare.<br/>Tillåtna värden är: **SANT** (standard), **FALSKT** | Nej |

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
