---
title: Indexering Azure Blob Storage med Azure Search
description: Lär dig att indexera Azure Blob Storage och extrahera text från dokument med Azure Search
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: eugenesh
ms.openlocfilehash: 77fac23286d536903e32140b554304e72c16097f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>Indexera dokument i Azure Blob Storage med Azure Search
Den här artikeln visar hur du använder Azure Search ska indexera dokument (till exempel PDF-filer, Microsoft Office-dokument och flera andra vanliga format) lagras i Azure Blob storage. Först förklarar det grunderna för att installera och konfigurera en indexerare blob. Sedan den erbjuder en ingående undersökning av beteenden och scenarier troligen kommer att stöta på.

## <a name="supported-document-formats"></a>Dokumentets format som stöds
Blob-indexeraren kan extrahera text från följande dokumentformat:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Konfigurera blob-indexering
Du kan konfigurera en Azure Blob Storage indexeraren med:

* [Azure Portal](https://ms.portal.azure.com)
* Azure Search [REST-API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Search [.NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> Vissa funktioner (till exempel fältmappningar) är ännu inte tillgängliga i portalen och måste användas via programmering.
>
>

Här kan visar vi flödet med hjälp av REST-API.

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla
En datakälla anger vilka data som ska indexera, autentiseringsuppgifter som krävs för åtkomst till data och principer för att effektivt identifiera ändringar i data (nya, ändrade eller borttagna rader). En datakälla kan användas av flera indexerare i samma search-tjänsten.

Datakällan måste ha följande nödvändiga egenskaper för blob-indexering:

* **namnet** är det unika namnet på datakällan i din söktjänst.
* **typen** måste vara `azureblob`.
* **autentiseringsuppgifter** lagringsanslutningssträngen för kontot som innehåller den `credentials.connectionString` parameter. Se [så här anger du autentiseringsuppgifter](#Credentials) nedan för information.
* **behållaren** anger en behållare i ditt lagringskonto. Som standard är alla blobbar i behållaren strängfält. Om du bara vill index blobbar i en viss virtuell katalog kan du ange katalogen med det valfria **frågan** parameter.

Skapa en datakälla:

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Mer information om skapa Datasource-API finns [skapa Datasource](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Så här anger du autentiseringsuppgifter ####

Du kan ange autentiseringsuppgifter för blob-behållaren i något av följande sätt:

- **Fullständig åtkomst lagringsanslutningssträng för kontot**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`. Du kan hämta anslutningssträngen från Azure portal genom att gå till bladet storage-konto > Inställningar > nycklar (för klassiska lagringskonton) eller inställningar > åtkomstnycklar (för Azure Resource Manager storage-konton).
- **Storage-konto signatur för delad åtkomst** (SAS)-anslutningssträng: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` i SAS bör ha i listan och läsbehörighet på behållare och objekt (BLOB-objekt i det här fallet).
-  **Signatur för delad åtkomst i behållaren**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` i SAS bör ha i listan och läsbehörighet till behållaren.

Mer information om lagring delad åtkomst till signaturer finns i avsnittet [använder signaturer för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Om du använder SAS-autentiseringsuppgifter, behöver du uppdatera autentiseringsuppgifterna för datakällan med förnyat signaturer för att förhindra att de gått ut. Om SAS-autentiseringsuppgifter ut indexeraren misslyckas med felmeddelandet liknar `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Steg 2: Skapa ett index
Indexet anger vilka fält i ett dokument, attribut, och andra konstruktioner som formar sökningen upplevelse.

Så här skapar du ett index med en sökbar `content` -fält som innehåller den text som extraheras från BLOB:   

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Mer information om hur du skapar index finns [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>Steg 3: Skapa en indexerare
En indexerare ansluter en datakälla med ett mål sökindex och ger ett schema för att automatisera datauppdateringen.

När index och datakälla har skapats är du redo att skapa indexeraren:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Indexeraren körs varannan timme (schemaintervallet är inställt på ”PT2H”). Ställa in intervall för ”PT30M” om du vill köra en indexerare var 30: e minut. Den kortaste stöds är 5 minuter. Schemat är valfritt - om detta utelämnas, en indexerare körs bara en gång när den skapas. Du kan dock köra en indexerare på begäran när som helst.   

Mer information om API: et för skapa indexeraren kolla [skapa indexeraren](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="how-azure-search-indexes-blobs"></a>Hur Azure Search index blobbar

Beroende på den [indexeraren configuration](#PartsOfBlobToIndex), blob indexeraren kan indexera lagring metadata (användbart när du bara är intresserad metadata och behöver inte att indexera innehållet i BLOB), lagring och innehåll metadata eller metadata och textinnehåll. Som standard extraheras indexeraren både i metadata och innehåll.

> [!NOTE]
> Som standard indexeras blobbar med strukturerat innehåll, till exempel JSON eller CSV som en enda del av texten. Om du vill indexera JSON och CSV blobbar på ett strukturerat sätt se [indexering JSON-blobbar](search-howto-index-json-blobs.md) och [indexering CSV-blobbar](search-howto-index-csv-blobs.md) Förhandsgranska funktioner.
>
> Ett sammansatt eller inbäddade dokument (t.ex ett ZIP-arkiv eller ett Word-dokument med inbäddade Outlook e-postmeddelande som innehåller bifogade filer) indexeras också som ett enskilt dokument.

* Det faktiska innehållet i dokumentet hämtas till en string-fält med namnet `content`.

> [!NOTE]
> Azure Search begränsar hur mycket text extraheras beroende på prisnivå: 32000 tecken kostnadsfritt nivån, 64,000 för grundläggande och 4 miljoner för Standard-, Standard S2- och Standard S3 nivåer. En varning visas i indexeraren Statussvaret för trunkerat dokument.  

* Användaren har angett metadataegenskaper finns på blob, extraheras eventuella ordagrant.
* Standard-blob metadataegenskaper extraheras i följande fält:

  * **metadata\_lagring\_namn** (Edm.String) - namnet på blob. Till exempel om du har en blob-/my-container/my-folder/subfolder/resume.pdf värdet för det här fältet är `resume.pdf`.
  * **metadata\_lagring\_sökväg** (Edm.String) - fullständiga URI blob, inklusive storage-konto. Till exempel, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metadata\_lagring\_innehåll\_typ** (Edm.String) - innehållstyp som anges av den kod som du använde för att överföra blobben. Till exempel `application/octet-stream`.
  * **metadata\_lagring\_senaste\_ändrade** (Edm.DateTimeOffset) - ändrades senast tidsstämpel för blob. Azure Search använder den här tidsstämpel för att identifiera ändrade BLOB för att undvika indexeringen allt efter den inledande indexeringen.
  * **metadata\_lagring\_storlek** (Edm.Int64) - blob-storlek i byte.
  * **metadata\_lagring\_innehåll\_md5** (Edm.String) - MD5-hash för blobinnehåll, om de är tillgängliga.
* Metadata-egenskaper som är specifika för varje dokumentformat extraheras i fälten som anges [här](#ContentSpecificMetadata).

Du behöver inte definiera fält för alla ovanstående egenskaper i din sökindex – bara samla in de egenskaper som du behöver för ditt program.

> [!NOTE]
> Ofta är fältnamn i ditt befintliga index olika från fältnamnen som genereras under Extraheringen av dokumentet. Du kan använda **fältet mappningar** att mappa egenskapsnamn som tillhandahålls av Azure Search till fältnamn i search-index. Ett exempel på fält mappningar använda nedan visas.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definiera dokumentet nycklar och fältmappningar
I Azure Search identifierar dokumentnyckeln ett dokument. Varje sökindex måste ha exakt ett fält av typen Edm.String. Nyckelfältet krävs för varje dokument som läggs till i indexet (det är faktiskt endast obligatoriskt fält).  

Du bör noggrant vilka extraherade fältet ska mappa till nyckelfältet för ditt index. Kandidater är:

* **metadata\_lagring\_namn** – detta kan vara praktiskt, men Observera att 1) namn inte kan vara unika, som du kanske har blobbar med samma namn i olika mappar och 2) namnet kan innehålla tecken Ogiltig i dokumentet nycklar, till exempel bindestreck. Du kan hantera ogiltiga tecken med hjälp av den `base64Encode` [fältet mappning funktionen](search-indexer-field-mappings.md#base64EncodeFunction) – om du gör detta, Kom ihåg att koda dokumentet nycklar när du skickar dem i API-anrop, till exempel sökning. (Till exempel i .NET du kan använda den [UrlTokenEncode metoden](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) för detta ändamål).
* **metadata\_lagring\_sökväg** - användning av den fullständiga sökvägen garanterar unikhet, men sökvägen innehåller definitivt `/` tecken [ogiltig i en Dokumentnyckel](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Som ovan, har du möjlighet att koda nycklar med hjälp av den `base64Encode` [funktionen](search-indexer-field-mappings.md#base64EncodeFunction).
* Om inget av alternativen ovan fungerar kan du lägga till en anpassad metadataegenskapen till blobarna. Det här alternativet, men kräver blob överföringsprocessen att lägga till metadataegenskapen i alla BLOB. Eftersom nyckeln är en obligatorisk egenskap, misslyckas alla BLOB som inte har egenskapen indexeras.

> [!IMPORTANT]
> Om det finns ingen explicit mappning för nyckelfältet i indexet, använder Azure Search automatiskt `metadata_storage_path` som nyckel och base-64 kodar nyckelvärdena (det andra alternativet ovan).
>
>

I det här exemplet ska vi välja den `metadata_storage_name` fält som Dokumentnyckel. Vi förutsätter också ditt index har ett fält med namnet `key` och ett fält `fileSize` för lagring av dokumentets storlek. Om du vill ansluta saker upp som du vill ange följande fältmappningar när du skapar eller uppdaterar indexeraren:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Att göra detta alla tillsammans, här är hur du kan lägga till fältet och aktivera Base64-kodning av nycklar för en befintlig indexerare:

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2016-09-01
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
> Läs mer om fältmappningar i [i den här artikeln](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Kontrollera vilka blobbar som indexeras
Du kan styra vilka blobbar indexeras och som hoppas över.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Indexera endast blobbar med specifika filnamnstillägg
Du kan indexera endast blobbar med filnamnstillägg som du anger genom att använda den `indexedFileNameExtensions` indexeraren konfigurationsparameter. Värdet är en sträng som innehåller en kommaavgränsad lista med filnamnstillägg (med en inledande punkt). Om du vill endast index i. PDF och. DOCX blobbar, gör du följande:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Uteslut BLOB med specifika filnamnstillägg
Du kan utesluta blobbar med specifika filnamnstillägg från att indexera med hjälp av den `excludedFileNameExtensions` konfigurationsparameter. Värdet är en sträng som innehåller en kommaavgränsad lista med filnamnstillägg (med en inledande punkt). Till exempel till index alla BLOB utom de med den. PNG och. JPEG-tillägg, gör du följande:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Om båda `indexedFileNameExtensions` och `excludedFileNameExtensions` parametrar finns, Azure har genomsöks vid `indexedFileNameExtensions`, sedan på `excludedFileNameExtensions`. Det innebär att om samma filnamnstillägget finns i båda listorna, den kommer att uteslutas från indexering.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Kontrollera vilka delar av blob indexeras

Du kan styra vilka delar av blobar som indexeras med det `dataToExtract` konfigurationsparameter. Det kan ha följande värden:

* `storageMetadata` -Anger att endast den [standard blob-egenskaper och användardefinierade metadata](../storage/blobs/storage-properties-metadata.md) indexeras.
* `allMetadata` -Anger att metadata för lagring och [innehållstypen specifika metadata](#ContentSpecificMetadata) extraheras från blob innehåll som indexeras.
* `contentAndMetadata` -Anger att alla metadata och textinnehåll extraheras från blob som indexeras. Detta är standardvärdet.

Använd till exempel för att indexera endast metadata för lagring:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Använda blobbmetadata för att styra hur blobbar indexeras

De konfigurationsparametrar som beskrivs ovan gäller för alla BLOB. Ibland kanske du vill styra hur *enskilda blobbar* indexeras. Du kan göra detta genom att lägga till följande egenskaper för blob-metadata och värden:

| Egenskapsnamn | Egenskapsvärde | Förklaring |
| --- | --- | --- |
| AzureSearch_Skip |”true” |Instruerar blob indexeraren att helt och hållet blob. Varken metadata eller innehåll extrahering görs. Detta är användbart när en viss blob misslyckas flera gånger och avbryter indexering processen. |
| AzureSearch_SkipContent |”true” |Detta är likvärdiga med `"dataToExtract" : "allMetadata"` inställningen beskrivs [ovan](#PartsOfBlobToIndex) begränsade till en viss blob. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Hantera fel

Som standard avbryts blob-indexering så snart den stöter på en blob med en stöds inte innehållstyp (till exempel en bild). Naturligtvis kan du använda den `excludedFileNameExtensions` parametern för att hoppa över vissa typer av innehåll. Du kan dock behöva index blobbar utan att känna till alla möjliga typer av innehåll i förväg. Om du vill fortsätta indexeringen när en innehållstyp som inte stöds har påträffats, ange den `failOnUnsupportedContentType` konfigurationsparameter till `false`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

Azure Search kan inte fastställa innehållstypen för vissa BLOB eller går inte att bearbeta ett dokument för annars stöds content-type. Om du vill ignorera det här felet läget, ange den `failOnUnprocessableDocument` konfigurationsparameter till false:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Azure Search begränsar storleken på blobbar som indexeras. Dessa gränser dokumenteras i [Tjänstbegränsningarna i Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Stora blobbar behandlas som fel som standard. Men du kan fortfarande indexera stora BLOB storage metadata om du ställer in `indexStorageMetadataOnlyForOversizedDocuments` konfigurationsparameter SANT: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Du kan också fortsätta indexera om fel inträffa när som helst bearbetning, antingen vid parsning av blobbar eller när du lägger till dokument i ett index. Om du vill ignorera ett visst antal fel, ange den `maxFailedItems` och `maxFailedItemsPerBatch` konfigurationsparametrar till önskade värden. Exempel:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Identifiering av inkrementell indexering och borttagning
När du ställer in en blob-indexeraren ska köras enligt ett schema, den indexerar endast ändrade blobbar, enligt blobben `LastModified` tidsstämpel.

> [!NOTE]
> Du behöver ange en princip för identifiering av ändring – stegvis indexering aktiveras åt dig automatiskt.

Använd en ”mjuk borttagning”-metoden för att stödja tar bort dokument. Om du tar bort blobbar direkt tas motsvarande dokument inte bort från sökindexet. Använd i stället följande steg:  

1. Lägga till en anpassad metadataegenskap blob att visa Azure Search att logiskt bort
2. Konfigurera en princip för mjuk borttagning identifiering på datakällan
3. När indexeraren har bearbetats blob (som visas av indexerare statusen API) kan du fysiskt tar bort blobben

Till exempel följande princip betraktar en blob som ska tas bort om den har en metadataegenskap `IsDeleted` med värdet `true`:

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2016-09-01
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

## <a name="indexing-large-datasets"></a>Indexering stora datauppsättningar

Indexera blobbar kan vara en tidskrävande process. Du kan påskynda indexering av partitionering dina data och använder flera indexerare för att bearbeta data i parallellt i fall där du har miljontals BLOB till index. Här är hur du kan konfigurera detta:

- Dela dina data i flera blob-behållare eller virtuella mappar
- Konfigurera flera Azure Search datakällor, en per behållare eller mappen. För att peka till en blob-mappen, använder den `query` parameter:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Skapa en motsvarande indexerare för varje datakälla. Alla indexerare peka på samma mål sökindexet.  

- En sökning-enhet i din tjänst kan köra en indexerare vid en given tidpunkt. Flera indexerare är som beskrivs ovan endast användbar om de köras parallellt. Om du vill köra flera indexerare parallellt, skala upp din söktjänst genom att skapa ett lämpligt antal partitioner och repliker. Till exempel om din söktjänst har 6 search-enheter (till exempel 2 partitioner x 3 repliker), kan sedan 6 indexerare köras samtidigt, vilket resulterar i en six-fold ökning av indexeringsflöde. Mer information om skalning och kapacitetsplanering finns [skala resursen nivåer för fråga och indexering arbetsbelastningar i Azure Search](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indexera dokument tillsammans med relaterade data

Du kanske vill ”montera” dokument från flera källor i ditt index. Du kanske vill sammanfoga text från BLOB med andra metadata som lagras i Cosmos-databasen. Du kan även använda push indexering API tillsammans med olika indexerare för att bygga upp Sök dokument från flera delar. 

För detta ska fungera måste alla indexerare och andra komponenter komma överens om dokumentnyckeln för. En detaljerad genomgång finns i den här externa artikeln: [kombinera dokument med andra data i Azure Search ](http://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indexering oformaterad text 

Om alla BLOB innehålla oformaterad text i samma kodning, kan du avsevärt förbättra indexering prestanda med hjälp av **text parsning läge**. Om du vill använda text parsning läge i `parsingMode` konfigurationsegenskapen till `text`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Som standard i `UTF-8` kodning antas. Om du vill ange en annan kodning, använda den `encoding` konfigurationsegenskapen: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Egenskaper för innehåll typspecifika metadata
I följande tabell sammanfattas bearbetas för respektive format och beskriver metadataegenskaper som extraherats med hjälp av Azure Search.

| Dokumentformat / innehållstyp | Innehållstypen specifika metadataegenskaper | Mer information |
| --- | --- | --- |
| HTML (`text/html`) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Remsans HTML-kod och extrahera text |
| PDF (`application/pdf`) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Extrahera text, inklusive embedded dokument (exklusive bilder) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahera text, inklusive embedded dokument |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahera text, inklusive embedded dokument |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahera text, inklusive embedded dokument |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahera text, inklusive embedded dokument |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahera text, inklusive embedded dokument |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahera text, inklusive embedded dokument |
| Meddelande (outlook-program/vnd.ms) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extrahera text, inklusive bifogade filer |
| ZIP (program/zip) |`metadata_content_type` |Extrahera text från alla dokument i arkivet |
| XML (application/xml) |`metadata_content_type`</br>`metadata_content_encoding`</br> |Remsans XML-koden och extrahera text |
| JSON (application/json) |`metadata_content_type`</br>`metadata_content_encoding` |Extrahera text<br/>Obs: Om du behöver extrahera flera dokumentfält från en JSON-blob finns [indexering JSON-blobbar](search-howto-index-json-blobs.md) information |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Extrahera text, inklusive bifogade filer |
| RTF (program/rtf) |`metadata_content_type`</br>`metadata_author`</br>`metadata_character_count`</br>`metadata_creation_date`</br>`metadata_page_count`</br>`metadata_word_count`</br> | Extrahera text|
| Oformaterad text (text/plain) |`metadata_content_type`</br>`metadata_content_encoding`</br> | Extrahera text|


## <a name="help-us-make-azure-search-better"></a>Hjälp oss att förbättra Azure Search
Om du har funktionsförfrågningar om eller förslag på förbättringar kan berätta för oss på vår [UserVoice-webbplatsen](https://feedback.azure.com/forums/263029-azure-search/).
