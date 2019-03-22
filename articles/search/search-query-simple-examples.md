---
title: Exempelfråga-exempel – Azure Search
description: Exempelfråga exempel för fulltextsökning, filter, search, geo-sökning, aspektbaserad sökning och andra frågesträngar som används för att fråga en Azure Search-index.
author: HeidiSteen
manager: cgronlun
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/09/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: a975c95af75e9f3e09e5d0142716795ab4b90e28
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136486"
---
# <a name="simple-syntax-query-examples-for-building-queries-in-azure-search"></a>Exempel på enkla syntaxfråga för att skapa frågor i Azure Search

[Enkel frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) anropar standard frågeparsern för att köra fulltextsökningsfrågor mot ett Azure Search-index. Enkel frågeanalysatorn går snabbt och hanterar vanliga scenarier i Azure Search, inklusive fulltextsökning, filtrerad och aspektbaserad sökning och geo-sökning. Gå igenom exempel som visar frågeåtgärder som är tillgängliga när du använder enkel syntax i den här artikeln.

Alternativa frågesyntaxen är [fullständig Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), stöd för mer komplex fråga strukturer som stöds, till exempel fuzzy och sökning med jokertecken, vilket kan ta längre tid att bearbeta. Mer information och exempel som visar fullständig syntax finns i [exempel på Lucene-syntaxfråga](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Formulera begäranden i Postman

I följande exempel utnyttja NYC Jobs sökindex som består av jobben tillgängliga baserat på en datauppsättning som tillhandahålls av den [stad New York OpenData](https://nycopendata.socrata.com/) initiativ. Dessa data ska inte betraktas aktuella eller klar. Indexet ligger på en sandbox-tjänst som tillhandahålls av Microsoft, vilket innebär att du inte behöver en Azure-prenumeration eller ett Azure Search för att prova de här frågorna.

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
+ **`api-version=2017-11-11`** Anger den api-versionen, vilket är en obligatorisk parameter för varje begäran.
+ **`search=*`** är frågesträngen som i den första frågan är null, returnerar de första 50 resultaten (som standard).

## <a name="send-your-first-query"></a>Skicka din första fråga

Klistra in följande begäran i GET som ett verifieringssteg och klicka på **skicka**. Resultat returneras som utförliga JSON-dokument. Du kan kopiera och klistra in URL: en i första exemplet nedan.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=*
  ```

Frågesträngen **`search=*`**, motsvarar en ospecificerad sökning null eller tom sökning. Det är inte särskilt användbart, men det är den enklaste sökningen som du kan göra.

Du kan också lägga till **`$count=true`** i URL: en för att returnera en uppräkning av dokument som matchar sökkriterierna. Det här är alla dokument i indexet (cirka 2800 när det gäller NYC Jobs) på en tom sökning-sträng.

## <a name="how-to-invoke-simple-query-parsing"></a>Hur du anropar en enkel fråga parsning

För interaktiva frågor du inte behöver ange något: enkel är standard. I koden, om du tidigare anropade **queryType = full** för fullständig frågesyntax, kan du återställa standard med **queryType = enkla**.

## <a name="example-1-field-scoped-query"></a>Exempel 1: Fältbegränsade frågor

Det här första exemplet är inte parser-specifika, men vi leda med den till introducerar begreppet första grundläggande fråga: inneslutning. Det här exemplet anger omfattningen för frågekörning och svaret på bara några specifika fält. Det är viktigt att att känna till hur du ska strukturera ett läsbara JSON-svar när ditt verktyg är Postman eller Search explorer. 

Av utrymmesskäl, frågan riktar sig till endast de *business_title* fältet och anger endast företag rubriker som returneras. Syntaxen är **searchFields** att begränsa Frågekörningen bara till business_title fältet, och **Välj** att ange vilka fält som ska ingå i svaret.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&search=*
```

Svar för den här frågan bör likna följande skärmbild.

  ![Postman exempelsvar](media/search-query-lucene-examples/postman-sample-results.png)

Du kanske har märkt sökpoängen i svaret. Enhetlig poäng 1 inträffa när det finns inga rankning, antingen eftersom sökningen inte var fulltextsökning, eller eftersom inga kriterier har tillämpats. För null search med några villkor gå rader tillbaka i valfri ordning. När du inkluderar aktuella visas search poäng utvecklas till meningsfulla värden.

## <a name="example-2-look-up-by-id"></a>Exempel 2: Leta upp efter ID

Det här exemplet är lite ovanlig, men när du utvärderar sökbeteenden kanske du vill granska hela innehållet i ett visst dokument att förstå varför den inkluderas eller uteslutas från resultaten. Du kan returnera ett enstaka dokument i sin helhet med en [sökning](https://docs.microsoft.com/rest/api/searchservice/lookup-document) att skicka in dokument-ID.

Alla dokument har en unik identifierare. Om du vill testa syntaxen för en lookup-fråga först returnera en lista över dokument-ID: N så att du kan hitta jag ska använda. För NYC Jobs identifierarna som lagras i den `id` fält.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=id&$select=id&search=*
```

I nästa exempel är en sökning-fråga som returnerar ett visst dokument baserat på `id` ”9E1E3AF9-0660-4E00-AF51-9B654925A2D5” som fanns först i föregående svar. Följande fråga returnerar hela dokumentet, inte bara valda fält. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2017-11-11&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Exempel 3: Filterfrågor

[Filtrera syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples) är ett OData-uttryck som du kan använda med **search** eller ensamt. Ett filter för fristående, utan en sökparameter är användbart när filteruttrycket kan fullständigt kvalificera dokument av intresse. Det finns inga lexikal eller lingvistisk analys utan en frågesträng utan bedömning som (samtliga värden är 1), och inga rangordning. Observera att strängen är tom.

```http
POST /indexes/nycjobs/docs/search?api-version=2017-11-11
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "select=job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Används tillsammans tillämpas filtret först på hela indexet och sedan utförs sökningen på resultatet av filtret. Filter kan därför vara en användbar teknik för att förbättra frågeprestanda eftersom de begränsar uppsättningen dokument som sökfrågan behöver bearbeta.

  ![Filtrera frågesvaret](media/search-query-simple-examples/filtered-query.png)

Om du vill testa detta i Postman med hjälp av GET, kan du klistra in följande sträng:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Ett annat kraftfulla sätt att kombinera filter och Sök **`search.ismatch*()`** i ett filteruttryck, där du kan använda en sökfråga i filtret. Den här filteruttrycket använder jokertecken på *plan* att välja business_title inklusive termen plan, planner, planering och så vidare.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Mer information om funktionen finns i [search.ismatch i ”Filter exempel”](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

## <a name="example-4-range-filters"></a>Exempel 4: Intervallfiltren

Filtrering av datumintervall stöds via **`$filter`** uttryck för datatyp. I följande exempel söker igenom sträng och numeriska fält. 

Datatyper är viktiga i intervallfiltren och fungerar bäst när numeriska data är i numeriska fält och strängdata i strängfält. Numeriska data i strängfält är inte lämpligt för adressintervall eftersom numeriska strängar inte är jämförbar i Azure Search. 

I följande exempel finns i INLÄGGET format för läsbarhet (numeriska intervall, följt av textintervall):

```http
POST /indexes/nycjobs/docs/search?api-version=2017-11-11
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  ![Filter för datumintervall för numeriska intervall](media/search-query-simple-examples/rangefilternumeric.png)


```http
POST /indexes/nycjobs/docs/search?api-version=2017-11-11
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  ![Filter för datumintervall för text-intervall](media/search-query-simple-examples/rangefiltertext.png)

Du kan också testa dessa i Postman med hjälp av GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> Fasettering över intervall med värden är ett vanligt krav för search-program. Mer information och exempel på att skapa filter för aspekten navigeringsstrukturer finns i [”Filter baserat på ett” i *implementera aspektbaserad navigering*](search-faceted-navigation.md#filter-based-on-a-range).

## <a name="example-5-geo-search"></a>Exempel 5: Geo-sökning

Exempelindexet innehåller ett geo_location fält med koordinater för latitud och longitud. Det här exemplet används den [geo.distance funktionen](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples) som filtrerar dokumenten i en startpunkt omkrets ut till en godtycklig avstånd (i kilometer) som du anger. Du kan justera det sista värdet i frågan (4) att minska eller öka ytan på frågan.

I följande exempel är i formatet för POST för läsbarhet:

```http
POST /indexes/nycjobs/docs/search?api-version=2017-11-11
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
För mer läsbara resultat bort sökresultaten om du vill inkludera ett jobb-ID, jobbtitel och arbetsplats. Från koordinaterna erhölls från ett slumpmässigt dokument i indexet (i det här fallet för arbetsplats på Staten ö.

Du kan också testa detta i Postman med hjälp av GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Exempel 6: Sök precision

Termen frågorna är enskilda termer, kanske många av dem, som utvärderas oberoende av varandra. Frasfrågor är inom citattecken och utvärderas som en ordagrant sträng. Precisionen för matchningen styrs av operatörer och searchMode.

Exempel 1: **`&search=fire`** returnerar 150 resultat där alla matchningar ska innehålla den word fire någonstans i dokumentet.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire
```

Exempel 2: **`&search=fire department`** returnerar 2002 resultat. Matchningar returneras för dokument som innehåller fire eller avdelning.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire department
```

Exempel 3: **`&search="fire department"`** returnerar 82 resultat. Omsluta strängen med citattecken är en ordagrant sökning på båda termerna och matchningar hittas på principfilerna villkor i indexet som består av de kombinerade villkoren. Det förklarar varför en sökning som **`search=+fire +department`** motsvarar inte. Båda termerna krävs, men genomsöks för oberoende av varandra. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Exempel 7: Booleska värden med searchMode

Enkel syntax stöder booleska operatorer i form av tecken (`+, -, |`). Parametern searchMode informerar rätt balans mellan precision och kanske kommer ihåg, med `searchMode=any` prioriterar återkallande (matchar på vilket kriterium som kvalificerar ett dokument för resultatuppsättningen), och `searchMode=all` prioriterar precision (alla villkor måste matchas). Standardvärdet är `searchMode=any`, vilket kan förvirrande om du stapling en fråga med flera operatorer och komma bredare i stället för smalare resultat. Detta gäller särskilt med inte, där resultaten ska innehålla alla dokument ”som inte innehåller” en viss tidsperiod.

Med hjälp av standard-searchMode (valfritt), returneras 2800 dokument: som innehåller flera del term ”Brandkår”, plus alla dokument som inte innehåller termen ”Metrotech Center”.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  ![Sök läge alla](media/search-query-simple-examples/searchmodeany.png)

Ändra searchMode till `all` tillämpar en ackumulerade effekten på ett villkor och returnerar en mindre resultatmängd - 21 dokument – som består av dokument som innehåller hela frasen ”Brandkår”, minus dessa jobb på Metrotech Center-adress.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  ![alla sökläge](media/search-query-simple-examples/searchmodeall.png)

## <a name="example-8-structuring-results"></a>Exempel 8: Strukturera resultat

Flera parametrar styra vilka fält som finns i sökningen resultat, antalet dokument i varje batch och sorteringsordning. Det här exemplet resurfaces några av i föregående exempel kan begränsa resultaten till specifika fält med hjälp av den **$select** -instruktionen och ordagrant sökvillkoren, returnerar 82 matchningar 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Läggs till i föregående exempel, kan du sortera efter rubrik. Den här typen fungerar eftersom civil_service_title *sorterbar* i indexet.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Växla resultat implementeras med hjälp av den **$top** parameter, i det här fallet returneras de 5 främsta dokument:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Hoppa över den första batchen för att hämta nästa 5:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Nästa steg
Försök att ange frågor i din kod. Följande länkar beskriver hur du ställer in sökfrågor för både .NET och REST-API med hjälp av enkel standard-syntax.

* [Fråga ditt Azure Search-Index med .NET SDK](search-query-dotnet.md)
* [Fråga ditt Azure Search-Index med hjälp av REST-API](search-create-index-rest-api.md)

Ytterligare referens, fråga arkitektur och exempel finns i följande länkar:

+ [Exempel på Lucene-syntaxfråga för att skapa avancerade frågor](search-query-lucene-examples.md)
+ [Hur Fullständig textsökning fungerar i Azure Search](search-lucene-query-architecture.md)
+ [Enkel frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Fullständiga Lucene-fråga](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Filter och Orderby-syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
