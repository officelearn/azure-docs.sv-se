---
title: Kopiera och omvandla data i Azure Data Lake Storage Gen2
description: Lär dig hur du kopierar data till och från Azure Data Lake Storage Gen2 och omvandlar data i Azure Data Lake Storage Gen2 med hjälp av Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: a061df302680488377c3006dcef57b818a101d67
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011539"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Kopiera och omvandla data i Azure Data Lake Storage Gen2 med Azure Data Factory

Azure Data Lake Storage Gen2 (ADLS Gen2) är en uppsättning funktioner som är avsedda för stordataanalys som är inbyggda i [Azure Blob-lagring](../storage/blobs/storage-blobs-introduction.md). Du kan använda den för att samverka med dina data med hjälp av både filsystem och objektlagringsparadigm.

I den här artikeln beskrivs hur du använder Kopiera aktivitet i Azure Data Factory för att kopiera data från och till Azure Data Lake Storage Gen2 och använda dataflöde för att omvandla data i Azure Data Lake Storage Gen2. Mer information om Azure Data Factory finns i den [inledande artikeln](introduction.md).

>[!TIP]
>För datasjö- eller informationslagermigreringsscenario kan du läsa mer från [Använd Azure Data Factory för att migrera data från datasjöen eller informationslager till Azure](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Data Lake Storage Gen2-kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Mappa dataflöde](concepts-data-flow-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)

För kopieringsaktivitet kan du med den här kopplingen:

