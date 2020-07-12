---
title: Sök över innehåll i Azure Blob Storage
titleSuffix: Azure Cognitive Search
description: Lär dig hur du indexerar Azure Blob Storage och extraherar text från dokument med Azure Kognitiv sökning.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 3ed3ff94b764c0fcb5521ef8106b32923b203a01
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2020
ms.locfileid: "86260641"
---
# <a name="how-to-index-documents-in-azure-blob-storage-with-azure-cognitive-search"></a>Indexera dokument i Azure Blob Storage med Azure Kognitiv sökning

Den här artikeln visar hur du använder Azure Kognitiv sökning för att indexera dokument (till exempel PDF-filer, Microsoft Office dokument och flera andra vanliga format) som lagras i Azure Blob Storage. Först förklarar det grunderna för att ställa in och konfigurera en BLOB-indexerare. Sedan ger den en djupare utforskning av beteenden och scenarier som du sannolikt kommer att stöta på.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Dokumentformat som stöds
BLOB-indexeraren kan extrahera text från följande dokument format:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Konfigurera BLOB-indexering
Du kan konfigurera en Azure Blob Storage-indexerare med hjälp av:

* [Azure Portal](https://ms.portal.azure.com)
* Azure Kognitiv sökning [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Kognitiv sökning [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search)

> [!NOTE]
> Vissa funktioner (till exempel fält mappningar) är ännu inte tillgängliga i portalen och måste användas program mässigt.
>

Här demonstrerar vi flödet med hjälp av REST API.

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla
En data källa anger vilka data som ska indexeras, autentiseringsuppgifter som behövs för att komma åt data och principer för att effektivt identifiera ändringar i data (nya, ändrade eller borttagna rader). En data källa kan användas av flera indexerare i samma Sök tjänst.

För BLOB-indexering måste data källan ha följande obligatoriska egenskaper:

* **Name** är det unika namnet på data källan i Sök tjänsten.
* **typen** måste vara `azureblob` .
* **autentiseringsuppgifterna** tillhandahåller anslutnings strängen för lagrings kontot som `credentials.connectionString` parameter. Mer information finns i [ange autentiseringsuppgifter](#Credentials) nedan.
* **container** anger en behållare i ditt lagrings konto. Som standard kan alla blobar i behållaren hämtas. Om du bara vill indexera blobbar i en viss virtuell katalog kan du ange den katalogen med hjälp av parametern valfri **fråga** .

Så här skapar du en data Källa:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   
```

Mer information om API för att skapa DataSource finns i [skapa data källa](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Ange autentiseringsuppgifter ####

Du kan ange autentiseringsuppgifter för BLOB-behållaren på något av följande sätt:

- **Anslutnings sträng för lagrings konto med fullständig åtkomst**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` du kan hämta anslutnings strängen från Azure Portal genom att gå till bladet lagrings konto > inställningar > nycklar (för klassiska lagrings konton) eller inställningar > åtkomst nycklar (för Azure Resource Manager lagrings konton).
- Anslutnings sträng för **signatur för delad åtkomst för lagrings konto** : `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` SAS ska ha listan och Läs behörigheter för behållare och objekt (blobbar i detta fall).
-  **Signatur för delad åtkomst till container**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` SAS ska ha list-och Läs behörighet för behållaren.

Mer information om signaturer för delad åtkomst för lagring finns i [använda signaturer för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Om du använder SAS-autentiseringsuppgifter måste du uppdatera autentiseringsuppgifterna för data källan regelbundet med förnyade signaturer för att förhindra att de upphör att gälla. Om autentiseringsuppgifter för SAS går ut Miss Miss känner indexeraren med ett fel meddelande som liknar `Credentials provided in the connection string are invalid or have expired.` .  

### <a name="step-2-create-an-index"></a>Steg 2: Skapa ett index
Indexet anger fält i ett dokument, attribut och andra konstruktioner som formar Sök funktionen.

Så här skapar du ett index med ett sökbart `content` fält för att lagra texten som extraheras från blobbar:   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

Mer information om hur du skapar index finns i [skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>Steg 3: skapa en indexerare
En indexerare ansluter en data källa med ett mål Sök index och innehåller ett schema för att automatisera data uppdateringen.

När indexet och data källan har skapats är du redo att skapa indexeraren:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Indexeraren körs varannan timme (schema intervall anges till "PT2H"). Om du vill köra en indexerare var 30: e minut anger du intervallet till "PT30M". Det kortaste intervall som stöds är 5 minuter. Schemat är valfritt – om det utelämnas körs en indexerare bara en gång när den skapas. Du kan dock köra en indexerare på begäran när du vill.   

Mer information om API för att skapa index finns i [skapa indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Mer information om hur du definierar indexerare scheman finns i [så här schemalägger du indexerare för Azure kognitiv sökning](search-howto-schedule-indexers.md).

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-azure-cognitive-search-indexes-blobs"></a>Hur Azure Kognitiv sökning indexerar blobbar

Beroende på [indexerings konfigurationen](#PartsOfBlobToIndex)kan BLOB-indexeraren bara indexera lagrings-metadata (användbart när du bara bryr dig om metadata och behöver inte indexera innehållet i blobbar), lagrings-och innehålls metadata, eller både metadata och text innehåll. Som standard extraherar indexeraren både metadata och innehåll.

> [!NOTE]
> Som standard indexeras blobbar med strukturerat innehåll som JSON eller CSV som ett enda text segment. Om du vill indexera JSON-och CSV-blobbar på ett strukturerat sätt, se [INDEXERA JSON-blobbar](search-howto-index-json-blobs.md) och [Indexera CSV-blobbar](search-howto-index-csv-blobs.md) för mer information.
>
> Ett sammansatt eller inbäddat dokument (till exempel ett ZIP-arkiv, ett Word-dokument med inbäddad Outlook-e-post med bifogade filer eller en. MSG-filen med bifogade filer är också indexerad som ett enda dokument. Till exempel alla bilder som extraheras från bilagorna till en. MSG-filen kommer att returneras i fältet normalized_images.

* Text innehållet i dokumentet extraheras till ett sträng fält med namnet `content` .

> [!NOTE]
> Azure Kognitiv sökning begränsar hur mycket text den extraherar beroende på pris nivå: 32 000 tecken för kostnads fri nivå, 64 000 för Basic, 4 000 000 för standard, 8 000 000 för standard S2 och 16 000 000 för standard S3. En varning ingår i status svaret för indexeraren för trunkerade dokument.  

* Användare-angivna metadataegenskaper som finns i blobben extraheras orda Grant. Observera att detta kräver att ett fält definieras i indexet med samma namn som metadata-nyckeln för blobben. Om din BLOB till exempel har en nyckel för `Sensitivity` med värde `High` , bör du definiera ett fält med namnet `Sensitivity` i Sök indexet så att det fylls med värdet `High` .
* Standard egenskaper för BLOB-metadata extraheras i följande fält:

  * ** \_ lagrings \_ namn för metadata** (EDM. String) – fil namnet för blobben. Om du till exempel har en BLOB-/My-container/My-Folder/subfolder/resume.pdf är värdet för det här fältet `resume.pdf` .
  * ** \_ lagrings \_ Sök väg för metadata** (EDM. String)-den fullständiga URI: n för blobben, inklusive lagrings kontot. Till exempel, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * ** \_ \_ innehålls \_ typ för metadata-lagring** (EDM. String) – innehålls typ som anges av den kod som du använde för att ladda upp blobben. Ett exempel är `application/octet-stream`.
  * **metadata \_ Storage \_ senast \_ ändrad** (EDM. DateTimeOffset)-den senaste ändrade tidsstämpeln för blobben. Azure Kognitiv sökning använder den här tidsstämpeln för att identifiera ändrade blobbar, för att undvika att indexera om allt efter den inledande indexeringen.
  * ** \_ lagrings \_ storlek för metadata** (EDM. Int64) – BLOB-storlek i byte.
  * **innehåll för metadata \_ Storage \_ \_ Md5** (EDM. String) – MD5-hash av BLOB-innehållet, om det är tillgängligt.
  * ** \_ SAS- \_ \_ token för metadata Storage** (EDM. String) – en tillfällig SAS-token som kan användas av [anpassade kunskaper](cognitive-search-custom-skill-interface.md) för att få åtkomst till blobben. Denna token ska inte lagras för senare användning eftersom den kan gå ut.

* Metadata-egenskaper som är speciella för varje dokument format extraheras till fälten som visas [här](#ContentSpecificMetadata).

Du behöver inte definiera fält för alla egenskaper som anges ovan i sökindexet – du behöver bara hämta de egenskaper du behöver för ditt program.

> [!NOTE]
> Ofta kommer fält namnen i det befintliga indexet att skilja sig från fält namnen som genereras under dokument extraheringen. Du kan använda **fält mappningar** för att mappa de egenskaps namn som tillhandahålls av Azure kognitiv sökning till fält namnen i Sök indexet. Du kommer att se ett exempel på fält mappningar som används nedan.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definiera dokument nycklar och fält mappningar
I Azure Kognitiv sökning identifierar dokument nyckeln ett dokument unikt. Varje Sök index måste ha exakt ett nyckel fält av typen EDM. String. Nyckel fältet krävs för varje dokument som läggs till i indexet (det är egentligen det enda obligatoriska fältet).  

Du bör noga överväga vilka extraherade fält som ska mappas till nyckel fältet för ditt index. Kandidater är:

* ** \_ lagrings \_ namn för metadata** – detta kan vara en lämplig kandidat, men Observera att namnet inte är unikt, eftersom du kan ha blobbar med samma namn i olika mappar och 2) namnet får innehålla ogiltiga tecken i dokument nycklar, till exempel bindestreck. Du kan hantera ogiltiga tecken med hjälp av `base64Encode` [fält mappnings funktionen](search-indexer-field-mappings.md#base64EncodeFunction) – om du gör det måste du komma ihåg att koda dokument nycklar när du skickar dem till API-anrop som lookup. (I .NET kan du till exempel använda UrlTokenEncode- [metoden](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) för det syftet).
* ** \_ lagrings \_ Sök väg för metadata** – genom att använda den fullständiga sökvägen ser du till att sökvägen är unik, men sökvägen innehåller definitivt `/` [ogiltiga tecken i en dokument nyckel](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Som ovan kan du välja att koda nycklar med hjälp av `base64Encode` [funktionen](search-indexer-field-mappings.md#base64EncodeFunction).
* Om inget av alternativen ovan fungerar kan du lägga till en anpassad metadata-egenskap till Blobbarna. Det här alternativet kräver dock att BLOB-uppladdnings processen lägger till denna metadata-egenskap till alla blobbar. Eftersom nyckeln är en obligatorisk egenskap, kommer alla blobar som inte har denna egenskap att kunna indexeras.

> [!IMPORTANT]
> Om det inte finns någon explicit mappning för nyckel fältet i indexet, används automatiskt Azure Kognitiv sökning `metadata_storage_path` som nyckel-och bas-64-kodningar nyckel värden (det andra alternativet ovan).
>
>

I det här exemplet väljer vi `metadata_storage_name` fältet som dokument nyckel. Vi antar också att ditt index har ett nyckel fält med namnet `key` och ett fält `fileSize` för att lagra dokument storleken. Om du vill ansluta saker efter behov anger du följande fält mappningar när du skapar eller uppdaterar indexeraren:

```http
    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]
```

Så här gör du tillsammans så här kan du lägga till fält mappningar och aktivera bas-64 kodning av nycklar för en befintlig indexerare:

```http
    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }
```

> [!NOTE]
> Mer information om fält mappningar finns i [den här artikeln](search-indexer-field-mappings.md).
>
>

#### <a name="what-if-you-need-to-encode-a-field-to-use-it-as-a-key-but-you-also-want-to-search-it"></a>Vad händer om du måste koda ett fält för att använda det som en nyckel, men du vill även söka i det?

Det finns tillfällen när du behöver använda en kodad version av ett fält som metadata_storage_path som nyckel, men du måste också att fältet ska vara sökbart (utan kodning). För att lösa det här problemet kan du mappa det i två fält; en som ska användas för nyckeln och en annan som ska användas i Sök syfte. I exemplet nedan innehåller *nyckel* fältet den kodade sökvägen, medan fältet *sökväg* inte är kodat och används som sökbart fält i indexet.

```http
    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
      ]
    }
