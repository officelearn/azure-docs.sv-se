---
title: Kopiera data till och från Azure Blob storage med hjälp av Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från datalager som stöds till Azure Blob storage eller från Blob storage till mottagarens datalager, med hjälp av Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 1ff20322f1d4f6024d4f41037ca18c327a0cc21f
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233198"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Kopiera data till och från Azure Blob storage med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-azure-blob-connector.md)
> * [Aktuell version](connector-azure-blob-storage.md)

Den här artikeln beskrivs hur du kopierar data till och från Azure Blob storage. Läs om Azure Data Factory den [introduktionsartikeln](introduction.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure-Blob-anslutningsapp stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [källa/mottagare matris som stöds](copy-activity-overview.md)
- [Mappning av dataflöde](concepts-data-flow-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktiviteten](control-flow-get-metadata-activity.md)

Mer specifikt stöder denna Blob storage-anslutning:

- Kopiera BLOB-objekt till och från Allmänt Azure storage-konton och frekvent/lågfrekvent – blob storage. 
- Kopiera blobar med hjälp av kontonyckel, signatur för delad åtkomst för tjänsten, tjänstens huvudnamn eller hanterade identiteter för Azure-resurser autentiseringar.
- Kopiera blobar från block, lägga till eller page blobs och kopiera data till endast blockblob-objekt.
- Kopiera BLOB-objekt som är eller parsning eller genererar-objekt med [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>Om du aktiverar den _”Tillåt att betrodda Microsoft-tjänster för att komma åt det här lagringskontot”_ alternativet på brandväggsinställningar för Azure Storage med hjälp av Azure Integration Runtime kan ansluta till Blob-lagring att misslyckas med ett förbjudet fel eftersom ADF inte behandlas som en betrodd Microsoft-tjänst. Anslut via en lokal Integration Runtime i stället.

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
| type | Type-egenskapen måste anges till **AzureBlobStorage** (rekommenderas) eller **AzureStorage** (se nedan). |Ja |
| connectionString | Ange information som behövs för att ansluta till lagringsutrymmet för connectionString-egenskapen. <br/>Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory. Du kan också publicera kontonyckeln i Azure Key Vault och använda pull i `accountKey` konfiguration av anslutningssträngen. Följande exempel finns och [Store autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. |Ja |
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

**Exempel: lagra kontonyckeln i Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt storage-konto. Du kan använda en signatur för delad åtkomst för att ge en klient begränsad behörighet till objekt i ditt storage-konto under en viss tid. Du behöver inte dela åtkomstnycklarna för kontot. Signatur för delad åtkomst är en URI som omfattar all information som behövs för autentiserad åtkomst till en lagringsresurs i dess Frågeparametrar. Om du vill få åtkomst till lagringsresurser med signatur för delad åtkomst, behöver klienten bara använda signatur för delad åtkomst till lämplig konstruktor nebo metodu. Mer information om signaturer för delad åtkomst finns i [signaturer för delad åtkomst: Förstå modellen](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Data Factory stöder nu både **service signaturer för delad åtkomst** och **konto signaturer för delad åtkomst**. Mer information om dessa två typer och hur du skapar dem finns i [typer av signaturer för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures).
>- I senare datauppsättning konfiguration är sökvägen till mappen den absoluta sökvägen från behållarenivån. Du måste konfigurera en linje med sökvägen i SAS-URI.

> [!TIP]
> Du kan köra följande PowerShell-kommandon för att generera en signatur för delad åtkomst av tjänsten för ditt lagringskonto. Ersätt platshållarna och bevilja behörigheten som krävs.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Om du vill använda autentisering med signatur för delad åtkomst, stöds följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till **AzureBlobStorage** (rekommenderas) eller **AzureStorage** (se nedan). |Ja |
| sasUri | Ange signaturen för delad åtkomst URI till lagringsresurser, t.ex blob-behållare. <br/>Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory. Du kan också placera SAS-token i Azure Key Vault för att dra nytta av automatisk rotation och ta bort token delen. Följande exempel finns och [Store autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. |Ja |
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
                "value": "<SAS URI of the Azure Storage resource e.g. https://<container>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: lagra kontonyckeln i Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<container>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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
- URI: N ska skapas i rätt behållare/blob baserat på behov. En signatur för delad åtkomst URI till en blob kan Data Factory för att få åtkomst till den specifika blobben. En signatur för delad åtkomst URI: N till ett Blob storage-behållare kan Data Factory att gå igenom blobar i behållaren. Ge åtkomst till fler eller färre objekt senare, eller om du vill uppdatera signatur för delad åtkomst URI, Kom ihåg att uppdatera den länkade tjänsten med den nya URI.

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
| type | Type-egenskapen måste anges till **AzureBlobStorage**. |Ja |
| serviceEndpoint | Ange Azure Blob storage-tjänsteslutpunkt med mönstret för `https://<accountName>.blob.core.windows.net/`. |Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en **SecureString** ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Hämta det håller musen i det övre högra hörnet i Azure Portal. | Ja |
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

En data factory kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md), som representerar den här specifika data factory. Du kan använda den här hanterade identiteten direkt för Blob storage-autentisering som är ungefär som att använda tjänstens huvudnamn. Det gör att den här avsedda factory ska kunna komma åt och kopiera data från/till ditt Blob storage.

Referera till [autentisera åtkomsten till Azure Storage med Azure Active Directory](../storage/common/storage-auth-aad.md) för Azure Storage-autentisering i allmänhet. Följ dessa steg om du vill använda hanterade identiteter för Azure-resurser autentisering:

1. [Hämta data factory hanteras identitetsinformation](data-factory-service-identity.md#retrieve-managed-identity) genom att kopiera värdet för ”SERVICE IDENTITETSPROGRAM-ID” genererade tillsammans med din datafabrik.

2. Ge rätt behörighet hanterad identitet i Azure Blob storage. Referera till [hantera åtkomsträttigheter till Azure Storage-data med RBAC](../storage/common/storage-auth-aad-rbac.md) med mer information om rollerna.

    - **Som källa**, i Access (IAM) genom att ge minst **Storage Blob Data-läsare** roll.
    - **Som mottagare**, i Access (IAM) genom att ge minst **Storage Blob Data-deltagare** roll.

De här egenskaperna har stöd för en Azure Blob storage-länkad tjänst:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till **AzureBlobStorage**. |Ja |
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. 

- För **Parquet och avgränsat textformat**, referera till [Parquet och avgränsad text formatera datauppsättning](#parquet-and-delimited-text-format-dataset) avsnittet.
- För andra format som **ORC/Avro/JSON/binära formatet**, referera till [andra format datauppsättning](#other-format-dataset) avsnittet.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet och avgränsad text format datauppsättning

För att kopiera data till och från Blob storage i Parquet eller avgränsat textformat, referera till [Parquet-format](format-parquet.md) och [avgränsat textformat](format-delimited-text.md) format-baserade datauppsättning och inställningar som stöds. Följande egenskaper har stöd för Azure Blob under `location` inställningar i formatet-baserade datauppsättning:

| Egenskap    | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Type-egenskapen för platsen i datauppsättningen måste anges till **AzureBlobStorageLocation**. | Ja      |
| container  | Blob-behållaren.                                          | Ja      |
| folderPath | Sökvägen till mappen under den angivna behållaren. Om du vill använda jokertecken för att filtrera mappar hoppa över den här inställningen och ange i källinställningar för aktiviteten. | Nej       |
| fileName   | Filnamnet under den angivna behållaren + folderPath. Om du vill använda jokertecken för att filtrera filerna hoppa över den här inställningen och ange i källinställningar för aktiviteten. | Nej       |

> [!NOTE]
>
> **AzureBlob** typ datauppsättning med Parquet-/ textformat som nämns i nästa avsnitt stöds fortfarande som – är för kopiera/Lookup/GetMetadata-aktiviteten för bakåtkompatibilitet, men den inte fungerar med mappning av dataflöde. Du rekommenderas för att använda den nya modellen framöver och ADF redigering Användargränssnittet har ändrats till att generera dessa nya typer.

**Exempel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
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

### <a name="other-format-dataset"></a>Andra format-datauppsättning

För att kopiera data till och från Blob storage i ORC/Avro/JSON/binära formatet, ange typegenskapen på datauppsättningen till **AzureBlob**. Följande egenskaper stöds.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för datauppsättningen måste anges till **AzureBlob**. |Ja |
| folderPath | Sökvägen till behållaren och mappen i blob storage. <br/><br/>Jokerteckenfilter stöds för sökvägen exklusive behållarnamn. Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken); Använd `^` att undvika om din faktiska mappnamn har jokertecken eller den här escape-tecken i. <br/><br/>Exempel: myblobcontainer/myblobfolder/se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). |Ja för kopiera/Lookup-aktivitet, inte för GetMetadata-aktiviteten |
| fileName | **Namn eller jokertecken-filtret** för BLOB(ar) under den angivna ”folderPath”. Om du inte anger ett värde för den här egenskapen datauppsättningen pekar på alla blobbar i mappen. <br/><br/>För filter tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken).<br/>– Exempel 1: `"fileName": "*.csv"`<br/>– Exempel 2: `"fileName": "???20180427.txt"`<br/>Använd `^` att undvika om din faktiska filnamnet har jokertecken eller den här escape-tecken i.<br/><br/>Om filnamnet har inte angetts för en utdatauppsättning och **preserveHierarchy** inte har angetts i aktiviteten-mottagare kopieringsaktiviteten genererar automatiskt blobnamnet med följande mönster: ”*Data. [aktivitet köras ID GUID]. [GUID om FlattenHierarchy]. [format om konfigurerat]. [komprimering om konfigurerat]* ”, t.ex. ”Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz”; Om du kopierar från tabular datakälla med hjälp av tabellnamn i stället för att fråga namnet har formatet är ”*[tabellnamn]. [ format]. [komprimering om konfigurerat]* ”, t.ex. ”MyTable.csv”. |Nej |
| modifiedDatetimeStart | Filter för filer baserat på attributet: Senast ändrades. Filerna markerade om deras tid för senaste ändring är inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br/><br/> Tänk på dataförflyttning serverns prestanda påverkas genom att aktivera den här inställningen när du vill filen filter från stora mängder filer. <br/><br/> Egenskaperna kan vara NULL som innebär att inga filfilter för attributet som ska användas för datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senaste ändrade attribut är större än eller lika med datum/tid-värde väljs.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filer vars senaste ändrade attributet är mindre än det markerade datetime-värde.| Nej |
| modifiedDatetimeEnd | Filter för filer baserat på attributet: Senast ändrades. Filerna markerade om deras tid för senaste ändring är inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br/><br/> Tänk på dataförflyttning serverns prestanda påverkas genom att aktivera den här inställningen när du vill filen filter från stora mängder filer. <br/><br/> Egenskaperna kan vara NULL som innebär att inga filfilter för attributet som ska användas för datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senaste ändrade attribut är större än eller lika med datum/tid-värde väljs.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filer vars senaste ändrade attributet är mindre än det markerade datetime-värde.| Nej |
| format | Om du vill kopiera filer som finns mellan filbaserade lager (binär kopia), kan du hoppa över avsnittet format i både inkommande och utgående datamängd definitionerna.<br/><br/>Om du vill parsa eller generera filer med ett visst format stöds format för följande filtyper: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, och **ParquetFormat**. Ange den **typ** egenskapen under **format** till någon av dessa värden. Mer information finns i den [textformat](supported-file-formats-and-compression-codecs.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [Orc-format](supported-file-formats-and-compression-codecs.md#orc-format), och [Parquet-format ](supported-file-formats-and-compression-codecs.md#parquet-format) avsnitt. |Nej (endast för binär kopia scenario) |
| compression | Ange typ och komprimeringsnivå för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Stöds nivåer **Optimal** och **snabbast**. |Nej |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Blob storage källa och mottagare.

### <a name="blob-storage-as-a-source-type"></a>BLOB storage som en typ av datakälla

- För kopia från **Parquet och avgränsat textformat**, referera till [Parquet och avgränsad text format källa](#parquet-and-delimited-text-format-source) avsnittet.
- För kopia från andra format som **ORC/Avro/JSON/binära formatet**, referera till [annan format källa](#other-format-source) avsnittet.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet och avgränsad text format källa

För att kopiera data från Blob storage i Parquet eller avgränsat textformat avser [Parquet-format](format-parquet.md) och [avgränsat textformat](format-delimited-text.md) format-baserade aktiviteten kopieringskälla och inställningar som stöds. Följande egenskaper har stöd för Azure Blob under `storeSettings` inställningar i formatet-baserade kopieringskälla:

| Egenskap                  | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Egenskapen type under `storeSettings` måste anges till **AzureBlobStorageReadSetting**. | Ja                                           |
| recursive                | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. Observera att när rekursiv har angetts till true och mottagaren är en filbaserad store, en tom mapp eller undermapp inte kopieras eller skapat i mottagaren. Tillåtna värden är **SANT** (standard) och **FALSKT**. | Nej                                            |
| wildcardFolderPath       | Sökvägen till mappen med jokertecken under den angivna behållaren som konfigurerats i datauppsättningen för att filtrera källa mappar. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken); Använd `^` att undvika om din faktiska mappnamn har jokertecken eller den här escape-tecken i. <br>Se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). | Nej                                            |
| wildcardFileName         | Filnamn med jokertecken under den angivna behållaren + folderPath/wildcardFolderPath till källfilerna för filter. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken); Använd `^` att undvika om din faktiska mappnamn har jokertecken eller den här escape-tecken i.  Se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). | Ja om `fileName` har inte angetts i datauppsättningen |
| modifiedDatetimeStart    | Filter för filer baserat på attributet: Senast ändrades. Filerna markerade om deras tid för senaste ändring är inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br> Egenskaperna kan vara NULL vilket innebär att inga filfilter för attributet som ska användas för datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senaste ändrade attribut är större än eller lika med datum/tid-värde väljs.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filer vars senaste ändrade attributet är mindre än det markerade datetime-värde. | Nej                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Nej                                            |
| maxConcurrentConnections | Antal anslutningar för att ansluta till storage store samtidigt. Ange bara när du vill begränsa samtidiga anslutningen till datalagret. | Nej                                            |

> [!NOTE]
> För Parquet/avgränsat textformat **BlobSource** typen kopiera aktivitetskälla som nämns i nästa avsnitt stöds fortfarande som – är för bakåtkompatibilitet. Du rekommenderas för att använda den nya modellen framöver och ADF redigering Användargränssnittet har ändrats till att generera dessa nya typer.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageReadSetting",
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

För att kopiera data från Blob storage i ORC/Avro/JSON/binärt format, ange typ av datakälla i kopieringsaktiviteten till **BlobSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för aktiviteten kopieringskälla måste anges till **BlobSource**. |Ja |
| recursive | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. Observera att när rekursiv har angetts till true och mottagaren är en filbaserad store, en tom mapp eller undermapp inte kopieras eller skapat i mottagaren.<br/>Tillåtna värden är **SANT** (standard) och **FALSKT**. | Nej |
| maxConcurrentConnections | Antal anslutningar för att ansluta till storage store samtidigt. Ange bara när du vill begränsa samtidiga anslutningen till datalagret. | Nej |

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

- För att kopiera till **Parquet och avgränsat textformat**, referera till [Parquet och avgränsad text format mottagare](#parquet-and-delimited-text-format-sink) avsnittet.
- För att kopiera till andra format som **ORC/Avro/JSON/binära formatet**, referera till [andra format mottagare](#other-format-sink) avsnittet.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet och avgränsad text format mottagare

För att kopiera data till Blob storage i Parquet eller avgränsat textformat avser [Parquet-format](format-parquet.md) och [avgränsat textformat](format-delimited-text.md) format-baserade kopiera aktivitet mottagare och inställningar som stöds. Följande egenskaper har stöd för Azure Blob under `storeSettings` inställningar i formatet-baserad kopia mottagare:

| Egenskap                  | Beskrivning                                                  | Krävs |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Egenskapen type under `storeSettings` måste anges till **AzureBlobStorageWriteSetting**. | Ja      |
| copyBehavior             | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: Bevarar filen hierarkin i målmappen. Den relativa sökvägen för källfilen för källmappen är identisk med den relativa sökvägen för målfilen till målmappen.<br/><b>-FlattenHierarchy</b>: Alla filer från källmappen finns i den första nivån i målmappen. Målfiler har automatiskt genererade unika namn. <br/><b>-MergeFiles</b>: Slår samman alla filer från källmappen till en fil. Om namnet på filen / bloben har angetts, är sammanfogade filnamnet det angivna namnet. Annars är det en automatiskt skapade filnamnet. | Nej       |
| maxConcurrentConnections | Antal anslutningar för att ansluta till storage store samtidigt. Ange bara när du vill begränsa samtidiga anslutningen till datalagret. | Nej       |

> [!NOTE]
> För Parquet/avgränsat textformat **BlobSink** typen kopiera aktivitet mottagare som nämns i nästa avsnitt stöds fortfarande som – är för bakåtkompatibilitet. Du rekommenderas för att använda den nya modellen framöver och ADF redigering Användargränssnittet har ändrats till att generera dessa nya typer.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Andra format-mottagare

För att kopiera data till Blob storage, ange Mottagartyp i kopieringsaktiviteten till **BlobSink**. Följande egenskaper stöds i den **mottagare** avsnittet.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen type kopiera aktivitet komprimeringstyp måste anges till **BlobSink**. |Ja |
| copyBehavior | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: Bevarar filen hierarkin i målmappen. Den relativa sökvägen för källfilen för källmappen är identisk med den relativa sökvägen för målfilen till målmappen.<br/><b>-FlattenHierarchy</b>: Alla filer från källmappen finns i den första nivån i målmappen. Målfiler har automatiskt genererade unika namn. <br/><b>-MergeFiles</b>: Slår samman alla filer från källmappen till en fil. Om namnet på filen / bloben har angetts, är sammanfogade filnamnet det angivna namnet. Annars är det en automatiskt skapade filnamnet. | Nej |
| maxConcurrentConnections | Antal anslutningar för att ansluta till storage store samtidigt. Ange bara när du vill begränsa samtidiga anslutningen till datalagret. | Nej |

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

### <a name="folder-and-file-filter-examples"></a>Mapp och fil filter-exempel

Det här avsnittet beskriver resultatet av sökvägen och namnet på mappen med jokertecken filter.

| folderPath | fileName | recursive | Käll-mappen struktur och filtrera resultatet (filer i **fetstil** hämtas)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (tom, Använd standard) | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (tom, Använd standard) | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Några exempel rekursiv och copyBehavior

Det här avsnittet beskrivs kopieringsåtgärden för olika kombinationer av värden för rekursiv och copyBehavior resulterande beteende.

| recursive | copyBehavior | Mappstruktur för källa | Resulterande mål |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | målmappen Mapp1 skapas med samma struktur som källa:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt skapade namn på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File5 |
| true |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 + fil3 + File4 + File5 innehållet slås samman i en fil med ett automatiskt genererade namnet. |
| false |preserveHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | målmappen Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/><br/>Subfolder1 med fil3, File4 och File5 hämtas inte. |
| false |flattenHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | målmappen Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt skapade namn på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/><br/>Subfolder1 med fil3, File4 och File5 hämtas inte. |
| false |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målmappen Mapp1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 innehållet slås samman i en fil med ett automatiskt genererade namnet. automatiskt skapade namn på File1<br/><br/>Subfolder1 med fil3, File4 och File5 hämtas inte. |

## <a name="mapping-data-flow-properties"></a>Egenskaper för mappning av dataflöde

Få mer detaljerad information från [source omvandling](data-flow-source.md) och [mottagare omvandling](data-flow-sink.md) i mappning dataflöde.

## <a name="next-steps"></a>Nästa steg

En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
