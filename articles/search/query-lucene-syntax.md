---
title: Lucene-frågesyntax – Azure Search
description: Referens för fullständig Lucene-syntaxen, som används med Azure Search.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/31/2019
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: 59362b28390556f12cce8813635894c9f06b9a20
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008477"
---
# <a name="lucene-query-syntax-in-azure-search"></a>Lucene-frågesyntax i Azure Search
Du kan skriva frågor mot Azure Search baserat på omfattande [frågeparser (Lucene)](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) syntaxen för specialiserade fråga formulär: jokertecken, fuzzy-sökning, närhetssökning, reguljära uttryck är några exempel. Mycket av frågeparser (Lucene)-syntax är [intakta implementeras i Azure Search](search-lucene-query-architecture.md), med undantag för *intervall sökningar* som skapas i Azure Search via `$filter` uttryck. 

## <a name="how-to-invoke-full-parsing"></a>Hur du anropar fullständig parsning

Ange den `queryType` sökparametern om du vill ange vilka parsern för att använda. Giltiga värden är `simple|full`, med `simple` som standard, och `full` för Lucene. 

<a name="bkmk_example"></a> 

## <a name="example-showing-full-syntax"></a>Exempel som visar fullständig syntax

I följande exempel söker efter dokument i indexet med hjälp av den Lucene-frågesyntaxen, tydligt i den `queryType=full` parametern. Den här frågan returnerar hotels där kategorifältet innehåller termen ”budget” och alla sökbara fält som innehåller frasen ”nyligen renoverade”. Dokument som innehåller frasen ”nyligen renoverade” rankas högre till följd av termen boost värdet (3).  

Den `searchMode=all` parametern är relevant i det här exemplet. När operatörer på frågan, bör du vanligtvis ange `searchMode=all` att se till att *alla* kriterier matchas.

```  
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2015-02-28&querytype=full  
```  

 Du kan också använda INLÄGG:  

```  
POST /indexes/hotels/docs/search?api-version=2015-02-28  
{  
  "search": "category:budget AND \"recently renovated\"^3",  
  "queryType": "full",  
  "searchMode": "all"  
}  
```  