```
<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Styra vilka blobbar som indexeras
Du kan kontrol lera vilka blobbar som är indexerade och vilka som hoppas över.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Indexera enbart blobbar med vissa fil namns tillägg
Du kan bara indexera blobbar med de fil namns tillägg som du anger med hjälp av `indexedFileNameExtensions` konfigurations parametern indexerare. Värdet är en sträng som innehåller en kommaavgränsad lista med fil namns tillägg (med en inledande punkt). Om du till exempel bara vill indexera. PDF och. DOCX-blobbar gör du följande:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }
```

### <a name="exclude-blobs-with-specific-file-extensions"></a>Uteslut blobbar med vissa fil namns tillägg
Du kan exkludera blobbar med vissa fil namns tillägg från indexering med hjälp av `excludedFileNameExtensions` konfigurations parametern. Värdet är en sträng som innehåller en kommaavgränsad lista med fil namns tillägg (med en inledande punkt). Om du till exempel vill indexera alla blobbar förutom de med. PNG och. JPEG-tillägg gör du följande:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }
```

Om både `indexedFileNameExtensions` och `excludedFileNameExtensions` -parametrarna finns, tittar Azure kognitiv sökning först på `indexedFileNameExtensions` , sedan på `excludedFileNameExtensions` . Det innebär att om samma fil namns tillägg finns i båda listorna, kommer det att undantas från indexering.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Kontrol lera vilka delar av blobben som indexeras

Du kan styra vilka delar av blobbar som indexeras med hjälp av `dataToExtract` konfigurations parametern. Det kan ha följande värden:

* `storageMetadata`-anger att endast [standard-BLOB-egenskaperna och användardefinierade metadata](../storage/blobs/storage-properties-metadata.md) indexeras.
* `allMetadata`-anger att lagrings-metadata och de [innehålls typbaserade metadata](#ContentSpecificMetadata) som extraherats från BLOB-innehållet indexeras.
* `contentAndMetadata`-anger att alla metadata och text innehåll som extraheras från blobben indexeras. Detta är standardvärdet.

Om du till exempel bara vill indexera lagringens metadata använder du:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }
```

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Använda BLOB-metadata för att kontrol lera hur blobbar indexeras

