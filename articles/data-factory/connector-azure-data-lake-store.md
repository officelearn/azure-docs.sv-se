---
title: Kopiera data till eller från Azure Data Lake Storage Gen1 med Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från käll data lager som stöds till Azure Data Lake Store eller från Data Lake Store till mottagar mottagar lager med hjälp av Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: d64a8431cb0331b58afc635bf8cf9d0fe0f1f225
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70276047"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Kopiera data till eller från Azure Data Lake Storage Gen1 med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Azure Data Factory som du använder:"]
> * [Version 1](v1/data-factory-azure-datalake-connector.md)
> * [Aktuell version](connector-azure-data-lake-store.md)

Den här artikeln beskriver hur du kopierar data till och från Azure Data Lake Storage Gen1. Läs om Azure Data Factory den [introduktionsartikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Data Lake Storage Gen1 anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [käll-eller mottagar mat ris som stöds](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)

Med den här anslutningen kan du särskilt:

- Kopiera filer med någon av följande autentiseringsmetoder: tjänstens huvud namn eller hanterade identiteter för Azure-resurser.
- Kopiera filer som är eller tolka eller generera filer med de [fil format och komprimerings-codecar som stöds](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Om du kopierar data med hjälp av integration runtime med egen värd konfigurerar du företags brand väggen så att den tillåter utgående `<ADLS account name>.azuredatalakestore.net` trafik `login.microsoftonline.com/<tenant>/oauth2/token` till och på port 443. Det sistnämnda är den Azure-säkerhetstokentjänst som integration runtime måste kommunicera med för att få åtkomst-token.

## <a name="get-started"></a>Kom igång

> [!TIP]
> En genom gång av hur du använder Azure Data Lake Store-anslutningen finns i [läsa in data i Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Azure Data Lake Store.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den länkade tjänsten Azure Data Lake Store:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen måste anges till AzureDataLakeStore. `type` | Ja |
| dataLakeStoreUri | Information om Azure Data Lake Store-konto. Den här informationen antar ett av följande format: `https://[accountname].azuredatalakestore.net/webhdfs/v1` eller `adl://[accountname].azuredatalakestore.net/`. | Ja |
| subscriptionId | ID för Azure-prenumerationen som Data Lake Store kontot tillhör. | Krävs för mottagare |
| resourceGroupName | Namnet på den Azure-resurs grupp som Data Lake Store kontot tillhör. | Krävs för mottagare |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure integration runtime eller en lokal integration Runtime om ditt data lager finns i ett privat nätverk. Om den här egenskapen inte anges används standard körningen av Azure integration. |Nej |

### <a name="use-service-principal-authentication"></a>Använd tjänstens huvud namns autentisering

Om du vill använda tjänstens huvud namns autentisering registrerar du en programentitet i Azure Active Directory och ger den till gång till Data Lake Store. Detaljerade anvisningar finns i [tjänst-till-tjänst-autentisering](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

- Program-ID:t
- Programnyckel
- Klient-ID:t

>[!IMPORTANT]
> Bevilja tjänstens huvud namn rätt behörighet i Data Lake Store:
>- **Som källa**: I **Data Explorer** > -**åtkomst**ger du minst **Läs** -och kör behörighet att visa och kopiera filerna i mappar och undermappar. Du kan också bevilja **Läs** behörighet för att kopiera en enskild fil. Du kan välja att lägga till i **den här mappen och alla underordnade objekt** för rekursivt, och lägga till som **åtkomst behörighet och en standard behörighets post**. Det finns inget krav på konto nivå åtkomst kontroll (IAM).
>- **Som mottagare**: I **Data Explorer** > -**åtkomst**beviljar du minst **Skriv + kör** -behörighet för att skapa underordnade objekt i mappen. Du kan välja att lägga till i **den här mappen och alla underordnade objekt** för rekursivt, och lägga till som **åtkomst behörighet och en standard behörighets post**. Om du använder en Azure integration runtime för att kopiera (både källa och mottagare finns i molnet) kan du, i IAM, bevilja minst rollen **läsare** för att låta Data Factory identifiera regionen för data Lake Store. Om du vill undvika den här IAM-rollen kan du uttryckligen [skapa en Azure integration runtime](create-azure-integration-runtime.md#create-azure-ir) med platsen för data Lake Store. Om din Data Lake Store till exempel är i Västeuropa, skapar du en Azure integration runtime med plats inställt på "Västeuropa". Koppla dem till den länkade tjänsten Data Lake Store så som visas i följande exempel.

>[!NOTE]
>Om du vill visa en lista över mappar som börjar från roten måste du ange behörighet för tjänstens huvud namn till **på rotnivå med behörigheten "kör"** . Detta gäller när du använder:
>- **Verktyget kopiera data** till redigerare för att skapa kopia.
>- **Data Factory UI** för att testa anslutningen och navigera i mappar under redigeringen.
>Om du har problem med att bevilja behörighet på rotnivå, under redigering, hoppa över test anslutning och ange en paraent-sökväg med behörighet som beviljats väljer du att bläddra från den angivna sökvägen. Kopierings aktiviteten fungerar så länge som tjänstens huvud namn beviljas med rätt behörighet till de filer som ska kopieras.

Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| servicePrincipalId | Ange programmets klient-ID. | Ja |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som `SecureString` en för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
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

Använda hanterade identiteter för Azure-resurser autentisering:

1. [Hämta Data Factory-hanterad identitets information](data-factory-service-identity.md#retrieve-managed-identity) genom att kopiera värdet för "tjänst identitetens program-ID" som genererats tillsammans med din fabrik.
2. Ge åtkomst till hanterad identitet till Data Lake Store, på samma sätt som du gör för tjänstens huvud namn, efter dessa kommentarer.

>[!IMPORTANT]
> Kontrol lera att du ger rätt behörighet för Data Factory-hanterad identitet i Data Lake Store:
>- **Som källa**: I **Data Explorer** > -**åtkomst**ger du minst **Läs** -och kör behörighet att visa och kopiera filerna i mappar och undermappar. Du kan också bevilja **Läs** behörighet för att kopiera en enskild fil. Du kan välja att lägga till i **den här mappen och alla underordnade objekt** för rekursivt, och lägga till som **åtkomst behörighet och en standard behörighets post**. Det finns inget krav på konto nivå åtkomst kontroll (IAM).
>- **Som mottagare**: I **Data Explorer** > -**åtkomst**beviljar du minst **Skriv + kör** -behörighet för att skapa underordnade objekt i mappen. Du kan välja att lägga till i **den här mappen och alla underordnade objekt** för rekursivt, och lägga till som **åtkomst behörighet och en standard behörighets post**. Om du använder en Azure integration runtime för att kopiera (både källa och mottagare finns i molnet) kan du, i IAM, bevilja minst rollen **läsare** för att låta Data Factory identifiera regionen för data Lake Store. Om du vill undvika den här IAM-rollen kan du uttryckligen [skapa en Azure integration runtime](create-azure-integration-runtime.md#create-azure-ir) med platsen för data Lake Store. Koppla dem till den länkade tjänsten Data Lake Store så som visas i följande exempel.

>[!NOTE]
>Om du vill visa en lista över mappar som börjar från roten måste du ange behörighet för den hanterade identiteten som beviljats till **rot nivå med behörighet att köra**. Detta gäller när du använder:
>- **Verktyget kopiera data** till redigerare för att skapa kopia.
>- **Data Factory UI** för att testa anslutningen och navigera i mappar under redigeringen.
>Om du har problem med att bevilja behörighet på rotnivå, under redigering, hoppa över test anslutning och ange en överordnad sökväg med behörighet som beviljats väljer du att bläddra från den angivna sökvägen. Kopierings aktiviteten fungerar så länge som tjänstens huvud namn beviljas med rätt behörighet till de filer som ska kopieras.

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

- För **Parquet, avgränsad text, Avro och binärt format**, se avsnittet [Parquet, delimited text, Avro och Binary format data uppsättning](#format-based-dataset) .
- Andra format som **Orc/JSON-format**finns i avsnittet [annan format data uppsättning](#other-format-dataset) .

### <a name="format-based-dataset"></a>Data uppsättning för Parquet, avgränsad text, Avro och binärt format

Om du vill kopiera data till och från **Parquet, avgränsad text, Avro eller binärt format**, se [Parquet format](format-parquet.md), [avgränsat text format](format-delimited-text.md), [Avro format](format-avro.md) och [binära format](format-binary.md) -artikel på format-baserad data uppsättning och inställningar som stöds.
Följande egenskaper stöds för Azure Data Lake Store gen1 under `location` inställningar i den formatbaserade data uppsättningen:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Egenskapen Type under `location` i data uppsättningen måste anges till **AzureDataLakeStoreLocation**. | Ja      |
| folderPath | Sökvägen till en mapp. Om du vill använda ett jokertecken för att filtrera mappar, hoppar du över den här inställningen och anger den i aktivitets källans inställningar. | Nej       |
| fileName   | Fil namnet under den aktuella folderPath. Om du vill använda ett jokertecken för att filtrera filer, hoppar du över den här inställningen och anger den i aktivitets källans inställningar. | Nej       |

> [!NOTE]
>
> Data uppsättningen för **AzureDataLakeStoreFile** -typen med Parquet eller text formatet som nämns i följande avsnitt stöds fortfarande för kopierings-, söknings-och getMetaData-aktivitet för bakåtkompatibilitet. Men det fungerar inte med funktionen mappa data flöde. Vi rekommenderar att du använder den här nya modellen som går framåt. Användar gränssnittet för Data Factory redigering genererar dessa nya typer.

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

### <a name="other-format-dataset"></a>Data uppsättning för andra format

Följande egenskaper stöds för att kopiera data till och från Azure Data Lake Store gen1 i **Orc/JSON-format**:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Data uppsättningens typ-egenskap måste anges till **AzureDataLakeStoreFile**. |Ja |
| folderPath | Sökväg till mappen i Data Lake Store. Om den inte anges som den pekar till roten. <br/><br/>Wildcard-filtret stöds. Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Används `^` för att kringgå om det faktiska mappnamnet har ett jokertecken eller detta escape-tecken inuti. <br/><br/>Till exempel: RootFolder/undermapp/. Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). |Nej |
| fileName | Namn eller Wildcard-filter för filerna under den angivna "folderPath". Om du inte anger ett värde för den här egenskapen datauppsättningen pekar på alla filer i mappen. <br/><br/>För filter är `*` tillåtna jokertecken (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken).<br/>– Exempel 1: `"fileName": "*.csv"`<br/>– Exempel 2: `"fileName": "???20180427.txt"`<br/>Används `^` för att kringgå om det faktiska fil namnet har ett jokertecken eller detta escape-tecken inuti.<br/><br/>När fil namnet inte har angetts för en data uppsättning för utdata och **preserveHierarchy** inte har angetts i aktivitets mottagaren genererar kopierings aktiviteten automatiskt fil namnet med följande mönster: "*Data. [GUID för aktivitets körnings-ID]. [GUID om FlattenHierarchy]. [format om det är konfigurerat]. [komprimering om konfigurerad]* ", till exempel" data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz ". Om du kopierar från en tabell källa med ett tabell namn i stället för en fråga, är namn mönstret " *[tabell namn]. [ format]. [komprimering om konfigurerad]* ", till exempel" Table. csv ". |Nej |
| modifiedDatetimeStart | Filter för filer baserat på det senast ändrade attributet. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Den övergripande prestandan för data förflyttning påverkas om du aktiverar den här inställningen när du vill göra fil filter till stora mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget attribut filter används för data uppsättningen. När `modifiedDatetimeStart` har ett datetime-värde `modifiedDatetimeEnd` men är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime är markerade. När `modifiedDatetimeEnd` har ett datetime-värde `modifiedDatetimeStart` men är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime är markerat.| Nej |
| modifiedDatetimeEnd | Filter för filer baserat på det senast ändrade attributet. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Den övergripande prestandan för data förflyttning påverkas om du aktiverar den här inställningen när du vill göra fil filter till stora mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget attribut filter används för data uppsättningen. När `modifiedDatetimeStart` har ett datetime-värde `modifiedDatetimeEnd` men är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime är markerade. När `modifiedDatetimeEnd` har ett datetime-värde `modifiedDatetimeStart` men är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime är markerat.| Nej |
| format | Om du vill kopiera filer som är mellan filbaserade arkiv (binär kopia) hoppar du över avsnittet format i definitionerna för både indata och utdata.<br/><br/>Om du vill parsa eller generera filer med ett speciellt format stöds följande fil format typer: Text **Forms**, **JsonFormat**, **AvroFormat**, **OrcFormat**och **ParquetFormat**. Ange den **typ** egenskapen under **format** till någon av dessa värden. Mer information finns i den [textformat](supported-file-formats-and-compression-codecs.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [Orc-format](supported-file-formats-and-compression-codecs.md#orc-format), och [Parquet-format ](supported-file-formats-and-compression-codecs.md#parquet-format) avsnitt. |Nej (endast för binär kopia scenario) |
| compression | Ange typ och komprimeringsnivå för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Stöds nivåer **Optimal** och **snabbast**. |Nej |


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

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [pipelines](concepts-pipelines-activities.md). Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Data Lake Store källa och mottagare.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store som källa

- Om du vill kopiera från **Parquet, avgränsad text, Avro och binärt format**, se avsnittet [Parquet, avgränsad text, Avro och binärt format](#format-based-source) .
- Om du vill kopiera från andra format som **Orc/JSON-format**, se avsnittet [annan format källa](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, avgränsad text, Avro och binär format källa

Om du vill kopiera data från **Parquet, avgränsad text, Avro eller binärt format**, se [Parquet format](format-parquet.md), [avgränsat text format](format-delimited-text.md), [Avro format](format-avro.md) och [binärt format](format-binary.md) artikel med format-baserad kopierings aktivitets källa och inställningar som stöds .  Följande egenskaper stöds för Azure Data Lake Store gen1 under `storeSettings` inställningar i den formatbaserade kopierings källan:

| Egenskap                 | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Typ egenskapen under `storeSettings` måste anges till **AzureDataLakeStoreReadSetting**. | Ja                                           |
| recursive                | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. När rekursivt är inställt på True och sinken är en filbaserad lagring, kopieras inte en tom mapp eller undermapp till mottagaren. Tillåtna värden är **SANT** (standard) och **FALSKT**. | Nej                                            |
| wildcardFolderPath       | Mappsökvägen med jokertecken för att filtrera källmappen. <br>Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Används `^` för att kringgå om det faktiska mappnamnet har ett jokertecken eller detta escape-tecken inuti. <br>Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Nej                                            |
| wildcardFileName         | Fil namnet med jokertecken under den aktuella folderPath/wildcardFolderPath för att filtrera källfiler. <br>Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Används `^` för att kringgå om det faktiska mappnamnet har ett jokertecken eller detta escape-tecken inuti. Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Ja om `fileName` inte anges i data uppsättningen |
| modifiedDatetimeStart    | Filter för filer baserat på det senast ändrade attributet. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br> Egenskaperna kan vara NULL, vilket innebär att inget attribut filter används för data uppsättningen. När `modifiedDatetimeStart` har ett datetime-värde `modifiedDatetimeEnd` men är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime är markerade. När `modifiedDatetimeEnd` har ett datetime-värde `modifiedDatetimeStart` men är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime är markerat. | Nej                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Nej                                            |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till lagrings lager samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej                                            |

> [!NOTE]
> För Parquet eller avgränsat text format har **AzureDataLakeStoreSource** typ kopierings aktivitet som anges i följande avsnitt fortfarande stöd för bakåtkompatibilitet. Vi rekommenderar att du använder den här nya modellen som går framåt. Användar gränssnittet för Data Factory redigering genererar dessa nya typer.

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

#### <a name="other-format-source"></a>Annan format källa

Om du vill kopiera data från Azure Data Lake Store gen1 i **Orc-eller JSON-format**, stöds följande egenskaper i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen för kopierings aktivitets källan måste anges till AzureDataLakeStoreSource. `type` |Ja |
| recursive | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. Om `recursive` är inställt på sant och mottagaren är en filbaserad lagring, kopieras eller skapas inte en tom mapp eller undermapp till mottagaren. Tillåtna värden är **SANT** (standard) och **FALSKT**. | Nej |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till data lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej |

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

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store som mottagare

- Om du vill kopiera till **Parquet, avgränsad text, Avro eller binärformat**, se avsnittet [Parquet, avgränsad text, Avro och binärt format](#format-based-sink) .
- Om du vill kopiera till andra format som **Orc/JSON-format**, se avsnittet [annat format mottagare](#other-format-sink) .

#### <a name="format-based-sink"></a>Parquet, avgränsad text, Avro och binärt format mottagare

Om du vill kopiera data till **Parquet, avgränsad text, Avro eller binärt format**, se [Parquet format](format-parquet.md), [avgränsat text format](format-delimited-text.md), [Avro format](format-avro.md) och [binära format](format-binary.md) -artikel i den formatbaserade kopierings aktivitets mottagare och inställningar som stöds.  Följande egenskaper stöds för Azure Data Lake Store gen1 under `storeSettings` inställningar i den formatbaserade kopierings mottagaren:

| Egenskap                 | Beskrivning                                                  | Krävs |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Typ egenskapen under `storeSettings` måste anges till **AzureDataLakeStoreWriteSetting**. | Ja      |
| copyBehavior             | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: Bevarar filens hierarki i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><b>-FlattenHierarchy</b>: Alla filer från källmappen är på den första nivån i målmappen. Målfiler har automatiskt genererade unika namn. <br/><b>-MergeFiles</b>: Sammanfogar alla filer från källmappen till en fil. Om filnamnet har angetts är namnet på sammanfogade filen det angivna namnet. Annars är det en automatiskt skapade filnamnet. | Nej       |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till data lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej       |

> [!NOTE]
> För Parquet eller avgränsat text format har **AzureDataLakeStoreSink** typ kopierings aktivitet som anges i följande avsnitt fortfarande stöd för bakåtkompatibilitet. Vi rekommenderar att du använder den här nya modellen som går framåt. Användar gränssnittet för Data Factory redigering genererar dessa nya typer.

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

#### <a name="other-format-sink"></a>Övrigt format mottagare

För att kopiera data till Azure Data Lake Store gen1 i **Orc-eller JSON-format**stöds följande egenskaper i avsnittet **mottagare** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen för kopierings aktivitetens Sink måste anges till **AzureDataLakeStoreSink.** `type` |Ja |
| copyBehavior | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: Bevarar filens hierarki i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><b>-FlattenHierarchy</b>: Alla filer från källmappen är på den första nivån i målmappen. Målfiler har automatiskt genererade unika namn. <br/><b>-MergeFiles</b>: Sammanfogar alla filer från källmappen till en fil. Om filnamnet har angetts är namnet på sammanfogade filen det angivna namnet. Annars genereras fil namnet automatiskt. | Nej |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till data lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | Nej |

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

### <a name="folder-and-file-filter-examples"></a>Exempel på mapp-och fil filter

I det här avsnittet beskrivs det resulterande beteendet hos mappsökvägen och fil namnet med filter för jokertecken.

| folderPath | fileName | recursive | Källans mappstruktur och filter resultat (filer i **fetstil** hämtas)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Tom, Använd standard) | false | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | (Tom, Använd standard) | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | false | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Exempel på hur kopierings åtgärden fungerar

I det här avsnittet beskrivs det resulterande beteendet för kopierings åtgärden `recursive` för `copyBehavior` olika kombinationer av värden och värden.

| recursive | copyBehavior | Mappstruktur för källa | Resulterande mål |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Mål-Mapp1 skapas med samma struktur som källan:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt skapade namn på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + Fil2 + File3 + File4 + File5-innehåll sammanfogas till en fil med ett automatiskt genererat fil namn. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt skapade namn på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + Fil2-innehåll sammanfogas till en fil med automatiskt genererat fil namn. automatiskt skapade namn på File1<br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Bevara ACL: er till Data Lake Storage Gen2

Om du vill replikera åtkomst kontrol listorna (ACL) tillsammans med datafiler när du uppgraderar från Data Lake Storage Gen1 till Data Lake Storage Gen2, se [bevara ACL: er från data Lake Storage gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1).

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

Lär dig mer om omvandling av [käll omvandling](data-flow-source.md) och [mottagare](data-flow-sink.md) i funktionen mappa data flöde.

## <a name="next-steps"></a>Nästa steg

En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
