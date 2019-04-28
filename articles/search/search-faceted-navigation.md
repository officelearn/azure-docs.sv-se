---
title: Implementera aspektbaserad navigering i kategorihierarkin – Azure Search
description: Lägg till aspekten navigering i program som integreras med Azure Search, en söktjänst i molnet på Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 3b31e796b07bea8c11bccb3f2bb306a4279f2ca3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61291657"
---
# <a name="how-to-implement-faceted-navigation-in-azure-search"></a>Implementera aspektbaserad navigering i Azure Search
Aspektbaserad navigering är en filtreringsmekanism som tillhandahåller självriktad detaljgranska navigering i sökprogram. Termen ”aspektbaserad navigering” kan vara bekant, men du har förmodligen använt den tidigare. Som i följande exempel visas är aspektbaserad navigering helt enkelt de kategorier som används för att filtrera resultaten.

 ![Azure Search jobbet Portal Demo][1]

Aspektbaserad navigering är en annan startpunkt för att söka. Det är ett praktiskt alternativ till att skriva komplexa sökuttryck manuellt. Fasetter kan hjälpa dig att hitta det du letar efter, samtidigt som man säkerställer att du inte blir noll. Som utvecklare kan du exponera mest användbara sökvillkoren för att navigera din sökkorpus fasetter. I onlinebutiker program skapas aspektbaserad navigering ofta över varumärken, avdelningar (barnens skor), storlek, pris, popularitet och betyg. 

Implementera aspektbaserad navigering skiljer sig åt mellan sökteknik. I Azure Search byggs aspektbaserad navigering när en fråga körs med fält som du tidigare hänföras i ditt schema.

-   I de frågor som programmet skapar en fråga måste skicka *aspekten frågeparametrar* att hämta tillgängliga aspekten värden för den dokumentet resultatuppsättningen.

-   Ställa in att faktiskt trimma dokumentet resultatet, programmet måste också använda en `$filter` uttryck.

I din programutveckling, skriva kod som skapar frågor som utgör den största delen av arbetet. Många av de programfunktioner som du förväntar dig från aspektbaserad navigering tillhandahålls av tjänsten, inklusive inbyggt stöd för att definiera intervall och hämtar antalet för aspekten resultat. Tjänsten omfattar även rimliga standardvärden som hjälper dig att undvika svårhanterlig navigeringsstrukturer. 

## <a name="sample-code-and-demo"></a>Exempelkod och demo
Den här artikeln används en jobbet search-portalen som exempel. I exempel implementeras som ett ASP.NET MVC-program.

