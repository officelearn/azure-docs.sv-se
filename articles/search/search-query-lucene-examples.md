---
title: Använd fullständig Lucene-frågesyntax
titleSuffix: Azure Cognitive Search
description: Lucene-frågesyntax för fuzzy-sökning, närhetssökning, termboost, sökning i reguljära uttryck och jokerteckensökningar i en Azure Cognitive Search-tjänst.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3c54f864b5bd562fdc0a84b2903198704032b360
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998485"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Använd den "fullständiga" lucene-söksyntaxen (avancerade frågor i Azure Cognitive Search)

När du skapar frågor för Azure Cognitive Search kan du ersätta [standardspråkaren](query-simple-syntax.md) för enkla frågor med den mer expansiva [Lucene Query-parsern i Azure Cognitive Search](query-lucene-syntax.md) för att formulera specialiserade och avancerade frågedefinitioner. 

Lucene-parsern stöder komplexa frågekonstruktioner, till exempel fältomfattade frågor, fuzzy- och prefixhcardsökning, närhetssökning, termförbsökning och sökning med reguljära uttryck. Den extra kraften levereras med ytterligare bearbetningskrav så du bör förvänta dig en något längre körningstid. I den här artikeln kan du gå igenom exempel som visar frågeåtgärder som är tillgängliga när du använder den fullständiga syntaxen.

