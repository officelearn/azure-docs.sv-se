---
title: Sök över JSON blobbar
titleSuffix: Azure Cognitive Search
description: Crawla Azure JSON-blobbar efter textinnehåll med hjälp av Azure Cognitive Search Blob indexeraren. Indexerare automatiserar datainmatning för valda datakällor som Azure Blob-lagring.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 37fc78971124240077a59d4ad99aa06cc408dbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533973"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Indexera JSON-blobbar med hjälp av en Blob-indexerare i Azure Cognitive Search

Den här artikeln visar hur du konfigurerar en Azure Cognitive Search [blob-indexerare](search-indexer-overview.md) för att extrahera strukturerat innehåll från JSON-dokument i Azure Blob-lagring och göra det sökbart i Azure Cognitive Search. Det här arbetsflödet skapar ett Azure Cognitive Search-index och läser in det med befintlig text som extraherats från JSON-blobbar. 

Du kan använda [portalen,](#json-indexer-portal) [REST-API:erna](#json-indexer-rest)eller [.NET SDK](#json-indexer-dotnet) för att indexera JSON-innehåll. Gemensamt för alla metoder är att JSON-dokument finns i en blob-behållare i ett Azure Storage-konto. Mer information om hur du pressar JSON-dokument från andra plattformar som inte är Azure Cognitive Search finns [i Dataimport i Azure Cognitive Search](search-what-is-data-import.md).

JSON-blobbar i Azure Blob-lagring är vanligtvis antingen ett `json`enda JSON-dokument (tolkningsläge är) eller en samling JSON-entiteter. För samlingar kan bloben ha en **rad** välformade JSON-element `jsonArray`(tolkningsläge är ). Blobbar kan också bestå av flera enskilda JSON-enheter åtskilda av `jsonLines`en nyrad (tolkningsläget är ). Parametern **parsingMode** på begäran bestämmer utdatastrukturerna.

> [!NOTE]
> Mer information om hur du indexerar flera sökdokument från en enda blob finns i [En-till-många-indexering](search-howto-index-one-to-many-blobs.md).

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Använda portalen

Den enklaste metoden för att indexera JSON-dokument är att använda en guide i [Azure-portalen](https://portal.azure.com/). Genom att tolka metadata i Azure blob-behållaren kan guiden [**Importera data**](search-import-data-portal.md) skapa ett standardindex, mappa källfält för att rikta indexfält och läsa in indexet i en enda åtgärd. Beroende på källdatas storlek och komplexitet kan du ha ett fungerande fulltextsökindex på några minuter.

Vi rekommenderar att du använder samma region eller plats för både Azure Cognitive Search och Azure Storage för lägre svarstid och för att undvika bandbreddsavgifter.

### <a name="1---prepare-source-data"></a>1 - Förbereda källdata

[Logga in på Azure-portalen](https://portal.azure.com/) och [skapa en Blob-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) för att innehålla dina data. Public Access-nivån kan ställas in på alla dess giltiga värden.

Du behöver lagringskontonamnet, behållarnamnet och en åtkomstnyckel för att hämta data i guiden **Importera data.**

### <a name="2---start-import-data-wizard"></a>2 - Guiden Starta importera data

På sidan Översikt i söktjänsten kan du [starta guiden](search-import-data-portal.md) från kommandofältet.

   ![Kommandot Importera data i portalen](./media/search-import-data-portal/import-data-cmd2.png "Starta guiden Importera data")

### <a name="3---set-the-data-source"></a>3 - Ställ in datakällan

På **sidan datakälla** måste källan vara **Azure Blob Storage**, med följande specifikationer:

+ **Data som ska extraheras** bör vara *Innehåll och metadata*. Om du väljer det här alternativet kan guiden dra slutsatsen ett indexschema och mappa fälten för import.
   
+ **Tolkningsläget** ska ställas in på *JSON-,* *JSON-matris-* eller *JSON-linjer*. 

  *JSON* artikulerar varje blob som ett enda sökdokument och visas som ett oberoende objekt i sökresultaten. 

  *JSON array* är för blobbar som innehåller välformade JSON-data - den välformade JSON motsvarar en matris med objekt, eller har en egenskap som är en matris med objekt och du vill att varje element ska artikuleras som en fristående, oberoende sökdokument. Om blobbar är komplexa och du inte väljer *JSON-matrisen* förtärs hela bloben som ett enda dokument.

  *JSON-rader* är för blobbar som består av flera JSON-entiteter avgränsade med en ny rad, där du vill att varje entitet ska artikuleras som ett fristående oberoende sökdokument. Om blobbar är komplexa och du inte väljer *JSON-linjers* tolkningsläge, intas hela bloben som ett enda dokument.
   
+ **Lagringsbehållaren** måste ange ditt lagringskonto och din behållare, eller en anslutningssträng som matchas till behållaren. Du kan hämta anslutningssträngar på blob-tjänstportalsidan.

   ![Definition av blob-datakälla](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - Hoppa över sidan "Berika innehåll" i guiden

Att lägga till kognitiva färdigheter (eller anrikning) är inte ett importkrav. Om du inte har ett specifikt behov av att lägga till [AI-anrikning](cognitive-search-concept-intro.md) i din indexeringspipeline bör du hoppa över det här steget.

Om du vill hoppa över steget klickar du på de blå knapparna längst ned på sidan för "Nästa" och "Hoppa över".

### <a name="5---set-index-attributes"></a>5 - Ange indexattribut

På sidan **Index** bör du se en lista över fält med en datatyp och en serie kryssrutor för inställning av indexattribut. Guiden kan generera en fältlista baserad på metadata och genom att prova källdata. 

Du kan mass markera attribut genom att klicka på kryssrutan högst upp i en attributkolumn. Välj **Hämtningsbar** och **sökbar** för varje fält som ska returneras till en klientapp och vara föremål för fulltextsökningsbearbetning. Du kommer att märka att heltal inte är fulltext eller suddiga sökbara (tal utvärderas ordagrant och är ofta användbara i filter).

Läs beskrivningen av [indexattribut](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) och [språkanalysatorer](https://docs.microsoft.com/rest/api/searchservice/language-support) för mer information. 

Ta en stund att granska dina val. När du har kört guiden skapas fysiska datastrukturer och du kan inte redigera dessa fält utan att släppa och återskapa alla objekt.

   ![Definition av Blob-index](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - Skapa indexerare

Guiden är helt angiven och skapar tre olika objekt i söktjänsten. Ett datakällobjekt och indexobjekt sparas som namngivna resurser i azure Cognitive Search-tjänsten. Det sista steget skapar ett indexerarobjekt. Genom att namnge indexeraren kan den finnas som en fristående resurs, som du kan schemalägga och hantera oberoende av index- och datakällobjektet, som skapas i samma guidesekvens.

Om du inte är bekant med indexerare är en *indexerare* en resurs i Azure Cognitive Search som genomsöker en extern datakälla efter sökbart innehåll. Utdataguiden **Import data** är en indexerare som genomsöker din JSON-datakälla, extraherar sökbart innehåll och importerar det till ett index på Azure Cognitive Search.

   ![Definition av Blob-indexerare](media/search-howto-index-json/import-wizard-json-indexer.png)

Klicka på **OK** om du vill köra guiden och skapa alla objekt. Indexeringen påbörjas omedelbart.

Du kan övervaka dataimporten på portalsidorna. Förloppsmeddelanden anger indexeringsstatus och hur många dokument som överförs. 

När indexeringen är klar kan du använda [Sökutforskaren](search-explorer.md) för att fråga ditt index.

> [!NOTE]
> Om du inte ser de data du förväntar dig kan du behöva ange fler attribut i fler fält. Ta bort index och indexerare som du just skapade och gå igenom guiden igen och ändra dina val för indexattribut i steg 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Använda REST-API:er

Du kan använda REST API för att indexera JSON-blobbar, efter ett tredeladt arbetsflöde som är gemensamt för alla indexerare i Azure Cognitive Search: skapa en datakälla, skapa ett index, skapa en indexerare. Dataextrahering från blob-lagring inträffar när du skickar begäran Skapa indexerare. När begäran är klar har du ett frågebart index. 

Du kan granska [REST-exempelkoden](#rest-example) i slutet av det här avsnittet som visar hur du skapar alla tre objekten. Det här avsnittet innehåller också information om [JSON-tolkningslägen,](#parsing-modes) [enstaka blobbar,](#parsing-single-blobs) [JSON-matriser](#parsing-arrays)och [kapslade matriser](#nested-json-arrays).

För kodbaserad JSON-indexering använder du [Postman](search-get-started-postman.md) och REST API för att skapa dessa objekt:

+ [Index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [datakälla](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [Indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

Åtgärdsordningen kräver att du skapar och anropar objekt i den här ordningen. I motsats till portalarbetsflödet kräver en kodmetod ett tillgängligt index för att acceptera JSON-dokument som skickas via **Begäran skapa indexerare.**

JSON-blobbar i Azure Blob-lagring är vanligtvis antingen ett enda JSON-dokument eller en JSON -matris. Blob-indexeraren i Azure Cognitive Search kan tolka antingen konstruktionen, beroende på hur du ställer in parametern **parsingMode** på begäran.

| JSON-dokument | tolkaMode | Beskrivning | Tillgänglighet |
|--------------|-------------|--------------|--------------|
| En per blob | `json` | Tolkar JSON-blobbar som ett enda textsegment. Varje JSON-blob blir ett enda Azure Cognitive Search-dokument. | Allmänt tillgängligt i både [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API och [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Flera per blob | `jsonArray` | Tolkar en JSON-matris i blobben, där varje element i matrisen blir ett separat Azure Cognitive Search-dokument.  | Allmänt tillgängligt i både [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API och [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Flera per blob | `jsonLines` | Tolkar en blob som innehåller flera JSON-entiteter (en "matris") avgränsad med en ny rad, där varje entitet blir ett separat Azure Cognitive Search-dokument. | Allmänt tillgängligt i både [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API och [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1 - Montera ingångar för begäran

För varje begäran måste du ange tjänstnamn och administratörsnyckel för Azure Cognitive Search (i POST-huvudet) och lagringskontots namn och nyckel för blob-lagring. Du kan använda [Postman](search-get-started-postman.md) för att skicka HTTP-begäranden till Azure Cognitive Search.

Kopiera följande fyra värden till Anteckningar så att du kan klistra in dem i en begäran:

+ Namn på Azure Cognitive Search-tjänst
+ Administratörsnyckel för Azure Cognitive Search
+ Azure Storage-kontonamn
+ Nyckeln för Azure-lagringskonto

Du hittar dessa värden i portalen:

1. Kopiera söktjänstens URL på portalsidorna för Azure Cognitive Search från sidan Översikt.

2. I det vänstra navigeringsfönstret klickar du på **Nycklar** och kopierar antingen primär- eller sekundärnyckeln (de är likvärdiga).

3. Växla till portalsidorna för ditt lagringskonto. Klicka på **Snabbtangenter**under **Inställningar**i det vänstra navigeringsfönstret. På den här sidan finns både kontonamnet och nyckeln. Kopiera lagringskontonamnet och en av nycklarna till Anteckningar.

### <a name="2---create-a-data-source"></a>2 - Skapa en datakälla

Det här steget innehåller information om datakällans anslutning som används av indexeraren. Datakällan är ett namngivet objekt i Azure Cognitive Search som bevarar anslutningsinformationen. Datakälltypen `azureblob`bestämmer vilka dataextraheringsbeteenden som anropas av indexeraren. 

Ersätt giltiga värden för tjänstnamn, administratörsnyckel, lagringskonto och platshållare för kontonyckel.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 - Skapa ett målsökindex 

Indexerare paras ihop med ett indexschema. Om du använder API:et (i stället för portalen) förbereder du ett index i förväg så att du kan ange det på indexeringsåtgärden.

Indexet lagrar sökbart innehåll i Azure Cognitive Search. Om du vill skapa ett index anger du ett schema som anger fälten i ett dokument, attribut och andra konstruktioner som formar sökupplevelsen. Om du skapar ett index som har samma fältnamn och datatyper som källan matchar indexeraren käll- och målfälten, vilket sparar arbetet med att uttryckligen mappa fälten.

I följande exempel visas en [begäran om skapa index.](https://docs.microsoft.com/rest/api/searchservice/create-index) Indexet har ett sökbart `content` fält för att lagra texten som extraherats från blobbar:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 - Konfigurera och kör indexeraren

Som med ett index och en datakälla, och indexerare är också ett namngivet objekt som du skapar och återanvänder på en Azure Cognitive Search-tjänst. En fullständigt angiven begäran om att skapa en indexerare kan se ut så här:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Indexerkonfigurationen finns i brödtexten för begäran. Det kräver en datakälla och ett tomt målindex som redan finns i Azure Cognitive Search. 

Schema och parametrar är valfria. Om du utelämnar dem körs indexeraren omedelbart med hjälp av `json` som tolkningsläge.

Den här indexeraren innehåller inte fältmappningar. I indexeringsdefinitionen kan du utelämna **fältmappningar** om egenskaperna för käll-JSON-dokumentet matchar fälten i målsökindexet. 


### <a name="rest-example"></a>EXEMPEL PÅ REST

Det här avsnittet är en sammanfattning av alla begäranden som används för att skapa objekt. En diskussion om komponenter finns i föregående avsnitt i den här artikeln.

### <a name="data-source-request"></a>Begäran om datakälla

Alla indexerare kräver ett datakällobjekt som tillhandahåller anslutningsinformation till befintliga data. 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Begäran om index

Alla indexerare kräver ett målindex som tar emot data. Brödtexten i begäran definierar indexschemat, som består av fält, som tillskrivs stödja önskade beteenden i ett sökbart index. Det här indexet ska vara tomt när du kör indexeraren. 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Begäran om indexerare

Den här begäran visar en fullständigt angiven indexerare. Den innehåller fältmappningar som utelämnades i tidigare exempel. Kom ihåg att "schema", "parametrar" och "fieldMappings" är valfria så länge det finns en tillgänglig standard. Om du utelämnar "schema" körs indexeraren omedelbart. Om du utelämnar "parsingMode" används indexet som standard.Utelämna "parsingMode" causes the index to use the "json" default.

Om du skapar indexeraren på Azure Cognitive Search utlöses dataimport. Det körs omedelbart, och därefter på ett schema om du har gett en.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

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

## <a name="use-net-sdk"></a>Använda .NET SDK

.NET SDK har full paritet med REST API. Vi rekommenderar att du granskar det tidigare REST API-avsnittet för att lära dig begrepp, arbetsflöde och krav. Du kan sedan referera till följande .NET API-referensdokumentation för att implementera en JSON-indexerare i hanterad kod.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Tolkningslägen

JSON blobbar kan anta flera formulär. Parametern **parsingMode** på JSON-indexeraren avgör hur JSON-blobinnehåll tolkas och struktureras i ett Azure Cognitive Search-index:

| tolkaMode | Beskrivning |
|-------------|-------------|
| `json`  | Indexera varje blob som ett enda dokument. Det här är standard. |
| `jsonArray` | Välj det här läget om dina blobbar består av JSON-matriser och du behöver varje element i matrisen för att bli ett separat dokument i Azure Cognitive Search. |
|`jsonLines` | Välj det här läget om dina blobbar består av flera JSON-entiteter, som avgränsas av en ny rad, och du behöver varje entitet för att bli ett separat dokument i Azure Cognitive Search. |

Du kan se ett dokument som ett enda objekt i sökresultaten. Om du vill att varje element i matrisen ska visas i `jsonArray` `jsonLines` sökresultaten som ett oberoende objekt använder du alternativet eller.

I indexerdefinitionen kan du också använda [fältmappningar](search-indexer-field-mappings.md) för att välja vilka egenskaper för käll-JSON-dokumentet som ska användas för att fylla i målsökindexet. Om `jsonArray` matrisen finns som egenskap på lägre nivå kan du ange en dokumentrot som anger var matrisen placeras i bloben om matrisen finns som egenskap på lägre nivå.

> [!IMPORTANT]
> När du `json` `jsonArray` använder `jsonLines` eller tolkar läge förutsätter Azure Cognitive Search att alla blobbar i datakällan innehåller JSON. Om du behöver stödja en blandning av JSON och icke-JSON blobbar i samma datakälla, låt oss veta på [vår UserVoice webbplats](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Tolka enda JSON blobbar

Som standard [tolkar Azure Cognitive Search blob indexerar](search-howto-indexing-azure-blob-storage.md) JSON-blobbar som en enda textbit. Ofta vill du bevara strukturen på dina JSON-dokument. Anta till exempel att du har följande JSON-dokument i Azure Blob-lagring:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Blob-indexeraren tolkar JSON-dokumentet till ett enda Azure Cognitive Search-dokument. Indexeraren läser in ett index genom att matcha "text", "datePublished" och "tags" från källan mot identiskt namngivna och maskinskrivna målindexfält.

Som nämnts krävs inte fältmappningar. Med tanke på ett index med fälten "text", "datePublished och "tags" kan blobindexeraren sluta sig till rätt mappning utan en fältmappning i begäran.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Tolka JSON-matriser

Du kan också använda alternativet JSON-matris. Det här alternativet är användbart när blobbar innehåller en *matris med välformade JSON-objekt*och du vill att varje element ska bli ett separat Azure Cognitive Search-dokument. Med tanke på följande JSON-blob kan du till exempel fylla i ditt Azure Cognitive Search-index med tre separata dokument, var och en med fälten "id" och "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

För en JSON-matris bör indexeringsdefinitionen se ut ungefär som i följande exempel. Observera att parametern parsingMode `jsonArray` anger tolken. Att ange rätt tolk och ha rätt datainmatning är de enda två matrisspecifika kraven för indexering av JSON-blobbar.

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

Observera återigen att fältmappningar kan utelämnas. Om du antar ett index med identiskt namngivna id- och "textfält" kan blobindexeraren sluta sig till rätt mappning utan en explicit fältmappningslista.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Tolka kapslade matriser
För JSON-matriser med kapslade `documentRoot` element kan du ange en för att ange en struktur på flera nivåer. Om dina blobbar till exempel ser ut så här:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Använd den här konfigurationen för `level2` att indexera matrisen i egenskapen:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="parse-blobs-separated-by-newlines"></a>Tolka blobbar åtskilda av newlines

Om din blob innehåller flera JSON-entiteter avgränsade med en ny rad och du vill att varje element ska bli ett separat Azure Cognitive Search-dokument, kan du välja alternativet JSON-rader. Med tanke på följande blob (där det finns tre olika JSON-entiteter) kan du till exempel fylla i ditt Azure Cognitive Search-index med tre separata dokument, var och en med fälten "id" och "text".

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

För JSON-rader bör indexeringsdefinitionen se ut ungefär som i följande exempel. Observera att parametern parsingMode `jsonLines` anger tolken. 

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

Observera återigen att fältmappningar kan utelämnas, `jsonArray` ungefär som tolkningsläget.

## <a name="add-field-mappings"></a>Lägga till fältmappningar

När käll- och målfält inte är helt justerade kan du definiera ett fältmappningsavsnitt i begärandetexten för explicita fält-till-fält-associationer.

Azure Cognitive Search kan för närvarande inte indexera godtyckliga JSON-dokument direkt eftersom den endast stöder primitiva datatyper, strängmatriser och GeoJSON-punkter. Du kan dock använda **fältmappningar** för att välja delar av JSON-dokumentet och "lyfta" dem till fält på den översta nivån i sökdokumentet. Mer information om grundläggande fältmappningar finns [i Fältmappningar i Azure Cognitive Search-indexerare](search-indexer-field-mappings.md).

Se över vårt exempel JSON dokument:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Anta att ett sökindex `text` med `Edm.String`följande `date` fält: av typen , av typen `Edm.DateTimeOffset`och `tags` av typen `Collection(Edm.String)`. Lägg märke till avvikelsen mellan "datePublished" i käll- och `date` fältet i indexet. Om du vill mappa din JSON till önskad form använder du följande fältmappningar:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Källfältnamnen i mappningarna anges med hjälp av [notationen JSON-pekare.](https://tools.ietf.org/html/rfc6901) Du börjar med ett snedstreck för att referera till roten i JSON-dokumentet och väljer sedan önskad egenskap (på godtycklig kapslingsnivå) med hjälp av framåtsnedstrecksavgränsad sökväg.

Du kan också referera till enskilda matriselement med hjälp av ett nollbaserat index. Om du till exempel vill välja det första elementet i matrisen "taggar" från exemplet ovan använder du en fältmappning så här:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Om ett källfältnamn i en fältmappningssökväg refererar till en egenskap som inte finns i JSON hoppas mappningen över utan fel. Detta görs så att vi kan stödja dokument med ett annat schema (vilket är ett vanligt användningsfall). Eftersom det inte finns någon validering måste du vara noga med att undvika stavfel i fältmappningsspecifikationen.
>
>

## <a name="see-also"></a>Se även

+ [Indexerare i Azure Cognitive Search](search-indexer-overview.md)
+ [Indexera Azure Blob Storage med Azure Cognitive Search](search-howto-index-json-blobs.md)
+ [Indexera CSV-blobbar med Azure Cognitive Search blob indexerare](search-howto-index-csv-blobs.md)
+ [Självstudiekurs: Sök efter semistrukturerad data från Azure Blob-lagring](search-semi-structured-data.md)
