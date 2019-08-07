---
title: Kopiera data till eller från Azure Data Lake Storage Gen2 med Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data till och från Azure Data Lake Storage Gen2 med hjälp av Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: a0a7a413d6c3344ccf5c3f7e4d14dd3d82715034
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840320"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Kopiera data till eller från Azure Data Lake Storage Gen2 med Azure Data Factory

Azure Data Lake Storage Gen2 är en uppsättning funktioner som är avsedda för stor data analys som är inbyggd i [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md). Du kan använda den för att samverka med dina data med hjälp av både fil systemet och objekt lagrings paradigm.

Den här artikeln beskriver hur du kopierar data till och från Azure Data Lake Storage Gen2. Läs om Azure Data Factory den [introduktionsartikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Data Lake Storage Gen2 anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [käll-eller mottagar mat ris som stöds](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)

Med den här anslutningen kan du särskilt:

- Kopiera data med hjälp av konto nyckel, tjänstens huvud namn eller hanterade identiteter för Azure-resursers autentiseringar.
- Kopiera filer som är eller tolka eller generera filer med [fil format och komprimerings-codec som stöds](supported-file-formats-and-compression-codecs.md).

>[!TIP]
>Om du aktiverar det hierarkiska namn området finns det för närvarande ingen samverkan mellan åtgärder mellan blob-och Data Lake Storage Gen2-API: er. Om du träffar felet "ErrorCode = FilesystemNotFound" med meddelandet "det angivna fil systemet finns inte", orsakas det av det angivna Sink-filsystemet som skapades via BLOB-API: et i stället för att Data Lake Storage Gen2 API någon annan stans. Åtgärda problemet genom att ange ett nytt fil system med ett namn som inte finns som namn på en BLOB-behållare. Sedan skapar Data Factory automatiskt fil systemet under data kopieringen.

>[!NOTE]
>Om du aktiverar alternativet **Tillåt betrodda Microsoft-tjänster för att komma åt det här lagrings kontot** i Azure Storage brand Väggs inställningar, ansluter inte Azure integration runtime till data Lake Storage Gen2 och visar ett otillåtet fel. Fel meddelandet visas eftersom Data Factory inte behandlas som en betrodd Microsoft-tjänst. Använd den egen värdbaserade integrerings körningen för att ansluta i stället.

## <a name="get-started"></a>Kom igång

>[!TIP]
>En genom gång av hur du använder Data Lake Storage Gen2-anslutningen finns i [läsa in data i Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Azure Data Lake Storage Gen2-anslutningen har stöd för följande typer av autentisering. Mer information finns i motsvarande avsnitt:

- [Konto-nyckelautentisering](#account-key-authentication)
- [Autentisering av tjänstens huvudnamn](#service-principal-authentication)
- [Hanterade identiteter för autentisering av Azure-resurser](#managed-identity)

>[!NOTE]
>När du använder PolyBase för att läsa in data i SQL Data Warehouse måste du använda hanterad identitetsautentisering som krävs för PolyBase om din käll Data Lake Storage Gen2 har kon figurer ATS med Virtual Network-slutpunkt. Se avsnittet [hanterad identitets autentisering](#managed-identity) med fler konfigurations krav.

### <a name="account-key-authentication"></a>Konto-nyckelautentisering

Om du vill använda nyckelautentisering för storage-konto, stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till **AzureBlobFS**. |Ja |
| url | Slut punkt för Data Lake Storage Gen2 med mönstret för `https://<accountname>.dfs.core.windows.net`. | Ja |
| accountKey | Konto nyckel för Data Lake Storage Gen2. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure integration runtime eller en lokal integration Runtime om ditt data lager finns i ett privat nätverk. Om den här egenskapen inte anges används standard körningen av Azure integration. |Nej |

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

2. Ge tjänstens huvud behörighet rätt behörighet. Läs mer om hur behörigheten fungerar i Data Lake Storage Gen2 från [åtkomst kontrol listor på filer och kataloger](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Som källa**: I Storage Explorer beviljar du minst **Kör** behörighet från käll fil systemet, tillsammans med **Läs** behörighet för de filer som ska kopieras. I åtkomst kontroll (IAM) beviljar du minst rollen **Storage BLOB data Reader** .
    - **Som mottagare**: I Storage Explorer beviljar du minst **Kör** behörighet från mottagar fil systemet, tillsammans med **Skriv** behörighet för mappen mottagare. Alternativt, i åtkomst kontroll (IAM), beviljar du minst rollen **Storage BLOB data Contributor** .

>[!NOTE]
>Om du vill visa en lista över mappar som börjar från konto nivån eller för att testa anslutningen måste du ange behörighet för tjänstens huvud namn till **lagrings kontot med behörigheten "Storage BLOB data Reader" i IAM**. Detta gäller när du använder:
>- **Verktyget kopiera data** till redigerare för att skapa kopia.
>- **Data Factory UI** för att testa anslutningen och navigera i mappar under redigeringen. 
>Om du har problem med att bevilja behörighet på konto nivå, under redigering, hoppa över test anslutning och ange en överordnad sökväg med behörighet som beviljats väljer du att bläddra från den angivna sökvägen. Kopierings aktiviteten fungerar så länge som tjänstens huvud namn beviljas med rätt behörighet till de filer som ska kopieras.

De här egenskaperna stöds för den länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till **AzureBlobFS**. |Ja |
| url | Slut punkt för Data Lake Storage Gen2 med mönstret för `https://<accountname>.dfs.core.windows.net`. | Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som `SecureString` en för att lagra det på ett säkert sätt i Data Factory. Eller så kan du [referera till en hemlighet som lagrats i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Hämta det genom att hovra musen i det övre högra hörnet av Azure Portal. | Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure integration runtime eller en lokal integration Runtime om ditt data lager finns i ett privat nätverk. Om inget värde anges används standard Azure integration Runtime. |Nej |

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

En data factory kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md), som representerar den här specifika data factory. Du kan använda den här hanterade identiteten direkt för Data Lake Storage Gen2 autentisering, på samma sätt som du använder ditt eget tjänst huvud namn. Det gör att den här fabriken kan komma åt och kopiera data till eller från din Data Lake Storage Gen2.

Följ dessa steg om du vill använda hanterade identiteter för Azure Resource Authentication.

1. [Hämta data Factory hanterad identitets information](data-factory-service-identity.md#retrieve-managed-identity) genom att kopiera värdet för det **tjänst identitet program-ID** som genererats tillsammans med din fabrik.

2. Ge behörigheten hanterad identitet korrekt. Läs mer om hur behörigheten fungerar i Data Lake Storage Gen2 från [åtkomst kontrol listor på filer och kataloger](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Som källa**: I Storage Explorer beviljar du minst **Kör** behörighet från käll fil systemet, tillsammans med **Läs** behörighet för de filer som ska kopieras. I åtkomst kontroll (IAM) beviljar du minst rollen **Storage BLOB data Reader** .
    - **Som mottagare**: I Storage Explorer beviljar du minst **Kör** behörighet från mottagar fil systemet, tillsammans med **Skriv** behörighet för mappen mottagare. Alternativt, i åtkomst kontroll (IAM), beviljar du minst rollen **Storage BLOB data Contributor** .

>[!NOTE]
>Om du vill visa en lista över mappar som börjar från konto nivån eller för att testa anslutningen måste du ange behörighet för den hanterade identiteten som beviljats till **lagrings kontot med behörigheten "Storage BLOB data Reader" i IAM**. Detta gäller när du använder:
>- **Verktyget kopiera data** till redigerare för att skapa kopia.
>- **Data Factory UI** för att testa anslutningen och navigera i mappar under redigeringen. 
>Om du har problem med att bevilja behörighet på konto nivå, under redigering, hoppa över test anslutning och ange en överordnad sökväg med behörighet som beviljats väljer du att bläddra från den angivna sökvägen. Kopierings aktiviteten fungerar så länge som tjänstens huvud namn beviljas med rätt behörighet till de filer som ska kopieras.

>[!IMPORTANT]
>Om du använder PolyBase för att läsa in data från Data Lake Storage Gen2 till SQL Data Warehouse, ska du, när du använder hanterad identitetsautentisering för Data Lake Storage Gen2, kontrol lera att du också följer steg 1 och 2 i [den här vägledningen](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) till 1) registrera din SQL Database-Server med Azure Active Directory (Azure AD) och 2) tilldela rollen Storage BLOB data Contributor till din SQL Database-Server. resten hanteras av Data Factory. Om din Data Lake Storage Gen2 har kon figurer ATS med en Azure Virtual Network-slutpunkt, för att använda PolyBase för att läsa in data från den, måste du använda hanterad identitetsautentisering som krävs av PolyBase.

De här egenskaperna stöds för den länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till **AzureBlobFS**. |Ja |
| url | Slut punkt för Data Lake Storage Gen2 med mönstret för `https://<accountname>.dfs.core.windows.net`. | Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure integration runtime eller en lokal integration Runtime om ditt data lager finns i ett privat nätverk. Om inget värde anges används standard Azure integration Runtime. |Nej |

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

- För **Parquet, avgränsad text och binärt format**, se avsnittet [Parquet, avgränsad text och binära format](#format-based-dataset) .
- Andra format som **Orc/Avro/JSON-format**finns i avsnittet [annan format data uppsättning](#other-format-dataset) .

### <a name="format-based-dataset"></a>Parquet, avgränsad text och binärt format data uppsättning

Om du vill kopiera data till och från **Parquet, avgränsade text-eller binärformat**, kan du läsa mer i [Parquet format](format-parquet.md), avgränsat [text format](format-delimited-text.md) och binära [format](format-binary.md) i artikeln format-baserad data uppsättning och inställningar som stöds. Följande egenskaper stöds för data Lake Storage Gen2 under `location` inställningar i den formatbaserade data uppsättningen:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Egenskapen Type under `location` i data uppsättningen måste anges till **AzureBlobFSLocation**. | Ja      |
| fileSystem | Namnet på filen med Data Lake Storage Gen2-systemet.                              | Nej       |
| folderPath | Sökvägen till en mapp under det angivna fil systemet. Om du vill använda ett jokertecken för att filtrera mappar, hoppar du över den här inställningen och anger den i aktivitets källans inställningar. | Nej       |
| fileName   | Fil namnet under angivet fileSystem + folderPath. Om du vill använda ett jokertecken för att filtrera filer, hoppar du över den här inställningen och anger den i aktivitets källans inställningar. | Nej       |

> [!NOTE]
> Data uppsättningen för **AzureBlobFSFile** -typ med Parquet eller text format som nämns i följande avsnitt stöds fortfarande för kopierings-, lookup-eller getMetaData-aktivitet för bakåtkompatibilitet. Men det fungerar inte med funktionen mappa data flöde. Vi rekommenderar att du använder den här nya modellen som går framåt. Användar gränssnittet för Data Factory redigering genererar dessa nya typer.

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

### <a name="other-format-dataset"></a>Data uppsättning för andra format

Följande egenskaper stöds för att kopiera data till och från Data Lake Storage Gen2 i **Orc/Avro/JSON-format**:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för datauppsättningen måste anges till **AzureBlobFSFile**. |Ja |
| folderPath | Sökväg till mappen i Data Lake Storage Gen2. Om den inte anges som den pekar till roten. <br/><br/>Wildcard-filtret stöds. Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Används `^` för att kringgå om det faktiska mappnamnet har ett jokertecken eller om detta escape-tecken finns inuti. <br/><br/>Exempel: filesystem/Folder/. Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). |Nej |
| fileName | Namn eller Wildcard-filter för filerna under den angivna "folderPath". Om du inte anger ett värde för den här egenskapen datauppsättningen pekar på alla filer i mappen. <br/><br/>För filter är `*` tillåtna jokertecken (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken).<br/>– Exempel 1: `"fileName": "*.csv"`<br/>– Exempel 2: `"fileName": "???20180427.txt"`<br/>Används `^` för att kringgå om det faktiska fil namnet har ett jokertecken eller om detta escape-tecken är inuti.<br/><br/>När fil namnet inte har angetts för en data uppsättning för utdata och **preserveHierarchy** inte har angetts i aktivitets mottagaren genererar kopierings aktiviteten automatiskt fil namnet med följande mönster: "*Data. [GUID för aktivitets körnings-ID]. [GUID om FlattenHierarchy]. [format om det är konfigurerat]. [komprimering om konfigurerad]* ", till exempel" data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz ". Om du kopierar från en tabell källa med ett tabell namn i stället för en fråga, är namn mönstret " *[tabell namn]. [ format]. [komprimering om konfigurerad]* ", till exempel" Table. csv ". |Nej |
| modifiedDatetimeStart | Filter för filer baserat på det senast ändrade attributet. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Den övergripande prestandan för data förflyttning påverkas om du aktiverar den här inställningen när du vill göra fil filter till stora mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget attribut filter används för data uppsättningen. När `modifiedDatetimeStart` har ett datetime-värde `modifiedDatetimeEnd` men är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime är markerade. När `modifiedDatetimeEnd` har ett datetime-värde `modifiedDatetimeStart` men är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime är markerat.| Nej |
| modifiedDatetimeEnd | Filter för filer baserat på det senast ändrade attributet. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Den övergripande prestandan för data förflyttning påverkas om du aktiverar den här inställningen när du vill göra fil filter till stora mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget attribut filter används för data uppsättningen. När `modifiedDatetimeStart` har ett datetime-värde `modifiedDatetimeEnd` men är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime är markerade. När `modifiedDatetimeEnd` har ett datetime-värde `modifiedDatetimeStart` men är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime är markerat.| Nej |
| format | Om du vill kopiera filer som finns mellan filbaserade lager (binär kopia), kan du hoppa över avsnittet format i både inkommande och utgående datamängd definitionerna.<br/><br/>Om du vill parsa eller generera filer med ett speciellt format stöds följande fil format typer:Text Forms, **JsonFormat**, **AvroFormat**, **OrcFormat**och **ParquetFormat**. Ange den **typ** egenskapen under **format** till någon av dessa värden. Mer information finns i avsnitten [text format](supported-file-formats-and-compression-codecs.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs.md#json-format), [Avro format](supported-file-formats-and-compression-codecs.md#avro-format), [Orc format](supported-file-formats-and-compression-codecs.md#orc-format)och [Parquet format](supported-file-formats-and-compression-codecs.md#parquet-format) . |Nej (endast för binär kopia scenario) |
| compression | Ange typ och komprimeringsnivå för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Stöds nivåer **Optimal** och **snabbast**. |Nej |

>[!TIP]
>Kopiera alla filer i en mapp genom att ange **folderPath** endast.<br>Om du vill kopiera en enskild fil med ett visst namn anger du **folderPath** med en mapp och ett fil namn med ett fil namn.<br>Om du vill kopiera en delmängd av filer under en mapp anger du **folderPath** med en mapp och ett **fil namn** med ett Wildcard-filter. 

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Kopiera aktivitets konfigurationer](copy-activity-overview.md#configuration) och [pipeliner och aktiviteter](concepts-pipelines-activities.md). Det här avsnittet innehåller en lista över egenskaper som stöds av Data Lake Storage Gen2 källa och mottagare.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 som typ av källa

- Om du vill kopiera från **Parquet, avgränsad text och binärt format**, se avsnittet [Parquet, avgränsad text och binärt format](#format-based-source) .
- Om du vill kopiera från andra format som **Orc/Avro/JSON-format**, se avsnittet [annan format källa](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, avgränsad text-och binär format källa

Om du vill kopiera data från **Parquet, avgränsad text eller binärt format**, referera till [Parquet-format](format-parquet.md), avgränsat [text format](format-delimited-text.md) och binära [format](format-binary.md) -artikel med formatbaserade kopierings aktivitets källor och inställningar som stöds. Följande egenskaper stöds för data Lake Storage Gen2 under `storeSettings` inställningar i format-baserad kopierings Källa:

| Egenskap                 | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Typ egenskapen under `storeSettings` måste anges till **AzureBlobFSReadSetting**. | Ja                                           |
| recursive                | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. När rekursivt är inställt på True och sinken är en filbaserad lagring, kopieras inte en tom mapp eller undermapp till mottagaren. Tillåtna värden är **SANT** (standard) och **FALSKT**. | Nej                                            |
| wildcardFolderPath       | Mappsökvägen med jokertecken under det angivna fil systemet som kon figurer ATS i data uppsättningen för att filtrera källmappen. <br>Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Används `^` för att kringgå om det faktiska mappnamnet har ett jokertecken eller detta escape-tecken inuti. <br>Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Nej                                            |
| wildcardFileName         | Fil namnet med jokertecken under angivet fil system + folderPath/wildcardFolderPath för att filtrera källfiler. <br>Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Används `^` för att kringgå om det faktiska mappnamnet har ett jokertecken eller detta escape-tecken inuti. Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Ja om `fileName` inte anges i data uppsättningen |
| modifiedDatetimeStart    | Filter för filer baserat på det senast ändrade attributet. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br> Egenskaperna kan vara NULL, vilket innebär att inget filter för filattribut används för data uppsättningen. När `modifiedDatetimeStart` har ett datetime-värde `modifiedDatetimeEnd` men är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime är markerade. När `modifiedDatetimeEnd` har ett datetime-värde `modifiedDatetimeStart` men är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime är markerat. | Nej                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Nej                                            |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till lagrings lager samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej                                            |

> [!NOTE]
> För Parquet eller avgränsat text format har **AzureBlobFSSource** typ kopierings aktivitet som anges i följande avsnitt fortfarande stöd för bakåtkompatibilitet. Vi rekommenderar att du använder den här nya modellen som går framåt. Användar gränssnittet för Data Factory redigering genererar dessa nya typer.

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

#### <a name="other-format-source"></a>Annan format källa

Om du vill kopiera data från Data Lake Storage Gen2 i **Orc/Avro/JSON-format**, stöds följande egenskaper i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för aktiviteten kopieringskälla måste anges till **AzureBlobFSSource**. |Ja |
| recursive | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. När rekursivt är inställt på True och sinken är en filbaserad lagring, kopieras inte en tom mapp eller undermapp till mottagaren.<br/>Tillåtna värden är **SANT** (standard) och **FALSKT**. | Nej |
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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 som en Mottagartyp

- Om du vill kopiera till **Parquet, avgränsad text eller binärt format**, se avsnittet [Parquet, avgränsad text och binära format mottagare](#format-based-sink) .
- Om du vill kopiera till andra format som **Orc/Avro/JSON-format**, se avsnittet [annat format mottagare](#other-format-sink) .

#### <a name="format-based-sink"></a>Parquet, avgränsad text och binära format mottagare

Om du vill kopiera data till **Parquet, avgränsad text eller binärt format**, referera till [Parquet-format](format-parquet.md), avgränsat [text format](format-delimited-text.md) och binära [format](format-binary.md) -artikel för formatbaserade kopierings aktivitets mottagare och inställningar som stöds. Följande egenskaper stöds för data Lake Storage Gen2 under `storeSettings` inställningar i format-baserad kopierings mottagare:

| Egenskap                 | Beskrivning                                                  | Krävs |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Typ egenskapen under `storeSettings` måste anges till **AzureBlobFSWriteSetting**. | Ja      |
| copyBehavior             | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: Bevarar filens hierarki i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><b>-FlattenHierarchy</b>: Alla filer från källmappen är på den första nivån i målmappen. Målfiler har automatiskt genererade unika namn. <br/><b>-MergeFiles</b>: Sammanfogar alla filer från källmappen till en fil. Om filnamnet har angetts är namnet på sammanfogade filen det angivna namnet. Annars är det en automatiskt skapade filnamnet. | Nej       |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till data lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej       |

> [!NOTE]
> För Parquet eller avgränsat text format har **AzureBlobFSSink** typ kopierings aktivitet som anges i följande avsnitt fortfarande stöd för bakåtkompatibilitet. Vi rekommenderar att du använder den här nya modellen som går framåt. Användar gränssnittet för Data Factory redigering genererar dessa nya typer.

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

#### <a name="other-format-sink"></a>Övrigt format mottagare

För att kopiera data till Data Lake Storage Gen2 i **Orc/Avro/JSON-format**, stöds följande egenskaper i avsnittet **mottagare** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen type kopiera aktivitet komprimeringstyp måste anges till **AzureBlobFSSink**. |Ja |
| copyBehavior | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: Bevarar filens hierarki i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><b>-FlattenHierarchy</b>: Alla filer från källmappen är på den första nivån i målmappen. Målfiler har automatiskt genererade unika namn. <br/><b>-MergeFiles</b>: Sammanfogar alla filer från källmappen till en fil. Om filnamnet har angetts är namnet på sammanfogade filen det angivna namnet. Annars är det en automatiskt skapade filnamnet. | Nej |
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

### <a name="folder-and-file-filter-examples"></a>Exempel på mapp-och fil filter

I det här avsnittet beskrivs det resulterande beteendet hos mappsökvägen och fil namnet med filter för jokertecken.

| folderPath | fileName | recursive | Källans mappstruktur och filter resultat (filer i **fetstil** hämtas)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Tom, Använd standard) | false | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | (Tom, Använd standard) | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | false | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Några exempel rekursiv och copyBehavior

I det här avsnittet beskrivs det resulterande beteendet för kopierings åtgärden för olika kombinationer av rekursiva och copyBehavior värden.

| recursive | copyBehavior | Mappstruktur för källa | Resulterande mål |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Mål-Mapp1 skapas med samma struktur som källan:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt skapade namn på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 + fil3 + File4 + File5 innehållet slås samman i en fil med ett automatiskt genererade namnet. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/><br/>Subfolder1 med File3, File4 och File5 har inte hämtats. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt skapade namn på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/><br/>Subfolder1 med File3, File4 och File5 har inte hämtats. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 innehållet slås samman i en fil med ett automatiskt genererade namnet. automatiskt skapade namn på File1<br/><br/>Subfolder1 med File3, File4 och File5 har inte hämtats. |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Behåll ACL: er från Data Lake Storage Gen1

>[!TIP]
>Information om hur du kopierar data från Azure Data Lake Storage Gen1 till Gen2 i allmänhet finns i [Kopiera data från Azure Data Lake Storage gen1 till Gen2 med Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) för en genom gång och bästa praxis.

När du kopierar filer från Azure Data Lake Storage Gen1 till Gen2, kan du välja att behålla åtkomst kontrol listorna för POSIX (ACL) tillsammans med data. Mer information om åtkomst kontroll finns i [åtkomst kontroll i Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md) och [åtkomst kontroll i Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

Följande typer av ACL: er kan bevaras med hjälp av Azure Data Factory kopierings aktiviteten. Du kan välja en eller flera typer:

- **ACL**: Kopiera och bevara POSIX-åtkomst kontrol listor på filer och kataloger. Den kopierar alla befintliga ACL: er från källan till Sink. 
- **Ägare**: Kopiera och bevara ägande användare av filer och kataloger. Super-User-åtkomst till Sink Data Lake Storage Gen2 krävs.
- **Grupp**: Kopiera och bevara den ägande gruppen av filer och kataloger. Super-User-åtkomst till Sink Data Lake Storage Gen2 eller ägande användare (om ägande användare också är medlem i mål gruppen) krävs.

Om du väljer att kopiera från en mapp replikeras Data Factory ACL: er för den aktuella mappen och de filer och kataloger under den, om `recursive` har angetts till sant. Om du väljer att kopiera från en enskild fil kopieras ACL: erna på den filen.

>[!IMPORTANT]
>När du väljer att bevara ACL: er bör du se till att du beviljar hög tillräcklig behörighet för Data Factory att använda ditt mottagares Data Lake Storage Gen2-konto. Använd t. ex. autentisering av konto nycklar eller tilldela rollen Storage BLOB data-ägare till tjänstens huvud namn eller hanterad identitet.

När du konfigurerar källa som Data Lake Storage Gen1 med alternativet binär kopia eller binärt format och handfat som Data Lake Storage Gen2 med alternativet binär kopia eller binärt format, kan du hitta alternativet för att **Spara** på **Kopiera data verktygs inställningar** eller på fliken **Kopiera aktivitets** > **Inställningar** för aktivitets redigering.

![Data Lake Storage Gen1 bevara ACL-Gen2](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Här är ett exempel på en JSON-konfiguration `preserve`(se): 

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

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

Lär dig mer om omvandling av [käll omvandling](data-flow-source.md) och [mottagare](data-flow-sink.md) i funktionen mappa data flöde.

## <a name="next-steps"></a>Nästa steg

En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
