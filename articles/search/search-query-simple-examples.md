---
title: Skapa en enkel fråga
titleSuffix: Azure Cognitive Search
description: Lär dig genom exempel genom att köra frågor baserat på den enkla syntaxen för fulltextsökning, filtersökning, geosökning, fasterad sökning mot ett Azure Cognitive Search-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3a801af7b97954510139a009a6d1344b281cf056
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261813"
---
# <a name="create-a-simple-query-in-azure-cognitive-search"></a>Skapa en enkel fråga i Azure Cognitive Search

I Azure Cognitive Search anropar den [enkla frågesyntaxen](query-simple-syntax.md) standardfrågetolkaren för att köra sökfrågor i fulltext mot ett index. Den här tolken är snabb och hanterar vanliga scenarier, inklusive fulltextsökning, filtrerad och fasterad sökning och geosökning. 

I den här artikeln använder vi exempel för `search=` att illustrera den enkla syntaxen och fylla i parametern för en [sökdokumentåtgärd.](https://docs.microsoft.com/rest/api/searchservice/search-documents)

En alternativ frågesyntax är [Full Lucene](query-lucene-syntax.md), som stöder mer komplexa frågestrukturer, till exempel fuzzy och jokerteckensökning, vilket kan ta ytterligare tid att bearbeta. Mer information och exempel som visar fullständig syntax finns i [Använd den fullständiga Lucen-syntaxen](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Formulera förfrågningar i Postman

Följande exempel utnyttjar ett NYC Jobs-sökindex som består av tillgängliga jobb baserat på en datauppsättning som tillhandahålls [av Initiativet City of New York OpenData.](https://nycopendata.socrata.com/) Dessa data bör inte betraktas som aktuella eller fullständiga. Indexet finns på en sandbox-tjänst som tillhandahålls av Microsoft, vilket innebär att du inte behöver en Azure-prenumeration eller Azure Cognitive Search för att prova dessa frågor.

Vad du behöver är Postman eller ett motsvarande verktyg för att utfärda HTTP-begäran på GET. Mer information finns i [Snabbstart: Utforska Azure Cognitive Search REST API med Postman](search-get-started-postman.md).

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

Frågesträngen **`search=*`** är en ospecificerad sökning som motsvarar null eller tom sökning. Det är inte särskilt användbart, men det är den enklaste sökningen du kan göra.

Du kan också **`$count=true`** lägga till webbadressen för att returnera antalet dokument som matchar sökvillkoren. På en tom söksträng är detta alla dokument i indexet (ca 2800 när det gäller NYC Jobs).

## <a name="how-to-invoke-simple-query-parsing"></a>Så här anropar du enkel frågetolning

För interaktiva frågor behöver du inte ange något: enkelt är standard. Om du tidigare har anropat **queryType=full** för fullständig frågesyntax kan du återställa standardinställningen med **queryType=simple**.

## <a name="example-1-field-scoped-query"></a>Exempel 1: Fråga om fältomfattning

Det här första exemplet är inte tolkspecifikt, men vi leder med det för att introducera det första grundläggande frågekonceptet: inneslutning. Det här exemplet omfattar frågekörning och svaret på bara några specifika fält. Att veta hur man strukturerar ett läsbart JSON-svar är viktigt när ditt verktyg är Postman eller Search Explorer. 

För enkelhetens skull riktar sig frågan endast *till business_title* fältet och anger att endast affärstitlar returneras. Syntaxen är **searchFields** för att begränsa frågekörning till bara business_title fältet och **väljer** att ange vilka fält som ska ingå i svaret.

### <a name="partial-query-string"></a>Partiell frågesträng

```http
searchFields=business_title&$select=business_title&search=*
```

Här är samma fråga med flera fält i en kommaavgränsad lista.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>Fullständig URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=business_title&$select=business_title&search=*
```

Svaret för den här frågan bör se ut ungefär som följande skärmbild.

  ![Exempelsvar för brevbärare](media/search-query-lucene-examples/postman-sample-results.png)

Du kanske har lagt märke till sökpoängen i svaret. Enhetliga poäng på 1 inträffar när det inte finns någon rang, antingen för att sökningen inte var fulltextsökning eller för att inga villkor tillämpades. För null-sökning utan villkor kommer rader tillbaka i godtycklig ordning. När du inkluderar faktiska kriterier ser du sökresultat utvecklas till meningsfulla värden.

## <a name="example-2-look-up-by-id"></a>Exempel 2: Slå upp efter ID

Det här exemplet är lite atypiskt, men när du utvärderar sökbeteenden kanske du vill granska hela innehållet i ett visst dokument för att förstå varför det ingick eller uteslöts från resultat. Om du vill returnera ett enskilt dokument i sin helhet använder du en [uppslagsåtgärd](https://docs.microsoft.com/rest/api/searchservice/lookup-document) för att skicka dokument-ID.

Alla dokument har en unik identifierare. Om du vill prova syntaxen för en uppslagsfråga returnerar du först en lista med dokument-ID:er så att du kan hitta en som du kan använda. För NYC-jobb lagras identifierarna i `id` fältet .

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=id&$select=id&search=*
```

Nästa exempel är en uppslagsfråga som `id` returnerar ett specifikt dokument baserat på "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", som dök upp först i föregående svar. Följande fråga returnerar hela dokumentet, inte bara markerade fält. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2019-05-06&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Exempel 3: Filterfrågor

[Filtersyntax](https://docs.microsoft.com/azure/search/search-query-odata-filter) är ett OData-uttryck som du kan använda med **sökning** eller av sig själv. Ett fristående filter, utan en sökparameter, är användbart när filteruttrycket kan kvalificera dokument av intresse fullt ut. Utan en frågesträng finns det ingen lexikal eller språklig analys, ingen poängsättning (alla poäng är 1) och ingen rangordning. Observera att söksträngen är tom.

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Används tillsammans, filtret tillämpas först på hela indexet och sedan sökningen utförs på resultaten av filtret. Filter kan därför vara en användbar teknik för att förbättra frågeprestanda eftersom de begränsar uppsättningen dokument som sökfrågan behöver bearbeta.

  ![Filtrera frågesvar](media/search-query-simple-examples/filtered-query.png)

Om du vill prova det här i Postman med GET kan du klistra in i den här strängen:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Ett annat kraftfullt sätt att **`search.ismatch*()`** kombinera filter och sökning är igenom i ett filteruttryck, där du kan använda en sökfråga i filtret. Det här filteruttrycket använder ett jokertecken i *planen* för att välja business_title inklusive termplan, planerare, planering och så vidare.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Mer information om funktionen finns [i search.ismatch i "Filterexempel".](https://docs.microsoft.com/azure/search/search-query-odata-full-text-search-functions#examples)

## <a name="example-4-range-filters"></a>Exempel 4: Intervallfilter

Områdesfiltrering **`$filter`** stöds via uttryck för alla datatyper. Följande exempel söker efter numeriska fält och strängfält. 

Datatyper är viktiga i intervallfilter och fungerar bäst när numeriska data finns i numeriska fält och strängdata i strängfält. Numeriska data i strängfält är inte lämpliga för intervall eftersom numeriska strängar inte är jämförbara i Azure Cognitive Search. 

Följande exempel är i POST-format för läsbarhet (numeriskt område, följt av textområde):

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  ![Områdesfilter för numeriska områden](media/search-query-simple-examples/rangefilternumeric.png)


```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  ![Områdesfilter för textområden](media/search-query-simple-examples/rangefiltertext.png)

Du kan också prova dessa i Postman med GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> Faceting över intervall av värden är ett vanligt sökprogram krav. Mer information och exempel på att skapa filter för fasnavigeringsstrukturer finns [i "Filter baserat på ett intervall" i *Hur du implementerar fasterad navigering*](search-faceted-navigation.md#filter-based-on-a-range).

## <a name="example-5-geo-search"></a>Exempel 5: Geo-sökning

Exempelindexet innehåller ett geo_location fält med latitud- och longitudkoordinater. I det här exemplet används [funktionen geo.distance](https://docs.microsoft.com/azure/search/search-query-odata-geo-spatial-functions#examples) som filtrerar på dokument i omkretsen av en startpunkt, ut till ett godtyckligt avstånd (i kilometer) som du anger. Du kan justera det sista värdet i frågan (4) för att minska eller förstora frågans yta.

Följande exempel är i POST-format för läsbarhet:

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
För mer läsbara resultat trimmas sökresultaten så att de innehåller ett jobb-ID, en befattning och arbetsplatsen. Startkoordinaterna erhölls från ett slumpmässigt dokument i indexet (i detta fall för en arbetsplats på Staten island.

Du kan också prova det här i Postman med GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Exempel 6: Sökprecision

Termfrågor är enstaka termer, kanske många av dem, som utvärderas oberoende av dem. Frasfrågor omges av citattecken och utvärderas som en ordagrann sträng. Matchens precision styrs av operatörer och searchMode.

Exempel 1: **`&search=fire`** returnerar 150 resultat, där alla matchningar innehåller ordet brand någonstans i dokumentet.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire
```

Exempel 2: **`&search=fire department`** returnerar 2002 års resultat. Matchningar returneras för dokument som innehåller antingen brand eller avdelning.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire department
```

Exempel 3: **`&search="fire department"`** returnerar 82 resultat. Omsluter strängen inom citattecken är en ordagrann sökning på båda termerna, och matchningar finns på tokeniserade termer i indexet som består av de kombinerade termerna. Detta förklarar varför **`search=+fire +department`** en sökning som inte är likvärdig. Båda termerna krävs, men skannas efter oberoende av dem. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Exempel 7: Booleans med searchMode

Enkel syntax stöder booleska operatorer`+, -, |`i form av tecken ( ). Parametern searchMode informerar kompromisser mellan precision `searchMode=any` och återkallande, med att gynna återkallande (matchning på `searchMode=all` alla kriterier som kvalificerar ett dokument för resultatuppsättningen) och gynnar precision (alla kriterier måste matchas). Standard är `searchMode=any`, vilket kan vara förvirrande om du stapla en fråga med flera operatorer och blir bredare i stället för smalare resultat. Detta gäller särskilt med NOT, där resultaten inkluderar alla dokument som "inte innehåller" en viss term.

Med hjälp av standardsökningenMode (valfri) returneras 2800 dokument: de som innehåller den flerdelade termen "brandkår", plus alla dokument som inte har termen "Metrotech Center".

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  ![sökläge valfritt](media/search-query-simple-examples/searchmodeany.png)

Ändra sökMode `all` att genomdriva en kumulativ effekt på kriterier och returnerar en mindre resultatuppsättning - 21 dokument - bestående av dokument som innehåller hela frasen "brandkår", minus dessa jobb på Metrotech Center adress.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  ![sökläge alla](media/search-query-simple-examples/searchmodeall.png)

## <a name="example-8-structuring-results"></a>Exempel 8: Strukturera resultat

Flera parametrar styr vilka fält som finns i sökresultaten, antalet dokument som returneras i varje batch och sorteringsordning. Det här exemplet rapporterar om några av de tidigare exemplen, vilket begränsar resultaten till specifika fält med hjälp av **$select-satsen** och fullständiga sökkriterier, vilket ger 82 matchningar 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Lägg till i föregående exempel kan du sortera efter rubrik. Den här sorten fungerar eftersom civil_service_title är *sorterbar* i indexet.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Växlingsresultat implementeras med parametern **$top,** i det här fallet returnerar de 5 översta dokumenten:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

För att få nästa 5, hoppa över den första batchen:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Nästa steg
Försök att ange frågor i koden. Följande länkar förklarar hur du ställer in sökfrågor för både .NET och REST API med hjälp av standardsyntaxen för enkel.

* [Fråga ditt index med hjälp av .NET SDK](search-query-dotnet.md)
* [Fråga ditt index med REST API](search-create-index-rest-api.md)

Ytterligare syntaxreferens, frågearkitektur och exempel finns i följande länkar:

+ [Lucene syntax frågeexempel för att skapa avancerade frågor](search-query-lucene-examples.md)
+ [Så här fungerar fulltextsökning i Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Enkel frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Fullständig Lucene-fråga](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Syntax för filter och ordning efter](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
