---
title: Lägga till en fasterad navigeringskategorihierarki
titleSuffix: Azure Cognitive Search
description: Lägg till fasterad navigering för självstyrd filtrering i sökprogram som integreras med Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5f4435ca213584fff84f3ddad9bda6f7e06628a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283165"
---
# <a name="how-to-implement-faceted-navigation-in-azure-cognitive-search"></a>Implementera fasterad navigering i Azure Cognitive Search

Fasterad navigering är en filtreringsmekanism som ger självstyrd navigering i detaljgranskning i sökprogram. Termen "fasterad navigering" kan vara obekant, men du har förmodligen använt den tidigare. Som följande exempel visar är fakterad navigering inget annat än de kategorier som används för att filtrera resultat.

 ![Demonstration av Azure Cognitive Search Job Portal](media/search-faceted-navigation/azure-search-faceting-example.png "Demonstration av Azure Cognitive Search Job Portal")

Fastrade navigering är en alternativ startpunkt för sökning. Det erbjuder ett bekvämt alternativ till att skriva komplexa sökuttryck för hand. Aspekter kan hjälpa dig att hitta det du letar efter, samtidigt som du ser till att du inte får noll resultat. Som utvecklare kan du med aspekter visa de mest användbara sökvillkoren för att navigera i sökindexet. I online detaljhandeln applikationer, fasterad navigering är ofta byggd över varumärken, avdelningar (barnens skor), storlek, pris, popularitet och betyg. 

Implementering av fasterad navigering skiljer sig åt mellan sökteknik. I Azure Cognitive Search skapas fasterad navigering vid frågetid med hjälp av fält som du tidigare har tillskrivit i schemat.

-   I de frågor som programmet skapar måste en fråga skicka *fasettfrågeparametrar* för att få de tillgängliga aspektfiltervärdena för den dokumentresultatuppsättningen.

-   Om du vill trimma dokumentresultatuppsättningen måste `$filter` programmet också använda ett uttryck.

I programutvecklingen utgör att skriva kod som konstruerar frågor huvuddelen av arbetet. Många av de programbeteenden som du förväntar dig av aspektbaserad navigering tillhandahålls av tjänsten, inklusive inbyggt stöd för att definiera intervall och få räknas för fasettresultat. Tjänsten innehåller också förnuftiga standardvärden som hjälper dig att undvika otympliga navigeringsstrukturer. 

## <a name="sample-code-and-demo"></a>Exempelkod och demo
I den här artikeln används en jobbsökningsportal som ett exempel. Exemplet implementeras som ett ASP.NET MVC-program.

