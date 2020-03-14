---
title: Kopiera och transformera data i Azure Data Lake Storage Gen2
description: Lär dig hur du kopierar data till och från Azure Data Lake Storage Gen2 och hur du omvandlar data i Azure Data Lake Storage Gen2 genom att använda Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/17/2020
ms.openlocfilehash: 2f147890887d5eb9dd1b2681bd09c662c14c74ff
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246193"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Kopiera och transformera data i Azure Data Lake Storage Gen2 med Azure Data Factory

Azure Data Lake Storage Gen2 (ADLS Gen2) är en uppsättning funktioner som är avsedda för stor data analys som är inbyggda i [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md). Du kan använda den för att samverka med dina data med hjälp av både fil systemet och objekt lagrings paradigm.

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från och till Azure Data Lake Storage Gen2 och använda data flödet för att transformera data i Azure Data Lake Storage Gen2. Läs den [inledande artikeln](introduction.md)om du vill veta mer om Azure Data Factory.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Data Lake Storage Gen2 anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)

För kopierings aktivitet kan du med den här anslutningen:

- Kopiera data från/till Azure Data Lake Storage Gen2 genom att använda konto nyckel, tjänstens huvud namn eller hanterade identiteter för Azure-resursers autentiseringar.
- Kopiera filer som-är eller parsa eller generera filer med [fil format och komprimerings-codec som stöds](supported-file-formats-and-compression-codecs.md).
- [Bevara filens metadata under kopieringen](#preserve-metadata-during-copy).
- [Bevara ACL: er](#preserve-metadata-during-copy) när du kopierar från Azure Data Lake Storage gen1.

>[!IMPORTANT]
>Om du aktiverar alternativet **Tillåt betrodda Microsoft-tjänster för att komma åt det här lagrings kontot** på Azure Storage brand Väggs inställningar och vill använda Azure integration runtime för att ansluta till din data Lake Storage Gen2 måste du använda [hanterad identitets autentisering](#managed-identity) för ADLS Gen2.

>[!TIP]
>Om du aktiverar det hierarkiska namn området finns det för närvarande ingen samverkan mellan åtgärder mellan blob-och Data Lake Storage Gen2-API: er. Om du träffar felet "ErrorCode = FilesystemNotFound" med meddelandet "det angivna fil systemet finns inte", orsakas det av det angivna Sink-filsystemet som skapades via BLOB-API: et i stället för att Data Lake Storage Gen2 API någon annan stans. Åtgärda problemet genom att ange ett nytt fil system med ett namn som inte finns som namn på en BLOB-behållare. Sedan skapar Data Factory automatiskt fil systemet under data kopieringen.

## <a name="get-started"></a>Kom igång

>[!TIP]
>En genom gång av hur du använder Data Lake Storage Gen2-anslutningen finns i [läsa in data i Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Azure Data Lake Storage Gen2-anslutningen har stöd för följande typer av autentisering. Mer information finns i motsvarande avsnitt:

- [Autentisering av konto nyckel](#account-key-authentication)
- [Autentisering av tjänstens huvudnamn](#service-principal-authentication)
- [Hanterade identiteter för Azure-resurser-autentisering](#managed-identity)

>[!NOTE]
>När du använder PolyBase för att läsa in data i SQL Data Warehouse måste du använda hanterad identitetsautentisering som krävs för PolyBase om din käll Data Lake Storage Gen2 har kon figurer ATS med Virtual Network-slutpunkt. Se avsnittet [hanterad identitets autentisering](#managed-identity) med fler konfigurations krav.

### <a name="account-key-authentication"></a>Konto-nyckelautentisering

Om du vill använda nyckelautentisering för storage-konto, stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **AzureBlobFS**. |Ja |
| url | Slut punkt för Data Lake Storage Gen2 med mönstret `https://<accountname>.dfs.core.windows.net`. | Ja |
| accountKey | Konto nyckel för Data Lake Storage Gen2. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure integration runtime eller en lokal integration Runtime om ditt data lager finns i ett privat nätverk. Om den här egenskapen inte anges används standard körningen av Azure integration. |Nej |

>[!NOTE]
>Sekundär slut punkt för fil system för ADLS stöds inte när du använder autentisering med konto nycklar. Du kan använda andra typer av autentisering.

**Exempel:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Följ dessa steg om du vill använda autentisering av tjänstens huvud namn.

1. Registrera en program enhet i Azure Active Directory (Azure AD) genom att följa stegen i [Registrera ditt program med en Azure AD-klient](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

2. Ge tjänstens huvud behörighet rätt behörighet. Se exempel på hur behörigheten fungerar i Data Lake Storage Gen2 från [åtkomst kontrol listor på filer och kataloger](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Som källa**: i Storage Explorer beviljar du minst **Kör** behörighet för alla överordnade mappar och fil systemet, tillsammans med **Läs** behörighet för de filer som ska kopieras. I åtkomst kontroll (IAM) beviljar du minst rollen **Storage BLOB data Reader** .
    - **Som mottagare**: i Storage Explorer beviljar du minst **Kör** behörighet för alla överordnade mappar och fil systemet, tillsammans med **Skriv** behörighet för mappen mottagare. Alternativt, i åtkomst kontroll (IAM), beviljar du minst rollen **Storage BLOB data Contributor** .

>[!NOTE]
>Om du använder Data Factory gränssnittet för att redigera och tjänstens huvud namn inte är inställt med rollen "Storage BLOB data Reader/Contributor" i IAM, när du gör en test anslutning eller bläddrar/navigerar mappar, väljer du "Testa anslutning till fil Sök väg" eller "Bläddra från angiven sökväg" och anger en sökväg med behörigheten **läsa + kör** för att fortsätta.

De här egenskaperna stöds för den länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **AzureBlobFS**. |Ja |
| url | Slut punkt för Data Lake Storage Gen2 med mönstret `https://<accountname>.dfs.core.windows.net`. | Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en `SecureString` för att lagra det på ett säkert sätt i Data Factory. Eller så kan du [referera till en hemlighet som lagrats i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Hämta det genom att hovra musen i det övre högra hörnet av Azure Portal. | Ja |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure integration runtime eller en lokal integration Runtime om ditt data lager finns i ett privat nätverk. Om inget värde anges används standard Azure integration Runtime. |Nej |

**Exempel:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a>Hanterade identiteter för Azure-resurser-autentisering

En data fabrik kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md)som representerar den aktuella data fabriken. Du kan använda den här hanterade identiteten direkt för Data Lake Storage Gen2 autentisering, på samma sätt som du använder ditt eget tjänst huvud namn. Det gör att den här fabriken kan komma åt och kopiera data till eller från din Data Lake Storage Gen2.

Följ dessa steg om du vill använda hanterade identiteter för Azure Resource Authentication.

1. [Hämta data Factory hanterad identitets information](data-factory-service-identity.md#retrieve-managed-identity) genom att kopiera värdet för det **hanterade ID-objekt-ID: t** som genererats tillsammans med din fabrik.

2. Ge behörigheten hanterad identitet korrekt. Se exempel på hur behörigheten fungerar i Data Lake Storage Gen2 från [åtkomst kontrol listor på filer och kataloger](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Som källa**: i Storage Explorer beviljar du minst **Kör** behörighet för alla överordnade mappar och fil systemet, tillsammans med **Läs** behörighet för de filer som ska kopieras. I åtkomst kontroll (IAM) beviljar du minst rollen **Storage BLOB data Reader** .
    - **Som mottagare**: i Storage Explorer beviljar du minst **Kör** behörighet för alla överordnade mappar och fil systemet, tillsammans med **Skriv** behörighet för mappen mottagare. Alternativt, i åtkomst kontroll (IAM), beviljar du minst rollen **Storage BLOB data Contributor** .

>[!NOTE]
>Om du använder Data Factory användar gränssnitt för att redigera och den hanterade identiteten inte har angetts med rollen "Storage BLOB data Reader/Contributor" i IAM, vid test anslutning eller bläddring/navigera mappar, väljer du "Testa anslutning till fil Sök väg" eller "Bläddra från angiven sökväg" och anger en sökväg med behörigheten **läsa + kör** för att fortsätta.

>[!IMPORTANT]
>Om du använder PolyBase för att läsa in data från Data Lake Storage Gen2 till SQL Data Warehouse, ska du, när du använder hanterad identitetsautentisering för Data Lake Storage Gen2, kontrol lera att du också följer steg 1 och 2 i [den här vägledningen](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) till 1) registrera din SQL Database-server med Azure Active Directory (Azure AD) och 2) tilldela rollen Storage BLOB data Contributor till SQL Database Server. resten hanteras av Data Factory. Om din Data Lake Storage Gen2 har kon figurer ATS med en Azure Virtual Network-slutpunkt, för att använda PolyBase för att läsa in data från den, måste du använda hanterad identitetsautentisering som krävs av PolyBase.

De här egenskaperna stöds för den länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **AzureBlobFS**. |Ja |
| url | Slut punkt för Data Lake Storage Gen2 med mönstret `https://<accountname>.dfs.core.windows.net`. | Ja |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure integration runtime eller en lokal integration Runtime om ditt data lager finns i ett privat nätverk. Om inget värde anges används standard Azure integration Runtime. |Nej |

**Exempel:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [data uppsättningar](concepts-datasets-linked-services.md).

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Data Lake Storage Gen2 under `location` inställningar i den formatbaserade data uppsättningen:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Egenskapen Type under `location` i data uppsättningen måste anges till **AzureBlobFSLocation**. | Ja      |
| fileSystem | Namnet på filen med Data Lake Storage Gen2-systemet.                              | Nej       |
| folderPath | Sökvägen till en mapp under det angivna fil systemet. Om du vill använda ett jokertecken för att filtrera mappar, hoppar du över den här inställningen och anger den i aktivitets källans inställningar. | Nej       |
| fileName   | Fil namnet under angivet fileSystem + folderPath. Om du vill använda ett jokertecken för att filtrera filer, hoppar du över den här inställningen och anger den i aktivitets källans inställningar. | Nej       |

**Exempel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Kopiera aktivitets konfigurationer](copy-activity-overview.md#configuration) och [pipeliner och aktiviteter](concepts-pipelines-activities.md). Det här avsnittet innehåller en lista över egenskaper som stöds av Data Lake Storage Gen2 källa och mottagare.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 som typ av källa

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Data Lake Storage Gen2 under `storeSettings` inställningar i format-baserad kopierings Källa:

| Egenskap                 | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Egenskapen Type under `storeSettings` måste anges till **AzureBlobFSReadSettings**. | Ja                                           |
| recursive                | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. När rekursivt är inställt på True och sinken är en filbaserad lagring, kopieras inte en tom mapp eller undermapp till mottagaren. Tillåtna värden är **True** (standard) och **false**. | Nej                                            |
| wildcardFolderPath       | Mappsökvägen med jokertecken under det angivna fil systemet som kon figurer ATS i data uppsättningen för att filtrera källmappen. <br>Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Använd `^` för att kringgå om det faktiska mappnamnet har ett jokertecken eller detta escape-tecken inuti. <br>Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Nej                                            |
| wildcardFileName         | Fil namnet med jokertecken under angivet fil system + folderPath/wildcardFolderPath för att filtrera källfiler. <br>Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Använd `^` för att kringgå om det faktiska mappnamnet har ett jokertecken eller detta escape-tecken inuti. Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Ja om `fileName` inte har angetts i data uppsättningen |
| modifiedDatetimeStart    | Filter för filer baserat på det senast ändrade attributet. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br> Egenskaperna kan vara NULL, vilket innebär att inget filter för filattribut används för data uppsättningen. När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime är markerade. När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datum och tid är markerade. | Nej                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Nej                                            |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till lagrings lager samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej                                            |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
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
                    "type": "AzureBlobFSReadSettings",
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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 som en Mottagartyp

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Data Lake Storage Gen2 under `storeSettings` inställningar i format-baserad kopierings mottagare:

| Egenskap                 | Beskrivning                                                  | Krävs |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | Egenskapen Type under `storeSettings` måste anges till **AzureBlobFSWriteSettings**. | Ja      |
| copyBehavior             | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: bevarar filens hierarki i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><b>-FlattenHierarchy</b>: alla filer från källmappen är på den första nivån i målmappen. Målfiler har automatiskt genererade unika namn. <br/><b>-MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om filnamnet har angetts är namnet på sammanfogade filen det angivna namnet. Annars är det en automatiskt skapade filnamnet. | Nej       |
| blockSizeInMB | Ange den block storlek i MB som används för att skriva data till ADLS Gen2. Läs mer [om block-blobar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Tillåtet värde är **mellan 4 och 100 MB**. <br/>Som standard fastställer ADF automatiskt block storleken baserat på källans lagrings typ och data. För icke-binär kopia i ADLS Gen2 är standard block storleken 100 MB så att den passar i högst 4,95 TB data. Det kanske inte är optimalt om dina data inte är stora, särskilt när du använder självbetjänings Integration Runtime med dåligt nätverk som resulterar i drift tids gräns eller prestanda problem. Du kan uttryckligen ange en block storlek, men se till att blockSizeInMB * 50000 är tillräckligt stor för att lagra data, annars Miss kopie ras kopierings aktivitets körningen. | Nej |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till data lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej       |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
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
                    "type": "AzureBlobFSWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exempel på mapp-och fil filter

I det här avsnittet beskrivs det resulterande beteendet hos mappsökvägen och fil namnet med filter för jokertecken.

| folderPath | fileName | recursive | Källans mappstruktur och filter resultat (filer i **fetstil** hämtas)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Tom, Använd standard) | false | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**fil2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | (Tom, Använd standard) | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**fil2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | false | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**fil1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Några exempel rekursiv och copyBehavior

I det här avsnittet beskrivs det resulterande beteendet för kopierings åtgärden för olika kombinationer av rekursiva och copyBehavior värden.

| recursive | copyBehavior | Mappstruktur för källa | Resulterande mål |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Mål-Mapp1 skapas med samma struktur som källan:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1 + Fil2 + File3 + File4 + File5-innehåll sammanfogas till en fil med ett automatiskt genererat fil namn. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/><br/>Subfolder1 med File3, File4 och File5 har inte hämtats. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för Fil2<br/><br/>Subfolder1 med File3, File4 och File5 har inte hämtats. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1 + Fil2-innehåll sammanfogas till en fil med ett automatiskt genererat fil namn. automatiskt skapade namn på File1<br/><br/>Subfolder1 med File3, File4 och File5 har inte hämtats. |

## <a name="preserve-metadata-during-copy"></a>Bevara metadata under kopiering

När du kopierar filer från Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 till Azure Data Lake Storage Gen2/Azure-Blob kan du välja att bevara filens metadata tillsammans med data. Läs mer om att [bevara metadata](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Behåll ACL: er från Data Lake Storage Gen1

>[!TIP]
>Information om hur du kopierar data från Azure Data Lake Storage Gen1 till Gen2 i allmänhet finns i [Kopiera data från Azure Data Lake Storage gen1 till Gen2 med Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) för en genom gång och bästa praxis.

När du kopierar filer från Azure Data Lake Storage Gen1 till Gen2, kan du välja att behålla åtkomst kontrol listorna för POSIX (ACL) tillsammans med data. Läs mer [om att bevara ACL: er från data Lake Storage gen1 till Gen2](copy-activity-preserve-metadata.md#preserve-acls).

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

När du transformerar data i mappnings data flödet kan du läsa och skriva filer från Azure Data Lake Storage Gen2 i JSON, Avro, avgränsad text eller Parquet format. Mer information finns i omvandling av [käll omvandling](data-flow-source.md) och [mottagare](data-flow-sink.md) i funktionen mappa data flöde.

### <a name="source-transformation"></a>Käll omvandling

I käll omvandlingen kan du läsa från en behållare, mapp eller en enskild fil i Azure Data Lake Storage Gen2. På fliken **käll alternativ** kan du hantera hur filerna får läsas. 

![Käll alternativ](media/data-flow/sourceOptions1.png "Käll alternativ")

**Sökväg till jokertecken:** Genom att använda jokertecken instrueras ADF att loopa igenom varje matchande mapp och fil i en enda käll omvandling. Detta är ett effektivt sätt att bearbeta flera filer i ett enda flöde. Lägg till flera matchnings mönster för jokertecken med +-tecknet som visas när du hovrar över ditt befintliga mönster i jokertecken.

Från din käll behållare väljer du en serie filer som matchar ett mönster. Det går endast att ange container i data uppsättningen. Sökvägen till jokertecken måste därför även innehålla sökvägen till din mapp från rotmappen.

Jokertecken exempel:

* ```*``` representerar en uppsättning tecken
* ```**``` representerar rekursiv katalog kapsling
* ```?``` ersätter ett Character
* ```[]``` matchar ett tecken i hakparenteserna

* ```/data/sales/**/*.csv``` hämtar alla CSV-filer under/data/Sales
* ```/data/sales/20??/**``` hämtar alla filer i det 20-talet
* ```/data/sales/2004/*/12/[XY]1?.csv``` hämtar alla CSV-filer i 2004 i december som börjar med X eller Y som föregås av ett tvåsiffrigt tal

**Partitionens rot Sök väg:** Om du har partitionerade mappar i fil källan med ett ```key=value``` format (t. ex. Year = 2019) kan du tilldela den översta nivån i det partitionens mappträd till ett kolumn namn i data flödes data strömmen.

Ange först ett jokertecken för att inkludera alla sökvägar som är de partitionerade mapparna plus de löv-filer som du vill läsa.

![Inställningar för partitionens källfil](media/data-flow/partfile2.png "Fil inställning för partition")

Använd inställningen för partitionens rot Sök väg för att definiera vad mappstrukturen är. När du visar innehållet i dina data via en data förhands granskning kommer du att se att ADF lägger till de matchade partitioner som finns i alla mappar.

![Partitionens rot Sök väg](media/data-flow/partfile1.png "För hands version av partitionens rot Sök väg")

**Lista över filer:** Detta är en fil uppsättning. Skapa en textfil som innehåller en lista över relativa Sök vägs filer som ska bearbetas. Peka på den här text filen.

**Kolumn att lagra fil namn på:** Lagra namnet på käll filen i en kolumn i dina data. Ange ett nytt kolumn namn här för att lagra fil namn strängen.

**Efter slut för ande:** Välj att inte göra något med käll filen när data flödet körts, ta bort käll filen eller flytta käll filen. Sök vägarna för flytten är relativa.

Om du vill flytta källfilerna till en annan plats efter bearbetning väljer du först flytta för fil åtgärd. Ange sedan "från"-katalogen. Om du inte använder jokertecken för sökvägen, kommer inställningen från att vara samma mapp som källmappen.

Om du har en käll Sök väg med jokertecken så ser din syntax ut så här:

```/data/sales/20??/**/*.csv```

Du kan ange "från" som

```/data/sales```

Och "till" som

```/backup/priorSales```

I det här fallet flyttas alla filer som har ursprung under/data/Sales till/backup/priorSales.

> [!NOTE]
> Fil åtgärder körs bara när du startar data flödet från en pipeline-körning (en pipeline för pipeline-fel eller körning) som använder aktiviteten kör data flöde i en pipeline. Fil åtgärder körs *inte* i fel söknings läge för data flöde.

**Filtrera efter senast ändrad:** Du kan filtrera vilka filer du bearbetar genom att ange ett datum intervall när de senast ändrades. Alla datum-gånger är i UTC-tid. 

### <a name="sink-properties"></a>Egenskaper för mottagare

I omvandling av mottagare kan du skriva till antingen en behållare eller mapp i Azure Data Lake Storage Gen2. på fliken **Inställningar** kan du hantera hur filerna skrivs.

![mottagar alternativ](media/data-flow/file-sink-settings.png "mottagar alternativ")

**Rensa mappen:** Anger om målmappen ska rensas innan data skrivs.

**Fil namns alternativ:** Anger hur målfiler namnges i målmappen. Fil namns alternativen är:
   * **Standard**: Tillåt Spark att namnge filer baserat på del standarder.
   * **Mönster**: Ange ett mönster som räknar upp dina utdatafiler per partition. Till exempel kommer **lån [n]. csv** att skapa loans1. csv, loans2. csv och så vidare.
   * **Per partition**: Ange ett fil namn per partition.
   * **Som data i kolumnen**: Ange utdatafilen till värdet för en kolumn. Sökvägen är relativ i förhållande till data uppsättnings behållaren, inte målmappen.
   * **Utdata till en enda fil**: kombinera de partitionerade utdatafilerna till en enda namngiven fil. Sökvägen är relativ i förhållande till dataset-mappen. Tänk på att det kan hända att te-sammanfognings åtgärder inte fungerar baserat på Node-storlek. Det här alternativet rekommenderas inte för stora data uppsättningar.

**Offert alla:** Anger om alla värden ska anges i citat tecken

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Egenskaper för GetMetadata-aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [getMetaData-aktivitet](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Ta bort aktivitets egenskaper

Om du vill veta mer om egenskaperna kontrollerar du [ta bort aktivitet](delete-activity.md)

## <a name="legacy-models"></a>Äldre modeller

>[!NOTE]
>Följande modeller stöds fortfarande för bakåtkompatibilitet. Du rekommenderar att du använder den nya modellen som anges ovan och fortsätter, och redigerings gränssnittet för ADF har växlat till att generera den nya modellen.

### <a name="legacy-dataset-model"></a>Äldre data uppsättnings modell

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till **AzureBlobFSFile**. |Ja |
| folderPath | Sökväg till mappen i Data Lake Storage Gen2. Om den inte anges som den pekar till roten. <br/><br/>Wildcard-filtret stöds. Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Använd `^` för att kringgå om det faktiska mappnamnet har ett jokertecken eller om detta escape-tecken finns inuti. <br/><br/>Exempel: filesystem/Folder/. Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). |Nej |
| fileName | Namn eller Wildcard-filter för filerna under den angivna "folderPath". Om du inte anger ett värde för den här egenskapen datauppsättningen pekar på alla filer i mappen. <br/><br/>För filter är de jokertecken som tillåts `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken).<br/>– Exempel 1: `"fileName": "*.csv"`<br/>– Exempel 2: `"fileName": "???20180427.txt"`<br/>Använd `^` för att kringgå om det faktiska fil namnet har ett jokertecken eller om detta escape-tecken är inuti.<br/><br/>När fil namnet inte har angetts för en data uppsättning för utdata och **preserveHierarchy** inte har angetts i aktivitets mottagaren genererar kopierings aktiviteten automatiskt fil namnet med följande mönster: "*data. [ aktivitetens körnings-ID GUID]. [GUID om FlattenHierarchy]. [format om det är konfigurerat]. [komprimering om konfigurerad]* ", till exempel" data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz ". Om du kopierar från en tabell källa med ett tabell namn i stället för en fråga, är namn mönstret " *[tabell namn]. [ format]. [komprimering om konfigurerad]* ", till exempel" Table. csv ". |Nej |
| modifiedDatetimeStart | Filter för filer baserat på det senast ändrade attributet. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Den övergripande prestandan för data förflyttning påverkas om du aktiverar den här inställningen när du vill göra fil filter till stora mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget attribut filter används för data uppsättningen. När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime är markerade. När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datum och tid är markerade.| Nej |
| modifiedDatetimeEnd | Filter för filer baserat på det senast ändrade attributet. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Den övergripande prestandan för data förflyttning påverkas om du aktiverar den här inställningen när du vill göra fil filter till stora mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget attribut filter används för data uppsättningen. När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime är markerade. När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datum och tid är markerade.| Nej |
| format | Om du vill kopiera filer som finns mellan filbaserade lager (binär kopia), kan du hoppa över avsnittet format i både inkommande och utgående datamängd definitionerna.<br/><br/>Om du vill parsa eller generera filer med ett särskilt format stöds följande fil format **typer: text**format, **JsonFormat**, **AvroFormat**, **OrcFormat**och **ParquetFormat**. Ange egenskapen **Type** under **format** till något av dessa värden. Mer information finns i avsnitten [text format](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)och [Parquet format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nej (endast för binär kopia scenario) |
| compression | Ange typ och komprimeringsnivå för data. Mer information finns i [fil format och komprimerings-codecar som stöds](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Typer som stöds är **gzip**, **DEFLATE**, **BZip2**och **ZipDeflate**.<br/>De nivåer som stöds är **optimala** och **snabbaste**. |Nej |

>[!TIP]
>Om du vill kopiera alla filer under en mapp anger du endast **folderPath** .<br>Om du vill kopiera en enskild fil med ett visst namn anger du **folderPath** med en mapp **och ett fil namn** med ett fil namn.<br>Om du vill kopiera en delmängd av filer under en mapp anger du **folderPath** med en mapp och ett **fil namn** med ett Wildcard-filter. 

**Exempel:**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
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

### <a name="legacy-copy-activity-source-model"></a>Käll modell för äldre kopierings aktiviteter

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till **AzureBlobFSSource**. |Ja |
| recursive | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. När rekursivt är inställt på True och sinken är en filbaserad lagring, kopieras inte en tom mapp eller undermapp till mottagaren.<br/>Tillåtna värden är **True** (standard) och **false**. | Nej |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till data lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
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
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>Legacy kopierings aktivitet, mottagar modell

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för kopierings aktivitetens Sink måste anges till **AzureBlobFSSink**. |Ja |
| copyBehavior | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: bevarar filens hierarki i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><b>-FlattenHierarchy</b>: alla filer från källmappen är på den första nivån i målmappen. Målfiler har automatiskt genererade unika namn. <br/><b>-MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om filnamnet har angetts är namnet på sammanfogade filen det angivna namnet. Annars är det en automatiskt skapade filnamnet. | Nej |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till data lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Nästa steg

En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
