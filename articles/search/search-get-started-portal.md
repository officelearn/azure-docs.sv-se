---
title: Självstudiekurs om indexering, frågor och filtrering i Azure Search med hjälp av portalen | Microsoft Docs
description: I den här självstudien använder du Azure Portal och fördefinierade exempeldata för att generera ett index i Azure Search. Utforska fulltextsökning, filter, fasetter, fuzzy-sökning, geosearch och mycket annat.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: tutorial
ms.date: 07/10/2018
ms.author: heidist
ms.openlocfilehash: 124963359d0b2d4050156958de195e47b9331c92
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007992"
---
# <a name="tutorial-use-built-in-tools-for-azure-search-indexing-and-queries"></a>Självstudier: Använda inbyggda verktyg för Azure Search-indexering och Azure Search-frågor

På en Azure Search-tjänstsida på Azure Portal kan du använda inbyggda verktyg för koncepttestning och för att få praktisk erfarenhet med minimal inkörningstid. Portalverktygen är inte helt likvärdiga med .NET- och REST-API:erna, men guiderna och redigerarna är praktiska för snabb koncepttestning. Den här introduktionen utan kod hjälper dig igång med en liten publicerad datauppsättning så att du genast kan skriva användbara frågor. 

> [!div class="checklist"]
> * Börja med offentliga exempeldata och generera ett Azure Search-index automatiskt med hjälp av guiden **Importera data**. 
> * Visa indexschema och attribut för valfritt index som har publicerats i Azure Search.
> * Utforska fulltextsökning, filter, fasetter, fuzzy-sökning och geosearch med **Sökutforskaren**.  

