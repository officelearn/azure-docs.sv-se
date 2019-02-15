---
title: Analysverktyg för språklig och bearbetning av text - Azure Search
description: Tilldela analysatorer till sökbara fält i ett index att ersätta standard standard Lucene med anpassade, fördefinierade eller språkspecifika varianter.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 5c3894b1f19a6baa65323391526ea5492d79f8a7
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301340"
---
# <a name="analyzers-for-text-processing-in-azure-search"></a>Analysverktyg för textbearbetning i Azure Search

En *analyzer* är en del av den [motorn för fulltextsökning](search-lucene-query-architecture.md) ansvarar för bearbetningen av text i frågesträngar och indexerade dokumenten. Det finns språkanalysverktyg och text manipulering analysverktyg. Språkanalysverktyg är de vanligaste och det är standard språkanalysverktyg som tilldelats varje strängfält i ett Azure Search-index.

Följande språk omvandlingarna är typiska under textanalys:

+ Icke-essential ord (stoppord) och skiljetecken tas bort.
+ Fraser och Avstavade ord är uppdelade i komponenter.
+ Versaler ord är alltid i lägre.
+ Ord minskas till roten formulär så att en matchning finns, oavsett Tempus.

Språk analysverktyg convert textinmatning i primitiv eller rot formulär som är effektivt för lagring av information och hämtning av filer. Konverteringen sker under indexering, när du har skapat indexet och sedan igen vid sökning när indexet läses. Det är mer sannolikt att hämta sökresultat som du förväntar dig om du använder samma analyzer för både.

## <a name="default-analyzer"></a>Standard analyzer  

