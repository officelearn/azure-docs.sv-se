---
title: Kopiera data från och till SFTP-server
description: Lär dig mer om hur du kopierar data från och till SFTP-server med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/02/2020
ms.openlocfilehash: e6d29f73716b04699e0cd250396df7f7d744d4c4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415247"
---
# <a name="copy-data-from-and-to-sftp-server-using-azure-data-factory"></a>Kopiera data från och till SFTP-server med Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-sftp-connector.md)
> * [Aktuell version](connector-sftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du kopierar data från och till SFTP-server. Mer information om Azure Data Factory finns i den [inledande artikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här SFTP-kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)

Den här SFTP-kontakten stöder:

- Kopiera filer från/till SFTP med **grundläggande** eller **SshPublicKey-autentisering.**
- Kopiera filer som de är eller tolka/generera filer med [filformat och komprimeringskodicer som stöds](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för SFTP.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för SFTP-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste ställas in på: **Sftp**. |Ja |
| värd | Namn eller IP-adress på SFTP-servern. |Ja |
| port | Port som SFTP-servern lyssnar på.<br/>Tillåtna värden är: heltal, standardvärdet är **22**. |Inga |
| hoppa överHostKeyValidation | Ange om värdnyckelverifiering ska ska skipas.<br/>Tillåtna värden är: **sant**, **falskt** (standard).  | Inga |
| hostKeyFingerprint | Ange värdnyckelns fingeravtryck. | Ja om "skipHostKeyValidation" är inställd på false.  |
| authenticationType | Ange autentiseringstyp.<br/>Tillåtna värden är: **Basic**, **SshPublicKey**. Se [Använda grundläggande autentisering](#using-basic-authentication) och använda autentiseringsavsnitt för [SSH-offentliga nyckel](#using-ssh-public-key-authentication) på fler egenskaper respektive JSON-exempel. |Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Läs mer från avsnittet [Förutsättningar.](#prerequisites) Om det inte anges används standardkörningen för Azure Integration. |Inga |

### <a name="using-basic-authentication"></a>Använda grundläggande autentisering

Om du vill använda grundläggande autentisering anger du egenskapen "authenticationType" till **Basic**och anger följande egenskaper förutom de allmänna SFTP-anslutningsapparna som introducerades i det sista avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| userName | Användare som har åtkomst till SFTP-servern. |Ja |
| password | Lösenord för användaren (användarnamn). Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |

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

### <a name="using-ssh-public-key-authentication"></a>Använda autentisering av SSH-offentliga nyckel

Om du vill använda SSH-autentisering med offentliga nycklar anger du egenskapen "authenticationType" som **SshPublicKey**och anger följande egenskaper förutom de allmänna SFTP-anslutningsapparna som introducerades i det sista avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| userName | Användare som har åtkomst till SFTP-servern |Ja |
| privateKeyPath (privatKeyPath) | Ange absolut sökväg till den privata nyckelfilen som Integration Runtime kan komma åt. Gäller endast när självvärderad typ av Integration Runtime anges i "connectVia". | Ange antingen `privateKeyPath` `privateKeyContent`eller .  |
| privatKeyContent | Base64 kodade SSH privat nyckelinnehåll. SSH privat nyckel bör OpenSSH-format. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ange antingen `privateKeyPath` `privateKeyContent`eller . |
| Lösenfras | Ange lösenfrasen/lösenordet för att dekryptera den privata nyckeln om nyckelfilen skyddas av en lösenfras. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja om den privata nyckelfilen skyddas av en lösenfras. |

> [!NOTE]
> SFTP-kontakt stöder RSA/DSA OpenSSH-nyckel. Kontrollera att ditt nyckelfilinnehåll börjar med "-----BEGIN [RSA/DSA] PRIVATE KEY-----". Om den privata nyckelfilen är en ppk-format fil, använd Putty verktyg för att konvertera från PPK till OpenSSH-format. 

**Exempel 1: SshPublicKey-autentisering med hjälp av privata nyckelfilerPath**

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

**Exempel 2: SshPublicKey-autentisering med privat nyckelinnehåll**

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Datauppsättningar.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för SFTP under `location` inställningar i formatbaserad datauppsättning:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Egenskapen type `location` under in dataset måste anges till **SftpLocation**. | Ja      |
| folderPath | Sökvägen till mappen. Om du vill använda jokertecken för att filtrera mappen hoppar du över den här inställningen och anger i inställningarna för aktivitetskällan. | Inga       |
| fileName   | Filnamnet under den angivna folderPath. Om du vill använda jokertecken för att filtrera filer hoppar du över den här inställningen och anger i inställningarna för aktivitetskällan. | Inga       |

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

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av SFTP-källa.

### <a name="sftp-as-source"></a>SFTP som källa

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för SFTP under `storeSettings` inställningar i formatbaserad kopieringskälla:

| Egenskap                 | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Egenskapen type `storeSettings` under måste vara inställd på **SftpReadSettings**. | Ja                                           |
| Rekursiv                | Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. Observera att när rekursiv är inställd på true och diskhon är ett filbaserat arkiv kopieras eller skapas inte en tom mapp eller undermapp i diskhon. Tillåtna värden är **sanna** (standard) och **falska**. | Inga                                            |
| jokerteckenMappspath       | Mappsökvägen med jokertecken för att filtrera källmappar. <br>Tillåtna jokertecken `*` är: (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken); används `^` för att fly om ditt faktiska mappnamn har jokertecken eller den här flyktteckenet inuti. <br>Se fler exempel i [exempel på mapp- och filfilter](#folder-and-file-filter-examples). | Inga                                            |
| jokerteckenFileName         | Filnamnet med jokertecken under den angivna mappenPath/wildcardFolderPath för att filtrera källfiler. <br>Tillåtna jokertecken `*` är: (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken); används `^` för att fly om ditt faktiska mappnamn har jokertecken eller den här flyktteckenet inuti.  Se fler exempel i [exempel på mapp- och filfilter](#folder-and-file-filter-examples). | Ja `fileName` om inte anges i datauppsättningen |
| modifiedDatetimeStart    | Filfilter baserat på attributet: Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` ligger `modifiedDatetimeEnd`inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br> Egenskaperna kan vara NULL, vilket innebär att inget filattributfilter tillämpas på datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` null betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet kommer att väljas.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` null betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet kommer att väljas. | Inga                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Inga                                            |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till lagringsarkivet samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga                                            |

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "SftpReadSettings",
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

### <a name="sftp-as-sink"></a>SFTP som diskbänk

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för SFTP under `storeSettings` inställningar i formatbaserad kopia sink:

| Egenskap                 | Beskrivning                                                  | Krävs |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | Typegenskapen under `storeSettings` måste vara inställd på **SftpWriteSettings**. | Ja      |
| kopieraBehavior             | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>- PreserveHierarchy (standard):</b>Bevarar filhierarkin i målmappen. Källfilens relativa sökväg till källmappen är identisk med målfilens relativa sökväg till målmappen.<br/><b>- FlattenHierarchy:</b>Alla filer från källmappen är i den första nivån i målmappen. Målfilerna har automatiskt urartade namn. <br/><b>- MergeFiles:</b>Sammanfogar alla filer från källmappen till en fil. Om filnamnet anges är det kopplade filnamnet det angivna namnet. Annars är det ett autogenererat filnamn. | Inga       |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till datalagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga       |
| användaTempFileRename | Ange om du vill överföra till tillfälliga filer och byta namn på eller skriva direkt till målmappen/filplatsen. Som standard, ADF först skriva till temporära filer (s) sedan göra filbyte vid uppladdning slutförande, för att 1) undvika konflikt skriva vilket resulterar i skadad fil om du har andra process skriver till samma fil, och 2) se till att den ursprungliga versionen av filen finns under hela överföringen. Om SFTP-servern inte stöder namnbyte inaktiverar du det här alternativet och ser till att du inte har samtidig skrivning till målfilen. Se felsökningstips under den här tabellen. | Nej. Standardvärdet är sant. |
| operationTimeout | Väntetiden innan varje skrivbegäran till SFTP-servern time out. Standardvärdet är 60 min (01:00:00).|Inga |

>[!TIP]
>Om du stöter på fel i "UserErrorSftpPathNotFound", "UserErrorSftpPermissionDenied" eller "SftpOperationFail" när du skriver data till SFTP, och SFTP-användaren du använder har rätt behörighet,`useTempFileRename`kontrollera om din SFTP-server stöder fil byta namn på åtgärden - om inte, inaktivera "Ladda upp med temp-fil" ( ) alternativet och försök igen. Läs mer om den här egenskapen ovanifrån. Om du använder Självvärdförd integrationskörning för kopia kontrollerar du att du använder version 4.6 eller senare.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToSFTP",
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
                "type": "<source type>"
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings":{
                    "type": "SftpWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exempel på mapp- och filfilter

I det här avsnittet beskrivs det resulterande beteendet för mappsökvägen och filnamnet med jokerteckenfilter.

| folderPath | fileName | Rekursiv | Källmappstruktur och filterresultat (filer i **fetstil** hämtas)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (tom, använd standard) | false | FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| `Folder*` | (tom, använd standard) | true | FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| `Folder*` | `*.csv` | false | FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| `Folder*` | `*.csv` | true | FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |

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
| typ | Datauppsättningens typegenskap måste anges till: **FileShare** |Ja |
| folderPath | Sökväg till mappen. Jokerteckenfilter stöds, tillåtna jokertecken `*` är: (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken); används `^` för att fly om ditt faktiska filnamn har jokertecken eller den här flyktteckenet inuti. <br/><br/>Exempel: rootfolder/subfolder/, se fler exempel i [exempel på mapp- och filfilter](#folder-and-file-filter-examples). |Ja |
| fileName |  **Namn eller jokerteckenfilter** för filen/filerna under den angivna "folderPath". Om du inte anger något värde för den här egenskapen pekar datauppsättningen på alla filer i mappen. <br/><br/>För filter är tillåtna `*` jokertecken: (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken).<br/>- Exempel 1:`"fileName": "*.csv"`<br/>- Exempel 2:`"fileName": "???20180427.txt"`<br/>Används `^` för att fly om ditt faktiska mappnamn har jokertecken eller den här flyktteckenet inuti. |Inga |
| modifiedDatetimeStart | Filfilter baserat på attributet: Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` ligger `modifiedDatetimeEnd`inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Tänk på att den övergripande prestandan för data förflyttning kommer att påverkas genom att aktivera den här inställningen när du vill göra filfilter från stora mängder filer. <br/><br/> Egenskaperna kan vara NULL som innebär att inget filattributfilter tillämpas på datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` null betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet kommer att väljas.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` null betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet kommer att väljas.| Inga |
| modifiedDatetimeEnd | Filfilter baserat på attributet: Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` ligger `modifiedDatetimeEnd`inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Tänk på att den övergripande prestandan för data förflyttning kommer att påverkas genom att aktivera den här inställningen när du vill göra filfilter från stora mängder filer. <br/><br/> Egenskaperna kan vara NULL som innebär att inget filattributfilter tillämpas på datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` null betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet kommer att väljas.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` null betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet kommer att väljas.| Inga |
| format | Om du vill **kopiera filer som de är** mellan filbaserade butiker (binär kopia) hoppar du över formatavsnittet i definitionerna för både in- och utdatadata.<br/><br/>Om du vill tolka filer med ett visst format stöds följande filformattyper: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange **type** typegenskapen under format till ett av dessa värden. Mer information finns i avsnitten [Textformat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Json Format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)och [Parquet Format.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Nej (endast för binärt kopieringsscenario) |
| komprimering | Ange typ och komprimeringsnivå för data. Mer information finns i [Filformat och komprimeringskodicer som stöds](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Typer som stöds är: **GZip**, **Deflate**, **BZip2**och **ZipDeflate**.<br/>Nivåer som stöds är: **Optimal** och **snabbaste**. |Inga |

>[!TIP]
>Om du vill kopiera alla filer under en mapp anger du endast **folderPath.**<br>Om du vill kopiera en enskild fil med ett förnamn anger du **folderPath** med mappdel och **fileName** med filnamn.<br>Om du vill kopiera en delmängd filer under en mapp anger du **folderPath** med mappdel och **fileName** med jokerteckenfilter.

>[!NOTE]
>Om du använde egenskapen fileFilter för filfilter stöds den fortfarande som den är, medan du föreslås använda den nya filterfunktionen som läggs till i "fileName" framöver.

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

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
