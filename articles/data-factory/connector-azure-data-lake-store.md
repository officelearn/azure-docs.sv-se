---
title: Kopiera data till och från Azure Data Lake Storage Gen1 med Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från datalager som stöds till Azure Data Lake Store (eller) från Data Lake Store till mottagarens datalager med Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: jingwang
ms.openlocfilehash: 932ddf5f377556e815205b7ad34a735815317ac0
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249051"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Kopiera data till och från Azure Data Lake Storage Gen1 med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-azure-datalake-connector.md)
> * [Aktuell version](connector-azure-data-lake-store.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data till och från Azure Data Lake Storage Gen1 (kallades tidigare Azure Data Lake Store). Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från alla dataarkiv till Azure Data Lake Store eller kopiera data från Azure Data Lake Store till alla mottagarens datalager. En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Azure Data Lake Store-anslutningen:

- Kopiering av filer med hjälp av **tjänstens huvudnamn** eller **hanterade identiteter för Azure-resurser** autentisering.
- Kopiera filer som-är eller parsning/genererar filer med den [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Om du kopierar data med hjälp av lokal Integration Runtime kan du konfigurera företagets brandvägg för att tillåta utgående trafik till `<ADLS account name>.azuredatalakestore.net` och `login.microsoftonline.com/<tenant>/oauth2/token` på port 443. Denna är Azure Security säkerhetstokentjänst (STS) som IR behöver kommunicera med för att hämta åtkomsttoken.

## <a name="get-started"></a>Kom igång

> [!TIP]
> En genomgång av att använda Azure Data Lake Store-anslutningen finns i [läser in data i Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

>[!NOTE]
>När du använder verktyget för att kopiera Data för att skapa kopiering av pipeline eller Använd ADF UI för att utföra belastningstester anslutning/navigera mappar under redigering, kräver behörigheten för tjänstens huvudnamn eller MSI beviljas på rotnivå. Tag, kan kopiera körningsmiljön för aktiviteten fungera så länge som beviljats behörighet till data som ska kopieras. Du kan hoppa över redigering åtgärder om du behöver begränsa behörigheten.

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Azure Data lake Store.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Azure Data Lake Store-länkade tjänst:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till **AzureDataLakeStore**. | Ja |
| dataLakeStoreUri | Information om Azure Data Lake Store-konto. Den här informationen antar ett av följande format: `https://[accountname].azuredatalakestore.net/webhdfs/v1` eller `adl://[accountname].azuredatalakestore.net/`. | Ja |
| subscriptionId | Azure-prenumerations-ID som Data Lake Store-kontot tillhör. | Krävs för mottagare |
| resourceGroupName | Azure resursgruppens namn som det Data Lake Store-kontot tillhör. | Krävs för mottagare |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller lokal Integration Runtime (om ditt datalager finns i privat nätverk). Om den inte anges används standard Azure Integration Runtime. |Nej |

Se följande avsnitt om fler egenskaper och JSON-exempel för olika typer av autentisering respektive:

- [Med hjälp av autentisering av tjänstens huvudnamn](#using-service-principal-authentication)
- [Med hjälp av hanterade identiteter för autentisering av Azure-resurser](#using-managed-service-identity-authentication)

### <a name="using-service-principal-authentication"></a>Med hjälp av autentisering av tjänstens huvudnamn

Registrera en entitet för program i Azure Active Directory (Azure AD) för att använda autentisering av tjänstens huvudnamn, och ge det åtkomst till Data Lake Store. Detaljerade anvisningar finns i [tjänst-till-tjänst-autentisering](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

- Program-ID:t
- Programnyckel
- Klient-ID:t

>[!IMPORTANT]
> Kontrollera att du ge tjänstens huvudnamn rätt behörighet i Azure Data Lake Store:
>- **Som källa**, i Data explorer -> åtkomst, ge minst **Läs + kör** behörighet att visa och kopiera filerna i mappen/undermappar, eller **Läs** tillstånd att kopiera en fil, och väljer att Lägg till i **den här mappen och alla underordnade** för rekursiv och Lägg till som **behörigheten och en standardbehörighetsinlägg**. Inga krav på kontot på åtkomstkontroll (IAM).
>- **Som mottagare**, i Data explorer -> åtkomst, ge minst **skriva + köra** behörighet att skapa underordnade objekt i mappen och välja att lägga till **den här mappen och alla underordnade** för rekursiv och Lägg till som **behörigheten och en standardbehörighetsinlägg**. Om du använder Azure IR för att kopiera (både källa och mottagare finns i molnet), i åtkomstkontroll (IAM), beviljar minst **läsare** roll för att identifiera Data Lake Store-region med Data Factory. Om du vill undvika den här IAM-rollen uttryckligen [skapa en Azure IR](create-azure-integration-runtime.md#create-azure-ir) med platsen för ditt Data Lake Store och associera i Data Lake Store-länkade tjänst som i följande exempel.

Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| servicePrincipalId | Ange programmets klient-ID. | Ja |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| klient | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Du kan hämta den håller musen i det övre högra hörnet i Azure Portal. | Ja |

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

### <a name="managed-identity"></a> Med hjälp av hanterade identiteter för autentisering av Azure-resurser

En data factory kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md), som representerar den här specifika data factory. Du kan använda den här tjänstidentitet direkt för Data Lake Store-autentisering är ungefär som att använda tjänstens huvudnamn. Det gör den här avsedda factory ska kunna komma åt och kopiera data från/till ditt Data Lake Store.

Använda hanterade identiteter för Azure-resurser autentisering:

1. [Hämta tjänstidentitet för datafabrik](data-factory-service-identity.md#retrieve-service-identity) genom att kopiera värdet för ”SERVICE IDENTITETSPROGRAM-ID” genererade tillsammans med din datafabrik.
2. Ge tjänstens identitet åtkomst till Data Lake Store på samma sätt som du gör med tjänstens huvudnamn följande kommentarerna nedan.

>[!IMPORTANT]
> Kontrollera att du ger data factory-tjänsten identitet rätt behörighet i Azure Data Lake Store:
>- **Som källa**, i Data explorer -> åtkomst, ge minst **Läs + kör** behörighet att visa och kopiera filerna i mappen/undermappar, eller **Läs** tillstånd att kopiera en fil, och väljer att Lägg till i **den här mappen och alla underordnade** för rekursiv och Lägg till som **behörigheten och en standardbehörighetsinlägg**. Inga krav på kontot på åtkomstkontroll (IAM).
>- **Som mottagare**, i Data explorer -> åtkomst, ge minst **skriva + köra** behörighet att skapa underordnade objekt i mappen och välja att lägga till **den här mappen och alla underordnade** för rekursiv och Lägg till som **behörigheten och en standardbehörighetsinlägg**. Om du använder Azure IR för att kopiera (både källa och mottagare finns i molnet), i åtkomstkontroll (IAM), beviljar minst **läsare** roll för att identifiera Data Lake Store-region med Data Factory. Om du vill undvika den här IAM-rollen uttryckligen [skapa en Azure IR](create-azure-integration-runtime.md#create-azure-ir) med platsen för ditt Data Lake Store och associera i Data Lake Store-länkade tjänst som i följande exempel.

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Data Lake Store-datauppsättningen.

För att kopiera data till och från Azure Data Lake Store, ange typegenskapen på datauppsättningen till **AzureDataLakeStoreFile**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till: **AzureDataLakeStoreFile** |Ja |
| folderPath | Sökvägen till mappen i Data Lake Store. Jokerteckenfilter stöds inte. Exempel: rootfolder/undermappen / |Ja |
| fileName | **Namn eller jokertecken-filtret** för den eller filerna under den angivna ”folderPath”. Om du inte anger ett värde för den här egenskapen datauppsättningen pekar på alla filer i mappen. <br/><br/>För filter tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken).<br/>– Exempel 1: `"fileName": "*.csv"`<br/>– Exempel 2: `"fileName": "???20180427.txt"`<br/>Använd `^` att undvika om din faktiska filnamnet har jokertecken eller den här escape-tecken i.<br/><br/>Om filnamnet har inte angetts för en utdatauppsättning och **preserveHierarchy** inte har angetts i aktiviteten-mottagare kopieringsaktiviteten genererar automatiskt filnamnet med följande format ”:*Data. [ aktivitetskörning id GUID]. [GUID om FlattenHierarchy]. [format om konfigurerat]. [komprimering om konfigurerat]* ". Ett exempel är ”Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz”. |Nej |
| Format | Om du vill **kopiera filer som – är** hoppa över avsnittet format i både inkommande och utgående datamängd definitioner mellan filbaserade (binär kopia).<br/><br/>Om du vill parsa eller generera filer med ett visst format format för följande filtyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typ** egenskapen under format till ett av dessa värden. Mer information finns i [textformat](supported-file-formats-and-compression-codecs.md#text-format), [Json-Format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [Orc-Format](supported-file-formats-and-compression-codecs.md#orc-format), och [Parquet-Format](supported-file-formats-and-compression-codecs.md#parquet-format) avsnitt. |Nej (endast för binär kopia scenario) |
| Komprimering | Ange typ och komprimeringsnivå för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Nivåer som stöds är: **Optimal** och **snabbast**. |Nej |

>[!TIP]
>Kopiera alla filer i en mapp genom att ange **folderPath** endast.<br>Om du vill kopiera en enstaka fil med ett givet namn, ange **folderPath** med mappdelen och **fileName** med filnamnet.<br>Om du vill kopiera en delmängd av filerna under en mapp, anger **folderPath** med mappdelen och **fileName** med jokertecken-filtret. 

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Data Lake källa och mottagare.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store som källa

För att kopiera data från Azure Data Lake Store, ange typ av datakälla i kopieringsaktiviteten till **AzureDataLakeStoreSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **AzureDataLakeStoreSource** |Ja |
| rekursiv | Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. Obs när rekursiv har angetts till true och mottagare är filbaserade store, tom mapp/underanvändningsfall-folder kan inte kopieras/skapas vid mottagare.<br/>Tillåtna värden är: **SANT** (standard), **FALSKT** | Nej |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS input dataset name>",
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

Om du vill kopiera data till Azure Data Lake Store, ange Mottagartyp i kopieringsaktiviteten till **AzureDataLakeStoreSink**. Följande egenskaper stöds i den **mottagare** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type kopiera aktivitet komprimeringstyp måste anges till: **AzureDataLakeStoreSink** |Ja |
| copyBehavior | Definierar kopieringsbeteendet när källan är filer från filbaserat datalager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: bevarar filen hierarkin i målmappen. Den relativa sökvägen för källfilen för källmappen är identisk med den relativa sökvägen för målfilen till målmappen.<br/><b>-FlattenHierarchy</b>: alla filer från källmappen finns i den första nivån i målmappen. Målfiler har automatiskt genererad namn. <br/><b>-MergeFiles</b>: slår samman alla filer från källmappen till en fil. Om namnet på filen/blobben har angetts, blir namnet på sammanfogade filen det angivna namnet; annars skulle vara automatiskt genererade filnamn. | Nej |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS output dataset name>",
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

### <a name="recursive-and-copybehavior-examples"></a>rekursiva och copyBehavior exempel

Det här avsnittet beskrivs kopieringsåtgärden för olika kombinationer av värden för rekursiv och copyBehavior resulterande beteende.

| rekursiv | copyBehavior | Mappstruktur för källa | Resulterande mål |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | målmappen Mapp1 skapas med samma struktur som källa:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatiskt genererade namn på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File5 |
| true |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 + fil3 + File4 + filen 5 innehållet slås samman i en fil med autogenererade filnamn |
| false |preserveHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målmappen Mapp1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/><br/>Subfolder1 med fil3, File4 och File5 plockas inte upp. |
| false |flattenHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målmappen Mapp1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatiskt genererade namn på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/><br/>Subfolder1 med fil3, File4 och File5 plockas inte upp. |
| false |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målmappen Mapp1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 innehållet slås samman i en fil med automatiskt genererade namnet. Automatiskt genererade namn på File1<br/><br/>Subfolder1 med fil3, File4 och File5 plockas inte upp. |

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
