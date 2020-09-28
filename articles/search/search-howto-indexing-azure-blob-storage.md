---
title: Konfigurera en BLOB-indexerare
titleSuffix: Azure Cognitive Search
description: Konfigurera en Azure Blob-indexerare för att automatisera indexering av BLOB-innehåll för fullständiga texts öknings åtgärder i Azure Kognitiv sökning.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: e3419711c9a7358914f85574f6dbd5af29def1cf
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403626"
---
# <a name="how-to-configure-a-blob-indexer-in-azure-cognitive-search"></a>Så här konfigurerar du en BLOB-indexerare i Azure Kognitiv sökning

Den här artikeln visar hur du använder Azure Kognitiv sökning för att indexera textbaserade dokument (till exempel PDF-filer, Microsoft Office dokument och flera andra vanliga format) som lagras i Azure Blob Storage. Först förklarar det grunderna för att ställa in och konfigurera en BLOB-indexerare. Sedan ger den en djupare utforskning av beteenden och scenarier som du sannolikt kommer att stöta på.

<a name="SupportedFormats"></a>

## <a name="supported-formats"></a>Format som stöds

BLOB-indexeraren kan extrahera text från följande dokument format:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="set-up-blob-indexing"></a>Konfigurera BLOB-indexering

Du kan konfigurera en Azure Blob Storage-indexerare med hjälp av:

