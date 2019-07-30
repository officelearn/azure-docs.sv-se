---
title: Kopiera data till och från Azure Data Lake Storage Gen1 med Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från datalager som stöds till Azure Data Lake Store eller från Data Lake Store till mottagarens datalager med Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: df88c3e2e07165182c917eaf30a5f37451fbd073
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509578"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Kopiera data till och från Azure Data Lake Storage Gen1 med Azure Data Factory
> [!div class="op_single_selector" title1="Välj versionen av Azure Data Factory som du använder:"]
> * [Version 1](v1/data-factory-azure-datalake-connector.md)
> * [Aktuell version](connector-azure-data-lake-store.md)

Den här artikeln beskrivs hur du kopierar data till och från Azure Data Lake Storage Gen1. Läs om Azure Data Factory den [introduktionsartikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här anslutningen för Azure Data Lake Storage Gen1 stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [källan eller mottagaren matris som stöds](copy-activity-overview.md)
- [Mappning av dataflöde](concepts-data-flow-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktiviteten](control-flow-get-metadata-activity.md)

Med den här anslutningen kan du mer specifikt:

- Kopiera filer med någon av följande metoder för autentisering: tjänsten huvudnamn eller hanterade identiteter för Azure-resurser.
- Kopiera filer som är eller parsa eller generera filer med den [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Om du kopierar data med hjälp av lokal integration runtime kan du konfigurera företagets brandvägg för att tillåta utgående trafik till `<ADLS account name>.azuredatalakestore.net` och `login.microsoftonline.com/<tenant>/oauth2/token` på port 443. Dessa är Azure säkerhetstokentjänsten som integration runtime behöver kommunicera med för att hämta åtkomsttoken.

## <a name="get-started"></a>Kom igång

> [!TIP]
> En genomgång av hur du använder Azure Data Lake Store-anslutningen finns i [läser in data i Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Azure Data Lake Store.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Azure Data Lake Store-länkade tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Den `type` egenskapen måste anges till **AzureDataLakeStore**. | Ja |
| dataLakeStoreUri | Information om Azure Data Lake Store-konto. Den här informationen antar ett av följande format: `https://[accountname].azuredatalakestore.net/webhdfs/v1` eller `adl://[accountname].azuredatalakestore.net/`. | Ja |
| subscriptionId | Azure-prenumerations-ID som Data Lake Store-kontot tillhör. | Krävs för mottagare |
| resourceGroupName | Azure resursgruppens namn som det Data Lake Store-kontot tillhör. | Krävs för mottagare |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure integration runtime eller en lokal integration runtime om ditt datalager finns i ett privat nätverk. Om egenskapen inte anges används standard Azure integration runtime. |Nej |

### <a name="use-service-principal-authentication"></a>Använd autentisering av tjänstens huvudnamn

Registrera en entitet för program i Azure Active Directory för att använda autentisering av tjänstens huvudnamn, och ge det åtkomst till Data Lake Store. Detaljerade anvisningar finns i [tjänst-till-tjänst-autentisering](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

- Program-ID:t
- Programnyckel
- Klient-ID:t

>[!IMPORTANT]
> Ge tjänstens huvudnamn rätt behörighet i Data Lake Store:
>- **Som källa**: I **datautforskaren** > **åtkomst**, ge minst **Läs + kör** behörighet att visa och kopiera filer i mappar och undermappar. Eller, du kan bevilja **Läs** tillstånd att kopiera en fil. Du kan välja att lägga till i **den här mappen och alla underordnade** för rekursiv, och Lägg till som **behörigheten och en standardbehörighetsinlägg**. Det finns inga krav på kontonivå åtkomstkontroll (IAM).
>- **Som mottagare**: I **datautforskaren** > **åtkomst**, ge minst **skriva + köra** behörighet att skapa underordnade objekt i mappen. Du kan välja att lägga till i **den här mappen och alla underordnade** för rekursiv, och Lägg till som **behörigheten och en standardbehörighetsinlägg**. Om du använder en Azure integration runtime för att kopiera (både källa och mottagare finns i molnet), i IAM, beviljar minst **läsare** roll för att kunna identifiera regionen för Data Lake Store med Data Factory. Om du vill undvika den här IAM-rollen uttryckligen [skapa en Azure integration runtime](create-azure-integration-runtime.md#create-azure-ir) med platsen för Data Lake Store. Till exempel om ditt Data Lake Store finns i Europa, västra, skapa en Azure integration runtime med platsen angetts till ”Europa, västra”. Koppla dem i Data Lake Store-länkade tjänsten som du ser i följande exempel.

>[!NOTE]
>Listan mappar börja från roten, måste du ange behörigheten för tjänstens huvudnamn som beviljas till **på rotnivå med ”kör” behörighet**. Detta gäller när du använder den:
>- **Kopiera data verktyget** att författaren kopiering av pipeline.
>- **Användargränssnittet för data Factory** att testa anslutningen och navigera mappar under redigering.
>Om du har frågor om hur du beviljar behörighet på rotnivå, under redigering, hoppar du över anslutning och indata paraent aktivitet med behörighet som beviljas sedan välja att bläddra från som angetts av sökvägen. Kopiera aktivitet fungerar så länge tjänstens huvudnamn har beviljats med rätt behörighet på filerna som ska kopieras.

Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| servicePrincipalId | Ange programmets klient-ID. | Ja |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en `SecureString` ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | Ange klientinformation, till exempel domännamnet eller klient-ID, där programmet finns. Du kan hämta den håller musen i det övre högra hörnet i Azure Portal. | Ja |

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

### <a name="managed-identity"></a> Använda hanterade identiteter för autentisering för Azure-resurser

En data factory kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md), som representerar den här specifika data factory. Du kan använda den här hanterade identiteten direkt för Data Lake Store-autentisering, ungefär som att använda tjänstens huvudnamn. Det gör den här avsedda fabriken kan komma åt och kopiera data till eller från Data Lake Store.

Använda hanterade identiteter för Azure-resurser autentisering:

1. [Hämta ID-information för data factory hanteras](data-factory-service-identity.md#retrieve-managed-identity) genom att kopiera värdet för ”Service Identity program-ID” genererade tillsammans med din datafabrik.
2. Bevilja hanterad identitet-åtkomst till Data Lake Store på samma sätt som du gör med tjänstens huvudnamn, följa dessa riktlinjer.

>[!IMPORTANT]
> Kontrollera att du ger data factory hanterad identitet rätt behörighet i Data Lake Store:
>- **Som källa**: I **datautforskaren** > **åtkomst**, ge minst **Läs + kör** behörighet att visa och kopiera filer i mappar och undermappar. Eller, du kan bevilja **Läs** tillstånd att kopiera en fil. Du kan välja att lägga till i **den här mappen och alla underordnade** för rekursiv, och Lägg till som **behörigheten och en standardbehörighetsinlägg**. Det finns inga krav på kontonivå åtkomstkontroll (IAM).
>- **Som mottagare**: I **datautforskaren** > **åtkomst**, ge minst **skriva + köra** behörighet att skapa underordnade objekt i mappen. Du kan välja att lägga till i **den här mappen och alla underordnade** för rekursiv, och Lägg till som **behörigheten och en standardbehörighetsinlägg**. Om du använder en Azure integration runtime för att kopiera (både källa och mottagare finns i molnet), i IAM, beviljar minst **läsare** roll för att kunna identifiera regionen för Data Lake Store med Data Factory. Om du vill undvika den här IAM-rollen uttryckligen [skapa en Azure integration runtime](create-azure-integration-runtime.md#create-azure-ir) med platsen för Data Lake Store. Koppla dem i Data Lake Store-länkade tjänsten som du ser i följande exempel.

>[!NOTE]
>Listan mappar börja från roten, måste du ange behörighet för den hanterade identitet som beviljas till **på rotnivå med ”kör” behörighet**. Detta gäller när du använder den:
>- **Kopiera data verktyget** att författaren kopiering av pipeline.
>- **Användargränssnittet för data Factory** att testa anslutningen och navigera mappar under redigering.
>Om du har frågor om hur du beviljar behörighet på rotnivå, under redigering, hoppar du över anslutning och indata som en överordnad sökväg med behörighet som beviljas sedan välja att bläddra från som angetts av sökvägen. Kopiera aktivitet fungerar så länge tjänstens huvudnamn har beviljats med rätt behörighet på filerna som ska kopieras.

I Azure Data Factory behöver du inte ange några egenskaper förutom den allmänna Data Lake Store-informationen i den länkade tjänsten.

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

- Parquet och avgränsat textformat finns i den [Parquet och avgränsad text formatera datauppsättning](#parquet-and-delimited-text-format-dataset) avsnittet.
- Andra format som ORC, Avro, JSON eller binärt format, finns det [andra format datauppsättning](#other-format-dataset) avsnittet.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet och avgränsad text format datauppsättning

För att kopiera data till och från Azure Data Lake Store Gen1 i parquet eller avgränsat textformat, se den [Parquet-format](format-parquet.md) och [avgränsat textformat](format-delimited-text.md) artiklar om format-baserade datauppsättning och inställningar som stöds. Följande egenskaper har stöd för Azure Data Lake Store Gen1 under `location` inställningar i datauppsättningen format-baserade:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Egenskapen type under `location` i datauppsättningen måste anges till **AzureDataLakeStoreLocation**. | Ja      |
| folderPath | Sökvägen till en mapp. Om du vill använda ett jokertecken för att filtrera mappar, hoppa över den här inställningen och anger den i inställningarna för kopieringsaktiviteten källa. | Nej       |
| fileName   | Filnamnet under den angivna folderPath. Om du vill använda ett jokertecken för att filtrera filer, hoppa över den här inställningen och anger den i inställningarna för kopieringsaktiviteten källa. | Nej       |

> [!NOTE]
>
> Den **AzureDataLakeStoreFile** typ datauppsättning med parquet- eller text-format som anges i följande avsnitt stöds fortfarande som kopiera, sökning och GetMetadata-aktiviteten för bakåtkompatibilitet. Men det fungerar inte med i flow-funktionen för mappning av data. Vi rekommenderar att du använder den nya modellen framöver. Data Factory redigering Användargränssnittet genererar dessa nya typer.

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

### <a name="other-format-dataset"></a>Andra format-datauppsättning

För att kopiera data till och från Azure Data Lake Store Gen1 i ORC-, Avro-, JSON- eller binärt format, stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för datauppsättningen måste anges till **AzureDataLakeStoreFile**. |Ja |
| folderPath | Sökvägen till mappen i Data Lake Store. Om den inte anges som den pekar till roten. <br/><br/>Jokertecken-filtret stöds. Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken). Använd `^` att undvika om din faktiska mappnamn har ett jokertecken eller den här escape-tecken i. <br/><br/>Till exempel: rootfolder/undermappen /. Se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). |Nej |
| fileName | Namn eller jokertecken filter för filerna under den angivna ”folderPath”. Om du inte anger ett värde för den här egenskapen datauppsättningen pekar på alla filer i mappen. <br/><br/>För filter jokertecken tillåts är `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken).<br/>– Exempel 1: `"fileName": "*.csv"`<br/>– Exempel 2: `"fileName": "???20180427.txt"`<br/>Använd `^` att undvika om din faktiska filnamnet har ett jokertecken eller den här escape-tecken i.<br/><br/>Om filnamnet har inte angetts för en utdatauppsättning och **preserveHierarchy** inte har angetts i aktiviteten-mottagare kopieringsaktiviteten genererar automatiskt filnamnet med följande mönster: ”*Data. [aktivitet köras ID GUID]. [GUID om FlattenHierarchy]. [format om konfigurerat]. [komprimering om konfigurerat]* ”, till exempel” Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz ”. Om du kopierar från en tabular källa med ett tabellnamn i stället för en fråga namnet har formatet är ” *[tabellnamn]. [ format]. [komprimering om konfigurerat]* ”, till exempel” MyTable.csv ”. |Nej |
| modifiedDatetimeStart | Filter för filer baserat på attribut som senast ändrad. Filerna har valts om deras tid för senaste ändring ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br/><br/> Dataförflyttning serverns prestanda påverkas genom att aktivera den här inställningen när du vill filen filter med stora mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inga attributfiltret tillämpas på datauppsättningen. När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senaste ändrade attribut är större än eller lika med datetime-värde har valts. När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är NULL, betyder det filer vars senaste ändrade attributet är mindre än det datum/tid-värdet är markerade.| Nej |
| modifiedDatetimeEnd | Filter för filer baserat på attribut som senast ändrad. Filerna har valts om deras tid för senaste ändring ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br/><br/> Dataförflyttning serverns prestanda påverkas genom att aktivera den här inställningen när du vill filen filter med stora mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inga attributfiltret tillämpas på datauppsättningen. När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senaste ändrade attribut är större än eller lika med datetime-värde har valts. När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är NULL, betyder det filer vars senaste ändrade attributet är mindre än det datum/tid-värdet är markerade.| Nej |
| format | Om du vill kopiera filer som finns mellan filbaserade lager (binär kopia), kan du hoppa över avsnittet format i både inkommande och utgående datamängd definitioner.<br/><br/>Om du vill parsa eller generera filer med ett visst format stöds format för följande filtyper: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, och **ParquetFormat**. Ange den **typ** egenskapen under **format** till någon av dessa värden. Mer information finns i den [textformat](supported-file-formats-and-compression-codecs.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [Orc-format](supported-file-formats-and-compression-codecs.md#orc-format), och [Parquet-format ](supported-file-formats-and-compression-codecs.md#parquet-format) avsnitt. |Nej (endast för binär kopia scenario) |
| compression | Ange typ och komprimeringsnivå för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Stöds nivåer **Optimal** och **snabbast**. |Nej |


>[!TIP]
>Kopiera alla filer i en mapp genom att ange **folderPath** endast.<br>Om du vill kopiera en enstaka fil med ett visst namn, ange **folderPath** med en mappdelen och **fileName** med ett filnamn.<br>Om du vill kopiera en delmängd av filerna under en mapp, anger **folderPath** med en mappdelen och **fileName** med jokertecken-filtret. 

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md). Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Data Lake Store-källa och mottagare.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store som källa

- Om du vill kopiera från parquet eller avgränsat textformat, se den [Parquet och avgränsad text format källa](#parquet-and-delimited-text-format-source) avsnittet.
- Kopiera från andra format som ORC-, Avro-, JSON- eller binärt format genom att se den [annan format källa](#other-format-source) avsnittet.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet och avgränsad text format källa

Om du vill kopiera data från Azure Data Lake Store Gen1 i parquet eller avgränsat textformat, se den [Parquet-format](format-parquet.md) och [avgränsat textformat](format-delimited-text.md) artiklar om format-baserade aktiviteten kopieringskälla och inställningar som stöds. Följande egenskaper har stöd för Azure Data Lake Store Gen1 under `storeSettings` inställningar i formatet-baserade kopieringskälla:

| Egenskap                 | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Egenskapen type under `storeSettings` måste anges till **AzureDataLakeStoreReadSetting**. | Ja                                           |
| recursive                | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. När rekursiv har angetts till true och mottagaren är en filbaserad store, en tom mapp eller undermapp inte kopieras eller skapat i mottagaren. Tillåtna värden är **SANT** (standard) och **FALSKT**. | Nej                                            |
| wildcardFolderPath       | Sökvägen till mappen med jokertecken för att filtrera källa mappar. <br>Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken). Använd `^` att undvika om din faktiska mappnamn har ett jokertecken eller den här escape-tecken i. <br>Se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). | Nej                                            |
| wildcardFileName         | Filnamn med jokertecken under den angivna folderPath/wildcardFolderPath filter källfilerna för. <br>Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken). Använd `^` att undvika om din faktiska mappnamn har ett jokertecken eller den här escape-tecken i. Se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). | Ja om `fileName` inte har angetts i datauppsättningen |
| modifiedDatetimeStart    | Filter för filer baserat på attribut som senast ändrad. Filerna har valts om deras tid för senaste ändring ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br> Egenskaperna kan vara NULL, vilket innebär att inga attributfiltret tillämpas på datauppsättningen. När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senaste ändrade attribut är större än eller lika med datetime-värde har valts. När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är NULL, betyder det filer vars senaste ändrade attributet är mindre än det datum/tid-värdet är markerade. | Nej                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Nej                                            |
| maxConcurrentConnections | Antal anslutningar för att ansluta till storage store samtidigt. Ange bara när du vill begränsa samtidiga anslutningen till datalagret. | Nej                                            |

> [!NOTE]
> För parquet eller avgränsat textformat den **AzureDataLakeStoreSource** typen kopiera aktivitetskälla som nämns i följande avsnitt stöds fortfarande skick för bakåtkompatibilitet. Vi rekommenderar att du använder den nya modellen framöver. Data Factory redigering Användargränssnittet genererar dessa nya typer.

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

#### <a name="other-format-source"></a>Andra format-källa

För att kopiera data från Azure Data Lake Store Gen1 i ORC-, Avro-, JSON- eller binärt format stöds följande egenskaper i kopieringsaktiviteten **källa** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Den `type` egenskapen för aktiviteten kopieringskälla måste anges till **AzureDataLakeStoreSource**. |Ja |
| recursive | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. När `recursive` har angetts till true och mottagaren är en filbaserad store en tom mapp eller undermapp inte kopieras eller skapad mottagaren. Tillåtna värden är **SANT** (standard) och **FALSKT**. | Nej |
| maxConcurrentConnections | Antal anslutningar för att ansluta till datalagret samtidigt. Ange bara när du vill begränsa samtidiga anslutningen till datalagret. | Nej |

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

- Om du vill kopiera till parquet och avgränsat textformat, se den [Parquet och avgränsad text format mottagare](#parquet-and-delimited-text-format-sink) avsnittet.
- Kopiera till andra format som ORC-, Avro-, JSON- eller binärt format genom att se den [andra format mottagare](#other-format-sink) avsnittet.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet och avgränsad text format mottagare

Om du vill kopiera data till Azure Data Lake Store Gen1 i parquet eller avgränsat textformat, se den [Parquet-format](format-parquet.md) och [avgränsat textformat](format-delimited-text.md) artiklar om format-baserade kopiera aktivitet mottagare och inställningar som stöds. Följande egenskaper har stöd för Azure Data Lake Store Gen1 under `storeSettings` inställningar i formatet-baserad kopia mottagare:

| Egenskap                 | Beskrivning                                                  | Krävs |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Egenskapen type under `storeSettings` måste anges till **AzureDataLakeStoreWriteSetting**. | Ja      |
| copyBehavior             | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: Bevarar filen hierarkin i målmappen. Den relativa sökvägen på källfilen för målmappen är identisk med den relativa sökvägen till målfilen till målmappen.<br/><b>-FlattenHierarchy</b>: Alla filer från källmappen finns i den första nivån i målmappen. Målfiler har automatiskt genererade unika namn. <br/><b>-MergeFiles</b>: Slår samman alla filer från källmappen till en fil. Om filnamnet har angetts är namnet på sammanfogade filen det angivna namnet. Annars är det en automatiskt skapade filnamnet. | Nej       |
| maxConcurrentConnections | Antal anslutningar för att ansluta till datalagret samtidigt. Ange bara när du vill begränsa samtidiga anslutningen till datalagret. | Nej       |

> [!NOTE]
> För parquet eller avgränsat textformat den **AzureDataLakeStoreSink** typen kopiera aktivitet mottagare som nämns i följande avsnitt stöds fortfarande skick för bakåtkompatibilitet. Vi rekommenderar att du använder den nya modellen framöver. Data Factory redigering Användargränssnittet genererar dessa nya typer.

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

#### <a name="other-format-sink"></a>Andra format-mottagare

Om du vill kopiera data till Azure Data Lake Store Gen1 i ORC-, Avro-, JSON- eller binärt format, stöds följande egenskaper i den **mottagare** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Den `type` egenskapen kopiera aktivitet komprimeringstyp måste anges till **AzureDataLakeStoreSink**. |Ja |
| copyBehavior | Definierar kopieringsbeteendet när källan är filer från ett filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: Bevarar filen hierarkin i målmappen. Den relativa sökvägen på källfilen för målmappen är identisk med den relativa sökvägen till målfilen till målmappen.<br/><b>-FlattenHierarchy</b>: Alla filer från källmappen finns i den första nivån i målmappen. Målfiler har automatiskt genererade unika namn. <br/><b>-MergeFiles</b>: Slår samman alla filer från källmappen till en fil. Om filnamnet har angetts är namnet på sammanfogade filen det angivna namnet. Annars är filnamnet automatiskt genererade. | Nej |
| maxConcurrentConnections | Antal anslutningar för att ansluta till datalagret samtidigt. Ange bara när du vill begränsa samtidiga anslutningen till datalagret. | Nej |

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

### <a name="folder-and-file-filter-examples"></a>Mapp och fil filter-exempel

Det här avsnittet beskriver resultatet av sökvägen och namnet på mappen med jokertecken filter.

| folderPath | fileName | recursive | Käll-mappen struktur och filtrera resultatet (filer i **fetstil** hämtas)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Tom, Använd standard) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Tom, Använd standard) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Exempel på beteendet för kopieringen

Det här avsnittet beskrivs kopieringsåtgärden för olika kombinationer av resulterande beteende `recursive` och `copyBehavior` värden.

| recursive | copyBehavior | Mappstruktur för källa | Resulterande mål |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med samma struktur som källa:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt skapade namn på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 + fil3 + File4 + File5 innehållet slås samman i en fil med ett automatiskt skapade filnamnet. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/><br/>Subfolder1 med fil3, File4 och File5 inte hämtas. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt skapade namn på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/><br/>Subfolder1 med fil3, File4 och File5 inte hämtas. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 innehållet slås samman i en fil med automatiskt skapade filnamnet. automatiskt skapade namn på File1<br/><br/>Subfolder1 med fil3, File4 och File5 inte hämtas. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Bevara ACL: er till Data Lake Storage Gen2

Om du vill replikera den åtkomstkontrollistor (ACL) tillsammans med filer när du uppgraderar från Data Lake Storage Gen1 till Data Lake Storage Gen2, se [bevara ACL: er från Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1).

## <a name="mapping-data-flow-properties"></a>Mappning av egenskaper för flödet av data

Läs mer om [source omvandling](data-flow-source.md) och [mottagare omvandling](data-flow-sink.md) i mappningsfunktionen av flödet.

## <a name="next-steps"></a>Nästa steg

En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