Använder Azure Search i [Apache Lucene Standard analyzer (standard lucene)](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) som standard, vilket delar upp text i element som följer den [”Unicode Text segmentering”](https://unicode.org/reports/tr29/) regler. Dessutom konverterar standard analysatorn alla tecken till deras gemen form. Både indexerade dokumenten och sökvillkoren kan du gå igenom analysen under indexering och frågebearbetning.  

Den används automatiskt på alla sökbara fält. Du kan åsidosätta standardinställningen på basis av fält i taget. Alternativa analysverktyg kan vara en [språkanalysverktyg](index-add-language-analyzers.md), [anpassat analysverktyg](index-add-custom-analyzers.md), eller en fördefinierad analysverktyget från den [lista över tillgängliga analysverktyg](index-add-custom-analyzers.md#AnalyzerTable).


## <a name="types-of-analyzers"></a>Typer av analysverktyg

I följande lista beskrivs vilka analysverktyg är tillgängliga i Azure Search.

| Kategori | Beskrivning |
|----------|-------------|
| [Analysverktyget från Lucene](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Standard. Ingen specifikation eller konfiguration krävs. Den här allmänna analyzer utför även för de flesta språk och scenarier.|
| Fördefinierade analysverktyg | Erbjuds en färdig produkt som är avsedd att användas som – är. <br/>Det finns två typer: specialiserade och språk. Vad gör dem ”fördefinierade” är att du referera till dem efter namn, utan konfiguration eller anpassning. <br/><br/>[Specialiserad (språkoberoende) analysverktyg](index-add-custom-analyzers.md#AnalyzerTable) används när Textinmatningar kräver särskild bearbetning eller minimal bearbetning. Icke-fördefinierade språkanalysverktyg inkluderar **Asciifolding**, **nyckelordet**, **mönstret**, **enkel**, **stoppa**, **Blanksteg**.<br/><br/>[Språkanalysverktyg](index-add-language-analyzers.md) används när du behöver omfattande språkliga stöd för enskilda språk. Azure Search har stöd för 35 Lucene-språkanalys och 50 analysverktyg för språkbearbetning av Microsoft. |
|[Anpassade analysverktyg](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Refererar till en användardefinierad konfiguration av en kombination av befintliga element, som består av en tokenizer (krävs) och valfritt filter (char eller token).|

Några fördefinierade analysatorer, till exempel **mönstret** eller **stoppa**, stöd för en begränsad uppsättning konfigurationsalternativ. Om du vill ange de här alternativen du effektivt skapar ett anpassat analysverktyg som består av de fördefinierade analzer och en av alternativ som beskrivs i [fördefinierade Analyzer referens](index-add-custom-analyzers.md#AnalyzerTable). Som med valfri anpassad konfiguration, ange den nya konfigurationen med ett namn, till exempel *myPatternAnalyzer* att skilja den från Lucene mönstret analysatorn.

## <a name="how-to-specify-analyzers"></a>Så här anger du analysverktyg

1. (för anpassade analysverktyg) Skapa en namngiven **analyzer** avsnittet i indexdefinitionen. Mer information finns i [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) och även [lägga till anpassade analysverktyg](index-add-custom-analyzers.md).

2. På en [fältet definition](https://docs.microsoft.com/rest/api/searchservice/create-index) i indexet, anger du fältets **analyzer** egenskapen till namnet på en mål-analyzer (till exempel `"analyzer" = "keyword"`. Giltiga värden är namnet på en fördefinierad analyzer, språkanalysverktyg eller anpassat analysverktyg definieras även i indexschemat. Planera hur du tilldelar analyzer i utvecklingsfasen index innan indexet har skapats i tjänsten.

3. Du kan också istället för en **analyzer** egenskapen, som du kan ange olika analysverktyg för indexering och fråga med hjälp av den **indexAnalyzer** och **searchAnalyzer** fält parametrar. 

3. Att lägga till en analyzer i en fältdefinition medför en skrivåtgärd i indexet. Om du lägger till en **analyzer** till ett befintligt index, Tänk på följande:
 
 | Scenario | Påverkan | Steg |
 |----------|--------|-------|
 | Lägg till ett nytt fält | minimal | Om fältet inte finns ännu i schemat, finns det inga fält revision eftersom fältet ännu inte har en fysisk närvaro i ditt index. Använd [uppdatera Index](https://docs.microsoft.com/rest/api/searchservice/update-index) att lägga till ett nytt fält till ett befintligt index.|
 | Lägg till en analyzer till ett befintligt indexerade fält. | [rebuild](search-howto-reindex.md) | Vägar i inverterad indexet för det fältet måste återskapas från grunden upp och innehållet för dessa fält måste indexeras. <br/> <br/>För index utvecklas, [ta bort](https://docs.microsoft.com/rest/api/searchservice/delete-index) och [skapa](https://docs.microsoft.com/rest/api/searchservice/create-index) i index för att hämta den nya fältdefinitionen. <br/> <br/>Du kan fördröja återskapning för index i produktionen genom att skapa ett nytt fält för att ge den omarbetade definitionen och börja använda det i stället för den gamla servern. Använd [uppdatera Index](https://docs.microsoft.com/rest/api/searchservice/update-index) att lägga till det nya fältet och [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) att fylla i itbet. Senare, som en del av index för planerat underhåll, kan du rensa i index för att ta bort föråldrade fält. |

## <a name="when-to-add-analyzers"></a>När du ska lägga till analysverktyg

Du kan definiera flera anpassade analysverktyg varierar kombination av filter, men varje fält kan bara använda en analyzer för indexering analys och en för search-analys.  

Du bör konfigurera analysverktyg under aktivt med utveckling när indexdefinitionen är fortfarande i som. En analyzer som anges på ett fält är en del av fältdefinition, så du kan bara lägga till det när fältet har skapats. Om du vill lägga till analysverktyg i befintliga fält måste du [släpp och återskapa](search-howto-reindex.md) indexet.

Ett undantag är searchAnalyzer variant. Det finns tre sätt att ange analysverktyg: **analyzer**, **indexAnalyzer**, **searchAnalyzer**. Det första **analyzer**, används för både indexerings- och förfrågningar. De andra två kan du styra vilka analysverktyg som används för varje typ av begäran.

Båda **analyzer** och **indexAnalyzer** måste anges på inledande fältdefinition. Den **searchAnalyzer** attribut kan läggas till ett fält som redan finns, utan att det medför ett återskapning krav.

## <a name="recommendations-for-working-with-analyzers"></a>Rekommendationer för att arbeta med analysverktyg

Det här avsnittet ger råd om hur du arbetar med analysverktyg.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>En analyzer för Läs-och om du inte har särskilda krav

Azure Search kan du ange olika analysverktyg för indexering och söka via ytterligare **indexAnalyzer** och **searchAnalyzer** fältet parametrar. Om inget anges analysatorn anges med den **analyzer** egenskapen används för både indexering och sökning. Om `analyzer` är inget anges används standard Standard Lucene analysatorn.

En allmän regel är att använda samma analysatorn för indexering och frågar, såvida inte framgår av specifika krav. Var noga med att testa noggrant. När texten bearbetning skiljer sig på sökning och indexering tid, kör du risken för matchningsfel mellan sökord och indexerade villkor när sökning och indexering analyzer konfigurationer inte är justerade.

### <a name="test-during-active-development"></a>Testa under aktivt med utveckling

Åsidosätta standard analysatorn kräver en återskapning av ett index. Bestäm om möjligt på vilka analysverktyg som ska användas under aktivt med utveckling, innan du distribuerar ett index i produktionen.

### <a name="inspect-tokenized-terms"></a>Inspektera principfilerna villkor

Om en sökning inte returnerar förväntade resultat, är det mest sannolika scenariot token avvikelser mellan termen indata på frågan och principfilerna termer i indexet. Om token som inte är samma, matchar inte Materialisera. Om du vill kontrollera tokenizer utdata, bör du använda den [analysera API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) som ett verktyg. Svaret består av token, som genererats av en specifik analyzer.

### <a name="compare-english-analyzers"></a>Jämför engelska analysverktyg

Den [Sökanalysverktyg](https://alice.unearth.ai/) är en tredjeparts-demonstrationsapp som visar en sida-vid-sida-jämförelse av analysverktyget från Lucene Lucenes engelska analyzer och Microsofts engelska naturligt språk processor. Indexet är oförändrade. den innehåller text från en populära historia. För varje sökinmatning anger du, visas resultaten från varje analyzer i intilliggande fönster, vilket ger dig en uppfattning om hur varje analyzer bearbetar samma sträng. 

## <a name="examples"></a>Exempel

Exemplen nedan visar analyzer definitioner för några viktiga scenarier.

+ [Anpassat analysverktyg exempel](#Example1)
+ [Tilldela ett fält till exempel analysverktyg](#Example2)
+ [Blanda analysverktyg för indexering och sökning](#Example3)
+ [Språk analyzer-exempel](#Example4)

<a name="Example1"></a>

### <a name="custom-analyzer-example"></a>Anpassat analysverktyg exempel

Det här exemplet illustrerar en analyzer-definition med anpassade alternativ. Anpassade alternativ för char-filter, tokenizers och token filter anges separat som namngivna konstruktioner och referenser till i analyzer-definitionen. Fördefinierade element används som – är och bara refereras till av namn.

Gå igenom det här exemplet:

* Analysverktyg är en egenskap i klassen fält för ett sökbart fält.
* Ett anpassat analysverktyg är en del av en Indexdefinition. Det kan lätt anpassas (till exempel hur du anpassar ett alternativ i ett filter) eller anpassade på flera platser.
* I det här fallet anpassade analysatorn är ”my_analyzer”, som i sin tur använder en anpassad standard tokenizer ”my_standard_tokenizer” och två token filter: gemener och anpassade asciifolding filtret ”my_asciifolding”.
* It also defines 2 custom char filters "map_dash" and "remove_whitespace". Den första som ersätter alla bindestreck med understreck medan den andra mallen tar du bort alla blanksteg. Blanksteg måste vara UTF-8-kodat i regler för mappning. Char-filter tillämpas innan tokenisering och påverkar de resulterande token (de standard tokenizer radbrytningar på bindestreck och blanksteg men inte på understreck).

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Example2"></a>

### <a name="per-field-analyzer-assignment-example"></a>Exempel på analyzer tilldelningar per fält

Standard analysatorn är standardinställningen. Anta att du vill ersätta standard med olika fördefinierade analyzer, till exempel mönstret analysatorn. Om du inte ange anpassade alternativ, behöver du bara ange efter namn i fältdefinitionen.

Elementet ”analyzer” åsidosätter Standard analysatorn på basis av fält i taget. Det finns ingen global åsidosättning. I det här exemplet `text1` använder mönstret analysatorn och `text2`, som inte anger en analyzer använder.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Example3"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Blanda analysverktyg för indexering och sökning

API: erna innehåller ytterligare indexattribut för att ange olika analysverktyg för indexering och sökning. Den **searchAnalyzer** och **indexAnalyzer** attribut måste anges som par, ersätter enda **analyzer** attribut.


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Example4"></a>

### <a name="language-analyzer-example"></a>Språk analyzer-exempel

Fält som innehåller strängar i olika språk kan använda ett språkanalysverktyg, medan andra fält behåller du standardvärdet (eller använda vissa andra fördefinierade eller anpassade analyzer). Om du använder ett språkanalysverktyg kan användas den för indexering och sökning. Fält som använder ett språkanalysverktyg kan inte ha olika analysverktyg för indexering och Sök.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="next-steps"></a>Nästa steg

+ Granska våra utförlig förklaring av [hur Fullständig textsökning fungerar i Azure Search](search-lucene-query-architecture.md). Den här artikeln använder exemplen för att förklara beteenden som kan verka krånglig på ytan.

+ Prova ytterligare frågesyntax från den [söka efter dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) exemplet eller från [enkla frågesyntaxen](query-simple-syntax.md) i Sökutforskaren i portalen.

+ Lär dig hur du använder [språkspecifika Lexikalisk](index-add-language-analyzers.md).

+ [Konfigurera anpassade analysverktyg](index-add-custom-analyzers.md) för minimal bearbetning eller särskild bearbetning för enskilda fält.

+ [Jämför standard- och engelska analysverktyg](https://alice.unearth.ai/) i intilliggande fönster på den här demo-webbplatsen. 

## <a name="see-also"></a>Se också

 [Söka efter dokument REST-API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Enkel frågesyntax](query-simple-syntax.md) 

 [Fullständig Lucene-frågesyntax](query-lucene-syntax.md) 
 
 [Hantera sökresultat](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