De konfigurations parametrar som beskrivs ovan gäller för alla blobbar. Ibland kanske du vill styra hur *enskilda blobbar* indexeras. Du kan göra detta genom att lägga till följande egenskaper och värden för BLOB-metadata:

| Egenskapsnamn | Egenskaps värde | Förklaring |
| --- | --- | --- |
| AzureSearch_Skip |värdet |Instruerar BLOB-indexeraren att helt hoppa över blobben. Varken metadata eller innehålls extrahering görs. Detta är användbart när en viss BLOB Miss lyckas upprepade gånger och avbryter indexerings processen. |
| AzureSearch_SkipContent |värdet |Detta motsvarar den `"dataToExtract" : "allMetadata"` inställning som beskrivs [ovan](#PartsOfBlobToIndex) begränsad till en viss blob. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Hantera fel

Som standard stoppas BLOB-indexeraren så snart den påträffar en blob med en innehålls typ som inte stöds (till exempel en bild). Du kan naturligtvis använda `excludedFileNameExtensions` parametern för att hoppa över vissa innehålls typer. Du kan dock behöva indexera blobbar utan att känna till alla möjliga innehålls typer i förväg. Om du vill fortsätta indexera när en innehålls typ som inte stöds har påträffats ställer du in `failOnUnsupportedContentType` konfigurations parametern på `false` :

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }
```

För vissa blobbar går det inte att identifiera innehålls typen i Azure Kognitiv sökning eller så det går inte att bearbeta ett dokument av en innehålls typ som inte stöds. Om du vill ignorera det här felläget ställer du in `failOnUnprocessableDocument` konfigurations parametern på falskt:

```http
      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Azure Kognitiv sökning begränsar storleken på blobbar som indexeras. De här gränserna dokumenteras i [tjänst begränsningar i Azure kognitiv sökning](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Överändrade blobbar behandlas som standard som fel. Du kan dock fortfarande indexera lagrings metadata för överändrade blobbar om du anger `indexStorageMetadataOnlyForOversizedDocuments` parametern för konfiguration till True: 

```http
    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

Du kan också fortsätta att indexera om fel inträffar när som helst, antingen vid parsning av blobbar eller när dokument läggs till i ett index. Om du vill ignorera ett visst antal fel anger du `maxFailedItems` `maxFailedItemsPerBatch` parametrarna och för konfigurationen till önskade värden. Exempel:

```http
    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }
