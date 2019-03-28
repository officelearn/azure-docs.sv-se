---
title: Indexera JSON-blobar från Azure Blob-indexeraren för fulltextsökning – Azure Search
description: Crawla Azure JSON-blobar för textinnehåll med Azure Search Blob-indexeraren. Indexerare automatisera datainmatning för valda datakällor som Azure Blob storage.
ms.date: 02/28/2019
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: f44161586f9f4e121001b9f5e285b0e1e1dcd9d1
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518753"
---
# <a name="how-to-index-json-blobs-using-azure-search-blob-indexer"></a>Indexera JSON-blobar med Azure Search Blob-indexeraren
Den här artikeln visar hur du konfigurerar en Azure Search-blob [indexeraren](search-indexer-overview.md) att extrahera strukturerat innehåll från JSON-dokument i Azure Blob storage och gör det sökbara i Azure Search. Det här arbetsflödet skapar ett Azure Search-index och läser in den med befintliga text som extraherats från JSON-blobar. 

Du kan använda den [portal](#json-indexer-portal), [REST API: er](#json-indexer-rest), eller [.NET SDK](#json-indexer-dotnet) att indexera JSON-innehåll. Gemensamma för alla metoder är att JSON-dokument finns i en blobbehållare i Azure Storage-kontot. Anvisningar för push-överför JSON-dokument från andra icke-Azure-plattformar finns i [dataimport i Azure Search](search-what-is-data-import.md).

JSON-blobar i Azure Blob storage är vanligtvis antingen ett enda JSON-dokument eller en samling av JSON-entiteter. För JSON-samlingar blob kan ha en **matris** välformulerad JSON-element. Blobbar kan också bestå av flera enskilda JSON-enheter med en ny rad. Blob-indexeraren i Azure Search kan parsa sådana konstruktion, beroende på hur du ställer in den **parsingMode** parametern på begäran.

> [!IMPORTANT]
> `json` och `jsonArray` parsningslägen är allmänt tillgängliga, men `jsonLines` parsningsläge finns i offentlig förhandsversion och ska inte användas i produktionsmiljöer. Mer information finns i [REST api-version = 2017-11-11-Preview](search-api-2017-11-11-preview.md). 

> [!NOTE]
> Följ rekommendationerna indexeraren konfiguration i [indexering en-till-många](search-howto-index-one-to-many-blobs.md) att mata ut flera söka efter dokument från en Azure-blob.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Använda portalen

Den enklaste metoden för att indexera JSON-dokument är att använda en guide i den [Azure-portalen](https://portal.azure.com/). Genom att parsa metadata i Azure blob-behållaren i [ **dataimport** ](search-import-data-portal.md) guiden kan skapa ett Standardindex mappar källfält till målfälten för index och läsa in index i en enda åtgärd. Du kan ha ett operational fulltext search-index på några minuter beroende på storleken och komplexiteten i källdata.

Vi rekommenderar att du använder samma Azure-prenumeration för Azure Search och Azure storage, helst i samma region.

### <a name="1---prepare-source-data"></a>1 – förbereda källdata

Du bör ha ett Azure storage-konto med Blob storage och en behållare för JSON-dokument. Om du är bekant med någon av dessa krav kan granska ”ställa in Azure Blob-tjänsten och Läs in exempeldata” i den [cognitive search-quickstart](cognitive-search-quickstart-blob.md#set-up-azure-blob-service-and-load-sample-data).

> [!Important]
> På behållare, så kontrollera att **offentlig åtkomstnivå** är inställd på ”behållare (anonym läsåtkomst för behållare och blobbar)”. Azure storage och Azure Search ska vara under samma prenumeration och om möjligt i samma region. 

### <a name="2---start-import-data-wizard"></a>2 – starta guiden Importera data

Du kan [starta guiden](search-import-data-portal.md) från kommandofältet på sidan för Azure Search-tjänsten eller genom att klicka på **Lägg till Azure Search** i den **Blob service** i ditt storage-konto vänstra navigeringsfönstret.

   ![Kommandot Importera data i portalen](./media/search-import-data-portal/import-data-cmd2.png "starta guiden Importera data")

### <a name="3---set-the-data-source"></a>3 – Konfigurera datakällan

I den **datakälla** sidan måste vara **Azure Blob Storage**, med följande specifikationer:

+ **Data att extrahera** ska vara *innehåll och metadata*. Det här alternativet kan guiden härleda ett indexschema och mappa fält för import.
   
+ **Parsning läge** ska vara inställd på *JSON*, *JSON-matris* eller *JSON rader*. 

  *JSON* tydliggör varje blob som en enda dokumentsökningsoperationer dyker upp som ett fristående objekt i sökresultaten. 

  *JSON-matris* är för blobbar som innehåller välformulerad JSON-data – välformulerad JSON motsvarar en matris med objekt eller har en egenskap som är en matris med objekt och du vill att varje element att vara uppvisat som en fristående, oberoende dokumentsökningsoperationer. Om BLOB-lagring är komplexa, och du inte väljer *JSON-matris* hela blob matas in som ett enskilt dokument.

  *JSON-rader* är för blobbar som består av flera JSON-entiteter som är avgränsade med en ny rad, där du vill att varje entitet till att uppvisat som ett fristående oberoende search-dokument. Om BLOB-lagring är komplexa, och du inte väljer *JSON rader* parsning läge och sedan hela blob matas in som ett enskilt dokument.
   
+ **Lagringsbehållare** måste ange ditt storage-konto och behållare eller en anslutningssträng som matchas till behållaren. Du kan hämta anslutningssträngar på portalsidan för Blob service.

   ![BLOB definitionen för datakälla](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 – hoppa över ”Lägg till cognitive search”-sidan i guiden

Att lägga till kognitiva funktioner är inte nödvändigt för import av JSON-dokument. Om du inte har ett specifikt behov av att [innehåller API: er med Cognitive Services och transformeringar](cognitive-search-concept-intro.md) till din pipeline för fulltextindexering, bör du hoppa över det här steget.

Om du vill hoppa över steg du först gå till nästa sida.

   ![Knappen Nästa sida för kognitiv sökning](media/search-get-started-portal/next-button-add-cog-search.png)

Från sidan Gå du vidare till indexet anpassning.

   ![Hoppa över steget Kognitiva kunskaper](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 – ange indexattribut

I den **Index** bör du se en lista med fält med datatypen och en serie kryssrutorna för att ange indexattribut. Guiden kan skapa en fält-lista baserat på metadata och av sampling källdata. 

Du kan bulk-Välj attribut genom att klicka på kryssrutan högst upp i en attributkolumn. Välj **hämtningsbara** och **sökbar** för varje fält som ska returneras till en klientapp och föremål för fulltext search bearbetning. Lägg märke till att heltal inte är fulltext eller fuzzy sökbara (siffror utvärderas ordagrant och är ofta användbara i filter).

Granska beskrivningen av [indexattribut](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) och [språkanalysverktyg](https://docs.microsoft.com/rest/api/searchservice/language-support) för mer information. 

Ta en stund att granska dina val. När du kör guiden fysiska datastrukturer skapas och du kan inte redigera dessa fält utan att släppa och återskapa alla objekt.

   ![BLOB-indexdefinitionen](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 – skapa indexerare

Fullständigt anges, skapas tre olika objekt i din söktjänst. Ett datakällobjekt och index objekt sparas som namngivna resurser i Azure Search-tjänsten. Det sista steget skapar ett indexer-objekt. Namngivning av indexeraren kan det finnas som en fristående-resurs som du kan schemalägga och hantera oberoende av index och källobjektet, skapas i samma följd i guiden.

Om du inte är bekant med indexerare, en *indexeraren* är en resurs i Azure Search som crawlar en extern datakälla för sökbart innehåll. Utdata från den **dataimport** guiden är en indexerare som crawlar datakällan JSON, den extraherar sökbart innehåll och importerar den till ett index i Azure Search.

   ![Definition av BLOB-indexeraren](media/search-howto-index-json/import-wizard-json-indexer.png)

Klicka på **OK** att köra guiden och skapa alla objekt. Indexering inleds omedelbart.

Du kan övervaka dataimporten i portalens sidor. Förlopp meddelanden visar status för indexering och hur många dokument laddas upp. 

När indexering är klar kan du använda [Sökutforskaren](search-explorer.md) fråga ditt index.

> [!NOTE]
> Om du inte ser de data du förväntar dig, kan du behöva ange fler attribut på flera fält. Ta bort index och indexerare som du just skapade och gå igenom guiden igen och ändra dina val för indexattribut i steg 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Använda REST-API:er

Du kan använda REST API för att indexera JSON-blobar, följa ett arbetsflöde för tre delar som är gemensamma för alla indexerare i Azure Search: skapa en datakälla, skapa ett index, skapa en indexerare. Extrahering av data från blob storage inträffar när du skickar in begäran skapa et indexerare. När den här begäran har slutförts har du ett frågningsbart index. 

Du kan granska [REST-exempelkod](#rest-example) i slutet av det här avsnittet som visar hur du skapar alla tre objekt. Det här avsnittet innehåller även information om [JSON-parsning lägen](#parsing-modes), [enkel blobar](#parsing-single-blobs), [JSON-matriser](#parsing-arrays), och [kapslade matriser](#nested-json-arrays).

Använd för kodbaserad JSON indexering, [Postman](search-fiddler.md) och REST-API för att skapa de här objekten:

+ [index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Datakälla](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

Operationernas kräver att du skapar och anropa objekt i den här ordningen. Till skillnad från portalen arbetsflödet, en kodmetoden kräver ett tillgängligt index accepterar JSON-dokument som skickas via den **skapa et indexerare** begäran.

JSON-blobar i Azure Blob storage är vanligtvis antingen ett enda JSON-dokument eller en JSON ”matris”. Blob-indexeraren i Azure Search kan parsa antingen konstruktion, beroende på hur du ställer in den **parsingMode** parametern på begäran.

| JSON-dokument | parsingMode | Beskrivning | Tillgänglighet |
|--------------|-------------|--------------|--------------|
| En per blob | `json` | Parsar JSON-blobar som ett enda segment med text. Varje JSON-blob blir ett enskilt dokument i Azure Search. | Allmänt tillgängliga i både [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API och [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Flera per blob | `jsonArray` | Tolkar en JSON-matris i blob där varje element i matrisen blir en separat Azure Search-dokument.  | Tillgänglig som förhandsversion i både [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API och [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Flera per blob | `jsonLines` | Tolkar en blob som innehåller flera JSON entiteter (en ”matris”) avgränsade med en ny rad, där varje entitet blir en separat Azure Search-dokument. | Tillgänglig som förhandsversion i både [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API och [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1 – Assemblera indata för begäran

För varje begäran måste du ange namnet på tjänsten och admin-nyckel för Azure Search (i rubriken POST) och lagringskontonamn och nyckel för blob storage. Du kan använda [Postman](search-fiddler.md) att skicka HTTP-begäranden till Azure Search.

Kopiera följande fyra värden till anteckningar så att du kan klistra in dem i en begäran:

+ Azure Search-tjänstnamn
+ Azure Search-administratörsnyckel
+ Azure Storage-kontonamn
+ Azure storage-kontonyckel

Du hittar dessa värden i portalen:

1. Kopiera URL: en för search-tjänsten från sidan översikt på portalsidor för Azure Search.

2. I det vänstra navigeringsfönstret klickar du på **nycklar** och kopierar sedan antingen den primära eller sekundära nyckeln (de är likvärdiga).

3. Växla till portalsidor för ditt lagringskonto. I det vänstra navigeringsfönstret under **inställningar**, klickar du på **åtkomstnycklar**. Den här sidan innehåller både kontonamnet och nyckeln. Kopiera lagringskontonamn och en av nycklarna till anteckningar.

### <a name="2---create-a-data-source"></a>2 – Skapa en datakälla

Det här steget beskriver anslutningsinformationen för datakällan används av indexeraren. Datakällan är en namngiven objekt i Azure Search som kvarstår anslutningsinformationen. Datakällans typ., `azureblob`, avgör vilka data extrahering beteenden anropas av indexeraren. 

Ersätt giltiga värden för tjänstnamnet, admin-nyckel, storage-konto och kontot viktiga platshållare.

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 – skapa en målsökindex 

Indexerare är kopplad till ett indexschema. Om du använder API: et (i stället för portalen) kan du förbereda ett index i förväg så att du kan ange det på indexeraren igen.

Indexet lagrar du sökbart innehåll i Azure Search. Ange ett schema som anger vilka fält i ett dokument, attribut och andra konstruktioner som formar sökupplevelsen för att skapa ett index. Om du skapar ett index som har samma fältnamn och datatyper som källa indexeraren kommer att matcha käll- och fält, vilket sparar arbete att uttryckligen Mappa fält.

I följande exempel visas en [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) begäran. Indexet har ett sökbart `content` -fält som innehåller den text som extraherats från BLOB-objekt:   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 – konfigurera och köra indexeraren

Som med ett index och en käll- och indexerare är också en namngiven objekt som du skapar och återanvända på en Azure Search-tjänst. En fullständigt angivna begäran om att skapa en indexerare kan se ut på följande sätt:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Indexerarkonfiguration är i brödtexten i begäran. Det krävs en datakälla och ett tomt målindex som redan finns i Azure Search. 

Schema och parametrar är valfria. Om du utelämnar dem indexeraren körs direkt, med hjälp av `json` som parsning läge.

Viss indexeraren inkluderar inte fältmappningar. Inom indexerardefinitionen, som du kan lämna **fältmappningar** om egenskaperna för käll-JSON-dokument överensstämmer med fälten för ditt mål search-index. 


### <a name="rest-example"></a>REST-exempel

Det här avsnittet är en sammanfattning av alla begäranden som används för att skapa objekt. En beskrivning av komponenter, finns i föregående avsnitt i den här artikeln.

### <a name="data-source-request"></a>Begäran om källa

Alla indexerare kräver ett datakällobjekt som innehåller anslutningsinformationen till befintliga data. 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Begäran om index

Alla indexerare kräver ett målindex som tar emot data. Brödtexten i begäran definierar indexschemat som består av fält, attributet för att stödja de önskade beteenden i ett sökbart index. Det här indexet får vara tom när du kör indexeraren. 

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Indexeraren begäran

Den här begäran visar ett fullständigt anges indexerare. Den innehåller fältmappningar som har utelämnats i föregående exempel. Kom ihåg att ”schema”, ”parametrar” och ”fieldMappings” är valfria så länge det finns en tillgänglig standard. Om du utesluter ”schemalägga” gör att indexeraren ska köras omedelbart. Om du utesluter ”parsingMode” gör i index för att använda standard ”json”.

Skapa indexeraren för Azure Search utlöser dataimporten. Den körs direkt och därefter enligt ett schema om du har angett en.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

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

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Parsningslägen

JSON-blobar kan anta att flera formulär. Den **parsingMode** parametern på JSON-indexeraren avgör hur JSON-blobinnehåll parsas och strukturerade i ett Azure Search-index:

| parsingMode | Beskrivning |
|-------------|-------------|
| `json`  | Indexera varje blob som ett enskilt dokument. Detta är standardinställningen. |
| `jsonArray` | Välj det här läget om dina blobar består av JSON-matriser och du behöver varje element i matrisen blir en separat dokument i Azure Search. |
|`jsonLines` | Välj det här läget om dina blobar ska bestå av flera JSON-entiteter som skiljs åt av en ny rad, och du behöver varje entitet blir en separat dokument i Azure Search. |

Du kan tänka dig ett dokument som ett enskilt objekt i sökresultaten. Om du vill att varje element i matrisen visas i sökresultaten som ett fristående objekt, använder du den `jsonArray` eller `jsonLines` alternativ efter behov.

Inom indexerardefinitionen, kan du använda [fältmappningar](search-indexer-field-mappings.md) att välja vilka egenskaper i JSON-dokumentet källa används för att fylla i sökindexet mål. För `jsonArray` parsningsläge, om matrisen finns som en egenskap på lägre nivå, du kan ange en dokumentroten som anger där matrisen är placerad i blobben.

> [!IMPORTANT]
> När du använder `json`, `jsonArray` eller `jsonLines` parsningsläge, Azure Search förutsätter att alla blobar i datakällan innehåller JSON. Om du vill hantera en blandning av JSON och icke-JSON-blobar i samma datakälla kan berätta för oss på [UserVoice-webbplatsen](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Parsa enda JSON-blobar

Som standard [Azure Search blob-indexeraren](search-howto-indexing-azure-blob-storage.md) Parsar JSON-blobar som ett enda segment med text. Ofta vill du bevara strukturen för JSON-dokument. Anta exempelvis att du har följande JSON-dokument i Azure Blob storage:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Blob-indexeraren tolkar JSON-dokument i ett enda dokument i Azure Search. Indexeraren läser in ett index genom att matcha ”text”, ”datePublished” och ”taggar” från källan mot identiskt namngivna och skrivna index målfälten.

Enligt vad som anges, krävs inte fältmappningar. Får ett index med ”text” ”, datePublished och” taggar ”-fält, blobben som indexeraren kan hämta korrekt mappning utan ett fält som mappar finns i begäran.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Parsa JSON-matriser

Du kan också använda alternativet JSON-matris. Det här alternativet är användbart när blobbarna innehåller en *objektmatris välformulerad JSON*, och du vill att varje element blir en separat Azure Search-dokument. Till exempel med följande JSON-blob kan du kan fylla i ditt Azure Search-index med tre separata dokument, var och en med fälten ”id” och ”text”.  

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

## <a name="parse-nested-arrays"></a>Parsa kapslade matriser
För JSON matriser med kapslade element, kan du ange en `documentRoot` att indikera en struktur med flera nivåer. Exempel: om dina blobar ska se ut så här:

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

## <a name="parse-blobs-separated-by-newlines"></a>Parsa blobar avgränsade med nya rader

Om din blob innehåller flera JSON-entiteter som är avgränsade med en ny rad och du vill att varje element blir en separat Azure Search-dokument, kan du välja alternativet JSON rader. Till exempel få följande blob (där det finns tre olika JSON-enheter), kan du fylla i ditt Azure Search-index med tre separata dokument, var och en med fälten ”id” och ”text”.

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

För JSON linjer bör indexerardefinitionen likna följande exempel. Observera att parametern parsingMode anger den `jsonLines` parser. 

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

Igen, Lägg märke till att fältmappningar kan vara utelämnas, vilket liknar den `jsonArray` parsningsläge.

## <a name="add-field-mappings"></a>Lägg till fält-mappningar

När källa och mål fälten inte är justerade perfekt, kan du definiera ett avsnitt för mappning av fält i begärandetexten för explicit-fälten associationer.

För närvarande kan Azure Search inte kan indexera godtyckliga JSON-dokument direkt eftersom den stöder endast primitiva datatyper, sträng-matriser och GeoJSON punkter. Du kan dock använda **fältmappningar** Välj delar av JSON-dokument och ”lyfta” dem till översta fälten för search-dokumentet. Läs om grunderna i fält-mappningar i [fältmappningar för Azure Search-indexerare](search-indexer-field-mappings.md).

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

## <a name="see-also"></a>Se också

+ [Indexerare i Azure Search](search-indexer-overview.md)
+ [Indexera Azure Blob Storage med Azure Search](search-howto-index-json-blobs.md)
+ [Indexera CSV-blobar med Azure Search blob-indexeraren](search-howto-index-csv-blobs.md)
+ [Självstudier: Söka efter halvstrukturerade data från Azure Blob storage](search-semi-structured-data.md)
