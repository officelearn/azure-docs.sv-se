---
title: Matcha mönster och specialtecken
titleSuffix: Azure Cognitive Search
description: Använd jokertecken- och prefixfrågor för att matcha på hela eller partiella termer i en Azure Cognitive Search-frågebegäran. Svårmatchade mönster som innehåller specialtecken kan lösas med hjälp av fullständig frågesyntax och anpassade analysatorer.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: f78ba5b351a3da46d7b8b3780cf00772c4f3b2ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289319"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>Matcha på mönster och specialtecken (streck)

För frågor som innehåller`-, *, (, ), /, \, =`specialtecken ( ) eller för frågemönster baserade på partiella termer inom en större term, behövs vanligtvis ytterligare konfigurationssteg för att säkerställa att indexet innehåller det förväntade innehållet i rätt format. 

Som standard är ett `+1 (425) 703-6214` telefonnummer som `"1"` `"425"`tokeniseras som , , `"703"`. `"6214"`. Som du kan föreställa `"3-62"`er, söka på , partiella termer som innehåller ett streck, kommer att misslyckas eftersom det innehållet faktiskt inte finns i indexet. 

När du behöver söka på partiella strängar eller specialtecken kan du åsidosätta standardanalysatorn med en anpassad analysator som fungerar under enklare tokeniseringsregler, bevara hela termer, nödvändigt när frågesträngar innehåller delar av en term eller Tecken. Med ett steg tillbaka ser tillvägagångssättet ut så här:

+ Välj en fördefinierad analysator eller definiera en anpassad analysator som ger önskad utdata
+ Tilldela analysatorn till fältet
+ Skapa index och test

I den här artikeln får du hjälp med dessa uppgifter. Den metod som beskrivs här är användbar i andra scenarier: jokertecken och frågor om reguljära uttryck behöver också hela termer som grund för mönstermatchning. 

> [!TIP]
> Att utvärdera analyers är en iterativ process som kräver frekventa indexrekningar. Du kan göra det här steget enklare genom att använda Postman, REST-API:erna för [Skapa index,](https://docs.microsoft.com/rest/api/searchservice/create-index) [Ta bort index,](https://docs.microsoft.com/rest/api/searchservice/delete-index)[läsa in dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)och [söka dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents). För Inläsningsdokument ska begärandetexten innehålla en liten representativ datauppsättning som du vill testa (till exempel ett fält med telefonnummer eller produktkoder). Med dessa API:er i samma Postman-samling kan du snabbt gå igenom de här stegen.

## <a name="choosing-an-analyzer"></a>Välja en analysator

När du väljer en analysator som producerar heltermtoken är följande analysatorer vanliga alternativ:

| Analyzer | Beteenden |
|----------|-----------|
| [Sökord](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Innehållet i hela fältet tokeniseras som en enda term. |
| [Blanksteg](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Separerar endast på vita utrymmen. Termer som innehåller streck eller andra tecken behandlas som en enda token. |
| [anpassad analysator](index-add-custom-analyzers.md) | (rekommenderas) Genom att skapa en anpassad analysator kan du ange både tokenizer- och tokenfiltret. De tidigare analysatorerna måste användas som de är. Med en anpassad analysator kan du välja vilka tokenizers och tokenfilter som ska användas. <br><br>En rekommenderad kombination är [nyckelordstokenizern](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) med ett [lågkärltokenfilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). I sig själv, den fördefinierade [sökordsanalysator](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) inte gemener någon versaler text, vilket kan orsaka frågor misslyckas. En anpassad analysator ger dig en mekanism för att lägga till det gemena tokenfiltret. |

Om du använder ett webb-API-testverktyg som Postman kan du lägga till [REST-anropet för Test Analyzer](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) för att granska tokeniserad utdata. Med tanke på ett befintligt index och ett fält som innehåller streck eller partiella termer kan du prova olika analysatorer över specifika termer för att se vilka token som skickas ut.  

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
> Tänk på att frågetolkar ofta gemener ofta är småster i ett sökuttryck när frågeträdet skapas. Om du använder en analysator som inte gemener text indata, och du inte får förväntade resultat, kan detta vara orsaken. Lösningen är att lägga till ett lwower-case token filter.

## <a name="analyzer-definitions"></a>Definitioner av analysor
 
Oavsett om du utvärderar analysatorer eller går vidare med en specifik konfiguration måste du ange analysatorn på fältdefinitionen och eventuellt konfigurera själva analysatorn om du inte använder en inbyggd analysator. När du byter analysatorer måste du vanligtvis återskapa indexet (släpp, återskapa och ladda om). 

### <a name="use-built-in-analyzers"></a>Använd inbyggda analysatorer

Inbyggda eller fördefinierade analysatorer kan anges med `analyzer` namn på en egenskap i en fältdefinition, utan någon ytterligare konfiguration som krävs i indexet. I följande exempel visas hur `whitespace` du ställer in analysatorn på ett fält.

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
Mer information om alla tillgängliga inbyggda analysatorer finns i [listan Fördefinierade analysatorer](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

### <a name="use-custom-analyzers"></a>Använda anpassade analysatorer

Om du använder en [anpassad analysator](index-add-custom-analyzers.md)definierar du den i indexet med en användardefinierad kombination av tokenizer, tokenfilter, med möjliga konfigurationsinställningar. Referera sedan till den på en fältdefinition, precis som en inbyggd analysator.

När målet är helterm tokenisering, rekommenderas en anpassad analysator som består av en **nyckelordstokenizer** och **ett lågaktykenfilter.**

+ Nyckelordstokenizern skapar en enda token för hela innehållet i ett fält.
+ Det gemena tokenfiltret omvandlar versaler till gemener. Frågetolkare ger vanligtvis gemener för versaler. Att minska omutseendet homogeniserar ingångarna med de tokeniserade termerna.

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

### <a name="duplicate-fields-for-different-scenarios"></a>Duplicera fält för olika scenarier

Ett annat alternativ utnyttjar datatilldelningen per fältanalysator för att optimera för olika scenarier. Specifikt kan du definiera "featureCode" och "featureCodeRegex" för att stödja regelbundna fulltextsökning på den första och avancerade mönstermatchningen på den andra.

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

I den här artikeln beskrivs hur analysatorer både bidrar till frågeproblem och löser frågeproblem. Som ett nästa steg bör du titta närmare på analysatorpåverkan på indexering och frågebearbetning. Överväg särskilt att använda API:et analysera text för att returnera tokeniserad utdata så att du kan se exakt vad en analysator skapar för indexet.

+ [Språkanalysverktyg](search-language-support.md)
+ [Analysatorer för textbearbetning i Azure Cognitive Search](search-analyzers.md)
+ [Analysera text-API (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Så här fungerar fulltextsökning (frågearkitektur)](search-lucene-query-architecture.md)
