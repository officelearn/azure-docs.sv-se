---
title: Kopiera data till och från Azure Blob storage med hjälp av Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från stöds källa datalager till Azure Blob-lagring eller från Blob storage till stöds sink datalager med hjälp av Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 04/27/2018
ms.author: jingwang
ms.openlocfilehash: 1c214dc34361bea49ad00cb5dfab71a7e6855996
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Kopiera data till och från Azure Blob storage med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – Allmänt tillgänglig](v1/data-factory-azure-blob-connector.md)
> * [Version 2 – förhandsversion](connector-azure-blob-storage.md)

Den här artikeln beskrivs hur du använder Kopieringsaktiviteten i Azure Data Factory för att kopiera data till och från Azure Blob storage. Den bygger på den [Kopieringsaktiviteten översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över Kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory som är allmänt tillgänglig, se [Blob storage-anslutningen i version 1](v1/data-factory-azure-blob-connector.md).


## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från alla datalager stöds källa till Blob storage. Du kan också kopiera data från Blob storage till alla stöds sink-datalagret. En lista över datalager som stöds som datakällor eller sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md) tabell.

Mer specifikt stöder den här Blob storage-anslutningen:

- Kopiera BLOB till och från allmänna Azure storage-konton och hot/lågfrekvent blob-lagring. 
- Kopierar blobar med hjälp av både kontonyckel och tjänsten för delad åtkomst signatur autentiseringar.
- Kopiera blobbar från block, Lägg till eller sidblobar och kopiera data till endast blockblobbar. Azure Premium-lagring stöds inte som en mottagare eftersom den backas upp av sidblobar.
- Kopiera BLOB eller parsning eller generera blobbar med [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Blob storage.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

### <a name="use-an-account-key"></a>Använd en kontonyckel

Du kan skapa en länkad Storage-tjänst genom att använda för kontot. Det ger datafabriken global åtkomst till lagring. Följande egenskaper stöds.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till **AzureStorage**. |Ja |
| connectionString | Ange information som behövs för att ansluta till lagring för egenskapen connectionString. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [integrering runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime (om din data store är i ett privat nätverk). Om inget anges används standard-Azure Integration Runtime. |Nej |

**Exempel:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
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

### <a name="use-service-shared-access-signature-authentication"></a>Använda tjänsten autentisering med signatur för delad åtkomst

Du kan också skapa en länkad Storage-tjänst genom att använda en signatur för delad åtkomst. Det ger datafabriken begränsad/Tidsbundna åtkomst till alla utvalda resurser (blobbehållare) i lagringen.

En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt lagringskonto. Du kan använda en signatur för delad åtkomst för att ge en klient begränsade behörigheter till objekt i ditt lagringskonto för en angiven tid. Du behöver inte dela åtkomstnycklarna för ditt konto. Signatur för delad åtkomst är en URI som omfattar all information som krävs för autentiserad åtkomst till en lagringsresurs i dess Frågeparametrar. För att komma åt lagringsresurser med signatur för delad åtkomst, måste klienten endast ska skickas i signatur för delad åtkomst till lämplig konstruktor eller metod. Mer information om signaturer för delad åtkomst finns [signaturer för delad åtkomst: Förstå delad åtkomst signatur modellen](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!IMPORTANT]
> Data Factory stöder nu endast service delade åtkomstsignaturer men inte kontot delade åtkomstsignaturer. Mer information om dessa två typer och hur du skapar dem finns [typer av signaturer för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures). Delad åtkomst signatur-URL som genereras från Azure-portalen eller Azure Lagringsutforskaren är ett konto signatur för delad åtkomst, vilket inte stöds.

> [!TIP]
> Du kan köra följande PowerShell-kommandon för att generera en signatur för delad åtkomst av tjänsten för ditt lagringskonto. Ersätt platshållarna och bevilja behörighet som krävs.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Om du vill använda autentisering med signatur för delad åtkomst service stöds följande egenskaper.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till **AzureStorage**. |Ja |
| sasUri | Ange signaturen för delad åtkomst URI till lagringsresurser, till exempel blob, behållare eller tabellen. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [integrering runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime (om datalager finns i ett privat nätverk). Om inget anges används standard-Azure Integration Runtime. |Nej |

**Exempel:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
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

När du skapar en signatur för delad åtkomst URI, bör följande saker övervägas:

- Ange lämpliga Läs-/ skrivbehörigheter för objekt baserat på hur den länkade tjänsten (läsa, skriva, läsning och skrivning) används i din data factory.
- Ange **förfallotiden** på lämpligt sätt. Kontrollera att åtkomsten till lagringsobjekt inte går ut inom den aktiva perioden för pipelinen.
- URI: N ska skapas på rätt behållare/blob- eller tabellen baserat på behovet. En signatur för delad åtkomst URI till en blobb kan Data Factory för att få åtkomst till den specifika blobben. En signatur för delad åtkomst URI till en behållare för Blob storage kan Data Factory för att gå igenom blobbar i behållaren. Kom ihåg att uppdatera den länkade tjänsten med den nya URI, för att ge åtkomst till fler eller färre objekt senare, eller uppdatera signatur för delad åtkomst URI.

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns på [datauppsättningar](concepts-datasets-linked-services.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Blob storage dataset.

Ange typegenskapen för dataset för att kopiera data till och från Blob storage, **AzureBlob**. Följande egenskaper stöds.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till **AzureBlob**. |Ja |
| folderPath | Sökvägen till behållaren och mappen i blob storage. Ett exempel är myblobcontainer/myblobfolder /. |Ja |
| fileName | **Namn eller jokertecken-filtret** för blob(s) under den angivna ”folderPath”. Om du inte anger ett värde för den här egenskapen dataset pekar på alla blobbar i mappen. <br/><br/>För filter som tillåts är jokertecken: `*` (flera tecken) och `?` (valfritt tecken).<br/>-Exempel 1: `"fileName": "*.csv"`<br/>-Exempel 2: `"fileName": "???20180427.txt"`<br/><br/>Om filnamnet har inte angetts för en datamängd för utdata och **preserveHierarchy** inte anges i aktiviteten-sink kopieringsaktiviteten genererar automatiskt blobbnamnet med följande mönster: `Data.[activity run id GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]`. Ett exempel är ”Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz”. |Nej |
| Format | Om du vill kopiera filer som finns mellan filbaserade butiker (binär kopia) kan du hoppa över avsnittet format i både inkommande och utgående dataset definitionerna.<br/><br/>Om du vill att parsa eller generera filer med ett specifikt format format för följande filtyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, och **ParquetFormat**. Ange den **typen** egenskap under **format** till någon av dessa värden. Mer information finns i [textformat](supported-file-formats-and-compression-codecs.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [Orc format](supported-file-formats-and-compression-codecs.md#orc-format), och [parkettgolv format ](supported-file-formats-and-compression-codecs.md#parquet-format) avsnitt. |Nej (endast för binära kopiera scenario) |
| Komprimering | Ange typ och kompression för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Stöds nivåerna **Optimal** och **snabbast**. |Nej |

>[!TIP]
>Kopiera alla BLOB under en mapp genom att ange **folderPath** endast.<br>Om du vill kopiera en enda blob med ett angivet namn, ange **folderPath** med Mappdel och **fileName** med filnamn.<br>Om du vill kopiera en delmängd av blobbar under en mapp, ange **folderPath** med Mappdel och **fileName** med jokerteckenfiltret. 

**Exempel:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Blob storage källa och mottagare.

### <a name="blob-storage-as-a-source-type"></a>BLOB storage som typ av datakälla

Om du vill kopiera data från Blob storage som källtypen i kopieringsaktiviteten till **BlobSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till **BlobSource**. |Ja |
| Rekursiva | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. Observera att när rekursiv är inställd på true och sink är en filbaserad lagring, en tom mapp eller undermapp inte kopieras eller skapades sink.<br/>Tillåtna värden är **SANT** (standard) och **FALSKT**. | Nej |

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

### <a name="blob-storage-as-a-sink-type"></a>BLOB storage som en Mottagartypen

Om du vill kopiera data till Blob storage, anger du sink i kopieringsaktiviteten till **BlobSink**. Följande egenskaper stöds i den **sink** avsnitt.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för kopiera aktivitet sink måste anges till **BlobSink**. |Ja |
| copyBehavior | Definierar beteendet kopia när källan är filer från en butik filbaserade data.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: bevarar fil hierarkin i målmappen. Den relativa sökvägen för källfilen till källmappen är identisk med den relativa sökvägen för filen till målmappen.<br/><b>-FlattenHierarchy</b>: alla filer från källmappen finns i den första nivån i målmappen. Mål-filer har genererats automatiskt namn. <br/><b>-MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om fil-eller blob anges är kopplade filnamnet det angivna namnet. Annars är ett automatiskt genererade filnamn. | Nej |

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

Det här avsnittet beskriver resultatet av kopieringen för olika kombinationer av värden rekursiv och copyBehavior.

| Rekursiva | copyBehavior | Mappstruktur för källa | Resulterande mål |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | målmappen Mapp1 skapas med samma struktur som källa:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för File5 |
| true |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | målet Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 + fil3 + File4 + File5 innehållet slås samman till en fil med ett automatiskt genererade namnet. |
| false |preserveHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | målmappen Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/><br/>Subfolder1 med fil3, File4 och File5 hämtas inte. |
| false |flattenHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | målmappen Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet på File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererade namnet för fil2<br/><br/>Subfolder1 med fil3, File4 och File5 hämtas inte. |
| false |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fil3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | målmappen Mapp1 har skapats med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + fil2 innehållet slås samman till en fil med ett automatiskt genererade namnet. automatiskt genererade namnet på File1<br/><br/>Subfolder1 med fil3, File4 och File5 hämtas inte. |

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).
