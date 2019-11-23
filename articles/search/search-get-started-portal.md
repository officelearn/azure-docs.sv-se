---
title: Create a search index in the Azure portal
titleSuffix: Azure Cognitive Search
description: In this portal quickstart, learn how to use the Import Data wizard to create, load, and query your first search index in Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: be2425d97573e7990ea7f0dfd4c2d999e85fe922
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74407001"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>Quickstart: Create an Azure Cognitive Search index in the Azure portal
> [!div class="op_single_selector"]
> * [Portalen](search-get-started-portal.md)
> * [C#](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Node.js](search-get-started-nodejs.md)
> * [PowerShell](search-get-started-powershell.md)
> * [Postman](search-get-started-postman.md)
> * [Python](search-get-started-python.md)

Use the portal's **Import data** wizard and **Search explorer** tools to quickly ramp up on concepts, and write interesting queries against an index within minutes.

If the tools are too limiting, you can consider a [code-based introduction to programming Azure Cognitive Search in .NET](search-howto-dotnet-sdk.md) or use [Postman for making REST API calls](search-get-started-postman.md). 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="prerequisites"></a>Krav

[Create an Azure Cognitive Search service](search-create-service-portal.md) or [find an existing service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under your current subscription. You can use a free service for this quickstart. 

### <a name="check-for-space"></a>Kontrollera utrymmet

Många kunder börjar med den kostnadsfria tjänsten. Den här versionen är begränsad till tre index, tre datakällor och tre indexerare. Kontrollera att du har plats för extra objekt innan du börjar. Med den här guiden kan du skapa ett objekt av varje sort.

Avsnitten i tjänstinstrumentpanelen visar hur många index, indexerare och datakällor som du redan har. 

![En lista över index, indexerare och datakällor](media/search-get-started-portal/tiles-indexers-datasources.png)

## <a name="create-index"></a> Skapa ett index och läsa in data

Sökfrågor itererar över ett [*index*](search-what-is-an-index.md) som innehåller sökbara data, metadata och ytterligare konstruktioner som optimerar vissa sökbeteenden.

For this tutorial, we use a built-in sample dataset that can be crawled using an [*indexer*](search-indexer-overview.md) via the [**Import data** wizard](search-import-data-portal.md). En indexerare är en källspecifik crawler som kan läsa metadata och innehåll från Azure-datakällor som stöds. Normalt sett används indexerare programmässigt, men i portalen så kan du komma åt dem via guiden **Importera data**. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Steg 1 – starta guiden Importera data och skapa en datakälla

1. On the Azure Cognitive Search service dashboard, click **Import data** on the command bar to create and populate a search index.

   ![Kommandot Importera data](media/search-get-started-portal/import-data-cmd.png)

2. In the wizard, click **Connect to your data** > **Samples** > **hotels-sample**. Den här datakällan är inbyggd. Om du skapar din egen datakälla så behöver du ange ett namn, typ och anslutningsinformation. När du har skapat den blir den en ”befintlig datakälla” som kan återanvändas i andra importåtgärder.

   ![Välj exempeldatauppsättning](media/search-get-started-portal/import-datasource-sample.png)

3. Fortsätt till nästa sida.

### <a name="step-2---skip-the-enrich-content-page"></a>Step 2 - Skip the "Enrich content" page

The wizard supports the creation of an [AI enrichment pipeline](cognitive-search-concept-intro.md) for incorporating the Cognitive Services AI algorithms into indexing. 

Vi hoppar över det här steget för tillfället och går direkt till **Anpassa målindex**.

   ![Hoppa över steget Kognitiva kunskaper](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> You can step through an AI-indexing example in a [quickstart](cognitive-search-quickstart-blob.md) or [tutorial](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Steg 3 – Konfigurera index

Skapandet av index är vanligtvis en kodbaserad övning som slutförs innan du läser in data. Som den här guiden visar kan dock guiden skapa ett grundläggande index för valfri datakälla som den kan crawla. Ett index kräver minst ett namn och en samling fält. Ett av fälten ska vara markerat som dokumentnyckeln som fungerar som en unik identifierare för dokumentet. Du kan också välja att använda språkanalys eller språkförslag för automatisk komplettering eller frågeförslag.

Fälten har datatyper och attribut. Kryssrutorna högst upp är *indexattribut* som styr hur fältet används.

* **Hämtningsbar** innebär att det visas i listor med sökresultat. You can mark individual fields as off limits for search results by clearing this checkbox, for example for fields used only in filter expressions.
* **Nyckel** är den unika dokumentdentifieraren. Den är alltid en sträng och den är obligatorisk.
* **Filtrerbar**, **Sorterbar** och **Fasetterbar** avgör om fält användas i ett filter, en sortering eller en fasetterad navigeringsstruktur.
* **Sökbar** innebär att ett fält ingår i fulltextsökning. Strängarna är sökbara. Numeriska fält och fält för booleska värden är ofta markerade som icke sökbara.

Lagringskraven varierar inte till följd av ditt val. Om du till exempel anger attributet **Hämtningsbar** på flera fält så ökar inte lagringskraven.

Som standard söker guiden igenom datakällan för att hitta unika identifierare som utgör själva grunden för sökordsfältet. *Strängar* får attributen **Hämtningsbar** och **Sökbar**. *Heltal* får attributen **Hämtningsbar**, **Filtrerbar**, **Sorterbar** och **Fasetterbar**.

1. Acceptera alla standardinställningar. 

   If you rerun the wizard a second time using an existing hotels data source, the index won't be configured with default attributes. Du måste då manuellt välja attribut för framtida importer. 

   ![Generated hotels index](media/search-get-started-portal/hotelsindex.png)

2. Fortsätt till nästa sida.


### <a name="step-4---configure-indexer"></a>Steg 4 – Konfigurera indexeraren

Klicka på **Indexnamn** > **Namn** i guiden **Importera data** och skriv in ett namn på indexeraren.

Det här objektet definierar en körbar process. Du kan lägga till det i ett återkommande schema, men för tillfället använder du standardalternativet för att köra indexeraren en gång direkt.

Klicka på **Skicka** för att skapa och köra indexeraren samtidigt.

  ![hotels indexer](media/search-get-started-portal/hotels-indexer.png)

## <a name="monitor-progress"></a>Övervaka förloppet

Guiden ska ta dig till listan med indexerare där du kan övervaka förloppet. För självnavigering går du till översikten över sidan och klickar på **indexerare**.

Det kan ta några minuter för portalen att uppdatera sidan men du borde se den nyligen skapade indexeraren i listan med status Pågående eller Lyckades, tillsammans med antalet dokument som indexerats.

   ![Meddelande om pågående indexeringsaktiviteter](media/search-get-started-portal/indexers-inprogress.png)

## <a name="view-the-index"></a>Visa indexet

The main service page provides links to the resources created in your Azure Cognitive Search service.  Om du vill visa det index som du precis skapat klickar du på **Index** i listan över länkar. 

Wait for the portal page to refresh. After a few minutes, you should see the index with a document count and storage size.

   ![Indexlista på instrumentpanelen för tjänster](media/search-get-started-portal/indexes-list.png)

From this list, you can click on the *hotels-sample* index that you just created, view the index schema. Du kan också lägga till nya fält. 

Fliken **Fält** visar indexschemat. Rulla till slutet av listan för att ange ett nytt fält. Oftast kan du inte ändra befintliga fält. Existing fields have a physical representation in Azure Cognitive Search and are thus non-modifiable, not even in code. Om du vill ändra ett befintligt fält från grunden så skapar du ett nytt index och tar bort det ursprungliga.

   ![exempel på indexdefinition](media/search-get-started-portal/sample-index-def.png)

Andra konstruktioner, t.ex. bedömningsprofiler och CORS-alternativ, kan läggas till när som helst.

Ägna några minuter åt att gå igenom definitionsalternativen för index så att du förstår vad du kan och inte kan redigera när du utformar ett index. Nedtonade alternativ indikerar att ett värde inte kan ändras eller tas bort. 

## <a name="query-index"></a> Fråga med Sökutforskaren

Nu bör du ha ett sökindex som du kan börja köra frågor mot med hjälp av den inbyggda frågesidan [**Sökutforskaren**](search-explorer.md). Den innehåller en sökruta så att du kan testa godtyckliga frågesträngar.

**Sökutforskaren** kan endast hantera [REST-API-förfrågningar](https://docs.microsoft.com/rest/api/searchservice/search-documents), men stöder syntax för både [enkel frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) och [fullständig Lucene-frågeparser](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), samt alla tillgängliga sökparametrar i åtgärder med [REST-API:et för dokumentsökning](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples).

> [!TIP]
> The following steps are demonstrated at 6m08s into the [Azure Cognitive Search Overview video](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Klicka på **Sökutforskaren** i kommandofältet.

   ![Kommandot Sökutforskaren](media/search-get-started-portal/search-explorer-cmd.png)

2. From the **Index** dropdown, choose  *hotels-sample-index*. Click the **API Version** dropdown, to see which REST APIs are available. For the queries below, use the generally available version (2019-05-06).

   ![Index- och API-kommandon](media/search-get-started-portal/search-explorer-changeindex.png)

3. Klistra in frågesträngarna nedan i sökfältet och klicka på **Sök**.

   ![Frågesträng och sökknapp](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Exempelfrågor

Du kan ange termer och fraser på ett liknande sätt som du gör i en Bing- eller Google-sökning eller fullständigt angivna frågeuttryck. Resultat returneras som utförliga JSON-dokument.

### <a name="simple-query-with-top-n-results"></a>Exempelfråga med de N främsta resultaten

#### <a name="example-string-query-searchspa"></a>Exempel (strängfråga): `search=spa`

* The **search** parameter is used to input a keyword search for full text search, in this case, returning hotel data for those containing *spa* in any searchable field in the document.

* **Sökutforskaren** returnerar resultat i JSON, vilket kan vara detaljerat och svårläst om dokumenten har en kompakt struktur. Detta är avsiktligt eftersom det är viktigt för utvecklingssyften att kunna se hela dokumentet, särskilt under testning. För en bättre användarupplevelse måste du skriva kod som [hanterar sökresultaten](search-pagination-page-layout.md) så att viktiga element framhävs.

* Dokument består av alla fält som är markerade som ”hämtningsbara” i indexet. To view index attributes in the portal, click *hotels-sample* in the **Indexes** list.

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Exempel (parameteriserad fråga): `search=spa&$count=true&$top=10`

* Symbolen **&** används för att lägga till sökparametrar, som kan anges i valfri ordning.

* The **$count=true** parameter returns the total count of all documents returned. Det här värdet visas längst upp i sökresultaten. Du kan verifiera filterfrågor genom att övervaka ändringar som rapporterats via **$count=true**. Mindre antal indikerar att filtret fungerar.

* The **$top=10** returns the highest ranked 10 documents out of the total. By default, Azure Cognitive Search returns the first 50 best matches. Du kan öka eller minska antalet via **$top**.

### <a name="filter-query"></a> Filtrera frågan

Filter tas med i sökbegäranden när du lägger till parametern **$filter**. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Exempel (filtrerat): `search=beach&$filter=Rating gt 4`

* Parametern **$filter** returnerar resultat som matchar de kriterier som du har angett. In this case, ratings greater than 4.

* Syntaxen för filtret är en OData-konstruktion. Mer information finns i [OData-filtersyntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-query"></a> Fasettera frågan

Fasettfilter tas med i sökbegäranden. Du kan använda parametern facet för att returnera ett aggregerat antal dokument som matchar ett fasettvärde som du anger.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Exempel (fasetterat med områdesreducering): `search=*&facet=Category&$top=2`

* **search=** * är en tom sökning. Tomma sökningar söker efter allt. En anledning till att skicka en tom fråga är att filtrera eller fasettera över hela uppsättningen dokument. For example, you want a faceting navigation structure to consist of all hotels in the index.
* **facet** returnerar en navigeringsstruktur som du kan skicka till en kontroll i användargränssnittet. Den returnerar kategorier och antal. In this case, categories are based on a field conveniently called *Category*. There is no aggregation in Azure Cognitive Search, but you can approximate aggregation via `facet`, which gives a count of documents in each category.

* **$top=2** hämtar tillbaka två dokument, som visar att du kan använda `top` för att både minska eller öka resultat.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Example (facet on numeric values): `search=spa&facet=Rating`

* This query is facet for rating, on a text search for *spa*. The term *Rating* can be specified as a facet because the field is marked as retrievable, filterable, and facetable in the index, and the values it contains (numeric, 1 through 5), are suitable for categorizing listings into groups.

* Endast filtrerbara fält kan fasetteras. Endast hämtningsbara fält kan returneras i resultatet.

* The *Rating* field is double-precision floating point and the grouping will be by precise value. For more information on grouping by interval (for instance, "3 star ratings," "4 star ratings," etc.), see [How to implement faceted navigation in Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-faceted-navigation#filter-based-on-a-range).


### <a name="highlight-query"></a> Markera sökresultat

Träffmarkering innebär att formatera all text som matchar sökordet på ett särskilt sätt inom ett givet fält. Om sökordet begravt långt ned i en beskrivning kan du använda träffmarkering för att göra det lättare att hitta ordet.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Exempel (markering): `search=beach&highlight=Description`

* In this example, the formatted word *beach* is easier to spot in the description field.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Exempel (språklig analys): `search=beaches&highlight=Description`

* Full text search recognizes basic variations in word forms. In this case, search results contain highlighted text for "beach", for hotels that have that word in their searchable fields, in response to a keyword search on "beaches". Tack vare språkanalysen kan olika former av samma ord visas i resultaten. 

* Azure Cognitive Search supports 56 analyzers from both Lucene and Microsoft. The default used by Azure Cognitive Search is the standard Lucene analyzer.

### <a name="fuzzy-search"></a> Prova fuzzy-sökning

By default, misspelled query terms, like *seatle* for "Seattle", fail to return matches in typical search. Följande exempel returnerar inga resultat.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Exempel (felstavat ord, ohanterat): `search=seatle`

Du kan använda fuzzy-sökning för att hantera felstavningar. Fuzzy-sökning aktiveras när du använder den fullständiga Lucene-frågesyntaxen, som aktiveras när du gör två saker: när du anger **queryType = full** i frågan och när du lägger till **~** i söksträngen.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Exempel (felstavat ord, hanterat): `search=seatle~&queryType=full`

This example now returns documents that include matches on "Seattle".

När **queryType** inte är angivet används den enklare standardfrågeparsern. Den enklare frågeparsern är snabbare, men om du behöver tillgång till fuzzy-sökning, reguljära uttryck, närhetssökning eller andra typer av avancerade frågetyper behöver du den fullständiga syntaxen.

Fuzzy-sökning och sökning med jokertecken påverkar sökresultatet. Språkliga analyser utförs inte med dessa frågeformat. Before using fuzzy and wildcard search, review [How full text search works in Azure Cognitive Search](search-lucene-query-architecture.md#stage-2-lexical-analysis) and look for the section about exceptions to lexical analysis.

For more information about query scenarios enabled by the full query parser, see [Lucene query syntax in Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="geo-search"></a> Prova geospatial sökning

Geospatial sökning stöds av [datatypen edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) i fält som innehåller koordinater. Geosearch är en filtertyp som finns med i [OData-filtersyntaxen](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Exempel (geo-koordinatfilter): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

Med den här exempelfrågan filtreras alla resultat efter platsdata, där resultaten måste ligga mindre än 5 km från en given plats (koordinaterna anges med latitud och longitud). Om du lägger till **$count** kan du se hur många resultat som returneras när du ändrar antingen avståndet eller koordinaterna.

Geospatial sökning kan vara användbart om sökprogrammet har en funktion av typen ”hitta en bensinstation i närheten av där jag befinner mig” eller om programmet har en funktion för kartnavigering. Det är dock inte fråga om någon fulltextsökning. If you have user requirements for searching on a city or country/region by name, add fields containing city or country/region names, in addition to coordinates.

## <a name="takeaways"></a>Lärdomar

This tutorial provided a quick introduction to Azure Cognitive Search using the Azure portal.

Du lärde dig hur du skapar ett sökindex med hjälp av guiden **Importera data**. Du lärde dig om [indexerare](search-indexer-overview.md), som är den drivande kraften bakom guiden, samt om det grundläggande arbetsflödet vid indexgenerering, inklusive vilka [ändringar du kan göra i ett publicerat index](https://docs.microsoft.com/rest/api/searchservice/update-index).

Med hjälp av **Sökutforskaren** i Azure-portalen lärde du dig grundläggande frågesyntax genom praktiska exempel som demonstrerade viktiga funktioner som filter, markering av träffar, fuzzy-sökning och geo-sökning.

Du har också läst hur du hittar index, indexerare och datakällor i portalen. När du får nya datakällor i framtiden kan du använda portalen för att snabbt kontrollera deras definitioner eller fältsamlingar utan större ansträngningar.

## <a name="clean-up-resources"></a>Rensa resurser

When you're working in your own subscription, it's a good idea at the end of a project to identify whether you still need the resources you created. Resources left running can cost you money. You can delete resources individually or delete the resource group to delete the entire set of resources.

You can find and manage resources in the portal, using the **All resources** or **Resource groups** link in the left-navigation pane.

If you are using a free service, remember that you are limited to three indexes, indexers, and data sources. You can delete individual items in the portal to stay under the limit. 

## <a name="next-steps"></a>Nästa steg

While the portal is useful for initial exploration and small tasks, reviewing the APIs early on will help you understand the concepts and workflow on a deeper level:

> [!div class="nextstepaction"]
> [Skapa ett index med .NET SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)