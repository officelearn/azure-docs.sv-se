---
title: Exempel på Lucene-syntaxfråga – Azure Search
description: Lucene-frågesyntax för fuzzy-sökning, närhetssökning, termförstärkning, sökning med reguljära uttryck och jokertecken i en Azure Search-tjänst.
author: HeidiSteen
manager: cgronlun
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 108dd80aa90772eb01fe3c7f0176ddd37e27acaa
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024459"
---
# <a name="query-examples-using-full-lucene-search-syntax-advanced-queries-in-azure-search"></a>Fråga exempel med ”full” Lucene Söksyntaxen (avancerade frågor i Azure Search)

När frågor för Azure Search, du kan ersätta standard [enklare frågeparsern](query-simple-syntax.md) med mer omfattande [frågeparser (Lucene) i Azure Search](query-lucene-syntax.md) formulera specialiserade och avancerad fråga definitioner. 

Lucene-parsern har stöd för komplex fråga konstruktioner som fältbegränsade frågor fuzzy och sökning med jokertecken för prefix, närhetssökning, termförstärkning och sökning med reguljära uttryck. Mer kraft levereras med ytterligare bearbetning-krav, så du kan förvänta dig en något längre körningstid. I den här artikeln kan du gå igenom exempel som visar frågeåtgärder som är tillgängliga när du använder den fullständiga syntaxen.

