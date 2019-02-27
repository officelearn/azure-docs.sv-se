---
title: Snabbsjälvstudie för indexering och fråga med Azure-portalen – Azure Search
description: Lär dig hur du skapar och kör frågor mot ditt första index i Azure Search med hjälp av inbyggda exempeldata och guiden Importera Data i Azure-portalen.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 260dfc829d0869b1fc4f58ae399351c5f68dd926
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270342"
---
# <a name="quickstart-use-built-in-portal-tools-for-azure-search-import-indexing-and-queries"></a>Snabbstart: Använda inbyggda portalverktyg för Azure Search-import, -indexering och -frågor

Om du vill få en snabb start med Azure Search-koncept så kan du testa de inbyggda verktygen i Azure-portalen. Guider och redigeringsverktyg erbjuder inte fullständig paritet med .NET och REST API:er, men du kan snabbt komma igång med en kodfri introduktion och skriva intressanta frågor mot exempeldata på bara några minuter.

> [!div class="checklist"]
> * Börja med en kostnadsfri offentlig uppsättning exempeldata i Azure
> * Kör guiden **Importera data** i Azure Search för att läsa in data och skapa ett index
> * Övervaka förloppet för indexeringen i portalen
> * Visa ett befintligt index och alternativ för att ändra det
> * Utforska fulltextsökning, filter, fasetter, fuzzy-sökning och geosearch med **Sökutforskaren**

