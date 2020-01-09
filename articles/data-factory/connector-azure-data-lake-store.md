---
title: Kopiera data till eller från Azure Data Lake Storage Gen1
description: Lär dig hur du kopierar data från käll data lager som stöds till Azure Data Lake Store eller från Data Lake Store till mottagar mottagar lager med hjälp av Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 701695c849a7f94abdba83f962806ecab3f21282
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440887"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Kopiera data till eller från Azure Data Lake Storage Gen1 med Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Azure Data Factory som du använder:"]
> * [Version 1](v1/data-factory-azure-datalake-connector.md)
> * [Aktuell version](connector-azure-data-lake-store.md)

Den här artikeln beskriver hur du kopierar data till och från Azure Data Lake Storage Gen1. Läs om Azure Data Factory den [introduktionsartikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Data Lake Storage Gen1 anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md) 
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)

Med den här anslutningen kan du särskilt:

- Kopiera filer med någon av följande autentiseringsmetoder: tjänstens huvud namn eller hanterade identiteter för Azure-resurser.
- Kopiera filer som är eller tolka eller generera filer med de [fil format och komprimerings-codecar som stöds](supported-file-formats-and-compression-codecs.md).
- [Bevara ACL: er](#preserve-acls-to-data-lake-storage-gen2) när du kopierar till Azure Data Lake Storage Gen2.

> [!IMPORTANT]
> Om du kopierar data med hjälp av integration runtime med egen värd konfigurerar du företags brand väggen så att utgående trafik kan `<ADLS account name>.azuredatalakestore.net` och `login.microsoftonline.com/<tenant>/oauth2/token` på port 443. Det sistnämnda är den Azure-säkerhetstokentjänst som integration runtime måste kommunicera med för att få åtkomst-token.

## <a name="get-started"></a>Kom i gång

> [!TIP]
> En genom gång av hur du använder Azure Data Lake Store-anslutningen finns i [läsa in data i Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Azure Data Lake Store.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den länkade tjänsten Azure Data Lake Store:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen `type` måste anges till **AzureDataLakeStore**. | Ja |
| dataLakeStoreUri | Information om Azure Data Lake Store-konto. Den här informationen antar ett av följande format: `https://[accountname].azuredatalakestore.net/webhdfs/v1` eller `adl://[accountname].azuredatalakestore.net/`. | Ja |
| subscriptionId | ID för Azure-prenumerationen som Data Lake Store kontot tillhör. | Krävs för mottagare |
| resourceGroupName | Namnet på den Azure-resurs grupp som Data Lake Store kontot tillhör. | Krävs för mottagare |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure integration runtime eller en lokal integration Runtime om ditt data lager finns i ett privat nätverk. Om den här egenskapen inte anges används standard körningen av Azure integration. |Inga |

### <a name="use-service-principal-authentication"></a>Använd tjänstens huvud namns autentisering

Följ dessa steg om du vill använda autentisering av tjänstens huvud namn.

1. Registrera en program enhet i Azure Active Directory och ge den till gång till Data Lake Store. Detaljerade anvisningar finns i [tjänst-till-tjänst-autentisering](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

2. Ge tjänstens huvud behörighet rätt behörighet. Se exempel på hur behörigheten fungerar i Data Lake Storage Gen1 från [åtkomst kontroll i Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Som källa**: i **data Utforskaren** > **åtkomst**, beviljar du minst **Kör** behörighet för alla överordnade mappar, inklusive roten, tillsammans med **Läs** behörighet för de filer som ska kopieras. Du kan välja att lägga till i **den här mappen och alla underordnade objekt** för rekursivt, och lägga till som **åtkomst behörighet och en standard behörighets post**. Det finns inget krav på konto nivå åtkomst kontroll (IAM).
    - **Som mottagare**: i **data Utforskaren** > **åtkomst**beviljas minst **Kör** behörighet för alla överordnade mappar, inklusive roten, tillsammans med **Skriv** behörighet för mappen mottagare. Du kan välja att lägga till i **den här mappen och alla underordnade objekt** för rekursivt, och lägga till som **åtkomst behörighet och en standard behörighets post**. Om du använder en Azure integration runtime för att kopiera (både källa och mottagare finns i molnet) kan du, i IAM, bevilja minst rollen **läsare** för att låta Data Factory identifiera regionen för data Lake Store. Om du vill undvika den här IAM-rollen kan du uttryckligen [skapa en Azure integration runtime](create-azure-integration-runtime.md#create-azure-ir) med platsen för data Lake Store. Om din Data Lake Store till exempel är i Västeuropa, skapar du en Azure integration runtime med plats inställt på "Västeuropa". Koppla dem till den länkade tjänsten Data Lake Store så som visas i följande exempel.

Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| servicePrincipalId | Ange programmets klient-ID. | Ja |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en `SecureString` för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | Ange klient information, till exempel domän namn eller klient-ID, under vilken ditt program finns. Du kan hämta den håller musen i det övre högra hörnet i Azure Portal. | Ja |

**Exempel:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a>Använda hanterade identiteter för Azure-resurser-autentisering

En data factory kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md), som representerar den här specifika data factory. Du kan använda den här hanterade identiteten direkt för Data Lake Store autentisering, på samma sätt som du använder ditt eget tjänst huvud namn. Det gör att den här fabriken kan komma åt och kopiera data till eller från Data Lake Store.

Följ dessa steg om du vill använda hanterade identiteter för autentisering med Azure-resurser.

1. [Hämta Data Factory-hanterad identitets information](data-factory-service-identity.md#retrieve-managed-identity) genom att kopiera värdet för "tjänst identitetens program-ID" som genererats tillsammans med din fabrik.

2. Ge åtkomst till hanterad identitet till Data Lake Store. Se exempel på hur behörigheten fungerar i Data Lake Storage Gen1 från [åtkomst kontroll i Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Som källa**: i **data Utforskaren** > **åtkomst**, beviljar du minst **Kör** behörighet för alla överordnade mappar, inklusive roten, tillsammans med **Läs** behörighet för de filer som ska kopieras. Du kan välja att lägga till i **den här mappen och alla underordnade objekt** för rekursivt, och lägga till som **åtkomst behörighet och en standard behörighets post**. Det finns inget krav på konto nivå åtkomst kontroll (IAM).
    - **Som mottagare**: i **data Utforskaren** > **åtkomst**beviljas minst **Kör** behörighet för alla överordnade mappar, inklusive roten, tillsammans med **Skriv** behörighet för mappen mottagare. Du kan välja att lägga till i **den här mappen och alla underordnade objekt** för rekursivt, och lägga till som **åtkomst behörighet och en standard behörighets post**. Om du använder en Azure integration runtime för att kopiera (både källa och mottagare finns i molnet) kan du, i IAM, bevilja minst rollen **läsare** för att låta Data Factory identifiera regionen för data Lake Store. Om du vill undvika den här IAM-rollen kan du uttryckligen [skapa en Azure integration runtime](create-azure-integration-runtime.md#create-azure-ir) med platsen för data Lake Store. Koppla dem till den länkade tjänsten Data Lake Store så som visas i följande exempel.

I Azure Data Factory behöver du inte ange några egenskaper förutom den allmänna Data Lake Store informationen i den länkade tjänsten.

**Exempel:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Azure Data Lake Store gen1 under `location` inställningar i den formatbaserade data uppsättningen:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Egenskapen Type under `location` i data uppsättningen måste anges till **AzureDataLakeStoreLocation**. | Ja      |
| folderPath | Sökvägen till en mapp. Om du vill använda ett jokertecken för att filtrera mappar, hoppar du över den här inställningen och anger den i aktivitets källans inställningar. | Inga       |
| fileName   | Fil namnet under den aktuella folderPath. Om du vill använda ett jokertecken för att filtrera filer, hoppar du över den här inställningen och anger den i aktivitets källans inställningar. | Inga       |

**Exempel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [pipelines](concepts-pipelines-activities.md). Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Data Lake Store källa och mottagare.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store som källa

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Azure Data Lake Store gen1 under `storeSettings` inställningar i den formatbaserade kopierings källan:

| Egenskap                 | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Egenskapen Type under `storeSettings` måste anges till **AzureDataLakeStoreReadSetting**. | Ja                                           |
| recursive                | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. När rekursivt är inställt på True och sinken är en filbaserad lagring, kopieras inte en tom mapp eller undermapp till mottagaren. Tillåtna värden är **SANT** (standard) och **FALSKT**. | Inga                                            |
| wildcardFolderPath       | Mappsökvägen med jokertecken för att filtrera källmappen. <br>Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Använd `^` för att kringgå om det faktiska mappnamnet har ett jokertecken eller detta escape-tecken inuti. <br>Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Inga                                            |
| wildcardFileName         | Fil namnet med jokertecken under den aktuella folderPath/wildcardFolderPath för att filtrera källfiler. <br>Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Använd `^` för att kringgå om det faktiska mappnamnet har ett jokertecken eller detta escape-tecken inuti. Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Ja om `fileName` inte har angetts i data uppsättningen |
| modifiedDatetimeStart    | Filter för filer baserat på det senast ändrade attributet. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br> Egenskaperna kan vara NULL, vilket innebär att inget attribut filter används för data uppsättningen. När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime är markerade. När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datum och tid är markerade. | Inga                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Inga                                            |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till lagrings lager samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Inga                                            |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
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
                    "type": "AzureDataLakeStoreReadSetting",
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

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store som mottagare

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Azure Data Lake Store gen1 under `storeSettings` inställningar i den formatbaserade kopierings mottagaren:

| Egenskap                 | Beskrivning                                                  | Krävs |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | Egenskapen Type under `storeSettings` måste anges till **AzureDataLakeStoreWriteSetting**. | Ja      |
| copyBehavior             | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: bevarar filen hierarkin i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><b>-FlattenHierarchy</b>: alla filer från källmappen finns i den första nivån i målmappen. Målfiler har automatiskt genererade unika namn. <br/><b>-MergeFiles</b>: slår samman alla filer från källmappen till en fil. Om filnamnet har angetts är namnet på sammanfogade filen det angivna namnet. Annars är det en automatiskt skapade filnamnet. | Inga       |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till data lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Inga       |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
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
                    "type": "AzureDataLakeStoreWriteSetting",
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
| `Folder*` | (Tom, Använd standard) | false | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | (Tom, Använd standard) | sant | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | false | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | sant | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Exempel på hur kopierings åtgärden fungerar

I det här avsnittet beskrivs det resulterande beteendet för kopierings åtgärden för olika kombinationer av `recursive` och `copyBehavior` värden.

| recursive | copyBehavior | Mappstruktur för källa | Resulterande mål |
|:--- |:--- |:--- |:--- |
| sant |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Mål-Mapp1 skapas med samma struktur som källan:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| sant |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt skapade namn på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File5 |
| sant |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1 + Fil2 + File3 + File4 + File5-innehåll sammanfogas till en fil med ett automatiskt genererat fil namn. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt skapade namn på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fil1 + Fil2-innehåll sammanfogas till en fil med automatiskt genererat fil namn. automatiskt skapade namn på File1<br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Bevara ACL: er till Data Lake Storage Gen2

>[!TIP]
>Information om hur du kopierar data från Azure Data Lake Storage Gen1 till Gen2 i allmänhet finns i [Kopiera data från Azure Data Lake Storage gen1 till Gen2 med Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) för en genom gång och bästa praxis.

Om du vill replikera åtkomst kontrol listorna (ACL) tillsammans med datafiler när du uppgraderar från Data Lake Storage Gen1 till Data Lake Storage Gen2, se [bevara ACL: er från data Lake Storage gen1](copy-activity-preserve-metadata.md#preserve-acls).

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

När du transformerar data i mappnings data flödet kan du läsa och skriva filer från Azure Data Lake Storage Gen1 i JSON, Avro, avgränsad text eller Parquet format. Mer information finns i omvandling av [käll omvandling](data-flow-source.md) och [mottagare](data-flow-sink.md) i funktionen mappa data flöde.

### <a name="source-transformation"></a>Käll omvandling

I käll omvandlingen kan du läsa från en behållare, mapp eller en enskild fil i Azure Data Lake Storage Gen1. På fliken **käll alternativ** kan du hantera hur filerna får läsas. 

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

I omvandling av mottagare kan du skriva till antingen en behållare eller mapp i Azure Data Lake Storage Gen1. på fliken **Inställningar** kan du hantera hur filerna skrivs.

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
| typ | Data uppsättningens typ-egenskap måste anges till **AzureDataLakeStoreFile**. |Ja |
| folderPath | Sökväg till mappen i Data Lake Store. Om den inte anges som den pekar till roten. <br/><br/>Wildcard-filtret stöds. Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Använd `^` för att kringgå om det faktiska mappnamnet har ett jokertecken eller detta escape-tecken inuti. <br/><br/>Till exempel: RootFolder/undermapp/. Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). |Inga |
| fileName | Namn eller Wildcard-filter för filerna under den angivna "folderPath". Om du inte anger ett värde för den här egenskapen datauppsättningen pekar på alla filer i mappen. <br/><br/>För filter är de jokertecken som tillåts `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken).<br/>– Exempel 1: `"fileName": "*.csv"`<br/>– Exempel 2: `"fileName": "???20180427.txt"`<br/>Använd `^` för att kringgå om det faktiska fil namnet har ett jokertecken eller detta escape-tecken inuti.<br/><br/>När fil namnet inte har angetts för en data uppsättning för utdata och **preserveHierarchy** inte har angetts i aktivitets mottagaren genererar kopierings aktiviteten automatiskt fil namnet med följande mönster: "*data. [ aktivitetens körnings-ID GUID]. [GUID om FlattenHierarchy]. [format om det är konfigurerat]. [komprimering om konfigurerad]* ", till exempel" data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz ". Om du kopierar från en tabell källa med ett tabell namn i stället för en fråga, är namn mönstret " *[tabell namn]. [ format]. [komprimering om konfigurerad]* ", till exempel" Table. csv ". |Inga |
| modifiedDatetimeStart | Filter för filer baserat på det senast ändrade attributet. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Den övergripande prestandan för data förflyttning påverkas om du aktiverar den här inställningen när du vill göra fil filter till stora mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget attribut filter används för data uppsättningen. När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime är markerade. När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datum och tid är markerade.| Inga |
| modifiedDatetimeEnd | Filter för filer baserat på det senast ändrade attributet. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Den övergripande prestandan för data förflyttning påverkas om du aktiverar den här inställningen när du vill göra fil filter till stora mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget attribut filter används för data uppsättningen. När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime är markerade. När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datum och tid är markerade.| Inga |
| format | Om du vill kopiera filer som är mellan filbaserade arkiv (binär kopia) hoppar du över avsnittet format i definitionerna för både indata och utdata.<br/><br/>Om du vill parsa eller generera filer med ett visst format format för följande filtyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, och **ParquetFormat**. Ange den **typ** egenskapen under **format** till någon av dessa värden. Mer information finns i den [textformat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro-formatet](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc-format](supported-file-formats-and-compression-codecs-legacy.md#orc-format), och [Parquet-format ](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) avsnitt. |Nej (endast för binär kopia scenario) |
| compression | Ange typ och komprimeringsnivå för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Typer som stöds är **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Stöds nivåer **Optimal** och **snabbast**. |Inga |

>[!TIP]
>Kopiera alla filer i en mapp genom att ange **folderPath** endast.<br>Om du vill kopiera en enskild fil med ett visst namn anger du **folderPath** med en mapp **och ett fil namn** med ett fil namn.<br>Om du vill kopiera en delmängd av filer under en mapp anger du **folderPath** med en mapp och ett **fil namn** med ett Wildcard-filter. 

**Exempel:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
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
| typ | Egenskapen `type` för kopierings aktivitets källan måste anges till **AzureDataLakeStoreSource**. |Ja |
| recursive | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. När `recursive` har värdet true och mottagaren är en filbaserad lagring, kopieras inte en tom mapp eller undermapp till mottagaren. Tillåtna värden är **SANT** (standard) och **FALSKT**. | Inga |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till data lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Inga |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
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
| typ | Egenskapen `type` för kopierings aktivitetens Sink måste anges till **AzureDataLakeStoreSink**. |Ja |
| copyBehavior | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: bevarar filen hierarkin i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><b>-FlattenHierarchy</b>: alla filer från källmappen finns i den första nivån i målmappen. Målfiler har automatiskt genererade unika namn. <br/><b>-MergeFiles</b>: slår samman alla filer från källmappen till en fil. Om filnamnet har angetts är namnet på sammanfogade filen det angivna namnet. Annars genereras fil namnet automatiskt. | Inga |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till data lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Inga |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Nästa steg

En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