Ytterligare exempel finns i [exempel på Lucene-fråga för att skapa frågor i Azure Search](search-query-lucene-examples.md). Mer information om hur du anger den fullständiga tillfällig frågeparametrarnas finns i [söka efter dokument &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  Azure Search stöder också [enkla frågesyntaxen](query-simple-syntax.md), enkla och robusta frågespråket som kan användas för enkel nyckelordssökning.  


##  <a name="bkmk_fields"></a> Fältbegränsade frågor  
 Du kan ange en `fieldname:searchterm` konstruktion att definiera en fielded frågeåtgärden där fältet är ett enstaka ord och söktermen är också ett enstaka ord eller en fras, eventuellt med booleska operatorer. Följande är några exempel:  

-   genre: jazz inte historiken  

-   artists:("Miles Davis" "John Coltrane")

 Se till att placera flera strängar inom citattecken om du vill att båda strängar som ska utvärderas som en enda enhet, i det här fallet söker efter två distinkta konstnärer i den `artists` fält.  

 Fält som anges i `fieldname:searchterm` måste vara en `searchable` fält.  Se [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) mer information om hur indexattribut används i fältdefinitioner.  

##  <a name="bkmk_fuzzy"></a> Fuzzy-sökning  
 En fuzzy-sökning söker efter matchningar i termer som har en liknande konstruktion. Per [Lucene dokumentation](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), fuzzy sökningar baseras på [Damerau Levenshtein avståndet](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance).  

 Om du vill göra en fuzzy-sökning, använder du tilde ”~” symbolen i slutet av ett enstaka ord med en valfri parameter, ett tal mellan 0 och 2 (standard), som anger avståndet redigera. Exempelvis ”blå ~” eller ”blå ~ 1” returneras ”blue”, ”blues” och ”sammanlänka”.

 Fuzzy-sökning kan endast tillämpas på villkor, inte fraser. Fuzzy sökningar kan expandera en term upp till högst 50 termer som uppfyller villkoren för avståndet.

##  <a name="bkmk_proximity"></a> närhetssökning  
 Närhet sökningar används för att hitta villkor som är nära varandra i ett dokument. Infoga en tilde ”~” tecken i slutet av en fras följt av antalet ord som skapar närhet gränsen. Till exempel `"hotel airport"~5` hittar villkoren ”hotell” och ”flygplats” inom 5 ord från varandra i ett dokument.  


##  <a name="bkmk_termboost"></a> termförstärkning  
 Termförstärkning avser rangordning ett dokument som är högre om den innehåller förbättrat termen, i förhållande till dokument som inte innehåller termen. Detta skiljer sig från poängprofiler i att bedömningsprofiler öka vissa fält i stället för på specifika villkor.  

I följande exempel hjälper visar skillnaderna. Anta att det är det en bedömning profilen som förstärker matchar i ett visst fält, exempelvis *genre* i den [musicstoreindex exempel](index-add-scoring-profiles.md#bkmk_ex). Termförstärkning kan användas för att ytterligare höja vissa search villkor som är högre än andra. Till exempel `rock^2 electronic` förbättras dokument som innehåller sökvillkor i fältet genre som är högre än andra sökbara fält i indexet. Ytterligare dokument som innehåller söktermen *rock* kommer att rangordnas högre än andra söktermen *elektronisk* till följd av termen boost värdet (2).  

 För att öka en term använder du cirkumflex ”^”, symbol med en faktor boost (ett tal) i slutet av perioden som du söker. Du kan också öka fraser. Ju högre boost-faktor, desto viktigare blir termen i förhållande till andra söktermer. Som standard är faktorn boost 1. Även om faktorn boost måste vara positivt, kan det vara mindre än 1 (till exempel 0.20).  

##  <a name="bkmk_regex"></a> sökning med reguljära uttryck  
 En sökning med reguljära uttryck hittar en matchning baserat på innehållet mellan snedstreck ”/”, som beskrivs i den [klassen RegExp](https://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).  

 Exempel: Om du vill hitta dokument som innehåller ”motel” eller ”hotell” anger `/[mh]otel/`.  Reguljärt uttryck sökningar matchas mot enstaka ord.   

##  <a name="bkmk_wildcard"></a> sökning med jokertecken  
 Du kan använda allmänt erkända syntax för flera (*) eller enskild (?) tecken jokertecken. Observera den frågeparser (Lucene) stöder användning av dessa symboler med en enda term och inte en fras.  

 Till exempel vill hitta dokument som innehåller ord med prefixet ”kommentar”, till exempel ”dagbok” eller ”anteckningar”, ange ”Obs! *”.  

> [!NOTE]  
>  Du kan inte använda en * eller? symbol som det första tecknet i en sökning.  
>  Inga textanalys utförs på sökningar med jokertecken. När en fråga körs med jokertecken sökord jämförd med analyserade villkoren i sökindexet och expanderas.

##  <a name="bkmk_syntax"></a> Syntaxgrunderna  
 Följande syntaxgrunderna gäller för alla frågor som använder Lucene-syntax.  

### <a name="operator-evaluation-in-context"></a>Operatorn utvärdering i kontexten

Placeringen avgör om en symbol tolkas som en operatör eller bara ett annat tecken i en sträng.

I Lucene fullständig syntax, till exempel används tilde (~) för både fuzzy-sökning och närhetssökning. När placeras efter en angiven fras ~ anropar närhetssökning. När placeras i slutet av en term ~ anropar fuzzy-sökning.

Inom en period, till exempel ”företag ~ analytiker”, tecknet utvärderas inte som en operatör. I det här fallet, förutsatt att frågan är en term eller fras fråga [fulltextsökning](search-lucene-query-architecture.md) med [lexikal analys](search-lucene-query-architecture.md#stage-2-lexical-analysis) tar bort den ~ och delar upp termen ”företag ~ analytiker” i två: företag eller analytiker.

I exemplet ovan är tilde (~), men samma principer gäller för varje operator.

### <a name="escaping-special-characters"></a>Undantagstecken specialtecken

 Särskilda tecken måste undantas som ska användas som en del av söktexten. Du kan hoppa över dem genom dem med omvänt snedstreck (\\). Följande: specialtecken som ska undantas  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Till exempel om du vill lämna ett jokertecken, använda \\*.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Kodning osäkra och reserverade tecken i URL: er

 Kontrollera att alla osäkra och reserverade tecken kodas i en URL. Till exempel är ett osäkra tecken '#' eftersom det är ett fragement/ankar-ID i URL-adresser. Tecknet måste vara kodad till `%23` om används i en URL. ' &' och '=' är exempel på reserverade tecken som de avgränsa parametrar och ange värden i Azure Search. Se [RFC1738: Uniform Resource Locator (URL)](https://www.ietf.org/rfc/rfc1738.txt) för mer information.

 Osäkra tecken är ``" ` < > # % { } | \ ^ ~ [ ] ``. Reserverade tecken är `; / ? : @ = + &`.

### <a name="precedence-operators-grouping-and-field-grouping"></a>Prioritetsoperatorer: gruppering och fältet gruppering  
 Du kan använda parenteser för att skapa underfrågor, inklusive operatörer i parentetiska-instruktion. Till exempel `motel+(wifi||luxury)` söker efter dokument som innehåller termen ”motel” och ”wifi” eller ”Lyxig” (eller båda).

Fältet grupperingen är liknande men scope grupperingen till ett enda fält. Till exempel `hotelAmenities:(gym+(wifi||pool))` söker i fältet ”hotelAmenities” för ”gymmet” och ”wifi” eller ”gymmet” och ”pool”.  

### <a name="searchmode-parameter-considerations"></a>Överväganden för SearchMode-parametern  
 Effekten av `searchMode` på frågor, enligt beskrivningen i [enkel frågesyntax i Azure Search](query-simple-syntax.md), gäller även för Lucene-frågesyntax. Nämligen `searchMode` tillsammans med inte operatörer kan resultera i fråga resultat som kan vara annorlunda om du inte radera på konsekvenserna av hur du ställer in parametern. Om du behåller du standardvärdet `searchMode=any`, och Använd åtgärden för operatorn inte beräknas som en OR-åtgärd, så att ”New York” inte ”Seattle” returnerar alla städer som inte är Seattle.  

##  <a name="bkmk_boolean"></a> Booleska operatorer  
 Alltid ange text booleska operatorer (AND, OR, inte) i versaler.  

#### <a name="or-operator-or-or-"></a>OR-operator `OR` eller `||`

Operatorn eller är en lodrät stapel- eller vertikalstrecket. Till exempel: `wifi || luxury` söker efter dokument som innehåller ”wifi” eller ”Lyxig” eller bådadera. Eftersom eller operatorn standard tillsammans, du kan även lämna ut, så att `wifi luxury` motsvarar `wifi || luxuery`.

#### <a name="and-operator-and--or-"></a>OCH den valda operatorn `AND`, `&&` eller `+`

Operatorn och är ett et-tecken eller ett plustecken. Till exempel: `wifi && luxury` söker efter dokument som innehåller både ”wifi” och ”Lyxig”. Plus-tecknet (+) används för nödvändiga villkor. Till exempel `+wifi +luxury` föreskriver att båda termerna måste finnas någonstans i fältet i ett enskilt dokument.


#### <a name="not-operator-not--or--"></a>NOT-operator `NOT`, `!` eller `-`

Operatorn inte är ett utropstecken eller minustecknet. Till exempel: `wifi !luxury` ska söka efter dokument som innehåller ”wifi” term och/eller har inte ”Lyxig”. Den `searchMode` alternativet kontroller om en term med operatorn inte är and eller ORed med andra villkor i fråga om en + eller || operator. Kom ihåg att `searchMode` kan vara inställd på antingen `any`(standard) eller `all`.

Med hjälp av `searchMode=any` ökar återkallande av frågor genom att inkludera fler resultat och som standard – tolkas som ”eller inte”. Till exempel `wifi -luxury` ska matcha dokument som antingen innehåller termen *wifi* eller som inte innehåller termen *Lyxig.*

Med hjälp av `searchMode=all` ökar precisionen för frågor genom att inkludera färre resultat och som standard – tolkas som ”och inte”. Till exempel `wifi -luxury` kommer att matcha dokument som innehåller termen `wifi` och innehåller ingen termen `luxury`. Det är utan tvekan en mer intuitiv användning som gäller för - operatorn. Därför bör du välja `searchMode=all` över `searchMode=any` om du vill optimera söker efter precision i stället för återkallande *och* som ofta används för den `-` operator i sökningar.

##  <a name="bkmk_querysizelimits"></a> Storleksbegränsningar för fråga  
 Det finns en gräns för storleken på frågor som du kan skicka till Azure Search. Mer specifikt kan kan du ha högst 1024-satser (uttryck avgränsade med AND, OR, och så vidare). Det finns också en gräns på cirka 32 KB på storleken av alla enskilda villkoret i en fråga. Om ditt program genererar sökfrågor programmässigt, rekommenderar vi att du utformar den så att den inte genererar frågor med obegränsad storlek.  

##  <a name="bkmk_searchscoreforwildcardandregexqueries"></a> Bedömning av jokertecken och regex-frågor
 Använder Azure Search frekvensbaserad bedömning ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) för textfrågor. För jokertecken och regex frågor där omfattning villkor kan vara bred ignoreras dock faktorn frekvens för att förhindra rangordning från börvärdessignalerna mot matchningar från sällsynta villkor. Alla matchningar behandlas på samma sätt för jokertecken och regex sökningar.

## <a name="see-also"></a>Se också  

+ [Söka efter dokument](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [OData-uttryckssyntax för filter och sortering](query-odata-filter-orderby-syntax.md)   
+ [Enkel frågesyntax i Azure Search](query-simple-syntax.md)   
