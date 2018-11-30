---
title: Kopiera data till och från Azure Data Lake Storage Gen2 förhandsversion med Data Factory (förhandsversion) | Microsoft Docs
description: Lär dig hur du kopierar data till och från Azure Data Lake Storage Gen2 förhandsversion med Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: jingwang
ms.openlocfilehash: ca2591f34a0aba598c12815de684ec6bb8fca929
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620361"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-preview-using-azure-data-factory-preview"></a>Kopiera data till och från Azure Data Lake Storage Gen2 förhandsversion med Azure Data Factory (förhandsversion)

[Azure Data Lake Storage Gen2 förhandsversion](../storage/data-lake-storage/introduction.md) är Microsofts hyperskala storage-tjänst som utformats för stordataanalyser. Det kan du samverka med dina data med paradigm för lagring av system- och båda fil. Detta gör Azure Data Lake Storage tjänsten endast molnbaserad multimodal lagring, så att du kan extrahera analytics värde från alla dina data. Du kan [registrera](https://aka.ms/adlsgen2signup) till den offentliga förhandsversionen av Azure Data Lake Storage Gen2.

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data till och från Data Lake Storage Gen2. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från alla dataarkiv till Data Lake Storage Gen2. Du kan också kopiera data från Data Lake Storage Gen2 till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md) tabell.

Mer specifikt stöder den här anslutningen:

- Kopiera data med nyckel, tjänstens huvudnamn eller hanterade identiteter för Azure-resurser autentiseringar.
- Kopiering av filer som- eller parsning eller generera filer med [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md).

>[!TIP]
>Om du aktiverar hierarkiskt namnområde, finns det för närvarande inga samverkan mellan åtgärder mellan Blob och ADLS Gen2 API: er. Om du stöter på fel i ”ErrorCode = FilesystemNotFound” med detaljerat meddelande som ”det angivna filsystemet finns inte”., beror det på den angivna mottagaren filsystemet har skapats via Blob-API: et i stället för ADLS Gen2 API någon annanstans. Om du vill åtgärda problemet ange ett nytt filsystem med ett namn som inte finns som namnet på en blobbehållare och ADF skapar automatiskt det filsystemet under Datakopieringen.

>[!NOTE]
>Om du aktiverar _”Tillåt att betrodda Microsoft-tjänster för att komma åt det här lagringskontot”_ alternativet på brandväggsinställningar för Azure Storage med hjälp av Azure Integration Runtime kan ansluta till Data Lake Storage Gen2 misslyckas med förbjudet fel som ADF inte behandlas som betrodda Microsoft-tjänst. Använd lokal Integration Runtime som ansluter via i stället.

## <a name="get-started"></a>Kom igång