> [!Note]
> Många av specialiserade fråga-konstruktioner som aktiveras via fullständig Lucene-frågesyntaxen är inte [text analyseras](search-lucene-query-architecture.md#stage-2-lexical-analysis), vilket kan vara så konstigt om du förväntar dig ordstamsigenkänning eller lemmatisering. Lexikal analys utförs endast på fullständiga villkor (en term fråga eller en frasfråga). Frågetyper med ofullständiga villkor (prefix fråga, jokerteckenfråga, regex-fråga, fuzzy-fråga) läggs till direkt i trädet fråga kringgår analysis-steg. Endast transformeringen utförs på ofullständig sökord platsargumentet. 
>

## <a name="formulate-requests-in-postman"></a>Formulera begäranden i Postman

I följande exempel utnyttja NYC Jobs sökindex som består av jobben tillgängliga baserat på en datauppsättning som tillhandahålls av den [stad New York OpenData](https://opendata.cityofnewyork.us/) initiativ. Dessa data ska inte betraktas aktuella eller klar. Indexet ligger på en sandbox-tjänst som tillhandahålls av Microsoft, vilket innebär att du inte behöver en Azure-prenumeration eller ett Azure Search för att prova de här frågorna.

Vad du behöver är Postman eller ett motsvarande verktyg för att utfärda HTTP-begäranden på GET. Mer information finns i [utforska med REST-klienter](search-fiddler.md).

### <a name="set-the-request-header"></a>Ange huvudet för begäran

1. I huvudet för begäran och ange **Content-Type** till `application/json`.

2. Lägg till en **api-nyckeln**, och ange den till följande sträng: `252044BE3886FE4A8E3BAA4F595114BB`. Det här är en frågenyckel för sandbox-search-tjänsten som är värd för NYC Jobs-index.

När du har angett huvudet för begäran, du kan återanvända den för alla frågor i den här artikeln, bara byta ut den **search =** sträng. 

  ![Begärandehuvud i Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Ange fråge-URL

Begäran är en GET-command tillsammans med en URL som innehåller Azure Search-slutpunkten och Sök efter strängen.

  ![Begärandehuvud i Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

Webbadressen har följande element:

+ **`https://azs-playground.search.windows.net/`** underhålls en sandbox söktjänst av Azure Search-Utvecklingsteamet. 
+ **`indexes/nycjobs/`** är NYC Jobs-index i samlingen index för tjänsten. Både namnet på tjänsten och index krävs på begäran.
+ **`docs`** är samlingen av dokument som innehåller alla sökbart innehåll. Fråga api-nyckeln angavs i begäranshuvudet fungerar bara på läsåtgärder Målsamlingar dokument.
+ **`api-version=2019-05-06`** Anger den api-versionen, vilket är en obligatorisk parameter för varje begäran.
+ **`search=*`** är frågesträngen som i den första frågan är null, returnerar de första 50 resultaten (som standard).

## <a name="send-your-first-query"></a>Skicka din första fråga

Klistra in följande begäran i GET som ett verifieringssteg och klicka på **skicka**. Resultat returneras som utförliga JSON-dokument. Hela returneras dokument, vilket gör att du kan se alla fält och alla värden.

Klistra in URL: en i en REST-klient som ett steg för verifiering och för att visa dokumentstruktur.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

Frågesträngen **`search=*`**, motsvarar en ospecificerad sökning null eller tom sökning. Det är den enklaste sökningen som du kan göra.

Du kan också lägga till **`$count=true`** i URL: en för att returnera en uppräkning av dokument som matchar sökkriterierna. Det här är alla dokument i indexet (cirka 2800 när det gäller NYC Jobs) på en tom sökning-sträng.

## <a name="how-to-invoke-full-lucene-parsing"></a>Hur du anropar fullständig Lucene parsning

Lägg till **queryType = full** att anropa fullständig frågesyntaxen, åsidosätter den enkla frågesyntaxen standard. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&search=*
```

Alla exemplen i den här artikeln anger de **queryType = full** sökparametern, som anger att den fullständiga syntaxen hanteras av den frågeparser (Lucene). 

## <a name="example-1-field-scoped-query"></a>Exempel 1: Fältbegränsade frågor

Det här första exemplet är inte Lucene-specifika, men vi leda med den till introducerar begreppet första grundläggande fråga: inneslutning. Det här exemplet anger omfattningen för frågekörning och svaret på bara några specifika fält. Det är viktigt att att känna till hur du ska strukturera ett läsbara JSON-svar när ditt verktyg är Postman eller Search explorer. 

Av utrymmesskäl, frågan riktar sig till endast de *business_title* fältet och anger endast företag rubriker som returneras. Syntaxen är **searchFields** att begränsa Frågekörningen bara till business_title fältet, och **Välj** att ange vilka fält som ska ingå i svaret.

### <a name="partial-query-string"></a>Partiell frågesträng

```http
&search=*&searchFields=business_title&$select=business_title
```

Här är samma fråga med flera fält i en kommaavgränsad lista.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>Fullständiga URL: en

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

Svar för den här frågan bör likna följande skärmbild.

  ![Postman exempelsvar](media/search-query-lucene-examples/postman-sample-results.png)

Du kanske har märkt sökpoängen i svaret. Enhetlig poäng 1 inträffa när det finns inga rankning, antingen eftersom sökningen inte var fulltextsökning, eller eftersom inga kriterier har tillämpats. För null search med några villkor gå rader tillbaka i valfri ordning. När du inkluderar aktuella visas search poäng utvecklas till meningsfulla värden.

## <a name="example-2-intra-field-filtering"></a>Exempel 2: Trafik fältet filtrering

Fullständig Lucene-syntax stöder uttryck i ett fält. Det här exemplet söker för företag med termen senior i dem, men inte unga.

### <a name="partial-query-string"></a>Partiell frågesträng

```http
searchFields=business_title&$select=business_title&search=business_title:senior+NOT+junior
```

Här är samma fråga med flera fält.

```http
searchFields=business_title, posting_type&$select=business_title, posting_type&search=business_title:senior+NOT+junior AND posting_type:external
```

### <a name="full-url"></a>Fullständiga URL: en

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:senior+NOT+junior
```

  ![Postman exempelsvar](media/search-query-lucene-examples/intrafieldfilter.png)

Genom att ange en **fieldname:searchterm** konstruktion, kan du definiera en fielded frågeåtgärden där fältet är ett enstaka ord och söktermen är också ett enstaka ord eller en fras, eventuellt med booleska operatorer. Följande är några exempel:

* business_title:(senior NOT junior)
* tillstånd: (”New York” och ”ny Jersey”)
* business_title:(senior NOT junior) och posting_type:external

Glöm inte att placera flera strängar inom citattecken om du vill att båda strängar som ska utvärderas som en enda enhet, som i det här fallet söker efter två olika städer i Platsfältet. Kontrollera också att operatören blir versal som du ser med inte och och.

Fält som anges i **fieldname:searchterm** måste vara ett sökbart fält. Se [Create Index (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) mer information om hur indexattribut används i fältdefinitioner.

## <a name="example-3-fuzzy-search"></a>Exempel 3: Fuzzy-sökning

Fullständig Lucene-syntax har även stöd för fuzzy-sökning, matcha mot termer som har en liknande konstruktion. Om du vill göra en fuzzy-sökning, lägger du till tilde `~` symbolen i slutet av ett enstaka ord med en valfri parameter, ett värde mellan 0 och 2, som anger avståndet redigera. Till exempel `blue~` eller `blue~1` kommer att returnera blå och blues sammanlänkande.

### <a name="partial-query-string"></a>Partiell frågesträng

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

Fraser stöds inte direkt, men du kan ange en ungefärlig matchning på beståndsdelar i en mening.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>Fullständiga URL: en

Den här frågan söker efter jobb med termen ”associera” (avsiktligt felstavad):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Fuzzy-sökning svar](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> Fuzzy frågor är inte [analyseras](search-lucene-query-architecture.md#stage-2-lexical-analysis). Frågetyper med ofullständiga villkor (prefix fråga, jokerteckenfråga, regex-fråga, fuzzy-fråga) läggs till direkt i trädet fråga kringgår analysis-steg. Endast transformeringen utförs på ofullständig sökord platsargumentet.
>

## <a name="example-4-proximity-search"></a>Exempel 4: närhetssökning
Närhet sökningar används för att hitta villkor som är nära varandra i ett dokument. Infoga en tilde ”~” tecken i slutet av en fras följt av antalet ord som skapar närhet gränsen. Till exempel hotell flygplats ”” ~ 5 hittar villkoren hotell och flygplats inom 5 ord från varandra i ett dokument.

### <a name="partial-query-string"></a>Partiell frågesträng

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>Fullständiga URL: en

I den här frågan för jobb med termen ”senior analytiker” där den avgränsas med mer än ett ord:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Närhet fråga](media/search-query-lucene-examples/proximity-before.png)

Prova igen tar du bort ord mellan termen ”senior analytiker”. Observera att returneras 8 dokument för den här frågan till skillnad från 10 för den föregående frågan.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Exempel 5: termförstärkning
Termförstärkning avser rangordning ett dokument som är högre om den innehåller förbättrat termen, i förhållande till dokument som inte innehåller termen. Om du vill öka en term använder du cirkumflex ”^”, symbol med en faktor boost (ett tal) i slutet av perioden som du söker. 

### <a name="full-urls"></a>Fullständiga URL: er

I den här frågan ”före” Sök efter jobb med termen *datorn analytiker* och Observera att det finns inga resultat med båda orden *datorn* och *analytiker*, ännu  *datorn* jobben är högst upp på resultaten.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![Termförstärkning innan](media/search-query-lucene-examples/termboostingbefore.png)

Upprepa sökningen nu öka resultat med termen i frågan ”efter” *analytiker* på sikt *datorn* om båda ord som inte finns. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
En mänskligare läsbara version av ovanstående fråga `search=business_title:computer analyst^2`. För en fungerande fråga `^2` kodas som `%5E2`, som är svårare att se.

  ![Termförstärkning efter](media/search-query-lucene-examples/termboostingafter.png)

Termförstärkning skiljer sig från poängprofiler i att bedömningsprofiler öka vissa fält i stället för på specifika villkor. I följande exempel hjälper visar skillnaderna.

Överväg en bedömningsprofil som förstärker matchar i ett visst fält som **genre** i musicstoreindex-exemplet. Termförstärkning kan användas för att ytterligare höja vissa search villkor som är högre än andra. Till exempel ”rock ^ 2 elektronisk” förbättras dokument som innehåller sökvillkor i den **genre** fält som är högre än andra sökbara fält i indexet. Dessutom kommer dokument som innehåller söktermen ”rock” att rangordnas högre än andra söktermen ”elektronisk” till följd av termen boost värdet (2).

När du anger factor-nivå, desto högre boost-faktor, mer relevant kommer vara i förhållande till andra söktermer. Som standard är faktorn boost 1. Även om faktorn boost måste vara positivt, kan det vara mindre än 1 (till exempel, 0.2).


## <a name="example-6-regex"></a>Exempel 6: Regex

En sökning med reguljära uttryck hittar en matchning baserat på innehållet mellan snedstreck ”/”, som beskrivs i den [klassen RegExp](https://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

### <a name="partial-query-string"></a>Partiell frågesträng

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>Fullständiga URL: en

I den här frågan söker du efter jobb med termen Senior eller sitt första år: `search=business_title:/(Sen|Jun)ior/`.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Regex-fråga](media/search-query-lucene-examples/regex.png)

> [!Note]
> Regex-frågor är inte [analyseras](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Endast transformeringen utförs på ofullständig sökord platsargumentet.
>

## <a name="example-7-wildcard-search"></a>Exempel 7: sökning med jokertecken
Du kan använda allmänt erkända syntax för flera (\*) eller enstaka (?) tecken jokertecken. Observera den frågeparser (Lucene) stöder användning av dessa symboler med en enda term och inte en fras.

### <a name="partial-query-string"></a>Partiell frågesträng

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>Fullständiga URL: en

Sök efter jobb som innehåller prefixet ”prog' som skulle inkludera rubriker för företag med villkor programming programmerare i den i den här frågan. Du kan inte använda en * eller? symbol som det första tecknet i en sökning.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![Jokerteckenfråga](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> Jokertecken frågor är inte [analyseras](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Endast transformeringen utförs på ofullständig sökord platsargumentet.
>

## <a name="next-steps"></a>Nästa steg
Försök att ange den frågeparser (Lucene) i din kod. Följande länkar beskriver hur du ställer in sökfrågor för både .NET och REST-API. Länkarna använder standard enkel syntax så behöver du tillämpa det du lärt dig från den här artikeln för att ange den **queryType**.

* [Fråga ditt Azure Search-Index med .NET SDK](search-query-dotnet.md)
* [Fråga ditt Azure Search-Index med hjälp av REST-API](search-create-index-rest-api.md)

Ytterligare referens, fråga arkitektur och exempel finns i följande länkar:

+ [Exempel på enkla syntaxfråga](search-query-simple-examples.md)
+ [Hur Fullständig textsökning fungerar i Azure Search](search-lucene-query-architecture.md)
+ [Enkel frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Fullständig Lucene-frågesyntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)