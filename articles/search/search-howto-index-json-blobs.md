---
title: Indexera JSON-blobbar från Azure Blob-indexeraren för full texts ökning – Azure Search
description: Crawla Azure JSON-blobbar för text innehåll med hjälp av Azure Search BLOB-indexeraren. Indexerare automatiserar data inmatning för valda data källor som Azure Blob Storage.
ms.date: 05/02/2019
author: HeidiSteen
manager: nitinme
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: d266f5edb85dd732cc39cfe98a64bee8019cdbd1
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656682"
---
# <a name="how-to-index-json-blobs-using-azure-search-blob-indexer"></a>Så här indexerar du JSON-blobbar med Azure Search BLOB-indexeraren
Den här artikeln visar hur du konfigurerar en Azure Search BLOB [](search-indexer-overview.md) -indexerare för att extrahera strukturerat innehåll från JSON-dokument i Azure Blob Storage och göra det sökbart i Azure Search. Det här arbets flödet skapar ett Azure Search-index och läser in det med befintlig text som extraherats från JSON-blobbar. 

Du kan använda [portalen](#json-indexer-portal), [REST-API: er](#json-indexer-rest)eller [.NET SDK](#json-indexer-dotnet) för att indexera JSON-innehåll. Gemensamma för alla tillvägagångs sätt är att JSON-dokument finns i en BLOB-behållare i ett Azure Storage-konto. Vägledning om hur du skickar JSON-dokument från andra plattformar än Azure-plattformar finns [i data import i Azure Search](search-what-is-data-import.md).

JSON-blobar i Azure Blob Storage är vanligt vis antingen ett enda JSON-dokument eller en samling JSON-entiteter. För JSON-samlingar kan blobben ha en **matris** med välformulerade JSON-element. Blobbar kan också bestå av flera enskilda JSON-entiteter som skiljs åt av en ny rad. BLOB-indexeraren i Azure Search kan parsa en sådan konstruktion, beroende på hur du ställer in parametern **parsingMode** på begäran.

Alla JSON-tolknings lägen`json`( `jsonArray`, `jsonLines`,) är nu allmänt tillgängliga. 

> [!NOTE]
> Följ konfigurations rekommendationerna för indexeraren i [en-till-många-indexering](search-howto-index-one-to-many-blobs.md) för att skriva ut flera Sök dokument från en Azure-blob.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Använda portalen

Den enklaste metoden för att indexera JSON-dokument är att använda en guide i [Azure Portal](https://portal.azure.com/). Genom att parsa metadata i Azure Blob-behållaren kan guiden [**Importera data**](search-import-data-portal.md) skapa ett standard index, mappa käll fält till mål index fält och läsa in indexet i en enda åtgärd. Beroende på storleken och komplexiteten hos källdata kan du ha ett fullständigt texts öknings index på några minuter.

Vi rekommenderar att du använder samma Azure-prenumeration för både Azure Search och Azure Storage, helst i samma region.

### <a name="1---prepare-source-data"></a>1 – Förbered källdata

1. [Logga](https://portal.azure.com/)in på Azure Portal.

1. [Skapa en BLOB-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) som innehåller dina data. Den offentliga åtkomst nivån kan anges till något av dess giltiga värden.

Du behöver lagrings kontots namn, behållar namn och en åtkomst nyckel för att hämta dina data i guiden **Importera data** .

### <a name="2---start-import-data-wizard"></a>2-Starta guiden Importera data

På sidan Översikt i Azure Searchs tjänsten kan du [starta guiden](search-import-data-portal.md) från kommando fältet eller genom att klicka på **Lägg till Azure Search** i avsnittet **BLOB service** i lagrings kontots vänstra navigerings fönster.

   ![Importera data kommando i portalen](./media/search-import-data-portal/import-data-cmd2.png "Starta guiden Importera data")

### <a name="3---set-the-data-source"></a>3 – ange data källan

På sidan **data källa** måste källan vara **Azure Blob Storage**, med följande specifikationer:

+ **Data som ska extraheras** bör vara *innehåll och metadata*. Genom att välja det här alternativet kan guiden härleda ett index schema och Mappa fälten för import.
   
+ **Tolknings läget** ska vara inställt på *JSON*, *JSON-matris* eller *JSON-linjer*. 

  *JSON* leder varje blob som ett enda sökdokument, som visas som ett oberoende objekt i Sök resultaten. 

  *JSON-matris* är för blobbar som innehåller välformulerade JSON-data – det välformulerade JSON motsvarar en matris med objekt, eller har en egenskap som är en objekt mat ris och du vill att varje element ska ledas som ett fristående, oberoende sökdokument. Om blobbar är komplexa och du inte väljer JSON- *matris* , matas hela blobben in som ett enda dokument.

  *JSON-linjer* är för blobbar som består av flera JSON-enheter, avgränsade med en ny rad, där du vill att varje entitet ska ledas som ett fristående, oberoende Sök dokument. Om blobbar är komplexa och du inte väljer JSON- *rader* tolknings läge, matas hela blobben in som ett enda dokument.
   
+ **Lagrings behållaren** måste ange ditt lagrings konto och din behållare, eller en anslutnings sträng som matchar behållaren. Du kan hämta anslutnings strängar på sidan Blob Service Portal.

   ![Definition av BLOB-datakälla](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 – hoppa över sidan Lägg till kognitiv sökning i guiden

Det krävs inte att lägga till kognitiva kunskaper för import av JSON-dokument. Om du inte har ett särskilt behov av att [inkludera API:er för Cognitive Services och omvandlingar](cognitive-search-concept-intro.md) till din indexerings pipeline, bör du hoppa över det här steget.

Om du vill hoppa över steget går du först till nästa sida.

   ![Knappen Nästa sida för kognitiv sökning](media/search-get-started-portal/next-button-add-cog-search.png)

Från den sidan kan du gå vidare till index anpassning.

   ![Hoppa över steget Kognitiva kunskaper](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5-Ange indexattribut

På sidan **index** visas en lista med fält med data typen och en serie kryss rutor för att ställa in indexattribut. Guiden kan generera en fält lista baserat på metadata och genom att sampla data källan. 

Du kan massredigera attribut genom att klicka på kryss rutan överst i en Attribute-kolumn. Välj **hämtnings** **Bart** och sökbart för varje fält som ska returneras till en klient-app och för full texts öknings bearbetning. Du märker att heltal inte är full text eller fuzzy sökbar (tal utvärderas orda Grant och ofta är användbara i filter).

Granska beskrivningen av [indexattribut](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) och [språk analys](https://docs.microsoft.com/rest/api/searchservice/language-support) verktyg för mer information. 

Ägna en stund åt att granska dina val. När du har kört guiden skapas fysiska data strukturer och du kan inte redigera dessa fält utan att släppa och återskapa alla objekt.

   ![Definition av BLOB index](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 – skapa indexerare

Fullständigt angivet skapar guiden tre distinkta objekt i din Sök tjänst. Ett data käll objekt och ett index objekt sparas som namngivna resurser i Azure Searchs tjänsten. Det sista steget skapar ett indexerare-objekt. Om du namnger indexeraren kan den finnas som en fristående resurs, som du kan schemalägga och hantera oberoende av indexet och datakällobjektet, vilket skapas i samma guide ordning.

Om du inte är bekant med indexerare är en *indexerare* en resurs i Azure Search som crawlar en extern data källa för sökbart innehåll. Utdata från guiden **Importera data** är en indexerare som crawlar din JSON-datakälla, extraherar sökbart innehåll och importerar det till ett index på Azure Search.

   ![Definition av BLOB-indexerare](media/search-howto-index-json/import-wizard-json-indexer.png)

Klicka på **OK** för att köra guiden och skapa alla objekt. Indexeringen börjar omedelbart.

Du kan övervaka data import på Portal sidorna. Förlopps meddelanden indikerar indexerings status och hur många dokument som laddas upp. 

När indexeringen är klar kan du använda [Sök Utforskaren](search-explorer.md) för att fråga ditt index.

> [!NOTE]
> Om du inte ser de data du förväntar dig kan du behöva ange fler attribut för fler fält. Ta bort indexet och indexeraren som du nyss skapade och gå igenom guiden igen och ändra dina val för indexattribut i steg 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Använda REST-API:er

Du kan använda REST API för att indexera JSON-blobbar, efter ett arbets flöde med tre delar som är gemensamt för alla indexerare i Azure Search: skapa en data källa, skapa ett index, skapa en indexerare. Data extrahering från Blob Storage sker när du skickar en begäran om att skapa indexerare. När den här begäran har avslut ATS har du ett index som kan anropas. 

Du kan granska [resten exempel kod](#rest-example) i slutet av det här avsnittet som visar hur du skapar alla tre objekten. Det här avsnittet innehåller också information om [JSON-parsningsfel](#parsing-modes), [enskilda blobbar](#parsing-single-blobs), JSON- [matriser](#parsing-arrays)och kapslade [matriser](#nested-json-arrays).

För kodbaserade JSON-indexering använder du [Postman](search-get-started-postman.md) och REST API för att skapa dessa objekt:

+ [index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [data Källa](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

Ordningen på åtgärder kräver att du skapar och anropar objekt i den här ordningen. Till skillnad från Portal arbets flödet kräver en kod metod ett tillgängligt index för att acceptera JSON-dokumenten som skickas via begäran om att **skapa indexerare** .

JSON-blobar i Azure Blob Storage är vanligt vis antingen ett enda JSON-dokument eller en JSON-matris. BLOB-indexeraren i Azure Search kan tolka antingen konstruktion, beroende på hur du ställer in parametern **parsingMode** på begäran.

| JSON-dokument | parsingMode | Beskrivning | Tillgänglighet |
|--------------|-------------|--------------|--------------|
| En per BLOB | `json` | Parsar JSON-blobbar som ett enda text segment. Varje JSON-BLOB blir ett enda Azure Search-dokument. | Allmänt tillgänglig i både [rest](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API och [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Flera per BLOB | `jsonArray` | Parsar en JSON-matris i blobben där varje element i matrisen blir ett separat Azure Search-dokument.  | Allmänt tillgänglig i både [rest](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API och [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Flera per BLOB | `jsonLines` | Parsar en blob som innehåller flera JSON-entiteter (en "matris") som skiljs åt av en ny rad, där varje entitet blir ett separat Azure Search-dokument. | Allmänt tillgänglig i både [rest](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API och [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1 – Sammanställ indata för begäran

För varje begäran måste du ange tjänst namnet och administratörs nyckeln för Azure Search (i POST huvudet) och lagrings kontots namn och nyckel för Blob Storage. Du kan använda [Postman](search-get-started-postman.md) för att skicka HTTP-begäranden till Azure Search.

Kopiera följande fyra värden till anteckningar så att du kan klistra in dem i en förfrågan:

+ Azure Search tjänstens namn
+ Azure Search administratörs nyckel
+ Azure Storage-kontonamn
+ Nyckel för Azure Storage-konto

Du kan hitta dessa värden i portalen:

1. På Portal sidorna för Azure Search kopierar du URL: en för Sök tjänsten från översikts sidan.

2. I det vänstra navigerings fönstret klickar du på **nycklar** och kopierar antingen den primära eller sekundära nyckeln (de är motsvarande).

3. Växla till Portal sidorna för ditt lagrings konto. I det vänstra navigerings fönstret, under **Inställningar**, klickar du på **åtkomst nycklar**. Den här sidan innehåller både konto namnet och nyckeln. Kopiera lagrings kontots namn och en av nycklarna till anteckningar.

### <a name="2---create-a-data-source"></a>2 – Skapa en data Källa

Det här steget ger anslutnings information för data källan som används av indexeraren. Data källan är ett namngivet objekt i Azure Search som behåller anslutnings informationen. Typen `azureblob`av data källa avgör vilka beteenden för data extrahering som anropas av indexeraren. 

Ersätt giltiga värden för tjänst namn, administratörs nyckel, lagrings konto och plats hållare för konto nycklar.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 – skapa ett mål Sök index 

Indexerare är kopplade till ett index schema. Om du använder API: et (i stället för portalen) förbereder du ett index i förväg så att du kan ange det för indexerings åtgärden.

Indexet lagrar sökbart innehåll i Azure Search. Skapa ett index genom att ange ett schema som anger fälten i ett dokument, attribut och andra konstruktioner som formar Sök upplevelsen. Om du skapar ett index som har samma fält namn och data typer som källan kommer indexeraren att matcha käll-och mål fälten, vilket sparar arbetet för att explicit Mappa fälten.

I följande exempel visas en begäran om att [skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index) . Indexet har ett sökbart `content` fält för att lagra texten som extraheras från blobbar:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 – Konfigurera och kör indexeraren

Precis som med ett index och en data källa, och indexeraren också är ett namngivet objekt som du skapar och återanvänder på en Azure Search-tjänst. En fullständigt angiven begäran om att skapa en indexerare kan se ut så här:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Indexerings konfigurationen är i bröd texten i begäran. Den kräver en data källa och ett tomt mål index som redan finns i Azure Search. 

Schema och parametrar är valfria. Om du utelämnar dem körs indexeraren omedelbart med `json` som tolknings läge.

Den här specifika indexeraren innehåller inte fält mappningar. I index definitions definitionen kan du lämna **fält mappningar** om egenskaperna för käll-JSON-dokumentet matchar fälten i mål Sök indexet. 


### <a name="rest-example"></a>REST-exempel

Det här avsnittet är en sammanfattning av alla förfrågningar som används för att skapa objekt. En beskrivning av komponent delar finns i föregående avsnitt i den här artikeln.

### <a name="data-source-request"></a>Begäran om data Källa

Alla indexerare kräver ett data käll objekt som ger anslutnings information till befintliga data. 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Index förfrågan

Alla indexerare kräver ett mål index som tar emot data. Bröd texten i begäran definierar index schemat, som består av fält, attribut som stöder önskade beteenden i ett sökbart index. Detta index måste vara tomt när du kör indexeraren. 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Indexerare-begäran

Den här begäran visar en fullständigt angiven indexerare. Den innehåller fält mappningar som utelämnats i föregående exempel. Kom ihåg att "schema", "parametrar" och "fieldMappings" är valfria så länge det finns ett tillgängligt standardvärde. Om du utelämnar "schema" körs indexeraren omedelbart. Att utelämna "parsingMode" gör att indexet använder standard-JSON.

Att skapa indexeraren på Azure Search utlöser data import. Den körs omedelbart och därefter enligt ett schema om du har angett ett.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
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

.NET SDK har fullständig paritet med REST API. Vi rekommenderar att du läser avsnittet tidigare REST API för att lära dig begrepp, arbets flöden och krav. Du kan sedan se följande dokumentation om .NET API-referens för att implementera en JSON-indexerare i förvaltad kod.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Tolknings lägen

JSON-blobbar kan anta flera formulär. Parametern **parsingMode** i JSON-indexeraren avgör hur JSON-BLOB-innehåll tolkas och struktureras i ett Azure Search index:

| parsingMode | Beskrivning |
|-------------|-------------|
| `json`  | Indexera varje blob som ett enskilt dokument. Detta är standardinställningen. |
| `jsonArray` | Välj det här läget om Blobbarna består av JSON-matriser och du behöver varje element i matrisen för att bli ett separat dokument i Azure Search. |
|`jsonLines` | Välj det här läget om Blobbarna består av flera JSON-entiteter, som skiljs åt av en ny rad, och du behöver varje entitet för att bli ett separat dokument i Azure Search. |

Du kan tänka dig ett dokument som ett enda objekt i Sök resultaten. Om du vill att varje element i matrisen ska visas i Sök resultat som ett oberoende objekt, använder du `jsonArray` alternativet eller `jsonLines` efter vad som är lämpligt.

Inom index definitions definitionen kan du välja att använda [fält mappningar](search-indexer-field-mappings.md) för att välja vilka egenskaper för käll-JSON-dokumentet som används för att fylla i mål Sök indexet. Om matrisen finns som en lågnivå egenskap i parsing-lägekanduangeendokumentrotsomangervarmatrisenplacerasiblobben.`jsonArray`

> [!IMPORTANT]
> När du använder `json`, `jsonArray` eller `jsonLines` tolknings läge, förutsätter Azure Search att alla blobbar i data källan innehåller JSON. Om du behöver stöd för en blandning av JSON-och icke-JSON-blobbar i samma data källa kan du berätta för oss på [vår UserVoice-webbplats](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Parsa enkla JSON-blobbar

Som standard parsar [Azure Search BLOB-INDEXERA](search-howto-indexing-azure-blob-storage.md) JSON-blobbar som ett enda text segment. Ofta vill du behålla strukturen för dina JSON-dokument. Anta till exempel att du har följande JSON-dokument i Azure Blob Storage:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

BLOB-indexeraren parsar JSON-dokumentet till ett enda Azure Search-dokument. Indexeraren läser in ett index genom att matcha "text", "datePublished" och "Taggar" från källan mot identiska namngivna och angivna mål index fält.

Som anges krävs inte fält mappningar. Med ett index med fälten "text", "datePublished" och "Taggar" kan BLOB-indexeraren härleda rätt mappning utan att det finns någon fält mappning i begäran.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Parsa JSON-matriser

Du kan också använda alternativet JSON-matris. Det här alternativet är användbart när blobbar innehåller en *matris med välformulerade JSON-objekt*, och du vill att varje element ska bli ett separat Azure Search-dokument. Om du till exempel har följande JSON-BLOB kan du fylla i Azure Search index med tre separata dokument, var och en med fälten "ID" och "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

För en JSON-matris bör index definitions definitionen se ut ungefär som i följande exempel. Observera att parsingMode-parametern anger `jsonArray` parsern. Att ange rätt parser och ha rätt data inmatning är de enda två leverantörsspecifika kraven för indexera JSON-blobbar.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Observera återigen att fält mappningar kan utelämnas. Om du antar ett index med identiskt med namnet "ID" och "text", kan BLOB-indexeraren härleda rätt mappning utan en explicit fält mappnings lista.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Parsa kapslade matriser
För JSON-matriser med kapslade element kan du ange `documentRoot` en för att ange en struktur på flera nivåer. Om dina blobbar till exempel ser ut så här:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Använd den här konfigurationen för att indexera matrisen som `level2` finns i egenskapen:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="parse-blobs-separated-by-newlines"></a>Parsa blobbar avgränsade med newlines

Om blobben innehåller flera JSON-enheter avgränsade med en ny rad och du vill att varje element ska bli ett separat Azure Search dokument, kan du välja alternativet JSON-linjer. Till exempel, med följande BLOB (där det finns tre olika JSON-enheter), kan du fylla i Azure Search-index med tre separata dokument, var och en med fälten "ID" och "text".

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

För JSON-linjer bör index indefinitionen se ut ungefär som i följande exempel. Observera att parsingMode-parametern anger `jsonLines` parsern. 

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

Observera återigen att fält mappningar kan utelämnas, liknande `jsonArray` tolknings läge.

## <a name="add-field-mappings"></a>Lägg till fält mappningar

När käll-och mål fälten inte är perfekt justerade kan du definiera ett fält mappnings avsnitt i begär ande texten för explicita fält-till-fält-kopplingar.

Azure Search kan för närvarande inte indexera valfria JSON-dokument direkt eftersom det endast stöder primitiva data typer, sträng matriser och interjson-punkter. Du kan dock använda **fält mappningar** för att välja delar av ditt JSON-dokument och "lyft" dem till översta fält i Sök dokumentet. Information om grundläggande fält mappningar finns [i fält mappningar i Azure Search indexerare](search-indexer-field-mappings.md).

Gå till vårt exempel JSON-dokument:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Anta ett sökindex med följande fält `text` : typ `date` `Edm.DateTimeOffset` `Edm.String`, typ och `tags` typ `Collection(Edm.String)`. Observera skillnaden mellan "datePublished" i källa och `date` fält i indexet. Använd följande fält mappningar för att mappa JSON till önskad form:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Käll fält namnen i mappningarna anges med [JSON](https://tools.ietf.org/html/rfc6901) -pekarens notation. Du börjar med ett snedstreck för att referera till roten i JSON-dokumentet och sedan väljer du önskad egenskap (på valfri kapslings nivå) med hjälp av en snedstreck-separerad sökväg.

Du kan också referera till enskilda mat ris element genom att använda ett nollbaserat index. Om du till exempel vill välja det första elementet i matrisen "Taggar" från exemplet ovan använder du en fält mappning som detta:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Om ett käll fält namn i en sökväg för fält mappning refererar till en egenskap som inte finns i JSON, hoppas mappningen över utan fel. Detta görs så att vi kan stödja dokument med ett annat schema (som är ett vanligt användnings fall). Eftersom det inte finns någon validering måste du ta hand om att undvika skrivfel i specifikationen för fält mappning.
>
>

## <a name="see-also"></a>Se också

+ [Indexerare i Azure Search](search-indexer-overview.md)
+ [Indexera Azure-Blob Storage med Azure Search](search-howto-index-json-blobs.md)
+ [Indexera CSV-blobar med Azure Search BLOB-indexeraren](search-howto-index-csv-blobs.md)
+ [Självstudier: Sök i delvis strukturerade data från Azure Blob Storage](search-semi-structured-data.md)