>[!TIP]
>En genomgång på hur du använder Data Lake Storage Gen2 connector finns i [läser in data i Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Azure Data Lake Storage Gen2 connector stöder följande typer av autentisering, finns i avsnittet motsvarande detaljer:

- [Konto-nyckelautentisering](#account-key-authentication)
- [Autentisering av tjänstens huvudnamn](#service-principal-authentication)
- [Hanterade identiteter för autentisering av Azure-resurser](#managed-identity)

### <a name="account-key-authentication"></a>Konto-nyckelautentisering

Om du vill använda nyckelautentisering för storage-konto, stöds följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till **AzureBlobFS**. |Ja |
| url | Slutpunkt för Data Lake Storage Gen2 med mönstret för `https://<accountname>.dfs.core.windows.net`. | Ja | 
| accountKey | Kontonyckel för Data Lake Storage Gen2-tjänsten. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller lokal Integration Runtime (om det är ditt datalager i ett privat nätverk). Om den inte anges används standard Azure Integration Runtime. |Nej |

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

Följ dessa steg om du vill använda autentisering av tjänstens huvudnamn:

1. Registrera en entitet för program i Azure Active Directory (Azure AD) genom att följa [registrera ditt program med en Azure AD-klient](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

2. Ge tjänstens huvudnamn rätt behörighet i Azure storage.

    - **Som källa**, i Access (IAM) genom att ge minst **Storage Blob Data-läsare** roll.
    - **Som mottagare**, i Access (IAM) genom att ge minst **Storage Blob Data-deltagare** roll.

Dessa egenskaper stöds i den länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till **AzureBlobFS**. |Ja |
| url | Slutpunkt för Data Lake Storage Gen2 med mönstret för `https://<accountname>.dfs.core.windows.net`. | Ja | 
| servicePrincipalId | Ange programmets klient-ID. | Ja |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en **SecureString** ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| klient | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Hämta det håller musen i det övre högra hörnet i Azure Portal. | Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller lokal Integration Runtime (om det är ditt datalager i ett privat nätverk). Om den inte anges används standard Azure Integration Runtime. |Nej |

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

### <a name="managed-identity"></a> Hanterade identiteter för autentisering av Azure-resurser

En data factory kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md), som representerar den här specifika data factory. Du kan använda den här tjänstidentitet direkt för Blob storage-autentisering som är ungefär som att använda tjänstens huvudnamn. Det gör att den här avsedda factory ska kunna komma åt och kopiera data från/till ditt Blob storage.

Följ dessa steg om du vill använda hanterade identiteter för Azure-resurser autentisering:

1. [Hämta tjänstidentitet för datafabrik](data-factory-service-identity.md#retrieve-service-identity) genom att kopiera värdet för ”SERVICE IDENTITETSPROGRAM-ID” genererade tillsammans med din datafabrik.

2. Ge rätt behörighet hanterad identitet i Azure storage. 

    - **Som källa**, i Access (IAM) genom att ge minst **Storage Blob Data-läsare** roll.
    - **Som mottagare**, i Access (IAM) genom att ge minst **Storage Blob Data-deltagare** roll.

Dessa egenskaper stöds i den länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till **AzureBlobFS**. |Ja |
| url | Slutpunkt för Data Lake Storage Gen2 med mönstret för `https://<accountname>.dfs.core.windows.net`. | Ja | 
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller lokal Integration Runtime (om det är ditt datalager i ett privat nätverk). Om den inte anges används standard Azure Integration Runtime. |Nej |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Följande egenskaper har stöd för Azure Data Lake Storage datauppsättning:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till **AzureBlobFSFile**. |Ja |
| folderPath | Sökvägen till mappen i Data Lake Storage Gen2. Jokerteckenfilter stöds inte. Om den inte anges som den pekar till roten. Exempel: rootfolder/undermappen /. |Nej |
| fileName | **Namn eller jokertecken-filtret** för den eller filerna under den angivna ”folderPath”. Om du inte anger ett värde för den här egenskapen datauppsättningen pekar på alla filer i mappen. <br/><br/>För filter tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken).<br/>– Exempel 1: `"fileName": "*.csv"`<br/>– Exempel 2: `"fileName": "???20180427.txt"`<br/>Använd `^` att undvika om din faktiska filnamnet har jokertecken eller den här escape-tecken i.<br/><br/>Om filnamnet har inte angetts för en utdatauppsättning och **preserveHierarchy** inte har angetts i aktiviteten-mottagare kopieringsaktiviteten genererar automatiskt filnamnet med följande mönster ”:*Data. [ aktivitetskörning id GUID]. [GUID om FlattenHierarchy]. [format om konfigurerat]. [komprimering om konfigurerat]* ". Ett exempel är ”Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz”. |Nej |
| Format | Om du vill kopiera filer som finns mellan filbaserade lager (binär kopia), kan du hoppa över avsnittet format i både inkommande och utgående datamängd definitionerna.<br/><br/>Om du vill parsa eller generera filer med ett visst format format för följande filtyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, och **ParquetFormat**. Ange den **typ** egenskapen under **format** till någon av dessa värden. Mer information finns i den [textformat](supported-file-formats-and-compression-codecs.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [Orc-format](supported-file-formats-and-compression-codecs.md#orc-format), och [Parquet-format ](supported-file-formats-and-compression-codecs.md#parquet-format) avsnitt. |Nej (endast för binär kopia scenario) |
| Komprimering | Ange typ och komprimeringsnivå för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Stöds nivåer **Optimal** och **snabbast**. |Nej |

>[!TIP]
>Kopiera alla filer i en mapp genom att ange **folderPath** endast.<br>Om du vill kopiera en enstaka fil med ett givet namn, ange **folderPath** med mappdelen och **fileName** med filnamnet.<br>Om du vill kopiera en delmängd av filerna under en mapp, anger **folderPath** med mappdelen och **fileName** med jokertecken-filtret. 

**Exempel:**

```json
{
    "name": "AzureDataLakeStorageDataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [kopiera aktivitetskonfigurationer](copy-activity-overview.md#configuration) och [pipelines och aktiviteter](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Data Lake Storage Gen2 källa och mottagare.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake storage Gen2 som en typ av datakälla

Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till **AzureBlobFSSource**. |Ja |
| rekursiv | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. Observera att när rekursiv har angetts till true och mottagaren är en filbaserad store, en tom mapp eller undermapp inte kopieras eller skapat i mottagaren.<br/>Tillåtna värden är **SANT** (standard) och **FALSKT**. | Nej |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataLakeStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Data Lake Storage input dataset name>",
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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 som en Mottagartyp

Följande egenskaper stöds i kopieringsaktiviteten **mottagare** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type kopiera aktivitet komprimeringstyp måste anges till **AzureBlobFSSink**. |Ja |
| copyBehavior | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: bevarar filen hierarkin i målmappen. Den relativa sökvägen för källfilen för källmappen är identisk med den relativa sökvägen för målfilen till målmappen.<br/><b>-FlattenHierarchy</b>: alla filer från källmappen finns i den första nivån i målmappen. Målfiler har automatiskt genererade unika namn. <br/><b>-MergeFiles</b>: slår samman alla filer från källmappen till en fil. Om filnamnet har angetts är namnet på sammanfogade filen det angivna namnet. Annars är det en automatiskt skapade filnamnet. | Nej |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToAzureDataLakeStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen2 output dataset name>",
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
