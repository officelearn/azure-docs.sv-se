---
title: Använd fullständig Lucene-frågesyntax
titleSuffix: Azure Cognitive Search
description: Lucene-frågesyntax för fuzzy search, närhets sökning, term förstärkning, sökning efter reguljära uttryck och jokertecken i en Azure Kognitiv sökning-tjänst.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: ae4dd8b82e40b46da52a1b1f396569fda1dfea2b
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694634"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Använd den fullständiga Söksyntaxen för Lucene (avancerade frågor i Azure Kognitiv sökning)

När du konstruerar frågor för Azure Kognitiv sökning kan du ersätta den [enkla standard fråge parsern](query-simple-syntax.md) med en mer mycket [Lucene-fråga parser i Azure kognitiv sökning](query-lucene-syntax.md) för att formulera specialiserade och avancerade fråge definitioner. 

Lucene-parsern stöder komplexa fråge konstruktioner, t. ex. frågor med fält omfång, fuzzy search, infix och suffix med jokertecken, närhets sökning, term förstärkning och sökning efter reguljära uttryck. Den extra kraften ger ytterligare bearbetnings krav så att du kan förvänta dig en något längre körnings tid. I den här artikeln kan du gå igenom exempel som demonstrerar frågor som är tillgängliga när du använder fullständig syntax.