-   Se och testa den fungerande demonstrationen online på [Azure Search jobbet Portal Demo](http://azjobsdemo.azurewebsites.net/).

-   Ladda ned koden från den [lagringsplats för Azure-exempel på GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Kom igång
Om du inte har använt Sök utveckling, är det bästa sättet att tänka på aspektbaserad navigering att den visar möjligheterna för självriktad sökning. Det är en typ av nedåt sökupplevelsen och baserat på fördefinierade filter som används för att snabbt teknikområde sökresultat igenom peka och klicka-åtgärder. 

### <a name="interaction-model"></a>Interaktion modell

Sökupplevelsen för fasetterad navigering är iterativ, så Låt oss börja med att förstå den som en sekvens av frågor som Vik ut som svar på användaråtgärder.

Startpunkt är en sida för programmet som tillhandahåller aspektbaserad navigering, vanligtvis placeras Infästningspunkten. Aspektbaserad navigering är ofta en trädstruktur med kryssrutor för varje värde eller klickbara text. 

1. En fråga som skickas till Azure Search anger aspektbaserad navigeringsstruktur via en eller flera aspekten Frågeparametrar. Exempelvis kan frågan kan innehålla `facet=Rating`, kanske med en `:values` eller `:sort` möjlighet att ytterligare förfina presentationen.
2. Presentationslagret återger en söksida som tillhandahåller aspektbaserad navigering, med hjälp av fasetterna som anges på begäran.
3. Med en aspektbaserad navigeringsstruktur som innefattar klassificering kan du klicka på ”4” som anger att endast produkter med en klassificering på 4 eller senare ska visas. 
4. Ett svar skickar programmet en fråga som innehåller `$filter=Rating ge 4` 
5. Presentationslagret uppdaterar sidan som visar en minskad resultatmängd som innehåller bara de objekt som uppfyller de nya kriterierna (i det här fallet produkter som har bedömts 4 och senare).

Ett fasettvärde är en frågeparameter, men är inte detsamma som det med frågan indata. Den används aldrig som urvalskriterier i en fråga. Se i stället aspekten frågeparametrar som indata till navigeringsstruktur som kommer tillbaka i svaret. För varje aspekten frågeparameter som du anger, utvärderar Azure Search hur många dokument som finns i ofullständiga resultat för varje aspektvärdet.

Observera den `$filter` i steg 4. Filtret är en viktig aspekt av aspektbaserad navigering. Även om fasetter och filter är fristående i API: et, måste båda för att leverera upplevelse som du avser. 

### <a name="app-design-pattern"></a>Designmönster för App

I programkoden är mönstret att använda aspekten frågeparametrar för att returnera aspektbaserad navigeringsstruktur tillsammans med aspekten resultat, plus ett $filter-uttryck.  Filteruttrycket hanterar click-händelse på aspektvärdet. Se den `$filter` uttryck som koden bakom faktiska hur av sökresultaten returneras till presentationslagret. Får ett fasettvärde färger, klicka på röd färg implementeras via en `$filter` som väljer bara de objekt som har en färg av rött. 

### <a name="query-basics"></a>Grunderna i frågan

I Azure Search, en begäran har angetts via en eller flera frågeparametrar (se [söka efter dokument](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) en beskrivning av var och en). Ingen av frågeparametrarna krävs, men du måste ha minst en för en fråga ska vara giltigt.

Noggrannhet på millisekunder, känna möjlighet att filtrera bort irrelevanta träffar uppnås via en eller båda av dessa uttryck:

-   **Search =**  
    Värdet för den här parametern utgör search-uttryck. Det kan vara en enda typ av text eller en komplex sökning-uttryck som innehåller flera villkor och operatörer. På servern används ett sökuttryck för fulltextsökning, fråga sökbara fält i indexet för matchning av villkor, rangordnas ordning och returnerar resultaten. Om du ställer in `search` null fråga körning är över hela indexet (det vill säga `search=*`). I det här fallet, andra element i frågan, till exempel en `$filter` eller bedömningsprofil, är de främsta faktorerna som påverkar vilka dokument som returneras `($filter`) och i vilken ordning (`scoringProfile` eller `$orderby`).

-   **$filter =**  
    Ett filter är en kraftfull mekanism för att begränsa storleken på sökresultaten baserat på värdena i specifika dokumentattribut. En `$filter` är utvärderas först, följt av fasettering logik som genererar tillgängliga värden och motsvarande antal för varje värde

Komplexa sökuttryck försämra prestanda för frågan. Om möjligt, kan du använda väl filteruttryck för att öka precision och förbättra frågeprestanda.

För att bättre förstå hur ett filter lägger till mer precision, jämför en komplex sökuttryck till ett som innehåller ett filteruttryck:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Båda frågor är giltiga, men andra är överlägsen om du letar efter icke-motell med parkering i Seattle.
-   Den första frågan är beroende av dessa specifika ord som nämns eller inte nämns i strängfält som namn, beskrivning och andra fält som innehåller sökbara data.
-   Den andra frågan söker efter exakta matchningar på strukturerade data och förväntas vara mycket mer exakta.

I program som omfattar aspektbaserad navigering, se till att varje användaråtgärd över en aspektbaserad navigeringsstruktur åtföljs av en begränsa sökresultaten. Du kan begränsa resultaten genom att använda ett filteruttryck.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Skapa en app för aspektbaserad navigering
Du kan implementera aspektbaserad navigering med Azure Search i din programkod som bygger sökbegäran. Aspektbaserad navigering är beroende av element i ditt schema som du definierade tidigare.

Fördefinierade i sökningen indexet är den `Facetable [true|false]` index attributuppsättningen i markerade fält att aktivera eller inaktivera deras användning i en aspektbaserad navigeringsstruktur. Utan `"Facetable" = true`, ett fält kan inte användas i aspekten navigering.

Presentationslagret i koden innehåller användarupplevelsen. Det bör en lista över de komponenter som ingår i aspektbaserad navigering, till exempel etiketten, värden, kryssrutorna och antalet. Azure Search REST API är plattformsoberoende, så Använd vilka språk och plattformar som du vill. Viktigt är att inkludera UI-element som har stöd för inkrementell uppdatering med uppdaterade gränssnittstillstånd som varje ytterligare aspekten har valts. 

När en fråga körs programkoden skapar en begäran som innehåller `facet=[string]`, en parameter för förfrågan som innehåller fältet för att aspekten av. En fråga kan ha flera aspekter, till exempel `&facet=color&facet=category&facet=rating`, var och en avgränsade med ett et-tecken (&) tecken.

Programkoden måste också skapa en `$filter` uttryck för att hantera klickar du på händelser i aspektbaserad navigering. En `$filter` minskar sökresultat, med hjälp av aspektvärdet som filterkriterier.

Azure Search returnerar sökresultaten baserat på en eller flera termer som du anger, tillsammans med uppdateringar till struktur för aspektbaserad navigering. I Azure Search aspektbaserad navigering är en en-nivå-deklarationskonstruktion med aspekten värden, och räknar hur många resultat hittades för var och en.

I följande avsnitt ta vi en närmare titt på hur du skapar varje del.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Skapa indexet
Fasettering är aktiverat på basis av fält i indexet, via den här indexattribut: `"Facetable": true`.  
Alla fälttyper av som eventuellt kan användas i aspektbaserad navigering är `Facetable` som standard. Sådana typer av fält `Edm.String`, `Edm.DateTimeOffset`, och alla fälttyper av numeriska (i princip alla fälttyper är fasettbar utom `Edm.GeographyPoint`, som inte kan användas i aspektbaserad navigering). 

När du skapar ett index är bästa praxis för aspektbaserad navigering uttryckligen inaktivera fasettering för fält som ska aldrig användas som ett fasettvärde.  I synnerhet strängfält för singleton-värden, till exempel ett ID eller produkt-namn ska vara inställd på `"Facetable": false` att förhindra användningen oavsiktlig (och ineffektiv) i aspektbaserad navigering. Att stänga fasettering av där du inte behöver den gör att storleken på indexet små och ger vanligtvis bättre prestanda.

Följande är en del av schemat för jobbet Portal Demo-exempelapp, tas bort av vissa attribut för att minska storleken:

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

Som du ser i exemplet-schemat `Facetable` stängs av för strängfält som inte bör användas som aspekter, till exempel ID-värden. Att stänga fasettering av där du inte behöver den gör att storleken på indexet små och ger vanligtvis bättre prestanda.

> [!TIP]
> Ett bra tips är att inkludera den fullständiga uppsättningen indexattribut för varje fält. Även om `Facetable` är aktiverad som standard för nästan alla fält, ange avsiktligen har varje attribut kan hjälpa dig att tänka igenom effekterna av varje schema beslut. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Kontrollera data
Kvaliteten på dina data har en direkt inverkan på om aspektbaserad navigeringsstruktur materialiserar som du tror. Det påverkar också enkel konstruera filter för att minska resultatuppsättningen.

Om du vill aspekten av varumärke eller pris varje dokument som ska innehålla värden för *BrandName* och *ProductPrice* som är giltig, konsekvent och produktiva som ett filtreringsalternativ.

Här följer några påminnelser om vad du ska Skrubba för:

* För varje fält som du vill aspekten genom att fråga dig själv om det innehåller värden som är lämpliga som filter i självriktad sökning. Värdena bör vara korta, beskrivande och tillräckligt särskiljande att erbjuda ett tydligt val mellan konkurrerande alternativ.
* Felstavningar och nästan matchande värden. Om du aspekten för färg och fältvärden är Orange och Ornage (stavningsförslag) och ett fasettvärde baserat på färgfältet skulle hämta båda.
* Blandad fall text kan också orsaka oreda i aspektbaserad navigering med orange och Orange visas som två olika värden. 
* Enkel och plural versioner av samma värde kan resultera i en separat aspekten för var och en.

Eftersom du kan föreställa dig är noggrannhet i förbereda data en viktig aspekt av effektiva aspektbaserad navigering.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Skapa användargränssnittet
Arbeta från presentationslagret kan hjälpa dig att identifiera krav som annars kan missas och förstå vilka funktioner är mycket viktigt att sökupplevelsen.

När det gäller aspektbaserad navigering, din webb- eller sida visar struktur för aspektbaserad navigering, identifierar indata från användaren på sidan och infogar ändrade elementen. 

För webbprogram används AJAX ofta i presentationslagret eftersom du kan uppdatera inkrementella ändringar. Du kan också använda ASP.NET MVC eller annan visualisering plattform som kan ansluta till en Azure Search-tjänst via HTTP. Exempelprogrammet som hänvisas till den här artikeln – den **Azure Search jobbet Portal Demo** – råkar vara ASP.NET MVC-program.

I det här exemplet ingår aspektbaserad navigering i sidan med sökresultat. Följande exempel hämtas från den `index.cshtml` -filen för det här exempelprogrammet visar statisk HTML-struktur för aspektbaserad navigering sökningen resultatsida. Listan över fasetter skapats eller dynamiskt byggas om eftersom du skicka in ett sökord eller markera eller avmarkera ett fasettvärde.

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

Följande kodavsnitt från den `index.cshtml` sidan dynamiskt skapa HTML-koden för att visa den första aspekten befattning. Liknande funktioner skapa dynamiskt HTML för andra fasetterna. Varje aspekt (Facet) har en etikett och ett antal, som visar hur många objekt hittades för aspekten resultatet.

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
> Kom ihåg att lägga till en mekanism för att rensa fasetter när du utformar sökresultatsidan. Om du lägger till kryssrutorna kan du enkelt se Rensa filtren. Du kanske behöver ett brödsmula mönster eller en annan kreativa metod för andra layouter. Till exempel i exempelprogrammet jobbet Search-portalen, du kan klicka på den `[X]` efter en valda aspekten att rensa fasetten.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Frågan
Den kod som du skriver för att skapa frågor bör ange alla delar av en giltig fråga, inklusive sökuttryck, fasetter, filter, bedömning profiler – allt används för att formulera en begäran. I det här avsnittet utforska vi där fasetter passas in i en fråga och hur filter används med fasetterna för att leverera en minskad resultatuppsättning.

Observera att fasetter är integrerad i det här exempelprogrammet. Sökmiljön i jobbet Portal demon är utformad kring aspektbaserad navigering och filter. Framstående placeringen av aspektbaserad navigering på sidan visar dess betydelse. 

Ett exempel är ofta ett bra ställe att börja. Följande exempel hämtas från den `JobsSearch.cs` versioner som en begäran som skapar aspekten navigering baserat på företag rubrik, plats, bokföring och minsta lön-fil. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

En frågeparameter för aspekten har angetts till ett fält och beroende på datatypen, kan ytterligare parameteriseras via kommaavgränsad lista med `count:<integer>`, `sort:<>`, `interval:<integer>`, och `values:<list>`. En värdelista som stöds för numeriska data när du konfigurerar intervall. Se [söka efter dokument (Azure Search-API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) för användningsinformation.

Tillsammans med fasetter, bör begäran formulerat av ditt program också skapa filter för att begränsa uppsättningen kandidatdokument baserat på en aspekten värdemarkering. För en cykel butik aspektbaserad navigering innehåller ledtrådar på frågor som *vilka färger, tillverkare och typer av cyklar är tillgängliga?*. Filtrering får du svar på frågor som *vilka exakta cyklar är röda mountain cyklar i den här pris av intervallet?*. När du klickar på ”Red” för att indikera att endast Red produkter ska visas i nästa fråga som programmet skickar innehåller `$filter=Color eq ‘Red’`.

Följande kodavsnitt från den `JobsSearch.cs` sidan lägger till valda företag rubriken till filtret om du väljer ett värde från företag rubrik-aspekten.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Tips och råd

### <a name="indexing-tips"></a>Indexering tips
**Förbättra effektiviteten för index om du inte använder en sökruta**

Om programmet använder aspektbaserad navigering exklusivt (dvs, inga sökrutan) kan du markera fältet som `searchable=false`, `facetable=true` att producera ett komprimerat index. Dessutom kan uppstår indexera bara på hela aspekten värden, med inga orddelare eller indexering av delarna av en flera ords-värde.

**Ange vilka fält som kan användas som aspekter**

Kom ihåg att schemat för indexet avgör vilka fält som är tillgängliga för användning som ett fasettvärde. Om vi antar att ett fält är fasettbar anger frågan vilka fält som ska aspekten av. Fältet som du är fasettering innehåller de värden som visas under etiketten. 

De värden som visas under varje etikett hämtas från indexet. Exempel: om fältet aspekten är *färg*, värdena som är tillgängliga för ytterligare filtrering är värden för det fältet - röd, svart och så vidare.

Numeriska datatyper och datum/tid-värden endast kan du uttryckligen ange värden på fältet för aspekten (till exempel `facet=Rating,values:1|2|3|4|5`). En värdelista som tillåts för dessa fälttyper att förenkla uppdelning av aspekten resultat till sammanhängande intervall (antingen intervall baserat på numeriska värden eller tidsperioder). 

**Som standard kan du bara ha en nivå med aspektbaserad navigering** 

Enligt vad som anges, finns det inget direkt stöd för att kapsla fasetter i en hierarki. Aspektbaserad navigering i Azure Search stöder bara en nivå av filter som standard. Dock finns lösningar. Du kan koda en hierarkisk aspekten struktur i en `Collection(Edm.String)` peka per hierarki med en post. Den här lösningen är utanför omfattningen för den här artikeln. 

### <a name="querying-tips"></a>Fråga tips
**Validera fält**

Om du skapa en lista över fasetter dynamiskt baserat på ej betrodda användarindata, kontrollerar du att namnen på fälten aspektbaserad är giltiga. Eller escape-namn när du skapar URL: er genom att använda antingen `Uri.EscapeDataString()` i .NET, eller motsvarande i din plattform.

### <a name="filtering-tips"></a>Tips för filtrering
**Öka search precision med filter**

Använda filter. Om du använder bara sökuttryck som är separat, ordstamsigenkänning kan leda till ett dokument som ska returneras som inte har det exakta aspektvärdet i någon av dess fält.

**Öka prestandan för sökningen med filter**

Filter begränsa uppsättningen kandidatdokument för sökning och exkluderar dem från rangordning. Om du har ett stort antal dokument, ger en selektiv aspekten nedåt ofta dig bättre prestanda.
  
**Filtrera endast aspektbaserad fält**

I aspektbaserad nedåt vill du vanligtvis bara innehålla dokument som innehåller aspektvärdet i ett visst (aspektbaserad) fält inte någonstans mellan alla sökbara fält. Lägger till ett filter förstärker målfältet genom att dirigera tjänsten för att söka endast i fältet aspektbaserad för ett motsvarande värde.

**Trimma aspekten resultat med flera filter**

Aspekten resultatet är dokument hittades i sökresultatet som matchar ett fasettvärde-begrepp. I följande exempel i sökresultaten för *molnbaserad databehandling*, 254 objekt har också *interna specifikationen* som en innehållstyp. Objekt är inte nödvändigtvis ömsesidigt uteslutande. Om ett objekt uppfyller ett villkor för båda filtren, räknas det i var och en. Duplicering är möjligt när fasettering på `Collection(Edm.String)` fält, som ofta används för att implementera dokumentet Taggning.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

I allmänhet om du upptäcker att aspekten resultat är konsekvent för stora, rekommenderar vi att du lägga till fler filter om du vill ge användare fler alternativ för att begränsa sökningen.

### <a name="tips-about-result-count"></a>Tips om resultatantal

**Begränsa antalet objekt i navigeringen till aspekten**

Det finns en standardgräns med 10 värden för varje aspektbaserad fält i navigeringsträdet. Den här standardinställningen är meningsfullt för navigeringsstrukturer eftersom den bevarar listan med värden till en lämplig storlek. Du kan åsidosätta standardinställningen genom att tilldela ett värde som ska räknas.

* `&facet=city,count:5` Anger att endast de första fem städer som finns i det övre rangordnas resultat returneras som ett fasettvärde resultat. Överväg en exempelfråga med en sökterm ”flygplats” och 32 matchningar. Om frågan anger `&facet=city,count:5`, de första fem unika orter med mest dokument i sökresultaten som ingår i aspekten resultaten.

Observera skillnaden mellan aspekten resultat och sökresultat. Sökresultaten är alla dokument som matchar frågan. Aspekten resultatet är matchningar för varje aspektvärdet. I det här exemplet innehåller sökresultaten stadsnamn som inte ingår i listan över klassificering aspekten (5 i vårt exempel). Resultat som filtreras bort via aspektbaserad navigering blir då synligt när du tar bort fasetter eller välja andra fasetter förutom stad. 

> [!NOTE]
> Diskutera `count` när det finns fler än en typ kan vara förvirrande. Följande tabell ger en kort sammanfattning av hur termen används i Azure Search-API, exempelkod och dokumentation. 

* `@colorFacet.count`<br/>
  Du bör se en count-parameter på aspekten, används för att visa antalet resultat som aspekten i presentationen kod. Antal anger antalet dokument som matchar det aspekten ord eller intervall i aspekten resultaten.
* `&facet=City,count:12`<br/>
  Du kan ange antalet till ett värde i en aspekten i fråga.  Standardvärdet är 10, men du kan ställa in den högre eller lägre. Ange `count:12` hämtar upp 12 matchar i resultaten för aspekten av dokumentantal.
* "`@odata.count`"<br/>
  I svaret på frågan anger det här värdet antalet matchande objekt i sökresultatet. I genomsnitt den är större än summan av alla aspekten resultat i kombination, på grund av förekomsten av objekt som matchar söktermen, men har ingen aspekten matchar.

**Hämta antal i aspekten resultat**

När du lägger till ett filter till en aspektbaserad fråga kanske du vill behålla instruktionen aspekten (till exempel `facet=Rating&$filter=Rating ge 4`). Tekniskt sett aspekten = klassificering behövs inte, men att den returnerar antalet aspekten värden för klassificeringarna 4 och senare. Om du klickar på ”4” och frågan som innehåller ett filter för större eller lika med ”4”, är till exempel antalet returnerade för varje klassificering som är 4 och senare.  

**Kontrollera att du får exakta aspekten antal**

I vissa fall kanske du upptäcker att aspekten antalet inte matchar resultatuppsättningar (se [Aspektbaserad navigering i Azure Search (foruminlägg)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Aspekten antalet kan vara felaktigt på grund av arkitekturen för horisontell partitionering. Varje search-index har flera shards och varje shard rapporterar övre N fasetterna av dokumentantal som sedan kombineras till ett enskilt resultat. Om vissa fragment har många matchande värden, medan andra har färre, kanske att vissa aspekten värden saknas eller är under-räknas i resultaten.

Men det här beteendet kan ändra när som helst om du får det här beteendet i dag, du kan kringgå det genom inflating artificiellt antalet:\<number > till ett stort antal att framtvinga fullständig rapportering från varje shard. Om värdet för antal: är större än eller lika med antalet unika värden i fältet du garanterat får korrekta resultat. Dock när antalet dokument är högt, det finns en prestandaförsämring, så Använd det här alternativet sparsamt.

### <a name="user-interface-tips"></a>Användaren gränssnittet tips
**Lägga till etiketter för varje fält i aspekten navigering**

Etiketter definieras vanligen i HTML eller formuläret (`index.cshtml` i exempelprogrammet). Det finns inga API i Azure Search för aspekten navigering etiketter eller andra metadata.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filter baserat på ett intervall
Fasettering över intervall med värden är ett vanligt krav för search-program. Intervall som stöds för numeriska data och datum/tid-värden. Du kan läsa mer om varje metod i [söka efter dokument (Azure Search-API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

Azure Search förenklar intervallet konstruktion genom att tillhandahålla två metoder för databehandling ett intervall. För båda metoderna skapar Azure Search lämpliga områden med de indata som du har angett. Till exempel om du anger värdena för identitetsområde av 10 | 20 | 30, skapas automatiskt intervall på 0 – 10, 10-20, 20 – 30. Ditt program kan du ta bort alla intervall som är tomma. 

**Metod 1: Använd parametern intervall**  
Om du vill ange pris fasetter i $10 steg om du anger: `&facet=price,interval:10`

**Metod 2: Använd en värdelista**  
Du kan använda en värdelista för numeriska data.  Överväg att aspekten intervallet för en `listPrice` fält, renderas på följande sätt:

  ![Exemplet värdelistan][5]

Om du vill ange ett intervall för aspekten som i föregående skärmbild, använder du en värdelista:

    facet=listPrice,values:10|25|100|500|1000|2500

Varje intervall skapas med hjälp av 0 som en startpunkt, ett värde i listan som en slutpunkt och tas sedan bort tidigare intervallet till skapa diskreta intervall. Azure Search gör dessa saker som en del av aspektbaserad navigering. Du behöver inte skriva kod för att strukturera varje intervall.

### <a name="build-a-filter-for-a-range"></a>Skapa ett filter för ett intervall
Du kan använda för att filtrera dokument baserat på ett intervall som du väljer den `"ge"` och `"lt"` filtrera operatorer i ett tvådelat-uttryck som definierar slutpunkterna för intervallet. Exempel: Om du väljer intervallet 10-25 för en `listPrice` fältet, filtret är `$filter=listPrice ge 10 and listPrice lt 25`. I exempelkoden filteruttrycket använder **priceFrom** och **priceTo** parametrar för att konfigurera slutpunkter. 

  ![Fråga för ett intervall med värden][6]

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filtrera baserat på avstånd
Vanliga för att se filtrerar som hjälper dig att du väljer en butik, restaurang eller mål baserat på dess närhet till din aktuella plats. Den här typen av filter kan se ut aspektbaserad navigering, men det är bara ett filter. Vi nämna det här för dig som mer specifikt söker efter implementering råd om det specifika design-problemet.

Det finns två geospatiala funktioner i Azure Search **geo.distance** och **geo.intersects**.

* Den **geo.distance** funktionen returnerar avståndet i kilometer mellan två punkter. En plats är ett fält och andra är en konstant som skickas som en del av filtret. 
* Den **geo.intersects** funktionen returnerar true om en viss tidpunkt som ligger inom en viss polygon. Det är ett fält och polygonen har angetts som en konstant över koordinater som skickas som en del av filtret.

Du kan hitta filter exemplen i [OData-uttryckssyntax (Azure Search)](query-odata-filter-orderby-syntax.md).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Prova demonstrationen
Azure Search jobbet Portal Demo innehåller exempel som nämns i den här artikeln.

-   Se och testa den fungerande demonstrationen online på [Azure Search jobbet Portal Demo](https://azjobsdemo.azurewebsites.net/).

-   Ladda ned koden från den [lagringsplats för Azure-exempel på GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

Titta på URL: en för ändringar i frågekonstruktion när du arbetar med sökresultat. Det här programmet händer att lägga till fasetter till URI: N som var och en.

1. Om du vill använda funktionen mappning av demoappen hämtar en Bing Maps-nyckel från den [Bing Maps Dev Center](https://www.bingmapsportal.com/). Klistra in det över den befintliga nyckeln i den `index.cshtml` sidan. Den `BingApiKey` i den `Web.config` filen inte används. 

2. Kör appen. Valfritt rundtur eller stänga dialogrutan.
   
3. Ange en sökterm, till exempel ”analytiker”, och klicka på sökikonen. Frågan körs snabbt.
   
   En aspektbaserad navigeringsstruktur också returneras med sökresultaten. På sidan sökning resultatet innehåller aspektbaserad navigeringsstruktur antalet för varje aspekten resultat. Inga fasetter markeras, så alla matchande resultat returneras.
   
   ![Sökresultaten innan du väljer fasetter][11]

4. Klicka på en rubrik för företag, plats eller minsta lön. Fasetter var null för den första sökningen, men precis de med värden, tas bort av objekt som inte längre matchar sökresultatet.
   
   ![Sökresultat när du har valt fasetter][12]

5. Om du vill ta bort aspektbaserad frågan så att du kan prova olika fråga beteenden, klickar du på den `[X]` efter valda fasetterna att rensa fasetterna.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Läs mer
Titta på [djupdykning i Azure Search](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). Vid 45:25 finns det en demo om hur du implementerar fasetter.

För mer information om designprinciper för aspektbaserad navigering rekommenderar vi följande länkar:

* [Designa för Aspektbaserad sökning](http://www.uie.com/articles/faceted_search/)
* [Designmönster: Aspektbaserad navigering](https://alistapart.com/article/design-patterns-faceted-navigation)


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
[Design Patterns: Faceted Navigation]: https://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[https://www.odata.org/documentation/odata-version-2-0/overview/]: https://www.odata.org/documentation/odata-version-2-0/overview/ 
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: https://docs.microsoft.com/rest/api/searchservice/Search-Documents

