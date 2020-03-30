---
title: Sök över Azure Blob-lagringsinnehåll
titleSuffix: Azure Cognitive Search
description: Lär dig hur du indexerar Azure Blob Storage och extraherar text från dokument med Azure Cognitive Search.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 5df1198e6681431738f886eb7c3ad549936eab1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067651"
---
# <a name="how-to-index-documents-in-azure-blob-storage-with-azure-cognitive-search"></a>Indexera dokument i Azure Blob Storage med Azure Cognitive Search

Den här artikeln visar hur du använder Azure Cognitive Search för att indexera dokument (till exempel PDF-filer, Microsoft Office-dokument och flera andra vanliga format) som lagras i Azure Blob-lagring. Först förklaras grunderna för att konfigurera och konfigurera en blob-indexerare. Sedan erbjuder det en djupare utforskning av beteenden och scenarier som du sannolikt kommer att stöta på.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Dokumentformat som stöds
Blob-indexeraren kan extrahera text från följande dokumentformat:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Ställa in blob-indexering
Du kan ställa in en Azure Blob Storage-indexerare med hjälp av:

* [Azure-portal](https://ms.portal.azure.com)
* AZURE Cognitive Search [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Cognitive Search [.NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> Vissa funktioner (till exempel fältmappningar) är ännu inte tillgängliga i portalen och måste användas programmässigt.
>

Här demonstrerar vi flödet med REST API.

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla
En datakälla anger vilka data som ska indexeras, autentiseringsuppgifter som behövs för att komma åt data och principer för att effektivt identifiera ändringar i data (nya, ändrade eller borttagna rader). En datakälla kan användas av flera indexerare i samma söktjänst.

För blob-indexering måste datakällan ha följande obligatoriska egenskaper:

* **namnet** är det unika namnet på datakällan i söktjänsten.
* **typ** måste `azureblob`vara .
* **autentiseringsuppgifterna** tillhandahåller anslutningssträngen för `credentials.connectionString` lagringskonto som parameter. Mer information [finns i Så här anger du autentiseringsuppgifter](#Credentials) nedan.
* **anger** en behållare i ditt lagringskonto. Som standard kan alla blobbar i behållaren hämtas. Om du bara vill indexera blobbar i en viss virtuell **query** katalog kan du ange den katalogen med hjälp av den valfria frågeparametern.

Så här skapar du en datakälla:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Mer information om API:et skapa datakälla finns i [Skapa datakälla](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Så här anger du autentiseringsuppgifter ####

Du kan ange autentiseringsuppgifterna för blob-behållaren på något av följande sätt:

- **Anslutningssträng för lagringskonto**med fullständig åtkomst: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` Du kan hämta anslutningssträngen från Azure-portalen genom att navigera till lagringskontobladet > Inställningar > nycklar (för klassiska lagringskonton) eller Inställningar > Åtkomstnycklar (för Azure Resource Manager-lagringskonton).
- **SAS-anslutningssträng (Shared Access** `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` Signature) för lagringskontot: SAS ska ha listan och läsbehörighet för behållare och objekt (blobbar i det här fallet).
-  **Signature för delad åtkomst för behållare**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` SAS ska ha listan och läsbehörigheten för behållaren.

Mer information om signaturer för delad åtkomst för lagring finns i [Använda signaturer för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Om du använder SAS-autentiseringsuppgifter måste du uppdatera datakällautentiseringsuppgifterna regelbundet med förnyade signaturer för att förhindra att de upphör att gälla. Om SAS-autentiseringsuppgifterna upphör att gälla misslyckas indexeraren med ett felmeddelande som liknar `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Steg 2: Skapa ett index
Indexet anger fälten i ett dokument, attribut och andra konstruktioner som formar sökupplevelsen.

Så här skapar du ett index `content` med ett sökbart fält för att lagra texten som extraherats från blobbar:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Mer information om hur du skapar index finns i [Skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>Steg 3: Skapa en indexerare
En indexerare ansluter en datakälla med ett målsökindex och tillhandahåller ett schema för att automatisera datauppdateringen.

När indexet och datakällan har skapats är du redo att skapa indexeraren:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Den här indexeraren körs varannan timme (schemaintervallet är inställt på "PT2H"). Om du vill köra en indexerare var 30:e minut ställer du in intervallet på "PT30M". Det kortaste intervallet som stöds är 5 minuter. Schemat är valfritt - om det utelämnas körs en indexerare bara en gång när det skapas. Du kan dock köra en indexerare på begäran när som helst.   

Mer information om API:et skapa indexerare finns i [Skapa indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Mer information om hur du definierar indexeringsscheman finns i [Så här schemalägger du indexerare för Azure Cognitive Search](search-howto-schedule-indexers.md).

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-azure-cognitive-search-indexes-blobs"></a>Så här indexerar Azure Cognitive Search blobbar

Beroende på [indexeringskonfigurationen](#PartsOfBlobToIndex)kan blob-indexeraren endast indexera lagringsmetadata (användbart när du bara bryr dig om metadata och inte behöver indexera innehållet i blobbar), lagrings- och innehållsmetadata eller både metadata och textinnehåll. Som standard extraherar indexeraren både metadata och innehåll.

> [!NOTE]
> Som standard indexeras blobbar med strukturerat innehåll som JSON eller CSV som ett enda textsegment. Om du vill indexera JSON- och CSV-blobbar på ett strukturerat sätt läser du [Indexera JSON-blobbar](search-howto-index-json-blobs.md) och [indexera CSV-blobbar](search-howto-index-csv-blobs.md) för mer information.
>
> Ett sammansatt eller inbäddat dokument (till exempel ett ZIP-arkiv eller ett Word-dokument med inbäddat Outlook-e-postmeddelande som innehåller bifogade filer) indexeras också som ett enda dokument.

* Dokumentets textinnehåll extraheras till ett strängfält `content`med namnet .

> [!NOTE]
> Azure Cognitive Search begränsar hur mycket text den extraherar beroende på prisnivån: 32 000 tecken för den kostnadsfria nivån, 64 000 för Basic, 4 miljoner standard, 8 miljoner för Standard S2 och 16 miljoner för Standard S3. En varning ingår i indexerarens statussvar för trunkerade dokument.  

* Användarspecificerade metadataegenskaper som finns på blobben, om sådana finns, extraheras ordagrant. Observera att detta kräver att ett fält definieras i indexet med samma namn som metadatanyckeln för bloben. Om din blob till exempel har `Sensitivity` en `High`metadatanyckel med värde `Sensitivity` bör du definiera ett fält som `High`heter i sökindexet och det fylls i med värdet .
* Standardmetadataegenskaper för blob extraheras i följande fält:

  * **metadatalagringsnamn\_\_** (Edm.String) - filnamnet på blobben. Om du till exempel har en blob /my-container/my-folder/subfolder/resume.pdf `resume.pdf`är värdet för det här fältet .
  * **metadatalagringssökvägen\_\_** (Edm.String) – den fullständiga URI-för bloben, inklusive lagringskontot. Till exempel, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **\_innehållstyp\_för metadatalagring\_** (Edm.String) - innehållstyp som anges av koden som du använde för att ladda upp blobben. Till exempel `application/octet-stream`.
  * **\_metadatalagring\_\_senast ändrad** (Edm.DateTimeOffset) - senast ändrad tidsstämpel för bloben. Azure Cognitive Search använder den här tidsstämpeln för att identifiera ändrade blobbar för att undvika att indexera om allt efter den första indexeringen.
  * **metadatalagringsstorlek\_\_** (Edm.Int64) - blobstorlek i byte.
  * **metadatalagringsinnehåll\_\_\_md5** (Edm.String) - MD5-hash av blob-innehållet, om tillgängligt.
  * **\_metadatalagring\_sas\_token** (Edm.String) - En tillfällig SAS-token som kan användas av [anpassade kunskaper](cognitive-search-custom-skill-interface.md) för att få åtkomst till bloben. Den här token bör inte lagras för senare användning eftersom den kan upphöra att gälla.

* Metadataegenskaper som är specifika för varje dokumentformat extraheras i fälten som visas [här](#ContentSpecificMetadata).

Du behöver inte definiera fält för alla ovanstående egenskaper i sökindexet – bara fånga de egenskaper du behöver för ditt program.

> [!NOTE]
> Ofta skiljer sig fältnamnen i det befintliga indexet från de fältnamn som genereras under dokumentextrahering. Du kan använda **fältmappningar** för att mappa de egenskapsnamn som tillhandahålls av Azure Cognitive Search till fältnamnen i sökindexet. Du kommer att se ett exempel på fältmappningar som används nedan.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definiera dokumentnycklar och fältmappningar
I Azure Cognitive Search identifierar dokumentnyckeln unikt ett dokument. Varje sökindex måste ha exakt ett nyckelfält av typen Edm.String. Nyckelfältet krävs för varje dokument som läggs till i indexet (det är faktiskt det enda obligatoriska fältet).  

Du bör noga överväga vilket extraherat fält som ska mappas till nyckelfältet för indexet. Kandidaterna är:

* **metadatalagringsnamn\_\_** - det kan vara en praktisk kandidat, men observera att 1) namnen kanske inte är unika, eftersom du kan ha blobbar med samma namn i olika mappar och 2) kan namnet innehålla tecken som är ogiltiga i dokumentnycklar, till exempel streck. Du kan hantera ogiltiga `base64Encode` tecken med hjälp av [fältmappningsfunktionen](search-indexer-field-mappings.md#base64EncodeFunction) - om du gör det, kom ihåg att koda dokumentnycklar när du skickar dem i API-anrop som Uppslag. (I .NET kan du till exempel använda [metoden UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) för detta ändamål).
* **metadatalagringssökvägen\_\_** – med hjälp av den fullständiga sökvägen säkerställer unikhet, men sökvägen innehåller definitivt `/` tecken som är [ogiltiga i en dokumentnyckel](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Som ovan har du möjlighet att koda nycklarna `base64Encode` med hjälp av [funktionen](search-indexer-field-mappings.md#base64EncodeFunction).
* Om inget av alternativen ovan fungerar för dig kan du lägga till en anpassad metadataegenskap i blobbar. Det här alternativet kräver dock att din blob-uppladdningsprocess lägger till den metadataegenskapen i alla blobbar. Eftersom nyckeln är en obligatorisk egenskap kommer alla blobbar som inte har den egenskapen att indexeras.

> [!IMPORTANT]
> Om det inte finns någon explicit mappning för nyckelfältet `metadata_storage_path` i indexet, azure cognitive search automatiskt använder som nyckel och base-64 kodar nyckelvärden (det andra alternativet ovan).
>
>

I det här exemplet ska `metadata_storage_name` vi välja fältet som dokumentnyckel. Anta också att indexet har ett `key` namn med `fileSize` ett nyckelfält och ett fält för lagring av dokumentstorleken. Om du vill koppla upp saker och ting anger du följande fältmappningar när du skapar eller uppdaterar indexeraren:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

För att sammanföra allt detta kan du lägga till fältmappningar och aktivera bas-64-kodning av nycklar för en befintlig indexerare:

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2019-05-06
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
> Mer information om fältmappningar finns i [den här artikeln](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Styra vilka blobbar som indexeras
Du kan styra vilka blobbar som ska indexeras och vilka som hoppas över.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Indexera endast blobbar med specifika filtillägg
Du kan bara indexera blobbar med de filnamnstillägg som du anger med hjälp av konfigurationsparametern `indexedFileNameExtensions` indexerare. Värdet är en sträng som innehåller en kommaavgränsad lista över filnamnstillägg (med en inledande punkt). Om du till exempel bara vill indexera . PDF och . DOCX blobbar, gör så här:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Exkludera blobbar med specifika filtillägg
Du kan utesluta blobbar med specifika filnamnstillägg `excludedFileNameExtensions` från indexering med hjälp av konfigurationsparametern. Värdet är en sträng som innehåller en kommaavgränsad lista över filnamnstillägg (med en inledande punkt). Om du till exempel vill indexera alla blobbar utom de med . PNG och . JPEG-tillägg gör så här:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Om `indexedFileNameExtensions` både `excludedFileNameExtensions` och parametrar finns tittar Azure `indexedFileNameExtensions`Cognitive `excludedFileNameExtensions`Search först på , sedan på . Detta innebär att om samma filnamnstillägg finns i båda listorna, kommer det att uteslutas från indexering.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Styra vilka delar av blobben som indexeras

Du kan styra vilka delar av blobbar `dataToExtract` som indexeras med konfigurationsparametern. Det kan ta följande värden:

* `storageMetadata`- anger att endast [standardblolobegenskaperna och användarspecificerade metadata](../storage/blobs/storage-properties-metadata.md) indexeras.
* `allMetadata`- anger att lagringsmetadata och [de innehållstypsspecifika metadata](#ContentSpecificMetadata) som extraherats från blob-innehållet indexeras.
* `contentAndMetadata`- anger att alla metadata och textinnehåll som extraherats från blobben indexeras. Detta är standardvärdet.

Om du till exempel bara vill indexera lagringsmetadata använder du:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Använda blobmetadata för att styra hur blobbar indexeras

De konfigurationsparametrar som beskrivs ovan gäller för alla blobbar. Ibland kanske du vill styra hur *enskilda blobbar* indexeras. Du kan göra detta genom att lägga till följande egenskaper och värden för blobmetadata:

| Egenskapsnamn | Egenskapsvärde | Förklaring |
| --- | --- | --- |
| AzureSearch_Skip |"Sant" |Instruerar blob-indexeraren att helt hoppa över blobben. Försök med att inte ta bort metadata eller innehåll. Detta är användbart när en viss blob misslyckas upprepade gånger och avbryter indexeringsprocessen. |
| AzureSearch_SkipContent |"Sant" |Detta motsvarar `"dataToExtract" : "allMetadata"` inställningen som beskrivs [ovan](#PartsOfBlobToIndex) begränsad till en viss blob. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Hantera fel

Som standard stoppas blob-indexeraren så fort den stöter på en blob med en innehållstyp som inte stöds (till exempel en bild). Du kan naturligtvis `excludedFileNameExtensions` använda parametern för att hoppa över vissa innehållstyper. Du kan dock behöva indexera blobbar utan att känna till alla möjliga innehållstyper i förväg. Om du vill fortsätta indexera när en innehållstyp som inte stöds påträffas ställer du in `failOnUnsupportedContentType` konfigurationsparametern på: `false`

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

För vissa blobbar kan Azure Cognitive Search inte avgöra innehållstypen eller inte kunna bearbeta ett dokument av innehållstyp som stöds på annat sätt. Om du vill ignorera `failOnUnprocessableDocument` det här felläget ställer du in konfigurationsparametern på false:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Azure Cognitive Search begränsar storleken på blobbar som är indexerade. Dessa gränser dokumenteras i [Servicegränser i Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Överdimensionerade blobbar behandlas som fel som standard. Du kan dock fortfarande indexera lagringsmetadata `indexStorageMetadataOnlyForOversizedDocuments` för överdimensionerade blobbar om du anger konfigurationsparametern till true: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Du kan också fortsätta indexera om fel inträffar vid någon bearbetningspunkt, antingen när du parsar blobbar eller samtidigt lägger till dokument i ett index. Om du vill ignorera ett visst `maxFailedItems` `maxFailedItemsPerBatch` antal fel ställer du in och konfigurationsparametrar till önskade värden. Ett exempel:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Inkrementell identifiering av indexering och borttagning

När du ställer in en blob-indexerare för att köras enligt ett schema indexerar `LastModified` den bara om de ändrade blobbar, enligt blobens tidsstämpel.

> [!NOTE]
> Du behöver inte ange en ändringsidentifieringsprincip – inkrementell indexering är aktiverat för dig automatiskt.

Om du vill stödja att ta bort dokument använder du en "mjuk borttagning"-metod. Om du tar bort blobbar direkt tas inte motsvarande dokument bort från sökindexet.

Det finns två sätt att implementera metoden för mjuk borttagning. Båda beskrivs nedan.

### <a name="native-blob-soft-delete-preview"></a>Inbyggd mjuk borttagning av blob (förhandsgranskning)

> [!IMPORTANT]
> Stöd för inbyggd blob mjuk borttagning är i förhandsgranskning. Förhandsversionsfunktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API-versionen 2019-05-06-Preview](https://docs.microsoft.com/azure/search/search-api-preview) innehåller den här funktionen. Det finns för närvarande inget stöd för portal eller .NET SDK.

> [!NOTE]
> När du använder den inbyggda principen för mjuk borttagning av blob måste dokumentnycklarna för dokumenten i indexet antingen vara en blob-egenskap eller blobmetadata.

I den här metoden ska du använda den [inbyggda blob mjuk borttagningsfunktionen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) som erbjuds av Azure Blob storage. Om den inbyggda mjuk borttagning av blob är aktiverad på ditt lagringskonto har datakällan en inbyggd mjuk borttagningsprincipuppsättning och indexeraren hittar en blob som har övergått till ett mjukt borttaget tillstånd, indexeraren tar bort dokumentet från indexet. Den inbyggda principen för mjuk borttagning av blob stöds inte vid indexering av blobbar från Azure Data Lake Storage Gen2.

Använd följande steg:
1. Aktivera [inbyggd mjuk borttagning för Azure Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete). Vi rekommenderar att du ställer in bevarandeprincipen på ett värde som är mycket högre än indexerarintervallschemat. På så sätt om det finns ett problem som kör indexeraren eller om du har ett stort antal dokument att indexera, finns det gott om tid för indexeraren att så småningom bearbeta mjuka borttagna blobbar. Azure Cognitive Search-indexerare tar bara bort ett dokument från indexet om det bearbetar bloben medan den är i ett mjukt borttaget tillstånd.
1. Konfigurera en inbyggd princip för identifiering av mjuk borttagning av blob på datakällan. Ett exempel på detta visas nedan. Eftersom den här funktionen är i förhandsversion måste du använda rest-API:et för förhandsversionen.
1. Kör indexeraren eller ange att indexeraren ska köras enligt ett schema. När indexeraren körs och bearbetar blobben tas dokumentet bort från indexet.

    ```
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2019-05-06-Preview
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

#### <a name="reindexing-undeleted-blobs"></a>Indexera om odelegerade blobbar

Om du tar bort en blob från Azure Blob-lagring med inbyggd mjuk borttagning aktiverad på ditt lagringskonto kommer bloben att övergå till ett mjukt borttaget tillstånd som ger dig möjlighet att ta bort den bloben inom kvarhållningsperioden. När en Azure Cognitive Search-datakälla har en inbyggd blob mjuk borttagningsprincip och indexeraren bearbetar en mjuk borttagen blob tas det bort från indexet. Om blobben senare inte har löpde bort indexeraren indexerar du inte alltid om den bloben. Detta beror på att indexeraren bestämmer vilka blobbar `LastModified` som ska indexeras baserat på blobens tidsstämpel. När en mjuk borttagen blob `LastModified` är odelegerad uppdateras inte tidsstämpeln, så om `LastModified` indexeraren redan har bearbetat blobbar med tidsstämplar nyare än den odelade blobben indexerar den inte den odelade bloben. Om du vill vara säker på att en odelegerad blob indexeras `LastModified` om måste du uppdatera blobens tidsstämpel. Ett sätt att göra detta är genom att spara metadata för den bloben. Du behöver inte ändra metadata men om du sparar metadata uppdaterar `LastModified` du blobens tidsstämpel så att indexeraren vet att den behöver indexera om den här bloben.

### <a name="soft-delete-using-custom-metadata"></a>Mjuk borttagning med anpassade metadata

I den här metoden ska du använda en blobs metadata för att ange när ett dokument ska tas bort från sökindexet.

Använd följande steg:

1. Lägg till ett anpassat nyckelvärdepar för metadata i bloben för att indikera för Azure Cognitive Search att det tas bort logiskt.
1. Konfigurera en princip för identifiering av en mjuk borttagningskolumn på datakällan. Ett exempel på detta visas nedan.
1. När indexeraren har bearbetat blobben och tagit bort dokumentet från indexet kan du ta bort bloben för Azure Blob-lagring.

Följande princip anser till exempel att en blob tas `IsDeleted` bort om `true`den har en metadataegenskap med värdet :

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2019-05-06
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

#### <a name="reindexing-undeleted-blobs"></a>Indexera om odelegerade blobbar

Om du anger en princip för identifiering av mjuk borttagning av kolumn på datakällan och sedan lägger till anpassade metadata i en blob med markörvärdet och kör sedan indexeraren, tar indexeraren bort dokumentet från indexet. Om du vill indexera om dokumentet ändrar du bara metadatavärdet för mjuk borttagning för den blobben och kör indexeraren igen.

## <a name="indexing-large-datasets"></a>Indexera stora datamängder

Indexering blobbar kan vara en tidskrävande process. I de fall där du har miljontals blobbar att indexera kan du snabba upp indexeringen genom att partitionera dina data och använda flera indexerare för att bearbeta data parallellt. Så här kan du ställa in det här:

- Partitionera dina data i flera blob-behållare eller virtuella mappar
- Konfigurera flera Azure Cognitive Search-datakällor, en per behållare eller mapp. Om du vill peka på `query` en blob-mapp använder du parametern:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Skapa en motsvarande indexerare för varje datakälla. Alla indexerare kan peka på samma målsökindex.  

- En sökenhet i tjänsten kan köra en indexerare vid en given tidpunkt. Det är bara användbart att skapa flera indexerare enligt beskrivningen ovan om de körs parallellt. Om du vill köra flera indexerare parallellt skalar du ut söktjänsten genom att skapa ett lämpligt antal partitioner och repliker. Om söktjänsten till exempel har 6 sökenheter (till exempel 2 partitioner x 3 repliker) kan 6 indexerare köras samtidigt, vilket resulterar i en sexfaldig ökning av indexeringsdataflödet. Mer information om skalning och kapacitetsplanering finns i [Skala resursnivåer för fråge- och indexeringsarbetsbelastningar i Azure Cognitive Search](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indexera dokument tillsammans med relaterade data

Du kanske vill "montera" dokument från flera källor i indexet. Du kanske till exempel vill koppla text från blobbar med andra metadata som lagras i Cosmos DB. Du kan även använda push indexering API tillsammans med olika indexerare för att bygga upp sökdokument från flera delar. 

För att detta ska fungera måste alla indexerare och andra komponenter komma överens om dokumentnyckeln. Mer information om det här avsnittet finns [i Index flera Azure-datakällor](https://docs.microsoft.com/azure/search/tutorial-multiple-data-sources). En detaljerad genomgång finns i den här externa artikeln: [Kombinera dokument med andra data i Azure Cognitive Search](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indexera oformaterad text 

Om alla blobbar innehåller oformaterad text i samma kodning kan du avsevärt förbättra indexeringsprestanda genom att använda **texttolkningsläge**. Om du vill använda texttolsningsläge ställer du in konfigurationsegenskapen `parsingMode` på: `text`

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Som standard `UTF-8` antas kodningen. Om du vill ange en annan `encoding` kodning använder du konfigurationsegenskapen: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Egenskaper för innehållstypspecifika metadata
I följande tabell sammanfattas bearbetning som gjorts för varje dokumentformat och de metadataegenskaper som extraheras av Azure Cognitive Search beskrivs.

| Dokumentformat/innehållstyp | Specifika metadataegenskaper för innehållstyp | Bearbeta detaljer |
| --- | --- | --- |
| HTML (text/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Strip HTML-markering och extrahera text |
| PDF (ansökan/pdf) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Extrahera text, inklusive inbäddade dokument (utom bilder) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahera text, inklusive inbäddade dokument |
| DOC (ansökan/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahera text, inklusive inbäddade dokument |
| DOCM (application/vnd.ms-word.document.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahera text, inklusive inbäddade dokument |
| WORD XML (program/vnd.ms-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Band-XML-markering och extrahera text |
| WORD 2003 XML (application/vnd.ms-wordml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Band-XML-markering och extrahera text |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahera text, inklusive inbäddade dokument |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahera text, inklusive inbäddade dokument |
| XLSM (application/vnd.ms-excel.sheet.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahera text, inklusive inbäddade dokument |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahera text, inklusive inbäddade dokument |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahera text, inklusive inbäddade dokument |
| PPTM (application/vnd.ms-powerpoint.presentation.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahera text, inklusive inbäddade dokument |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extrahera text, inklusive bifogade filer. `metadata_message_to_email`och `metadata_message_cc_email` `metadata_message_bcc_email` är strängsamlingar, resten av fälten är strängar.|
| ODT (application/vnd.oasis.opendocument.text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahera text, inklusive inbäddade dokument |
| ODS (application/vnd.oasis.opendocument.spreadsheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahera text, inklusive inbäddade dokument |
| ODP (application/vnd.oasis.opendocument.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Extrahera text, inklusive inbäddade dokument |
| ZIP (applicering/zip) |`metadata_content_type` |Extrahera text från alla dokument i arkivet |
| GZ (ansökan/gzip) |`metadata_content_type` |Extrahera text från alla dokument i arkivet |
| EPUB (ansökan/epub+zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Extrahera text från alla dokument i arkivet |
| XML (program/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Band-XML-markering och extrahera text |
| JSON (ansökan/json) |`metadata_content_type`<br/>`metadata_content_encoding` |Extrahera text<br/>Obs! [Indexing JSON blobs](search-howto-index-json-blobs.md) |
| EML (meddelande/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Extrahera text, inklusive bifogade filer |
| RTF (ansökan/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Extrahera text|
| Oformaterad text (text/oformaterad) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Extrahera text|


## <a name="help-us-make-azure-cognitive-search-better"></a>Hjälp oss att göra Azure Cognitive Search bättre
Om du har funktionsförfrågningar eller idéer för förbättringar, låt oss veta på vår [UserVoice webbplats](https://feedback.azure.com/forums/263029-azure-search/).
