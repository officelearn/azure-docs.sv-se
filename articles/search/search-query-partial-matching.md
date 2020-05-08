---
title: Ofullständiga termer, mönster och specialtecken
titleSuffix: Azure Cognitive Search
description: Använd jokertecken, regex och prefix för att matcha hela eller delar av termer i en förfrågan om Azure Kognitiv sökning frågor. Hårda till matchnings mönster som innehåller specialtecken kan lösas med fullständig frågesyntax och anpassade analyser.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 05ff56c904fc48a1041ad40f00110a8ff0fd01f1
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592051"
---
# <a name="partial-term-search-and-patterns-with-special-characters-wildcard-regex-patterns"></a>Partiell terms ökning och mönster med specialtecken (jokertecken, regex, mönster)

En *partiell term sökning* refererar till frågor som består av term fragment, där i stället för en hel period, kanske du bara har Start, mitten eller slutet av termen (kallas ibland för prefix, infix eller suffix). Ett *mönster* kan vara en kombination av fragment, ofta med specialtecken, till exempel bindestreck eller snedstreck som ingår i frågesträngen. Vanliga användnings fall omfattar frågor för delar av ett telefonnummer, en URL, personer eller produkt koder eller sammansatta ord.

Partiell och mönsters ökning kan vara problematiska om indexet inte har villkor i det förväntade formatet. Under den [lexikala analys fasen](search-lucene-query-architecture.md#stage-2-lexical-analysis) av indexering (förutsatt standard analys) tas specialtecken bort, sammansatta och sammansatta strängar delas upp och blank steg tas bort. alla kan orsaka att mönster frågor inte fungerar när ingen matchning hittas. Till exempel visas ett telefonnummer `+1 (425) 703-6214` som (token as `"1"`, `"425"`, `"703"`, `"6214"`) inte i en `"3-62"` fråga eftersom innehållet faktiskt inte finns i indexet. 

Lösningen är att anropa en analys som bevarar en fullständig sträng, inklusive blank steg och specialtecken, om det behövs, så att du kan matcha delar av termer och mönster. Att skapa ytterligare ett fält för en intakt sträng, plus en innehålls bevarad analys, är grunden för lösningen.

> [!TIP]
> Är du bekant med Postman och REST-API: er? [Ladda ned frågan exempel samling](https://github.com/Azure-Samples/azure-search-postman-samples/) för att fråga efter del termer och specialtecken som beskrivs i den här artikeln.

## <a name="what-is-partial-search-in-azure-cognitive-search"></a>Vad är delvis sökning i Azure Kognitiv sökning

I Azure Kognitiv sökning är partiell sökning och mönster tillgängligt i följande formulär:

+ [Prefix](query-simple-syntax.md#prefix-search), till exempel, `search=cap*`som matchar "Cap'n Jack ' s Waterfront Inn" eller "gacc kapitalet". Du kan använda enkel frågesyntax eller fullständig Lucene-frågesyntax för prefixs ökning.

+ [Sökning efter jokertecken](query-lucene-syntax.md#bkmk_wildcard) eller [reguljära uttryck](query-lucene-syntax.md#bkmk_regex) som söker efter ett mönster eller delar av en inbäddad sträng. Jokertecken och reguljära uttryck kräver fullständig Lucene-syntax. Suffix och index frågor formuleras som ett reguljärt uttryck.

  Några exempel på delvis term ökning är följande. För en suffix-fråga, med termen "alfanumerisk", använder du en sökning med jokertecken`search=/.*numeric.*/`() för att hitta en matchning. För en partiell term som innehåller inre tecken, till exempel ett URL-fragment, kan du behöva lägga till escape-tecken. I JSON undantas ett `/` snedstreck med ett omvänt snedstreck. `\` Därför `search=/.*microsoft.com\/azure\/.*/` är syntaxen för URL-fragmentet "Microsoft.com/Azure/".

Som nämnts måste alla ovanstående krav på att indexet innehålla strängar i ett format som främjar mönster matchning, som standard Analyzer inte tillhandahåller. Genom att följa stegen i den här artikeln kan du se till att det nödvändiga innehållet finns för att stödja dessa scenarier.

## <a name="solving-partialpattern-search-problems"></a>Lösa problem med partiell/mönstrad sökning

När du behöver söka efter fragment eller mönster eller specialtecken kan du åsidosätta standard analys verktyget med en anpassad analys som fungerar under enklare tokenisering-regler och behåller hela strängen. Genom att gå tillbaka, ser metoden ut så här:

+ Definiera ett fält för att lagra en intakt version av strängen (förutsatt att du vill analysera och icke-analyserad text)
+ Välj en fördefinierad analys eller definiera en anpassad analys för att mata ut en icke-analyserad intakt sträng
+ Tilldela den anpassade analysen till fältet
+ Bygg och testa indexet

> [!TIP]
> Utvärderings versioner är en iterativ process som kräver upprepade index återuppbyggnadar. Du kan göra det här steget enklare genom att använda Postman, REST-API: er för [create index](https://docs.microsoft.com/rest/api/searchservice/create-index), [ta bort index](https://docs.microsoft.com/rest/api/searchservice/delete-index),[läsa in dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)och [söka dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents). För Läs dokument bör begär ande texten innehålla en liten representativ data uppsättning som du vill testa (till exempel ett fält med telefonnummer eller produkt koder). Med dessa API: er i samma Postman-samling kan du snabbt gå igenom de här stegen.

## <a name="duplicate-fields-for-different-scenarios"></a>Duplicera fält för olika scenarier

Analys verktyg tilldelas per fält, vilket innebär att du kan skapa fält i ditt index för att optimera för olika scenarier. Mer specifikt kan du definiera "featureCode" och "featureCodeRegex" som stöd för vanlig full texts ökning på den första och avancerade mönster matchningen på den andra.

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
  "analyzer": "my_custom_analyzer"
},
```

## <a name="choose-an-analyzer"></a>Välj en analys

När du väljer en analys som producerar token för en hel period är följande analys verktyg vanliga alternativ:

| Analysen | Funktions |
|----------|-----------|
| [språk analys verktyg](index-add-language-analyzers.md) | Bevarar bindestreck i sammansatta ord eller strängar, vokal mutationer och verb-formulär. Om fråga mönster innehåller bindestreck kan det vara tillräckligt med språk analys. |
| [följt](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Innehållet i hela fältet är token som en enskild term. |
| [blank steg](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Separerar bara på blank steg. Termer som innehåller bindestreck eller andra tecken behandlas som en enda token. |
| [anpassad analys](index-add-custom-analyzers.md) | rekommenderas Genom att skapa en anpassad analys kan du ange både tokenizer och token-filtret. Föregående analyser måste användas som de är. Med en anpassad analys kan du välja vilka tokenizers och token filter som ska användas. <br><br>En rekommenderad kombination är [nyckelordet tokenizer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) med ett [lägsta token-filter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). Den fördefinierade [nyckelords analysen](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) innehåller i själva fall inte gemener och versaler, vilket kan orsaka att frågor Miss söker. Med en anpassad analys får du en mekanism för att lägga till det nedre token-filtret. |

Om du använder ett webb-API-testverktyg som Postman kan du lägga till [test analys rest-anropet](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) för att inspektera token-utdata.

Du måste ha ett befintligt index för att arbeta med. Om du har ett befintligt index och ett fält som innehåller bindestreck eller delar av termer kan du prova olika analys verktyg över vissa villkor för att se vilka tokens som genereras.  

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
1. Ändra begäran om att använda `whitespace` eller `keyword` Analyzer:

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
> Tänk på att Query parser ofta använder gemener i ett Sök uttryck när du skapar frågeuttrycket. Om du använder en analys som inte innehåller text indata från gemener, och du inte får förväntat resultat, kan detta vara orsaken. Lösningen är att lägga till ett token med lägre Case-filter, enligt beskrivningen i avsnittet "Använd anpassade analys verktyg" nedan.

## <a name="configure-an-analyzer"></a>Konfigurera en analys
 
Oavsett om du utvärderar analyser eller om du flyttar framåt med en speciell konfiguration måste du ange analys på fält definitionen och eventuellt konfigurera själva analysen om du inte använder en inbyggd analys. När du byter analys verktyg behöver du vanligt vis bygga om indexet (släpp, återskapa och Läs in igen). 

### <a name="use-built-in-analyzers"></a>Använda inbyggda analyser

Inbyggda eller fördefinierade analyser kan anges efter namn på en `analyzer` egenskap i en fält definition, utan ytterligare konfiguration som krävs i indexet. Följande exempel visar hur du ställer in analys funktionen `whitespace` för ett fält. 

För andra scenarier och mer information om andra inbyggda analyser, se [fördefinierade analys listor](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

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

### <a name="use-custom-analyzers"></a>Använd anpassade analys verktyg

Om du använder en [anpassad analys](index-add-custom-analyzers.md)definierar du den i indexet med en användardefinierad kombination av tokenizer, token filter med möjliga konfigurations inställningar. Nu ska du referera till den i en fält definition, precis som du skulle göra med en inbyggd analys.

När målet är en tokenisering rekommenderas en anpassad analys som består av en **nyckelords tokenizer** och ett **token med lägre Case-filter** .

+ Nyckelordet tokenizer skapar en enda token för hela innehållet i ett fält.
+ Filtret för gemen token transformerar versaler i gemener. Fråge tolkare är vanligt vis gemena på versaler. Med gemen-versaler homogeniseras indata med de token som du har sett.

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
],

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
> Filtret `keyword_v2` tokenizer och `lowercase` token är känt för systemet och använder sina standardkonfigurationer, vilket är anledningen till att du kan referera till dem efter namn utan att behöva definiera dem först.

## <a name="build-and-test"></a>Skapa och testa

När du har definierat ett index med analys verktyg och fält definitioner som stöder ditt scenario kan du läsa in dokument som har representativa strängar så att du kan testa partiella sträng frågor. 

I föregående avsnitt förklaras logiken. I det här avsnittet beskrivs varje API som du bör anropa när du testar din lösning. Om du använder ett interaktivt webb test verktyg, till exempel Postman, som tidigare antecknas, kan du snabbt gå igenom dessa uppgifter.

+ [Ta bort index](https://docs.microsoft.com/rest/api/searchservice/delete-index) tar bort ett befintligt index med samma namn så att du kan återskapa det.

+ [Skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index) skapar index strukturen för Sök tjänsten, inklusive analys definitioner och fält med en analys specifikation.

+ [Läs in dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) importerar dokument med samma struktur som ditt index, samt sökbart innehåll. Efter det här steget är indexet redo att fråga eller testa.

+ [Test analys](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) introducerades i [Välj en analys](#choose-an-analyzer). Testa några av strängarna i ditt index med hjälp av en rad olika analys verktyg för att förstå hur termerna är tokens.

+ [Sök i dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents) förklarar hur du skapar en fråga med hjälp av antingen [enkel syntax](query-simple-syntax.md) eller [fullständig Lucene-syntax](query-lucene-syntax.md) för jokertecken och reguljära uttryck.

  För partiella term frågor, till exempel frågan "3-6214" för att hitta en matchning på "+ 1 (425) 703-6214", kan du använda den enkla syntaxen: `search=3-6214&queryType=simple`.

  För infix-och suffix-frågor, till exempel fråga "NUM" eller "numeric för att hitta en matchning på" alfanumerisk ", använder du fullständig Lucene-syntax och ett reguljärt uttryck:`search=/.*num.*/&queryType=full`

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
],

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

Analys verktyg anropas vid indexering och under frågekörningen. Det är vanligt att använda samma analys för båda, men du kan konfigurera anpassade analys verktyg för varje arbets belastning. Åsidosättningar för Analyzer anges i [index definitionen](https://docs.microsoft.com/rest/api/searchservice/create-index) i ett `analyzers` avsnitt och refereras sedan till i specifika fält. 

När anpassad analys bara krävs under indexeringen kan du använda anpassad analys för att bara indexera och fortsätta att använda standard Lucene Analyzer (eller en annan analys) för frågor.

Om du vill ange rollbaserad analys kan du ange egenskaper för fältet för var `indexAnalyzer` och en, och `searchAnalyzer` i stället för standard `analyzer` egenskapen.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln förklarar hur analyserare båda bidrar till att fråga efter problem och lösa problem med frågor. I nästa steg ska du ta en närmare titt på analys effekten vid indexering och bearbetning av frågor. Överväg särskilt att använda API: et för analys av text för att returnera utdata i token så att du kan se exakt hur en analys skapas för ditt index.

+ [Språkanalysverktyg](search-language-support.md)
+ [Analys verktyg för text bearbetning i Azure Kognitiv sökning](search-analyzers.md)
+ [Analysera text-API (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Så här fungerar full texts ökning (fråga arkitektur)](search-lucene-query-architecture.md)