```

## <a name="incremental-indexing-and-deletion-detection"></a>Identifiering och borttagning av stegvis indexering

När du konfigurerar en BLOB-indexerare så att den körs enligt ett schema, indexerar den bara om de ändrade Blobbarna, vilket bestäms av blobens `LastModified` tidsstämpel.

> [!NOTE]
> Du behöver inte ange en princip för ändrings identifiering – stegvis indexering aktive ras automatiskt.

Använd en "mjuk borttagning"-metod för att stödja borttagning av dokument. Om du tar bort Blobbarna till höger tas inte motsvarande dokument bort från Sök indexet.

Det finns två sätt att implementera metoden för mjuk borttagning. Båda beskrivs nedan.

### <a name="native-blob-soft-delete-preview"></a>Intern BLOB-mjuk borttagning (förhands granskning)

> [!IMPORTANT]
> Stöd för intern BLOB-mjuk borttagning är i för hands version. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Den [REST API version 2020-06-30 – för hands version](https://docs.microsoft.com/azure/search/search-api-preview) innehåller den här funktionen. Det finns för närvarande inget stöd för Portal eller .NET SDK.

> [!NOTE]
> När du använder den inbyggda principen för mjuk borttagning av BLOB måste dokument nycklarna för dokumenten i ditt index antingen vara en BLOB-egenskap eller BLOB-metadata.

I den här metoden ska du använda den inbyggda funktionen för [mjuk borttagning av BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) som erbjuds av Azure Blob Storage. Om den inbyggda BLOB-borttagningen är aktive rad på ditt lagrings konto har data källan en inbyggd princip uppsättning för mjuk borttagning och indexeraren hittar en blob som har överförts till ett mjukt borttaget läge, tar indexeraren bort dokumentet från indexet. Den inbyggda principen för mjuk borttagning av BLOB stöds inte när du indexerar blobbar från Azure Data Lake Storage Gen2.

Gör så här:
1. Aktivera [inbyggd mjuk borttagning för Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete). Vi rekommenderar att du anger bevarande principen till ett värde som är mycket högre än schemats intervall schema. På det här sättet är det mycket tid för indexeraren att bearbeta de mjuka borttagna blobarna om det uppstår ett problem med att köra indexeraren eller om du har ett stort antal dokument att indexera. Azure Kognitiv sökning-indexerare tar bara bort ett dokument från indexet om det bearbetar blobben när det är i ett mjukt borttaget tillstånd.
1. Konfigurera en intern identifierings princip för mjuk borttagning av BLOB på data källan. Ett exempel på detta visas nedan. Eftersom den här funktionen är i för hands version måste du använda REST API för förhands granskning.
1. Kör indexeraren eller Ställ in indexeraren så att den körs enligt ett schema. När indexeraren kör och bearbetar blobben tas dokumentet bort från indexet.

    ```
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