Portalverktygen har inte stöd för alla funktioner i Azure Search. Om verktygen är för begränsade rekommenderar vi en [kodbaserad introduktion till Azure Search-programmering i .NET](search-howto-dotnet-sdk.md) eller [webbtestningsverktyg för att göra REST API-anrop](search-fiddler.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. Du kan också titta på en sex minuter lång demonstration av stegen i den här självstudiekursen. Demonstrationen finns cirka tre minuter in i den här [översiktsvideon över Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).

## <a name="prerequisites"></a>Nödvändiga komponenter

[Skapa en Azure Search-tjänst](search-create-service-portal.md) eller hitta en befintlig tjänst i din aktuella prenumeration. 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Öppna instrumentpanelen för Azure Search-tjänsten. Om du inte har fäst tjänstepanelen på instrumentpanelen kan du hitta din tjänst på det här sättet: 
   
   * Klicka på **Alla tjänster** i det vänstra navigeringsfönstret.
   * Skriv *sök* i sökrutan för att hämta en lista över sökrelaterade tjänster till din prenumeration. Klicka på **Söktjänster**. Din tjänst ska finnas med i listan. 

### <a name="check-for-space"></a>Kontrollera utrymmet
Många kunder börjar med den kostnadsfria tjänsten. Den här versionen är begränsad till tre index, tre datakällor och tre indexerare. Kontrollera att du har plats för extra objekt innan du börjar. Med den här guiden kan du skapa ett objekt av varje sort. 

> [!TIP] 
> Panelerna på instrumentpanelen med tjänster visar hur många index, indexerare och datakällor du redan har. Panelen Indexerare visar lyckade och misslyckade operationer. Klicka på panelen för att visa antalet indexerare. 
>
> ![Paneler för indexerare och datakällor][1]
>

## <a name="create-index"></a> Skapa ett index och läsa in data
Sökfrågor itererar över ett [*index*](search-what-is-an-index.md) med sökbara data, metadata och konstruktioner som används för att optimera vissa sökbeteenden.

För att den här uppgiften ska vara helt portalbaserad använder vi en inbyggd exampeldatauppsättning som kan crawlas med en [*indexerare*](search-indexer-overview.md) via guiden **Importera data**. En indexerare är en källspecifik crawler som kan läsa metadata och innehåll från Azure-datakällor som stöds. I koden kan du skapa och hantera indexerare som oberoende resurser. På portalen exponeras indexerare via guiden **Importera data**. 

#### <a name="step-1-start-the-import-data-wizard"></a>Steg 1: Starta guiden Importera data
1. Klicka på **Importera data** i kommandofältet på instrumentpanelen för Azure Search-tjänsten för att starta en guide som hjälper dig att skapa och fylla ett index.
   
    ![Kommandot Importera data][2]

2. I guiden klickar du på **Anslut till dina data** > **Exempel** > **realestate-us-sample**. Den här datakällan är förkonfigurerad med namn, typ och anslutningsinformation. När du har skapat den blir den en ”befintlig datakälla” som kan återanvändas i andra importåtgärder.

    ![Välj exempeldatauppsättning][9]

3. Klicka på **OK** att använda den.

#### <a name="skip-cognitive-skills"></a>Hoppa över steget Kognitiva kunskaper

**Importera data** innehåller ett valfritt steg för kognitiva kunskaper som lägger till AI-algoritmer till indexeringen. Eftersom den här funktionen inte ingår i den här självstudiekursen hoppar du över steget och går vidare till **Anpassa målindex**. Om du är nyfiken på den nya förhandsgranskningsfunktionen för kognitiv sökning i Azure Search rekommenderar vi att du går till [snabbstarten](cognitive-search-quickstart-blob.md) eller [självstudien för kognitiv sökning](cognitive-search-tutorial-blob.md).

   ![Hoppa över steget Kognitiva kunskaper][11]

#### <a name="step-2-define-the-index"></a>Steg 2: Definiera indexet
Att skapa ett index är vanligtvis en manuell och kodbaserad process, men guiden kan skapa ett index för alla datakällor som den kan crawla. Ett index kräver minst ett namn och en samling fält, där ett fält är markerat som dokumentnyckeln som fungerar som en unik identifierare för dokumentet.

Fälten har datatyper och attribut. Kryssrutorna högst upp är *indexattribut* som styr hur fältet används. 

* **Hämtningsbar** innebär att det visas i listor med sökresultat. Du kan markera enskilda fält som ska utelämnas från sökresultat genom att avmarkera den här kryssrutan, till exempel om fält endast används i filteruttryck. 
* **Filtrerbar**, **Sorterbar** och **Fasettbar** avgör om ett fält kan användas i ett filter, en sortering eller en struktur för aspektbaserad navigering. 
* **Sökbar** innebär att ett fält ingår i fulltextsökning. Strängarna är sökbara. Numeriska fält och fält för booleska värden är ofta markerade som icke sökbara. 

Som standard söker guiden igenom datakällan för att hitta unika identifierare som utgör själva grunden för sökordsfältet. Strängarna får attributen hämtningsbara och sökbara. Heltal får attributen hämtningsbara, filtrerbara, sorterbara och fasettbara.

  ![Genererade realestate-index][3]

Klicka på **OK** för att skapa indexet.

#### <a name="step-3-define-the-indexer"></a>Steg 3: Definiera indexeraren
Klicka på **Indexnamn** > **Namn** i guiden **Importera data** och skriv in ett namn på indexeraren. 

Det här objektet definierar en körbar process. Du kan lägga till det i ett återkommande schema, men för stunden ska du använda standardalternativet och köra indexeraren en gång direkt när du klickar på **OK**.  

  ![realestate indexer][8]

## <a name="check-progress"></a>Kontrollera förloppet
Övervaka dataimporten genom att gå tillbaka till instrumentpanelen för tjänsten, rulla nedåt och dubbelklicka på panelen **Indexerare** för att öppna listan med indexerare. Du bör då se den nyskapade indexeraren i listan med statusen ”pågående” eller ”lyckades”, tillsammans med antalet dokument som indexerats.

   ![Meddelande om pågående indexeringsaktiviteter][4]

## <a name="view-the-index"></a>Visa indexet

På panelerna på instrumentpanelen för tjänsten kan du visa både sammanfattningsinformation och detaljerad information. På panelen **Index** bör du till exempel se en lista över befintliga index, inklusive indexet *realestate-us-sample* som du skapade i föregående steg.

Klicka på indexet *realestate-us-sample* för att visa portalalternativen för dess definition. Med alternativet **Lägg till/redigera fält** kan du skapa och lägga till attribut till nya fält. Eftersom de befintliga fälten har en fysisk representation i Azure Search kan de inte ändras, inte ens i kod. Om du vill ändra ett befintligt fält från grunden skapar du ett nytt fält i stället och tar bort det ursprungliga fältet. 

   ![exempel på indexdefinition][10]

Andra konstruktioner, t.ex. bedömningsprofiler och CORS-alternativ, kan läggas till när som helst. 

Ägna några minuter åt att gå igenom definitionsalternativen för index så att du förstår vad du kan och inte kan redigera när du utformar ett index. Nedtonade alternativ indikerar att ett värde inte kan ändras eller tas bort.

## <a name="query-index"></a> Skicka frågor mot indexet
Nu bör du ha ett sökindex som du kan börja köra frågor mot med hjälp av den inbyggda frågesidan [**Sökutforskaren**](search-explorer.md). Den innehåller en sökruta så att du kan testa godtyckliga frågesträngar. 

> [!TIP]
> Följande steg demonstreras 6 minuter och 8 sekunder in i [översiktsvideon över Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Klicka på **Sökutforskaren** i kommandofältet.

   ![Kommandot Sökutforskaren][5]

2. Klicka på **Ändra index** i kommandofältet för att byta till *realestate-us-sample*. Klicka på **Ange API-version** i kommandofältet om du vill se vilka REST-API:er som finns tillgängliga. Använd den allmänt tillgängliga versionen (2017-11-11) för frågorna nedan. 

   ![Index- och API-kommandon][6]

3. Skriv in frågesträngarna nedan i sökfältet och klicka på **Sök**.

    > [!NOTE]
    > **Sökutforskaren** kan endast hantera [REST API-förfrågningar](https://docs.microsoft.com/rest/api/searchservice/search-documents). Den accepterar syntax från både [enkel frågeparser](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) och [fullständig frågeparser (Lucene)](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) samt alla tillgängliga sökparametrar i [dokumentsökningsoperationer](https://docs.microsoft.com/rest/api/searchservice/search-documents).
    > 


#### <a name="example-string-searchseattle"></a>Exempel (sträng): `search=seattle`

+ Parametern **search** används för att ange en nyckelordssökning för fulltextsökning, som i det här fallet returnerar poster från King County i delstaten Washington som innehåller *Seattle* i sökbara fält i dokumentet. 

+ **Sökutforskaren** returnerar resultat i JSON, vilket kan vara detaljerat och svårläst om dokumenten har en kompakt struktur. Detta är avsiktligt eftersom det är viktigt att kunna se hela dokumentet, särskilt under testning. För en bättre användarupplevelse måste du skriva kod som [hanterar sökresultaten](search-pagination-page-layout.md) så att viktiga element framhävs.

+ Dokument består av alla fält som är markerade som ”hämtningsbara” i indexet. Om du vill visa indexattribut i portalen klickar du på *realestate-us-sample* på ikonen **Index**.

#### <a name="example-parameterized-searchseattlecounttruetop100"></a>Exempel (parameteriserat): `search=seattle&$count=true&$top=100`

+ Symbolen **&** används för att lägga till sökparametrar, som kan anges i valfri ordning. 

+  Parametern **$count=true** returnerar ett antal för summan av alla returnerade dokument. Det här värdet visas längst upp i sökresultaten. Du kan verifiera filterfrågor genom att övervaka ändringar som rapporterats via **$count=true**. Mindre antal indikerar att filtret fungerar.

+ **$top=100** returnerar de högst rangordnade 100 dokumenten bland alla dokument. Som standard returnerar Azure Search de första 50 bästa matchningarna. Du kan öka eller minska antalet via **$top**.

## <a name="filter-query"></a> Filtrera frågan

Filter tas med i sökbegäranden när du lägger till parametern **$filter**. 

#### <a name="example-filtered-searchseattlefilterbeds-gt-3"></a>Exempel (filtrerat): `search=seattle&$filter=beds gt 3`

+ Parametern **$filter** returnerar resultat som matchar de kriterier som du har angett. I det här fallet sovrum som är större än 3. 

+ Syntaxen för filtret är en OData-konstruktion. Mer information finns i [OData-filtersyntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

## <a name="facet-query"></a> Fasettera frågan

Fasettfilter tas med i sökbegäranden. Du kan använda parametern facet för att returnera ett aggregerat antal dokument som matchar ett fasettvärde som du anger. 

#### <a name="example-faceted-with-scope-reduction-searchfacetcitytop2"></a>Exempel (fasetterat med områdesreducering): `search=*&facet=city&$top=2`

+ **search=*** är en tom sökning. Tomma sökningar söker efter allt. En anledning till att skicka en tom fråga är att filtrera eller fasettera över hela uppsättningen dokument. Om du exempelvis vill att en fasetterande navigeringsstruktur ska bestå av alla städer i indexet.

+  **facet** returnerar en navigeringsstruktur som du kan skicka till en kontroll i användargränssnittet. Den returnerar kategorier och antal. I det här fallet baseras kategorier på antalet städer. Det finns ingen aggregering i Azure Search, men du kan uppskatta aggregering via `facet`, som ger en uppräkning av dokument i varje kategori.

+ **$top=2** hämtar tillbaka två dokument, som visar att du kan använda `top` för att både minska eller öka resultat.

#### <a name="example-facet-on-numeric-values-searchseattlefacetbeds"></a>Exempel (fasetterat för numeriska värden): `search=seattle&facet=beds`**

+ De här frågan är en aspekt för sängar, i en textsökning för *Seattle*. *beds* kan klassas som ett fasettvärde eftersom fältet är märkt som ett hämtningsbart, filtrerbart och fasettbart fält i indexet. Värdena (numeriska, 1–5) är väl lämpade för att kategorisera och dela upp listor i grupper (listor med 3 sovrum, 4 sovrum etc.). 

+ Endast filtrerbara fält kan fasetteras. Endast hämtningsbara fält kan returneras i resultatet.

## <a name="highlight-query"></a> Lägga till markering

Träffmarkering innebär att formatera all text som matchar sökordet på ett särskilt sätt inom ett givet fält. Om sökordet begravt långt ned i en beskrivning kan du använda träffmarkering för att göra det lättare att hitta ordet. 

#### <a name="example-highlighter-searchgranite-countertopshighlightdescription"></a>Exempel (markering): `search=granite countertops&highlight=description`

+ I det här exemplet är den formaterade frasen *granite countertops* enklare att hitta i beskrivningsfältet.

#### <a name="example-linguistic-analysis-searchmicehighlightdescription"></a>Exempel (språklig analys): `search=mice&highlight=description`

+ Fulltextsökning hittar ordformer med liknande semantisk struktur. I det här fallet har någon sökt på ”möss”. Sökresultaten innehåller då även texter med ordet ”mus” och referenser till skadedjursbekämpning. Tack vare språkanalysen kan olika former av samma ord visas i resultaten. 

+ Azure Search har stöd för 56 analysverktyg från både Lucene och Microsoft. Som standard används analysverktyget från Lucene av Azure Search. 

## <a name="fuzzy-search"></a> Prova fuzzy-sökning

Som standard får du inga träffar på felstavade ord i en typisk sökning, om du till exempel skriver *samamish* när du söker på ”Sammamish Plateau” utanför Seattle. Följande exempel returnerar inga resultat.

#### <a name="example-misspelled-term-unhandled-searchsamamish"></a>Exempel (felstavat ord, ohanterat): `search=samamish`

Du kan använda fuzzy-sökning för att hantera felstavningar. Fuzzy-sökning aktiveras när du använder den fullständiga Lucene-frågesyntaxen, som aktiveras när du gör två saker: när du anger **queryType = full** i frågan och när du lägger till **~** i söksträngen. 

#### <a name="example-misspelled-term-handled-searchsamamishquerytypefull"></a>Exempel (felstavat ord, hanterat): `search=samamish~&queryType=full`

Nu returnerar det här exemplet dokument som innehåller matchningar på ”Sammamish”.

När **queryType** inte är angivet används den enklare standardfrågeparsern. Den enklare frågeparsern är snabbare, men om du behöver tillgång till fuzzy-sökning, reguljära uttryck, närhetssökning eller andra typer av avancerade frågetyper behöver du den fullständiga syntaxen. 

Fuzzy-sökning och sökning med jokertecken påverkar sökresultatet. Språkliga analyser utförs inte med dessa frågeformat. Innan du använder fuzzy-sökning och sökning med jokertecken rekommenderar vi att du läser [Så här fungerar fullständig textsökning i Azure Search](search-lucene-query-architecture.md#stage-2-lexical-analysis) (How full text search works in Azure Search), och särskilt avsnittet om undantag för språklig analys.

Mer information om frågescenarier i den fullständiga frågeparsern finns i [Lucene-frågesyntax i Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

## <a name="geo-search"></a> Prova geospatial sökning

Geospatial sökning stöds av [datatypen edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) i fält som innehåller koordinater. Geosearch är en filtertyp som finns med i [OData-filtersyntaxen](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-122121513-47673988-le-5"></a>Exempel (geo-koordinatfilter): `search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`

Med den här exempelfrågan filtreras alla resultat efter platsdata, där resultaten måste ligga mindre än 5 km från en given plats (koordinaterna anges med latitud och longitud). Om du lägger till **$count** kan du se hur många resultat som returneras när du ändrar antingen avståndet eller koordinaterna. 

Geospatial sökning kan vara användbart om sökprogrammet har en funktion av typen ”hitta en bensinstation i närheten av där jag befinner mig” eller om programmet har en funktion för kartnavigering. Det är dock inte fråga om någon fulltextsökning. Om användarna ställer krav på att kunna söka efter en ort eller ett land lägger du till fält för ort eller land som ett komplement till koordinaterna.

## <a name="takeaways"></a>Lärdomar

Den här självstudien går igenom de grundläggande stegen i guiden **Importera data** och beskriver hur du använder **Sökutforskaren** på Azure Portal.

När vi gick igenom guiden Importera data lärde du dig om [indexerare](search-indexer-overview.md), som är den drivande kraften bakom guiden, samt om det grundläggande arbetsflödet vid indexgenerering, inklusive vilka [ändringar du kan göra i ett publicerat index](ttps://docs.microsoft.com/rest/api/searchservice/update-index). 

Du lärde dig frågesyntax genom praktiska exempel som demonstrerade viktiga funktioner som filter, markering av träffar, fuzzy-sökning och geo-sökning.

Slutligen lärde du dig också hur du hämtar information genom att klicka på paneler på instrumentpanelen för ett index, en indexerare eller en datakälla som du skapar för din prenumeration. Senare, när du arbetar med dina egna index eller index som dina kollegor har skapat, kan du använda portalen för att snabbt kontrollera definitionen för en datakälla, eller konstruktionen för en samling fält, utan att behöva söka igenom okänd kod.

## <a name="clean-up-resources"></a>Rensa resurser

Det snabbaste sättet att rensa upp efter en självstudie är att ta bort resursgruppen som innehåller Azure Search-tjänsten. Du kan ta bort resursgruppen nu så att allt innehåll i den tas bort permanent. På portalen visas resursgruppens namn på översiktssidan för varje Azure Search-tjänst.

## <a name="next-steps"></a>Nästa steg

Överväg att använda ett REST-testningsverktyg, t.ex. Postman eller Fiddler, för ytterligare verktygsbaserad utforskning av Azure Search:

> [!div class="nextstepaction"]
> [Webbtestningsverktyg för att anropa Azure Search REST-API:erna](search-fiddler.md)


<!--Image references-->
[1]: ./media/search-get-started-portal/tiles-indexers-datasources2.png
[2]: ./media/search-get-started-portal/import-data-cmd2.png
[3]: ./media/search-get-started-portal/realestateindex2.png
[4]: ./media/search-get-started-portal/indexers-inprogress2.png
[5]: ./media/search-get-started-portal/search-explorer-cmd2.png
[6]: ./media/search-get-started-portal/search-explorer-changeindex-se2.png
[7]: ./media/search-get-started-portal/search-explorer-query2.png
[8]: ./media/search-get-started-portal/realestate-indexer2.png
[9]: ./media/search-get-started-portal/import-datasource-sample2.png
[10]: ./media/search-get-started-portal/sample-index-def.png
[11]: ./media/search-get-started-portal/skip-cog-skill-step.png