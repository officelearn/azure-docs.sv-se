---
title: Indexera Azure Blob storage-innehåll för fulltextsökning – Azure Search
description: Lär dig mer om att indexera Azure Blob Storage och extrahera text från dokument med Azure Search.
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 87dc1dab0670f69ff8c418be476986baec2821fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871357"
---
# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>Indexera dokument i Azure Blob Storage med Azure Search
Den här artikeln visar hur du använder Azure Search att indexera dokument (till exempel PDF: er och Microsoft Office-dokument och flera andra vanliga format) lagras i Azure Blob storage. Först förklarar den grunderna för att installera och konfigurera en blob-indexeraren. Sedan den erbjuder en djupare förklaring av beteenden och scenarier som du kan stöta på.

## <a name="supported-document-formats"></a>Dokumentformat som stöds
Blob-indexeraren extrahera text från följande dokumentformat:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Konfigurera blob-indexering
Du kan ställa in en Azure Blob Storage-indexeraren med:

* [Azure Portal](https://ms.portal.azure.com)
* Azure Search [REST-API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Search [.NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> Vissa funktioner (till exempel fältmappningar) ännu inte är tillgänglig i portalen och användas via programmering.
>

Här kan visar vi flödet med hjälp av REST-API.

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla
En datakälla anger vilka data som ska indexera, autentiseringsuppgifter som krävs för åtkomst till data och principer för att effektivt identifiera ändringar i data (nya, ändrade eller borttagna rader). En datakälla kan användas av flera indexerare i söktjänsten samma.

Datakällan måste ha följande nödvändiga egenskaper för blob-indexering:

* **namn på** är det unika namnet på datakällan i din söktjänst.
* **typ** måste vara `azureblob`.
* **autentiseringsuppgifter** ger anslutningssträngen för lagringskonto som den `credentials.connectionString` parametern. Se [hur du anger autentiseringsuppgifter](#Credentials) nedan för information.
* **behållaren** anger en behållare i ditt storage-konto. Som standard är alla blobbar i behållaren hämtningsbar. Om du bara vill index blobarna i en viss virtuell katalog kan du ange katalogen med det valfria **fråga** parametern.

Skapa en datakälla:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Mer information om API: et för skapa datakällan finns i [skapa Datasource](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Så här anger du autentiseringsuppgifter ####

Du kan ange autentiseringsuppgifterna för blob-behållaren i något av följande sätt:

- **Fullständig åtkomst lagringskontots anslutningssträng**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` Du kan hämta anslutningssträngen från Azure portal genom att gå till bladet för storage-konto > Inställningar > nycklar (för klassiska lagringskonton) eller inställningar > åtkomstnycklar (för Azure Resource Manager-lagringskonton).
- **Signatur för delad åtkomst av Storage-konto** (SAS) anslutningssträng: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` SAS bör ha i listan och läsbehörighet på behållare och objekt (BLOB-objekt i det här fallet).
-  **Signatur för delad åtkomst**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` SAS bör ha i listan och läsbehörighet till behållaren.

Mer information om storage delade åtkomstsignaturer, se [med signaturer för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Om du använder SAS-autentiseringsuppgifter, kommer du behöva uppdatera datakällans autentiseringsuppgifter med jämna mellanrum med förnyade signaturer för att förhindra att de gått ut. Om SAS-autentiseringsuppgifterna upphör att gälla indexeraren misslyckas med ett felmeddelande som liknar `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Steg 2: Skapa ett index
Indexet anger fälten i dokument, attribut, och andra konstruktioner som formar sökningen uppleva.

Här är hur du skapar ett index med en sökbara `content` -fält som innehåller den text som extraherats från BLOB-objekt:   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Mer information om hur du skapar index finns i [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>Steg 3: Skapa en indexerare
En indexerare ansluter en datakälla med en målsökindex och tillhandahåller ett schema för att automatisera datauppdateringen.

När index och datakälla har skapats är du redo att skapa indexeraren:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Den här indexeraren ska köras varannan timme (schemaintervallet är inställd på ”PT2H”). Ange intervallet till ”PT30M” för att köra en indexerare var 30: e minut. Den kortaste stöds är 5 minuter. Schemat är valfritt – om det utelämnas, en indexerare körs en gång när den har skapats. Du kan dock köra en indexerare på begäran när som helst.   

Mer information om API: et för skapa indexerare finns [skapa et indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="how-azure-search-indexes-blobs"></a>Hur Azure Search indexerar blobar

Beroende på den [indexerarkonfiguration](#PartsOfBlobToIndex), blob-indexeraren kan enbart indexera lagringsmetadata (användbart om du bara bryr dig om metadata och behöver inte att indexera innehållet i BLOB-objekt), lagring och metadata eller både metadata och textinnehåll. Som standard extraherar indexeraren både metadata och innehåll.

> [!NOTE]
> Som standard indexeras blobar med strukturerat innehåll, till exempel JSON eller CSV som ett enda segment med text. Om du vill indexera JSON- och CSV-blobar på ett strukturerat sätt se [indexera JSON-blobbar](search-howto-index-json-blobs.md) och [indexera CSV-blobbar](search-howto-index-csv-blobs.md) förhandsversionsfunktioner.
>
> Ett sammansatt eller inbäddade dokument (till exempel ett ZIP-arkiv eller ett Word-dokument med inbäddade Outlook e-post som innehåller bifogade filer) indexeras också som ett enskilt dokument.

* Det faktiska innehållet i dokumentet extraheras till ett strängfält som heter `content`.

> [!NOTE]
> Azure Search begränsar hur mycket text som extraheras beroende på prisnivå: 32000 tecken datanivå kostnadsfritt 64,000 för Basic och 4 miljoner för nivåerna Standard, Standard S2 och Standard S3. En varning ingår i indexeraren statussvar för trunkerade dokument.  

* Användardefinierade extraheras på blob, om sådana finns, metadataegenskaper ordagrant.
* Standard blob metadataegenskaper extraheras till följande fält:

  * **metadata\_storage\_namn** (Edm.String) - filnamnet för bloben. Till exempel om du har en blob-/my-container/my-folder/subfolder/resume.pdf värdet för det här fältet är `resume.pdf`.
  * **metadata\_storage\_sökväg** (Edm.String) - fullständiga URI för blob, inklusive lagringskontot. Till exempel, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metadata\_storage\_innehåll\_typ** (Edm.String) - innehållstyp som anges av den kod som du använde för att ladda upp blob. Till exempel `application/octet-stream`.
  * **metadata\_storage\_senaste\_ändras** (Edm.DateTimeOffset) - senast ändrad tidsstämpel för bloben. Den här tidsstämpeln använder Azure Search för att identifiera ändrade blobbar, för att undvika omindexering allt efter den inledande indexeringen.
  * **metadata\_storage\_storlek** (Edm.Int64) - blob-storlek i byte.
  * **metadata\_storage\_innehåll\_md5** (Edm.String) - MD5-hashen för blobbinnehåll, om det är tillgängligt.
* Metadataegenskaper som är specifika för varje dokumentformat extraheras till de fält som visas [här](#ContentSpecificMetadata).

Du behöver inte definiera fält för alla ovanstående egenskaper i sökindexet – bara avbilda egenskaper som du behöver för ditt program.

> [!NOTE]
> Namnen på i ditt befintliga index kommer ofta kan skilja sig från fältnamn som genereras under extrahering av dokumentet. Du kan använda **fältmappningar** att mappa egenskapsnamnen som tillhandahålls av Azure Search till fältnamn i sökindexet. Ett exempel på fält mappningar använder sig av nedan visas.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definiera dokumentet nycklar och fältmappningar
I Azure Search identifierar dokumentnyckeln ett dokument. Varje search-index måste ha exakt ett fält av typen Edm.String. Nyckelfältet krävs för varje dokument som läggs till indexet (det är faktiskt det enda obligatoriska fältet).  

Du bör noggrant vilka extraherade fält ska mappa till nyckelfältet för ditt index. Kandidaterna är:

* **metadata\_storage\_namn** – detta kan vara praktiskt, men Observera att (1) namn inte kan vara unikt, som du kan ha BLOB-objekt med samma namn i olika mappar och 2) namnet får bestå av tecken som är Ogiltig i dokumentet nycklar, till exempel bindestreck. Du kan hantera ogiltiga tecken med hjälp av den `base64Encode` [fältet mappning funktionen](search-indexer-field-mappings.md#base64EncodeFunction) – om du gör detta, Kom ihåg att koda dokumentet nycklar när skicka dem i API-anrop, till exempel sökning. (Till exempel i .NET du kan använda den [UrlTokenEncode metoden](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) för detta ändamål).
* **metadata\_storage\_sökväg** – med den fullständiga sökvägen garanterar unikhet, men sökvägen innehåller definitivt `/` tecken [ogiltig i en dokumentnyckeln](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Som ovan, har du möjlighet att koda nycklar med hjälp av den `base64Encode` [funktionen](search-indexer-field-mappings.md#base64EncodeFunction).
* Om inget av alternativen ovan fungerar för dig kan du lägga till en anpassad metadata-egenskap till blobarna. Det här alternativet, men kräver blob ladda uppladdningsprocessen att lägga till den metadata-egenskapen i alla blobbar. Eftersom nyckeln är en obligatorisk egenskap, misslyckas alla blobar som inte har den egenskapen som ska indexeras.

> [!IMPORTANT]
> Om det finns ingen explicit mappning för nyckelfältet i indexet, använder Azure Search automatiskt `metadata_storage_path` som nyckel och Base64-kodar nyckelvärden (andra alternativet ovan).
>
>

I det här exemplet vi väljer den `metadata_storage_name` fältet som dokumentnyckeln. Vi antar också ditt index har ett nyckelfält med namnet `key` och ett fält `fileSize` för att lagra dokumentstorlek. Ange följande fältmappningar när du skapar eller uppdaterar indexeraren för att ansluta saker upp efter behov:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Att göra detta helt och hållet, så du kan lägga till fält-mappningar och aktivera Base64-kodning av nycklar för en befintlig indexerare:

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2017-11-11
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

> [!NOTE]
> Mer information om fältmappningar finns [i den här artikeln](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Kontrollera vilka blobbar indexeras
Du kan styra vilka blobbar indexeras och som hoppas över.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Indexera endast blobbar med specifika filnamnstillägg
Du kan indexera endast blobbar med filnamnstillägg som du anger med hjälp av den `indexedFileNameExtensions` indexeraren konfigurationsparameter. Värdet är en sträng som innehåller en kommaavgränsad lista med filnamnstillägg (med en inledande punkt). Till exempel till indexet endast den. PDF och. DOCX-objekt, gör så här:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Exkludera blobar med specifika filnamnstillägg
Du kan utesluta blobar med specifika filnamnstillägg från att indexera med hjälp av den `excludedFileNameExtensions` konfigurationsparameter. Värdet är en sträng som innehåller en kommaavgränsad lista med filnamnstillägg (med en inledande punkt). Till exempel till indexet alla blobbar utom de med den. PNG och. JPEG-tillägg, gör så här:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Om både `indexedFileNameExtensions` och `excludedFileNameExtensions` parametrar finns kan Azure Search först tittar på `indexedFileNameExtensions`, sedan på `excludedFileNameExtensions`. Det innebär att om samma filnamnstillägg finns i båda listorna det kommer att uteslutas från indexering.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Kontrollera vilka delar av blobben som ska indexeras

Du kan styra vilka delar av blobbar indexeras med det `dataToExtract` konfigurationsparameter. Det kan ta följande värden:

* `storageMetadata` -Anger att endast den [standard blobbegenskaper och användardefinierade metadata](../storage/blobs/storage-properties-metadata.md) indexeras.
* `allMetadata` -Anger att storage-metadata och [innehållstyp specifika metadata](#ContentSpecificMetadata) extraheras från blob innehåll som ska indexeras.
* `contentAndMetadata` -Anger att alla metadata och textinnehåll som extraheras från blob indexeras. Detta är standardvärdet.

Till exempel för att indexera endast lagringsmetadata, använder du:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Använder blob-metadata för att styra hur blobbar indexeras

De konfigurationsparametrar som beskrivs ovan gäller för alla blobbar. Ibland kanske du vill styra hur *enskilda blobbar* indexeras. Du kan göra detta genom att lägga till följande egenskaper för blob-metadata och värden:

| Egenskapsnamn | Egenskapsvärde | Förklaring |
| --- | --- | --- |
| AzureSearch_Skip |”true” |Instruerar blob-indexeraren att helt och hållet blob. Extrahering av varken metadata eller innehåll görs. Detta är användbart när en viss blob misslyckas upprepade gånger och avbryter indexeringsprocessen. |
| AzureSearch_SkipContent |”true” |Det här är likvärdiga med `"dataToExtract" : "allMetadata"` inställningen beskrivs [ovan](#PartsOfBlobToIndex) begränsade till en viss blob. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Hantera fel

Som standard avbryts blob-indexeraren när den stöter på en blob med en innehållstyp (till exempel en bild). Naturligtvis kan du använda den `excludedFileNameExtensions` parametern för att hoppa över vissa typer av innehåll. Du kan dock behöva index blobar utan att känna till alla möjliga typer av innehåll i förväg. Om du vill fortsätta indexering när en innehållstyp påträffas, ange den `failOnUnsupportedContentType` konfigurationsparameter till `false`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

Azure Search kan inte avgöra innehållstypen för vissa BLOB eller kunde inte bearbeta ett dokument för annars stöds innehållstyp. Om du vill ignorera denna, ange den `failOnUnprocessableDocument` konfigurationsparameter till false:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Azure Search begränsar storleken på BLOB-objekt som ska indexeras. Dessa gränser dokumenteras i [tjänstbegränsningar i Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Stora blobbar behandlas som fel som standard. Dock kan du fortfarande indexera lagringsmetadata av stora blobar om du ställer in `indexStorageMetadataOnlyForOversizedDocuments` konfigurationsparameter till true: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Du kan också fortsätta indexera om fel inträffa när som helst bearbetning, vid parsning av BLOB-objekt eller när du lägger till dokument till ett index. Om du vill ignorera ett visst antal fel, ange den `maxFailedItems` och `maxFailedItemsPerBatch` konfigurationsparametrar för önskade värden. Exempel:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Inkrementell indexering och borttagning av identifiering
När du ställer in en blob-indexeraren ska köras enligt ett schema, den reindexes endast ändrade blobbarna, systemets blobens `LastModified` tidsstämpel.

> [!NOTE]
> Du behöver ange en princip för identifiering av ändring – inkrementella indexering aktiveras åt dig automatiskt.

Använd en ”mjuk borttagning”-metod för att stödja borttagning av dokument. Om du tar bort en gång blobarna tas motsvarande dokument inte bort från search-index. I stället använda följande steg:  

1. Lägga till en anpassad metadataegenskap till bloben som visar att Azure Search att det logiskt tas bort
2. Konfigurera en princip för mjuk borttagning på datakällan
3. När indexeraren har bearbetats blob (som visas efter status för indexeraren API) kan du fysiskt tar bort blobben

Till exempel följande princip anser att en blob som ska tas bort om den har en metadata-egenskap `IsDeleted` med värdet `true`:

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }   

## <a name="indexing-large-datasets"></a>Indexering av stora datauppsättningar

Indexera BLOB-objekt kan vara en tidskrävande process. I fall där du har miljontals BLOB-och index, kan du snabbt indexera genom att partitionera dina data och använda flera indexerare för att bearbeta data parallellt. Här är hur du kan konfigurera detta:

- Partitionera data i flera blob-behållare eller virtuella mappar
- Ställ in flera Azure Search-datakällor, en per behållaren eller mappen. För att peka till en blobbmapp, använda den `query` parameter:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Skapa en motsvarande indexerare för varje datakälla. Alla indexerare kan peka på samma målsökindex.  

- En sökenheten i din tjänst kan köra en indexerare vid en given tidpunkt. Flera indexerare är som beskrivs ovan bara användbar om de köras parallellt. Skala ut din söktjänst genom att skapa ett lämpligt antal partitioner och -repliker för att köra flera indexerare parallellt. Till exempel om söktjänsten har 6 search-enheter (till exempel 2 partitioner x 3 repliker), kan sedan 6 indexerare köras samtidigt, vilket resulterar i en six-fold ökning av indexeringsflöde. Läs mer om skalning och kapacitetsplanering i [skala resursnivåer för fråge- och indexeringsarbetsbelastningar i Azure Search](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indexera dokument tillsammans med relaterade data

Du kanske vill ”Assemblera” dokument från flera källor i ditt index. Du kanske exempelvis vill sammanfoga text från BLOB-objekt med andra metadata som lagras i Cosmos DB. Du kan även använda push-meddelandet indexering API tillsammans med olika indexerare för att bygga upp söka efter dokument från flera delar. 

För detta ska fungera måste alla indexerare och andra komponenter komma överens om dokumentnyckeln. En detaljerad genomgång finns i den här externa artikeln: [Kombinera dokument med andra data i Azure Search](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indexering oformaterad text 

Om alla blobbarna innehåller oformaterad text i samma kodning, kan du avsevärt förbättra indexering prestanda med hjälp av **text parsningsläge**. Om du vill använda text parsningsläge, ange den `parsingMode` konfigurationsegenskapen till `text`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Som standard den `UTF-8` kodning förväntas. Om du vill ange en annan kodning, använda den `encoding` konfigurationsegenskapen: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Egenskaper för innehåll typspecifika metadata
I följande tabell sammanfattas som bearbetas för respektive format och beskriver de metadataegenskaper som extraherats med hjälp av Azure Search.

| Dokumentformat / innehållstyp | Innehållstyp specifika metadataegenskaper | Bearbetar information |
| --- | --- | --- |
| HTML (`text/html`) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Tar bort HTML-kod och extrahera text |
| PDF (`application/pdf`) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Extrahera text, inklusive embedded dokument (exklusive bilder) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahera text, inklusive embedded dokument |
| DOKUMENT (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahera text, inklusive embedded dokument |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahera text, inklusive embedded dokument |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahera text, inklusive embedded dokument |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahera text, inklusive embedded dokument |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahera text, inklusive embedded dokument |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extrahera text, inklusive bifogade filer |
| ZIP (application/zip) |`metadata_content_type` |Extrahera text från alla dokument i arkivet |
| XML (application/xml) |`metadata_content_type`</br>`metadata_content_encoding`</br> |Remsans XML-kod och extrahera text |
| JSON (application/json) |`metadata_content_type`</br>`metadata_content_encoding` |Extrahera text<br/>OBS! Om du vill extrahera flera dokumentfält från en JSON-blob finns i [indexera JSON-blobbar](search-howto-index-json-blobs.md) information |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Extrahera text, inklusive bifogade filer |
| RTF (application/rtf) |`metadata_content_type`</br>`metadata_author`</br>`metadata_character_count`</br>`metadata_creation_date`</br>`metadata_page_count`</br>`metadata_word_count`</br> | Extrahera text|
| Oformaterad text (text/plain) |`metadata_content_type`</br>`metadata_content_encoding`</br> | Extrahera text|


## <a name="help-us-make-azure-search-better"></a>Hjälp oss att förbättra Azure Search
Om du har funktionsförfrågningar eller idéer om förbättringar kan berätta för oss på vår [UserVoice-webbplatsen](https://feedback.azure.com/forums/263029-azure-search/).