#### <a name="reindexing-undeleted-blobs"></a>Omindexerade blobar som inte har raderats

Om du tar bort en BLOB från Azure Blob Storage med inbyggd mjuk borttagning aktive rad på ditt lagrings konto, övergår bloben till ett mjukt borttaget tillstånd och ger dig möjlighet att ångra borttagningen av bloben inom kvarhållningsperioden. När en Azure Kognitiv sökning-datakälla har en inbyggd princip för mjuk borttagning av BLOB och indexeraren bearbetar en mjuk borttagen BLOB tas dokumentet bort från indexet. Om bloben senare tas bort, kommer indexeraren inte alltid att indexera om denna blob. Detta beror på att indexeraren bestämmer vilka blobbar som ska indexeras baserat på blobens `LastModified` tidsstämpel. När en mjuk borttagen BLOB inte har tagits bort `LastModified` kommer dess tidsstämpel inte att uppdateras, så om indexeraren redan har bearbetat blobbar med `LastModified` tidsstämplar senare än den borttagna blobben indexeras inte den icke borttagna blobben. För att se till att en ångrad BLOB indexeras om måste du uppdatera blobens `LastModified` tidstämpel. Ett sätt att göra detta är genom att spara om metadata för denna blob. Du behöver inte ändra metadata, men om du sparar om metadata uppdateras blobens `LastModified` tidstämpel så att indexeraren vet att den måste indexera om denna blob.

