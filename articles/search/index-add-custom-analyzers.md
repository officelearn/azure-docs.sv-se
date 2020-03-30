---
title: Lägga till anpassade analysatorer i strängfält
titleSuffix: Azure Cognitive Search
description: Konfigurera text tokenizers och teckenfilter som används i Azure Cognitive Search fulltext sökfrågor.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: 9bf0fb1a33a98031a78155a3956ac6d6abe33029
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113626"
---
# <a name="add-custom-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Lägga till anpassade analysatorer i strängfält i ett Azure Cognitive Search-index

En *anpassad analysator* är en viss typ av [textanalysator](search-analyzers.md) som består av en användardefinierad kombination av befintlig tokenizer och valfria filter. Genom att kombinera tokenizers och filter på nya sätt kan du anpassa textbehandling i sökmotorn för att uppnå specifika resultat. Du kan till exempel skapa en anpassad analysator med ett *teckenfilter* för att ta bort HTML-markering innan textindata tokeniseras.

 Du kan definiera flera anpassade analysatorer för att variera kombinationen av filter, men varje fält kan bara använda en analysator för indexeringsanalys och en för sökanalys. En illustration av hur en kundanalysator ser ut finns i [exempel på anpassad analysator](search-analyzers.md#Custom-analyzer-example).

## <a name="overview"></a>Översikt

 Rollen för en [fulltextsökmotor](search-lucene-query-architecture.md), i enkla ordalag, är att bearbeta och lagra dokument på ett sätt som möjliggör effektiv frågor och hämtning. På en hög nivå handlar det om att extrahera viktiga ord från dokument, placera dem i ett index och sedan använda indexet för att hitta dokument som matchar ord i en viss fråga. Processen att extrahera ord från dokument och sökfrågor kallas *lexikal analys*. Komponenter som utför lexikal analys kallas *analysatorer*.

 I Azure Cognitive Search kan du välja mellan en uppsättning fördefinierade språkagnostiska analysatorer i tabellen [Analyzers](#AnalyzerTable) eller språkspecifika analysatorer som anges i [Language analyzers &#40;AZURE Cognitive Search service REST API&#41;](index-add-language-analyzers.md). Du har också möjlighet att definiera dina egna anpassade analysatorer.  

 En anpassad analysator kan du ta kontroll över processen att konvertera text till indexerbara och sökbara token. Det är en användardefinierad konfiguration som består av en enda fördefinierad tokenizer, ett eller flera tokenfilter och ett eller flera teckenfilter. Tokenizern är ansvarig för att dela upp text i token och tokenfiltren för att ändra token som avges av tokenizern. Teckenfilter används för att förbereda indatatext innan den bearbetas av tokenizern. Exempelvis kan teckenfilter ersätta vissa tecken eller symboler.

 Populära scenarier som aktiveras av anpassade analysatorer inkluderar:  

- Fonetisk sökning. Lägg till ett fonetiskt filter för att aktivera sökning baserat på hur ett ord låter, inte hur det stavas.  

- Inaktivera lexikal analys. Använd nyckelordsanalysatorn för att skapa sökbara fält som inte analyseras.  

- Snabb prefix/suffixsökning. Lägg till tokenfiltret Edge N-gram i indexprefix med ord för att aktivera snabb prefixmatchning. Kombinera det med filtret Omvänd token för att göra suffixmatchning.  

- Anpassad tokenisering. Använd till exempel whitespace-tokenizer för att dela upp meningar i token med blanktecken som avgränsare  

- ASCII vikning. Lägg till standard-ASCII-vikningsfiltret för att normalisera diakritiska tecken som ö eller ê i söktermer.  

  Den här sidan innehåller en lista över analysatorer, tokenizers, tokenfilter och teckenfilter. Du kan också hitta en beskrivning av ändringar i indexdefinitionen med ett användningsexempel. Mer bakgrund om den underliggande tekniken som utnyttjas i Implementeringen av Azure Cognitive Search finns i [Sammanfattning av analyspaket (Lucene)](https://lucene.apache.org/core/6_0_0/core/org/apache/lucene/codecs/lucene60/package-summary.html). Exempel på analysatorkonfigurationer finns [i Lägga till analysatorer i Azure Cognitive Search](search-analyzers.md#examples).

## <a name="validation-rules"></a>Valideringsregler  
 Namn på analysatorer, tokenizers, tokenfilter och teckenfilter måste vara unika och kan inte vara samma som någon av de fördefinierade analysatorerna, tokenizers, tokenfilter eller teckenfilter. Se [egenskapsreferensen](#PropertyReference) för namn som redan används.

## <a name="create-custom-analyzers"></a>Skapa anpassade analysatorer
 Du kan definiera anpassade analysatorer vid skapande av index. Syntaxen för att ange en anpassad analysator beskrivs i det här avsnittet. Du kan också bekanta dig med syntaxen genom att granska exempeldefinitioner i [Lägg till analysatorer i Azure Cognitive Search](search-analyzers.md#examples).  

 En analysatordefinition innehåller ett namn, en typ, ett eller flera teckenfilter, högst en tokeniserare och ett eller flera tokenfilter för bearbetning efter tokenisering. Char filers tillämpas före tokenisering. Tokenfilter och teckenfilter tillämpas från vänster till höger.

 Namnet `tokenizer_name` på en tokeniserare `token_filter_name_1` `token_filter_name_2` och är namnen på `char_filter_name_1` tokenfilter och `char_filter_name_2` är namnen på teckenfilter (se [tokenizers,](#Tokenizers) [tokenfilter](#TokenFilters) och char filtertabeller för giltiga värden).

Analysatordefinitionen är en del av det större indexet. Se [Skapa index-API](https://docs.microsoft.com/rest/api/searchservice/create-index) för information om resten av indexet.

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
>  Anpassade analysatorer som du skapar visas inte i Azure-portalen. Det enda sättet att lägga till en anpassad analysator är genom kod som gör anrop till API när du definierar ett index.  

 I en indexdefinition kan du placera det här avsnittet var som helst i brödtexten i en skapa indexbegäran, men vanligtvis går det i slutet:  

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

Definitioner för teckenfilter, tokenizers och tokenfilter läggs bara till i indexet om du anger anpassade alternativ. Om du vill använda ett befintligt filter eller tokenizer som det är anger du det efter namn i analysatordefinitionen.

<a name="Testing custom analyzers"></a>

## <a name="test-custom-analyzers"></a>Testa anpassade analysatorer

Du kan använda **testanalysåtgärden** i [REST API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) för att se hur en analysator bryter in viss text i token.

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

## <a name="update-custom-analyzers"></a>Uppdatera anpassade analysatorer

När en analysator, en tokenizer, ett tokenfilter eller ett teckenfilter har definierats kan det inte ändras. Nya kan bara läggas till i ett `allowIndexDowntime` befintligt index om flaggan är inställd på true i begäran om indexuppdatering:

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

Den här åtgärden tar indexet offline i minst några sekunder, vilket gör att indexeringen och frågebegärandena misslyckas. Indexets prestanda- och skrivtillgänglighet kan försämras i flera minuter efter att indexet har uppdaterats, eller längre för mycket stora index, men dessa effekter är tillfälliga och löses så småningom på egen hand.

 <a name="ReferenceIndexAttributes"></a>

## <a name="analyzer-reference"></a>Analysatorreferens

Tabellerna nedan listar konfigurationsegenskaperna för analysatorerna, tokeniserarna, tokenfiltren och teckenfiltret i en indexdefinition. Strukturen för en analysator, tokenizer eller filter i indexet består av dessa attribut. Information om värdetilldelning finns i [Egenskapsreferensen](#PropertyReference).

### <a name="analyzers"></a>Analysverktyg

För analysatorer varierar indexattributen beroende på om du använder fördefinierade eller anpassade analysatorer.

#### <a name="predefined-analyzers"></a>Fördefinierade analysatorer

|||  
|-|-|  
|Namn|Den får bara innehålla bokstäver, siffror, blanksteg, streck eller understreck, kan bara börja och sluta med alfanumeriska tecken och är begränsad till 128 tecken.|  
|Typ|Analysatortyp från listan över analysatorer som stöds. Se **kolumnen analyzer_type** i tabellen [Analyzers](#AnalyzerTable) nedan.|  
|Alternativ|Måste vara giltiga alternativ för en fördefinierad analysator som anges i tabellen [Analyzers](#AnalyzerTable) nedan.|  

#### <a name="custom-analyzers"></a>Anpassade analysatorer

|||  
|-|-|  
|Namn|Den får bara innehålla bokstäver, siffror, blanksteg, streck eller understreck, kan bara börja och sluta med alfanumeriska tecken och är begränsad till 128 tecken.|  
|Typ|Måste vara "#Microsoft.Azure.Search.CustomAnalyzer".|  
|CharFilters|Ange antingen ett av fördefinierade teckenfilter som visas i tabellen [Teckenfilter](#char-filters-reference) eller ett anpassat teckenfilter som anges i indexdefinitionen.|  
|Tokenizer (tokeniserare)|Krävs. Ange antingen en av fördefinierade tokenizers som anges i tabellen [Tokenizers](#Tokenizers) nedan eller en anpassad tokenizer som anges i indexdefinitionen.|  
|Tokenfilter|Ange antingen ett av fördefinierade tokenfilter som visas i tabellen [Tokenfilter](#TokenFilters) eller ett anpassat tokenfilter som anges i indexdefinitionen.|  

> [!NOTE]
> Det krävs att du konfigurerar din anpassade analysator för att inte producera token som är längre än 300 tecken. Indexering misslyckas för dokument med sådana token. Om du vill trimma dem eller ignorera dem använder du **TruncateTokenFilter** respektive **LengthTokenFilter.**  Kontrollera [**tokenfilter**](#TokenFilters) som referens.

<a name="CharFilter"></a>

### <a name="char-filters"></a>Teckenfilter

 Ett teckenfilter används för att förbereda indatatext innan den bearbetas av tokenizern. De kan till exempel ersätta vissa tecken eller symboler. Du kan ha flera teckenfilter i en anpassad analysator. Teckenfilter körs i den ordning som de visas i.  

|||  
|-|-|  
|Namn|Den får bara innehålla bokstäver, siffror, blanksteg, streck eller understreck, kan bara börja och sluta med alfanumeriska tecken och är begränsad till 128 tecken.|  
|Typ|Teckenfiltertyp från listan över char-filter som stöds. Se **char_filter_type** kolumnen i tabellen [Teckenfilter](#char-filters-reference) nedan.|  
|Alternativ|Måste vara giltiga alternativ för en viss [typ av teckenfilter.](#char-filters-reference)|  

### <a name="tokenizers"></a>Tokenizers (tokeniserare)

 En tokenizer delar upp kontinuerlig text i en sekvens av token, till exempel att bryta en mening i ord.  

 Du kan ange exakt en tokenizer per anpassad analysator. Om du behöver mer än en tokeniserare kan du skapa flera anpassade analysatorer och tilldela dem fält för fält i indexschemat.  
En anpassad analysator kan använda en fördefinierad tokenizer med antingen standardalternativ eller anpassade alternativ.  

|||  
|-|-|  
|Namn|Den får bara innehålla bokstäver, siffror, blanksteg, streck eller understreck, kan bara börja och sluta med alfanumeriska tecken och är begränsad till 128 tecken.|  
|Typ|Tokenizer-namn från listan över tokenizers som stöds. Se **tokenizer_type** kolumn i [tabellen Tokenizers](#Tokenizers) nedan.|  
|Alternativ|Måste vara giltiga alternativ för en viss tokenizertyp som anges i [tabellen Tokenizers](#Tokenizers) nedan.|  

### <a name="token-filters"></a>Tokenfilter

 Ett tokenfilter används för att filtrera bort eller ändra token som genereras av en tokenizer. Du kan till exempel ange ett gemener som konverterar alla tecken till gemener.   
Du kan ha flera tokenfilter i en anpassad analysator. Tokenfilter körs i den ordning som de visas i.  

|||  
|-|-|  
|Namn|Den får bara innehålla bokstäver, siffror, blanksteg, streck eller understreck, kan bara börja och sluta med alfanumeriska tecken och är begränsad till 128 tecken.|  
|Typ|Tokenfilternamn från listan över tokenfilter som stöds. Se **token_filter_type** kolumn i tabellen [Tokenfilter](#TokenFilters) nedan.|  
|Alternativ|Måste vara [tokenfilter](#TokenFilters) för en viss tokenfiltertyp.|  

<a name="PropertyReference"></a>  

## <a name="property-reference"></a>Egenskapsreferens

Det här avsnittet innehåller giltiga värden för attribut som anges i definitionen av ett anpassat analysator-, tokenizer-, teckenfilter- eller tokenfilter i indexet. Analysatorer, tokenizers och filter som implementeras med Apache Lucene har länkar till Lucene API-dokumentationen.

<a name="AnalyzerTable"></a>

###  <a name="predefined-analyzers-reference"></a>Fördefinierad analysatorreferens

|**analyzer_name**|**analyzer_type**  <sup>1</sup>|**Beskrivning och alternativ**|  
|-|-|-|  
|[Sökord](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (typen gäller endast när alternativ är tillgängliga) |Behandlar hela innehållet i ett fält som en enda token. Detta är användbart för data som postnummer, ID och vissa produktnamn.|  
|[Mönster](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|MönsterAnalyzer|Separerar flexibelt text i termer via ett mönster för reguljära uttryck.<br /><br /> **Alternativ**<br /><br /> gemener (typ: bool) - Avgör om termerna är sänkta. Standardinställningen är sann.<br /><br /> [mönster](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (typ: sträng) - Ett mönster för reguljära uttryck för att matcha tokenavgränsare. Standardär \w+.<br /><br /> [flaggor](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (typ: sträng) - Flaggor för reguljära uttryck. Standardär en tom sträng. Tillåtna värden: CANON_EQ, CASE_INSENSITIVE, KOMMENTARER, DOTALL, BOKSTAVLIG, FLERRADIG, UNICODE_CASE, UNIX_LINES<br /><br /> stopwords (typ: strängmatris) - En lista med stopwords. Standard är en tom lista.|  
|[Enkel](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(typen gäller endast när alternativ är tillgängliga) |Delar upp text vid icke-bokstäver och konverterar dem till gemener. |  
|[standard](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) <br />(Även kallad standard.lucene)|StandardAnalyzer|Standard Lucene-analysator, som består av standardtokenizern, gemenerfiltret och stoppfiltret.<br /><br /> **Alternativ**<br /><br /> maxTokenLength (typ: int) - Den maximala tokenlängden. Standardvärdet är 255. Token som är längre än den maximala längden delas. Maximal tokenlängd som kan användas är 300 tecken.<br /><br /> stopwords (typ: strängmatris) - En lista med stopwords. Standard är en tom lista.|  
|standardasciifolding.lucene|(typen gäller endast när alternativ är tillgängliga) |Standardanalysator med Ascii fällfilter. |  
|[Stanna](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer (StopAnalyzer)|Delar upp text med icke-bokstäver, använder gemener och stopword tokenfilter.<br /><br /> **Alternativ**<br /><br /> stopwords (typ: strängmatris) - En lista med stopwords. Standardär en fördefinierad lista för engelska. |  
|[Blanksteg](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(typen gäller endast när alternativ är tillgängliga) |En analysator som använder tokenizern för blanktecken. Tokens som är längre än 255 tecken delas.|  

 <sup>1</sup> Analystyper är alltid prefixed i kod med "#Microsoft.Azure.Search" så att "PatternAnalyzer" faktiskt skulle anges som "#Microsoft.Azure.Search.PatternAnalyzer". Vi tog bort prefixet för korthet, men prefixet krävs i din kod. 
 
Den analyzer_type tillhandahålls endast för analysatorer som kan anpassas. Om det inte finns några alternativ, vilket är fallet med nyckelordsanalysatorn, finns det ingen associerad #Microsoft.Azure.Search-typ.


<a name="CharFilter"></a>

###  <a name="char-filters-reference"></a>Referens för teckenfilter

I tabellen nedan länkas teckenfiltren som implementeras med Apache Lucene till Lucene API-dokumentationen.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**Beskrivning och alternativ**|  
|-|-|-|
|[html_strip](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Ett teckenfilter som försöker ta bort HTML-konstruktioner.|  
|[mapping](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|Ett teckenfilter som tillämpar mappningar som definierats med alternativet mappningar. Matchning är girig (längsta mönster matchning vid en viss punkt vinner). Ersättning får vara den tomma strängen.<br /><br /> **Alternativ**<br /><br /> mappningar (typ: strängmatris) - En lista med mappningar av följande format: "a=>b" (alla förekomster av tecknet "a" ersätts med tecknet "b"). Krävs.|  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|MönsterReplaceCharFilter|Ett teckenfilter som ersätter tecken i indatasträngen. Den använder ett reguljärt uttryck för att identifiera teckensekvenser att bevara och ett ersättningsmönster för att identifiera tecken att ersätta. Till exempel indatatext = "aa bb aa bb",\\pattern="(aa) \s+(bb)" replacement="$1#$2", resultat = "aa#bb aa#bb".<br /><br /> **Alternativ**<br /><br /> mönster (typ: sträng) - Obligatoriskt.<br /><br /> ersättning (typ: sträng) - Krävs.|  

 <sup>1</sup> Teckenfiltertyper är alltid prefixed i kod med "#Microsoft.Azure.Search" så att "MappingCharFilter" faktiskt skulle anges som "#Microsoft.Azure.Search.MappingCharFilter. Vi tog bort prefixet för att minska bredden på tabellen, men kom ihåg att inkludera det i din kod. Observera att char_filter_type endast tillhandahålls för filter som kan anpassas. Om det inte finns några alternativ, vilket är fallet med html_strip, finns det ingen associerad #Microsoft.Azure.Search-typ.

<a name="Tokenizers"></a>

###  <a name="tokenizers-reference"></a>Tokenizers referens

I tabellen nedan länkas tokenizers som implementeras med Apache Lucene till Lucene API-dokumentationen.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**Beskrivning och alternativ**|  
|-|-|-|  
|[Klassiska](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|KlassiskaTokenizer|Grammatikbaserad tokenizer som är lämplig för bearbetning av de flesta europeiska dokument.<br /><br /> **Alternativ**<br /><br /> maxTokenLength (typ: int) - Den maximala tokenlängden. Standard: 255, max: 300. Token som är längre än den maximala längden delas.|  
|[kantNGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|KantgramTokenizer|Tokenizes indata från en kant till n-gram av given storlek(er).<br /><br /> **Alternativ**<br /><br /> minGram (typ: int) - Standard: 1, max: 300.<br /><br /> maxGram (typ: int) - Standard: 2, max: 300. Måste vara större än minGram.<br /><br /> tokenChars (typ: strängmatris) - Teckenklasser att behålla i token. Tillåtna värden: <br />"bokstav", "siffra", "blanktecken", "interpunktion", "symbol". Standard till en tom matris - behåller alla tecken. |  
|[keyword_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|Avger hela indata som en enda token.<br /><br /> **Alternativ**<br /><br /> maxTokenLength (typ: int) - Den maximala tokenlängden. Standard: 256, max: 300. Token som är längre än den maximala längden delas.|  
|[Brev](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(typen gäller endast när alternativ är tillgängliga)  |Delar upp text med icke-bokstäver. Tokens som är längre än 255 tecken delas.|  
|[Gemener](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(typen gäller endast när alternativ är tillgängliga)  |Delar upp text vid icke-bokstäver och konverterar dem till gemener. Tokens som är längre än 255 tecken delas.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Delar upp text med hjälp av språkspecifika regler.<br /><br /> **Alternativ**<br /><br /> maxTokenLength (typ: int) - Den maximala tokenlängden, standard: 255, max: 300. Token som är längre än den maximala längden delas. Tokens längre än 300 tecken delas först upp i tokens med längd 300 och sedan delas var och en av dessa tokens baserat på maxTokenLength-uppsättningen.<br /><br />isSearchTokenizer (typ: bool) - Ställ in på true om den används som söktokenizer, inställd på false om den används som indexeringstokenizer. <br /><br /> språk (typ: sträng) - Språk att använda, standard "engelska". Tillåtna värden inkluderar:<br />"bangla", "bulgariska", "katalanska", "chineseSimplified", "chineseTraditional", "croatian", "czech", "danish", "dutch", "english", "french", "german", "greek", "gujarati", "hindi", "icelandic", "indonesian", "italian", "japanese", "kannada", " koreanska", "malajiska", "malayalam", "marathi", "norwegianBokmaal", "polish", "portuguese", "portugueseBrazilian", "punjabi", "romanian", "russian", "serbiskCyrillic", "serbiskLatin", "slovenska", "spanska", "svensk", "tamil", "telugu", "thai", " ukrainska", "urdu", "vietnamesiska" |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageS som hejar På| Delar upp text med hjälp av språkspecifika regler och reducerar ord till sina basformulär<br /><br /> **Alternativ**<br /><br />maxTokenLength (typ: int) - Den maximala tokenlängden, standard: 255, max: 300. Token som är längre än den maximala längden delas. Tokens längre än 300 tecken delas först upp i tokens med längd 300 och sedan delas var och en av dessa tokens baserat på maxTokenLength-uppsättningen.<br /><br /> isSearchTokenizer (typ: bool) - Ställ in på true om den används som söktokenizer, inställd på false om den används som indexeringstokenizer.<br /><br /> språk (typ: sträng) - Språk att använda, standard "engelska". Tillåtna värden inkluderar:<br />"arabiska", "bangla", "bulgariska", "katalanska", "kroatiska", "tjeckiska", "danska", "holländska", "engelska", "estniska", "finska", "franska", "tyska", "grekiska", "gujarati", "hebrew", "hindi", "ungerska", "isländska", "indonesiska", "italienska", "kannada", " lettiska", "litauiska", "malajiska", "malayalam", "marathi", "norwegianBokmaal", "polish", "portugueseBrazilian", "punjabi", "romanian", "russian", "serbiskCyrillic", "serbiskLatin", "slovakisk", "slovensk", "spanska", "svensk", " tamil", "telugu", "turkiska", "ukrainska", "urdu" |
|[nGram (på annat sätt)](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer (svenska)|Tokenizes indata till n-gram av den angivna storleken(s).<br /><br /> **Alternativ**<br /><br /> minGram (typ: int) - Standard: 1, max: 300.<br /><br /> maxGram (typ: int) - Standard: 2, max: 300. Måste vara större än minGram. <br /><br /> tokenChars (typ: strängmatris) - Teckenklasser att behålla i token. Tillåtna värden: "bokstav", "siffra", "blanktecken", "interpunktion", "symbol". Standard till en tom matris - behåller alla tecken. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|Tokenizer för sökvägsliknande hierarkier.<br /><br /> **Alternativ**<br /><br /> avgränsare (typ: sträng) - Standard: '/.<br /><br /> ersättning (typ: sträng) - Om den är inställd ersätter du avgränsaren. Standard samma som värdet för avgränsare.<br /><br /> maxTokenLength (typ: int) - Den maximala tokenlängden. Standard: 300, max: 300. Vägar som är längre än maxTokenLength ignoreras.<br /><br /> omvänd (typ: bool) - Om sant, genererar token i omvänd ordning. Standard: falskt.<br /><br /> hoppa (typ: bool) - Inledande tokens att hoppa över. Standardvärdet är 0.|  
|[Mönster](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|MönsterTokenizer|Den här tokeniseraren använder regex-mönstermatchning för att skapa distinkta token.<br /><br /> **Alternativ**<br /><br /> [mönster](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html) (typ: sträng) - Mönster för reguljärt uttryck. Standardär \W+. <br /><br /> [flaggor](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (typ: sträng) - Flaggor för reguljära uttryck. Standardär en tom sträng. Tillåtna värden: CANON_EQ, CASE_INSENSITIVE, KOMMENTARER, DOTALL, BOKSTAVLIG, FLERRADIG, UNICODE_CASE, UNIX_LINES<br /><br /> grupp (typ: int) - Vilken grupp som ska extraheras till token. Standardär -1 (split).|
|[standard_v2](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|Bryter text efter [unicode-textsegmenteringsreglerna](https://unicode.org/reports/tr29/).<br /><br /> **Alternativ**<br /><br /> maxTokenLength (typ: int) - Den maximala tokenlängden. Standard: 255, max: 300. Token som är längre än den maximala längden delas.|  
|[uax_url_email](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|Tokenizes urls och e-post som en token.<br /><br /> **Alternativ**<br /><br /> maxTokenLength (typ: int) - Den maximala tokenlängden. Standard: 255, max: 300. Token som är längre än den maximala längden delas.|  
|[Blanksteg](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(typen gäller endast när alternativ är tillgängliga) |Delar upp text i blanktecken. Tokens som är längre än 255 tecken delas.|  

 <sup>1</sup> Tokenizer-typer är alltid prefixed i kod med "#Microsoft.Azure.Search" så att "ClassicTokenizer" faktiskt skulle anges som "#Microsoft.Azure.Search.ClassicTokenizer". Vi tog bort prefixet för att minska bredden på tabellen, men kom ihåg att inkludera det i din kod. Observera att tokenizer_type endast tillhandahålls för tokenizers som kan anpassas. Om det inte finns några alternativ, vilket är fallet med bokstavstokenizern, finns det ingen associerad #Microsoft.Azure.Search-typ.

<a name="TokenFilters"></a>

###  <a name="token-filters-reference"></a>Referens för tokenfilter

I tabellen nedan länkas tokenfiltren som implementeras med Apache Lucene till Lucene API-dokumentationen.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Beskrivning och alternativ**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Ett tokenfilter som tillämpar den arabiska normaliseraren för att normalisera rättskrivningskonsten.|  
|[Apostrof](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Remsor alla tecken efter en apostrof (inklusive apostrof själv). |  
|[asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|Konverterar alfabetiska, numeriska och symboliska Unicode-tecken som inte finns i de första 127 ASCII-tecknen (det "grundläggande latinska" Unicode-blocket) till sina ASCII-motsvarigheter, om det finns någon.<br /><br /> **Alternativ**<br /><br /> preserveOriginal (typ: bool) - Om den är true, behålls den ursprungliga token. Standardvärdet är falskt.|  
|[cjk_bigram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|Bildar bigrams av CJK termer som genereras från StandardTokenizer.<br /><br /> **Alternativ**<br /><br /> ignoreScripts (typ: strängmatris) - Skript som ska ignoreras. Tillåtna värden inkluderar: "han", "hiragana", "katakana", "hangul". Standard är en tom lista.<br /><br /> outputUnigrams (typ: bool) - Ställ in på true om du alltid vill mata ut både unigram och bigrams. Standardvärdet är falskt.|  
|[cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Normaliserar CJK breddskillnader. Viker full bredd ASCII varianter i motsvarande grundläggande latin och halv bredd Katakana varianter i motsvarande kana. |  
|[Klassiska](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Tar bort den engelska possessiva, och prickar från förkortningar. |  
|[common_grams](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|Konstruera bigrams för ofta förekommande termer vid indexering. Enstaka termer är fortfarande indexeras också, med bigrams överlagrad.<br /><br /> **Alternativ**<br /><br /> commonWords (typ: strängmatris) - Uppsättningen vanliga ord. Standard är en tom lista. Krävs.<br /><br /> ignoreCase (typ: bool) - Om sant, är matchning skiftlägesokänslig. Standardvärdet är falskt.<br /><br /> queryMode (typ: bool) - Genererar bigrams tar sedan bort vanliga ord och enstaka termer följt av ett vanligt ord. Standardvärdet är falskt.|  
|[dictionary_decompounder](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|Sönderdelas sammansatta ord som finns i många germanska språk.<br /><br /> **Alternativ**<br /><br /> wordList (typ: strängmatris) - Listan med ord att matcha mot. Standard är en tom lista. Krävs.<br /><br /> minWordSize (typ: int) - Endast ord längre än detta bearbetas. Standard är 5.<br /><br /> minSubwordSize (typ: int) - Endast underord som är längre än så skrivs ut. Standard är 2.<br /><br /> maxSubwordSize (typ: int) - Endast underord som är kortare än så skrivs ut. Standardvärdet är 15.<br /><br /> onlyLongestMatch (typ: bool) - Lägg bara till det längsta matchande underordet i utdata. Standardvärdet är falskt.|  
|[edgeNGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|Genererar n-gram av den angivna storleken/storlekarna från att börja framifrån eller baksidan av en indatatoken.<br /><br /> **Alternativ**<br /><br /> minGram (typ: int) - Standard: 1, max: 300.<br /><br /> maxGram (typ: int) - Standard: 2, max 300. Måste vara större än minGram.<br /><br /> (typ: sträng) - Anger vilken sida av indata n-gram ska genereras från. Tillåtna värden: "front", "back" |  
|[elision (elision)](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|Tar bort elisioner. Till exempel konverteras "l'avion" (planet) till "avion" (plan).<br /><br /> **Alternativ**<br /><br /> artiklar (typ: strängmatris) - En uppsättning artiklar att ta bort. Standard är en tom lista. Om det inte finns någon lista över artiklar som har angetts tas som standard alla franska artiklar bort.|  
|[german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Normaliserar tyska tecken enligt heuristiken i [den german2 snöbollsalgoritmen](https://snowballstem.org/algorithms/german2/stemmer.html) .|  
|[hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Normaliserar text på hindi för att ta bort vissa skillnader i stavningsvariationer. |  
|[indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|Normaliserar Unicode-representationen av text på indiska språk.
|[Hålla](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|Ett tokenfilter som bara håller token med text i angiven lista med ord.<br /><br /> **Alternativ**<br /><br /> keepWords (typ: string array) - En lista med ord att behålla. Standard är en tom lista. Krävs.<br /><br /> keepWordsCase (typ: bool) - Om sant, gemener alla ord först. Standardvärdet är falskt.|  
|[keyword_marker](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|Markerar termer som nyckelord.<br /><br /> **Alternativ**<br /><br /> nyckelord (typ: strängmatris) - En lista med ord som ska markeras som nyckelord. Standard är en tom lista. Krävs.<br /><br /> ignoreCase (typ: bool) - Om sant, gemener alla ord först. Standardvärdet är falskt.|  
|[keyword_repeat](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Avger varje inkommande token två gånger som nyckelord och en gång som icke-sökord. |  
|[kstem (kstem)](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Ett högpresterande kstemfilter för engelska. |  
|[Längd](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LängdTokenFilter|Tar bort ord som är för långa eller för korta.<br /><br /> **Alternativ**<br /><br /> min (typ: int) - Det minsta antalet. Standard: 0, max: 300.<br /><br /> max (typ: int) - Det maximala antalet. Standard: 300, max: 300.|  
|[Gräns](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft.Azure.Search.LimitTokenFilter|Begränsar antalet token vid indexering.<br /><br /> **Alternativ**<br /><br /> maxTokenCount (typ: int) - Max antal tokens att producera. Standard är 1.<br /><br /> consumeAllTokens (typ: bool) - Om alla token från indata måste förbrukas även om maxTokenCount nås. Standardvärdet är falskt.|  
|[Gemener](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Normaliserar tokentext till gemener. |  
|[nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|Genererar n-gram av den angivna storleken(er).<br /><br /> **Alternativ**<br /><br /> minGram (typ: int) - Standard: 1, max: 300.<br /><br /> maxGram (typ: int) - Standard: 2, max 300. Måste vara större än minGram.|  
|[pattern_capture](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|Använder Java regexes för att avge flera token, en för varje insamlingsgrupp i ett eller flera mönster.<br /><br /> **Alternativ**<br /><br /> mönster (typ: strängmatris) - En lista med mönster som matchar mot varje token. Krävs.<br /><br /> preserveOriginal (typ: bool) - Ställ in på true för att returnera den ursprungliga token även om ett av mönstren matchar, standard: true |  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|Ett tokenfilter som tillämpar ett mönster på varje token i dataströmmen och ersätter matchningsförekomster med den angivna ersättningssträngen.<br /><br /> **Alternativ**<br /><br /> mönster (typ: sträng) - Obligatoriskt.<br /><br /> ersättning (typ: sträng) - Krävs.|  
|[persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(typen gäller endast när alternativ är tillgängliga) |Tillämpar normalisering för persiska. |  
|[Fonetiska](https://lucene.apache.org/core/6_6_1/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|PhoneticTokenFilter|Skapa token för fonetiska matchningar.<br /><br /> **Alternativ**<br /><br /> kodaren (typ: sträng) - Fonetisk kodare att använda. Tillåtna värden inkluderar: "metaphone", "doubleMetaphone", "soundex", "refinedSoundex", "caverphone1", "caverphone2", "cologne", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse". Standard: "metafon". Standard är metaphone.<br /><br /> Se [kodare](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) för mer information.<br /><br /> ersätt (typ: bool) - Sant om kodade token ska ersätta ursprungliga token, falskt om de ska läggas till som synonymer. Standardinställningen är sann.|  
|[porter_stem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(typen gäller endast när alternativ är tillgängliga)  |Omvandlar tokenströmmen enligt [porter-algoritmen](https://tartarus.org/~martin/PorterStemmer/). |  
|[Omvänd](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Återför tokensträngen. |  
|[scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Normaliserar användningen av de utbytbara skandinaviska tecknen. |  
|[scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Viker skandinaviska karaktärer åÅäæÄÆ->a och ööøØ->o. Det diskriminerar också användning av dubbla vokaler aa, ae, ao, oe och oo, lämnar bara den första. |  
|[Singel](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|Klapperstenstokenfilter|Skapar kombinationer av token som en enda token.<br /><br /> **Alternativ**<br /><br /> maxShingleSize (typ: int) - Standardvärden till 2.<br /><br /> minShingleSize (typ: int) - Standardvärden till 2.<br /><br /> outputUnigrams (typ: bool) - om den är sann innehåller utdataströmmen indatatoken (unigram) samt bältros. Standardinställningen är sann.<br /><br /> outputUnigramsIfNoShingles (typ: bool) - Om sant, åsidosätta beteendet för outputUnigrams==false för de gånger då inga bältros är tillgängliga. Standardvärdet är falskt.<br /><br /> tokenSeparator (typ: sträng) - Strängen som ska användas när du ansluter intilliggande token för att bilda en singel. Standard är " ".<br /><br /> filterToken (typ: sträng) - Strängen som ska infogas för varje position där det inte finns någon token. Standard är "_".|  
|[Snöboll](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnöbollTokenFilter|Tokenfilter för snöboll.<br /><br /> **Alternativ**<br /><br /> Språk (typ: sträng) - Tillåtna värden inkluderar: "armeniska", "baskiska", "katalanska", "danska", "holländska", "engelska", "finska", "franska", "tyska", "german2", "ungerska", "italienska", "kp", "lovins", "norska", "porter", "portugisiska", "rumänska", " ryska", "spanska", "svenska", "turkiska"|  
|[sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|Normaliserar Unicode-representationen av sorani-text.<br /><br /> **Alternativ**<br /><br /> Inga.|  
|Stemmer|StemmerTokenFilter|Språkspecifikt härstamningsfilter.<br /><br /> **Alternativ**<br /><br /> språk (typ: sträng) - Tillåtna värden inkluderar: <br /> -   ["arabiska"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   ["Armeniska"](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   ["Baskiska"](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   ["Brasilianare"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />- "bulgariska"<br />-   ["katalanska"](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   ["Tjeckiska"](https://portal.acm.org/citation.cfm?id=1598600)<br />-   ["danska"](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   ["Holländska"](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   ["Engelska"](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["lightEnglish"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalEnglish"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["possessivaEnglish"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["Porter2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["Lovins"](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   ["finska"](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />- "lightFinnish"<br />-   ["Franska"](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["lightFrench"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalFrench"](https://dl.acm.org/citation.cfm?id=318984)<br />- "Galiciska"<br />- "minimalGalician"<br />-   ["tyska"](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["German2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["lightGerman"](https://dl.acm.org/citation.cfm?id=1141523)<br />- "minimalTyskland"<br />-   ["grekiska"](https://sais.se/mthprize/2007/ntais2007.pdf)<br />- "hindi"<br />-   ["ungerska"](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["lightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["Indonesiska"](https://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf)<br />-   ["Irländare"](https://snowballstem.org/otherapps/oregan/)<br />-   ["Italienska"](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["lättitalienska"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["Sorani"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   ["Lettiska"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   ["norska"](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["lightNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalnorriska"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["Portugisiska"](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortuguese"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimalPortuguese"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugisiskaRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   ["Rumänsk"](https://snowballstem.org/otherapps/romanian/)<br />-   ["ryska"](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["lightRussian"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   ["Spanska"](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["lättSpansk"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["svenska"](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />- "ljusSvensk"<br />-   ["turkiska"](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|Alla ordlistade termer markeras som nyckelord, vilket förhindrar att kedjan kommer ned. Måste placeras före några avstämda filter.<br /><br /> **Alternativ**<br /><br /> regler (typ: strängmatris) - Hejda regler i följande format "word => stem" till exempel "ran => run". Standard är en tom lista.  Krävs.|  
|[stopwords](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Tar bort stoppord från en tokenström. Som standard använder filtret en fördefinierad stoppordslista för engelska.<br /><br /> **Alternativ**<br /><br /> stopwords (typ: strängmatris) - En lista med stopwords. Det går inte att komma att anges om en stopwordsList har angetts.<br /><br /> stopwordsList (typ: sträng) - En fördefinierad lista med stopwords. Det går inte att komma att anges om stopwords har angetts. Tillåtna värden inkluderar:"arabiska", "armeniska", "baskiska", "brasilianska", "bulgariska", "katalanska", "tjeckiska", "danska", "holländska", "engelska", "finska", "franska", "galiciska", "tyska", "grekiska", "hindi", "ungerska", "indonesiska", "irländska", "italienska", "lettiska", "lettiska", "lettiska", "lettiska", "lettiska", "lettiska", "lettiska", "lettiska", "lettiska", " ", "norska", "persiska", "portugisiska", "rumänska", "ryska", "sorani", "spanska", "svenska", "thai", "turkiska", standard: "engelska". Det går inte att komma att anges om stopwords har angetts. <br /><br /> ignoreCase (typ: bool) - Om sant, alla ord är gemener först. Standardvärdet är falskt.<br /><br /> removeTrailing (typ: bool) - Om sant, ignorera den senaste söktermen om det är ett stoppord. Standardinställningen är sann.
|[Synonym](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynonymTokenFilter|Matchar en- eller flerords synonymer i en tokenström.<br /><br /> **Alternativ**<br /><br /> synonymer (typ: strängmatris) - Obligatoriskt. Lista över synonymer i något av följande två format:<br /><br /> -otroligt, otroligt, fantastiskt => fantastiskt - alla termer på vänster sida av => symbol ersätts med alla termer på sin högra sida.<br /><br /> -otroligt, otroligt, fantastiskt, fantastiskt - En kommaavskiljad lista med motsvarande ord. Ange alternativet expandera för att ändra hur den här listan ska tolkas.<br /><br /> ignoreCase (typ: bool) - Case-folds indata för matchning. Standardvärdet är falskt.<br /><br /> expandera (typ: bool) - Om det är sant mappas alla ord i synonymlistan (om => notation inte) till varandra. <br />Följande lista: otroligt, otroligt, fantastisk, fantastisk motsvarar: otroligt, otroligt, fantastisk, fantastisk => otroligt, otroligt, fantastiskt, fantastiskt<br /><br />- Om falska, följande lista: otroligt, otroligt, fantastisk, fantastisk motsvarar: otroligt, otroligt, fantastisk, fantastisk => otroligt.|  
|[Trimma](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Trimmar inledande och avslutande blanktecken från tokens. |  
|[truncate](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|Kortstäcktokenfilter|Trunkerar termerna till en viss längd.<br /><br /> **Alternativ**<br /><br /> längd (typ: int) - Standard: 300, max: 300. Krävs.|  
|[Unik](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniqueTokenFilter|Filtrerar bort token med samma text som föregående token.<br /><br /> **Alternativ**<br /><br /> onlyOnSamePosition (typ: bool) - Om den är inställd, ta bara bort dubbletter på samma plats. Standardinställningen är sann.|  
|[Versaler](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(typen gäller endast när alternativ är tillgängliga)  |Normaliserar tokentext till versaler. |  
|[word_delimiter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|Delar upp ord i underord och utför valfria omformningar på underordsgrupper.<br /><br /> **Alternativ**<br /><br /> generateWordParts (typ: bool) - Orsakar att delar av ord genereras, till exempel "AzureSearch" blir "Azure" "Sök". Standardinställningen är sann.<br /><br /> generateNumberParts (typ: bool) - Orsakar att nummerunderord genereras. Standardinställningen är sann.<br /><br /> catenateWords (typ: bool) - Orsakar att maximala körningar av orddelar kategoriseras, till exempel blir "Azure-Search" "AzureSearch". Standardvärdet är falskt.<br /><br /> catenateNumbers (typ: bool) - Orsakar maximala körningar av antal delar som skall catenated, till exempel "1-2" blir "12". Standardvärdet är falskt.<br /><br /> catenateAll (typ: bool) - Gör att alla underordsdelar catenated, till exempel "Azure-Search-1" blir "AzureSearch1". Standardvärdet är falskt.<br /><br /> splitOnCaseChange (typ: bool) - Om det är sant, delar ord på caseChange, till exempel "AzureSearch" blir "Azure" "Sök". Standardinställningen är sann.<br /><br /> preserveOriginal - Gör att originalord bevaras och läggs till i underordslistan. Standardvärdet är falskt.<br /><br /> splitOnNumerics (typ: bool) - Om det är sant, delas på tal, till exempel "Azure1Search" blir "Azure" "1" "Sök". Standardinställningen är sann.<br /><br /> stemEnglishPossessive (typ: bool) - Orsakar avslutande "s" tas bort för varje underord. Standardinställningen är sann.<br /><br /> protectedWords (typ: strängmatris) - Tokens för att skydda från att avgränsas. Standard är en tom lista.|  

 <sup>1</sup> Tokenfiltertyper är alltid prefixed i kod med "#Microsoft.Azure.Search" så att "ArabicNormalizationTokenFilter" faktiskt skulle anges som "#Microsoft.Azure.Search.ArabicNormalizationTokenFilter".  Vi tog bort prefixet för att minska bredden på tabellen, men kom ihåg att inkludera det i din kod.  


## <a name="see-also"></a>Se även  
 [AZURE Cognitive Search REST API:er](https://docs.microsoft.com/rest/api/searchservice/)   
 [Analysatorer i Azure Cognitive Search > exempel](search-analyzers.md#examples)    
 [Skapa index &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
