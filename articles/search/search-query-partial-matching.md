---
title: Matcha mönster och specialtecken
titleSuffix: Azure Cognitive Search
description: Använd jokertecken och prefixfrågor för att matcha hela eller delar av termer i en förfrågan om Azure Kognitiv sökning frågor. Hårda till matchnings mönster som innehåller specialtecken kan lösas med fullständig frågesyntax och anpassade analyser.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ec1422d03cce78bdd8206f6687a78b63ddf989dc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989623"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>Matcha på mönster och specialtecken (bindestreck)

För frågor som innehåller specialtecken (`-, *, (, ), /, \, =`), eller för fråge mönster baserade på del termer inom en större period, krävs det vanligt vis ytterligare konfigurations steg för att säkerställa att indexet innehåller det förväntade innehållet i rätt format. 

Som standard är ett telefonnummer som `+1 (425) 703-6214` är token som `"1"`, `"425"`, `"703"`, `"6214"`. Som du kan föreställa dig kan du söka på `"3-62"`, ofullständiga termer som innehåller ett bindestreck, det går inte att utföra eftersom innehållet inte finns i indexet. 

När du behöver söka efter partiella strängar eller specialtecken kan du åsidosätta standard analys verktyget med en anpassad analys som fungerar under enklare tokenisering-regler och som bevarar hela villkoret, som behövs när frågesträngar innehåller delar av en term eller en särskild tabbtecken. Genom att gå tillbaka, ser metoden ut så här:

+ Välj en fördefinierad analys eller definiera en anpassad analys som ger önskad utdata
+ Koppla analysen till fältet
+ Bygg index och testa

Den här artikeln vägleder dig genom dessa uppgifter. Den metod som beskrivs här är användbar i andra scenarier: jokertecken och reguljära uttrycks frågor behöver också hela termer som grund för mönster matchning. 