### <a name="soft-delete-using-custom-metadata"></a>Mjuk borttagning med anpassade metadata

I den här metoden ska du använda en blobs metadata för att ange när ett dokument ska tas bort från Sök indexet.

Gör så här:

1. Lägg till ett anpassat nyckel/värde-par för metadata i blobben för att indikera Azure-Kognitiv sökning att den tas bort logiskt.
1. Konfigurera en princip för identifiering av mjuk borttagnings kolumner på data källan. Ett exempel på detta visas nedan.
1. När indexeraren har bearbetat blobben och tagit bort dokumentet från indexet kan du ta bort bloben för Azure Blob Storage.

Följande princip anser till exempel att en BLOB tas bort om den har en metadata-egenskap `IsDeleted` med värdet `true` :

```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
```

#### <a name="reindexing-undeleted-blobs"></a>Omindexerade blobar som inte har raderats

Om du anger en princip för att ta bort en mjuk borttagnings kolumn på din data källa, lägger du till anpassade metadata i en blob med värdet markör och kör sedan indexeraren, så tar indexeraren bort dokumentet från indexet. Om du vill indexera om dokumentet ska du bara ändra värdet för mjuk borttagning av metadata för denna blob och köra indexeraren igen.

## <a name="indexing-large-datasets"></a>Indexera stora data uppsättningar

Indexering av blobbar kan vara en tids krävande process. I de fall där du har miljon tals blobbar att indexera kan du påskynda indexeringen genom att partitionera data och använda flera indexerare för att bearbeta data parallellt. Så här kan du konfigurera detta:

- Partitionera dina data i flera BLOB-behållare eller virtuella mappar
- Konfigurera flera Azure Kognitiv sökning data källor, en per behållare eller mapp. Använd parametern för att peka på en BLOB-mapp `query` :

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Skapa en motsvarande indexerare för varje data källa. Alla indexerare kan peka på samma mål Sök index.  

- En Sök enhet i din tjänst kan köra en indexerare vid en specifik tidpunkt. Att skapa flera indexerare enligt beskrivningen ovan är bara användbart om de faktiskt körs parallellt. Om du vill köra flera indexerare parallellt kan du skala ut Sök tjänsten genom att skapa ett lämpligt antal partitioner och repliker. Om Sök tjänsten till exempel har 6 Sök enheter (till exempel 2 partitioner x 3 repliker) kan 6 indexerare köras samtidigt, vilket resulterar i en Six-vikt i index data flödet. Mer information om skalning och kapacitets planering finns i [skala resurs nivåer för frågor och indexering av arbets belastningar i Azure kognitiv sökning](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indexera dokument tillsammans med relaterade data

Du kanske vill montera dokument från flera källor i ditt index. Du kanske till exempel vill koppla text från blobbar med andra metadata som lagras i Cosmos DB. Du kan även använda API för push-indexering tillsammans med olika indexerare för att skapa Sök dokument från flera delar. 

För att detta ska fungera måste alla indexerare och andra komponenter godkänna dokument nyckeln. Mer information om det här ämnet finns i [index flera Azure-datakällor](https://docs.microsoft.com/azure/search/tutorial-multiple-data-sources). En detaljerad genom gång finns i den här externa artikeln: [kombinera dokument med andra data i Azure kognitiv sökning](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indexera oformaterad text 

Om alla blobar innehåller oformaterad text i samma kodning, kan du förbättra indexerings prestanda avsevärt genom att använda **text tolknings läge**. Om du vill använda text tolknings läge ställer du in `parsingMode` konfigurations egenskapen på `text` :

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }
```

Som standard `UTF-8` antas kodningen. Om du vill ange en annan kodning använder du `encoding` konfigurations egenskapen: 

```http
    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }
