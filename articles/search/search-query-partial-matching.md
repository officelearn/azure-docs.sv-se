---
title: Partiella termer, mönster och specialtecken
titleSuffix: Azure Cognitive Search
description: Använd jokertecken-, regex- och prefixfrågor för att matcha hela eller partiella termer i en Azure Cognitive Search-frågebegäran. Svårmatchade mönster som innehåller specialtecken kan lösas med hjälp av fullständig frågesyntax och anpassade analysatorer.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: db60a864ff29ff9eccdcfbdc0bd63587375d4bbd
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114966"
---
# <a name="partial-term-search-and-patterns-with-special-characters-wildcard-regex-patterns"></a>Partiell termsökning och mönster med specialtecken (jokertecken, regex, mönster)

En *partiell termsökning* refererar till frågor som består av termfragment, där du i stället för en hel term kanske bara har början, mitten eller slutet av termen (kallas ibland prefix- eller infix- eller suffixfrågor). Ett *mönster* kan vara en kombination av fragment, ofta med specialtecken som streck eller snedstreck som ingår i frågesträngen. Vanliga användningsfall är att fråga efter delar av ett telefonnummer, URL, personer eller produktkoder eller sammansatta ord.

Partiell sökning och mönstersökning kan vara problematiskt om indexet inte har termer i det förväntade formatet. Under den [lexikala analysfasen](search-lucene-query-architecture.md#stage-2-lexical-analysis) av indexering (förutsatt att standardstandardanalysatorn) ignoreras specialtecken, sammansatta och sammansatta strängar delas upp och blanktecken tas bort. alla som kan orsaka mönsterfrågor misslyckas när ingen matchning hittas. Ett telefonnummer `+1 (425) 703-6214` som (tokeniserat som `"1"` `"425"`, `"703"` `"6214"`, ) visas till `"3-62"` exempel inte i en fråga eftersom innehållet inte finns i indexet. 

Lösningen är att anropa en analysator som bevarar en komplett sträng, inklusive blanksteg och specialtecken om det behövs, så att du kan matcha på partiella termer och mönster. Att skapa ytterligare ett fält för en intakt sträng, plus att använda en innehållsbevarande analysator, är grunden för lösningen.

## <a name="what-is-partial-search-in-azure-cognitive-search"></a>Vad är partiell sökning i Azure Cognitive Search

I Azure Cognitive Search är partiell sökning och mönster tillgängligt i följande formulär:

+ [Prefixsökning](query-simple-syntax.md#prefix-search), `search=cap*`till exempel , matchning på "Cap'n Jack's Waterfront Inn" eller "Gacc Capital". Du kan använda den enkla frågesyntaxen eller den fullständiga Lucene-frågesyntaxen för prefixsökning.

+ [Jokerteckensökning](query-lucene-syntax.md#bkmk_wildcard) eller [Reguljära uttryck](query-lucene-syntax.md#bkmk_regex) som söker efter ett mönster eller delar av en inbäddad sträng. Jokertecken och reguljära uttryck kräver den fullständiga Lucene-syntaxen. Suffix- och indexfrågor formuleras som ett reguljärt uttryck.

  Några exempel på partiell termsökning är följande. För en suffixfråga, med tanke på termen "alfanumerisk", använder du en jokerteckensökning (`search=/.*numeric.*/`) för att hitta en matchning. För en delterm som innehåller invändiga tecken, till exempel ett URL-fragment, kan du behöva lägga till escape-tecken. I JSON, en `/` framåt snedstreck `\`flyr med en bakåt snedstreck . Som sådan `search=/.*microsoft.com\/azure\/.*/` är syntaxen för URL-fragmentet "microsoft.com/azure/".

Som nämnts kräver alla ovanstående att indexet innehåller strängar i ett format som bidrar till mönstermatchning, vilket standardanalysatorn inte tillhandahåller. Genom att följa stegen i den här artikeln kan du se till att det finns nödvändigt innehåll för att stödja dessa scenarier.

## <a name="solving-partialpattern-search-problems"></a>Lösa partiella/mönstersökproblem

När du behöver söka efter fragment eller mönster eller specialtecken kan du åsidosätta standardanalysatorn med en anpassad analysator som fungerar under enklare tokeniseringsregler och behålla hela strängen. Med ett steg tillbaka ser tillvägagångssättet ut så här:

+ Definiera ett fält för att lagra en intakt version av strängen (förutsatt att du vill ha analyserad och icke-analyserad text)
+ Välj en fördefinierad analysator eller definiera en anpassad analysator för att mata ut en icke-analyserad intakt sträng
+ Tilldela den anpassade analysatorn till fältet
+ Skapa och testa indexet

> [!TIP]
> Utvärdera analysatorer är en iterativ process som kräver täta index återuppbyggningar. Du kan göra det här steget enklare genom att använda Postman, REST-API:erna för [Skapa index,](https://docs.microsoft.com/rest/api/searchservice/create-index) [Ta bort index,](https://docs.microsoft.com/rest/api/searchservice/delete-index)[läsa in dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)och [söka dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents). För Inläsningsdokument ska begärandetexten innehålla en liten representativ datauppsättning som du vill testa (till exempel ett fält med telefonnummer eller produktkoder). Med dessa API:er i samma Postman-samling kan du snabbt gå igenom de här stegen.

## <a name="duplicate-fields-for-different-scenarios"></a>Duplicera fält för olika scenarier

Analysatorer tilldelas per fält, vilket innebär att du kan skapa fält i indexet för att optimera för olika scenarier. Specifikt kan du definiera "featureCode" och "featureCodeRegex" för att stödja regelbundna fulltextsökning på den första och avancerade mönstermatchningen på den andra.

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

## <a name="choose-an-analyzer"></a>Välj en analysator

När du väljer en analysator som producerar heltermtoken är följande analysatorer vanliga alternativ:

| Analyzer | Beteenden |
|----------|-----------|
| [Sökord](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Innehållet i hela fältet tokeniseras som en enda term. |
| [Blanksteg](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Separerar endast på vita utrymmen. Termer som innehåller streck eller andra tecken behandlas som en enda token. |
| [anpassad analysator](index-add-custom-analyzers.md) | (rekommenderas) Genom att skapa en anpassad analysator kan du ange både tokenizer- och tokenfiltret. De tidigare analysatorerna måste användas som de är. Med en anpassad analysator kan du välja vilka tokenizers och tokenfilter som ska användas. <br><br>En rekommenderad kombination är [nyckelordstokenizern](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) med ett [lågkärltokenfilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). I sig själv, den fördefinierade [sökordsanalysator](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) inte gemener någon versaler text, vilket kan orsaka frågor misslyckas. En anpassad analysator ger dig en mekanism för att lägga till det gemena tokenfiltret. |

Om du använder ett webb-API-testverktyg som Postman kan du lägga till [REST-anropet för Test Analyzer](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) för att granska tokeniserad utdata.

Du måste ha ett befintligt index att arbeta med. Med tanke på ett befintligt index och ett fält som innehåller streck eller partiella termer kan du prova olika analysatorer över specifika termer för att se vilka token som skickas ut.  

1. Kontrollera standardanalysatorn för att se hur termer tokeniseras som standard.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Utvärdera svaret för att se hur texten tokeniseras i indexet. Lägg märke till hur varje term är gemener och delas upp.

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
1. Ändra begäran om `whitespace` att `keyword` använda eller analysatorn:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Nu består svaret av en enda token, versaler, med streck bevarade som en del av strängen. Om du behöver söka på ett mönster eller en partiell term har frågemotorn nu grunden för att hitta en matchning.


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
> Tänk på att frågetolkar ofta gemener ofta är småster i ett sökuttryck när frågeträdet skapas. Om du använder en analysator som inte gemener text indata, och du inte får förväntade resultat, kan detta vara orsaken. Lösningen är att lägga till ett lågskrivligt tokenfilter, enligt beskrivningen i avsnittet "Använd anpassade analysatorer" nedan.

## <a name="configure-an-analyzer"></a>Konfigurera en analysator
 
Oavsett om du utvärderar analysatorer eller går vidare med en specifik konfiguration måste du ange analysatorn på fältdefinitionen och eventuellt konfigurera själva analysatorn om du inte använder en inbyggd analysator. När du byter analysatorer måste du vanligtvis återskapa indexet (släpp, återskapa och ladda om). 

### <a name="use-built-in-analyzers"></a>Använd inbyggda analysatorer

Inbyggda eller fördefinierade analysatorer kan anges med `analyzer` namn på en egenskap i en fältdefinition, utan någon ytterligare konfiguration som krävs i indexet. I följande exempel visas hur `whitespace` du ställer in analysatorn på ett fält. Mer information om tillgängliga inbyggda analysatorer finns i [listan Fördefinierade analysatorer](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

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

### <a name="use-custom-analyzers"></a>Använda anpassade analysatorer

Om du använder en [anpassad analysator](index-add-custom-analyzers.md)definierar du den i indexet med en användardefinierad kombination av tokenizer, tokenfilter, med möjliga konfigurationsinställningar. Referera sedan till den på en fältdefinition, precis som en inbyggd analysator.

När målet är helterm tokenisering, rekommenderas en anpassad analysator som består av en **nyckelordstokenizer** och **ett lågaktykenfilter.**

+ Nyckelordstokenizern skapar en enda token för hela innehållet i ett fält.
+ Det gemena tokenfiltret omvandlar versaler till gemener. Frågetolkare ger vanligtvis gemener för versaler. Lägre hölje homogeniserar ingångarna med de tokeniserade termerna.

Följande exempel illustrerar en anpassad analysator som tillhandahåller nyckelordstokenizern och ett gemens tokenfilter.

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
> Tokenizer- `keyword_v2` `lowercase` och tokenfiltret är kända för systemet och använder deras standardkonfigurationer, vilket är anledningen till att du kan referera till dem med namn utan att behöva definiera dem först.

## <a name="build-and-test"></a>Skapa och testa

När du har definierat ett index med analysatorer och fältdefinitioner som stöder ditt scenario läser du in dokument som har representativa strängar så att du kan testa partiella strängfrågor. 

I föregående avsnitt förklarades logiken. Det här avsnittet går igenom varje API som du bör anropa när du testar din lösning. Som tidigare nämnts, om du använder ett interaktivt webbtestverktyg som Postman, kan du snabbt gå igenom dessa uppgifter.

+ [Ta bort index](https://docs.microsoft.com/rest/api/searchservice/delete-index) tar bort ett befintligt index med samma namn så att du kan återskapa det.

+ [Skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index) skapar indexstrukturen på söktjänsten, inklusive analysatorer och fält med en analysatorspecifikation.

+ [Läs in Dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) importerar dokument med samma struktur som ditt index, samt sökbart innehåll. Efter det här steget är indexet redo att fråga eller testa.

+ [Test Analyzer](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) introducerades i [Välj en analysator](#choose-an-analyzer). Testa några av strängarna i indexet med hjälp av en mängd olika analysatorer för att förstå hur termer tokeniseras.

+ [Sökdokument](https://docs.microsoft.com/rest/api/searchservice/search-documents) förklarar hur du skapar en frågebegäran med hjälp av [antingen enkel syntax](query-simple-syntax.md) eller fullständig [Lucene-syntax](query-lucene-syntax.md) för jokertecken och reguljära uttryck.

  För partiella termfrågor, till exempel att fråga "3-6214" för att hitta en matchning på "+1 (425) 703-6214", kan du använda den enkla syntaxen: `search=3-6214&queryType=simple`.

  För infix- och suffixfrågor, till exempel frågar "num" eller "numeriskt för att hitta en matchning på "alfanumerisk", använder du den fullständiga Lucene-syntaxen och ett reguljärt uttryck:`search=/.*num.*/&queryType=full`

## <a name="tips-and-best-practices"></a>Tips och regelverk

### <a name="tune-query-performance"></a>Justera prestanda för frågor

Om du implementerar den rekommenderade konfigurationen som innehåller keyword_v2 tokenizer och lågpristotofilter, kan du märka en minskning av frågeprestanda på grund av ytterligare tokenfilterbearbetning över befintliga token i indexet. 

I följande exempel läggs ett [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) till för att göra prefixmatchningar snabbare. Ytterligare token genereras för i 2-25 teckenkombinationer som innehåller tecken: (inte bara MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/SQ, MSFT/SQL). Som du kan föreställa er resulterar den ytterligare tokeniseringen i ett större index.

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

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>Använda olika analysatorer för indexering och frågebearbetning

Analysatorer anropas under indexering och under frågekörning. Det är vanligt att använda samma analysator för båda men du kan konfigurera anpassade analysatorer för varje arbetsbelastning. Analysoridosättning anges i [indexdefinitionen](https://docs.microsoft.com/rest/api/searchservice/create-index) `analyzers` i ett avsnitt och refereras sedan till i specifika fält. 

När anpassad analys endast krävs under indexering kan du använda den anpassade analysatorn för att bara indexera och fortsätta att använda standard Lucene-analysatorn (eller en annan analysator) för frågor.

Om du vill ange rollspecifik analys kan du ange `indexAnalyzer` egenskaper `searchAnalyzer` i fältet `analyzer` för var och en, ange och i stället för standardegenskapen.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur analysatorer både bidrar till frågeproblem och löser frågeproblem. Som ett nästa steg bör du titta närmare på analysatorpåverkan på indexering och frågebearbetning. Överväg särskilt att använda API:et analysera text för att returnera tokeniserad utdata så att du kan se exakt vad en analysator skapar för indexet.

+ [Språkanalysverktyg](search-language-support.md)
+ [Analysatorer för textbearbetning i Azure Cognitive Search](search-analyzers.md)
+ [Analysera text-API (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Så här fungerar fulltextsökning (frågearkitektur)](search-lucene-query-architecture.md)