* [Azure-portalen](https://ms.portal.azure.com)
* Azure Kognitiv sökning [REST API](/rest/api/searchservice/Indexer-operations)
* Azure Kognitiv sökning [.NET SDK](/dotnet/api/overview/azure/search)

> [!NOTE]
> Vissa funktioner (till exempel fält mappningar) är ännu inte tillgängliga i portalen och måste användas program mässigt.
>

Här demonstrerar vi flödet med hjälp av REST API.

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla

En data källa anger vilka data som ska indexeras, autentiseringsuppgifter som behövs för att komma åt data och principer för att effektivt identifiera ändringar i data (nya, ändrade eller borttagna rader). En data källa kan användas av flera indexerare i samma Sök tjänst.

För BLOB-indexering måste data källan ha följande obligatoriska egenskaper:

* **Name** är det unika namnet på data källan i Sök tjänsten.
* **typen** måste vara `azureblob` .
* * * autentiseringsuppgifter ger lagrings kontots anslutnings sträng som `credentials.connectionString` parameter. Mer information finns i [ange autentiseringsuppgifter](#Credentials) nedan.
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

Mer information om API för att skapa DataSource finns i [skapa data källa](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>

#### <a name="how-to-specify-credentials"></a>Ange autentiseringsuppgifter

Du kan ange autentiseringsuppgifter för BLOB-behållaren på något av följande sätt:

* **Anslutnings sträng för hanterad identitet**: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;` 

  Den här anslutnings strängen kräver ingen konto nyckel, men du måste följa instruktionerna för [att konfigurera en anslutning till ett Azure Storage konto med hjälp av en hanterad identitet](search-howto-managed-identities-storage.md).

* **Anslutnings sträng för lagrings konto med fullständig åtkomst**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`

  Du kan hämta anslutnings strängen från Azure Portal genom att gå till bladet lagrings konto > inställningar > nycklar (för klassiska lagrings konton) eller inställningar > åtkomst nycklar (för Azure Resource Manager lagrings konton).

* Anslutnings sträng för **signatur för delad åtkomst för lagrings konto** (SAS):`BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl`

  SAS ska ha listan och Läs behörigheter för behållare och objekt (blobbar i detta fall).

* **Signatur för delad åtkomst för container**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl`

  SAS ska ha listan och Läs behörigheter för behållaren.

Mer information om signaturer för delad åtkomst för lagring finns i [använda signaturer för delad åtkomst](../storage/common/storage-sas-overview.md).

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

Mer information finns i [skapa index (REST API)](/rest/api/searchservice/create-index).

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

Mer information finns i [skapa indexerare (REST API)](/rest/api/searchservice/create-indexer). Mer information om hur du definierar indexerare scheman finns i [så här schemalägger du indexerare för Azure kognitiv sökning](search-howto-schedule-indexers.md).

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-blobs-are-indexed"></a>Hur blobbar indexeras

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

  * ** \_ lagrings \_ Sök väg för metadata** (EDM. String)-den fullständiga URI: n för blobben, inklusive lagrings kontot. Till exempel `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

  * ** \_ \_ innehålls \_ typ för metadata-lagring** (EDM. String) – innehålls typ som anges av den kod som du använde för att ladda upp blobben. Till exempel `application/octet-stream`.

  * **metadata \_ Storage \_ senast \_ ändrad** (EDM. DateTimeOffset)-den senaste ändrade tidsstämpeln för blobben. Azure Kognitiv sökning använder den här tidsstämpeln för att identifiera ändrade blobbar, för att undvika att indexera om allt efter den inledande indexeringen.

  * ** \_ lagrings \_ storlek för metadata** (EDM. Int64) – BLOB-storlek i byte.

  * **innehåll för metadata \_ Storage \_ \_ Md5** (EDM. String) – MD5-hash av BLOB-innehållet, om det är tillgängligt.

  * ** \_ SAS- \_ \_ token för metadata Storage** (EDM. String) – en tillfällig SAS-token som kan användas av [anpassade kunskaper](cognitive-search-custom-skill-interface.md) för att få åtkomst till blobben. Denna token ska inte lagras för senare användning eftersom den kan gå ut.

* Metadata-egenskaper som är speciella för varje dokument format extraheras till fälten som visas [här](#ContentSpecificMetadata).

Du behöver inte definiera fält för alla egenskaper som anges ovan i sökindexet – du behöver bara hämta de egenskaper du behöver för ditt program.

> [!NOTE]
> Ofta kommer fält namnen i det befintliga indexet att skilja sig från fält namnen som genereras under dokument extraheringen. Du kan använda **fält mappningar** för att mappa de egenskaps namn som tillhandahålls av Azure kognitiv sökning till fält namnen i Sök indexet. Du kommer att se ett exempel på fält mappningar som används nedan.
>

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>Definiera dokument nycklar och fält mappningar

I Azure Kognitiv sökning identifierar dokument nyckeln ett dokument unikt. Varje Sök index måste ha exakt ett nyckel fält av typen EDM. String. Nyckel fältet krävs för varje dokument som läggs till i indexet (det är egentligen det enda obligatoriska fältet).  

Du bör noga överväga vilka extraherade fält som ska mappas till nyckel fältet för ditt index. Kandidater är:

* ** \_ lagrings \_ namn för metadata** – detta kan vara en lämplig kandidat, men Observera att namnet inte är unikt, eftersom du kan ha blobbar med samma namn i olika mappar och 2) namnet får innehålla ogiltiga tecken i dokument nycklar, till exempel bindestreck. Du kan hantera ogiltiga tecken med hjälp av `base64Encode` [fält mappnings funktionen](search-indexer-field-mappings.md#base64EncodeFunction) – om du gör det måste du komma ihåg att koda dokument nycklar när du skickar dem till API-anrop som lookup. (I .NET kan du till exempel använda UrlTokenEncode- [metoden](/dotnet/api/system.web.httpserverutility.urltokenencode) för det syftet).

* ** \_ lagrings \_ Sök väg för metadata** – genom att använda den fullständiga sökvägen ser du till att sökvägen är unik, men sökvägen innehåller definitivt `/` [ogiltiga tecken i en dokument nyckel](/rest/api/searchservice/naming-rules).  Som ovan kan du välja att koda nycklar med hjälp av `base64Encode` [funktionen](search-indexer-field-mappings.md#base64EncodeFunction).

* Ett tredje alternativ är att lägga till en anpassad metadata-egenskap till Blobbarna. Det här alternativet kräver dock att BLOB-uppladdnings processen lägger till denna metadata-egenskap till alla blobbar. Eftersom nyckeln är en obligatorisk egenskap, kommer alla blobar som inte har denna egenskap att kunna indexeras.

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

Mer information finns i [fält mappningar och transformeringar](search-indexer-field-mappings.md).

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

## <a name="index-by-file-type"></a>Index efter filtyp

Du kan kontrol lera vilka blobbar som är indexerade och vilka som hoppas över.

### <a name="include-blobs-having-specific-file-extensions"></a>Inkludera blobbar med vissa fil namns tillägg

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

### <a name="exclude-blobs-having-specific-file-extensions"></a>Uteslut blobbar med vissa fil namns tillägg

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

## <a name="index-parts-of-a-blob"></a>Index delar av en BLOB

Du kan styra vilka delar av blobbar som indexeras med hjälp av `dataToExtract` konfigurations parametern. Det kan ha följande värden:

* `storageMetadata` -anger att endast [standard-BLOB-egenskaperna och användardefinierade metadata](../storage/blobs/storage-blob-container-properties-metadata.md) indexeras.
* `allMetadata` -anger att lagrings-metadata och de [innehålls typbaserade metadata](#ContentSpecificMetadata) som extraherats från BLOB-innehållet indexeras.
* `contentAndMetadata` -anger att alla metadata och text innehåll som extraheras från blobben indexeras. Detta är standardvärdet.

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

## <a name="index-from-multiple-sources"></a>Index från flera källor

Du kanske vill montera dokument från flera källor i ditt index. Du kanske till exempel vill koppla text från blobbar med andra metadata som lagras i Cosmos DB. Du kan även använda API för push-indexering tillsammans med olika indexerare för att skapa Sök dokument från flera delar.

För att detta ska fungera måste alla indexerare och andra komponenter godkänna dokument nyckeln. Mer information om det här ämnet finns i [index flera Azure-datakällor](./tutorial-multiple-data-sources.md) eller det här blogg inlägget, [kombinera dokument med andra data i Azure kognitiv sökning](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

## <a name="index-large-datasets"></a>Indexera stora data uppsättningar

Indexering av blobbar kan vara en tids krävande process. I de fall där du har miljon tals blobbar att indexera kan du påskynda indexeringen genom att partitionera data och använda flera indexerare för att bearbeta data parallellt. Så här kan du konfigurera detta:

* Partitionera dina data i flera BLOB-behållare eller virtuella mappar

* Konfigurera flera Azure Kognitiv sökning data källor, en per behållare eller mapp. Använd parametern för att peka på en BLOB-mapp `query` :

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

* Skapa en motsvarande indexerare för varje data källa. Alla indexerare kan peka på samma mål Sök index.  

* En Sök enhet i din tjänst kan köra en indexerare vid en specifik tidpunkt. Att skapa flera indexerare enligt beskrivningen ovan är bara användbart om de faktiskt körs parallellt. Om du vill köra flera indexerare parallellt kan du skala ut Sök tjänsten genom att skapa ett lämpligt antal partitioner och repliker. Om Sök tjänsten till exempel har 6 Sök enheter (till exempel 2 partitioner x 3 repliker) kan 6 indexerare köras samtidigt, vilket resulterar i en Six-vikt i index data flödet. Mer information om skalning och kapacitets planering finns i [Justera kapaciteten för en Azure kognitiv sökning-tjänst](search-capacity-planning.md).

<a name="DealingWithErrors"></a>

## <a name="handle-errors"></a>Hantera fel

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

Azure Kognitiv sökning begränsar storleken på blobbar som indexeras. De här gränserna dokumenteras i [tjänst begränsningar i Azure kognitiv sökning](./search-limits-quotas-capacity.md). Överändrade blobbar behandlas som standard som fel. Du kan dock fortfarande indexera lagrings metadata för överändrade blobbar om du anger `indexStorageMetadataOnlyForOversizedDocuments` parametern för konfiguration till True:

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

<a name="ContentSpecificMetadata"></a>

## <a name="content-type-specific-metadata-properties"></a>Egenskaper för innehålls typ-/regionsspecifika metadata

I följande tabell sammanfattas bearbetningen för varje dokument format och de metadata-egenskaper som har extraherats av Azure Kognitiv sökning beskrivs.

| Dokument format/innehålls typ | Extraherade metadata | Bearbetnings information |
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

## <a name="see-also"></a>Se även

* [Indexerare i Azure Cognitive Search](search-indexer-overview.md)
* [Förstå blobbar med AI](search-blob-ai-integration.md)
* [Översikt över BLOB-indexering](search-blob-storage-integration.md)