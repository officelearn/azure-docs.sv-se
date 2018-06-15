---
title: Implementera fasetterad navigering i Azure Search | Microsoft Docs
description: Lägga till fasetterad navigering i program som integreras med Azure Search molnbaserade search-tjänsten på Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 3/10/2017
ms.author: heidist
ms.openlocfilehash: e00e875619e4ed6800f5739362ff0c52971f6f16
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195302"
---
# <a name="how-to-implement-faceted-navigation-in-azure-search"></a>Implementera aspektbaserad navigering i Azure Search
Fasetterad navigering är en filtreringsmekanism som ger automatisk dirigerad nedbrytning navigering i sökprogram. Termen ”fasetterad navigering' kanske inte känner, men du förmodligen har använt det förut. I följande exempel visas är fasetterad navigering inget annat än de kategorier som används för att filtrera resultat.

 ![Azure Search-jobbet Portal Demo][1]

Fasetterad navigering är en annan startpunkt att söka. Det är ett praktiskt alternativ till att skriva komplicerade sökuttryck manuellt. Facets kan hjälpa dig att hitta det du söker efter, samtidigt som du säkerställer att du inte blir noll. Som en utvecklare kan du exponera mest användbara sökkriterierna för att navigera din sökning Kristi facets. I online retail-program bygger ofta fasetterad navigering via varumärken, avdelningar (barnens situation), storlek, pris, popularitet och klassificeringar. 

Implementera fasetterad navigering skiljer sig åt mellan Sök tekniker. I Azure Search skapas fasetterad navigering när databasfrågan, med fält som du tidigare attributet i schemat.

-   I de frågor som programmet skapar en fråga måste skicka *aspekten frågeparametrar* att hämta tillgängliga aspekten värden för detta dokument.

-   Att ta bort dokumentet resultatet faktiskt inställd och programmet måste också använda en `$filter` uttryck.

I din programutveckling skriva kod som skapar frågor som utgör den största delen av arbetet. Många av de programfunktioner som du förväntar dig från fasetterad navigering tillhandahålls av tjänsten, inklusive inbyggt stöd för att definiera intervall och hämtar antal för aspekten resultat. Tjänsten innehåller sensible standardvärden som hjälper dig också undvika svårhanterliga navigeringsstrukturer. 

## <a name="sample-code-and-demo"></a>Exempelkod och demo
Den här artikeln används en jobbet sökportal som exempel. Exemplet implementeras som en ASP.NET MVC-program.