```

<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Egenskaper för innehålls typ-/regionsspecifika metadata
I följande tabell sammanfattas bearbetningen för varje dokument format och de metadata-egenskaper som har extraherats av Azure Kognitiv sökning beskrivs.

| Dokument format/innehålls typ | Egenskaper för speciella metadata för innehålls typ | Bearbetnings information |
| --- | --- | --- |
| HTML (text/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Remsa HTML-kod och extrahera text |
| PDF (program/PDF) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Extrahera text, inklusive inbäddade dokument (exklusive bilder) |
| DOCX (Application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahera text, inklusive inbäddade dokument |
| DOC (program/MSWord) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahera text, inklusive inbäddade dokument |
| DOCM (Application/vnd.ms-word.document. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahera text, inklusive inbäddade dokument |
| WORD XML (Application/VND. MS-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Stripe XML-kod och extrahera text |
| WORD 2003 XML (Application/VND. MS-WordML) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Stripe XML-kod och extrahera text |
| XLSX (Application/VND. openxmlformats-officedocument. SpreadsheetML. Sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahera text, inklusive inbäddade dokument |
| XLS (Application/VND. MS-Excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahera text, inklusive inbäddade dokument |
| XLSM (Application/VND. MS-Excel. Sheet. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahera text, inklusive inbäddade dokument |
| PPTX (Application/VND. openxmlformats-officedocument. presentationml. presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahera text, inklusive inbäddade dokument |
| PPT (Application/VND. MS-PowerPoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahera text, inklusive inbäddade dokument |
| PPTM (Application/VND. MS-PowerPoint. presentation. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahera text, inklusive inbäddade dokument |
| MSG (Application/VND. MS-Outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extrahera text, inklusive text som extraherats från bilagor. `metadata_message_to_email`, `metadata_message_cc_email` och `metadata_message_bcc_email` är sträng samlingar, är resten av fälten strängar.|
| ODT (Application/VND. Oasis. OpenDocument. text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahera text, inklusive inbäddade dokument |
| ODS (Application/VND. Oasis. OpenDocument. kalkyl blad) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahera text, inklusive inbäddade dokument |
| ODP (Application/VND. Oasis. OpenDocument. presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Extrahera text, inklusive inbäddade dokument |
| ZIP (program/zip) |`metadata_content_type` |Extrahera text från alla dokument i arkivet |
| GZ (program/gzip) |`metadata_content_type` |Extrahera text från alla dokument i arkivet |
| EPUB (Application/EPUB + zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Extrahera text från alla dokument i arkivet |
| XML (program/XML) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Stripe XML-kod och extrahera text |
| JSON (Application/JSON) |`metadata_content_type`<br/>`metadata_content_encoding` |Extrahera text<br/>Obs! Om du behöver extrahera flera dokument fält från en JSON-BLOB kan du läsa mer i [INDEXERA JSON-blobbar](search-howto-index-json-blobs.md) |
| EML (meddelande/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Extrahera text, inklusive bifogade filer |
| RTF (program/RTF) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Extrahera text|
| Oformaterad text (text/plain) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Extrahera text|


## <a name="help-us-make-azure-cognitive-search-better"></a>Hjälp oss att göra Azure Kognitiv sökning bättre
Om du har funktions förfrågningar eller idéer om förbättringar kan du berätta för oss på vår [UserVoice-webbplats](https://feedback.azure.com/forums/263029-azure-search/).