- Se och testa den fungerande demon online på [Azure Cognitive Search Job Portal Demo](https://aka.ms/azjobsdemo).

- Hämta koden från [reporäntan för Azure-Samples på GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Komma igång
Om du är ny på sökutveckling, är det bästa sättet att tänka på fasterad navigering att det visar möjligheterna till självstyrd sökning. Det är en typ av sökupplevelse för detaljgranskning, baserat på fördefinierade filter, som används för att snabbt begränsa sökresultaten genom peka-och-klicka-åtgärder. 

### <a name="interaction-model"></a>Interaktionsmodell

Sökupplevelsen för fasterad navigering är iterativ, så låt oss börja med att förstå det som en sekvens av frågor som utvecklas som svar på användaråtgärder.

Utgångspunkten är en programsida som ger aspektig navigering, vanligtvis placerad i periferin. Fasterad navigering är ofta en trädstruktur, med kryssrutor för varje värde eller klickbar text. 

1. En fråga som skickas till Azure Cognitive Search anger den fasterade navigeringsstrukturen via en eller flera fastfrågaparametrar. Frågan kan till exempel `facet=Rating`innehålla , `:values` `:sort` kanske med ett eller alternativ för att ytterligare förfina presentationen.
2. Presentationslagret återger en söksida som ger aspektig navigering med hjälp av de aspekter som anges på begäran.
3. Med tanke på en fasterad navigeringsstruktur som inkluderar Betyg klickar du på "4" för att ange att endast produkter med en klassificering på 4 eller högre ska visas. 
4. Som svar skickar programmet en fråga som innehåller`$filter=Rating ge 4` 
5. Presentationslagret uppdaterar sidan och visar en reducerad resultatuppsättning som bara innehåller de objekt som uppfyller de nya kriterierna (i det här fallet produkter med klassat 4 och uppåt).

En aspekt är en frågeparameter, men förväxla den inte med frågeindata. Det används aldrig som urvalskriterier i en fråga. Tänk i stället på aspektfrågeparametrar som indata till navigeringsstrukturen som kommer tillbaka i svaret. För varje aspektfrågasparameter som du anger utvärderar Azure Cognitive Search hur många dokument som är i delresultaten för varje nominellt värde.

Lägg `$filter` märke till i steg 4. Filtret är en viktig aspekt av fasterad navigering. Även om faser och filter är oberoende i API: et, måste du både leverera den erfarenhet du tänker. 

### <a name="app-design-pattern"></a>Mönster för appdesign

I programkoden är mönstret att använda fatfrågeparametrar för att returnera den fastrade navigeringsstrukturen tillsammans med fastresultat, plus ett $filter uttryck.  Filteruttrycket hanterar klickhändelsen på fasningsvärdet. Tänk på `$filter` uttrycket som koden bakom den faktiska trimningen av sökresultat som returneras till presentationslagret. Med tanke på en färgastektiv implementeras `$filter` färgen Röd genom ett uttryck som bara markerar de objekt som har en röd färg. 

### <a name="query-basics"></a>Grunderna i fråga

I Azure Cognitive Search anges en begäran via en eller flera frågeparametrar (se [Sökdokument](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) för en beskrivning av var och en). Ingen av frågeparametrarna krävs, men du måste ha minst en för att en fråga ska vara giltig.

Precision, förstås som förmågan att filtrera bort irrelevanta träffar, uppnås genom ett eller båda av dessa uttryck:

-   **sök=**  
    Värdet för den här parametern utgör sökuttrycket. Det kan vara en enda text, eller ett komplext sökuttryck som innehåller flera termer och operatorer. På servern används ett sökuttryck för fulltextsökning, frågar sökbara fält i indexet för matchande termer, returnerar resultat i rangordning. Om du `search` ställer in på null är frågekörningen `search=*`över hela indexet (d.v.s. ). I det här fallet är andra element `$filter` i frågan, till exempel en eller `($filter`poängsättningsprofil,`scoringProfile` de `$orderby`primära faktorer som påverkar vilka dokument som returneras ) och i vilken ordning ( eller ).

-   **$filter=**  
    Ett filter är en kraftfull mekanism för att begränsa storleken på sökresultaten baserat på värdena för specifika dokumentattribut. A `$filter` utvärderas först, följt av fasningslogik som genererar tillgängliga värden och motsvarande antal för varje värde

Komplexa sökuttryck minskar frågans prestanda. Använd om möjligt välkonstruerade filteruttryck för att öka precisionen och förbättra frågeprestanda.

Om du bättre vill förstå hur ett filter lägger till mer precision kan du jämföra ett komplext sökuttryck med ett som innehåller ett filteruttryck:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq 'Seattle' and Parking and Type ne 'motel'`

Båda frågorna är giltiga, men den andra är överlägsen om du letar efter icke-motell med parkering i Seattle.
-   Den första frågan bygger på att de specifika ord som nämns eller inte nämns i strängfält som Namn, Beskrivning och alla andra fält som innehåller sökbara data.
-   Den andra frågan letar efter exakta matchningar på strukturerade data och kommer sannolikt att vara mycket mer exakt.

I program som innehåller fasterad navigering kontrollerar du att varje användaråtgärd över en fasterad navigeringsstruktur åtföljs av en förträngning av sökresultaten. Om du vill begränsa resultaten använder du ett filteruttryck.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Skapa en fasterad navigeringsapp
Du implementerar fasterad navigering med Azure Cognitive Search i din programkod som skapar sökbegäran. Den fasterade navigeringen är beroende av element i schemat som du definierade tidigare.

Fördefinierad i sökindexet `Facetable [true|false]` är indexattributet, som anges på valda fält för att aktivera eller inaktivera deras användning i en aspektad navigeringsstruktur. Utan `"Facetable" = true`kan ett fält inte användas i aspektnavigering.

Presentationslagret i koden ger användarupplevelsen. Den bör lista de ingående delarna av den fasterade navigeringen, till exempel etikett, värden, kryssrutor och antalet. Azure Cognitive Search REST API är plattformsoberoende, så använd vilket språk och vilken plattform du vill. Det viktiga är att inkludera gränssnittselement som stöder inkrementell uppdatering, med uppdaterat gränssnittstillstånd när varje ytterligare aspekt väljs. 

Vid frågetillfället skapar programkoden en `facet=[string]`begäran som innehåller en begäranparameter som ger fältet att aspekt av. En fråga kan ha flera aspekter, till exempel `&facet=color&facet=category&facet=rating`, var och en avgränsad med ett et-tecken (&).

Programkoden måste också `$filter` skapa ett uttryck för att hantera klickhändelser i aspektad navigering. A `$filter` minskar sökresultaten med hjälp av det nominella värdet som filtervillkor.

Azure Cognitive Search returnerar sökresultaten, baserat på ett eller flera termer som du anger, tillsammans med uppdateringar av den fasterade navigeringsstrukturen. I Azure Cognitive Search är fasterad navigering en konstruktion på en nivå, med aspektvärden, och räknar med hur många resultat som hittas för var och en.

I följande avsnitt tar vi en närmare titt på hur man bygger varje del.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Skapa indexet
Fasning aktiveras fält för fält i indexet via det här `"Facetable": true`indexattributet: .  
Alla fälttyper som eventuellt kan användas i `Facetable` fasterad navigering är som standard. Sådana fälttyper `Edm.String` `Edm.DateTimeOffset`inkluderar , och alla numeriska fälttyper (i huvudsak `Edm.GeographyPoint`är alla fälttyper vändbara utom , som inte kan användas i fasetterad navigering). 

När du skapar ett index är en bästa praxis för aspektad navigering att uttryckligen stänga av fasning för fält som aldrig ska användas som aspekt.  I synnerhet bör strängfält för singleton-värden, till exempel ett `"Facetable": false` ID eller produktnamn, ställas in så att de oavsiktlig (och ineffektiv) används i fasetterad navigering. Om du inaktiverar ytan där du inte behöver det kan du hålla storleken på indexet litet och förbättrar vanligtvis prestanda.

Följande är en del av schemat för exempelappen Job Portal Demo, trimmad av vissa attribut för att minska storleken:

```json
{
  ...
  "name": "nycjobs",
  "fields": [
    { "name": "id",                 "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { "name": "job_id",             "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { "name": "agency",              "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "posting_type",        "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "num_of_positions",    "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "business_title",      "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "civil_service_title", "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "title_code_no",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "level",               "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_range_from",   "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_range_to",     "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_frequency",    "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "work_location",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
…
    { "name": "geo_location",        "type": "Edm.GeographyPoint",     "searchable": false, "filterable": true, ...  "facetable": false, ... },
    { "name": "tags",                "type": "Collection(Edm.String)", "searchable": true,  "filterable": true, ...  "facetable": true, ...  }
  ],
…
}
```

Som du kan se i `Facetable` exempelschemat är inaktiverat för strängfält som inte ska användas som fasett, till exempel ID-värden. Om du inaktiverar ytan där du inte behöver det kan du hålla storleken på indexet litet och förbättrar vanligtvis prestanda.

> [!TIP]
> Som bästa praxis bör du inkludera den fullständiga uppsättningen indexattribut för varje fält. Även `Facetable` om det är aktiverat som standard för nästan alla fält kan du tänka igenom konsekvenserna av varje schemabeslut avsiktligt. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Kontrollera data
Kvaliteten på dina data har en direkt effekt på om den fasterade navigeringsstrukturen materialiseras som du förväntar dig. Det påverkar också hur enkelt det är att konstruera filter för att minska resultatuppsättningen.

Om du vill aspekt av varumärke eller pris ska varje dokument innehålla värden för *BrandName* och *ProductPrice* som är giltiga, konsekventa och produktiva som ett filteralternativ.

Här är några påminnelser om vad man ska skrubba för:

* För varje fält som du vill aspekt av, fråga dig själv om det innehåller värden som är lämpliga som filter i självstyrd sökning. Värdena bör vara korta, beskrivande och tillräckligt distinkta för att kunna erbjuda ett tydligt val mellan konkurrerande alternativ.
* Felstavningar eller nästan matchande värden. Om du aspekt på Färg och fältvärden inkluderar Orange och Ornage (en felstavning), en aspekt baserad på fältet Färg skulle plocka upp båda.
* Blandad falltext kan också orsaka förödelse i fasterad navigering, med orange och Orange visas som två olika värden. 
* Enstaka och pluralversioner av samma värde kan resultera i en separat aspekt för varje.

Som ni kan föreställa er, flit i att förbereda data är en viktig aspekt av effektiv aspekt av navigering.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Skapa användargränssnittet
Om du går tillbaka från presentationslagret kan du hitta krav som kan missas på annat sätt och förstå vilka funktioner som är nödvändiga för sökupplevelsen.

När det gäller fasterad navigering visar webb- eller programsidan den fasterade navigeringsstrukturen, identifierar användarindata på sidan och infogar de ändrade elementen. 

För webbapplikationer används AJAX ofta i presentationslagret eftersom det gör att du kan uppdatera inkrementella ändringar. Du kan också använda ASP.NET MVC eller någon annan visualiseringsplattform som kan ansluta till en Azure Cognitive Search-tjänst via HTTP. Exempelprogrammet som refereras i den här artikeln – **Azure Cognitive Search Job Portal Demo** – råkar vara ett ASP.NET MVC-program.

I exemplet är fasterad navigering inbyggd i sökresultatsidan. I följande exempel, `index.cshtml` taget från filen i exempelprogrammet, visas den statiska HTML-strukturen för att visa aspektig navigering på sökresultatsidan. Listan över fasor byggs eller återskapas dynamiskt när du skickar in en sökterm, eller väljer eller rensar en aspekt.

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

Följande kodavsnitt från `index.cshtml` sidan skapar HTML-koden dynamiskt för att visa den första aspekten, Affärstitel. Liknande funktioner dynamiskt bygga HTML för andra aspekter. Varje aspekt har en etikett och ett antal, som visar antalet objekt som hittades för det aspektresultatet.

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

> [!TIP]
> Kom ihåg att lägga till en mekanism för att rensa fasor när du utformar sökresultatsidan. Om du lägger till kryssrutor kan du enkelt se hur du avmarkerar filtren. För andra layouter kan du behöva ett brödsmulor eller en annan kreativ metod. I exempelvis jobbsökningsportalens exempelprogram `[X]` kan du klicka på efter en markerad aspekt för att rensa aspekten.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Skapa frågan
Koden som du skriver för byggnadsfrågor ska ange alla delar av en giltig fråga, inklusive sökuttryck, faser, filter, bedömningsprofiler – allt som används för att formulera en begäran. I det här avsnittet utforskar vi var aspekter passar in i en fråga och hur filter används med fasor för att leverera en reducerad resultatuppsättning.

Observera att faset är integrerade i det här exempelprogrammet. Sökupplevelsen i Job Portal Demo är utformad kring fasterad navigering och filter. Den framträdande placeringen av fasterad navigering på sidan visar dess betydelse. 

Ett exempel är ofta ett bra ställe att börja på. I följande exempel, `JobsSearch.cs` som hämtats från filen, skapas en begäran som skapar aspektnavigering baserat på affärstitel, plats, bokföringstyp och minimilön. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

En aspektfrågaparameter är inställd på ett fält och beroende på datatyp kan den parameteriseras `count:<integer>` `sort:<>`ytterligare `interval:<integer>`av `values:<list>`kommaavgränsad lista som innehåller , , och . En värdelista stöds för numeriska data när du ställer in områden. Mer information om användning finns [i Sökdokument (Azure Cognitive Search API).](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

Tillsammans med fasor, begäran formuleras av ditt program bör också bygga filter för att begränsa uppsättningen kandidatdokument baserat på ett nominellt värde val. För en cykel butik, fasterad navigering ger ledtrådar till frågor som *Vilka färger, tillverkare och typer av cyklar finns tillgängliga?*. Filtrering svarar på frågor som *Vilka exakta cyklar är röda, mountainbikes, i denna prisklass?*. När du klickar på "Röd" för att ange att endast röda `$filter=Color eq 'Red'`produkter ska visas, innehåller nästa fråga som programmet skickar .

Följande kodavsnitt på `JobsSearch.cs` sidan lägger till den valda företagstiteln i filtret om du väljer ett värde från affärsrubrikens aspekt.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Tips och regelverk

### <a name="indexing-tips"></a>Indexeringstips
**Förbättra indexeffektiviteten om du inte använder en sökruta**

Om programmet använder aspekten navigering exklusivt (det vill säga ingen sökruta) kan du markera fältet som `searchable=false`för `facetable=true` att skapa ett mer kompakt index. Dessutom sker indexering endast på hela aspektvärden, utan ordbrytning eller indexering av komponentdelarna i ett flerordsvärde.

**Ange vilka fält som kan användas som fasor**

Kom ihåg att schemat för indexet avgör vilka fält som är tillgängliga att använda som en aspekt. Om du antar att ett fält är facetable anger frågan vilka fält som ska visas. Det fält som du faser med innehåller värden som visas under etiketten. 

De värden som visas under varje etikett hämtas från indexet. Om faktfältet till exempel är *Färg*är de värden som är tillgängliga för ytterligare filtrering värdena för det fältet - Röd, Svart och så vidare.

Endast för Numeriska värden och DateTime-värden kan du uttryckligen ange `facet=Rating,values:1|2|3|4|5`värden i aspektfältet (till exempel ). En värdelista tillåts för dessa fälttyper för att förenkla separationen av fastresultat i sammanhängande intervall (antingen intervall baserat på numeriska värden eller tidsperioder). 

**Som standard kan du bara ha en nivå av fasterad navigering** 

Som nämnts finns det inget direkt stöd för kapsling aspekter i en hierarki. Som standard stöder fakterad navigering i Azure Cognitive Search bara en nivå av filter. Det finns dock lösningar. Du kan koda en hierarkisk `Collection(Edm.String)` aspektstruktur i en med en startpunkt per hierarki. Implementering av den här lösningen ligger utanför den här artikelns omfattning. 

### <a name="querying-tips"></a>Tips för frågor
**Validera fält**

Om du skapar listan över fasor dynamiskt baserat på ej betrodda användarindata, verifiera att namnen på de fasterade fälten är giltiga. Du kan också undkomma namnen när `Uri.EscapeDataString()` du skapar webbadresser genom att använda antingen i .NET eller motsvarande i valfri plattform.

### <a name="filtering-tips"></a>Filtrera tips
**Öka sökprecisionen med filter**

Använd filter. Om du bara förlitar dig på enbart sökuttryck kan det leda till att ett dokument returneras som inte har det exakta nominella värdet i något av dess fält.

**Öka sökprestanda med filter**

Filter begränsar uppsättningen kandidatdokument för sökning och utesluter dem från rangordningen. Om du har en stor uppsättning dokument ger du ofta bättre prestanda med hjälp av en selektiv aspektgranskning.
  
**Filtrera endast fasterade fält**

I aspektbaserad detaljgranskning vill du vanligtvis bara inkludera dokument som har det nominella värdet i ett specifikt (fasterat) fält, inte någonstans i alla sökbara fält. Om du lägger till ett filter förstärker målfältet genom att tjänsten endast söks i det fasterade fältet efter ett matchande värde.

**Trimma fastresultat med fler filter**

Aspektresultat är dokument som finns i sökresultaten som matchar en aspektterm. I följande exempel, i sökresultat för *cloud computing*, har 254 objekt också *intern specifikation* som innehållstyp. Objekt är inte nödvändigtvis ömsesidigt uteslutande. Om en artikel uppfyller kriterierna för båda filtren räknas den i var och en. Duplicering är möjlig när `Collection(Edm.String)` du faceting på fält, som ofta används för att implementera dokumenttaggning.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Om du upptäcker att aspektresultaten är konsekvent för stora rekommenderar vi i allmänhet att du lägger till fler filter för att ge användarna fler alternativ för att begränsa sökningen.

### <a name="tips-about-result-count"></a>Tips om resultaträkning

**Begränsa antalet objekt i aspektnavigeringen**

För varje fasterat fält i navigeringsträdet finns det en standardgräns på 10 värden. Den här standardinställningen är meningsfull för navigeringsstrukturer eftersom den håller värdelistan till en hanterbar storlek. Du kan åsidosätta standardinställningen genom att tilldela ett värde som ska räknas.

* `&facet=city,count:5`anger att endast de fem första städerna som finns i de högst rankade resultaten returneras som ett aspektresultat. Överväg en exempelfråga med söktermen "flygplats" och 32 matchningar. Om frågan anger `&facet=city,count:5`inkluderas endast de fem första unika städerna med flest dokument i sökresultaten i aspektresultaten.

Lägg märke till skillnaden mellan fatesresultat och sökresultat. Sökresultat är alla dokument som matchar frågan. Fastresultat är matchningarna för varje aspektvärde. I exemplet innehåller sökresultaten Ortnamn som inte finns i aspektklassificeringslistan (5 i vårt exempel). Resultat som filtreras bort via fasterad navigering blir synliga när du rensar fasor, eller väljer andra aspekter förutom City. 

> [!NOTE]
> Att `count` diskutera när det finns mer än en typ kan vara förvirrande. Följande tabell innehåller en kort sammanfattning av hur termen används i Azure Cognitive Search API, exempelkod och dokumentation. 

* `@colorFacet.count`<br/>
  I presentationskod bör du se en räkneparameter på aspekten som används för att visa antalet aspektresultat. I aspektresultat anger antalet dokument som matchar på aspekttermen eller intervallet.
* `&facet=City,count:12`<br/>
  I en aspektfråga kan du ange antalet till ett värde.  Standardvärdet är 10, men du kan ställa in den högre eller lägre. Inställningen `count:12` får de 12 bästa matcherna i aspektresultat efter antal dokument.
* "`@odata.count`"<br/>
  I frågesvaret anger det här värdet antalet matchande objekt i sökresultaten. I genomsnitt är den större än summan av alla sökresultat som kombineras, på grund av förekomsten av objekt som matchar söktermen, men som inte har några svarovärdesmatchningar.

**Få räknas i aspektresultat**

När du lägger till ett filter i en aspektfråga kanske du vill `facet=Rating&$filter=Rating ge 4`behålla aspektsatsen (till exempel ). Tekniskt sett är facet =Rating inte behövs, men att hålla den returnerar antalet faktvärden för betyg 4 och högre. Om du till exempel klickar på "4" och frågan innehåller ett filter för större eller lika med "4", returneras antalet för varje klassificering som är 4 och högre.  

**Se till att du får korrekta faktantal**

Under vissa omständigheter kan det hända att fatesantalet inte matchar resultatuppsättningarna (se [Fasterad navigering i Azure Cognitive Search (foruminlägg).](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)

Fastantal kan vara felaktigt på grund av fragmenteringsarkitekturen. Varje sökindex har flera shards och varje fragment rapporterar de översta N-aspekterna efter dokumentantal, som sedan kombineras till ett enda resultat. Om vissa shards har många matchande värden, medan andra har färre, kan du upptäcka att vissa aspektvärden saknas eller underräknas i resultaten.

Även om det här beteendet kan ändras när som helst, om du stöter\<på det här beteendet idag, kan du undvika det genom att blåsa upp antalet på konstgjord väg: antalet> till ett stort antal för att framtvinga fullständig rapportering från varje shard. Om värdet för antalet: är större än eller lika med antalet unika värden i fältet, garanteras du korrekta resultat. Men när antalet dokument är höga, det finns en prestanda straff, så använd det här alternativet omdömesgillt.

### <a name="user-interface-tips"></a>Tips för användargränssnitt
**Lägga till etiketter för varje fält i aspektnavigering**

Etiketter definieras vanligtvis i HTML-`index.cshtml` eller formuläret (i exempelprogrammet). Det finns inget API i Azure Cognitive Search för aspektnavigeringsetiketter eller andra metadata.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filter baserat på ett intervall
Faceting över intervall av värden är ett vanligt sökprogram krav. Intervall stöds för numeriska data och DateTime-värden. Du kan läsa mer om varje metod i [sökdokument (Azure Cognitive Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

Azure Cognitive Search förenklar intervallkonstruktionen genom att tillhandahålla två metoder för att beräkna ett intervall. För båda metoderna skapar Azure Cognitive Search lämpliga intervall med tanke på de indata du har angett. Om du till exempel anger intervallvärden på 10|20|30 skapas intervall på 0–10, 10–20, 20–30. Programmet kan eventuellt ta bort alla toma intervall. 

**Metod 1: Använd intervallparametern**  
Om du vill ange prisastrar i steg om 10 000 kr anger du:`&facet=price,interval:10`

**Metod 2: Använd en värdelista**  
För numeriska data kan du använda en värdelista.  Tänk på aspektintervallet `listPrice` för ett fält, återges enligt följande:

  ![Lista över exempelvärden](media/search-faceted-navigation/Facet-5-Prices.PNG "Lista över exempelvärden")

Om du vill ange ett aspektintervall som det i föregående skärmbild använder du en värdelista:

    facet=listPrice,values:10|25|100|500|1000|2500

Varje område skapas med 0 som utgångspunkt, ett värde från listan som en slutpunkt och trimmas sedan av föregående intervall för att skapa diskreta intervall. Azure Cognitive Search gör dessa saker som en del av fasterad navigering. Du behöver inte skriva kod för att strukturera varje intervall.

### <a name="build-a-filter-for-a-range"></a>Skapa ett filter för ett område
Om du vill filtrera dokument baserat på `"ge"` ett `"lt"` område som du väljer kan du använda operatorerna och filtret i ett tvådelat uttryck som definierar slutpunkterna för intervallet. Om du till exempel väljer intervallet 10-25 för `listPrice` ett `$filter=listPrice ge 10 and listPrice lt 25`fält blir filtret . I exempelkoden använder filteruttrycket **priceFrom-** och **priceTo-parametrar** för att ange slutpunkter. 

  ![Fråga efter ett värdeområde](media/search-faceted-navigation/Facet-6-buildfilter.PNG "Fråga efter ett värdeområde")

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filter baserat på avstånd
Det är vanligt att se filter som hjälper dig att välja en butik, restaurang eller destination baserat på dess närhet till din aktuella plats. Även om den här typen av filter kan se ut som fasterad navigering, är det bara ett filter. Vi nämner det här för er som specifikt letar efter genomförande råd för just detta designproblem.

Det finns två Geospatiala funktioner i Azure Cognitive Search, **geo.distance** och **geo.intersects**.

* **Geo.distance-funktionen** returnerar avståndet i kilometer mellan två punkter. En punkt är ett fält och en annan är en konstant som skickas som en del av filtret. 
* Funktionen **geo.intersects returnerar** sant om en viss punkt finns inom en viss polygon. Punkten är ett fält och polygonen anges som en konstant lista över koordinater som skickas som en del av filtret.

Du hittar filterexempel i [OData-uttryckssyntax (Azure Cognitive Search)](query-odata-filter-orderby-syntax.md).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Prova demon
Azure Cognitive Search Job Portal Demo innehåller de exempel som refereras i den här artikeln.

-   Se och testa den fungerande demon online på [Azure Cognitive Search Job Portal Demo](https://aka.ms/azjobsdemo).

-   Hämta koden från [reporäntan för Azure-Samples på GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

När du arbetar med sökresultat tittar du på webbadressen efter ändringar i frågekonstruktionen. Det här programmet råkar lägga till fasat till URI när du väljer var och en.

1. Om du vill använda mappningsfunktionen i demoappen hämtar du en Bing Maps-nyckel från [Bing Maps Dev Center](https://www.bingmapsportal.com/). Klistra in den över `index.cshtml` den befintliga nyckeln på sidan. Inställningen `BingApiKey` i `Web.config` filen används inte. 

2. Kör appen. Ta den valfria turen eller stäng dialogrutan.
   
3. Ange en sökterm, till exempel "analytiker", och klicka på sökikonen. Frågan körs snabbt.
   
   En fasterad navigeringsstruktur returneras också med sökresultaten. På sökresultatsidan innehåller den fasterade navigeringsstrukturen antal för varje aspektresultat. Inga aspekter är markerade, så alla matchande resultat returneras.
   
   ![Sökresultat innan du väljer faset](media/search-faceted-navigation/faceted-search-before-facets.png "Sökresultat innan du väljer faset")

4. Klicka på en företagstitel, plats eller minimilön. Fasat var null på den första sökningen, men när de tar på värden, är sökresultaten trimmas av objekt som inte längre matchar.
   
   ![Sökresultat efter att ha valt faset](media/search-faceted-navigation/faceted-search-after-facets.png "Sökresultat efter att ha valt faset")

5. Om du vill rensa den fasterade frågan så `[X]` att du kan prova olika frågebeteenden klickar du på efter de markerade faserna för att rensa faserna.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Läs mer
Titta på [Azure Cognitive Search Deep Dive](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). Klockan 45:25 finns en demo om hur man implementerar aspekter.

För mer information om designprinciper för aspekten navigering rekommenderar vi följande länkar:

* [Designmönster: Fastrade navigering](https://alistapart.com/article/design-patterns-faceted-navigation)
* [Front End oro vid genomförandet fasterad sökning - Del 1](https://articles.uie.com/faceted_search2/)

