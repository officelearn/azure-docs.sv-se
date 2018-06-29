---
title: Kopiera data till/från Azure Data Lake Store med hjälp av Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från stöds källa datalager till Azure Data Lake Store (eller) från Data Lake Store stöds sink Arkiv med hjälp av Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: jingwang
ms.openlocfilehash: cf1ac441f695e5427e57f1a85bafd095777eed80
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049306"
---
# <a name="copy-data-to-or-from-azure-data-lake-store-by-using-azure-data-factory"></a>Kopiera data till och från Azure Data Lake Store med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-azure-datalake-connector.md)
> * [Aktuell version](connector-azure-data-lake-store.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data till och från Azure Data Lake Store. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från alla datalager stöds källa till Azure Data Lake Store eller kopiera data från Azure Data Lake Store till alla stöds sink-datalagret. En lista över datalager som stöds som datakällor eller sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Azure Data Lake Store-anslutningen:

- Kopiera filer med **tjänstens huvudnamn** eller **hanterade tjänstidentiteten (MSI)** autentisering.
- Filer som kopieras-är eller parsning/genererar filer med den [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Om du kopierar data med hjälp av Self-hosted integrering Runtime, konfigurera företagets brandvägg om du vill tillåta utgående trafik till `<ADLS account name>.azuredatalakestore.net` och `login.microsoftonline.com/<tenant>/oauth2/token` på port 443. Denna är Azure Security säkerhetstokentjänst (STS) som IR måste kommunicera med för att hämta åtkomsttoken.

## <a name="get-started"></a>Kom igång

> [!TIP]
> En genomgång av Azure Data Lake Store-anslutningen finns [Läs in data till Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Azure Data lake Store.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Azure Data Lake Store länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till **AzureDataLakeStore**. | Ja |
| dataLakeStoreUri | Information om Azure Data Lake Store-konto. Den här informationen har någon av följande format: `https://[accountname].azuredatalakestore.net/webhdfs/v1` eller `adl://[accountname].azuredatalakestore.net/`. | Ja |
| subscriptionId | ID för Azure-prenumeration som Data Lake Store-kontot tillhör. | Krävs för sink |
| resourceGroupName | Azure resursgruppens namn som Data Lake Store-kontot tillhör. | Krävs för sink |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime (om datalager finns i privat nätverk). Om inget anges används standard-Azure Integration Runtime. |Nej |

Se följande avsnitt om fler egenskaper och JSON-exempel för olika typer av autentisering respektive:

- [Med hjälp av service principal autentisering](#using-service-principal-authentication)
- [Med hjälp av hanteringstjänster identitetsverifiering](#using-managed-service-identity-authentication)

### <a name="using-service-principal-authentication"></a>Med hjälp av service principal autentisering

Om du vill använda huvudnamn autentiseringen av tjänsten registrera en entitet för program i Azure Active Directory (AD Azure) och bevilja åtkomst till Data Lake Store. Detaljerade anvisningar finns i [tjänst-till-tjänst autentisering](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

- Program-ID:t
- Nyckeln för programmet
- Klient-ID:t

>[!IMPORTANT]
> Kontrollera att du ger service principal rätt behörighet i Azure Data Lake Store:
>- **Som källa**, i Data explorer -> åtkomst, bevilja minst **Läs + Execute** behörighet att visa och kopiera filerna i mappen/undermappar eller **Läs** tillstånd att kopiera en fil, och välja att lägga till som **behörigheten och en standard behörighetspost**. Inga krav på administratörsnivå kontroll (IAM).
>- **Som mottagare**, i Data explorer -> åtkomst, bevilja minst **skriva + köra** behörighet att skapa underordnade objekt i mappen och välja att lägga till som **behörigheten och en standard behörighetspost**. Om du använder Azure IR för att kopiera (både källa och mottagare är i molnet), i åtkomstkontroll (IAM) ger minst **Reader** för att låta Data Factory identifiera Data Lake Store-region. Om du vill undvika den här IAM-rollen explicit [skapa ett Azure-IR](create-azure-integration-runtime.md#create-azure-ir) med platsen för ditt Data Lake Store och koppla i Data Lake Store länkade tjänsten som i följande exempel.

Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| servicePrincipalId | Ange programmets klient-ID. | Ja |
| servicePrincipalKey | Ange programmets nyckeln. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| klient | Ange information om klient (domain name eller klient ID) under där programmet finns. Du kan hämta den hovrar muspekaren i det övre högra hörnet i Azure-portalen. | Ja |

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

### <a name="using-managed-service-identity-authentication"></a>Med hjälp av hanteringstjänster identitetsverifiering

En datafabrik kan associeras med en [hanterade tjänstidentiteten](data-factory-service-identity.md), som motsvarar denna specifika data factory. Du kan använda den här tjänstidentiteten direkt för Data Lake Store-autentisering är ungefär som att använda egna huvudnamn för tjänsten. Det gör denna avsedda factory åtkomst till och kopiera data från/till din Data Lake Store.

Använda hanteringstjänster identitetsverifiering (MSI):

1. [Hämta data factory-tjänsten](data-factory-service-identity.md#retrieve-service-identity) genom att kopiera värdet för ”IDENTITY-ID för programmet” skapas tillsammans med din factory.
2. Ge service identitet åtkomst till Data Lake Store på samma sätt som du gör för tjänstens huvudnamn följande kommentarerna nedan.

>[!IMPORTANT]
> Kontrollera att du ger data factory-tjänsten identitet rätt behörighet i Azure Data Lake Store:
>- **Som källa**, i Data explorer -> åtkomst, bevilja minst **Läs + Execute** behörighet att visa och kopiera filerna i mappen/undermappar eller **Läs** tillstånd att kopiera en fil, och välja att lägga till som **behörigheten och en standard behörighetspost**. Inga krav på administratörsnivå kontroll (IAM).
>- **Som mottagare**, i Data explorer -> åtkomst, bevilja minst **skriva + köra** behörighet att skapa underordnade objekt i mappen och välja att lägga till som **behörigheten och en standard behörighetspost**. Om du använder Azure IR för att kopiera (både källa och mottagare är i molnet), i åtkomstkontroll (IAM) ger minst **Reader** för att låta Data Factory identifiera Data Lake Store-region. Om du vill undvika den här IAM-rollen explicit [skapa ett Azure-IR](create-azure-integration-runtime.md#create-azure-ir) med platsen för ditt Data Lake Store och koppla i Data Lake Store länkade tjänsten som i följande exempel.

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Data Lake Store dataset.

Ange typegenskapen för dataset för att kopiera data till/från Azure Data Lake Store, **AzureDataLakeStoreFile**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **AzureDataLakeStoreFile** |Ja |
| folderPath | Sökvägen till mappen i Data Lake Store. Wildcard-filter stöds inte. Exempel: RootFolder så/undermappen / |Ja |
| fileName | **Namn eller jokertecken-filtret** för alla filer under den angivna ”folderPath”. Om du inte anger ett värde för den här egenskapen dataset pekar på alla filer i mappen. <br/><br/>För filter som tillåts är jokertecken: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken).<br/>-Exempel 1: `"fileName": "*.csv"`<br/>-Exempel 2: `"fileName": "???20180427.txt"`<br/>Använd `^` att undanta om din faktiska filnamnet innehåller jokertecken eller den här escape-tecken i.<br/><br/>Om filnamnet har inte angetts för en datamängd för utdata och **preserveHierarchy** inte anges i aktiviteten-sink kopieringsaktiviteten genererar automatiskt filnamn med följande format ”:*Data. [ aktiviteten kör id GUID]. [GUID om FlattenHierarchy]. [format om konfigurerat]. [komprimering om konfigurerat]* ". Ett exempel är ”Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz”. |Nej |
| Format | Om du vill **kopiera filer som-är** mellan filbaserade butiker (binär kopia), hoppa över avsnittet format i både inkommande och utgående dataset-definitioner.<br/><br/>Om du vill att parsa eller generera filer med ett specifikt format format för följande filtyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [textformat](supported-file-formats-and-compression-codecs.md#text-format), [Json-Format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs.md#orc-format), och [parkettgolv Format](supported-file-formats-and-compression-codecs.md#parquet-format) avsnitt. |Nej (endast för binära kopiera scenario) |
| komprimering | Ange typ och kompression för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Nivåer som stöds är: **Optimal** och **snabbast**. |Nej |

>[!TIP]
>Kopiera alla filer i en mapp genom att ange **folderPath** endast.<br>Om du vill kopiera en enstaka fil med ett angivet namn, ange **folderPath** med Mappdel och **fileName** med filnamn.<br>Om du vill kopiera en delmängd av filer på en mapp, ange **folderPath** med Mappdel och **fileName** med jokerteckenfiltret. 

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Data Lake källa och mottagare.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store som källa

Om du vill kopiera data från Azure Data Lake Store, anger du källa i kopieringsaktiviteten till **AzureDataLakeStoreSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **AzureDataLakeStoreSource** |Ja |
| rekursiva | Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. Obs när rekursiv är inställd på true och mottagare är filbaserad lagring, tom mapp/underåtgärder-folder kommer inte att kopieras/skapas på mottagare.<br/>Tillåtna värden är: **SANT** (standard), **FALSKT** | Nej |

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

Om du vill kopiera data till Azure Data Lake Store, anger du sink i kopieringsaktiviteten till **AzureDataLakeStoreSink**. Följande egenskaper stöds i den **sink** avsnitt:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för kopiera aktivitet sink måste anges till: **AzureDataLakeStoreSink** |Ja |
| copyBehavior | Definierar beteendet kopia när källan är filer från filbaserad dataarkiv.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: bevarar fil hierarkin i målmappen. Den relativa sökvägen för källfilen till källmappen är identisk med den relativa sökvägen för filen till målmappen.<br/><b>-FlattenHierarchy</b>: alla filer från källmappen finns i den första nivån i målmappen. Mål-filer har genereras automatiskt namn. <br/><b>-MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om namnet på filen/Blob har angetts, är kopplade filnamnet det angivna namnet; annars skulle vara automatiskt genererade filnamn. | Nej |

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

Det här avsnittet beskriver resultatet av kopieringen för olika kombinationer av värden rekursiv och copyBehavior.

| rekursiva | copyBehavior | Mappstruktur för källa | Resulterande mål |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målmappen Mapp1 skapas med samma struktur som källa:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File5 |
| true |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 + fil3 + File4 + filen 5 innehållet slås samman till en fil med automatiskt genererade namnet |
| false |preserveHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målmappen Mapp1 har skapats med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/><br/>Subfolder1 med fil3, File4 och File5 har inte plockats. |
| false |flattenHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målmappen Mapp1 har skapats med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil2<br/><br/>Subfolder1 med fil3, File4 och File5 har inte plockats. |
| false |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målmappen Mapp1 har skapats med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 innehållet slås samman till en fil med automatiskt genererade namnet. automatiskt genererade namnet på File1<br/><br/>Subfolder1 med fil3, File4 och File5 har inte plockats. |

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).