> [!Note]
> Många av de specialiserade frågekonstruktioner som aktiveras via den fullständiga Lucene-frågesyntaxen är inte [textanalyserade](search-lucene-query-architecture.md#stage-2-lexical-analysis), vilket kan vara förvånande om du förväntar dig att hejda eller lemmatisering. Lexikal analys utförs endast på fullständiga villkor (en termfråga eller frasfråga). Frågetyper med ofullständiga termer (prefixfråga, jokerteckenfråga, regex-fråga, fuzzy query) läggs till direkt i frågeträdet och kringgår analysfasen. Den enda omvandlingen som utförs på ofullständiga frågetermer är att sänkacasingen. 
>

## <a name="formulate-requests-in-postman"></a>Formulera förfrågningar i Postman

Följande exempel utnyttjar ett NYC Jobs-sökindex som består av tillgängliga jobb baserat på en datauppsättning som tillhandahålls [av Initiativet City of New York OpenData.](https://opendata.cityofnewyork.us/) Dessa data bör inte betraktas som aktuella eller fullständiga. Indexet finns på en sandbox-tjänst som tillhandahålls av Microsoft, vilket innebär att du inte behöver en Azure-prenumeration eller Azure Cognitive Search för att prova dessa frågor.

Vad du behöver är Postman eller ett motsvarande verktyg för att utfärda HTTP-begäran på GET. Mer information finns i [Utforska med REST-klienter](search-get-started-postman.md).

### <a name="set-the-request-header"></a>Ange förfrådehuvudet

1. Ange **Content-Type** till `application/json`i begäranden .

2. Lägg till en **api-nyckel**och ställ `252044BE3886FE4A8E3BAA4F595114BB`in den på den här strängen: . Detta är en frågenyckel för söktjänsten för sandlådan som är värd för NYC Jobs-indexet.

När du har angett förfrågshuvudet kan du återanvända det för alla frågor i den här artikeln och bara byta ut **söksträngen.** 

  ![Begärandehuvud i Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Ange URL för begäran

Begäran är ett GET-kommando som är kopplat till en URL som innehåller slutpunkten för Azure Cognitive Search och söksträngen.

  ![Begärandehuvud i Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

URL-sammansättning har följande element:

+ **`https://azs-playground.search.windows.net/`** är en sandbox-söktjänst som underhålls av Azure Cognitive Search-utvecklingsteamet. 
+ **`indexes/nycjobs/`** är NYC Jobs-indexet i indexsamlingen för den tjänsten. Både tjänstnamnet och indexet krävs på begäran.
+ **`docs`** är dokumentsamlingen som innehåller allt sökbart innehåll. Fråge-api-nyckeln som finns i begäranden fungerar bara på läsåtgärder som är inriktade på dokumentsamlingen.
+ **`api-version=2019-05-06`** anger api-versionen, vilket är en obligatorisk parameter på varje begäran.
+ **`search=*`** är frågesträngen, som i den första frågan är null, returnerar de första 50 resultaten (som standard).

## <a name="send-your-first-query"></a>Skicka din första fråga

Som ett verifieringssteg klistrar du in följande begäran i GET och klickar på **Skicka**. Resultat returneras som utförliga JSON-dokument. Hela dokument returneras, vilket gör att du kan se alla fält och alla värden.

Klistra in den här URL:en i en REST-klient som ett valideringssteg och för att visa dokumentstrukturen.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

Frågesträngen **`search=*`** är en ospecificerad sökning som motsvarar null eller tom sökning. Det är den enklaste sökningen du kan göra.

Du kan också **`$count=true`** lägga till webbadressen för att returnera antalet dokument som matchar sökvillkoren. På en tom söksträng är detta alla dokument i indexet (ca 2800 när det gäller NYC Jobs).

## <a name="how-to-invoke-full-lucene-parsing"></a>Hur man åberopar full Lucene tolkning

Lägg till **queryType=full** om du vill anropa hela frågesyntaxen och åsidosätta standardsyntaxen för enkel fråga. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&search=*
```

Alla exempel i den här artikeln anger parametern **queryType=full** search, vilket anger att den fullständiga syntaxen hanteras av Lucene Query Parser. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Exempel 1: Fråga omfång till en lista med fält

Det här första exemplet är inte Lucene-specifika, men vi leder med det för att introducera det första grundläggande frågekonceptet: fältomfattning. Det här exemplet omfattar hela frågan och svaret på bara några specifika fält. Att veta hur man strukturerar ett läsbart JSON-svar är viktigt när ditt verktyg är Postman eller Search Explorer. 

För enkelhetens skull riktar sig frågan endast *till business_title* fältet och anger att endast affärstitlar returneras. Parametern **searchFields** begränsar frågekörningen till bara business_title fältet och **väljer** anger vilka fält som ingår i svaret.

### <a name="search-expression"></a>Sökkriterier

```http
&search=*&searchFields=business_title&$select=business_title
```

Här är samma fråga med flera fält i en kommaavgränsad lista.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

Blankstegen efter kommatecken är valfria.

> [!Tip]
> När du använder REST API från din programkod, glöm inte `$select` `searchFields`att URL-koda parametrar som och .

### <a name="full-url"></a>Fullständig URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

Svaret för den här frågan bör se ut ungefär som följande skärmbild.

  ![Exempelsvar för brevbärare](media/search-query-lucene-examples/postman-sample-results.png)

Du kanske har lagt märke till sökpoängen i svaret. Enhetliga poäng på 1 inträffar när det inte finns någon rang, antingen för att sökningen inte var fulltextsökning eller för att inga villkor tillämpades. För null-sökning utan villkor kommer rader tillbaka i godtycklig ordning. När du inkluderar faktiska sökvillkor ser du sökresultat utvecklas till meningsfulla värden.

## <a name="example-2-fielded-search"></a>Exempel 2: Fältsökning

Fullständig Lucene-syntax stöder omfång för enskilda sökuttryck till ett visst fält. Det här exemplet söker efter affärstitlar med termen senior i dem, men inte junior.

### <a name="search-expression"></a>Sökkriterier

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Här är samma fråga med flera fält.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>Fullständig URL

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  ![Exempelsvar för brevbärare](media/search-query-lucene-examples/intrafieldfilter.png)

Du kan definiera en fältsökåtgärd med syntaxen **fieldName:searchExpression,** där sökuttrycket kan vara ett enda ord eller en fras, eller ett mer komplext uttryck inom parenteser, eventuellt med booleska operatorer. Några exempel är följande:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Var noga med att placera flera strängar inom citattecken om du vill att båda strängarna ska utvärderas `state` som en enda entitet, som i det här fallet söker efter två olika platser i fältet. Se också till att operatören aktiveras som du ser med NOT och OCH.

Fältet som anges i **fieldName:searchExpression** måste vara ett sökbart fält. Mer information om hur indexattribut används i fältdefinitioner finns i [Skapa index (AZURE Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) för information om hur indexattribut används i fältdefinitioner.

> [!NOTE]
> I exemplet ovan behövde vi inte `searchFields` använda parametern eftersom varje del av frågan har ett fältnamn uttryckligen angivet. Du kan dock fortfarande `searchFields` använda parametern om du vill köra en fråga där vissa delar är begränsade till ett visst fält, och resten kan gälla för flera fält. Frågan `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` matchar till exempel `senior NOT junior` bara `business_title` fältet, medan den matchar "externt" med fältet. `posting_type` Fältnamnet som anges i **fieldName:searchExpression** har `searchFields` alltid företräde framför parametern, vilket är `business_title` anledningen `searchFields` till att vi i det här exemplet inte behöver inkludera i parametern.

## <a name="example-3-fuzzy-search"></a>Exempel 3: Fuzzy sökning

Fullständig Lucene syntax stöder också fuzzy sökning, matchning på termer som har en liknande konstruktion. Om du vill göra en suddig `~` sökning lägger du till tildesymbolen i slutet av ett enda ord med en valfri parameter, ett värde mellan 0 och 2, som anger redigeringsavståndet. Till `blue~` exempel, `blue~1` eller skulle återvända blå, blues och lim.

### <a name="search-expression"></a>Sökkriterier

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

Fraser stöds inte direkt, men du kan ange en suddig matchning på komponenter i en fras.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>Fullständig URL

Den här frågan söker efter jobb med termen "associera" (avsiktligt felstavat):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Fuzzy söksvar](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> Luddiga frågor [analyseras](search-lucene-query-architecture.md#stage-2-lexical-analysis)inte . Frågetyper med ofullständiga termer (prefixfråga, jokerteckenfråga, regex-fråga, fuzzy query) läggs till direkt i frågeträdet och kringgår analysfasen. Den enda omvandlingen som utförs på ofullständiga frågetermer är att sänkacasingen.
>

## <a name="example-4-proximity-search"></a>Exempel 4: Närhetssökning
Närhetssökningar används för att hitta termer som är nära varandra i ett dokument. Infoga en tilde "~"-symbol i slutet av en fras följt av antalet ord som skapar närhetsgränsen. Till exempel hittar "hotellflygplats"~5 termerna hotell och flygplats inom 5 ord från varandra i ett dokument.

### <a name="search-expression"></a>Sökkriterier

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>Fullständig URL

I den här frågan, för jobb med termen "senior analytiker" där den är åtskild med högst ett ord:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Närhetsfråga](media/search-query-lucene-examples/proximity-before.png)

Försök igen ta bort orden mellan termen "senior analytiker". Observera att 8 dokument returneras för den här frågan jämfört med 10 för föregående fråga.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Exempel 5: Term öka
Termökning refererar till rangordning av ett dokument högre om det innehåller den marknadsförda termen, i förhållande till dokument som inte innehåller termen. Om du vill öka en term använder du careten "^", symbolen med en boostfaktor (ett tal) i slutet av termen du söker. 

### <a name="full-urls"></a>Fullständiga webbadresser

I den här "före"-frågan söker du efter jobb med termen *datoranalytiker* och märker att det inte finns några resultat med både *orddator* och *analytiker*, men *datorjobb* är överst i resultatet.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![Term öka före](media/search-query-lucene-examples/termboostingbefore.png)

I frågan "efter" upprepar du sökningen, den här gången ökar resultaten med termen *analytiker* över *termdatorn* om båda orden inte finns. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
En mer läsbar version av `search=business_title:computer analyst^2`ovanstående fråga är . För en fungerande `^2` fråga, kodas som `%5E2`, vilket är svårare att se.

  ![Term öka efter](media/search-query-lucene-examples/termboostingafter.png)

Term öka skiljer sig från scoring profiler i att scoring profiler öka vissa fält, snarare än specifika termer. Följande exempel illustrerar skillnaderna.

Överväg en poängprofil som ökar matchningar i ett visst fält, till exempel **genre** i exemplet musicstoreindex. Term öka kan användas för att ytterligare öka vissa söktermer högre än andra. Till exempel kommer "rock^2 electronic" att öka dokument som innehåller söktermerna i **genrefältet** högre än andra sökbara fält i indexet. Dessutom kommer dokument som innehåller sökordet "rock" att rankas högre än den andra sökordet "elektronisk" som ett resultat av termen boost värde (2).

När du ställer in faktornivån, desto högre ökningsfaktor, desto mer relevant kommer termen att vara relativ till andra söktermer. Som standard är boostfaktorn 1. Även om boostfaktorn måste vara positiv kan den vara mindre än 1 (till exempel 0,2).


## <a name="example-6-regex"></a>Exempel 6: Regex

En sökning i reguljära uttryck hittar en matchning baserat på innehållet mellan snedstrecken "/", som dokumenteras i [klassen RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

### <a name="search-expression"></a>Sökkriterier

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>Fullständig URL

I den här frågan söker du efter jobb `search=business_title:/(Sen|Jun)ior/`med antingen termen Senior eller Junior: .

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Regex-fråga](media/search-query-lucene-examples/regex.png)

> [!Note]
> Regex-frågor [analyseras](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis)inte . Den enda omvandlingen som utförs på ofullständiga frågetermer är att sänkacasingen.
>

## <a name="example-7-wildcard-search"></a>Exempel 7: Jokerteckenssökning
Du kan använda allmänt erkända\*syntax för jokerteckensökningar med flera ( ) eller enstaka (?). Den Lucene-frågetolken stöder användningen av dessa symboler med en enda term och inte en fras.

### <a name="search-expression"></a>Sökkriterier

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>Fullständig URL

I den här frågan söker du efter jobb som innehåller prefixet "prog" som skulle innehålla affärstitlar med termerna programmering och programmerare i den. Du kan inte använda en * eller? som det första tecknet i en sökning.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![Jokerteckenfråga](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> Jokerteckenfrågor [analyseras](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis)inte . Den enda omvandlingen som utförs på ofullständiga frågetermer är att sänkacasingen.
>

## <a name="next-steps"></a>Nästa steg
Prova att ange Lucene Query Parser i koden. Följande länkar förklarar hur du ställer in sökfrågor för både .NET och REST API. Länkarna använder standardsyntaxen för enkel syntax så att du måste använda det du lärde dig av den här artikeln för att ange **queryType**.

* [Fråga ditt index med hjälp av .NET SDK](search-query-dotnet.md)
* [Fråga ditt index med REST API](search-create-index-rest-api.md)

Ytterligare syntaxreferens, frågearkitektur och exempel finns i följande länkar:

+ [Exempel på enkel syntaxfråga](search-query-simple-examples.md)
+ [Så här fungerar fulltextsökning i Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Enkel frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Fullständigt Lucene-frågesyntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)