Om verktygen är för begränsade kan du prova en [kodbaserad introduktion till Azure Search-programmering i .NET](search-howto-dotnet-sdk.md) eller använda [Postman eller Fiddler för att uföra REST API-anrop](search-fiddler.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. Du kan också titta på en sex minuter lång demonstration av stegen i den här självstudiekursen. Demonstrationen finns cirka tre minuter in i den här [översiktsvideon över Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).

## <a name="prerequisites"></a>Nödvändiga komponenter

[Skapa en Azure Search-tjänst](search-create-service-portal.md) eller hitta en befintlig tjänst i din aktuella prenumeration.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Öppna instrumentpanelen för Azure Search-tjänsten. Om du inte har fäst tjänstepanelen på instrumentpanelen kan du hitta din tjänst på det här sättet:

   * Klicka på **Alla tjänster** i det vänstra navigeringsfönstret.
   * Skriv *sök* i sökrutan för att hämta en lista över sökrelaterade tjänster till din prenumeration. Klicka på **Söktjänster**. Din tjänst ska finnas med i listan.

### <a name="check-for-space"></a>Kontrollera utrymmet

Många kunder börjar med den kostnadsfria tjänsten. Den här versionen är begränsad till tre index, tre datakällor och tre indexerare. Kontrollera att du har plats för extra objekt innan du börjar. Med den här guiden kan du skapa ett objekt av varje sort.

Avsnitten i tjänstinstrumentpanelen visar hur många index, indexerare och datakällor som du redan har. 

![En lista över index, indexerare och datakällor](media/search-get-started-portal/tiles-indexers-datasources2.png)

## <a name="create-index"></a> Skapa ett index och läsa in data

Sökfrågor itererar över ett [*index*](search-what-is-an-index.md) som innehåller sökbara data, metadata och ytterligare konstruktioner som optimerar vissa sökbeteenden.

För den här självstudien använder vi en inbyggd exampeldatamängd som kan crawlas med en [*indexerare*](search-indexer-overview.md) via guiden **Importera data**. En indexerare är en källspecifik crawler som kan läsa metadata och innehåll från Azure-datakällor som stöds. Normalt sett används indexerare programmässigt, men i portalen så kan du komma åt dem via guiden **Importera data**. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Steg 1 – starta guiden Importera data och skapa en datakälla

1. Från instrumentpanelen för Azure Search-tjänsten klickar du på **Importera data** i kommandofältet för att skapa och fylla i sökindexet.

   ![Kommandot Importera data](media/search-get-started-portal/import-data-cmd2.png)

2. I guiden klickar du på **Anslut till dina data** > **Exempel** > **realestate-us-sample**. Den här datakällan är inbyggd. Om du skapar din egen datakälla så behöver du ange ett namn, typ och anslutningsinformation. När du har skapat den blir den en ”befintlig datakälla” som kan återanvändas i andra importåtgärder.

   ![Välj exempeldatauppsättning](media/search-get-started-portal/import-datasource-sample2.png)

3. Fortsätt till nästa sida.

   ![Knappen Nästa sida för kognitiv sökning](media/search-get-started-portal/next-button-add-cog-search.png)

### <a name="step-2---skip-cognitive-skills"></a>Steg 2 – Hoppa över Kognitiva kunskaper

Guiden stöder skapandet av en [pipeline för kognitiva kunskaper](cognitive-search-concept-intro.md) för att införliva Cognitive Services AI-algoritmer i indexering. 

Vi hoppar över det här steget för tillfället och går direkt till **Anpassa målindex**.

   ![Hoppa över steget Kognitiva kunskaper](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> Du kan prova den nya förhandsgranskningsfunktionen för kognitiv sökning i Azure Search från [snabbstarten](cognitive-search-quickstart-blob.md) eller [självstudien för kognitiv sökning](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Steg 3 – Konfigurera index

Skapandet av index är vanligtvis en kodbaserad övning som slutförs innan du läser in data. Som den här guiden visar kan dock guiden skapa ett grundläggande index för valfri datakälla som den kan crawla. Ett index kräver minst ett namn och en samling fält. Ett av fälten ska vara markerat som dokumentnyckeln som fungerar som en unik identifierare för dokumentet. Du kan också välja att använda språkanalys eller språkförslag för automatisk komplettering eller frågeförslag.

Fälten har datatyper och attribut. Kryssrutorna högst upp är *indexattribut* som styr hur fältet används.

* **Hämtningsbar** innebär att det visas i listor med sökresultat. Du kan markera enskilda fält som ska utelämnas från sökresultat genom att avmarkera den här kryssrutan, till exempel om fält endast används i filteruttryck.
* **Nyckel** är den unika dokumentdentifieraren. Den är alltid en sträng och den är obligatorisk.
* **Filtrerbar**, **Sorterbar** och **Fasetterbar** avgör om fält användas i ett filter, en sortering eller en fasetterad navigeringsstruktur.
* **Sökbar** innebär att ett fält ingår i fulltextsökning. Strängarna är sökbara. Numeriska fält och fält för booleska värden är ofta markerade som icke sökbara.

Lagringskraven varierar inte till följd av ditt val. Om du till exempel anger attributet **Hämtningsbar** på flera fält så ökar inte lagringskraven.

Som standard söker guiden igenom datakällan för att hitta unika identifierare som utgör själva grunden för sökordsfältet. *Strängar* får attributen **Hämtningsbar** och **Sökbar**. *Heltal* får attributen **Hämtningsbar**, **Filtrerbar**, **Sorterbar** och **Fasetterbar**.

1. Acceptera alla standardinställningar. 

   Om du kör guiden en andra gång med hjälp av en befintlig realestate-datakälla konfigureras inte indexet med standardattribut. Du måste då manuellt välja attribut för framtida importer.

   ![Genererade realestate-index](media/search-get-started-portal/realestateindex2.png)

2. Fortsätt till nästa sida.

   ![Nästa sida skapa indexeraren](media/search-get-started-portal/next-button-create-indexer.png)

### <a name="step-4---configure-indexer"></a>Steg 4 – Konfigurera indexeraren

Klicka på **Indexnamn** > **Namn** i guiden **Importera data** och skriv in ett namn på indexeraren.

Det här objektet definierar en körbar process. Du kan lägga till det i ett återkommande schema, men för tillfället använder du standardalternativet för att köra indexeraren en gång direkt.

Klicka på **Skicka** för att skapa och köra indexeraren samtidigt.

  ![realestate indexer](media/search-get-started-portal/realestate-indexer2.png)

## <a name="monitor-progress"></a>Övervaka förloppet

Guiden ska ta dig till listan med indexerare där du kan övervaka förloppet. För självnavigering går du till översiktssidan och klickar på **Indexerare**.

Det kan ta några minuter för portalen att uppdatera sidan men du borde se den nyligen skapade indexeraren i listan med status Pågående eller Lyckades, tillsammans med antalet dokument som indexerats.

   ![Meddelande om pågående indexeringsaktiviteter](media/search-get-started-portal/indexers-inprogress2.png)

## <a name="view-the-index"></a>Visa indexet

Den överordnade tjänstsidan innehåller länkar till de resurser som skapats i Azure Search-tjänsten.  Om du vill visa det index som du precis skapat klickar du på **Index** i listan över länkar. 

   ![Indexlista på instrumentpanelen för tjänster](media/search-get-started-portal/indexes-list.png)

I den här listan kan du klicka på indexet *realestate-us-sample* som du precis skapat och visa indexschemat. Du kan också lägga till nya fält. 

Fliken **Fält** visar indexschemat. Rulla till slutet av listan för att ange ett nytt fält. Oftast kan du inte ändra befintliga fält. Eftersom de befintliga fälten har en fysisk representation i Azure Search kan de inte ändras, inte ens i kod. Om du vill ändra ett befintligt fält från grunden så skapar du ett nytt index och tar bort det ursprungliga.

   ![exempel på indexdefinition](media/search-get-started-portal/sample-index-def.png)

Andra konstruktioner, t.ex. bedömningsprofiler och CORS-alternativ, kan läggas till när som helst.

Ägna några minuter åt att gå igenom definitionsalternativen för index så att du förstår vad du kan och inte kan redigera när du utformar ett index. Nedtonade alternativ indikerar att ett värde inte kan ändras eller tas bort. 

## <a name="query-index"></a> Fråga med Sökutforskaren

Nu bör du ha ett sökindex som du kan börja köra frågor mot med hjälp av den inbyggda frågesidan [**Sökutforskaren**](search-explorer.md). Den innehåller en sökruta så att du kan testa godtyckliga frågesträngar.

**Sökutforskaren** kan endast hantera [REST-API-förfrågningar](https://docs.microsoft.com/rest/api/searchservice/search-documents), men stöder syntax för både [enkel frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) och [fullständig Lucene-frågeparser](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), samt alla tillgängliga sökparametrar i åtgärder med [REST-API:et för dokumentsökning](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples).

> [!TIP]
> Följande steg demonstreras 6 minuter och 8 sekunder in i [översiktsvideon över Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Klicka på **Sökutforskaren** i kommandofältet.

   ![Kommandot Sökutforskaren](media/search-get-started-portal/search-explorer-cmd2.png)

2. Klicka på **Ändra index** i kommandofältet för att byta till *realestate-us-sample*. Klicka på **Ange API-version** i kommandofältet om du vill se vilka REST-API:er som finns tillgängliga. Använd den allmänt tillgängliga versionen (2017-11-11) för frågorna nedan.

   ![Index- och API-kommandon](media/search-get-started-portal/search-explorer-changeindex-se2.png)

3. Klistra in frågesträngarna nedan i sökfältet och klicka på **Sök**.

   ![Frågesträng och sökknapp](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Exempelfrågor

Du kan ange termer och fraser på ett liknande sätt som du gör i en Bing- eller Google-sökning eller fullständigt angivna frågeuttryck. Resultat returneras som utförliga JSON-dokument.

### <a name="simple-query-with-top-n-results"></a>Exempelfråga med de N främsta resultaten

#### <a name="example-string-query-searchseattle"></a>Exempel (strängfråga): `search=seattle`

* Parametern **search** används för att ange en nyckelordssökning för fulltextsökning, som i det här fallet returnerar poster från King County i delstaten Washington som innehåller *Seattle* i sökbara fält i dokumentet.

* **Sökutforskaren** returnerar resultat i JSON, vilket kan vara detaljerat och svårläst om dokumenten har en kompakt struktur. Detta är avsiktligt eftersom det är viktigt för utvecklingssyften att kunna se hela dokumentet, särskilt under testning. För en bättre användarupplevelse måste du skriva kod som [hanterar sökresultaten](search-pagination-page-layout.md) så att viktiga element framhävs.

* Dokument består av alla fält som är markerade som ”hämtningsbara” i indexet. Visa indexattribut i portalen genom att klicka på *realestate-us-sample* på **Index**-listan.

#### <a name="example-parameterized-query-searchseattlecounttruetop100"></a>Exempel (parameteriserad fråga): `search=seattle&$count=true&$top=100`

* Symbolen **&** används för att lägga till sökparametrar, som kan anges i valfri ordning.

* Parametern **$count=true** returnerar det totala antalet returnerade dokument. Det här värdet visas längst upp i sökresultaten. Du kan verifiera filterfrågor genom att övervaka ändringar som rapporterats via **$count=true**. Mindre antal indikerar att filtret fungerar.

* **$top=100** returnerar de högst rangordnade 100 dokumenten bland alla dokument. Som standard returnerar Azure Search de första 50 bästa matchningarna. Du kan öka eller minska antalet via **$top**.

### <a name="filter-query"></a> Filtrera frågan

Filter tas med i sökbegäranden när du lägger till parametern **$filter**. 

#### <a name="example-filtered-searchseattlefilterbeds-gt-3"></a>Exempel (filtrerat): `search=seattle&$filter=beds gt 3`

* Parametern **$filter** returnerar resultat som matchar de kriterier som du har angett. I det här fallet sovrum som är större än 3.

* Syntaxen för filtret är en OData-konstruktion. Mer information finns i [OData-filtersyntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-query"></a> Fasettera frågan

Fasettfilter tas med i sökbegäranden. Du kan använda parametern facet för att returnera ett aggregerat antal dokument som matchar ett fasettvärde som du anger.

#### <a name="example-faceted-with-scope-reduction-searchfacetcitytop2"></a>Exempel (fasetterat med områdesreducering): `search=*&facet=city&$top=2`

* **search=*** är en tom sökning. Tomma sökningar söker efter allt. En anledning till att skicka en tom fråga är att filtrera eller fasettera över hela uppsättningen dokument. Om du exempelvis vill att en fasetterande navigeringsstruktur ska bestå av alla städer i indexet.

* **facet** returnerar en navigeringsstruktur som du kan skicka till en kontroll i användargränssnittet. Den returnerar kategorier och antal. I det här fallet baseras kategorier på antalet städer. Det finns ingen aggregering i Azure Search, men du kan uppskatta aggregering via `facet`, som ger en uppräkning av dokument i varje kategori.

* **$top=2** hämtar tillbaka två dokument, som visar att du kan använda `top` för att både minska eller öka resultat.

#### <a name="example-facet-on-numeric-values-searchseattlefacetbeds"></a>Exempel (fasetterat för numeriska värden): `search=seattle&facet=beds`**

* De här frågan är en aspekt för sängar, i en textsökning för *Seattle*. *beds* kan klassas som ett fasettvärde eftersom fältet är märkt som ett hämtningsbart, filtrerbart och fasettbart fält i indexet. Värdena (numeriska, 1–5) är väl lämpade för att kategorisera och dela upp listor i grupper (listor med 3 sovrum, 4 sovrum etc.).

* Endast filtrerbara fält kan fasetteras. Endast hämtningsbara fält kan returneras i resultatet.

### <a name="highlight-query"></a> Markera sökresultat

Träffmarkering innebär att formatera all text som matchar sökordet på ett särskilt sätt inom ett givet fält. Om sökordet begravt långt ned i en beskrivning kan du använda träffmarkering för att göra det lättare att hitta ordet.

#### <a name="example-highlighter-searchgranite-countertopshighlightdescription"></a>Exempel (markering): `search=granite countertops&highlight=description`

* I det här exemplet är den formaterade frasen *granite countertops* enklare att hitta i beskrivningsfältet.

#### <a name="example-linguistic-analysis-searchmicehighlightdescription"></a>Exempel (språklig analys): `search=mice&highlight=description`

* Fulltextsökning hittar ordformer med liknande semantisk struktur. I det här fallet har någon sökt på ”möss”. Sökresultaten innehåller då även texter med ordet ”mus” och referenser till skadedjursbekämpning. Tack vare språkanalysen kan olika former av samma ord visas i resultaten.

* Azure Search har stöd för 56 analysverktyg från både Lucene och Microsoft. Som standard används analysverktyget från Lucene av Azure Search.

### <a name="fuzzy-search"></a> Prova fuzzy-sökning

Som standard får du inga träffar på felstavade ord i en typisk sökning, om du till exempel skriver *samamish* när du söker på ”Sammamish Plateau” utanför Seattle. Följande exempel returnerar inga resultat.

#### <a name="example-misspelled-term-unhandled-searchsamamish"></a>Exempel (felstavat ord, ohanterat): `search=samamish`

Du kan använda fuzzy-sökning för att hantera felstavningar. Fuzzy-sökning aktiveras när du använder den fullständiga Lucene-frågesyntaxen, som aktiveras när du gör två saker: när du anger **queryType = full** i frågan och när du lägger till **~** i söksträngen.

#### <a name="example-misspelled-term-handled-searchsamamishquerytypefull"></a>Exempel (felstavat ord, hanterat): `search=samamish~&queryType=full`

Nu returnerar det här exemplet dokument som innehåller matchningar på ”Sammamish”.

När **queryType** inte är angivet används den enklare standardfrågeparsern. Den enklare frågeparsern är snabbare, men om du behöver tillgång till fuzzy-sökning, reguljära uttryck, närhetssökning eller andra typer av avancerade frågetyper behöver du den fullständiga syntaxen.

Fuzzy-sökning och sökning med jokertecken påverkar sökresultatet. Språkliga analyser utförs inte med dessa frågeformat. Innan du använder fuzzy-sökning och sökning med jokertecken rekommenderar vi att du läser [Så här fungerar fullständig textsökning i Azure Search](search-lucene-query-architecture.md#stage-2-lexical-analysis) (How full text search works in Azure Search), och särskilt avsnittet om undantag för språklig analys.

Mer information om frågescenarier i den fullständiga frågeparsern finns i [Lucene-frågesyntax i Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="geo-search"></a> Prova geospatial sökning

Geospatial sökning stöds av [datatypen edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) i fält som innehåller koordinater. Geosearch är en filtertyp som finns med i [OData-filtersyntaxen](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-122121513-47673988-le-5"></a>Exempel (geo-koordinatfilter): `search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`

Med den här exempelfrågan filtreras alla resultat efter platsdata, där resultaten måste ligga mindre än 5 km från en given plats (koordinaterna anges med latitud och longitud). Om du lägger till **$count** kan du se hur många resultat som returneras när du ändrar antingen avståndet eller koordinaterna.

Geospatial sökning kan vara användbart om sökprogrammet har en funktion av typen ”hitta en bensinstation i närheten av där jag befinner mig” eller om programmet har en funktion för kartnavigering. Det är dock inte fråga om någon fulltextsökning. Om användarna ställer krav på att kunna söka efter en ort eller ett land lägger du till fält för ort eller land som ett komplement till koordinaterna.

## <a name="takeaways"></a>Lärdomar

Den här självstudien ger en snabb introduktion till Azure Search med Azure-portalen.

Du lärde dig hur du skapar ett sökindex med hjälp av guiden **Importera data**. Du lärde dig om [indexerare](search-indexer-overview.md), som är den drivande kraften bakom guiden, samt om det grundläggande arbetsflödet vid indexgenerering, inklusive vilka [ändringar du kan göra i ett publicerat index](https://docs.microsoft.com/rest/api/searchservice/update-index).

Med hjälp av **Sökutforskaren** i Azure-portalen lärde du dig grundläggande frågesyntax genom praktiska exempel som demonstrerade viktiga funktioner som filter, markering av träffar, fuzzy-sökning och geo-sökning.

Du har också läst hur du hittar index, indexerare och datakällor i portalen. När du får nya datakällor i framtiden kan du använda portalen för att snabbt kontrollera deras definitioner eller fältsamlingar utan större ansträngningar.

## <a name="clean-up"></a>Rensa

Om den här självstudien var den första gången du använde Azure Search-tjänsten så kan du ta bort resursgruppen med Azure Search-tjänsten. Om inte så letar du fram den rätta resursgruppens namn i listan över tjänster och tar bort den lämpligaste.

## <a name="next-steps"></a>Nästa steg

Du kan utforska mer av Azure Search med hjälp av programmässiga verktyg:

* [Skapa ett index med .NET SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)
* [Skapa ett index med REST API:er](https://docs.microsoft.com/azure/search/search-create-index-rest-api)
* [Skapa ett index med Postman eller Fiddler och Azure Search REST API:er](search-fiddler.md)