-   Se och testa den fungerande demon online på [Azure Search-jobbet Portal Demo](http://azjobsdemo.azurewebsites.net/).

-   Hämta koden från den [lagringsplatsen för Azure-exempel på GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Kom igång
Om du har använt Sök utveckling, är det bästa sättet att tänka på fasetterad navigering att den visar möjligheterna för automatisk dirigerad sökning. Det är en typ av nedåt sökinställningar, baserat på fördefinierade filter som används för att begränsa snabbt ned sökresultat via plats och klicka på åtgärder. 

### <a name="interaction-model"></a>Interaktion modellen

Sökinställningar för fasetterad navigering är iterativ, så vi först förstå som en sekvens av frågor som skapas som svar på åtgärder från användaren.

Startpunkten är en sida för program som ger fasetterad navigering, normalt placeras Infästningspunkten. Fasetterad navigering är ofta en trädstruktur med kryssrutorna för varje värde eller en klickbar text. 

1. En fråga skickas till Azure Search anger navigeringsstrukturen fasetterad via en eller flera aspekten Frågeparametrar. Till exempel frågan kan innehålla `facet=Rating`, kanske med en `:values` eller `:sort` alternativet för att förfina presentationen.
2. Presentation lagret återger en sida som innehåller fasetterad navigering, med aspekterna som har angetts i begäran.
3. En fasetterad navigeringsstruktur som innehåller klassificering får du klickar på ”4” som anger att endast produkter med en klassificering på 4 eller senare ska visas. 
4. Svar skickar programmet en fråga som innehåller `$filter=Rating ge 4` 
5. Presentation lagret uppdaterar sidan visar en minskad resultatmängd som innehåller bara de objekt som uppfyller de nya kriterierna (i det här fallet produkter klassificerade 4 och senare).

En begränsningsaspekt är en frågeparameter men Blanda inte ihop med frågan indata. Den används aldrig som urvalskriterier i en fråga. Se i stället aspekten frågeparametrar som indata till navigeringsstrukturen som kommer tillbaka i svaret. För varje aspekten frågeparameter som du anger, utvärderar Azure Search är hur många dokumenten i partiella resultaten för varje aspektvärdet.

Observera den `$filter` i steg 4. Filtret är en viktig del av fasetterad navigeringen. Även om facets och filter är oberoende API: et, måste båda att leverera den erfarenhet som du avser. 

### <a name="app-design-pattern"></a>Designmönstret för App

I programkoden är mönstret att använda aspekten frågeparametrar för att returnera fasetterad navigeringsstrukturen tillsammans med aspekten resultat, plus ett $filter uttryck.  Filteruttrycket hanterar klickningshändelsen på aspektvärdet. Se den `$filter` uttryck som koden bakom faktiska Trimning av sökresultat returneras i lagret med presentation. Klicka på röd färg ges en färger-begränsningsaspekt implementeras via en `$filter` som väljer endast de objekt som har en röd färg. 

### <a name="query-basics"></a>Grunderna i frågan

En begäran har angetts via en eller flera frågeparametrar i Azure Search (se [Sök dokument](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) en beskrivning av var och en). Ingen av frågeparametrarna krävs, men du måste ha minst en för en fråga ska vara giltigt.

Precision, tolkas som möjlighet att filtrera bort irrelevanta träffar uppnås genom en eller båda av dessa uttryck.

-   **Sök =**  
    Värdet för den här parametern utgör sökuttryck. Det kan vara en enda typ av text eller en komplex sökning-uttryck som innehåller flera villkor och operatörer. Ett sökuttryck används på servern för textsökning frågar sökbara fält i indexet för matchning av villkor, returnerar resultat i rank ordning. Om du ställer in `search` null frågan är körningen över hela index (det vill säga `search=*`). I det här fallet, andra delar av frågan som en `$filter` eller bedömningen profil, är de primära faktorer som påverkar vilka dokument som returneras `($filter`) och i vilken ordning (`scoringProfile` eller `$orderby`).

-   **$filter =**  
    Ett filter är en kraftfull mekanism för att begränsa storleken på sökresultatet baserat på värdena i specifika dokumentattribut. En `$filter` är utvärderas först, följt av faceting logik som genererar tillgängliga värden och motsvarande räknare för varje värde

Komplexa sökuttryck försämra prestandan för frågan. Om möjligt använda väl avvägt filteruttryck för att öka precision och förbättra frågeprestanda.

För att bättre förstå hur ett filter lägger till mer exakta, jämför en komplex sökuttryck till ett som innehåller ett filteruttryck:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Båda frågorna är giltig men andra är högre om du letar efter icke motell med parkering i Seattle.
-   Den första frågan är beroende av dessa specifika ord som nämns eller inte anges i strängfält som namn, beskrivning och ett annat fält som innehåller sökbara data.
-   Den andra frågan söker efter exakt matchar på strukturerade data och kan förväntas vara mycket mer exakt.

Kontrollera att varje användaråtgärd via en fasetterad navigeringsstruktur åtföljs av en begränsa sökresultaten i program som innehåller fasetterad navigering. Använd ett filteruttryck för att begränsa resultaten.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Skapa en app fasetterad navigering
Du kan implementera fasetterad navigering med Azure Search i din programkod som bygger sökbegäran. Fasetterad navigeringen är beroende av element i din schema som du definierade tidigare.

Fördefinierade i sökningen index är den `Facetable [true|false]` index attributuppsättningen på markerade fälten för att aktivera eller inaktivera deras användning i en fasetterad navigeringsstruktur. Utan `"Facetable" = true`, ett fält kan inte användas i aspekten navigering.

Presentation lager i koden innehåller användarupplevelsen. Den bör innehålla beståndsdelar av fasetterad navigering, till exempel etiketten, värden, kryssrutorna och antalet. Azure Search REST API är plattformsoberoende, så Använd oavsett språk och plattformar som du vill. Viktigt är att inkludera UI-element som stöder inkrementell uppdatering med uppdaterade gränssnittstillstånd eftersom varje ytterligare aspekten har valts. 

Frågan samtidigt programkoden skapar en begäran som innehåller `facet=[string]`, en begäranparameter för som innehåller fältet till aspekten av. En fråga kan ha flera aspekter som `&facet=color&facet=category&facet=rating`, var och en avgränsade med ett et-tecken (&).

Programkod måste också skapa ett `$filter` uttryck för att hantera klickar du på händelser i fasetterad navigeringsfältet. En `$filter` minskar sökresultaten med aspektvärdet som filtervillkor.

Azure Search returnerar sökresultat, baserat på en eller flera villkor som du anger, tillsammans med uppdateringar till fasetterad navigeringsstrukturen. I Azure Search fasetterad navigering är en nivå-konstruktion med aspekten värden och räknar hur många resultat hittades för var och en.

I följande avsnitt ta vi en närmare titt på hur du skapar varje del.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Skapa index
Faceting är aktiverat för fältet av fält i index via det här attributet index: `"Facetable": true`.  
Alla typer av fält som eventuellt kan användas i fasetterad navigering `Facetable` som standard. Dessa typer av fältet `Edm.String`, `Edm.DateTimeOffset`, och alla typer av numeriskt fält (i princip alla fälttyp är facetable utom `Edm.GeographyPoint`, som inte kan användas i fasetterad navigeringsfältet). 

När du skapar ett index är bästa praxis för fasetterad navigering uttryckligen inaktivera faceting för fält som ska aldrig användas som en säkerhetsåtgärd.  I synnerhet strängfält för singleton-värden, till exempel ett ID eller produkt-namn ska anges till `"Facetable": false` för att förhindra användningen oavsiktliga (och ineffektiv) i fasetterad navigeringsfältet. Aktivera faceting av där du inte behöver det hjälper till att storleken på indexet liten och vanligtvis förbättrar prestandan.

Följande är en del av schemat för exempelappen jobb Portal Demo trimmade av vissa attribut för att minska storlek:

```json
{
  ...
  "name": "nycjobs",
  "fields": [
    { “name”: "id",                 "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { “name”: "job_id",             "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { “name”: "agency",              "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "posting_type",        "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "num_of_positions",    "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "business_title",      "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "civil_service_title", "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "title_code_no",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "level",               "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_range_from",   "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_range_to",     "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_frequency",    "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "work_location",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
…
    { “name”: "geo_location",        "type": "Edm.GeographyPoint",     "searchable": false, "filterable": true, ...  "facetable": false, ... },
    { “name”: "tags",                "type": "Collection(Edm.String)", "searchable": true,  "filterable": true, ...  "facetable": true, ...  }
  ],
…
}
```

Som du ser i exemplet-schemat `Facetable` är inaktiverat för strängfält som inte får användas som facets, t.ex ID-värden. Aktivera faceting av där du inte behöver det hjälper till att storleken på indexet liten och vanligtvis förbättrar prestandan.

> [!TIP]
> Som bästa praxis, innehåller en fullständig uppsättning indexattribut för varje fält. Även om `Facetable` är aktiverad som standard för nästan alla fält, ange ändamålsenligt varje attribut kan hjälpa dig att tänka igenom konsekvenserna av att varje beslut om schemat. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Kontrollera data
Kvaliteten på dina data har en direkt inverkan på om navigeringsstrukturen fasetterad materialiseras som förväntat. Det påverkar också enkelt skapa filter för att reducera resultatuppsättningen.

Om du vill aspekten av varumärken eller pris varje dokument som ska innehålla värden för *BrandName* och *ProductPrice* som är giltig, konsekvent och produktiva som ett filter.

Här följer några påminnelser om vad som Skrubba för:

* För varje fält som du vill aspekten av fundera över om den innehåller värden som är lämpliga som filter i själva dirigerad sökning. Värdena bör vara korta och beskrivande tillräckligt särskiljande att avmarkera välja mellan konkurrerande alternativ.
* Stavfel eller nästan matchande värden. Om aspekten färg och fältvärden omfatta Orange och Ornage (felstavat), en begränsningsaspekt baserat på fältet färg skulle hämta båda.
* Blandad case text kan också orsaka oreda i fasetterad navigering med orange och Orange visas som två olika värden. 
* Enkel och pluralis versioner av samma värde kan resultera i en separat begränsningsaspekt för varje.

Du kan föreställa dig är omsorg med förbereder data en viktig aspekt av effektiva fasetterad navigering.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Skapa användargränssnittet
Arbeta från lagret med presentation hjälper dig att upptäcka krav som kan missas annars och förstå vilka funktioner är mycket viktigt att personer.

Vad gäller fasetterad navigering webb- eller sidan visar navigeringsstrukturen fasetterad, identifierar användarindata på sidan och infogar ändrade element. 

För webbprogram används AJAX ofta i lagret med presentation eftersom du kan uppdatera stegvisa ändringar. Du kan också använda ASP.NET MVC eller andra visualiseringen plattform som kan ansluta till en Azure Search-tjänsten via HTTP. Exempelprogrammet hänvisas till i den här artikeln – den **Azure Search-jobbet Portal Demo** – råkar finnas ett ASP.NET MVC-program.

I det här exemplet bygger fasetterad navigering till sökresultatsidan. I följande exempel hämtas från den `index.cshtml` resultatsida-filen för det här exempelprogrammet visar statiska HTML-strukturen för att visa fasetterad navigering för sökningen. Listan över facets inbyggda eller byggas dynamiskt om du skickar en sökterm eller markera eller avmarkera en begränsningsaspekt.

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

Följande kodavsnitt från den `index.cshtml` sidan dynamiskt skapa HTML-koden för att visa den första aspekten befattning. Liknande funktioner att dynamiskt skapa HTML för andra facets. Varje aspekten har en etikett och ett antal som visar hur många objekt hittades för aspekten resultatmängden.

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
> Kom ihåg att lägga till en mekanism för att rensa facets när du utformar sökresultatsidan. Om du lägger till kryssrutor kan du enkelt se Rensa filter. För andra layouter behöva spåret mönster eller en annan kreativa metod. Till exempel i exempelprogrammet jobbet Sök portalen kan du klicka på `[X]` efter en valda begränsningsaspekt att rensa aspekten.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Skapa frågan
Den kod som du skriver för att skapa frågor ska ange alla delar av en giltig fråga, inklusive sökuttryck, facets, filter, bedömningen profiler – allt används för att formulera en begäran. I det här avsnittet förklarar vi där facets passar in i en fråga och hur filter som används av aspekter för att leverera en minskad resultatmängd.

Observera att facets är integrerad i det här exempelprogrammet. Sökinställningar i jobbet Portal Demo är designad runt fasetterad navigering och filter. Framträdande placeringen av fasetterad navigering på sidan visar dess prioritet. 

Ett exempel är ofta en bra plats att börja. I följande exempel hämtas från den `JobsSearch.cs` -fil, en begäran som skapar aspekten navigering baserat på befattning, plats, bokföring och minsta lön versioner. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Begränsningsaspekten frågeparameter har angetts till ett fält och beroende på datatyp, kan ytterligare parameteriseras via kommaavgränsad lista med `count:<integer>`, `sort:<>`, `interval:<integer>`, och `values:<list>`. En värdelista som stöds för numeriska data när du ställer in intervall. Se [Sök dokument (Azure Search-API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) för användningsinformation.

Tillsammans med facets, ska begäran formulerade av programmet även skapa filter som begränsar uppsättningen kandidatdokument baserat på en markering för aspekten värdet. För en cykel store fasetterad navigering innehåller ledtrådar till frågor som *vilka färger, tillverkare och typer av cyklar är tillgängliga?*. Filtrering svar på frågor som *vilka exakt cyklar är röda mountain cyklar i det här pris av intervallet?*. När du klickar på ”Red” för att indikera att endast Red produkter ska visas i nästa fråga som programmet skickar innehåller `$filter=Color eq ‘Red’`.

Följande kodavsnitt från den `JobsSearch.cs` sidan lägger till den valda befattning till filtret om du väljer ett värde från befattning-begränsningsaspekt.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Tips och råd

### <a name="indexing-tips"></a>Indexering tips
**Förbättra effektiviteten för index om du inte använder en kryssruta**

Om programmet använder fasetterad navigering exklusivt (d.v.s. inga sökrutan) kan du markera fältet som `searchable=false`, `facetable=true` att producera ett komprimerat index. Dessutom kan uppstår indexera bara på hela aspekten värden med ingen word radbrytning eller indexering av delarna av en flera ords-värde.

**Ange vilka fält som kan användas som facets**

Kom ihåg att schemat för indexet avgör vilka fält som kan användas som en säkerhetsåtgärd. Under förutsättning att ett fält är facetable anger frågan vilka fält som ska aspekten av. Fältet som du är faceting innehåller de värden som visas under etiketten. 

De värden som visas under varje etikett hämtas från indexet. Om fältet aspekten är till exempel *färg*, värden som är tillgängliga för ytterligare filtrering är värdena för fältet - röd, svart och så vidare.

För numeriska och DateTime-värden endast, kan du ange värden på fältet för aspekten (till exempel `facet=Rating,values:1|2|3|4|5`). En värdelista som tillåts för dessa fält att förenkla avgränsning av aspekten resultat i sammanhängande intervall (antingen intervall baserat på numeriska värden eller tidsperioder). 

**Som standard kan du bara ha en nivå med fasetterad navigering** 

Som nämndes, finns det inget direkt stöd för många kapslade facets i en hierarki. Som standard stöder fasetterad navigering i Azure Search endast en nivå med filter. Dock finns lösningar. Du kan koda en hierarkisk aspekten struktur i en `Collection(Edm.String)` peka per hierarki med en post. Den här lösningen är utanför omfattningen för den här artikeln. 

### <a name="querying-tips"></a>Frågar tips
**Validera fält**

Om du skapar listan över facets dynamiskt baserat på ej betrodda användarindata du validera att namnen på fälten fasetterad är giltiga. Eller undanta namn när du skapar URL: er med hjälp av antingen `Uri.EscapeDataString()` i .NET, eller motsvarande på din plattform föredrar.

### <a name="filtering-tips"></a>Tips för filtrering
**Öka Sök precision med filter**

Använda filter. Om du använder bara sökuttryck som är enbart härrör kan orsaka ett dokument ska returneras som inte har det exakta aspektvärdet i något av dess fält.

**Öka sökningsprestanda med filter**

Filter begränsa uppsättning kandidatdokument för sökning och utesluta rangordning. Om du har ett stort utbud av dokument får med en selektiv begränsningsaspekt nedåt ofta du bättre prestanda.
  
**Filtrera fasetterad fälten**

I fasetterad nedåt vanligtvis vill du inkludera endast dokument som innehåller aspektvärdet i ett visst (fasetterad) fält inte någonstans i alla sökbara fält. Lägger till ett filter förstärker målfältet genom att styra tjänsten för att söka i fasetterad fält för ett motsvarande värde.

**Trim aspekten resultat med flera filter**

Begränsningsaspekten resultat är dokument hittades i sökresultaten som matchar en term som aspekten. I följande exempel visas i sökresultaten för *molntjänster*, 254 objekt har också *interna specifikationen* som en innehållstyp. Objekt är inte nödvändigtvis ömsesidigt uteslutande. Om ett objekt uppfyller ett villkor för båda filtren, räknas den i var och en. Duplicering är möjligt när faceting på `Collection(Edm.String)` fält som ofta används för att implementera dokumentet Taggning.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

I allmänhet om du upptäcker att aspekten resultat konsekvent är för stor, rekommenderar vi att du lägga till fler filter om du vill ge användare fler alternativ för att begränsa sökningen.

### <a name="tips-about-result-count"></a>Tips om resultatantal

**Begränsa antalet objekt i aspekten navigering**

Det finns en standardgräns med 10 värden för varje fasetterad fält i navigeringsträdet. Den här standardinställningen är meningsfullt för navigeringsstrukturer eftersom den bevarar värdelistan till en hanterbar storlek. Du kan åsidosätta standardinställningen genom att tilldela ett värde som ska räknas.

* `&facet=city,count:5` Anger att de första fem orter hittades i det övre rangordnas resultat returneras som ett resultat av aspekten. Överväg att en exempelfråga med en sökterm ”flygplats” och 32 matchar. Om frågan anger `&facet=city,count:5`, de fem första unika orter med flest dokument i sökresultatet ingår i aspekten resultaten.

Observera skillnaden mellan aspekten resultat och sökresultat. Sökresultaten är alla dokument som matchar frågan. Begränsningsaspekten resultat är matchningarna för varje aspektvärdet. Sökresultaten innehåller stadsnamn som inte finns med i listan över klassificering aspekten (5 i vårt exempel) i det här exemplet. Resultat som har filtrerats ut via fasetterad navigering visas när du tar bort facets eller välja andra facets förutom stad. 

> [!NOTE]
> Diskutera `count` när det finns fler än en typ kan vara förvirrande. Följande tabell ger en kort sammanfattning av hur termen används i Azure Search-API, exempelkod och dokumentation. 

* `@colorFacet.count`<br/>
  Du bör se antalet parameter på aspekten, används för att visa antalet aspekten resultat i presentation kod. I aspekten resultat anger antalet antal dokument som matchar det aspekten ord eller ett intervall.
* `&facet=City,count:12`<br/>
  Du kan ange antal till ett värde i en aspekten-fråga.  Standardvärdet är 10, men du kan ange den högre eller lägre. Ange `count:12` hämtar upp 12 matchar i aspekten resultaten av dokumentantal.
* "`@odata.count`"<br/>
  Det här värdet anger hur många matchningar i sökresultaten i svaret på frågan. I genomsnitt den är större än summan av alla aspekten resultat i kombination, på grund av förekomsten av objekt som matchar söktermen, men har ingen aspekten matchar.

**Få fram i aspekten resultat**

När du lägger till ett filter fasetterad frågan kanske du vill behålla instruktionen aspekten (till exempel `facet=Rating&$filter=Rating ge 4`). Tekniskt sett aspekten = klassificering inte krävs, men håller den returnerar antalet aspekten värden för klassificeringar 4 och senare. Om du klickar på ”4” och frågan som innehåller ett filter för större eller lika med ”4”, är till exempel räknar returneras för varje klassificering är 4 och senare.  

**Kontrollera att du få fram exakta aspekten**

I vissa fall kanske du upptäcker att aspekten räknas inte matchar resultatmängder (se [fasetterad navigering i Azure Search (foruminlägg)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Begränsningsaspekten antalet kan vara felaktig på grund av arkitekturen för horisontell partitionering. Varje sökindex har flera delar och varje Fragmentera rapporterar övre N aspekter av dokumentantal som sedan kombineras till ett enskilt resultat. Om vissa delar har många matchande värden, medan andra har färre hända att vissa aspekten värden saknas eller är under-inventerat i resultaten.

Men det här beteendet kan ändra när som helst om du stöter på problemet idag, du kan undvika det genom inflating artificiellt antalet:<number> till ett stort antal att framtvinga fullständig reporting från varje Fragmentera. Om värdet för antal: är större än eller lika med antalet unika värden i fältet, är det garanterat korrekta resultat. Men när dokumentantal är hög, det finns en prestandaförsämring, så Använd det här alternativet omdöme.

### <a name="user-interface-tips"></a>Användaren gränssnittet tips
**Lägga till etiketter för varje fält i aspekten navigering**

Etiketter definieras vanligen i formuläret eller HTML (`index.cshtml` i exempelprogrammet). Det finns inga API i Azure Search för aspekten navigering etiketter eller andra metadata.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filtrera baserat på ett intervall
Faceting över intervall med värden är en gemensam programkrav för sökning. Adressintervall stöds för numeriska data och DateTime-värden. Du kan läsa mer om varje metod i [Sök dokument (Azure Search-API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

Azure Search förenklar intervallet konstruktionen genom att tillhandahålla två metoder för att beräkna ett intervall. Azure Search skapar lämpliga områden angivna indata som du har angett för båda metoderna. Till exempel om du anger intervallvärden 10 | 20 | 30, skapas automatiskt intervall på 0-10, 20 10, 20 – 30. Programmet kan du ta bort alla intervall som är tomma. 

**Metod 1: Använd parametern intervall**  
Om du vill ange pris facets i $10 steg, anger du: `&facet=price,interval:10`

**Metod 2: Använda en värdelista med**  
Du kan använda en värdelista för numeriska data.  Överväg att aspekten intervall för en `listPrice` -fält ska renderas på följande sätt:

  ![Exempel värdelistan][5]

Använd en värdelista om du vill ange ett intervall för aspekten som det i föregående skärmbild:

    facet=listPrice,values:10|25|100|500|1000|2500

Varje område bygger använder 0 som en startpunkt, ett värde från listan som en slutpunkt och beskärs sedan tidigare intervallets skapa diskreta intervall. Azure Search gör dessa saker som en del av fasetterad navigeringen. Du behöver inte skriva kod för att strukturera varje intervall.

### <a name="build-a-filter-for-a-range"></a>Skapa ett filter för ett intervall
Du kan använda för att filtrera dokument baserat på ett intervall som du väljer den `"ge"` och `"lt"` filtrera operatorer i ett tvådelat uttryck som definierar slutpunkter för intervallet. Om du väljer intervallet 10-25 för till exempel en `listPrice` fältet filtret skulle bli `$filter=listPrice ge 10 and listPrice lt 25`. I exempelkoden, filteruttrycket använder **priceFrom** och **priceTo** parametrar för att konfigurera slutpunkter. 

  ![Frågan för ett intervall med värden][6]

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filtrera baserat på avstånd
Vanliga för att se filtrerar som hjälper du väljer en butik, restaurang eller mål baserat på dess närhet till din aktuella plats. Den här typen av filter kan se ut så fasetterad navigering, men det är bara ett filter. Vi anges det här gäller de av er som specifikt söker efter implementering råd om det specifika design problemet.

Det finns två geospatiala funktioner i Azure Search **geo.distance** och **geo.intersects**.

* Den **geo.distance** funktionen returnerar avståndet i kilometer, mellan två punkter. En punkt är ett fält och andra är en konstant skickas som en del av filtret. 
* Den **geo.intersects** funktionen returnerar true om en viss punkten befinner sig inom en viss polygon. Det är ett fält och från en polygon har angetts som en konstant lista över koordinater som skickas som en del av filtret.

Du kan hitta filter exemplen i [syntaxen för OData-uttryck (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Prova demonstrationen
Azure Search-jobbet Portal Demo innehåller exempel som refereras till i den här artikeln.

-   Se och testa den fungerande demon online på [Azure Search-jobbet Portal Demo](http://azjobsdemo.azurewebsites.net/).

-   Hämta koden från den [lagringsplatsen för Azure-exempel på GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

Titta på URL: en för ändringar i Frågekonstruktionen när du arbetar med sökresultaten. Det här programmet sker att lägga till facets till URI: N som var och en.

1. Om du vill använda funktionen mappning av demoappen få en Bing Maps-nyckel från den [Bing Maps Dev Center](https://www.bingmapsportal.com/). Klistra in den över den befintliga nyckeln i den `index.cshtml` sidan. Den `BingApiKey` i den `Web.config` filen inte används. 

2. Kör appen. Valfritt rundtur eller stänga dialogrutan.
   
3. Ange en sökterm, till exempel ”analytiker”, och klicka på sökikonen. Frågan körs snabbt.
   
   En fasetterad navigeringsstruktur returneras också med sökresultaten. I sökresultatsidan inkluderar fasetterad navigeringsstrukturen för varje aspekten resultat. Inga facets är markerad, så alla matchande resultat returneras.
   
   ![Sökresultat innan du väljer facets][11]

4. Klicka på en befattning, plats eller minsta lön. Facets var null för den första sökningen, men som de får på värden där objekt som matchar inte längre bort sökresultaten.
   
   ![Sökresultat när du har valt facets][12]

5. Om du vill ta bort fasetterad frågan så att du kan prova olika frågan beteenden, klickar du på den `[X]` efter de valda aspekter att rensa fasetterna.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Läs mer
Titta på [Azure Search ingående](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). Vid 45:25 finns det en demonstration på hur du implementerar facets.

För mer information om principer för fasetterad navigering rekommenderar vi följande länkar:

* [Utformning för fasetterad sökning](http://www.uie.com/articles/faceted_search/)
* [Designmönster: Fasetterad navigering](http://alistapart.com/article/design-patterns-faceted-navigation)


<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/azure-search-faceting-example.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png
[11]: ./media/search-faceted-navigation/faceted-search-before-facets.png
[12]: ./media/search-faceted-navigation/faceted-search-after-facets.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/ 
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: https://docs.microsoft.com/rest/api/searchservice/Search-Documents

