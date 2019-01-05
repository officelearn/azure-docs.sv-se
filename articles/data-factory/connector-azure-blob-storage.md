---
title: Kopiera data till och från Azure Blob storage med hjälp av Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från datalager som stöds till Azure Blob storage eller från Blob storage till mottagarens datalager, med hjälp av Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: a020b0a803fd9cfd00dd7da2fa6d28388bc268a3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53095354"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Kopiera data till och från Azure Blob storage med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj vilken version av Data Factory-tjänsten du använder:"]
> * [Version 1](v1/data-factory-azure-blob-connector.md)
> * [Aktuell version](connector-azure-blob-storage.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data till och från Azure Blob storage. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

Läs om Azure Data Factory den [introduktionsartikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från alla dataarkiv till Blob storage. Du kan också kopiera data från Blob storage till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md) tabell.

Mer specifikt stöder denna Blob storage-anslutning:

- Kopiera BLOB-objekt till och från Allmänt Azure storage-konton och frekvent/lågfrekvent – blob storage. 
- Kopiera blobar med hjälp av kontonyckel, signatur för delad åtkomst för tjänsten, tjänstens huvudnamn eller hanterade identiteter för Azure-resurser autentiseringar.
- Kopiera blobar från block, lägga till eller page blobs och kopiera data till endast blockblob-objekt.
- Kopiera BLOB-objekt som är eller parsning eller genererar-objekt med [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>Om du aktiverar _”Tillåt att betrodda Microsoft-tjänster för att komma åt det här lagringskontot”_ alternativet på brandväggsinställningar för Azure Storage med hjälp av Azure Integration Runtime kan ansluta till Blob storage misslyckas med förbjudet fel, vilket inte är ADF behandlas som betrodda Microsoft-tjänst. Använd lokal Integration Runtime som ansluter via i stället.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Blob storage.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Azure Blob-anslutningsapp stöd för följande autentiseringstyper av, se informationen om motsvarande avsnitt:

- [Konto-nyckelautentisering](#account-key-authentication)
- [Autentisering med signatur för delad åtkomst](#shared-access-signature-authentication)
- [Autentisering av tjänstens huvudnamn](#service-principal-authentication)
- [Hanterade identiteter för autentisering av Azure-resurser](#managed-identity)

>[!NOTE]
>HDInsights, Azure Machine Learning och Azure SQL Data Warehouse PolyBase laddning stöder bara nyckelautentisering för Azure Blob storage-konto.

### <a name="account-key-authentication"></a>Konto-nyckelautentisering

Om du vill använda nyckelautentisering för storage-konto, stöds följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till **AzureBlobStorage** (rekommenderas) eller **AzureStorage** (se nedan). |Ja |
| connectionString | Ange information som behövs för att ansluta till lagringsutrymmet för connectionString-egenskapen. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller lokal Integration Runtime (om det är ditt datalager i ett privat nätverk). Om den inte anges används standard Azure Integration Runtime. |Nej |

>[!NOTE]
>Om du använde ”AzureStorage” typen länkad tjänst, stöds det fortfarande som – är att medan du är föreslagna länkad tjänsttyp som vi rekommenderar att du använder den här nya ”AzureBlobStorage”.

**Exempel:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="shared-access-signature-authentication"></a>Autentisering med signatur för delad åtkomst

En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt storage-konto. Du kan använda en signatur för delad åtkomst för att ge en klient begränsad behörighet till objekt i ditt storage-konto under en viss tid. Du behöver inte dela åtkomstnycklarna för kontot. Signatur för delad åtkomst är en URI som omfattar all information som behövs för autentiserad åtkomst till en lagringsresurs i dess Frågeparametrar. Om du vill få åtkomst till lagringsresurser med signatur för delad åtkomst, behöver klienten bara använda signatur för delad åtkomst till lämplig konstruktor nebo metodu. Mer information om signaturer för delad åtkomst finns i [signaturer för delad åtkomst: Förstå modellen för signatur för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Data Factory stöder nu både **service signaturer för delad åtkomst** och **konto signaturer för delad åtkomst**. Mer information om dessa två typer och hur du skapar dem finns i [typer av signaturer för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures).
>- I senare datauppsättning konfiguration är sökvägen till mappen den absoluta sökvägen från behållarenivån. Du måste konfigurera en linje med sökvägen i SAS-URI.

> [!TIP]
> Du kan köra följande PowerShell-kommandon för att generera en signatur för delad åtkomst av tjänsten för ditt lagringskonto. Ersätt platshållarna och bevilja behörigheten som krävs.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Om du vill använda autentisering med signatur för delad åtkomst, stöds följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till **AzureBlobStorage** (rekommenderas) eller **AzureStorage** (se nedan). |Ja |
| sasUri | Ange signaturen för delad åtkomst URI till lagringsresurser, t.ex blob, behållaren eller tabellen. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller den lokala Integration Runtime (om ditt datalager finns i ett privat nätverk). Om den inte anges används standard Azure Integration Runtime. |Nej |

>[!NOTE]
>Om du använde ”AzureStorage” typen länkad tjänst, stöds det fortfarande som – är att medan du är föreslagna länkad tjänsttyp som vi rekommenderar att du använder den här nya ”AzureBlobStorage”.

**Exempel:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

När du skapar en signatur för delad åtkomst URI kan du överväga följande punkter:

- Ange lämpliga Läs/Skriv-behörigheter för objekt baserat på hur den länkade tjänsten (läsa, skriva, Läs/Skriv) används i din datafabrik.
- Ange **förfallotiden** på rätt sätt. Se till att åtkomsten till lagringsobjekt inte går ut inom den aktiva perioden för pipelinen.
- URI: N ska skapas på rätt behållare/blob eller table nivå utifrån behov. En signatur för delad åtkomst URI till en blob kan Data Factory för att få åtkomst till den specifika blobben. En signatur för delad åtkomst URI: N till ett Blob storage-behållare kan Data Factory att gå igenom blobar i behållaren. Ge åtkomst till fler eller färre objekt senare, eller om du vill uppdatera signatur för delad åtkomst URI, Kom ihåg att uppdatera den länkade tjänsten med den nya URI.

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Autentisering av Azure Storage tjänstens huvudnamn i allmänhet finns i [autentisera åtkomsten till Azure Storage med Azure Active Directory](../storage/common/storage-auth-aad.md).

Följ dessa steg om du vill använda autentisering av tjänstens huvudnamn:

1. Registrera en entitet för program i Azure Active Directory (Azure AD) genom att följa [registrera ditt program med en Azure AD-klient](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

2. Ge tjänstens huvudnamn rätt behörighet i Azure Blob storage. Referera till [hantera åtkomsträttigheter till Azure Storage-data med RBAC](../storage/common/storage-auth-aad-rbac.md) med mer information om rollerna.

    - **Som källa**, i Access (IAM) genom att ge minst **Storage Blob Data-läsare** roll.
    - **Som mottagare**, i Access (IAM) genom att ge minst **Storage Blob Data-deltagare** roll.

De här egenskaperna har stöd för en Azure Blob storage-länkad tjänst:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till **AzureBlobStorage**. |Ja |
| serviceEndpoint | Ange Azure Blob storage-tjänsteslutpunkt med mönstret för `https://<accountName>.blob.core.windows.net/`. |Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en **SecureString** ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| klient | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Hämta det håller musen i det övre högra hörnet i Azure Portal. | Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller lokal Integration Runtime (om det är ditt datalager i ett privat nätverk). Om den inte anges används standard Azure Integration Runtime. |Nej |

>[!NOTE]
>Länkad tjänst för service principal authentication stöds endast av typen ”AzureBlobStorage” men inte föregående ”AzureStorage” skriva länkad tjänst.

**Exempel:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
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

### <a name="managed-identity"></a> Hanterade identiteter för autentisering av Azure-resurser

En data factory kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md), som representerar den här specifika data factory. Du kan använda den här tjänstidentitet direkt för Blob storage-autentisering som är ungefär som att använda tjänstens huvudnamn. Det gör att den här avsedda factory ska kunna komma åt och kopiera data från/till ditt Blob storage.

Azure Storage MSI-autentisering i allmänhet finns i [autentisera åtkomsten till Azure Storage med Azure Active Directory](../storage/common/storage-auth-aad.md).

Följ dessa steg om du vill använda hanterade identiteter för Azure-resurser autentisering:

1. [Hämta tjänstidentitet för datafabrik](data-factory-service-identity.md#retrieve-service-identity) genom att kopiera värdet för ”SERVICE IDENTITETSPROGRAM-ID” genererade tillsammans med din datafabrik.

2. Ge rätt behörighet hanterad identitet i Azure Blob storage. Referera till [hantera åtkomsträttigheter till Azure Storage-data med RBAC](../storage/common/storage-auth-aad-rbac.md) med mer information om rollerna.

    - **Som källa**, i Access (IAM) genom att ge minst **Storage Blob Data-läsare** roll.
    - **Som mottagare**, i Access (IAM) genom att ge minst **Storage Blob Data-deltagare** roll.

De här egenskaperna har stöd för en Azure Blob storage-länkad tjänst:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till **AzureBlobStorage**. |Ja |
| serviceEndpoint | Ange Azure Blob storage-tjänsteslutpunkt med mönstret för `https://<accountName>.blob.core.windows.net/`. |Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller lokal Integration Runtime (om det är ditt datalager i ett privat nätverk). Om den inte anges används standard Azure Integration Runtime. |Nej |

> [!NOTE]
> Hanterade identiteter för Azure-resurser autentisering stöds endast av ”AzureBlobStorage” Skriv länkade tjänsten men inte föregående ”AzureStorage” skriva länkad tjänst. 

**Exempel:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Blob storage-datauppsättningen.

Om du vill kopiera data till och från Blob storage, ange typegenskapen på datauppsättningen till **AzureBlob**. Följande egenskaper stöds.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till **AzureBlob**. |Ja |
| folderPath | Sökvägen till behållaren och mappen i blob storage. Jokerteckenfilter stöds inte. Ett exempel är myblobcontainer/myblobfolder /. |Ja för kopiera/Lookup-aktivitet, inte för GetMetadata-aktiviteten |
| fileName | **Namn eller jokertecken-filtret** för BLOB(ar) under den angivna ”folderPath”. Om du inte anger ett värde för den här egenskapen datauppsättningen pekar på alla blobbar i mappen. <br/><br/>För filter tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken).<br/>– Exempel 1: `"fileName": "*.csv"`<br/>– Exempel 2: `"fileName": "???20180427.txt"`<br/>Använd `^` att undvika om din faktiska filnamnet har jokertecken eller den här escape-tecken i.<br/><br/>Om filnamnet har inte angetts för en utdatauppsättning och **preserveHierarchy** inte har angetts i aktiviteten-mottagare kopieringsaktiviteten genererar automatiskt blobnamnet med följande mönster ”:*Data. [ aktivitetskörning id GUID]. [GUID om FlattenHierarchy]. [format om konfigurerat]. [komprimering om konfigurerat]* ”, t.ex. ”Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz”; Om du kopierar från tabular datakälla med hjälp av tabellnamn i stället för att fråga namnet har formatet är ”*[tabellnamn]. [ format]. [komprimering om konfigurerat]* ”, t.ex. ”MyTable.csv”. |Nej |
| modifiedDatetimeStart | Filter för filer baserat på attributet: senast ändrad. Filerna markerade om deras tid för senaste ändring är inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br/><br/> Egenskaperna kan vara NULL vilket innebär att inga filfilter för attributet som ska användas för datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senaste ändrade attribut är större än eller lika med datum/tid-värde väljs.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filer vars senaste ändrade attributet är mindre än det markerade datetime-värde.| Nej |
| modifiedDatetimeEnd | Filter för filer baserat på attributet: senast ändrad. Filerna markerade om deras tid för senaste ändring är inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br/><br/> Egenskaperna kan vara NULL vilket innebär att inga filfilter för attributet som ska användas för datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senaste ändrade attribut är större än eller lika med datum/tid-värde väljs.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filer vars senaste ändrade attributet är mindre än det markerade datetime-värde.| Nej |
| Format | Om du vill kopiera filer som finns mellan filbaserade lager (binär kopia), kan du hoppa över avsnittet format i både inkommande och utgående datamängd definitionerna.<br/><br/>Om du vill parsa eller generera filer med ett visst format format för följande filtyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, och **ParquetFormat**. Ange den **typ** egenskapen under **format** till någon av dessa värden. Mer information finns i den [textformat](supported-file-formats-and-compression-codecs.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [Orc-format](supported-file-formats-and-compression-codecs.md#orc-format), och [Parquet-format ](supported-file-formats-and-compression-codecs.md#parquet-format) avsnitt. |Nej (endast för binär kopia scenario) |
| Komprimering | Ange typ och komprimeringsnivå för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Stöds nivåer **Optimal** och **snabbast**. |Nej |

>[!TIP]
>Kopiera alla blobar under en mapp genom att ange **folderPath** endast.<br>Om du vill kopiera en enda blob med ett givet namn, ange **folderPath** med mappdelen och **fileName** med filnamnet.<br>Om du vill kopiera en delmängd av blobar under en mapp, anger **folderPath** med mappdelen och **fileName** med jokertecken-filtret. 

**Exempel:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Blob storage källa och mottagare.

### <a name="blob-storage-as-a-source-type"></a>BLOB storage som en typ av datakälla

För att kopiera data från Blob storage, ange typ av datakälla i kopieringsaktiviteten till **BlobSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till **BlobSource**. |Ja |
| rekursiv | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. Observera att när rekursiv har angetts till true och mottagaren är en filbaserad store, en tom mapp eller undermapp inte kopieras eller skapat i mottagaren.<br/>Tillåtna värden är **SANT** (standard) och **FALSKT**. | Nej |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="blob-storage-as-a-sink-type"></a>BLOB storage som en Mottagartyp

För att kopiera data till Blob storage, ange Mottagartyp i kopieringsaktiviteten till **BlobSink**. Följande egenskaper stöds i den **mottagare** avsnittet.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type kopiera aktivitet komprimeringstyp måste anges till **BlobSink**. |Ja |
| copyBehavior | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: bevarar filen hierarkin i målmappen. Den relativa sökvägen för källfilen för källmappen är identisk med den relativa sökvägen för målfilen till målmappen.<br/><b>-FlattenHierarchy</b>: alla filer från källmappen finns i den första nivån i målmappen. Målfiler har automatiskt genererade unika namn. <br/><b>-MergeFiles</b>: slår samman alla filer från källmappen till en fil. Om namnet på filen / bloben har angetts, är sammanfogade filnamnet det angivna namnet. Annars är det en automatiskt skapade filnamnet. | Nej |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="some-recursive-and-copybehavior-examples"></a>Några exempel rekursiv och copyBehavior

Det här avsnittet beskrivs kopieringsåtgärden för olika kombinationer av värden för rekursiv och copyBehavior resulterande beteende.

| rekursiv | copyBehavior | Mappstruktur för källa | Resulterande mål |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | målmappen Mapp1 skapas med samma struktur som källa:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt skapade namn på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File5 |
| true |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 + fil3 + File4 + File5 innehållet slås samman i en fil med ett automatiskt genererade namnet. |
| false |preserveHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | målmappen Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/><br/>Subfolder1 med fil3, File4 och File5 hämtas inte. |
| false |flattenHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | målmappen Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt skapade namn på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/><br/>Subfolder1 med fil3, File4 och File5 hämtas inte. |
| false |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målmappen Mapp1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 innehållet slås samman i en fil med ett automatiskt genererade namnet. automatiskt skapade namn på File1<br/><br/>Subfolder1 med fil3, File4 och File5 hämtas inte. |

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