- Kopiera data från/till Azure Data Lake Storage Gen2 med hjälp av kontonyckel, tjänsthuvudnamn eller hanterade identiteter för Azure-resurserautentiseringar.
- Kopiera filer som de är eller tolka eller generera filer med [filformat och komprimeringskodicer som stöds](supported-file-formats-and-compression-codecs.md).
- [Bevara filmetadata under kopieringen](#preserve-metadata-during-copy).
- [Bevara ACL:er](#preserve-acls) vid kopiering från Azure Data Lake Storage Gen1/Gen2.

>[!IMPORTANT]
>Om du aktiverar alternativet **Tillåt betrodda Microsoft-tjänster för att komma åt det här lagringskontot** på Azure Storage-brandväggsinställningar och vill använda Azure-integreringskörning för att ansluta till din DataSjölagringsgen2, måste du använda [hanterad identitetsautentisering](#managed-identity) för ADLS Gen2.


## <a name="get-started"></a>Kom igång

>[!TIP]
>En genomgång av hur du använder Data Lake Storage Gen2-kopplingen finns [i Läs in data i Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Azure Data Lake Storage Gen2-anslutningen stöder följande autentiseringstyper. Mer information finns i motsvarande avsnitt:

- [Autentisering av kontonyckel](#account-key-authentication)
- [Autentisering av tjänstens huvudnamn](#service-principal-authentication)
- [Hanterade identiteter för Azure-resursautentisering](#managed-identity)

>[!NOTE]
>När du använder PolyBase för att läsa in data i SQL Data Warehouse, om källan Data Lake Storage Gen2 är konfigurerad med slutpunkten för virtuellt nätverk, måste du använda autentisering av hanterad identitet enligt PolyBase. Se avsnittet [autentisering av hanterad identitet](#managed-identity) med fler konfigurationsförutnämningar.

### <a name="account-key-authentication"></a>Autentisering av kontonyckel

Så här använder du autentisering av lagringskontonyckel:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste anges till **AzureBlobFS**. |Ja |
| url | Slutpunkt för Data Lake Storage Gen2 med mönstret `https://<accountname>.dfs.core.windows.net`. | Ja |
| kontoNyckel | Kontonyckel för Data Lake Storage Gen2. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Du kan använda Azure-integreringskörningen eller en självvärderad integrationskörning om ditt datalager finns i ett privat nätverk. Om den här egenskapen inte anges används standardkörningen för Azure-integrering. |Inga |

>[!NOTE]
>Sekundär ADLS-filsystemslutpunkt stöds inte när du använder autentisering av kontonyckel. Du kan använda andra autentiseringstyper.

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

Så här använder du autentisering av tjänstens huvudnamn.

1. Registrera en programentitet i Azure Active Directory (Azure AD) genom att följa stegen i [Registrera ditt program med en Azure AD-klientorganisation](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anteckna följande värden, som du använder för att definiera den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

2. Ge tjänstens huvudnamn rätt tillstånd. Se exempel på hur behörighet fungerar i Data Lake Storage Gen2 från [Access-kontrolllistor för filer och kataloger](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Som källa:** I Storage Explorer, bevilja åtminstone **Kör** behörighet för alla uppströms mappar och filsystemet, tillsammans med **läsbehörighet** för filerna att kopiera. Alternativt i Access control (IAM) beviljar du minst rollen **Storage Blob Data Reader.**
    - **Som mottagare:** I Storage Explorer, bevilja åtminstone **Kör** behörighet för alla uppströms mappar och filsystemet, tillsammans med **skrivbehörighet** för sink-mappen. Alternativt i Access control (IAM) beviljar du minst rollen **Storage Blob Data Contributor.**

>[!NOTE]
>Om du använder Data Factory UI för att skapa och tjänstens huvudnamn inte är inställt med rollen "Storage Blob Data Reader/Contributor" i IAM väljer du "Testa anslutning till filsökväg" eller "Bläddra från angiven sökväg" när du utför testanslutnings- eller surfnings-/navigeringsmappar och ange en sökväg med behörigheten **Läs + Kör** för att fortsätta.

Dessa egenskaper stöds för den länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste anges till **AzureBlobFS**. |Ja |
| url | Slutpunkt för Data Lake Storage Gen2 med mönstret `https://<accountname>.dfs.core.windows.net`. | Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja |
| servicePrincipalKey | Ange programmets nyckel. Markera det här `SecureString` fältet som ett för att lagra det säkert i Data Factory. Du kan också [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | Ange klientinformation (domännamn eller klient-ID) som programmet finns under. Hämta den genom att hålla musen i det övre högra hörnet av Azure-portalen. | Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Du kan använda Azure-integreringskörningen eller en självvärderad integrationskörning om ditt datalager finns i ett privat nätverk. Om inget anges används standardkörningen för Azure-integrering. |Inga |

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Hanterade identiteter för Azure-resursautentisering

En datafabrik kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md), som representerar den här specifika datafabriken. Du kan direkt använda den här hanterade identiteten för Data Lake Storage Gen2-autentisering, på samma sätt som med ditt eget tjänsthuvudnamn. Det gör att denna utsedda fabrik för att komma åt och kopiera data till eller från din Data Lake Storage Gen2.

Så här använder du hanterade identiteter för Azure-resursautentisering.

1. [Hämta datafabrikens hanterade identitetsinformation](data-factory-service-identity.md#retrieve-managed-identity) genom att kopiera värdet för det **hanterade identitetsobjekt-ID** som genereras tillsammans med din fabrik.

2. Bevilja den hanterade identiteten rätt behörighet. Se exempel på hur behörighet fungerar i Data Lake Storage Gen2 från [Access-kontrolllistor för filer och kataloger](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Som källa:** I Storage Explorer, bevilja åtminstone **Kör** behörighet för alla uppströms mappar och filsystemet, tillsammans med **läsbehörighet** för filerna att kopiera. Alternativt i Access control (IAM) beviljar du minst rollen **Storage Blob Data Reader.**
    - **Som mottagare:** I Storage Explorer, bevilja åtminstone **Kör** behörighet för alla uppströms mappar och filsystemet, tillsammans med **skrivbehörighet** för sink-mappen. Alternativt i Access control (IAM) beviljar du minst rollen **Storage Blob Data Contributor.**

>[!NOTE]
>Om du använder datafabrikens användargränssnitt för att skapa och den hanterade identiteten inte är inställd med rollen "Storage Blob Data Reader/Contributor" i IAM väljer du "Testa anslutning till filsökväg" eller "Bläddra från angiven sökväg" när du utför testanslutnings- eller surfnings-/navigeringsmappar och ange en sökväg med behörigheten **Läs + Kör** för att fortsätta.

>[!IMPORTANT]
>Om du använder PolyBase för att läsa in data från Data Lake Storage Gen2 i SQL Data Warehouse, när du använder hanterad identitetsautentisering för Data Lake Storage Gen2, kontrollerar du att du också följer steg 1 och 2 i [den här vägledningen](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) till 1) registrera SQL Database-servern med Azure Active Directory (Azure AD) och 2) tilldela rollen Storage Blob Data Contributor till sql Database-servern. resten hanteras av Data Factory. Om din Data Lake Storage Gen2 är konfigurerad med en Azure Virtual Network-slutpunkt, för att använda PolyBase för att läsa in data från den, måste du använda hanterad identitetsautentisering enligt PolyBase.

Dessa egenskaper stöds för den länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste anges till **AzureBlobFS**. |Ja |
| url | Slutpunkt för Data Lake Storage Gen2 med mönstret `https://<accountname>.dfs.core.windows.net`. | Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Du kan använda Azure-integreringskörningen eller en självvärderad integrationskörning om ditt datalager finns i ett privat nätverk. Om inget anges används standardkörningen för Azure-integrering. |Inga |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [Datauppsättningar](concepts-datasets-linked-services.md).

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för DataSjölagring `location` Gen2 under inställningar i den formatbaserade datauppsättningen:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Egenskapen type `location` under i datauppsättningen måste anges till **AzureBlobFSLocation**. | Ja      |
| Filesystem | Filsystemnamnet Data Lake Storage Gen2.                              | Inga       |
| folderPath | Sökvägen till en mapp under det angivna filsystemet. Om du vill använda ett jokertecken för att filtrera mappar hoppar du över den här inställningen och anger den i aktivitetskällans inställningar. | Inga       |
| fileName   | Filnamnet under den angivna filenSystem + folderPath. Om du vill använda ett jokertecken för att filtrera filer hoppar du över den här inställningen och anger den i aktivitetskällans inställningar. | Inga       |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Kopiera aktivitetskonfigurationer](copy-activity-overview.md#configuration) och [Pipelines och aktiviteter](concepts-pipelines-activities.md). Det här avsnittet innehåller en lista över egenskaper som stöds av källan och diskbänken Data Lake Storage Gen2.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 som källtyp

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för DataSjölagring `storeSettings` Gen2 under inställningar i formatbaserad kopieringskälla:

| Egenskap                 | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Typegenskapen under `storeSettings` måste anges till **AzureBlobFSReadSettings**. | Ja                                           |
| Rekursiv                | Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. När rekursiv är inställd på true och diskhon är ett filbaserat arkiv kopieras eller skapas inte en tom mapp eller undermapp i diskhon. Tillåtna värden är **sanna** (standard) och **falska**. | Inga                                            |
| jokerteckenMappspath       | Mappsökvägen med jokertecken under det angivna filsystemet som konfigurerats i datauppsättningen för att filtrera källmappar. <br>Tillåtna jokertecken `*` är (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken). Används `^` för att fly om ditt faktiska mappnamn har ett jokertecken eller den här flykttecknet inuti. <br>Se fler exempel i [exempel på mapp- och filfilter](#folder-and-file-filter-examples). | Inga                                            |
| jokerteckenFileName         | Filnamnet med jokertecken under det angivna filsystemet + folderPath/wildcardFolderPath för att filtrera källfiler. <br>Tillåtna jokertecken `*` är (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken). Används `^` för att fly om ditt faktiska mappnamn har ett jokertecken eller den här flykttecknet inuti. Se fler exempel i [exempel på mapp- och filfilter](#folder-and-file-filter-examples). | Ja `fileName` om inte anges i datauppsättningen |
| modifiedDatetimeStart    | Filfilter baserat på attributet Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` `modifiedDatetimeEnd`ligger inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br> Egenskaperna kan vara NULL, vilket innebär att inget filattributfilter tillämpas på datauppsättningen. När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är NULL betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet väljs. När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är NULL betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet är markerade. | Inga                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Inga                                            |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till lagringsarkivet samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga                                            |

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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 som en sink-typ

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för DataSjölagring `storeSettings` Gen2 under inställningar i formatbaserad kopia sink:

| Egenskap                 | Beskrivning                                                  | Krävs |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | Typegenskapen under `storeSettings` måste anges till **AzureBlobFSWriteSettings**. | Ja      |
| kopieraBehavior             | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>- PreserveHierarchy (standard):</b>Bevarar filhierarkin i målmappen. Källfilens relativa sökväg till källmappen är identisk med målfilens relativa sökväg till målmappen.<br/><b>- FlattenHierarchy:</b>Alla filer från källmappen är i den första nivån i målmappen. Målfilerna har automatiskt urartade namn. <br/><b>- MergeFiles:</b>Sammanfogar alla filer från källmappen till en fil. Om filnamnet anges är det kopplade filnamnet det angivna namnet. Annars är det ett autogenererat filnamn. | Inga       |
| blockSizeInMB | Ange blockstorleken i MB som används för att skriva data till ADLS Gen2. Läs mer [om Block Blobbar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Tillåtet värde är **mellan 4 och 100 MB**. <br/>Som standard bestämmer ADF automatiskt blockstorleken baserat på källlagringstypen och data. För icke-binär kopia till ADLS Gen2 är standardblockstorleken 100 MB för att få plats med högst 4,95 TB-data. Det kanske inte är optimalt när dina data inte är stora, särskilt när du använder Självvärdförd integrationskörningstid med dålig nätverkstimeout eller prestandaproblem. Du kan uttryckligen ange en blockstorlek, medan se till att blockSizeInMB*50000 är tillräckligt stor för att lagra data, annars misslyckas kopieringsaktivitetskörningen. | Inga |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till datalagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga       |

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

### <a name="folder-and-file-filter-examples"></a>Exempel på mapp- och filfilter

I det här avsnittet beskrivs det resulterande beteendet för mappsökvägen och filnamnet med jokerteckenfilter.

| folderPath | fileName | Rekursiv | Källmappstruktur och filterresultat (filer i **fetstil** hämtas)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Tom, använd standard) | false | FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| `Folder*` | (Tom, använd standard) | true | FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| `Folder*` | `*.csv` | false | FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |
| `Folder*` | `*.csv` | true | FolderA (FolderA)<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arkiv5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Några rekursiva och copyBehavior exempel

I det här avsnittet beskrivs det resulterande beteendet för kopieringsåtgärden för olika kombinationer av rekursiva värden och copyBehavior-värden.

| Rekursiv | kopieraBehavior | Källmappstruktur | Resulterande mål |
|:--- |:--- |:--- |:--- |
| true |bevaraHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 | Målmapp1 skapas med samma struktur som källan:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 |
| true |plattaHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 | Målmapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för File5 |
| true |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 | Målmapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 innehåll slås samman till en fil med ett autogenererat filnamn. |
| false |bevaraHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 | Målmapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/><br/>Undermapp1 med File3, File4 och File5 plockas inte upp. |
| false |plattaHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 | Målmapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenererat namn för File2<br/><br/>Undermapp1 med File3, File4 och File5 plockas inte upp. |
| false |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arkiv2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Undermapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arkiv5 | Målmapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + File2-innehåll sammanfogas till en fil med ett filnamn som skapas automatiskt. autogenererat namn för Arkiv1<br/><br/>Undermapp1 med File3, File4 och File5 plockas inte upp. |

## <a name="preserve-metadata-during-copy"></a>Bevara metadata under kopiering

När du kopierar filer från Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 till Azure Data Lake Storage Gen2/Azure Blob kan du välja att bevara filmetadata tillsammans med data. Läs mer från [Bevara metadata](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="preserve-acls-from-data-lake-storage-gen1gen2"></a><a name="preserve-acls"></a>Bevara ACL:er från Data Lake Storage Gen1/Gen2

När du kopierar filer från Azure Data Lake Storage Gen1/Gen2 till Gen2 kan du välja att bevara POSIX-åtkomstkontrollistorna (ACL: er) tillsammans med data. Läs mer från [Bevara ACL:er från Data Lake Storage Gen1/Gen2 till Gen2](copy-activity-preserve-metadata.md#preserve-acls).

>[!TIP]
>Information om hur du kopierar data från Azure Data Lake Storage Gen1 till Gen2 i allmänhet finns i [Kopiera data från Azure Data Lake Storage Gen1 till Gen2 med Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) för en genomgång och metodtips.

## <a name="mapping-data-flow-properties"></a>Mappa dataflödesegenskaper

När du omvandlar data i mappningsdataflödet kan du läsa och skriva filer från Azure Data Lake Storage Gen2 i JSON-, Avro-, avgränsat text- eller parkettformat. Mer information finns i [källomvandling](data-flow-source.md) och [sink-omvandling](data-flow-sink.md) i funktionen för mappningsdataflöde.

### <a name="source-transformation"></a>Omvandling av källa

I källomvandlingen kan du läsa från en behållare, mapp eller en enskild fil i Azure Data Lake Storage Gen2. På fliken **Källalternativ** kan du hantera hur filerna läss. 

![Alternativ för källa](media/data-flow/sourceOptions1.png "Alternativ för källa")

**Jokerteckens sökväg:** Med hjälp av ett jokerteckenmönster instruerar ADF att loopa igenom varje matchande mapp och fil i en enda källomvandling. Detta är ett effektivt sätt att bearbeta flera filer inom ett enda flöde. Lägg till flera matchningsmönster med +-tecknet som visas när du hovrar över ditt befintliga jokerteckenmönster.

Välj en serie filer som matchar ett mönster från källbehållaren. Endast behållare kan anges i datauppsättningen. Jokertecknets sökväg måste därför också innehålla mappsökvägen från rotmappen.

Exempel på jokertecken:

* ```*```Representerar alla teckenuppsättningar
* ```**```Representerar rekursiv katalogkapsling
* ```?```Ersätter ett tecken
* ```[]```Matchar ett av flera tecken inom parenteserna

* ```/data/sales/**/*.csv```Hämtar alla csv-filer under /data/sales
* ```/data/sales/20??/**/```Hämtar alla filer på 1900-talet
* ```/data/sales/*/*/*.csv```Hämtar csv-filer två nivåer under /data/sales
* ```/data/sales/2004/*/12/[XY]1?.csv```Hämtar alla csv-filer under 2004 i december med början med X eller Y som föregås av ett tvåsiffrigt nummer

**Sökvägen till partitionsrot:** Om du har partitionerade mappar i ```key=value``` filkällan med ett format (till exempel year=2019) kan du tilldela den översta nivån i det partitionsmappträdet till ett kolumnnamn i dataflödesdataströmmen.

Ange först ett jokertecken så att alla sökvägar som är de partitionerade mapparna plus lövfilerna som du vill läsa.

![Inställningar för partitionskälla](media/data-flow/partfile2.png "Partitionsfilinställning")

Använd inställningen Partition Root Path för att definiera vad den översta nivån i mappstrukturen är. När du visar innehållet i dina data via en förhandsgranskning av data ser du att ADF lägger till de lösta partitionerna som finns i var och en av mappnivåerna.

![Partitionsrotsökväg](media/data-flow/partfile1.png "Förhandsgranskning av partitionsrotsökväg")

**Lista över filer:** Det här är en filuppsättning. Skapa en textfil som innehåller en lista över relativa sökvägsfiler som ska bearbetas. Peka på den här textfilen.

**Kolumn för att lagra filnamn:** Lagra namnet på källfilen i en kolumn i dina data. Ange ett nytt kolumnnamn här för att lagra filnamnssträngen.

**Efter avslutad:** Välj att inte göra något med källfilen när dataflödet har körts, källfilen tas bort eller flyttar källfilen. Sökvägarna för flytten är relativa.

Om du vill flytta källfiler till en annan plats efterbearbetningen väljer du först "Flytta" för filåtgärd. Ställ sedan in katalogen "från". Om du inte använder några jokertecken för sökvägen kommer inställningen "från" att vara samma mapp som källmappen.

Om du har en källsökväg med jokertecken ser syntaxen ut så här nedan:

```/data/sales/20??/**/*.csv```

Du kan ange "från" som

```/data/sales```

Och "till" som

```/backup/priorSales```

I det här fallet flyttas alla filer som har hämtats under /data/sales till /backup/priorSales.

> [!NOTE]
> Filåtgärder körs bara när du startar dataflödet från en pipeline-körning (en pipeline-felsökning eller körningskörning) som använder aktiviteten Kör dataflöde i en pipeline. Filåtgärder körs *inte* i felsökningsläge för dataflöde.

**Filtrera efter senast ändrad:** Du kan filtrera vilka filer du bearbetar genom att ange ett datumintervall för när de senast ändrades. Alla datumtider finns i UTC. 

### <a name="sink-properties"></a>Sink-egenskaper

I sink-omvandlingen kan du skriva till antingen en behållare eller mapp i Azure Data Lake Storage Gen2. På fliken **Inställningar** kan du hantera hur filerna skrivs.

![sink alternativ](media/data-flow/file-sink-settings.png "sink alternativ")

**Rensa mappen:** Avgör om målmappen rensas innan data skrivs eller inte.

**Alternativet Filnamn:** Bestämmer hur målfilerna namnges i målmappen. Alternativen för filnamn är:
   * **Standard**: Tillåt Spark att namnge filer baserat på DEL-standardvärden.
   * **Mönster**: Ange ett mönster som räknar upp dina utdatafiler per partition. Till exempel, **lån [n].csv** kommer att skapa loans1.csv, loans2.csv, och så vidare.
   * **Per partition**: Ange ett filnamn per partition.
   * **Som data i kolumnen**: Ange utdatafilen till värdet för en kolumn. Sökvägen är relativ till datamängdsbehållaren, inte målmappen. Om du har en mappsökväg i datauppsättningen åsidosätts den.
   * **Utdata till en enda fil**: Kombinera de partitionerade utdatafilerna till en enda namngiven fil. Sökvägen är relativ till datauppsättningsmappen. Tänk på att åtgärden för kopplad te kan eventuellt misslyckas baserat på nodstorlek. Det här alternativet rekommenderas inte för stora datauppsättningar.

**Citera alla:** Bestämmer om alla värden ska omslutas inom citationstecken

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
| typ | Egenskapen Type property för datauppsättningen måste anges till **AzureBlobFSFile**. |Ja |
| folderPath | Sökväg till mappen i Gen2 för lagring av datasjö. Om det inte anges pekar den på roten. <br/><br/>Jokerteckenfilter stöds. Tillåtna jokertecken `*` är (matchar noll `?` eller fler tecken) och (matchar noll eller ett tecken). Används `^` för att fly om ditt faktiska mappnamn har ett jokertecken eller om den här flykttecknet finns inuti. <br/><br/>Exempel: filsystem/mapp/. Se fler exempel i [exempel på mapp- och filfilter](#folder-and-file-filter-examples). |Inga |
| fileName | Namn eller jokerteckenfilter för filerna under den angivna "folderPath". Om du inte anger något värde för den här egenskapen pekar datauppsättningen på alla filer i mappen. <br/><br/>För filter är `*` de tillåtna jokerteckenen (matchar `?` noll eller fler tecken) och (matchar noll eller ett tecken).<br/>- Exempel 1:`"fileName": "*.csv"`<br/>- Exempel 2:`"fileName": "???20180427.txt"`<br/>Används `^` för att fly om ditt faktiska filnamn har ett jokertecken eller om den här flykttecknet finns inuti.<br/><br/>När filnamn inte anges för en utdatauppsättning och **bevaraHierarchy** inte anges i aktivitetsmottagaren, genererar kopieringsaktiviteten automatiskt filnamnet med följande mönster: "*Data.[ aktivitetskörning ID GUID]. [GUID om FlattenHierarchy]. [format om det är konfigurerat]. [komprimering om den är konfigurerad]*", till exempel "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Om du kopierar från en tabellkälla med ett tabellnamn i stället för en fråga, är namnmönstret "*[tabellnamn].[ format]. [komprimering om den är konfigurerad]*", till exempel "MyTable.csv". |Inga |
| modifiedDatetimeStart | Filfilter baserat på attributet Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` `modifiedDatetimeEnd`ligger inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Den övergripande prestandan för data förflyttning påverkas genom att aktivera den här inställningen när du vill göra filfilter med stora mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget filattributfilter tillämpas på datauppsättningen. När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är NULL betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet är markerade. När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är NULL betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet är markerade.| Inga |
| modifiedDatetimeEnd | Filfilter baserat på attributet Senast ändrad. Filerna väljs om deras senaste ändrade tid `modifiedDatetimeStart` `modifiedDatetimeEnd`ligger inom tidsintervallet mellan och . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Den övergripande prestandan för data förflyttning påverkas genom att aktivera den här inställningen när du vill göra filfilter med stora mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget filattributfilter tillämpas på datauppsättningen. När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är NULL betyder det att de filer vars senast ändrade attribut är större än eller lika med datetime-värdet är markerade. När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är NULL betyder det att de filer vars senast ändrade attribut är mindre än datetime-värdet är markerade.| Inga |
| format | Om du vill kopiera filer som mellan filbaserade butiker (binär kopia) hoppar du över formatavsnittet i både indata- och utdatauppsättningsdefinitionerna.<br/><br/>Om du vill tolka eller generera filer med ett visst format stöds följande filformattyper: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**och **ParquetFormat**. Ange **type** typegenskapen under **format** till ett av dessa värden. Mer information finns i avsnitten [Textformat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro-format,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [ORC-format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)och [Parkettformat.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Nej (endast för binärt kopieringsscenario) |
| komprimering | Ange typ och komprimeringsnivå för data. Mer information finns i [Filformat och komprimeringskodicer som stöds](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Typer som stöds är **GZip**, **Deflate**, **BZip2**och **ZipDeflate**.<br/>Nivåer som stöds är **optimala** och **snabbaste.** |Inga |

>[!TIP]
>Om du vill kopiera alla filer under en mapp anger du endast **folderPath.**<br>Om du vill kopiera en enskild fil med ett förnamn anger du **folderPath** med en mappdel och **filnamn** med ett filnamn.<br>Om du vill kopiera en delmängd filer under en mapp anger du **folderPath** med en mappdel och **filnamn** med ett jokerteckenfilter. 

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

### <a name="legacy-copy-activity-source-model"></a>Källmodell för äldre kopieringsaktivitet

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste anges till **AzureBlobFSSource**. |Ja |
| Rekursiv | Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. När rekursiv är inställd på true och diskhon är ett filbaserat arkiv kopieras eller skapas inte en tom mapp eller undermapp i diskhon.<br/>Tillåtna värden är **sanna** (standard) och **falska**. | Inga |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till datalagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga |

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

### <a name="legacy-copy-activity-sink-model"></a>Äldre modell för referensmodell för kopierad aktivitet

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för copy activity sink måste anges till **AzureBlobFSSink**. |Ja |
| kopieraBehavior | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>- PreserveHierarchy (standard):</b>Bevarar filhierarkin i målmappen. Källfilens relativa sökväg till källmappen är identisk med målfilens relativa sökväg till målmappen.<br/><b>- FlattenHierarchy:</b>Alla filer från källmappen är i den första nivån i målmappen. Målfilerna har automatiskt urartade namn. <br/><b>- MergeFiles:</b>Sammanfogar alla filer från källmappen till en fil. Om filnamnet anges är det kopplade filnamnet det angivna namnet. Annars är det ett autogenererat filnamn. | Inga |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till datalagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga |

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

En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Data Factory finns i [Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
