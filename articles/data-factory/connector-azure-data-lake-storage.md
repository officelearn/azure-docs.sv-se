---
title: Kopiera data till och från Azure Data Lake Storage Gen2 med Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data till och från Azure Data Lake Storage Gen2 med hjälp av Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: jingwang
ms.openlocfilehash: 6425fdfe89ca2f4c47aaf0e5ffd1dac7767b5020
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057933"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Kopiera data till och från Azure Data Lake Storage Gen2 med Azure Data Factory

Azure Data Lake Storage Gen2 är en uppsättning funktioner för analys av stordata är inbyggda i [Azure Blob storage](../storage/blobs/storage-blobs-introduction.md). Du kan använda den för att samverka med dina data med hjälp av paradigm för lagring av system- och båda fil.

Den här artikeln beskrivs hur du kopierar data till och från Azure Data Lake Storage Gen2. Läs om Azure Data Factory den [introduktionsartikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här anslutningen för Azure Data Lake Storage Gen2 stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [källan eller mottagaren matris som stöds](copy-activity-overview.md)
- [Mappning av dataflöde](concepts-data-flow-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktiviteten](control-flow-get-metadata-activity.md)

Med den här anslutningen kan du mer specifikt:

- Kopiera data med hjälp av nyckeln, tjänstens huvudnamn eller hanterade identiteter för Azure-resurser autentiseringar.
- Kopiera filer som är eller parsa eller generera filer med [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md).

>[!TIP]
>Om du aktiverar hierarkiskt namnområde, finns det för närvarande inga samverkan mellan åtgärder mellan Blob och Data Lake Storage Gen2 API: er. Om du stöter på felet ”ErrorCode = FilesystemNotFound” med meddelandet ”det angivna filsystemet inte finns”, beror det på filsystemet för angivna mottagare som har skapats via Blob-API: et i stället för Data Lake Storage Gen2 API någon annanstans. Åtgärda problemet genom att ange ett nytt filsystem med ett namn som inte finns som namnet på en blobbehållare. Data Factory skapar sedan automatiskt det filsystemet under Datakopieringen.

>[!NOTE]
>Om du aktiverar den **Tillåt att betrodda Microsoft-tjänster för att komma åt det här lagringskontot** alternativet i inställningarna för brandväggen för Azure Storage, Azure integration runtime inte går att ansluta till Data Lake Storage Gen2 och visar ett förbjudet fel. Ett felmeddelande visas eftersom Data Factory inte är behandlas som en betrodd Microsoft-tjänst. Använd lokal integration runtime för att ansluta i stället.

## <a name="get-started"></a>Kom igång

>[!TIP]
>En genomgång av hur du använder Data Lake Storage Gen2-anslutningen finns i [läser in data i Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Azure Data Lake Storage Gen2 anslutningsappen stöder följande autentiseringstyper av. Finns i motsvarande avsnitt för mer information:

- [Konto-nyckelautentisering](#account-key-authentication)
- [Autentisering av tjänstens huvudnamn](#service-principal-authentication)
- [Hanterade identiteter för autentisering av Azure-resurser](#managed-identity)

### <a name="account-key-authentication"></a>Konto-nyckelautentisering

Om du vill använda nyckelautentisering för storage-konto, stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till **AzureBlobFS**. |Ja |
| url | Slutpunkt för Data Lake Storage Gen2 med mönstret för `https://<accountname>.dfs.core.windows.net`. | Ja |
| accountKey | Kontonyckel för Data Lake Storage Gen2. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure integration runtime eller en lokal integration runtime om ditt datalager är i ett privat nätverk. Om egenskapen inte anges används standard Azure integration runtime. |Nej |

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

Följ dessa steg om du vill använda autentisering av tjänstens huvudnamn.

1. Registrera en entitet för program i Azure Active Directory (Azure AD) genom att följa stegen i [registrera ditt program med en Azure AD-klient](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

2. Ge tjänstens huvudnamn rätt behörighet.

    - **Som källa**: I Azure Storage Explorer ge minst **Läs + kör** behörighet att visa och kopiera filer i mappar och undermappar. Eller, du kan bevilja **Läs** tillstånd att kopiera en fil. Du kan också i åtkomstkontroll (IAM), beviljar minst **Storage Blob Data-läsare** roll.
    - **Som mottagare**: I Lagringsutforskaren kan ge minst **skriva + köra** behörighet att skapa underordnade objekt i mappen. Du kan också i åtkomstkontroll (IAM), beviljar minst **Storage Blob Data-deltagare** roll.

>[!NOTE]
>Listan mappar som börjar från kontonivå eller att testa anslutning, måste du ange behörigheten för tjänstens huvudnamn som beviljas till **storage-konto med ”Storage Blob Data Reader” behörighet i IAM**. Detta gäller när du använder den:
>- **Kopiera data verktyget** att författaren kopiering av pipeline.
>- **Användargränssnittet för data Factory** att testa anslutningen och navigera mappar under redigering. 
>Om du har frågor om hur du beviljar behörighet på kontonivå, du kan hoppa över Testa anslutning och indatasökvägen manuellt under redigering. Kopieringsaktivitet fungerar fortfarande så länge tjänstens huvudnamn har beviljats med rätt behörighet på filerna som ska kopieras.

De här egenskaperna har stöd för den länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till **AzureBlobFS**. |Ja |
| url | Slutpunkt för Data Lake Storage Gen2 med mönstret för `https://<accountname>.dfs.core.windows.net`. | Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en `SecureString` ska lagras på ett säkert sätt i Data Factory. Du kan också [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Hämta det håller musen i det övre högra hörnet i Azure Portal. | Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure integration runtime eller en lokal integration runtime om ditt datalager är i ett privat nätverk. Om den inte anges används standard Azure integration runtime. |Nej |

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

En data factory kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md), som representerar den här specifika data factory. Du kan använda den här hanterade identiteten direkt för Data Lake Storage Gen2 autentisering, ungefär som att använda tjänstens huvudnamn. Det gör den här avsedda fabriken kan komma åt och kopiera data till eller från ditt Data Lake Storage Gen2.

Följ dessa steg om du vill använda hanterade identiteter för Azure-resurs-autentisering.

1. [Hämta Data Factory hanteras identitetsinformation](data-factory-service-identity.md#retrieve-managed-identity) genom att kopiera värdet för den **identitet program-ID för tjänstens** genereras tillsammans med din datafabrik.

2. Ge hanterad identitet rätt behörighet.

    - **Som källa**: I Lagringsutforskaren kan ge minst **Läs + kör** behörighet att visa och kopiera filer i mappar och undermappar. Eller, du kan bevilja **Läs** tillstånd att kopiera en fil. Du kan också i åtkomstkontroll (IAM), beviljar minst **Storage Blob Data-läsare** roll.
    - **Som mottagare**: I Lagringsutforskaren kan ge minst **skriva + köra** behörighet att skapa underordnade objekt i mappen. Du kan också i åtkomstkontroll (IAM), beviljar minst **Storage Blob Data-deltagare** roll.

>[!NOTE]
>Listan mappar som börjar från kontonivå eller att testa anslutning, måste du ange behörighet för den hanterade identitet som beviljas till **storage-konto med ”Storage Blob Data Reader” behörighet i IAM**. Detta gäller när du använder den:
>- **Kopiera data verktyget** att författaren kopiering av pipeline.
>- **Användargränssnittet för data Factory** att testa anslutningen och navigera mappar under redigering. 
>Om du har frågor om hur du beviljar behörighet på kontonivå, du kan hoppa över Testa anslutning och indatasökvägen manuellt under redigering. Kopieringsaktivitet fungerar fortfarande så länge som den hanterade identitet beviljas med rätt behörighet på filerna som ska kopieras.

>[!IMPORTANT]
>Om du använder PolyBase för att läsa in data från Data Lake Storage Gen2 till SQL Data Warehouse, när du använder Data Lake Storage Gen2 hanterad identitetsautentisering, se till att du även följa steg 1 och 2 i [den här vägledningen](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Följ instruktionerna för att registrera din SQL Database-server med Azure Active Directory (Azure AD). Du kan också tilldela rollen Storage Blob Data-deltagare med rollbaserad åtkomstkontroll till din SQL Database-server. Resten hanteras av Data Factory. Om dina Data Lake Storage Gen2 konfigureras med en Azure Virtual Network-slutpunkt för att använda PolyBase för att läsa in data från den, måste du använda hanterade identitetsautentisering.

De här egenskaperna har stöd för den länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till **AzureBlobFS**. |Ja |
| url | Slutpunkt för Data Lake Storage Gen2 med mönstret för `https://<accountname>.dfs.core.windows.net`. | Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure integration runtime eller en lokal integration runtime om ditt datalager är i ett privat nätverk. Om den inte anges används standard Azure integration runtime. |Nej |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningar](concepts-datasets-linked-services.md).

- Parquet och avgränsat textformat finns i den [Parquet och avgränsad text formatera datauppsättning](#parquet-and-delimited-text-format-dataset) avsnittet.
- Andra format som ORC, Avro, JSON eller binärt format, finns det [andra format datauppsättning](#other-format-dataset) avsnittet.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet och avgränsad text format datauppsättning

För att kopiera data till och från Data Lake Storage Gen2 i parquet eller avgränsat textformat, se den [Parquet-format](format-parquet.md) och [avgränsat textformat](format-delimited-text.md) artiklar om format-baserade datauppsättning och inställningar som stöds. Följande egenskaper har stöd för Data Lake Storage Gen2 under `location` inställningar i datauppsättningen format-baserade:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Egenskapen type under `location` i datauppsättningen måste anges till **AzureBlobFSLocation**. | Ja      |
| fileSystem | Data Lake Storage Gen2 system filnamnet.                              | Nej       |
| folderPath | Sökvägen till en mapp under det angivna filsystemet. Om du vill använda ett jokertecken för att filtrera mappar, hoppa över den här inställningen och anger den i inställningarna för kopieringsaktiviteten källa. | Nej       |
| fileName   | Filnamnet under den angivna filsystem + folderPath. Om du vill använda ett jokertecken för att filtrera filer, hoppa över den här inställningen och anger den i inställningarna för kopieringsaktiviteten källa. | Nej       |

> [!NOTE]
> Den **AzureBlobFSFile** typ datauppsättning med parquet- eller text-format som anges i följande avsnitt stöds fortfarande skick för kopia, sökning och GetMetadata-aktiviteten för bakåtkompatibilitet. Men det fungerar inte med i flow-funktionen för mappning av data. Vi rekommenderar att du använder den nya modellen framöver. Data Factory redigering Användargränssnittet genererar dessa nya typer.

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

### <a name="other-format-dataset"></a>Andra format-datauppsättning

För att kopiera data till och från Data Lake Storage Gen2 i ORC-, Avro-, JSON- eller binärt format, stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för datauppsättningen måste anges till **AzureBlobFSFile**. |Ja |
| folderPath | Sökvägen till mappen i Data Lake Storage Gen2. Om den inte anges som den pekar till roten. <br/><br/>Jokertecken-filtret stöds. Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken). Använd `^` att undvika om din faktiska mappnamn har ett jokertecken eller har tillgång till den här escape-tecken. <br/><br/>Exempel: filsystem/mapp /. Se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). |Nej |
| fileName | Namn eller jokertecken filter för filerna under den angivna ”folderPath”. Om du inte anger ett värde för den här egenskapen datauppsättningen pekar på alla filer i mappen. <br/><br/>För filter jokertecken tillåts är `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken).<br/>– Exempel 1: `"fileName": "*.csv"`<br/>– Exempel 2: `"fileName": "???20180427.txt"`<br/>Använd `^` att undvika om din faktiska filnamnet har ett jokertecken eller har tillgång till den här escape-tecken.<br/><br/>Om filnamnet har inte angetts för en utdatauppsättning och **preserveHierarchy** inte har angetts i aktiviteten-mottagare kopieringsaktiviteten genererar automatiskt filnamnet med följande mönster: ”*Data. [aktivitet köras ID GUID]. [GUID om FlattenHierarchy]. [format om konfigurerat]. [komprimering om konfigurerat]* ”, till exempel” Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz ”. Om du kopierar från en tabular källa med ett tabellnamn i stället för en fråga namnet har formatet är ” *[tabellnamn]. [ format]. [komprimering om konfigurerat]* ”, till exempel” MyTable.csv ”. |Nej |
| modifiedDatetimeStart | Filter för filer baserat på attribut som senast ändrad. Filerna har valts om deras tid för senaste ändring ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br/><br/> Dataförflyttning serverns prestanda påverkas genom att aktivera den här inställningen när du vill filen filter med stora mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inga attributfiltret tillämpas på datauppsättningen. När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senaste ändrade attribut är större än eller lika med datetime-värde har valts. När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är NULL, betyder det filer vars senaste ändrade attributet är mindre än det datum/tid-värdet är markerade.| Nej |
| modifiedDatetimeEnd | Filter för filer baserat på attribut som senast ändrad. Filerna har valts om deras tid för senaste ändring ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br/><br/> Dataförflyttning serverns prestanda påverkas genom att aktivera den här inställningen när du vill filen filter med stora mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inga attributfiltret tillämpas på datauppsättningen. När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senaste ändrade attribut är större än eller lika med datetime-värde har valts. När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är NULL, betyder det filer vars senaste ändrade attributet är mindre än det datum/tid-värdet är markerade.| Nej |
| format | Om du vill kopiera filer som finns mellan filbaserade lager (binär kopia), kan du hoppa över avsnittet format i både inkommande och utgående datamängd definitionerna.<br/><br/>Om du vill parsa eller generera filer med ett visst format stöds format för följande filtyper: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, och **ParquetFormat**. Ange den **typ** egenskapen under **format** till någon av dessa värden. Mer information finns i den [textformat](supported-file-formats-and-compression-codecs.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [ORC-format](supported-file-formats-and-compression-codecs.md#orc-format), och [Parquet-format ](supported-file-formats-and-compression-codecs.md#parquet-format) avsnitt. |Nej (endast för binär kopia scenario) |
| compression | Ange typ och komprimeringsnivå för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Stöds nivåer **Optimal** och **snabbast**. |Nej |

>[!TIP]
>Kopiera alla filer i en mapp genom att ange **folderPath** endast.<br>Om du vill kopiera en enstaka fil med ett givet namn, ange **folderPath** med en mappdelen och **fileName** med ett filnamn.<br>Om du vill kopiera en delmängd av filerna under en mapp, anger **folderPath** med en mappdelen och **fileName** med jokertecken-filtret. 

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

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [kopiera aktivitetskonfigurationer](copy-activity-overview.md#configuration) och [Pipelines och aktiviteter](concepts-pipelines-activities.md). Det här avsnittet innehåller en lista över egenskaper som stöds av Data Lake Storage Gen2 källa och mottagare.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 som en typ av datakälla

- Om du vill kopiera från parquet eller avgränsat textformat, se den [Parquet och avgränsad text format källa](#parquet-and-delimited-text-format-source) avsnittet.
- Kopiera från andra format som ORC-, Avro-, JSON- eller binärt format genom att se den [annan format källa](#other-format-source) avsnittet.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet och avgränsad text format källa

Om du vill kopiera data från Data Lake Storage Gen2 i parquet eller avgränsat textformat, se den [Parquet-format](format-parquet.md) och [avgränsat textformat](format-delimited-text.md) format-baserade aktiviteten kopieringskälla och inställningar som stöds. Följande egenskaper har stöd för Data Lake Storage Gen2 under `storeSettings` inställningar i formatet-baserade kopieringskälla:

| Egenskap                 | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Egenskapen type under `storeSettings` måste anges till **AzureBlobFSReadSetting**. | Ja                                           |
| recursive                | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. När rekursiv har angetts till true och mottagaren är en filbaserad store, en tom mapp eller undermapp inte kopieras eller skapat i mottagaren. Tillåtna värden är **SANT** (standard) och **FALSKT**. | Nej                                            |
| wildcardFolderPath       | Sökvägen till mappen med jokertecken under den angivna filsystem som konfigurerats i datauppsättningen för att filtrera källa mappar. <br>Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken). Använd `^` att undvika om din faktiska mappnamn har ett jokertecken eller den här escape-tecken i. <br>Se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). | Nej                                            |
| wildcardFileName         | Filnamn med jokertecken under den angivna filsystemet + folderPath/wildcardFolderPath filter källfilerna för. <br>Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken). Använd `^` att undvika om din faktiska mappnamn har ett jokertecken eller den här escape-tecken i. Se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). | Ja om `fileName` inte har angetts i datauppsättningen |
| modifiedDatetimeStart    | Filter för filer baserat på attribut som senast ändrad. Filerna har valts om deras tid för senaste ändring ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br> Egenskaperna kan vara NULL, vilket innebär att inga attributfiltret tillämpas på datauppsättningen. När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senast ändrades attribut är större än eller lika med datetime-värde har valts. När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är NULL, betyder det att filer vars senaste ändrade attributet är mindre än det datum/tid-värdet är markerade. | Nej                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Nej                                            |
| maxConcurrentConnections | Antal anslutningar för att ansluta till storage store samtidigt. Ange bara när du vill begränsa samtidiga anslutningen till datalagret. | Nej                                            |

> [!NOTE]
> För parquet eller avgränsat textformat den **AzureBlobFSSource** typen kopiera aktivitetskälla som nämns i följande avsnitt stöds fortfarande skick för bakåtkompatibilitet. Vi rekommenderar att du använder den nya modellen framöver. Data Factory redigering Användargränssnittet genererar dessa nya typer.

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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSetting",
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

För att kopiera data från Data Lake Storage Gen2 i ORC-, Avro-, JSON- eller binärt format stöds följande egenskaper i kopieringsaktiviteten **källa** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för aktiviteten kopieringskälla måste anges till **AzureBlobFSSource**. |Ja |
| recursive | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. När rekursiv har angetts till true och mottagaren är en filbaserad store, en tom mapp eller undermapp inte kopieras eller skapat i mottagaren.<br/>Tillåtna värden är **SANT** (standard) och **FALSKT**. | Nej |
| maxConcurrentConnections | Antal anslutningar för att ansluta till datalagret samtidigt. Ange bara när du vill begränsa samtidiga anslutningen till datalagret. | Nej |

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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 som en Mottagartyp

- Om du vill kopiera till parquet eller avgränsat textformat, se den [Parquet och avgränsad text format mottagare](#parquet-and-delimited-text-format-sink) avsnittet.
- Kopiera till andra format som ORC-, Avro-, JSON- eller binärt format genom att se den [andra format mottagare](#other-format-sink) avsnittet.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet och avgränsad text format mottagare

För att kopiera data till Data Lake Storage Gen2 i parquet eller avgränsat textformat, se den [Parquet-format](format-parquet.md) och [avgränsat textformat](format-delimited-text.md) artiklar om format-baserade kopiera aktivitet mottagare och inställningar som stöds. Följande egenskaper har stöd för Data Lake Storage Gen2 under `storeSettings` inställningar i formatet-baserad kopia mottagare:

| Egenskap                 | Beskrivning                                                  | Krävs |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Egenskapen type under `storeSettings` måste anges till **AzureBlobFSWriteSetting**. | Ja      |
| copyBehavior             | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: Bevarar filen hierarkin i målmappen. Den relativa sökvägen på källfilen för målmappen är identisk med den relativa sökvägen till målfilen till målmappen.<br/><b>-FlattenHierarchy</b>: Alla filer från källmappen finns i den första nivån i målmappen. Målfiler har automatiskt genererade unika namn. <br/><b>-MergeFiles</b>: Slår samman alla filer från källmappen till en fil. Om filnamnet har angetts är namnet på sammanfogade filen det angivna namnet. Annars är det en automatiskt skapade filnamnet. | Nej       |
| maxConcurrentConnections | Antal anslutningar för att ansluta till datalagret samtidigt. Ange bara när du vill begränsa samtidiga anslutningen till datalagret. | Nej       |

> [!NOTE]
> För parquet eller avgränsat textformat den **AzureBlobFSSink** typen kopiera aktivitet mottagare som nämns i följande avsnitt stöds fortfarande skick för bakåtkompatibilitet. Vi rekommenderar att du använder den nya modellen framöver. Data Factory redigering Användargränssnittet genererar dessa nya typer.

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
                    "type": "AzureBlobFSWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Andra format-mottagare

För att kopiera data till Data Lake Storage Gen2 i ORC-, Avro-, JSON- eller binärt format stöds följande egenskaper i den **mottagare** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen type kopiera aktivitet komprimeringstyp måste anges till **AzureBlobFSSink**. |Ja |
| copyBehavior | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: Bevarar filen hierarkin i målmappen. Den relativa sökvägen på källfilen för målmappen är identisk med den relativa sökvägen till målfilen till målmappen.<br/><b>-FlattenHierarchy</b>: Alla filer från källmappen finns i den första nivån i målmappen. Målfiler har automatiskt genererade unika namn. <br/><b>-MergeFiles</b>: Slår samman alla filer från källmappen till en fil. Om filnamnet har angetts är namnet på sammanfogade filen det angivna namnet. Annars är det en automatiskt skapade filnamnet. | Nej |
| maxConcurrentConnections | Antal anslutningar för att ansluta till datalagret samtidigt. Ange bara när du vill begränsa samtidiga anslutningen till datalagret. | Nej |

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

### <a name="folder-and-file-filter-examples"></a>Mapp och fil filter-exempel

Det här avsnittet beskriver resultatet av sökvägen och namnet på mappen med jokertecken filter.

| folderPath | fileName | recursive | Käll-mappen struktur och filtrera resultatet (filer i **fetstil** hämtas)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Tom, Använd standard) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Tom, Använd standard) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Några exempel rekursiv och copyBehavior

Det här avsnittet beskrivs kopieringsåtgärden för olika kombinationer av värden för rekursiv och copyBehavior resulterande beteende.

| recursive | copyBehavior | Mappstruktur för källa | Resulterande mål |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med samma struktur som källa:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt skapade namn på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File5 |
| true |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 + fil3 + File4 + File5 innehållet slås samman i en fil med ett automatiskt genererade namnet. |
| false |preserveHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/><br/>Subfolder1 med fil3, File4 och File5 hämtas inte. |
| false |flattenHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt skapade namn på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/><br/>Subfolder1 med fil3, File4 och File5 hämtas inte. |
| false |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 innehållet slås samman i en fil med ett automatiskt genererade namnet. automatiskt skapade namn på File1<br/><br/>Subfolder1 med fil3, File4 och File5 hämtas inte. |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Bevara ACL: er från Data Lake Storage Gen1

>[!TIP]
>Om du vill kopiera data från Azure Data Lake Storage Gen1 till Gen2 i allmänhet, se [kopiera data från Azure Data Lake Storage Gen1 till Gen2 med Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) för en genomgång och bästa praxis.

När du kopierar filer från Azure Data Lake Storage Gen1 till Gen2, kan du välja att bevara de POSIX åtkomstkontrollistor (ACL) tillsammans med data. Mer information om behörighet finns i [åtkomstkontroll i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) och [åtkomstkontroll i Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

Följande typer av ACL: er kan bevaras med hjälp av Azure Data Factory-kopieringsaktiviteten. Du kan välja en eller flera typer:

- **ACL**: Kopiera och bevara POSIX-åtkomstkontrollistor för filer och kataloger. Fullständig befintliga ACL: er kopieras från källan till mottagare. 
- **Ägare**: Kopiera och bevara ägande användare av filer och kataloger. En användaråtkomst till mottagare Data Lake Storage Gen2 krävs.
- **Grupp**: Kopiera och bevara den ägande gruppen för filer och kataloger. En användaråtkomst till mottagare Data Lake Storage Gen2 eller ägande användare (om den ägande användaren också är medlem i målgruppen) krävs.

Om du anger för att kopiera från en mapp, replikerar Data Factory ACL: er för den angivna mappen och filer och kataloger under det om `recursive` har angetts till true. Om du anger för att kopiera från en enda fil, kopieras filen åtkomstkontrollistor.

>[!IMPORTANT]
>När du väljer att bevara ACL: er kan du kontrollera att du hög beviljar tillräcklig behörighet för Data Factory ska kunna arbeta mot dina mottagare Gen2 för Data Lake Storage-konto. Till exempel använder kontot nyckelautentisering eller tilldela rollen ägare för Storage Blob-Data till primära eller hanterad tjänstidentitet.

När du konfigurerar källa som Data Lake Storage Gen1 med alternativet binär kopia eller binärt format och mottagare som Data Lake Storage Gen2 med alternativet binär kopia eller binärt format, du kan hitta den **bevara** alternativet på den **kopia Inställningar för verktyget** sidan eller på den **Kopieringsaktiviteten** > **inställningar** fliken för redigering av aktivitet.

![Data Lake Storage Gen1 till Gen2 Preserve ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Här är ett exempel på JSON-konfiguration (se `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mappning av egenskaper för flödet av data

Läs mer om [source omvandling](data-flow-source.md) och [mottagare omvandling](data-flow-sink.md) i mappningsfunktionen av flödet.

## <a name="next-steps"></a>Nästa steg

En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
