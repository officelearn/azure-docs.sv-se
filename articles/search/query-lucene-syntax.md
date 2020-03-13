---
title: Lucene-frågesyntaxen
titleSuffix: Azure Cognitive Search
description: Referens för fullständig Lucene-frågesyntax som används i Azure Kognitiv sökning för jokertecken, fuzzy search, RegEx och andra avancerade fråge konstruktioner.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: d35c96657f48905f37c9ebe246d81ebb9545cf27
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283139"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Lucene-frågesyntax i Azure Kognitiv sökning

Du kan skriva frågor mot Azure-Kognitiv sökning baserat på den avancerade Lucene-syntaxen för att [köra frågor](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) för särskilda fråge formulär: jokertecken, fuzzy search, närhets sökning, reguljära uttryck är några exempel. En stor del av den här syntaxen för en Lucene-fråga [implementeras intakt i azure kognitiv sökning](search-lucene-query-architecture.md), med undantag för *intervalls ökningar* som är konstruerade i Azure kognitiv sökning genom `$filter` uttryck. 

> [!NOTE]
> Den fullständiga Lucene-syntaxen används för frågeuttryck som skickas i **Sök** -parametern i [sökdokument](https://docs.microsoft.com/rest/api/searchservice/search-documents) -API: t, inte att förväxlas med [OData-syntaxen](query-odata-filter-orderby-syntax.md) som används för parametern [$filter](search-filters.md) för detta API. Dessa olika syntaxer har sina egna regler för att skapa frågor, undantags strängar och så vidare.

## <a name="how-to-invoke-full-parsing"></a>Så här anropar du fullständig parsning

Ange den `queryType` Sök parametern för att ange vilken parser som ska användas. Giltiga värden är `simple|full`, med `simple` som standard och `full` för Lucene. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Exempel som visar fullständig syntax

I följande exempel hittar du dokument i indexet med hjälp av Lucene-frågesyntaxen, som är tydlig i `queryType=full`-parametern. Den här frågan returnerar hotell där fältet Category innehåller termen "budget" och alla sökbara fält som innehåller frasen "nyligen renovated". Dokument som innehåller frasen "nyligen renovated" rangordnas högre upp till följd av termen förstärknings värde (3).  

Parametern `searchMode=all` är relevant i det här exemplet. När operatörer finns i frågan bör du vanligt vis ange `searchMode=all` för att säkerställa att *alla* villkor matchas.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2019-05-06&querytype=full
```

 Du kan också använda POST:  

```
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

Fler exempel finns i exempel på Lucene-frågesyntax [för att skapa frågor i Azure kognitiv sökning](search-query-lucene-examples.md). Mer information om hur du anger fullständig förfrågan om frågeparametrar finns i [Sök efter &#40;dokument Azure-&#41;kognitiv sökning REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  Azure Kognitiv sökning stöder också [enkel frågesyntax](query-simple-syntax.md), ett enkelt och robust frågespråk som kan användas för enkel nyckelords sökning.  

##  <a name="bkmk_syntax"></a>Grundläggande syntax  
 Följande grundläggande syntax gäller för alla frågor som använder Lucene-syntaxen.  

### <a name="operator-evaluation-in-context"></a>Utvärdering av operator i kontext

Placering avgör om en symbol tolkas som en operator eller bara ett tecken i en sträng.

I till exempel fullständig syntax för Lucene används tilde (~) för både fuzzy search och närhets sökning. Vid placering efter en citerad fras, ~ aktiverar närhets sökning. När den placeras i slutet av en term, så anropar ~ Sök funktionen.

Under en period, till exempel "Business ~ analytiker", utvärderas inte det som en operator. I detta fall antar vi att frågan är en term-eller fras fråga. [full texts ökning](search-lucene-query-architecture.md) med en [lexikalisk analys](search-lucene-query-architecture.md#stage-2-lexical-analysis) tar bort den ~ och bryter termen "Business ~ analytiker" i två: Business eller analytiker.

Exemplet ovan är tilde (~), men samma princip gäller för alla operatorer.

### <a name="escaping-special-characters"></a>Hoppar över specialtecken

 Specialtecken måste vara undantagna för att kunna användas som en del av Sök texten. Du kan kringgå dem genom att åtgärda dem med omvänt snedstreck (\\). Specialtecken som behöver undantas är följande:  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Om du till exempel vill kringgå ett jokertecken använder \\\*.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Koda osäkra och reserverade tecken i URL: er

 Kontrol lera att alla osäkra och reserverade tecken är kodade i en URL. Till exempel är ' # ' ett osäkert värde eftersom det är ett fragement/ankare-ID i en URL. Specialtecknet måste vara kodad till `%23` om det används i en URL. "&" och "=" är exempel på reserverade tecken när de begränsar parametrar och anger värden i Azure Kognitiv sökning. Mer information finns i [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) .

 Osäkra tecken är ``" ` < > # % { } | \ ^ ~ [ ]``. Reserverade tecken är `; / ? : @ = + &`.

### <a name="precedence-operators-grouping-and-field-grouping"></a>Prioritets operatorer: gruppering och fält gruppering  
 Du kan använda parenteser för att skapa under frågor, inklusive operatorer inom den parentetiska instruktionen. `motel+(wifi||luxury)` kan till exempel söka efter dokument som innehåller termen "Motel" och antingen "WiFi" eller "lyxen" (eller båda).

Fält grupperingen liknar varandra men omfångerar grupperingen till ett enda fält. `hotelAmenities:(gym+(wifi||pool))` söker till exempel i fältet "hotelAmenities" för "gymmet" och "WiFi", eller "gymmet" och "pool".  

### <a name="searchmode-parameter-considerations"></a>SearchMode parameter överväganden  
 Effekten av `searchMode` på frågor, enligt beskrivningen i [enkel frågesyntax i Azure kognitiv sökning](query-simple-syntax.md), gäller även för Lucene-frågesyntaxen. Det betyder att `searchMode` tillsammans med icke-operatörer kan leda till att frågan kommer att bli ovanlig om du inte är klar med konsekvenserna av hur du anger parametern. Om du behåller standardvärdet, `searchMode=any`och använder en NOT-Operator, beräknas åtgärden som en-eller-åtgärd, så att "New York" inte "Seattle" returnerar alla städer som inte är Seattle.  

##  <a name="bkmk_boolean"></a>Booleska operatorer (och, eller, inte) 
 Ange alltid text booleska operatorer (och, eller, inte) med versaler.  

### <a name="or-operator-or-or-"></a>ELLER operatör `OR` eller `||`

Operatorn OR är ett lodrätt streck eller ett vertikalstreck. Exempel: `wifi || luxury` kommer att söka efter dokument som innehåller antingen "WiFi" eller "lyxen" eller både och. Eftersom eller är standard operatorn, kan du också lämna ut den, så att `wifi luxury` motsvarar `wifi || luxuery`.

### <a name="and-operator-and--or-"></a>Operatorn `AND`, `&&` eller `+`

Operatorn och är ett et-tecken eller ett plus tecken. Exempel: `wifi && luxury` kommer att söka efter dokument som innehåller både "WiFi" och "lyxen". Plus tecknet (+) används för obligatoriska villkor. `+wifi +luxury` till exempel anges att båda termerna måste finnas någonstans i fältet i ett enda dokument.


### <a name="not-operator-not--or--"></a>NOT operator `NOT`, `!` eller `-`

Operatorn NOT är ett utrops tecken eller minus tecknet. Exempel: `wifi !luxury` söker efter dokument som har "WiFi"-termen och/eller inte har "lyxen". Alternativet `searchMode` styr om en term med operatorn NOT är ANDed eller ORed med andra termer i frågan om ingen + eller | | Operator. Kom ihåg att `searchMode` kan ställas in på antingen `any`(standard) eller `all`.

Om du använder `searchMode=any` ökas åter kallelsen av frågor genom att inkludera fler resultat, och som standard tolkas det som "eller inte". `wifi -luxury` kommer till exempel att matcha dokument som innehåller termen *WiFi* eller de som inte innehåller termen *lyxen.*

Att använda `searchMode=all` ökar precisionen för frågor genom att ta med färre resultat och tolkas som standard som "och inte". `wifi -luxury` kommer till exempel att matcha dokument som innehåller termen `wifi` och som inte innehåller termen `luxury`. Detta är utan tvekan ett mer intuitivt beteende för-operatorn. Därför bör du överväga att välja `searchMode=all` över `searchMode=any` om du vill optimera sökningar efter precision i stället för att återkalla *och* användarna ofta använder `-` operatören i sökningar.

##  <a name="bkmk_querysizelimits"></a>Begränsningar för frågans storlek  
 Det finns en gräns för hur många frågor du kan skicka till Azure Kognitiv sökning. Mer specifikt kan du ha högst 1024-satser (uttryck avgränsade med och, eller, och så vidare). Det finns också en gräns på ungefär 32 KB på storleken på en enskild term i en fråga. Om programmet genererar Sök frågor program mässigt rekommenderar vi att du utformar det på ett sådant sätt att det inte genererar frågor om obegränsad storlek.  

##  <a name="bkmk_searchscoreforwildcardandregexqueries"></a>Poängsättnings-och regex-frågor
 Azure Kognitiv sökning använder frekvens-baserad poängsättning ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) för text frågor. För jokertecken och regexfrågor där termernas omfattning kan vara breda, ignoreras dock frekvens faktorn för att förhindra att rankningen prioriteras mot matchningar från rarer villkor. Alla matchningar behandlas lika för jokertecken och regex-sökningar.

##  <a name="bkmk_fields"></a>Sökning efter fält  
Du kan definiera en fält Sök åtgärd med `fieldName:searchExpression` syntax, där Sök uttrycket kan vara ett enstaka ord eller en fras, eller ett mer komplext uttryck inom parentes, eventuellt med booleska operatorer. Några exempel är följande:  

- Genre: inte historik för Jazz  

- artister:("" Maria Davis "" John Coltrane ")

Se till att placera flera strängar inom citat tecken om du vill att båda strängarna ska utvärderas som en enda entitet, i det här fallet söker du efter två distinkta artister i fältet `artists`.  

Fältet som anges i `fieldName:searchExpression` måste vara ett `searchable`-fält.  Se [skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index) för information om hur index-attribut används i fält definitioner.  

> [!NOTE]
> När du använder ett fält med sökuttryck behöver du inte använda parametern `searchFields` eftersom varje fält som är angivet i Sök uttrycket har ett explicit angivet fält namn. Du kan dock fortfarande använda `searchFields` parameter om du vill köra en fråga där vissa delar är begränsade till ett visst fält och resten kan gälla för flera fält. Frågan `search=genre:jazz NOT history&searchFields=description` skulle till exempel matcha `jazz` endast till fältet `genre`, medan den matchar `NOT history` med fältet `description`. Det fält namn som anges i `fieldName:searchExpression` alltid prioriteras över `searchFields`-parametern, vilket är anledningen till att vi inte behöver inkludera `genre` i `searchFields`-parametern.

##  <a name="bkmk_fuzzy"></a>Fuzzy-sökning  
 En Fuzzy-sökning hittar matchningar i termer som har en liknande konstruktion. Per [Lucene-dokumentation](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)är Fuzzy-sökningar baserade på [Damerau-levenshtein avstånd](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance). Fuzzy-sökningar kan expandera en term upp till det högsta antalet 50 villkor som uppfyller avstånds kriterierna. 

 Om du vill göra en Fuzzy-sökning använder du Tilde-symbolen "~" i slutet av ett enstaka ord med en valfri parameter, ett tal mellan 0 och 2 (standard) som anger redigerings avståndet. Till exempel "blå ~" eller "blå ~ 1" returnerar "blått", "blått" och "lim".

 En Fuzzy-sökning kan bara tillämpas på termer, inte fraser, men du kan lägga till Tilde till varje term individuellt i ett namn eller en fras i flera delar. Exempel: "Unviersty ~ ~" Wshington ~ "skulle matcha" University of Washington ".
 

##  <a name="bkmk_proximity"></a>Närhets sökning  
 Närhets sökningar används för att hitta termer som ligger nära varandra i ett dokument. Infoga en tilde ~-symbol i slutet av en fras följt av antalet ord som skapar närhets kanten. `"hotel airport"~5` hittar till exempel villkoren "hotell" och "flyg plats" inom 5 ord för varandra i ett dokument.  


##  <a name="bkmk_termboost"></a>Term förstärkning  
 Term förstärkning syftar på att rangordna ett dokument högre om det innehåller den ökade perioden, i förhållande till dokument som inte innehåller termen. Detta skiljer sig från bedömnings profiler i dessa bedömnings profiler och ökar vissa fält i stället för specifika villkor.  

I följande exempel kan du illustrera skillnaderna. Anta att det finns en bedömnings profil som höjer matchningar i ett visst fält, t. ex. *Genre* i [musicstoreindex-exemplet](index-add-scoring-profiles.md#bkmk_ex). Termen förstärkning kan användas för att ytterligare öka vissa Sök villkor som är större än andra. `rock^2 electronic` höjer till exempel dokument som innehåller Sök villkoren i fältet Genre högre än andra sökbara fält i indexet. Dessutom rangordnas dokument som innehåller Sök termen *rock* högre än den andra Sök termen *elektroniskt* som ett resultat av termen förstärknings värde (2).  

 Om du vill förstärka en term använder du cirkumflex, "^", symbol med en förstärknings faktor (ett tal) i slutet av den period som du söker. Du kan också öka fraser. Ju högre förstärknings faktor, desto mer relevant är termen i förhållande till andra Sök villkor. Som standard är förstärknings faktorn 1. Förstärknings faktorn måste vara positiv, men den kan vara mindre än 1 (till exempel 0,20).  

##  <a name="bkmk_regex"></a>Sökning efter reguljära uttryck  
 En sökning efter reguljära uttryck hittar en matchning baserat på innehållet mellan snedstreck "/", enligt beskrivningen i [klassen RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).  

 Om du till exempel vill hitta dokument som innehåller "Motel" eller "hotell", anger du `/[mh]otel/`.  Sökningar i reguljära uttryck matchas mot enstaka ord.   

##  <a name="bkmk_wildcard"></a>Sökning med jokertecken  
 Du kan använda allmänt identifierad syntax för flera (*) eller enkla (?) Character-jokertecken. Observera att funktionen Lucene Query parser stöder användningen av dessa symboler med en enda term och inte en fras.  

 Om du till exempel vill hitta dokument som innehåller orden med prefixet "anmärkning", till exempel "notebook" eller "Anteckningar", anger du "Note *".  

> [!NOTE]  
>  Du kan inte använda * eller? symbol som det första tecknet i en sökning.  
>  Ingen text analys utförs för Sök frågor med jokertecken. Vid tidpunkten för frågor jämförs jokertecken med de analyserade villkoren i Sök indexet och expanderas.

## <a name="see-also"></a>Se även  

+ [Sök dokument](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [OData-uttrycks syntax för filter och sortering](query-odata-filter-orderby-syntax.md)   
+ [Enkel frågesyntax i Azure Kognitiv sökning](query-simple-syntax.md)   
