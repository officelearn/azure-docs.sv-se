---
title: Indexera JSON-blobar från Azure Blob-indexeraren för fulltextsökning – Azure Search
description: Crawla Azure JSON-blobar för textinnehåll med Azure Search Blob-indexeraren. Indexerare automatisera datainmatning för valda datakällor som Azure Blob storage.
ms.date: 12/21/2018
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 6df8d9a5c1ca1e587834ea08f73b3dd9498f8537
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753157"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indexera JSON-blobar med Azure Search Blob-indexeraren
Den här artikeln visar hur du konfigurerar ett Azure Search blob-indexeraren för att extrahera strukturerat innehåll från JSON-blobar i Azure Blob storage.

Du kan använda den [portal](#json-indexer-portal), [REST API: er](#json-indexer-rest), eller [.NET SDK](#json-indexer-dotnet) att indexera JSON-innehåll. Gemensamma för alla metoder är JSON-dokument som finns i en blobbehållare i Azure Storage-kontot. Anvisningar för push-överför JSON-dokument från andra icke-Azure-plattformar finns i [dataimport i Azure Search](search-what-is-data-import.md).

JSON-blobar i Azure Blob storage är vanligtvis antingen ett enda JSON-dokument eller en JSON-matris. Blob-indexeraren i Azure Search kan parsa antingen konstruktion, beroende på hur du ställer in den **parsingMode** parametern på begäran.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Använda portalen

Den enklaste metoden för att indexera JSON-dokument är att använda en guide i den [Azure-portalen](https://portal.azure.com/). Genom att parsa metadata i Azure blob-behållaren i [ **dataimport** ](search-import-data-portal.md) guiden kan skapa ett Standardindex mappar källfält till målfälten för index och läsa in index i en enda åtgärd. Du kan ha ett operational fulltext search-index på några minuter beroende på storleken och komplexiteten i källdata.

### <a name="1---prepare-source-data"></a>1 – förbereda källdata

Du bör ha ett Azure storage-konto med Blob storage och en behållare för JSON-dokument. Om du är bekant med någon av dessa uppgifter kan du granska nödvändiga ”ställa in Azure Blob-tjänsten och Läs in exempeldata” i den [cognitive search-quickstart](cognitive-search-quickstart-blob.md#set-up-azure-blob-service-and-load-sample-data).

### <a name="2---start-import-data-wizard"></a>2 – starta guiden Importera data

Du kan [starta guiden](search-import-data-portal.md) från kommandofältet på sidan för Azure Search-tjänsten eller genom att klicka på **Lägg till Azure Search** i den **Blob service** i ditt storage-konto vänstra navigeringsfönstret.

### <a name="3---set-the-data-source"></a>3 – Konfigurera datakällan

I den **datakälla** sidan måste vara **Azure Blob Storage**, med följande specifikationer:

+ **Data att extrahera** ska vara *innehåll och metadata*. Det här alternativet kan guiden härleda ett indexschema och mappa fält för import.
   
+ **Parsning läge** ska vara inställd på *JSON* eller *JSON-matris*. 

  *JSON* tydliggör varje blob som en enda dokumentsökningsoperationer dyker upp som ett fristående objekt i sökresultaten. 

  *JSON-matris* är för blobbar som består av flera element som du vill att varje element att vara uppvisat som en fristående, oberoende dokumentsökningsoperationer. Om BLOB-lagring är komplexa, och du inte väljer *JSON-matris* hela blob matas in som ett enskilt dokument.
   
+ **Lagringsbehållare** måste ange ditt storage-konto och behållare eller en anslutningssträng som matchas till behållaren. Du kan hämta anslutningssträngar på portalsidan för Blob service.

   ![BLOB definitionen för datakälla](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 – hoppa över ”Lägg till cognitive search”-sidan i guiden

Att lägga till kognitiva funktioner är inte nödvändigt för import av JSON-dokument. Om du inte har ett specifikt behov av att [innehåller API: er med Cognitive Services och transformeringar](cognitive-search-concept-intro.md) till din pipeline för fulltextindexering, bör du hoppa över det här steget.

Om du vill hoppa över steg klickar du på nästa sida **anpassa målindex**.

### <a name="5---set-index-attributes"></a>5 – ange indexattribut

I den **Index** bör du se en lista med fält med datatypen och en serie kryssrutorna för att ange indexattribut. Guiden kan generera ett Standardindex baserat på metadata och av sampling källdata. 

Standardvärdena leder ofta till en fungerande lösning: att välja ett fält (cast som sträng) att fungera som nyckeln eller dokument-ID för att unikt identifiera varje dokument, samt fält som är lämpliga för fulltextsökning och hämtningsbara i en resultatuppsättning. För BLOB, den `content` fältet är det bästa alternativet för sökbart innehåll.

Du kan acceptera standardinställningarna eller igenom beskrivningen av [indexattribut](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) och [språkanalysverktyg](https://docs.microsoft.com/rest/api/searchservice/language-support) att åsidosätta eller komplettera de initiala värdena. 

Ta en stund att granska dina val. När du kör guiden fysiska datastrukturer skapas och du kan inte redigera dessa fält utan att släppa och återskapa alla objekt.

   ![BLOB-indexdefinitionen](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 – skapa indexerare

Fullständigt anges, skapas tre olika objekt i din söktjänst. Ett datakällobjekt och index objekt sparas som namngivna resurser i Azure Search-tjänsten. Det sista steget skapar ett indexer-objekt. Namngivning av indexeraren kan det finnas som en fristående-resurs som du kan schemalägga och hantera oberoende av index och källobjektet, skapas i samma följd i guiden.

Om du inte är bekant med indexerare, en *indexeraren* är en resurs i Azure Search som crawlar en extern datakälla för sökbart innehåll. Utdata från den **dataimport** guiden är en indexerare som crawlar datakällan JSON, den extraherar sökbart innehåll och importerar den till ett index i Azure Search.

   ![Definition av BLOB-indexeraren](media/search-howto-index-json/import-wizard-json-indexer.png)

Klicka på **OK** att köra guiden och skapa alla objekt. Indexering inleds omedelbart.

Du kan övervaka dataimporten i portalens sidor. Förlopp meddelanden visar status för indexering och hur många dokument laddas upp. När indexering är klar kan du använda [Sökutforskaren](search-explorer.md) fråga ditt index.

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Använda REST-API:er

Indexera JSON-blobar liknar extraheringen vanligt dokument i ett arbetsflöde för tre delar som är gemensamma för alla indexerare i Azure Search: skapa en datakälla, skapa ett index, skapa en indexerare.

För kodbaserad JSON indexering, kan du använda REST-API med API: er för [indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer), [datakällor](https://docs.microsoft.com/rest/api/searchservice/create-data-source), och [index](https://docs.microsoft.com/rest/api/searchservice/create-index). Till skillnad från guiden portalen en kodmetoden kräver att du har ett index på plats, kan acceptera JSON-dokument när du skickar den **skapa et indexerare** begäran.

JSON-blobar i Azure Blob storage är vanligtvis antingen ett enda JSON-dokument eller en JSON-matris. Blob-indexeraren i Azure Search kan parsa antingen konstruktion, beroende på hur du ställer in den **parsingMode** parametern på begäran.

| JSON-dokument | parsingMode | Beskrivning | Tillgänglighet |
|--------------|-------------|--------------|--------------|
| En per blob | `json` | Parsar JSON-blobar som ett enda segment med text. Varje JSON-blob blir ett enskilt dokument i Azure Search. | Allmänt tillgängliga i både [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) och [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) API: er. |
| Flera per blob | `jsonArray` | Tolkar en JSON-matris i blob där varje element i matrisen blir en separat Azure Search-dokument.  | Allmänt tillgängliga i både [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) och [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) API: er. |


### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla

Det första steget är att tillhandahålla anslutningsinformation för datakällan används av indexeraren. Datakällans typ som anges här som `azureblob`, avgör vilka data extrahering beteenden anropas av indexeraren. För JSON blob-indexering är definition av datakällan samma för både JSON-dokument och matriser. 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>Steg 2: Skapa en målsökindex 

Indexerare är kopplad till ett indexschema. Om du använder API: et (i stället för portalen) kan du förbereda ett index i förväg så att du kan ange det på indexeraren igen.

Indexet lagrar du sökbart innehåll i Azure Search. Ange ett schema som anger vilka fält i ett dokument, attribut och andra konstruktioner som formar sökupplevelsen för att skapa ett index. Om du skapar ett index som har samma fältnamn och datatyper som källa indexeraren kommer att matcha käll- och fält, vilket sparar arbete att uttryckligen Mappa fält.

I följande exempel visas en [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) begäran. Indexet har ett sökbart `content` -fält som innehåller den text som extraherats från BLOB-objekt:   

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


### <a name="step-3-configure-and-run-the-indexer"></a>Steg 3: Konfigurera och köra indexeraren

Fram till nu har definitioner för datakällan och indexet parsingMode oberoende. Men i steg 3 för indexerarkonfiguration sökvägen skiljer sig beroende på hur du vill att JSON-blob innehåll att parsas och strukturerade i ett Azure Search-index. Alternativen är `json` eller `jsonArray`:

+ Ange **parsingMode** till `json` indexera varje blob som ett enskilt dokument.

+ Ange **parsingMode** till `jsonArray` om dina blobar består av JSON-matriser och du behöver varje element i matrisen blir en separat dokument i Azure Search. Du kan tänka dig ett dokument som ett enskilt objekt i sökresultaten. Om du vill att varje element i matrisen visas i sökresultaten som ett fristående objekt, använder du den `jsonArray` alternativet.

Inom indexerardefinitionen, kan du använda **fältmappningar** att välja vilka egenskaper i JSON-dokumentet källa används för att fylla i sökindexet mål. För JSON-matriser om matrisen finns som en egenskap på lägre nivå, kan du ange en dokumentroten som anger där matrisen är placerad i blobben.

> [!IMPORTANT]
> När du använder `json` eller `jsonArray` parsningsläge, Azure Search förutsätter att alla blobar i datakällan innehåller JSON. Om du vill hantera en blandning av JSON och icke-JSON-blobar i samma datakälla kan berätta för oss på [UserVoice-webbplatsen](https://feedback.azure.com/forums/263029-azure-search).


### <a name="how-to-parse-single-json-blobs"></a>Hur du Parsar enda JSON-blobar

Som standard [Azure Search blob-indexeraren](search-howto-indexing-azure-blob-storage.md) Parsar JSON-blobar som ett enda segment med text. Ofta vill du bevara strukturen för JSON-dokument. Anta exempelvis att du har följande JSON-dokument i Azure Blob storage:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Blob-indexeraren tolkar JSON-dokument i ett enda dokument i Azure Search. Indexeraren läser in ett index genom att matcha ”text”, ”datePublished” och ”taggar” från källan mot identiskt namngivna och skrivna index målfälten.

Konfigurationen tillhandahålls i brödtexten i en indexerare-åtgärd. Kom ihåg att datakällobjektet som tidigare definierats anger och informationen om datakällan. Dessutom måste målindex också finnas som en tom behållare i din tjänst. Schema och parametrar är valfria, men om du utelämnar dem indexeraren körs direkt, med hjälp av `json` som parsning läge.

En fullständigt angiven begäran kan se ut på följande sätt:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Enligt vad som anges, krävs inte fältmappningar. Får ett index med ”text” ”, datePublished och” taggar ”-fält, blobben som indexeraren kan hämta korrekt mappning utan ett fält som mappar finns i begäran.

### <a name="how-to-parse-json-arrays"></a>Så här att parsa JSON-matriser

Du kan också välja funktionen JSON-matris. Den här funktionen är användbar när blobbarna innehåller en *matris av JSON-objekt*, och du vill att varje element blir en separat Azure Search-dokument. Till exempel med följande JSON-blob kan du kan fylla i ditt Azure Search-index med tre separata dokument, var och en med fälten ”id” och ”text”.  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

För en JSON-matris bör att indexerarens definition likna följande exempel. Observera att parametern parsingMode anger den `jsonArray` parser. Ange rätt parsern och har rätt data indata krävs bara två matris-specifika för att indexera JSON-blobar.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Observera återigen att fältmappningar kan utelämnas. Under förutsättning att ett index med identiskt namngivna ”id” och ”text”-fält, härleda blob-indexeraren korrekt mappning utan en mappning för explicit fältlistan.

<a name="nested-json-arrays"></a>

### <a name="nested-json-arrays"></a>Kapslad JSON-matriser
Vad händer om du vill indexera en matris av JSON-objekt, men matrisen är kapslade någonstans i dokumentet? Du kan välja vilken egenskap innehåller en matris med hjälp av den `documentRoot` konfigurationsegenskapen. Exempel: om dina blobar ska se ut så här:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Använd den här konfigurationen för att indexera matrisen i den `level2` egenskapen:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

### <a name="using-field-mappings-to-build-search-documents"></a>Använder fältmappningar för att skapa söka efter dokument

När källa och mål fälten inte är justerade perfekt, kan du definiera ett avsnitt för mappning av fält i begärandetexten för explicit-fälten associationer.

För närvarande kan Azure Search inte kan indexera godtyckliga JSON-dokument direkt, eftersom den stöder endast primitiva datatyper, sträng-matriser och GeoJSON punkter. Du kan dock använda **fältmappningar** Välj delar av JSON-dokument och ”lyfta” dem till översta fälten för search-dokumentet. Läs om grunderna i fält-mappningar i [fältmappningar för Azure Search-indexerare](search-indexer-field-mappings.md).

Gå tillbaka till vårt exempel JSON-dokument:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Anta att en search-index med följande fält: `text` av typen `Edm.String`, `date` av typen `Edm.DateTimeOffset`, och `tags` av typen `Collection(Edm.String)`. Lägg märke till skillnaden mellan ”datePublished” i källan och `date` i indexet. Använd följande fältmappningar för att mappa din JSON till det önskade format:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Namnen på källan i mappningarna anges med den [JSON-visare](https://tools.ietf.org/html/rfc6901) notation. Du börjar med ett snedstreck att referera till roten i JSON-dokument och sedan välja önskad egenskap (på valfri nivå av inkapsling) via vanlig snedstreck kommaavgränsade sökväg.

Du kan även gå till enskilda matriselement med hjälp av ett Nollbaserat index. Till exempel använda en fältmappning så här för att välja det första elementet i matrisen ”taggar” från exemplet ovan:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Om ett källa fältnamn i en mappning fältsökväg refererar till en egenskap som inte finns i JSON, hoppas mappningen utan fel. Det gör du så att vi kan stödja dokument med ett annat schema (vilket är ett vanligt användningsfall). Eftersom det finns ingen validering, måste du tänka på att undvika skrivfel i din specifikation för mappning av fält.
>
>

### <a name="rest-example-indexer-request-with-field-mappings"></a>REST-exempel: Indexeraren begäran med fältmappningar

I följande exempel är en fullständigt angivna indexeraren nyttolast, inklusive fältmappningar:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }

<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>Använd .NET SDK

.NET SDK har fullständigt paritet med REST API. Vi rekommenderar att du läser avsnittet ovan REST API för att lära dig begrepp, arbetsflöde och krav. Du kan referera till följande .NET API-referensdokumentation att implementera en JSON-indexerare i förvaltad kod.

+ [Microsoft.Azure.Search.models.DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [Microsoft.Azure.Search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [Microsoft.Azure.Search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [Microsoft.Azure.Search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

## <a name="see-also"></a>Se också

+ [Indexerare i Azure Search](search-indexer-overview.md)
+ [Indexera Azure Blob Storage med Azure Search](search-howto-index-json-blobs.md)
+ [Indexera CSV-blobar med Azure Search blob-indexeraren](search-howto-index-csv-blobs.md)
+ [Självstudier: Söka efter halvstrukturerade data från Azure Blob storage](search-semi-structured-data.md)
