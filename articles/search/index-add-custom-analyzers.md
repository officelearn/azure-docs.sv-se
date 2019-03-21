---
title: Lägg till anpassade analysverktyg – Azure Search
description: Ändra text tokenizers och tecknet filter som används i Azure Search-fulltextsökningsfrågor.
ms.date: 02/14/2019
services: search
ms.service: search
ms.topic: conceptual
author: Yahnoosh
ms.author: jlembicz
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
ms.openlocfilehash: 8eb762e8a18ea5de25413681894f692628493a2f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842863"
---
# <a name="add-custom-analyzers-to-an-azure-search-index"></a>Lägga till anpassade analysverktyg i ett Azure Search-index

En *anpassat analysverktyg* är en viss typ av [text analyzer](search-analyzers.md) som består av en användardefinierad kombination av befintliga tokenizer och valfritt filter. Du kan anpassa textbearbetning i sökmotorn att uppnå specifika resultat genom att kombinera tokenizers och filter på nya sätt. Du kan till exempel skapa ett anpassat analysverktyg med en *char filter* att ta bort HTML-kod innan Textinmatningar är tokeniserad.

 Du kan definiera flera anpassade analysverktyg varierar kombination av filter, men varje fält kan bara använda en analyzer för indexering analys och en för search-analys. En illustration av hur en kund analyzer ser ut finns i [anpassad analyzer exempel](search-analyzers.md#Example1).

## <a name="overview"></a>Översikt

 Rollen för en [motorn för fulltextsökning](search-lucene-query-architecture.md), enkelt uttryckt är att bearbeta och lagra dokument på ett sätt som gör det möjligt för effektiva frågor och hämtning av filer. På hög nivå handlar allt om att extrahera viktiga ord från dokument, placera dem i ett index och sedan använda indexet för att hitta dokument som matchar ord i en viss fråga. Extrahera ord från dokument och sökfrågor kallas *lexikal analys*. Komponenter som utför lexikal analys kallas *analysverktyg*.

 I Azure Search kan du välja från en uppsättning fördefinierade språkoberoende analysverktyg i den [analysverktyg](#AnalyzerTable) tabell eller språkspecifika analysverktyg som anges i [språkanalysverktyg &#40;Azure Search Service REST API&#41;](index-add-language-analyzers.md). Du har också möjlighet att ange dina egna anpassade analysverktyg.  

 Ett anpassat analysverktyg låter dig ta kontroll över att konvertera text till vara och sökbara token. Det är en användardefinierad konfiguration som består av en enda fördefinierade tokenizer, ett eller flera token filter och en eller flera char-filter. Tokenizer ansvarar för att dela upp text i token och token filtren för att ändra token som skickas från tokenizer. Char-filter används för att förbereda indatatexten innan den bearbetas av tokenizer. Char-filter kan till exempel ersätta vissa tecken och symboler.

 Populära scenarier som anpassade analysverktyg är:  

- Fonetisk sökning. Lägg till ett fonetiska, filter för att söka baserat på hur ett ord låter, inte hur de stavas.  

- Inaktivera lexikal analys. Använda nyckelordet analyzer för att skapa sökbara fält som inte har analyserats.  

- Prefix eller suffix till snabb sökning. Lägga till filtret Edge N-gram token på index prefix ord att aktivera snabb prefix matchning. Kombinera det med filtret för omvänd token för att suffix matchar.  

- Anpassade tokenisering. Till exempel använda blanksteg tokenizer att dela upp meningar i token med blanksteg som avgränsare  

- ASCII-standardvikning. Lägg till Standard ASCII vikning filter för att normalisera diakritiska tecken som ö eller ê i sökvillkor.  

  Den här sidan innehåller en lista över stöds analysverktyg, tokenizers, token filter och char-filter. Du kan också hitta en beskrivning av ändringar i indexdefinitionen med ett användningsexempel på. Mer bakgrundsinformation om den underliggande tekniken som används i Azure Search-implementeringen, finns [Analysis-sammanfattning av paketet (Lucene)](https://lucene.apache.org/core/4_10_0/core/org/apache/lucene/codecs/lucene410/package-summary.html). Exempel på analyzer konfigurationer finns [lägga till analysverktyg i Azure Search](search-analyzers.md#examples).

## <a name="validation-rules"></a>Valideringsregler  
 Namnen på analysverktyg, tokenizers, token filter och char filter måste vara unikt och får inte vara samma som något av de fördefinierade analysverktyg, tokenizers, token filter eller char filter. Se den [Egenskapsreferens](#PropertyReference) för namn redan används.

## <a name="create-custom-analyzers"></a>Skapa anpassade analysverktyg
 Du kan definiera anpassade analysverktyg vid tidpunkten för skapandet av index. Syntaxen för att ange ett anpassat analysverktyg beskrivs i det här avsnittet. Du kan också bekanta dig med syntaxen genom att granska exempel definitioner i [lägga till analysverktyg i Azure Search](search-analyzers.md#examples).  

 En analyzer definition innehåller ett namn, en typ, ett eller flera char-filter, högst en tokenizer och ett eller flera token filter för efter tokenisering bearbetning. Char-filter tillämpas innan tokenisering. Token filter och char-filter tillämpas från vänster till höger.

 Den `tokenizer_name` är namnet på en tokenizer `token_filter_name_1` och `token_filter_name_2` är namnen på token filter och `char_filter_name_1` och `char_filter_name_2` är namnen på char filter (se den [Tokenizers](#Tokenizers), [ Token filter](#TokenFilters) och Char filtrerar tabeller för giltiga värden).

Analyzer-definitionen är en del av det största indexet. Se [Create Index-API](https://docs.microsoft.com/rest/api/searchservice/create-index) information om resten av indexet.

```
"analyzers":(optional)[
   {
      "name":"name of analyzer",
      "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenizer":"tokenizer_name",
      "tokenFilters":[
         "token_filter_name_1",
         "token_filter_name_2"
      ]
   },
   {
      "name":"name of analyzer",
      "@odata.type":"#analyzer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenizers":(optional)[
   {
      "name":"tokenizer_name",
      "@odata.type":"#tokenizer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

> [!NOTE]  
>  Anpassade analysverktyg som du skapar exponeras inte i Azure-portalen. Det enda sättet att lägga till ett anpassat analysverktyg är kod som anropar API: et när du definierar ett index.  

 Du kan placera det här avsnittet var som helst i brödtexten i en begäran om att skapa index i en Indexdefinition men vanligtvis är den i slutet:  

```
{
  "name": "name_of_index",
  "fields": [ ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "analyzers":(optional)[ ],
  "charFilters":(optional)[ ],
  "tokenizers":(optional)[ ],
  "tokenFilters":(optional)[ ]
}
```

Definitioner för char-filter, tokenizers och token filter har lagts till indexet endast om du bygger anpassade alternativ. Du använder ett befintligt filter eller tokenizer som – är, ange den efter namn i analyzer-definitionen.

<a name="Testing custom analyzers"></a>

## <a name="test-custom-analyzers"></a>Testa anpassade analysverktyg

Du kan använda den **Test Analyzer åtgärden** i den [REST API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) att se hur en analyzer delar angivna text i token.

**Förfrågan**
```
  POST https://[search service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
  Content-Type: application/json
    api-key: [admin key]

  {
     "analyzer":"my_analyzer",
     "text": "Vis-à-vis means Opposite"
  }
```
**Svar**
```
  {
    "tokens": [
      {
        "token": "vis_a_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "vis_à_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "means",
        "startOffset": 10,
        "endOffset": 15,
        "position": 1
      },
      {
        "token": "opposite",
        "startOffset": 16,
        "endOffset": 24,
        "position": 2
      }
    ]
  }
```

## <a name="update-custom-analyzers"></a>Uppdatera anpassade analysverktyg

När en analyzer, en tokenizer, ett token filter eller ett char-filter har definierats kan ändras inte. Nya värden kan läggas till ett befintligt index endast om den `allowIndexDowntime` är inställd på true i begäran om uppdatering index:

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

Den här åtgärden tar ditt index offline för minst ett par sekunder, och din indexerings- och begäranden att misslyckas. Prestanda- och skrivbehörighet tillgängligheten för indexet kan skrivas i flera minuter efter att indexet är uppdaterade eller längre för mycket stora index, men dessa effekter är tillfälliga och så småningom lösa på egen hand.

 <a name="ReferenceIndexAttributes"></a>

## <a name="analyzer-reference"></a>Analyzer-referens

I tabellerna nedan lista konfigurationsegenskaper för analysverktyg, tokenizers, token filter och char filterområdet för en Indexdefinition. Strukturen för en analyzer, tokenizer eller filter i ditt index består av dessa attribut. Värdet tilldelningsinformation finns i den [Egenskapsreferens](#PropertyReference).

### <a name="analyzers"></a>Analysverktyg

Analysverktyg, indexattribut variera beroende på den om du använder fördefinierade eller anpassade analysverktyg.

#### <a name="predefined-analyzers"></a>Fördefinierade analysverktyg

|||  
|-|-|  
|Namn|Det får bara innehålla bokstäver, siffror, blanksteg, bindestreck eller understreck, inledas och avslutas med alfanumeriska tecken och är begränsat till 128 tecken.|  
|Type|Typ av Analyzer från listan över stöds analysverktyg. Se den **analyzer_type** kolumnen i den [analysverktyg](#AnalyzerTable) tabellen nedan.|  
|Alternativ|Måste vara giltiga alternativ för en fördefinierad analyzer som anges i den [analysverktyg](#AnalyzerTable) tabellen nedan.|  

#### <a name="custom-analyzers"></a>Anpassade analysverktyg

|||  
|-|-|  
|Namn|Det får bara innehålla bokstäver, siffror, blanksteg, bindestreck eller understreck, inledas och avslutas med alfanumeriska tecken och är begränsat till 128 tecken.|  
|Type|Måste vara ”#Microsoft.Azure.Search.CustomAnalyzer”.|  
|CharFilters|Inställt på någon av fördefinierade char filter listas i den [Char filter](#CharFilter) tabell eller ett anpassat char-filter som anges i indexdefinitionen.|  
|Tokenizer|Krävs. Inställt på någon av fördefinierade tokenizers som anges i den [Tokenizers](#Tokenizers) tabellen nedan eller en anpassad tokenizer som anges i indexdefinitionen.|  
|TokenFilters|Inställt på någon av fördefinierade token filter som listas i den [Token filter](#TokenFilters) tabell eller ett anpassat token filter som anges i indexdefinitionen.|  

<a name="CharFilter"></a>

### <a name="char-filters"></a>Char-filter

 Ett char-filter används för att förbereda indatatexten innan den bearbetas av tokenizer. De kan till exempel ersätta vissa tecken och symboler. Du kan ha flera char-filter i ett anpassat analysverktyg. Char filter kör i angiven ordning.  

|||  
|-|-|  
|Namn|Det får bara innehålla bokstäver, siffror, blanksteg, bindestreck eller understreck, inledas och avslutas med alfanumeriska tecken och är begränsat till 128 tecken.|  
|Type|Char filtertyp i listan över stöds char-filter. Se **char_filter_type** kolumnen i den [Char filter](#CharFilter) tabellen nedan.|  
|Alternativ|Måste vara giltiga alternativ för en viss [Char filter](#CharFilter) typen.|  

### <a name="tokenizers"></a>Tokenizers

 En tokenizer delas upp kontinuerlig text i en sekvens av token, till exempel dela upp en mening i orden.  

 Du kan ange exakt en tokenizer per anpassat analysverktyg. Om du behöver mer än en tokenizer kan du skapa flera anpassade analysverktyg och tilldela dem på basis av fält i taget i ditt indexschema.  
Ett anpassat analysverktyg kan använda en fördefinierad tokenizer med antingen standard eller anpassade alternativ.  

|||  
|-|-|  
|Namn|Det får bara innehålla bokstäver, siffror, blanksteg, bindestreck eller understreck, inledas och avslutas med alfanumeriska tecken och är begränsat till 128 tecken.|  
|Type|Tokenizer namnet från listan över stöds tokenizers. Se **tokenizer_type** kolumnen i den [Tokenizers](#Tokenizers) tabellen nedan.|  
|Alternativ|Måste vara giltiga alternativ för en viss tokenizer-typ som anges i den [Tokenizers](#Tokenizers) tabellen nedan.|  

### <a name="token-filters"></a>Token filter

 Ett token filter används för att filtrera bort eller ändra de token som genererats av en tokenizer. Du kan till exempel ange ett gemener filter som konverterar alla tecken till gemener.   
Du kan ha flera token filter i ett anpassat analysverktyg. Token filter kör i angiven ordning.  

|||  
|-|-|  
|Namn|Det får bara innehålla bokstäver, siffror, blanksteg, bindestreck eller understreck, inledas och avslutas med alfanumeriska tecken och är begränsat till 128 tecken.|  
|Type|Token filternamnet i listan över stöds token filter. Se **token_filter_type** kolumnen i den [Token filter](#TokenFilters) tabellen nedan.|  
|Alternativ|Måste vara [Token filter](#TokenFilters) av en viss token filtertyp.|  

<a name="PropertyReference"></a>  

## <a name="property-reference"></a>Referens för egenskaper

Det här avsnittet innehåller de giltiga värdena för attribut som anges i definitionen av ett anpassat analysverktyg, tokenizer, char-filter eller token filter i ditt index. Analysverktyg, tokenizers och filter som implementeras med hjälp av Apache Lucene ha länkar till Lucene API-dokumentationen.

<a name="AnalyzerTable"></a>

###  <a name="predefined-analyzers-reference"></a>Fördefinierade analysverktyg referens

|**analyzer_name**|**analyzer_type**<sup>1  </sup>|**Beskrivning och alternativ**|  
|-|-|-|  
|[keyword](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (typen gäller endast när alternativ är tillgängliga) |Behandlar hela innehållet i ett fält som en enskild token. Detta är användbart för data som postnummer, ID: N och vissa produktnamn.|  
|[pattern](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PatternAnalyzer|Flexibelt delar upp text i villkor via ett mönster för reguljärt uttryck.<br /><br /> **Alternativ**<br /><br /> gemener (typ: bool) – anger om villkoren är ska skrivas med gemen. Standardvärdet är sant.<br /><br /> [mönstret](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (typ: sträng) – ett mönster för reguljärt uttryck som matchar token avgränsare. Standardvärdet är \w+.<br /><br /> [flaggor](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (typ: sträng)-flaggor för reguljärt uttryck. Standardvärdet är en tom sträng. Tillåtna värden: CANON_EQ, CASE_INSENSITIVE, KOMMENTARER, DOTALL, LITTERALVÄRDE, MULTILINE, UNICODE_CASE, UNIX_LINES<br /><br /> stoppord (typ: Strängmatrisen) – en lista över stoppord. Standardvärdet är en tom lista.|  
|[simple](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(typen gäller endast när alternativ är tillgängliga) |Delar upp text på inte är bokstäver och konverterar dem till gemener. |  
|[standard](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) <br />(Kallas även standard.lucene)|StandardAnalyzer|Analysverktyget från Lucene, består av standard tokenizer, gemener filtret och stoppa filter.<br /><br /> **Alternativ**<br /><br /> maxTokenLength (typ: int)-den maximala längden för token. Standardvärdet är 255. Token som är längre än maxlängden delas. Maximal längd för token som kan användas är 300 tecken.<br /><br /> stoppord (typ: Strängmatrisen) – en lista över stoppord. Standardvärdet är en tom lista.|  
|standardasciifolding.lucene|(typen gäller endast när alternativ är tillgängliga) |Standard analyzer med Ascii vikning filter. |  
|[Stanna](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|Delar upp text på inte är bokstäver, gäller de små och stoppord token filter.<br /><br /> **Alternativ**<br /><br /> stoppord (typ: Strängmatrisen) – en lista över stoppord. Standardvärdet är en fördefinierad lista för engelska. |  
|[whitespace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(typen gäller endast när alternativ är tillgängliga) |En analyzer som använder blanksteg tokenizer. Token som är längre än 255 tecken delas.|  

 <sup>1</sup> analyzer typer har alltid ett prefix i kod med ”#Microsoft.Azure.Search” så att ”PatternAnalyzer” faktiskt anges som ”#Microsoft.Azure.Search.PatternAnalyzer”. Vi har tagit bort prefixet kortfattat, men prefixet som krävs i din kod. 
 
Analyzer_type tillhandahålls endast för analysverktyg som kan anpassas. Om det finns inga alternativ så är fallet med nyckelordet analyzer, finns det inga associerade #Microsoft.Azure.Search-typen.


<a name="CharFilter"></a>

###  <a name="char-filters-reference"></a>Char filter-referens

I tabellen nedan är tecknet filtren som implementeras med hjälp av Apache Lucene länkade Lucene API-dokumentationen.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**Beskrivning och alternativ**|  
|-|-|-|
|[html_strip](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Skapar ett char-filter som försöker ta bort HTML.|  
|[mapping](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|Ett char-filter som gäller mappningar som definierats med alternativet mappningar. Matchar är girig (längsta mönstermatchning i en viss tidpunkt wins). Ersättning har tillåtelse att vara en tom sträng.<br /><br /> **Alternativ**<br /><br /> mappningar (typ: Strängmatrisen) – en lista över mappningar av följande format ”: a = > b” (alla förekomster av tecknet ”a” ersätts med ”b”). Krävs.|  
|[pattern_replace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatternReplaceCharFilter|Ett char-filter som ersätter tecken i strängen. Den använder ett reguljärt uttryck för att identifiera teckensekvenser att bevara och en ersättning för att identifiera tecken för att ersätta. Till exempel textinmatning = ”aa-bb aa bb”, pattern="(aa)\\\s+(bb)” ersättning = ”$1 #$2”, resultat = ”aa #bb aa #bb”.<br /><br /> **Alternativ**<br /><br /> mönstret (typ: sträng) – krävs.<br /><br /> ersättning (typ: sträng) – krävs.|  

 <sup>1</sup> char filtertyper föregås alltid i kod med ”#Microsoft.Azure.Search” så att ”MappingCharFilter” faktiskt anges som ”#Microsoft.Azure.Search.MappingCharFilter. Vi har tagit bort prefix om du vill minska bredden på tabellen, men kom ihåg att inkludera den i din kod. Observera att char_filter_type finns bara för filter som kan anpassas. Om det finns inga alternativ så är fallet med html_strip, finns det inga associerade #Microsoft.Azure.Search-typen.

<a name="Tokenizers"></a>

###  <a name="tokenizers-reference"></a>Tokenizers referens

I tabellen nedan länkas tokenizers som implementeras med hjälp av Apache Lucene Lucene API-dokumentationen.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**Beskrivning och alternativ**|  
|-|-|-|  
|[classic](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|Grammatik baserat tokenizer som passar för bearbetning av dokument för de flesta europeiska språk.<br /><br /> **Alternativ**<br /><br /> maxTokenLength (typ: int)-den maximala längden för token. Standard: 255 maximala: 300. Token som är längre än maxlängden delas.|  
|[edgeNGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|Tokenizes indata från en kant i n-gram beroende storlek.<br /><br /> **Alternativ**<br /><br /> minGram (typ: int)-standard: 1, max: 300.<br /><br /> maxGram (typ: int)-standard: 2, maximalt: 300. Måste vara större än minGram.<br /><br /> tokenChars (typ: Strängmatrisen)-tecken klasser att token. Tillåtna värden: <br />”bokstaven”, ”siffra”, ”blanksteg”, ”skiljetecken”, ”symbolen”. Som standard till en tom matris - ser till att alla tecken. |  
|[keyword_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|Genererar hela indata som en enda token.<br /><br /> **Alternativ**<br /><br /> maxTokenLength (typ: int)-den maximala längden för token. Standard: 256, maximal: 300. Token som är längre än maxlängden delas.|  
|[letter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(typen gäller endast när alternativ är tillgängliga)  |Delar upp text på inte är bokstäver. Token som är längre än 255 tecken delas.|  
|[lowercase](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(typen gäller endast när alternativ är tillgängliga)  |Delar upp text på inte är bokstäver och konverterar dem till gemener. Token som är längre än 255 tecken delas.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Delar upp text med språkspecifika regler.<br /><br /> **Alternativ**<br /><br /> maxTokenLength (typ: int)-token maxlängden, standard: 255 maximala: 300. Token som är längre än maxlängden delas. Token som är längre än 300 tecken först är uppdelade i token med längden 300 och sedan var och en av dessa token delas utifrån maxTokenLength uppsättningen.<br /><br />isSearchTokenizer (typ: bool) – Ställ in som SANT om den används som Sök tokenizer inställt på falskt om den används som indexering tokenizer. <br /><br /> språk (typ: sträng)-språket som ska användas, standard ”engelska”. Tillåtna värden är:<br />”bangla”, ”bulgariska”, ”katalanska”, ”chineseSimplified”, ”chineseTraditional”, ”kroatiska”, ”tjeckiska”, ”danska”, ”nederländska”, ”english”, ”franska”, ”tyska”, ”grekiska”, ”gujarati”, ”hindi”, ”isländsk”, ”indonesiska”, ”italienska”, ”japanska”, ”kannada” ” koreanska ”,” malay ”,” malayalam ”,” marathi ”,” norwegianBokmaal ”,” polska ”,” portugisiska ”,” portugueseBrazilian ”,” panjabi ”,” rumänska ”,” ryska ”,” serbianCyrillic ”,” serbianLatin ”,” slovenska ”,” spanska ”,” svenska ”,” hindi ”,” telugu ”,” thailändska ”” ukrainska ”,” urdu ”,” vietnamesiska ” |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| Delar upp text med språkspecifika regler och minskar ord i formulären bas<br /><br /> **Alternativ**<br /><br />maxTokenLength (typ: int)-token maxlängden, standard: 255 maximala: 300. Token som är längre än maxlängden delas. Token som är längre än 300 tecken först är uppdelade i token med längden 300 och sedan var och en av dessa token delas utifrån maxTokenLength uppsättningen.<br /><br /> isSearchTokenizer (typ: bool) – Ställ in som SANT om den används som Sök tokenizer inställt på falskt om den används som indexering tokenizer.<br /><br /> språk (typ: sträng)-språket som ska användas, standard ”engelska”. Tillåtna värden är:<br />”arabiska”, ”bangla”, ”bulgariska”, ”katalanska”, ”kroatiska”, ”tjeckiska”, ”danska”, ”nederländska”, ”english”, ”estniska”, ”finska”, franska ””, ”tyska”, ”grekiska”, ”gujarati”, ”hebreiska”, ”hindi”, ”ungerska”, ”isländsk”, ”indonesiska”, ”italienska”, ”kannada” ” lettiska ”,” litauiska ”,” malay ”,” malayalam ”,” marathi ”,” norwegianBokmaal ”,” polska ”,” portugisiska ”,” portugueseBrazilian ”,” panjabi ”,” rumänska ”,” ryska ”,” serbianCyrillic ”,” serbianLatin ”,” slovakiska ”,” slovenska ”,” spanska ”,” svenska ”” hindi ”,” telugu ”,” turkiska ”,” ukrainsk ”,” urdu ” |
|[nGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|Tokenizes mata in n-gram av den angivna storlek.<br /><br /> **Alternativ**<br /><br /> minGram (typ: int)-standard: 1, max: 300.<br /><br /> maxGram (typ: int)-standard: 2, maximalt: 300. Måste vara större än minGram. <br /><br /> tokenChars (typ: Strängmatrisen)-tecken klasser att token. Tillåtna värden: ”bokstaven”, ”siffra”, ”blanksteg”, ”skiljetecken”, ”symbolen”. Som standard till en tom matris - ser till att alla tecken. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|Tokenizer för sökväg-liknande hierarkier.<br /><br /> **Alternativ**<br /><br /> avgränsare (typ: sträng)-standard ”: /.<br /><br /> ersättning (typ: sträng) – om ställer, ersätter avgränsningstecken. Som standard samma som värde för avgränsare.<br /><br /> maxTokenLength (typ: int)-den maximala längden för token. Standard: 300, maximum: 300. Sökvägar som är längre än maxTokenLength ignoreras.<br /><br /> omvänd (typ: bool) – om SANT, genererar token i omvänd ordning. Standard: FALSKT.<br /><br /> Hoppa över (typ: bool)-inledande token ska hoppas över. Standardvärdet är 0.|  
|[pattern](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer|Den här tokenizer använder regex mönstermatchning för att konstruera distinkta token.<br /><br /> **Alternativ**<br /><br /> mönstret (typ: sträng)-mönster för reguljärt uttryck. Standardvärdet är \w+.<br /><br /> [flaggor](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (typ: sträng)-flaggor för reguljärt uttryck. Standardvärdet är en tom sträng. Tillåtna värden: CANON_EQ, CASE_INSENSITIVE, KOMMENTARER, DOTALL, LITTERALVÄRDE, MULTILINE, UNICODE_CASE, UNIX_LINES<br /><br /> grupp (typ: int)-vilken grupp att extrahera till token. Standardvärdet är -1 (delad).|  
|[standard_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|Delar upp text följande den [Unicode-Text segmentering regler](https://unicode.org/reports/tr29/).<br /><br /> **Alternativ**<br /><br /> maxTokenLength (typ: int)-den maximala längden för token. Standard: 255 maximala: 300. Token som är längre än maxlängden delas.|  
|[uax_url_email](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|Tokenizes URL: er och e-postmeddelanden som en token.<br /><br /> **Alternativ**<br /><br /> maxTokenLength (typ: int)-den maximala längden för token. Standard: 255 maximala: 300. Token som är längre än maxlängden delas.|  
|[whitespace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(typen gäller endast när alternativ är tillgängliga) |Delar upp text vid blanksteg. Token som är längre än 255 tecken delas.|  

 <sup>1</sup> tokenizer typer har alltid ett prefix i kod med ”#Microsoft.Azure.Search” så att ”ClassicTokenizer” faktiskt anges som ”#Microsoft.Azure.Search.ClassicTokenizer”. Vi har tagit bort prefix om du vill minska bredden på tabellen, men kom ihåg att inkludera den i din kod. Observera att tokenizer_type finns bara för tokenizers som kan anpassas. Om det finns inga alternativ så är fallet med bokstav tokenizer, finns det inga associerade #Microsoft.Azure.Search-typen.

<a name="TokenFilters"></a>

###  <a name="token-filters-reference"></a>Referens för token filter

I tabellen nedan länkas token filtren som implementeras med hjälp av Apache Lucene Lucene API-dokumentationen.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Beskrivning och alternativ**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Ett token filter som gäller Arabic normaliserare för att normalisera orthography.|  
|[apostrophe](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Tar bort datoridentiteten från alla tecken efter apostrof (inklusive apostrof själva). |  
|[asciifolding](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|Konverterar bokstäver, siffror och symboliska Unicode-tecken som inte är i först 127 ASCII-tecken (”Grundläggande latinska” Unicode-block) till deras ASCII-motsvarigheter, om sådan finns.<br /><br /> **Alternativ**<br /><br /> preserveOriginal (typ: bool) – om värdet är true, den ursprungliga token sparas. Standardvärdet är FALSKT.|  
|[cjk_bigram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|Formulär bigrams av CJK villkoren som genereras från StandardTokenizer.<br /><br /> **Alternativ**<br /><br /> ignoreScripts (typ: Strängmatrisen)-skript för att ignorera. Tillåtna värden är: ”han”, ”hiragana”, ”katakana”, ”hangul”. Standardvärdet är en tom lista.<br /><br /> outputUnigrams (typ: bool) – Ange värdet true om du alltid vill spara både unigrams och bigrams. Standardvärdet är FALSKT.|  
|[cjk_width](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Normaliserar CJK bredd skillnader. Vikningar full bredd ASCII varianter i motsvarande Grundläggande latin och halv bredd Katakana varianter till motsvarande kana. |  
|[classic](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Tar bort de engelska genitiv och punkter från förkortningar. |  
|[common_grams](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|Konstruera bigrams för ofta uppstår villkor under indexering. Enskilda termer indexeras fortfarande, med bigrams finns.<br /><br /> **Alternativ**<br /><br /> commonWords (typ: Strängmatrisen)-uppsättningen med vanliga ord. Standardvärdet är en tom lista. Krävs.<br /><br /> ignoreCase (typ: bool) – om värdet är true, är matchning skiftlägeskänsligt. Standardvärdet är FALSKT.<br /><br /> queryMode (typ: bool) - genererar bigrams sedan tar bort vanliga ord och enskilda termer följt av ett vanligt ord. Standardvärdet är FALSKT.|  
|[dictionary_decompounder](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|Delar upp sammansatta ord som finns i många germanskt språk.<br /><br /> **Alternativ**<br /><br /> Ordlistan (typ: Strängmatrisen)-listan över ord att matcha mot. Standardvärdet är en tom lista. Krävs.<br /><br /> minWordSize (typ: int)-bearbetas bara ord som är längre än så. Standardvärdet är 5.<br /><br /> minSubwordSize (typ: int) – endast subwords som är längre än så är för utdata. Standardvärdet är 2.<br /><br /> maxSubwordSize (typ: int) – endast subwords som är kortare än det här är utdata. Standardvärdet är 15.<br /><br /> onlyLongestMatch (typ: bool) – Lägg till den längsta matchande subword på skärmen. Standardvärdet är FALSKT.|  
|[edgeNGram_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|Genererar n-gram av den angivna storlek från att starta från början eller baksidan av en indata-token.<br /><br /> **Alternativ**<br /><br /> minGram (typ: int)-standard: 1, max: 300.<br /><br /> maxGram (typ: int)-standard: 2, maximalt 300. Måste vara större än minGram.<br /><br /> sida (typ: sträng) – anger vilken sida av indata n-gram ska genereras från. Tillåtna värden: ”klientdelens”, ”tillbaka” |  
|[elision](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|Tar bort elisions. Till exempel konverteras ”l'avion” (plan) till ”avion” (plan).<br /><br /> **Alternativ**<br /><br /> artiklar (typ: Strängmatrisen) – en uppsättning artiklar för att ta bort. Standardvärdet är en tom lista. Om det finns någon lista över artiklar set, som standard tas alla franska artiklar bort.|  
|[german_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Normaliserar tyska tecken i enlighet med heuristik för den [German2 snowball algoritmen](https://snowballstem.org/algorithms/german2/stemmer.html) .|  
|[hindi_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Normaliserar text i Hindi att ta bort några skillnader i stavning variationer. |  
|[indic_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|Normaliserar Unicode-representation av text i indiska språk.
|[Behåll](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|Ett token filter som endast håller token med text som ingår i den angivna listan med orden.<br /><br /> **Alternativ**<br /><br /> keepWords (typ: Strängmatrisen) – en lista över ord att hålla. Standardvärdet är en tom lista. Krävs.<br /><br /> keepWordsCase (typ: bool) – om värdet är true, lägre fall alla ord först. Standardvärdet är FALSKT.|  
|[keyword_marker](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|Markerar villkor som nyckelord.<br /><br /> **Alternativ**<br /><br /> nyckelord (typ: Strängmatrisen) – en lista över ord att markera som nyckelord. Standardvärdet är en tom lista. Krävs.<br /><br /> ignoreCase (typ: bool) – om värdet är true, lägre fall alla ord först. Standardvärdet är FALSKT.|  
|[keyword_repeat](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Genererar varje inkommande token två gånger en gång som nyckelord och en gång som icke-nyckelord. |  
|[kstem](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Ett högpresterande kstem filter för engelska. |  
|[Längd](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LengthTokenFilter|Tar bort ord som är för lång eller för kort.<br /><br /> **Alternativ**<br /><br /> min (typ: int) – det lägsta tillåtna värdet. Standard: 0, maximal: 300.<br /><br /> Max (typ: int)-det maximala antalet. Standard: 300, maximum: 300.|  
|[limit](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft.Azure.Search.LimitTokenFilter|Begränsar antalet token under indexering.<br /><br /> **Alternativ**<br /><br /> maxTokenCount (typ: int)-maxantal-tokens för att producera. Standardvärdet är 1.<br /><br /> consumeAllTokens (typ: bool) – oavsett om alla token från angivna indata måste utnyttjas även om maxTokenCount har uppnåtts. Standardvärdet är FALSKT.|  
|[lowercase](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Normaliserar token-texten till gemener. |  
|[nGram_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|Genererar n-gram av den angivna storlek.<br /><br /> **Alternativ**<br /><br /> minGram (typ: int)-standard: 1, max: 300.<br /><br /> maxGram (typ: int)-standard: 2, maximalt 300. Måste vara större än minGram.|  
|[pattern_capture](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|Använder Java regexes för att skapa flera token, en för varje avbildning i en eller flera mönster.<br /><br /> **Alternativ**<br /><br /> mönster (typ: Strängmatrisen) – en lista över mönster för matchning mot varje token. Krävs.<br /><br /> preserveOriginal (typ: bool) – Ställ in som SANT för att returnera den ursprungliga token även om en av mönster som matchar standard: SANT |  
|[pattern_replace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|Ett token filter som gäller ett mönster för varje token i strömmen ersätta matchning förekomster med angiven sträng.<br /><br /> **Alternativ**<br /><br /> mönstret (typ: sträng) – krävs.<br /><br /> ersättning (typ: sträng) – krävs.|  
|[persian_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(typen gäller endast när alternativ är tillgängliga) |Gäller normalisering för persiska. |  
|[fonetiska](https://lucene.apache.org/core/4_10_3/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|PhoneticTokenFilter|Skapa token för fonetiska matchningar.<br /><br /> **Alternativ**<br /><br /> kodare (typ: sträng)-fonetiska encoder att använda. Tillåtna värden är: ”metaphone”, ”doubleMetaphone”, ”soundex”, ”refinedSoundex”, ”caverphone1”, ”caverphone2”, ”Köln”, ”nysiis”, ”koelnerPhonetik”, ”haasePhonetik”, ”beiderMorse”. Standard: ”metaphone”. Standardvärdet är metaphone.<br /><br /> Se [encoder](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) för mer information.<br /><br /> Ersätt (typ: bool) – SANT om kodade token bör ersätta ursprungliga token, FALSKT om de ska läggas till som synonymer. Standardvärdet är sant.|  
|[porter_stem](https://lucene.apache.org/core/4_10_3/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(typen gäller endast när alternativ är tillgängliga)  |Omvandlar token stream enligt den [Porter ordstamsigenkänning algoritmen](https://tartarus.org/~martin/PorterStemmer/). |  
|[omvänd](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Kastar token strängen. |  
|[scandinavian_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Normaliserar utbytbara skandinaviska tecken. |  
|[scandinavian_folding](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Folds Scandinavian characters åÅäæÄÆ->a and öÖøØ->o. Den också diskriminering mot användning av dubbla vokaler aa, ae, ao, Outlook Express och säkerhetskopiering, lämna de andra två. |  
|[klapperstensstränder](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenFilter|Skapar kombinationer av token som en enskild token.<br /><br /> **Alternativ**<br /><br /> maxShingleSize (typ: int) – 2 som standard.<br /><br /> minShingleSize (typ: int) – 2 som standard.<br /><br /> outputUnigrams (typ: bool) – om värdet är true, utdataströmmen innehåller de inkommande token (unigrams) samt överläggsplattor. Standardvärdet är sant.<br /><br /> outputUnigramsIfNoShingles (typ: bool) – om värdet är true åsidosätta beteendet för outputUnigrams == false för de tillfällen när det finns inga överläggsplattor. Standardvärdet är FALSKT.<br /><br /> tokenSeparator (typ: sträng)-strängen som ska användas för att sammanfoga intilliggande token för att bilda en klapperstensstränder. Standardvärdet är ””.<br /><br /> filterToken (typ: sträng)-strängen som ska infoga för varje position där det finns ingen token. Standardvärdet är ”_”.|  
|[snowball](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Snowball Token Filter.<br /><br /> **Alternativ**<br /><br /> språk (typ: sträng)-tillåtna värden är: ”Armenisk”, ”Baskiska”, ”katalanska”, ”danska”, ”nederländska”, ”english”, ”finska”, ”franska”, ”tyska”, ”german2”, ”ungerska”, ”italienska”, ”kp”, ”lovins”, ”norska”, ”porter”, ”portugisiska”, ”rumänska” ”, ryska ”,” spanska ”,” svenska ”,” turkiska ”|  
|[sorani_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|Normaliserar Unicode-representation av Sorani text.<br /><br /> **Alternativ**<br /><br /> Ingen.|  
|Verbböjning|StemmerTokenFilter|Språkspecifika ordstamsigenkänning filter.<br /><br /> **Alternativ**<br /><br /> språk (typ: sträng)-tillåtna värden är: <br /> -   [”arabic”](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   [”Armenisk”](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   ["basque"](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   [”brasilianska”](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />-”bulgariska”<br />-   [”Katalanska”](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   ["czech"](https://portal.acm.org/citation.cfm?id=1598600)<br />-   [”danska”](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   ["dutch"](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   ["english"](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["lightEnglish"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalEnglish"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   [”possessiveEnglish”](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porter2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   [”lovins”](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   [”finska”](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />-”lightFinnish”<br />-   [”franska”](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   [”lightFrench”](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalFrench"](https://dl.acm.org/citation.cfm?id=318984)<br />-”galiciska”<br />-   "minimalGalician"<br />-   ["german"](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["german2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   [”lightGerman”](https://dl.acm.org/citation.cfm?id=1141523)<br />-”minimalGerman”<br />-   [”Grekiska”](https://sais.se/mthprize/2007/ntais2007.pdf)<br />-   "hindi"<br />-   ["hungarian"](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   [”lightHungarian”](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   [”indonesiska”](https://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf)<br />-   ["irish"](https://snowballstem.org/otherapps/oregan/)<br />-   [”italienska”](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   [”lightItalian”](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   [”sorani”](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   [”Lettiska”](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   [”Norska”](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   [”lightNorwegian”](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNorwegian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   [”portugisiska”](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   [”lightPortuguese”](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimalPortuguese"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugueseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   [”rumänska”](https://snowballstem.org/otherapps/romanian/)<br />-   [”Ryska”](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   [”lightRussian”](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   [”Spanska”](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   [”lightSpanish”](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   [”svenska”](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />-”lightSwedish”<br />-   [”turkiska”](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|Eventuella ordlista berodde villkor är markerade som nyckelord, vilket förhindrar ordstamsigenkänning ner i kedjan. Måste vara placerade innan några stemming filter.<br /><br /> **Alternativ**<br /><br /> regler (typ: Strängmatrisen)-ordstamsigenkänning regler i formatet ”word = > stam” till exempel ”körde = > Kör”. Standardvärdet är en tom lista.  Krävs.|  
|[stoppord](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Tar bort stoppord från en dataström med token. Som standard använder filtret en fördefinierad stop-ordlista för engelska.<br /><br /> **Alternativ**<br /><br /> stoppord (typ: Strängmatrisen) – en lista över stoppord. Kan inte anges om en stopwordsList har angetts.<br /><br /> stopwordsList (typ: sträng) – en fördefinierad lista över stoppord. Kan inte anges om stoppord har angetts. Tillåtna värden är: ”arabiska”, ”Armenisk”, ”Baskiska”, ”brasilianska”, ”bulgariska”, ”katalanska”, ”tjeckiska”, ”danska”, ”nederländska”, ”english”, ”finska”, ”franska”, ”galiciska”, ”tyska”, ”grekiska”, ”hindi”, ”ungerska”, ”indonesiska”, ”irländska”, ”italienska”, ”lettiska ”,” norska ”,” persiska ”,” portugisiska ”,” rumänska ”,” ryska ”,” sorani ”,” spanska ”,” svenska ”,” thailändska ”,” turkiska ”standard:” engelska ”. Kan inte anges om stoppord har angetts. <br /><br /> ignoreCase (typ: bool) – om SANT, alla ord är lägre alltid först. Standardvärdet är FALSKT.<br /><br /> removeTrailing (typ: bool) – om SANT, Ignorera sista söktermen om den stoppa ordet. Standardvärdet är sant.
|[synonym](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynonymTokenFilter|Matchningar enda eller flera ord synonymer i en token ström.<br /><br /> **Alternativ**<br /><br /> Synonymer (typ: Strängmatrisen) – krävs. Lista över synonymer i något av följande format:<br /><br /> -fantastiska, otroligt fantastisk = > spännande – alla villkor på vänster sida av = > symbolen ersätts med alla villkor på höger sida.<br /><br /> -fantastiska, otroligt fantastisk, spännande – en kommaavgränsad lista med motsvarande orden. Ange alternativet Expandera för att ändra hur den här listan tolkas.<br /><br /> ignoreCase (typ: bool)-fall vikningar indata för matchning. Standardvärdet är FALSKT.<br /><br /> Expandera (typ: bool) – om värdet är true, alla ord i listan över synonymer (om = > notation används inte) mappas till varandra. <br />I följande lista: fantastiska, otroligt fantastisk, spännande motsvarar: fantastiska, otroligt fantastisk, spännande = > fantastiska, otroligt fantastisk, spännande<br /><br />-Om FALSKT, i följande lista: fantastiska, otroligt fantastisk, spännande är likvärdiga med: fantastiska, otroligt fantastisk, spännande = > fantastiska.|  
|[trim](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Tar bort inledande och avslutande blanksteg från token. |  
|[truncate](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|TruncateTokenFilter|Trunkerar villkoren i en angiven tidsgräns.<br /><br /> **Alternativ**<br /><br /> längden (typ: int)-standard: 300, maximum: 300. Krävs.|  
|[unique](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniqueTokenFilter|Filtrerar ut token med samma namn som den föregående token.<br /><br /> **Alternativ**<br /><br /> onlyOnSamePosition (typ: bool) – om ställer, ta bort dubbletter endast på samma plats. Standardvärdet är sant.|  
|[versaler](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Normaliserar token-texten till versaler. |  
|[word_delimiter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|Delar upp ord i subwords och utför valfritt transformationer på subword grupper.<br /><br /> **Alternativ**<br /><br /> generateWordParts (typ: bool) – gör att delar av ord som ska genereras, till exempel ”AzureSearch” blir ”Azure” ”Search”. Standardvärdet är sant.<br /><br /> generateNumberParts (typ: bool) – gör att antalet subwords som ska genereras. Standardvärdet är sant.<br /><br /> catenateWords (typ: bool) – gör maximala körningar av word delar att vara catenated för exempel ”Azure Search” blir ”AzureSearch”. Standardvärdet är FALSKT.<br /><br /> catenateNumbers (typ: bool)-orsaker maximala körs mellan antalet delar och vara catenated, till exempel ”1 – 2” till ”12”. Standardvärdet är FALSKT.<br /><br /> catenateAll (typ: bool)-orsaker alla subword delar att vara catenated, t.ex. ”Azure-Search-1” blir ”AzureSearch1”. Standardvärdet är FALSKT.<br /><br /> splitOnCaseChange (typ: bool) – om värdet är true, delningar ord på caseChange, till exempel ”AzureSearch” blir ”Azure” ”Search”. Standardvärdet är sant.<br /><br /> preserveOriginal - orsaker ursprungliga ord att bevaras och läggs till i subword-listan. Standardvärdet är FALSKT.<br /><br /> splitOnNumerics (typ: bool) – om värdet är true, delningar på nummer, till exempel ”Azure1Search” blir ”Azure” ”1” ”Search”. Standardvärdet är sant.<br /><br /> stemEnglishPossessive (typ: bool) – orsaker avslutande ”s” som ska tas bort för varje subword. Standardvärdet är sant.<br /><br /> protectedWords (typ: Strängmatrisen)-Tokens för att skydda mot som avgränsade. Standardvärdet är en tom lista.|  

 <sup>1</sup> token filtertyper föregås alltid i kod med ”#Microsoft.Azure.Search” så att ”ArabicNormalizationTokenFilter” faktiskt anges som ”#Microsoft.Azure.Search.ArabicNormalizationTokenFilter”.  Vi har tagit bort prefix om du vill minska bredden på tabellen, men kom ihåg att inkludera den i din kod.  

> [!NOTE]
> Det krävs att du konfigurerar din anpassat analysverktyg för att inte skapa tokens högst 300 tecken. Indexering misslyckas för dokument med sådana token. Om du vill trimma dem eller ignorera dem, använda den **TruncateTokenFilter** och **LengthTokenFilter** respektive.


## <a name="see-also"></a>Se också  
 [Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Analysverktyg i Azure Search > exempel](search-analyzers.md#examples)    
 [Skapa Index &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