> [!Note]
> Många av de specialiserade fråge konstruktioner som aktive ras med fullständig Lucene-frågesyntax är inte [text-analyserad](search-lucene-query-architecture.md#stage-2-lexical-analysis), vilket kan vara överraskande om du förväntar dig eller lemmatisering. Lexikalisk analys utförs endast på fullständiga villkor (en term fråga eller en fras fråga). Frågetyper med ofullständiga termer (prefixlängd, jokertecken, regex-fråga, fuzzy-fråga) läggs direkt till i frågans träd, vilket kringgår analys steget. Den enda omvandlingen som utförs på ofullständiga sökord är lowercasing. 
>

## <a name="formulate-requests-in-postman"></a>Formulera förfrågningar i Postman

I följande exempel används ett sökindex för NYC-jobb som består av jobb som är tillgängliga baserat på en data uppsättning som tillhandahålls av Göteborg från [New York-data](https://opendata.cityofnewyork.us/) initiativ. Dessa data ska inte betraktas som aktuella eller fullständiga. Indexet finns på en sandbox-tjänst från Microsoft, vilket innebär att du inte behöver en Azure-prenumeration eller Azure-Kognitiv sökning för att testa dessa frågor.

Vad du behöver är Postman eller ett motsvarande verktyg för att skicka HTTP-begäran på GET. Mer information finns i [utforska med rest-klienter](search-get-started-rest.md).

### <a name="set-the-request-header"></a>Ange rubriken för begäran

1. I begär ande huvudet anger du **Content-Type** till `application/json` .

2. Lägg till en **API-nyckel** och ange den som den här strängen: `252044BE3886FE4A8E3BAA4F595114BB` . Det här är en frågeparameter för tjänsten sandbox search som är värd för NYC-jobb indexet.

När du har angett rubriken för begäran kan du återanvända det för alla frågor i den här artikeln och bara växla ut **Sök =** strängen. 

  :::image type="content" source="media/search-query-lucene-examples/postman-header.png" alt-text="Parametrar för set-begär ande huvud för Postman" border="false":::

### <a name="set-the-request-url"></a>Ange URL för begäran

Begäran är ett GET-kommando som paras ihop med en URL som innehåller Azure Kognitiv sökning-slutpunkten och Sök strängen.

  :::image type="content" source="media/search-query-lucene-examples/postman-basic-url-request-elements.png" alt-text="Hämta rubrik för Postman-förfrågan" border="false":::

URL-kompositionen har följande element:

+ **`https://azs-playground.search.windows.net/`** är en Sök tjänst i begränsat läge som underhålls av Azure Kognitiv sökning Development-teamet. 
+ **`indexes/nycjobs/`** är NYC-jobb indexet i index samlingen för den tjänsten. Både tjänst namnet och indexet krävs på begäran.
+ **`docs`** är dokument samlingen som innehåller allt sökbart innehåll. Den API-nyckel för frågor som anges i huvudet för begäran fungerar endast med Läs åtgärder som riktar dokument samlingen.
+ **`api-version=2020-06-30`** anger API-versionen, som är en obligatorisk parameter på varje begäran.
+ **`search=*`** är frågesträngen som i den inledande frågan är null och returnerar de första 50 resultaten (som standard).

## <a name="send-your-first-query"></a>Skicka din första fråga

Som ett verifierings steg klistrar du in följande begäran i Hämta och klickar på **Skicka**. Resultat returneras som utförliga JSON-dokument. Hela dokument returneras, vilket gör att du kan se alla fält och alla värden.

Klistra in URL: en i en REST-klient som ett verifierings steg och Visa dokument strukturen.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*
  ```

Frågesträngen, **`search=*`** , är en ospecificerad Sök motsvarighet till null eller tom sökning. Det är den enklaste sökningen du kan göra.

Du kan också lägga till **`$count=true`** i URL: en för att returnera ett antal dokument som matchar Sök villkoren. I en tom Sök sträng är detta alla dokument i indexet (cirka 2800 när det gäller NYC-jobb).

## <a name="how-to-invoke-full-lucene-parsing"></a>Så här anropar du fullständig Lucene-parsning

Lägg till **frågetyp = fullständig** för att anropa fullständig frågesyntax och åsidosätta standardvärdet för enkel frågesyntax. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&search=*
```

Alla exempel i den här artikeln anger parametern **frågetyp = fullständig** sökning, vilket indikerar att den fullständiga syntaxen hanteras av Lucene-frågeuttrycket. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Exempel 1: frågan är begränsad till en lista med fält

Det första exemplet är inte Lucene-Specific, men vi leder det till att introducera det första grundläggande frågeuttrycket: fält omfång. I det här exemplet omfångs hela frågan och svaret på bara några få angivna fält. Att lära dig att strukturera ett läsbart JSON-svar är viktigt när ditt verktyg är Postman eller Sök Utforskaren. 

För det kortfattat är frågan bara den *business_title* fältet och anger bara affärs titlar som returneras. **SearchFields** -parametern begränsar frågekörningen till bara fältet business_title och **väljer** anger vilka fält som ska ingå i svaret.

### <a name="search-expression"></a>Sök uttryck

```http
&search=*&searchFields=business_title&$select=business_title
```

Här är samma fråga med flera fält i en kommaavgränsad lista.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

Blank stegen efter kommatecken är valfria.

> [!Tip]
> När du använder REST API från program koden glömmer du inte till URL-koda parametrar som `$select` och `searchFields` .

### <a name="full-url"></a>Fullständig URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

Svar för den här frågan bör se ut ungefär som på följande skärm bild.

  ![Postman-exempel svar med Poäng](media/search-query-lucene-examples/postman-sample-results.png)

Du kanske har lagt märke till Sök poängen i svaret. Enhetliga resultat på 1 inträffar när det inte finns någon rang, antingen på grund av att sökningen inte var full texts ökning eller att inga kriterier tillämpades. För null-sökning utan villkor kommer rader tillbaka i valfri ordning. När du inkluderar faktiska Sök villkor visas Sök resultaten i meningsfulla värden.

## <a name="example-2-fielded-search"></a>Exempel 2: Fältad sökning

Fullständig Lucene-syntax har stöd för omfångs enskilda Sök uttryck till ett visst fält. Det här exemplet söker efter affärs titlar med termen Senior, men inte Junior.

### <a name="search-expression"></a>Sök uttryck

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Här är samma fråga med flera fält.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>Fullständig URL

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  :::image type="content" source="media/search-query-lucene-examples/intrafieldfilter.png" alt-text="Sök uttryck för exempel svar på Postman" border="false":::

Du kan definiera en fält Sök åtgärd med syntaxen **FieldName: searchExpression** , där Sök uttrycket kan vara ett enstaka ord eller en fras, eller ett mer komplext uttryck inom parentes, eventuellt med booleska operatorer. Några exempel är följande:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Se till att placera flera strängar inom citat tecken om du vill att båda strängarna ska utvärderas som en enda entitet, som i det här fallet söker efter två olika platser i `state` fältet. Se också till att operatorn är kapitaliserad som du ser med inte och och.

Fältet som anges i **FieldName: searchExpression** måste vara ett sökbart fält. Mer information om hur indexera attribut används i fält definitioner finns i [skapa index (Azure Kognitiv sökning REST API)](/rest/api/searchservice/create-index) .

> [!NOTE]
> I exemplet ovan behöver vi inte använda `searchFields` parametern eftersom varje del av frågan har ett angett fält namn explicit. Du kan dock fortfarande använda `searchFields` parametern om du vill köra en fråga där vissa delar är begränsade till ett visst fält och resten kan gälla för flera fält. Frågan skulle till exempel `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` matcha `senior NOT junior` enbart till `business_title` fältet, medan den matchar "extern" med `posting_type` fältet. Fält namnet som anges i **FieldName: searchExpression** har alltid företräde framför `searchFields` parametern, vilket är anledningen till att vi inte behöver inkludera `business_title` i `searchFields` parametern.

## <a name="example-3-fuzzy-search"></a>Exempel 3: suddig sökning

Fullständig Lucene-syntax stöder också fuzzy search, som matchar på villkor som har en liknande konstruktion. Om du vill göra en Fuzzy-sökning lägger du till Tilde- `~` symbolen i slutet av ett enstaka ord med en valfri parameter, ett värde mellan 0 och 2, som anger redigerings avståndet. Till exempel `blue~` eller `blue~1` skulle returnera blå, blå och limma.

### <a name="search-expression"></a>Sök uttryck

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

Fraser stöds inte direkt, men du kan ange en Fuzzy-matchning på komponent delar av en fras.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>Fullständig URL

Den här frågan söker efter jobb med termen "associera" (avsiktligt felstavat):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Fuzzy search-svar](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> Fuzzy-frågor [analyseras](search-lucene-query-architecture.md#stage-2-lexical-analysis)inte. Frågetyper med ofullständiga termer (prefixlängd, jokertecken, regex-fråga, fuzzy-fråga) läggs direkt till i frågans träd, vilket kringgår analys steget. Den enda omvandlingen som utförs på ofullständiga sökord är lowercasing.
>

## <a name="example-4-proximity-search"></a>Exempel 4: närhets sökning
Närhets sökningar används för att hitta termer som ligger nära varandra i ett dokument. Infoga en tilde ~-symbol i slutet av en fras följt av antalet ord som skapar närhets kanten. Till exempel "hotell flyg plats" ~ 5 hittar du villkoret hotell och flyg plats inom 5 ord för varandra i ett dokument.

### <a name="search-expression"></a>Sök uttryck

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>Fullständig URL

I den här frågan för jobb med termen "senior analytiker" där det är avgränsat med mer än ett ord:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  :::image type="content" source="media/search-query-lucene-examples/proximity-before.png" alt-text="Närhets fråga" border="false":::

Prova igen att ta bort orden mellan termen "senior analytiker". Observera att åtta dokument returneras för den här frågan i stället för 10 för föregående fråga.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Exempel 5: term förstärkning
Term förstärkning syftar på att rangordna ett dokument högre om det innehåller den ökade perioden, i förhållande till dokument som inte innehåller termen. Om du vill förstärka en term använder du cirkumflex, "^", symbol med en förstärknings faktor (ett tal) i slutet av den period som du söker. 

### <a name="full-urls"></a>Fullständiga URL: er

I den här frågan kan du söka efter jobb med termen *dator analytiker* och Observera att det inte finns några resultat med *både ord och* *analytiker*, men *dator* jobb är högst upp i resultaten.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  :::image type="content" source="media/search-query-lucene-examples/termboostingbefore.png" alt-text="Term förstärkning före" border="false":::

I frågan "efter" upprepar du sökningen, den här tiden ökar resultatet med termen *analytiker* över termen *dator* om båda orden inte finns. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
En mer lättläst version av ovanstående fråga är `search=business_title:computer analyst^2` . För en fungerande fråga `^2` kodas som `%5E2` , vilket är svårare att se.

  :::image type="content" source="media/search-query-lucene-examples/termboostingafter.png" alt-text="Term förstärkning efter" border="false":::

Term förstärkning skiljer sig från bedömnings profiler i dessa bedömnings profiler ökar vissa fält i stället för specifika villkor. I följande exempel kan du illustrera skillnaderna.

Överväg en bedömnings profil som höjer träffar i ett visst fält, till exempel **Genre** i musicstoreindex-exemplet. Termen förstärkning kan användas för att ytterligare öka vissa Sök villkor som är större än andra. Exempelvis ökar "Rock ^ 2 Electronic" dokument som innehåller Sök villkoren i fältet **Genre** högre än andra sökbara fält i indexet. Dessutom rangordnas dokument som innehåller Sök termen "Rock" högre än den andra Sök termen "elektronisk" som ett resultat av termen förstärknings värde (2).

När du anger faktor nivå, desto högre grad av öknings faktorn, är det mer relevant att ange villkoret i förhållande till andra Sök villkor. Som standard är förstärknings faktorn 1. Förstärknings faktorn måste vara positiv, men den kan vara mindre än 1 (till exempel 0,2).


## <a name="example-6-regex"></a>Exempel 6: regex

En sökning efter reguljära uttryck hittar en matchning baserat på innehållet mellan snedstreck "/", enligt beskrivningen i [klassen RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

### <a name="search-expression"></a>Sök uttryck

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>Fullständig URL

I den här frågan kan du söka efter jobb med antingen termen Senior eller Junior: `search=business_title:/(Sen|Jun)ior/` .

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  :::image type="content" source="media/search-query-lucene-examples/regex.png" alt-text="Regex-fråga" border="false":::

> [!Note]
> Regex-frågor [analyseras](./search-lucene-query-architecture.md#stage-2-lexical-analysis)inte. Den enda omvandlingen som utförs på ofullständiga sökord är lowercasing.
>

## <a name="example-7-wildcard-search"></a>Exempel 7: sökning med jokertecken
Du kan använda allmänt identifierad syntax för jokertecken för flera ( \* ) eller enkla (?)-tecknen. Observera att funktionen Lucene Query parser stöder användningen av dessa symboler med en enda term och inte en fras.

### <a name="search-expression"></a>Sök uttryck

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>Fullständig URL

I den här frågan kan du söka efter jobb som innehåller prefixet "PROG" som innehåller affärs titlar med termerna programmering och programmerare. Du kan inte använda * eller? symbol som det första tecknet i en sökning.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  :::image type="content" source="media/search-query-lucene-examples/wildcard.png" alt-text="Fråga med jokertecken" border="false":::

> [!Note]
> Jokertecken har inte [analyser](./search-lucene-query-architecture.md#stage-2-lexical-analysis)ATS. Den enda omvandlingen som utförs på ofullständiga sökord är lowercasing.
>

## <a name="next-steps"></a>Nästa steg
Försök att ange en Lucene-fråga i koden. Följande länkar förklarar hur du ställer in Sök frågor för både .NET och REST API. Länkarna använder den enkla standard syntaxen så att du måste använda det du har lärt dig i den här artikeln för att ange **frågetyp**.

* [Fråga ditt index med .NET SDK](./search-get-started-dotnet.md)
* [Fråga ditt index med hjälp av REST API](./search-get-started-powershell.md)

Ytterligare syntax-referens, fråga arkitektur och exempel finns i följande länkar:

+ [Exempel på enkla syntax](search-query-simple-examples.md)
+ [Så här fungerar fulltextsökning i Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Enkel frågesyntax](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Fullständig Lucene-frågesyntax](/rest/api/searchservice/lucene-query-syntax-in-azure-search)