> [!TIP]
> Utvärdering av analyers är en iterativ process som kräver upprepade index återuppbyggnadar. Du kan göra det här steget enklare genom att använda Postman, REST-API: er för [create index](https://docs.microsoft.com/rest/api/searchservice/create-index), [ta bort index](https://docs.microsoft.com/rest/api/searchservice/delete-index),[läsa in dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)och [söka dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents). För Läs dokument bör begär ande texten innehålla en liten representativ data uppsättning som du vill testa (till exempel ett fält med telefonnummer eller produkt koder). Med dessa API: er i samma Postman-samling kan du snabbt gå igenom de här stegen.

## <a name="choosing-an-analyzer"></a>Välja en analys

När du väljer en analys som producerar token för en hel period är följande analys verktyg vanliga alternativ:

| Analysen | Funktions |
|----------|-----------|
| [följt](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Innehållet i hela fältet är token som en enskild term. |
| [blank steg](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Separerar bara på blank steg. Termer som innehåller bindestreck eller andra tecken behandlas som en enda token. |
| [anpassad analys](index-add-custom-analyzers.md) | rekommenderas Genom att skapa en anpassad analys kan du ange både tokenizer och token-filtret. Föregående analyser måste användas som de är. Med en anpassad analys kan du välja vilka tokenizers och token filter som ska användas. <br><br>En rekommenderad kombination är [nyckelordet tokenizer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) med ett [lägsta token-filter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). Den fördefinierade [nyckelords analysen](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) innehåller i själva fall inte gemener och versaler, vilket kan orsaka att frågor Miss söker. Med en anpassad analys får du en mekanism för att lägga till det nedre token-filtret. |

Om du använder ett webb-API-testverktyg som Postman kan du lägga till [test analys rest-anropet](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) för att inspektera token-utdata. Om du har ett befintligt index och ett fält som innehåller bindestreck eller delar av termer kan du prova olika analys verktyg över vissa villkor för att se vilka tokens som genereras.  

1. Kontrol lera standard analys för att se hur termerna är token som standard.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Utvärdera svaret för att se hur texten är token i indexet. Observera hur varje term är lägre – bokstäver och uppdelad.

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. Ändra begäran om du vill använda `whitespace` eller `keyword` Analyzer:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Svaret består nu av en enda token, en övre bokstäver med streck som bevaras som en del av strängen. Om du behöver söka efter ett mönster eller en partiell term har du nu en grund för att söka efter en matchning med hjälp av frågemotor.


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> Tänk på att Query parser ofta använder gemener i ett Sök uttryck när du skapar frågeuttrycket. Om du använder en analys som inte innehåller text indata från gemener, och du inte får förväntat resultat, kan detta vara orsaken. Lösningen är att lägga till ett lwower-token-filter.

## <a name="analyzer-definitions"></a>Analys definitioner
 
Oavsett om du utvärderar analyser eller om du flyttar framåt med en speciell konfiguration måste du ange analys på fält definitionen och eventuellt konfigurera själva analysen om du inte använder en inbyggd analys. När du byter analys verktyg behöver du vanligt vis bygga om indexet (släpp, återskapa och Läs in igen). 

### <a name="use-built-in-analyzers"></a>Använda inbyggda analyser

Inbyggda eller fördefinierade analyser kan anges efter namn på en `analyzer`-egenskap för en fält definition, utan ytterligare konfiguration som krävs i indexet. Följande exempel visar hur du ställer in `whitespace` Analyzer för ett fält.

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```
För ytterligare information om alla tillgängliga inbyggda analyser, se [fördefinierade analyserare-lista](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

### <a name="use-custom-analyzers"></a>Använd anpassade analys verktyg

Om du använder en [anpassad analys](index-add-custom-analyzers.md)definierar du den i indexet med en användardefinierad kombination av tokenizer, tokenfilter, med möjliga konfigurations inställningar. Nu ska du referera till den i en fält definition, precis som du skulle göra med en inbyggd analys.

När målet är en tokenisering rekommenderas en anpassad analys som består av en **nyckelords tokenizer** och ett **token med lägre Case-filter** .

+ Nyckelordet tokenizer skapar en enda token för hela innehållet i ett fält.
+ Filtret för gemen token transformerar versaler i gemener. Fråge tolkare är vanligt vis gemena på versaler. Lowercasing homogeniserar indata med de token som gäller för token.

I följande exempel visas en anpassad analys som ger nyckelordet tokenizer och ett gement token-filter.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
]

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> Filtret för `keyword_v2` tokenizer och `lowercase` token är känt för systemet och använder sina standardkonfigurationer, vilket är anledningen till att du kan referera till dem efter namn utan att behöva definiera dem först.

## <a name="tips-and-best-practices"></a>Tips och regelverk

### <a name="tune-query-performance"></a>Justera prestanda för frågor

Om du implementerar den rekommenderade konfigurationen som innehåller keyword_v2 tokenizer och filtrerat token-token, kan du märka en minskning av frågans prestanda på grund av ytterligare token filter-bearbetning över befintliga tokens i ditt index. 

I följande exempel läggs en [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) till för att prefixet ska matchas snabbare. Ytterligare tokens genereras för i 2-25-teckenkombinationer som innehåller tecken: (inte bara MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/kv, MSFT/SQL). Som du kan föreställa dig resulterar det ytterligare tokenisering i ett större index.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
]

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>Använda olika analys verktyg för indexering och bearbetning av frågor

Analys verktyg anropas vid indexering och under frågekörningen. Det är vanligt att använda samma analys för båda, men du kan konfigurera anpassade analys verktyg för varje arbets belastning. Åsidosättningar för Analyzer anges i [index definitionen](https://docs.microsoft.com/rest/api/searchservice/create-index) i ett `analyzers`-avsnitt och refereras sedan till i specifika fält. 

När anpassad analys bara krävs under indexeringen kan du använda anpassad analys för att bara indexera och fortsätta att använda standard Lucene Analyzer (eller en annan analys) för frågor.

Om du vill ange rollbaserad analys kan du ange egenskaper för fältet för var och en, och ange `indexAnalyzer` och `searchAnalyzer` i stället för standard egenskapen `analyzer`.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

### <a name="duplicate-fields-for-different-scenarios"></a>Duplicera fält för olika scenarier

Ett annat alternativ utnyttjar analys tilldelningen per fält för att optimera för olika scenarier. Mer specifikt kan du definiera "featureCode" och "featureCodeRegex" som stöd för vanlig full texts ökning på den första och avancerade mönster matchningen på den andra.

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_customanalyzer"
},
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln förklarar hur analyserare båda bidrar till att fråga efter problem och lösa problem med frågor. I nästa steg ska du ta en närmare titt på analys effekten vid indexering och bearbetning av frågor. Överväg särskilt att använda API: et för analys av text för att returnera utdata i token så att du kan se exakt hur en analys skapas för ditt index.

+ [Språkanalysverktyg](search-language-support.md)
+ [Analys verktyg för text bearbetning i Azure Kognitiv sökning](search-analyzers.md)
+ [Analysera text-API (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Så här fungerar full texts ökning (fråga arkitektur)](search-lucene-query-architecture.md)