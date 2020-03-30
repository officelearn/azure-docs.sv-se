---
title: Skapa ett sökindex i Azure-portalen
titleSuffix: Azure Cognitive Search
description: I den här snabbstarten för Azure-portalen använder du guiden Importera data för att skapa, läsa in och fråga ditt första sökindex i Azure Cognitive Search.
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 8324ca0184c508591fa4568175bad0f606f952a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369457"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>Snabbstart: Skapa ett Azure Cognitive Search-index i Azure-portalen
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [C#](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Node.js](search-get-started-nodejs.md)
> * [Powershell](search-get-started-powershell.md)
> * [Postman](search-get-started-postman.md)
> * [Python](search-get-started-python.md)

Använd portalens **importdataguide** och **sökutforskaren** för att snabbt öka begreppen och skriva intressanta frågor mot ett index inom några minuter.

Om verktygen är för begränsande kan du överväga en [kodbaserad introduktion till programmering av Azure Cognitive Search i .NET](search-howto-dotnet-sdk.md) eller använda [Postman för att ringa REST API-anrop](search-get-started-postman.md). 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar. 

## <a name="prerequisites"></a>Krav

[Skapa en Azure Cognitive Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst för den här snabbstarten. 

### <a name="check-for-space"></a>Kontrollera utrymmet

Många kunder börjar med den kostnadsfria tjänsten. Den här versionen är begränsad till tre index, tre datakällor och tre indexerare. Kontrollera att du har plats för extra objekt innan du börjar. Med den här guiden kan du skapa ett objekt av varje sort.

Avsnitten i tjänstinstrumentpanelen visar hur många index, indexerare och datakällor som du redan har. 

![En lista över index, indexerare och datakällor](media/search-get-started-portal/tiles-indexers-datasources.png)

## <a name="create-an-index-and-load-data"></a><a name="create-index"></a> Skapa ett index och läsa in data

Sökfrågor itererar över ett [*index*](search-what-is-an-index.md) som innehåller sökbara data, metadata och ytterligare konstruktioner som optimerar vissa sökbeteenden.

För den här självstudien använder vi en inbyggd exempeldatauppsättning som kan genomsökas med hjälp av en [*indexerare*](search-indexer-overview.md) via [ **guiden Importera data** ](search-import-data-portal.md). En indexerare är en källspecifik crawler som kan läsa metadata och innehåll från Azure-datakällor som stöds. Normalt sett används indexerare programmässigt, men i portalen så kan du komma åt dem via guiden **Importera data**. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Steg 1 – starta guiden Importera data och skapa en datakälla

1. På instrumentpanelen för Azure Cognitive Search-tjänsten klickar du på **Importera data** i kommandofältet för att skapa och fylla i ett sökindex.

   ![Kommandot Importera data](media/search-get-started-portal/import-data-cmd.png)

2. Klicka på **Anslut till data** > **Exempel på** > **hotell-exempel**i guiden. Den här datakällan är inbyggd. Om du skapar din egen datakälla så behöver du ange ett namn, typ och anslutningsinformation. När du har skapat den blir den en ”befintlig datakälla” som kan återanvändas i andra importåtgärder.

   ![Välj exempeldatauppsättning](media/search-get-started-portal/import-datasource-sample.png)

3. Fortsätt till nästa sida.

### <a name="step-2---skip-the-enrich-content-page"></a>Steg 2 - Hoppa över sidan "Berika innehåll"

Guiden stöder skapandet av en [AI-anrikningspipeline](cognitive-search-concept-intro.md) för att införliva Cognitive Services AI-algoritmer i indexeringen. 

Vi hoppar över det här steget för tillfället och går direkt till **Anpassa målindex**.

   ![Hoppa över steget Kognitiva kunskaper](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> Du kan gå igenom ett AI-indexeringsexempel i en [snabbstart](cognitive-search-quickstart-blob.md) eller [självstudiekurs](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Steg 3 – Konfigurera index

Skapandet av index är vanligtvis en kodbaserad övning som slutförs innan du läser in data. Som den här guiden visar kan dock guiden skapa ett grundläggande index för valfri datakälla som den kan crawla. Ett index kräver minst ett namn och en samling fält. Ett av fälten ska vara markerat som dokumentnyckeln som fungerar som en unik identifierare för dokumentet. Du kan också välja att använda språkanalys eller språkförslag för automatisk komplettering eller frågeförslag.

Fälten har datatyper och attribut. Kryssrutorna högst upp är *indexattribut* som styr hur fältet används.

* **Hämtningsbar** innebär att det visas i listor med sökresultat. Du kan markera enskilda fält som utanför gränserna för sökresultat genom att avmarkera den här kryssrutan, till exempel för fält som bara används i filteruttryck.
* **Nyckel** är den unika dokumentdentifieraren. Den är alltid en sträng och den är obligatorisk.
* **Filtrerbar**, **Sorterbar** och **Fasetterbar** avgör om fält användas i ett filter, en sortering eller en fasetterad navigeringsstruktur.
* **Sökbar** innebär att ett fält ingår i fulltextsökning. Strängarna är sökbara. Numeriska fält och fält för booleska värden är ofta markerade som icke sökbara.

Lagringskraven varierar inte till följd av ditt val. Om du till exempel anger attributet **Hämtningsbar** på flera fält så ökar inte lagringskraven.

Som standard söker guiden igenom datakällan för att hitta unika identifierare som utgör själva grunden för sökordsfältet. *Strängar tillskrivs* som **Hämtningsbara** och **sökbara**. *Heltal tillskrivs* som **Retrievable**, **Filterable,** **Sortable**och **Facetable**.

1. Acceptera alla standardinställningar. 

   Om du kör guiden igen en andra gång med hjälp av en befintlig hotelldatakälla konfigureras inte indexet med standardattribut. Du måste då manuellt välja attribut för framtida importer. 

   ![Genererat hotellindex](media/search-get-started-portal/hotelsindex.png)

2. Fortsätt till nästa sida.


### <a name="step-4---configure-indexer"></a>Steg 4 – Konfigurera indexeraren

Klicka på **Indexnamn** > **Namn** i guiden **Importera data** och skriv in ett namn på indexeraren.

Det här objektet definierar en körbar process. Du kan lägga till det i ett återkommande schema, men för tillfället använder du standardalternativet för att köra indexeraren en gång direkt.

Klicka på **Skicka** för att skapa och köra indexeraren samtidigt.

  ![hotell indexerare](media/search-get-started-portal/hotels-indexer.png)

## <a name="monitor-progress"></a>Övervaka förloppet

Guiden ska ta dig till listan med indexerare där du kan övervaka förloppet. För självnavigering går du till översikten över sidan och klickar på **indexerare**.

Det kan ta några minuter för portalen att uppdatera sidan men du borde se den nyligen skapade indexeraren i listan med status Pågående eller Lyckades, tillsammans med antalet dokument som indexerats.

   ![Meddelande om pågående indexeringsaktiviteter](media/search-get-started-portal/indexers-inprogress.png)

## <a name="view-the-index"></a>Visa indexet

Huvudtjänstsidan innehåller länkar till de resurser som skapas i din Azure Cognitive Search-tjänst.  Om du vill visa det index som du precis skapat klickar du på **Index** i listan över länkar. 

Vänta tills portalsidan har uppdaterats. Efter några minuter bör du se indexet med ett antal dokument och lagringsstorlek.

   ![Indexlista på instrumentpanelen för tjänster](media/search-get-started-portal/indexes-list.png)

Från den här listan kan du klicka på *det hotell-exempelindex* som du just skapade, visa indexschemat. Du kan också lägga till nya fält. 

Fliken **Fält** visar indexschemat. Rulla till slutet av listan för att ange ett nytt fält. Oftast kan du inte ändra befintliga fält. Befintliga fält har en fysisk representation i Azure Cognitive Search och kan därför inte ändras, inte ens i kod. Om du vill ändra ett befintligt fält från grunden så skapar du ett nytt index och tar bort det ursprungliga.

   ![exempel på indexdefinition](media/search-get-started-portal/sample-index-def.png)

Andra konstruktioner, t.ex. bedömningsprofiler och CORS-alternativ, kan läggas till när som helst.

Ägna några minuter åt att gå igenom definitionsalternativen för index så att du förstår vad du kan och inte kan redigera när du utformar ett index. Nedtonade alternativ indikerar att ett värde inte kan ändras eller tas bort. 

## <a name="query-using-search-explorer"></a><a name="query-index"></a> Fråga med Sökutforskaren

Nu bör du ha ett sökindex som du kan börja köra frågor mot med hjälp av den inbyggda frågesidan [**Sökutforskaren**](search-explorer.md). Den innehåller en sökruta så att du kan testa godtyckliga frågesträngar.

**Sökutforskaren** kan endast hantera [REST-API-förfrågningar](https://docs.microsoft.com/rest/api/searchservice/search-documents), men stöder syntax för både [enkel frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) och [fullständig Lucene-frågeparser](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), samt alla tillgängliga sökparametrar i åtgärder med [REST-API:et för dokumentsökning](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples).

> [!TIP]
> Följande steg visas på 6m08s i [Azure Cognitive Search Overview video](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Klicka på **Sökutforskaren** i kommandofältet.

   ![Kommandot Sökutforskaren](media/search-get-started-portal/search-explorer-cmd.png)

2. Välj *hotell-sample-index*i listrutan **Index** . Klicka på listrutan **API-version** för att se vilka REST-API:er som är tillgängliga. För frågorna nedan använder du den allmänt tillgängliga versionen (2019-05-06).

   ![Index- och API-kommandon](media/search-get-started-portal/search-explorer-changeindex.png)

3. Klistra in frågesträngarna nedan i sökfältet och klicka på **Sök**.

   ![Frågesträng och sökknapp](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Exempelfrågor

Du kan ange termer och fraser på ett liknande sätt som du gör i en Bing- eller Google-sökning eller fullständigt angivna frågeuttryck. Resultat returneras som utförliga JSON-dokument.

### <a name="simple-query-with-top-n-results"></a>Exempelfråga med de N främsta resultaten

#### <a name="example-string-query-searchspa"></a>Exempel (strängfråga): `search=spa`

* **Sökparametern** används för att mata in en nyckelordssökning för fulltextsökning, i det här fallet, returnera hotelldata för dem som innehåller *spa* i alla sökbara fält i dokumentet.

* **Sökutforskaren** returnerar resultat i JSON, vilket kan vara detaljerat och svårläst om dokumenten har en kompakt struktur. Detta är avsiktligt eftersom det är viktigt för utvecklingssyften att kunna se hela dokumentet, särskilt under testning. För en bättre användarupplevelse måste du skriva kod som [hanterar sökresultaten](search-pagination-page-layout.md) så att viktiga element framhävs.

* Dokument består av alla fält som är markerade som ”hämtningsbara” i indexet. Om du vill visa indexattribut i portalen klickar du på *exempel på hotell* i listan **Index.**

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Exempel (parameteriserad fråga): `search=spa&$count=true&$top=10`

* Symbolen **&** används för att lägga till sökparametrar, som kan anges i valfri ordning.

* Parametern **$count=true** returnerar det totala antalet dokument som returneras. Det här värdet visas längst upp i sökresultaten. Du kan verifiera filterfrågor genom att övervaka ändringar som rapporterats via **$count=true**. Mindre antal indikerar att filtret fungerar.

* Den **$top = 10** returnerar högst rankade 10 dokument av den totala. Som standard returnerar Azure Cognitive Search de första 50 bästa matchningarna. Du kan öka eller minska antalet via **$top**.

### <a name="filter-the-query"></a><a name="filter-query"></a> Filtrera frågan

Filter tas med i sökbegäranden när du lägger till parametern **$filter**. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Exempel (filtrerat): `search=beach&$filter=Rating gt 4`

* Parametern **$filter** returnerar resultat som matchar de kriterier som du har angett. I det här fallet betyg större än 4.

* Syntaxen för filtret är en OData-konstruktion. Mer information finns i [OData-filtersyntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-the-query"></a><a name="facet-query"></a> Fasettera frågan

Fasettfilter tas med i sökbegäranden. Du kan använda parametern facet för att returnera ett aggregerat antal dokument som matchar ett fasettvärde som du anger.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Exempel (fasetterat med områdesreducering): `search=*&facet=Category&$top=2`

* **search=*** är en tom sökning. Tomma sökningar söker efter allt. En anledning till att skicka en tom fråga är att filtrera eller fasettera över hela uppsättningen dokument. Du vill till exempel att en faceting-navigeringsstruktur ska bestå av alla hotell i indexet.
* **facet** returnerar en navigeringsstruktur som du kan skicka till en kontroll i användargränssnittet. Den returnerar kategorier och antal. I det här fallet baseras kategorier på ett fält som med ett praktiskt namn *Kallas Kategori*. Det finns ingen aggregering i Azure Cognitive Search, `facet`men du kan approximera aggregering via , vilket ger ett antal dokument i varje kategori.

* **$top=2** hämtar tillbaka två dokument, som visar att du kan använda `top` för att både minska eller öka resultat.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Exempel (fasetterat för numeriska värden): `search=spa&facet=Rating`

* Den här frågan är aspekt för klassificering, på en textsökning för *spa*. Termen *Klassificering* kan anges som en aspekt eftersom fältet är markerat som hämtningsbart, filterbart och facetable i indexet, och de värden som det innehåller (numeriska, 1 till 5), är lämpliga för att kategorisera listor i grupper.

* Endast filtrerbara fält kan fasetteras. Endast hämtningsbara fält kan returneras i resultatet.

* Fältet *Klassificering* är dubbelprecisionsarbetspunkt och gruppningen kommer att vara med exakt värde. Mer information om gruppering efter intervall (till exempel "3 stjärnor", "4 stjärnor" etc.) finns i Så här implementerar du [fasterad navigering i Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-faceted-navigation#filter-based-on-a-range).


### <a name="highlight-search-results"></a><a name="highlight-query"></a> Markera sökresultat

Träffmarkering innebär att formatera all text som matchar sökordet på ett särskilt sätt inom ett givet fält. Om sökordet begravt långt ned i en beskrivning kan du använda träffmarkering för att göra det lättare att hitta ordet.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Exempel (markering): `search=beach&highlight=Description`

* I det här exemplet är det formaterade ordet *strand* lättare att upptäcka i beskrivningsfältet.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Exempel (språklig analys): `search=beaches&highlight=Description`

* Fulltextsökning känner igen grundläggande variationer i ordformulär. I det här fallet innehåller sökresultaten markerad text för "strand", för hotell som har det ordet i sina sökbara fält, som svar på en sökordssökning på "stränder". Tack vare språkanalysen kan olika former av samma ord visas i resultaten. 

* Azure Cognitive Search stöder 56 analysatorer från både Lucene och Microsoft. Standardinställningen som används av Azure Cognitive Search är standardanalysatorn för Lucene.

### <a name="try-fuzzy-search"></a><a name="fuzzy-search"></a> Prova fuzzy-sökning

Som standard misslyckas felstavade frågetermer, som *sittplats* för "Seattle", att returnera matchningar i typisk sökning. Följande exempel returnerar inga resultat.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Exempel (felstavat ord, ohanterat): `search=seatle`

Du kan använda fuzzy-sökning för att hantera felstavningar. Fuzzy-sökning aktiveras när du använder den fullständiga Lucene-frågesyntaxen, som aktiveras när du gör två saker: när du anger **queryType = full** i frågan och när du lägger till **~** i söksträngen.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Exempel (felstavat ord, hanterat): `search=seatle~&queryType=full`

I det här exemplet returneras dokument som innehåller matchningar i "Seattle".

När **queryType** inte är angivet används den enklare standardfrågeparsern. Den enklare frågeparsern är snabbare, men om du behöver tillgång till fuzzy-sökning, reguljära uttryck, närhetssökning eller andra typer av avancerade frågetyper behöver du den fullständiga syntaxen.

Fuzzy-sökning och sökning med jokertecken påverkar sökresultatet. Språkliga analyser utförs inte med dessa frågeformat. Innan du använder fuzzy och wildcard-sökning, granska [Hur fulltextsökning fungerar i Azure Cognitive Search](search-lucene-query-architecture.md#stage-2-lexical-analysis) och leta efter avsnittet om undantag från lexikal analys.

Mer information om frågescenarier som aktiveras av den fullständiga frågetolken finns [i Lucene-frågesyntaxen i Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="try-geospatial-search"></a><a name="geo-search"></a> Prova geospatial sökning

 Geospatial sökning stöds av [datatypen edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) i fält som innehåller koordinater. Geosearch är en filtertyp som finns med i [OData-filtersyntaxen](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Exempel (geo-koordinatfilter): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

Med den här exempelfrågan filtreras alla resultat efter platsdata, där resultaten måste ligga mindre än 5 km från en given plats (koordinaterna anges med latitud och longitud). Om du lägger till **$count** kan du se hur många resultat som returneras när du ändrar antingen avståndet eller koordinaterna.

Geospatial sökning kan vara användbart om sökprogrammet har en funktion av typen ”hitta en bensinstation i närheten av där jag befinner mig” eller om programmet har en funktion för kartnavigering. Det är dock inte fråga om någon fulltextsökning. Om du har användarkrav för att söka på en stad eller ett land/en region efter namn lägger du till fält som innehåller namn på ort eller land/region, förutom koordinater.

## <a name="takeaways"></a>Lärdomar

Den här självstudien gav en snabb introduktion till Azure Cognitive Search med Azure-portalen.

Du lärde dig hur du skapar ett sökindex med hjälp av guiden **Importera data**. Du lärde dig om [indexerare](search-indexer-overview.md), som är den drivande kraften bakom guiden, samt om det grundläggande arbetsflödet vid indexgenerering, inklusive vilka [ändringar du kan göra i ett publicerat index](https://docs.microsoft.com/rest/api/searchservice/update-index).

Med hjälp av **Sökutforskaren** i Azure-portalen lärde du dig grundläggande frågesyntax genom praktiska exempel som demonstrerade viktiga funktioner som filter, markering av träffar, fuzzy-sökning och geo-sökning.

Du har också läst hur du hittar index, indexerare och datakällor i portalen. När du får nya datakällor i framtiden kan du använda portalen för att snabbt kontrollera deras definitioner eller fältsamlingar utan större ansträngningar.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kan medföra kostnader. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken **Alla resurser** eller **Resursgrupper** i fönstret för vänsternavigering.

Om du använder en kostnadsfri tjänst bör du komma ihåg att du är begränsad till tre index, indexerare och datakällor. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

Använd en portalguide för att skapa en färdig webbapp som körs i en webbläsare. Du kan prova den här guiden på det lilla index som du just skapade, eller använda en av de inbyggda exempeldatauppsättningarna för en rikare sökupplevelse.

> [!div class="nextstepaction"]
> [Skapa en sökapp i portalen](search-create-app-portal.md)