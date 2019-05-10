---
title: Kopiera data från SFTP-server med Azure Data Factory | Microsoft Docs
description: Läs mer om MySQL-connector i Azure Data Factory som låter dig kopiera data från en SFTP-server till ett datalager som stöds som en mottagare.
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
ms.openlocfilehash: 1a62e9e8377705af1a70e356f554cfa549c58f20
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233467"
---
# <a name="copy-data-from-sftp-server-using-azure-data-factory"></a>Kopiera data från SFTP-server med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-sftp-connector.md)
> * [Aktuell version](connector-sftp.md)

Den här artikeln beskrivs hur du kopierar data från SFTP-server. Läs om Azure Data Factory den [introduktionsartikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här SFTP-anslutningsappen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [källa/mottagare matris som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktiviteten](control-flow-get-metadata-activity.md)

Mer specifikt stöder den här SFTP-anslutningen:

- Kopiering av filer med hjälp av **grundläggande** eller **SshPublicKey** autentisering.
- Kopiera filer som-är eller parsningsfilerna med den [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för SFTP.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för SFTP länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till: **SFTP**. |Ja |
| host | Namn eller IP-adressen för SFTP-servern. |Ja |
| port | Porten som SFTP-servern lyssnar.<br/>Tillåtna värden är: heltal, standardvärdet är **22**. |Nej |
| skipHostKeyValidation | Ange om du vill hoppa över nyckelvalidering för värden.<br/>Tillåtna värden är: **SANT**, **FALSKT** (standard).  | Nej |
| ska hostKeyFingerprint | Ange fingeravtryck av serverns värdnyckel. | Ja om ”skipHostKeyValidation” är inställt på FALSKT.  |
| authenticationType | Ange autentiseringstyp.<br/>Tillåtna värden är: **Basic**, **SshPublicKey**. Referera till [använder grundläggande autentisering](#using-basic-authentication) och [med hjälp av SSH autentisering med offentlig nyckel](#using-ssh-public-key-authentication) respektive avsnitt på fler egenskaper och JSON-exempel. |Ja |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller lokal Integration Runtime (om ditt datalager finns i privat nätverk). Om den inte anges används standard Azure Integration Runtime. |Nej |

### <a name="using-basic-authentication"></a>Använder grundläggande autentisering

Om du vill använda grundläggande autentisering för egenskapen ”authenticationType” **grundläggande**, och ange följande egenskaper förutom SFTP-anslutningsappen Allmänt som introducerades i det sista avsnittet:

| Egenskap  | Beskrivning | Obligatoriskt |
|:--- |:--- |:--- |
| userName | Användare som har åtkomst till SFTP-servern. |Ja |
| password | Lösenordet för användaren (användarnamn). Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |

**Exempel:**

```json
{
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
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

### <a name="using-ssh-public-key-authentication"></a>Med hjälp av SSH-autentisering för offentlig nyckel

Om du vill använda autentisering med SSH offentlig nyckel, egenskapen ”authenticationType” som **SshPublicKey**, och ange följande egenskaper förutom SFTP-anslutningsappen Allmänt som introducerades i det sista avsnittet:

| Egenskap  | Beskrivning | Obligatoriskt |
|:--- |:--- |:--- |
| userName | Användare som har åtkomst till SFTP-server |Ja |
| privateKeyPath | Ange absolut sökväg till filen för privat nyckel som Integration Runtime kan komma åt. Gäller bara när lokal Integration Runtime har angetts i ”connectVia”. | Ange antingen den `privateKeyPath` eller `privateKeyContent`.  |
| privateKeyContent | Base64-kodade innehåll för SSH privat nyckel. SSH-privata nyckeln ska vara OpenSSH-format. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ange antingen den `privateKeyPath` eller `privateKeyContent`. |
| Lösenfras | Ange pass frasen/lösenord för att dekryptera den privata nyckeln om nyckelfilen skyddas av en godkänd fras. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja om filen för privata nyckeln skyddas av en godkänd fras. |

> [!NOTE]
> SFTP-anslutningsappen har stöd för RSA/DSA OpenSSH-nyckel. Kontrollera att innehållet nyckelfil som börjar med ”---BEGIN [RSA/DSA] privata NYCKELN---”. Om den privata nyckelfilen är en fil för ppk-format, Använd Putty verktyget för att konvertera från .ppk till OpenSSH-format. 

**Exempel 1: SshPublicKey autentisering med hjälp av privat nyckel filePath**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel 2: SshPublicKey autentisering med hjälp av privat nyckel innehåll**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
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

Att kopiera data från SFTP i **Parquet eller avgränsat textformat**, referera till [Parquet-format](format-parquet.md) och [avgränsat textformat](format-delimited-text.md) artikel om format-baserade datauppsättning och som stöds inställningar. Följande egenskaper har stöd för SFTP under `location` inställningar i formatet-baserade datauppsättning:

| Egenskap    | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Egenskapen type under `location` i datauppsättningen måste anges till **SftpLocation**. | Ja      |
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
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
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

Att kopiera data från SFTP i **ORC/Avro/JSON/binära formatet**, stöds följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för datauppsättningen måste anges till: **FileShare** |Ja |
| folderPath | Sökvägen till mappen. Jokerteckenfilter stöds, tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken); Använd `^` att undvika om din faktiska filnamnet har jokertecken eller den här escape-tecken i. <br/><br/>Exempel: rootfolder/undermappen/se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). |Ja |
| fileName |  **Namn eller jokertecken-filtret** för den eller filerna under den angivna ”folderPath”. Om du inte anger ett värde för den här egenskapen datauppsättningen pekar på alla filer i mappen. <br/><br/>För filter tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken).<br/>– Exempel 1: `"fileName": "*.csv"`<br/>– Exempel 2: `"fileName": "???20180427.txt"`<br/>Använd `^` att undvika om din faktiska mappnamn har jokertecken eller den här escape-tecken i. |Nej |
| modifiedDatetimeStart | Filter för filer baserat på attributet: Senast ändrades. Filerna markerade om deras tid för senaste ändring är inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br/><br/> Tänk på dataförflyttning serverns prestanda påverkas genom att aktivera den här inställningen när du vill filen filter från stora mängder filer. <br/><br/> Egenskaperna kan vara NULL som innebär att inga filfilter för attributet som ska användas för datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senaste ändrade attribut är större än eller lika med datum/tid-värde väljs.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filer vars senaste ändrade attributet är mindre än det markerade datetime-värde.| Nej |
| modifiedDatetimeEnd | Filter för filer baserat på attributet: Senast ändrades. Filerna markerade om deras tid för senaste ändring är inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br/><br/> Tänk på dataförflyttning serverns prestanda påverkas genom att aktivera den här inställningen när du vill filen filter från stora mängder filer. <br/><br/> Egenskaperna kan vara NULL som innebär att inga filfilter för attributet som ska användas för datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senaste ändrade attribut är större än eller lika med datum/tid-värde väljs.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filer vars senaste ändrade attributet är mindre än det markerade datetime-värde.| Nej |
| format | Om du vill **kopiera filer som – är** hoppa över avsnittet format i både inkommande och utgående datamängd definitioner mellan filbaserade (binär kopia).<br/><br/>Om du vill parsa filer med ett visst format stöds format för följande filtyper: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typ** egenskapen under format till ett av dessa värden. Mer information finns i [textformat](supported-file-formats-and-compression-codecs.md#text-format), [Json-Format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [Orc-Format](supported-file-formats-and-compression-codecs.md#orc-format), och [Parquet-Format](supported-file-formats-and-compression-codecs.md#parquet-format) avsnitt. |Nej (endast för binär kopia scenario) |
| compression | Ange typ och komprimeringsnivå för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Nivåer som stöds är: **Optimal** och **snabbaste**. |Nej |

>[!TIP]
>Kopiera alla filer i en mapp genom att ange **folderPath** endast.<br>Om du vill kopiera en enstaka fil med ett givet namn, ange **folderPath** med mappdelen och **fileName** med filnamnet.<br>Om du vill kopiera en delmängd av filerna under en mapp, anger **folderPath** med mappdelen och **fileName** med jokertecken-filtret.

>[!NOTE]
>Om du använder ”fileFilter”-egenskapen för filtret, stöds det fortfarande som – är att medan du rekommenderas för att använda den nya funktionen för filter som lagts till i ”fileName” framöver.

**Exempel:**

```json
{
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av SFTP-källa.

### <a name="sftp-as-source"></a>SFTP som källa

- För kopia från **Parquet och avgränsat textformat**, referera till [Parquet och avgränsad text format källa](#parquet-and-delimited-text-format-source) avsnittet.
- För kopia från andra format som **ORC/Avro/JSON/binära formatet**, referera till [annan format källa](#other-format-source) avsnittet.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet och avgränsad text format källa

Att kopiera data från SFTP i **Parquet eller avgränsat textformat**, referera till [Parquet-format](format-parquet.md) och [avgränsat textformat](format-delimited-text.md) artikel om format-baserade aktiviteten kopieringskälla och inställningar som stöds. Följande egenskaper har stöd för SFTP under `storeSettings` inställningar i formatet-baserade kopieringskälla:

| Egenskap                  | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Egenskapen type under `storeSettings` måste anges till **SftpReadSetting**. | Ja                                           |
| recursive                | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. Observera att när rekursiv har angetts till true och mottagaren är en filbaserad store, en tom mapp eller undermapp inte kopieras eller skapat i mottagaren. Tillåtna värden är **SANT** (standard) och **FALSKT**. | Nej                                            |
| wildcardFolderPath       | Sökvägen till mappen med jokertecken för att filtrera källa mappar. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken); Använd `^` att undvika om din faktiska mappnamn har jokertecken eller den här escape-tecken i. <br>Se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). | Nej                                            |
| wildcardFileName         | Filnamn med jokertecken under den angivna folderPath/wildcardFolderPath filter källfilerna för. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken); Använd `^` att undvika om din faktiska mappnamn har jokertecken eller den här escape-tecken i.  Se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). | Ja om `fileName` har inte angetts i datauppsättningen |
| modifiedDatetimeStart    | Filter för filer baserat på attributet: Senast ändrades. Filerna markerade om deras tid för senaste ändring är inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br> Egenskaperna kan vara NULL vilket innebär att inga filfilter för attributet som ska användas för datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senaste ändrade attribut är större än eller lika med datum/tid-värde väljs.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filer vars senaste ändrade attributet är mindre än det markerade datetime-värde. | Nej                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Nej                                            |
| maxConcurrentConnections | Antal anslutningar för att ansluta till storage store samtidigt. Ange bara när du vill begränsa samtidiga anslutningen till datalagret. | Nej                                            |

> [!NOTE]
> För Parquet/avgränsat textformat **FileSystemSource** typen kopiera aktivitetskälla som nämns i nästa avsnitt stöds fortfarande som – är för bakåtkompatibilitet. Du rekommenderas för att använda den nya modellen framöver och ADF redigering Användargränssnittet har ändrats till att generera dessa nya typer.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
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
                    "type": "SftpReadSetting",
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

Att kopiera data från SFTP i **ORC/Avro/JSON/binära formatet**, följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **FileSystemSource** |Ja |
| recursive | Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. Obs när rekursiv har angetts till true och mottagare är filbaserade store, tom mapp/underanvändningsfall-folder kan inte kopieras/skapas vid mottagare.<br/>Tillåtna värden är: **SANT** (standard), **FALSKT** | Nej |
| maxConcurrentConnections | Antal anslutningar för att ansluta till storage store samtidigt. Ange bara när du vill begränsa samtidiga anslutningen till datalagret. | Nej |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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

| folderPath | fileName | recursive | Käll-mappen struktur och filtrera resultatet (filer i **fetstil** hämtas)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (tom, Använd standard) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (tom, Använd standard) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
