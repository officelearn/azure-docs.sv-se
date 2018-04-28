---
title: Analyzers i Azure Search | Microsoft Docs
description: Tilldela analyzers till sökbara fält i ett index ersätta standard standard Lucene med anpassade, fördefinierade eller språkspecifika alternativ.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.openlocfilehash: e858966fb5a15b84af1952399a5eff3ca50d0d59
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="analyzers-in-azure-search"></a>Analysverktyg i Azure Search

En *analyzer* är en del av [fulltextsökning](search-lucene-query-architecture.md) ansvarar för bearbetning av text i frågesträngar och indexerade dokument. Följande omvandlingar är vanliga vid analys av:

+ Icke-viktigt ord (stoppord) och skiljetecken tas bort.
+ Fraser och Avstavade ord kan delas upp i delar.
+ Versaler ord är alltid i nedre.
+ Ord minskas till roten formulär så att det finns en matchning oavsett Tempus.

Språkliga analyzers konvertera en text som indata för primitiva eller rot-formulär som är effektiva för lagring av information och hämtning. Konvertering inträffar under indexeringen när indexet skapas, och sedan igen under sökningen när indexet läses. Det är mer sannolikt att hämta sökresultat som du förväntar dig om du använder samma text analyzer för båda åtgärder.

Azure Search använder den [Standard Lucene analyzer](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) som standard. Du kan åsidosätta standardvärdet på grundval av fält. Den här artikeln beskrivs olika alternativen och bästa praxis för anpassade analys. Det ger också exempelkonfigurationer för viktiga scenarier.

## <a name="supported-analyzers"></a>Stöds analyzers

I följande lista beskrivs vilka analyzers stöds i Azure Search.

| Kategori | Beskrivning |
|----------|-------------|
| [Standard Lucene analyzer](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Som standard. Det krävs ingen specifikation eller konfigurationen. Detta allmänna analyzer utför bra för de flesta scenarier och språk.|
| Fördefinierade analyzers | Erbjuds en färdig produkt som är avsedd att användas som-är med begränsad anpassning. <br/>Det finns två typer: specialiserade och språk. Vad gör dem ”fördefinierade” är att referera med namn med Ingen anpassning. <br/><br/>[Specialiserad (språk oberoende) analyzers](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) används när texten indata kräver särskild bearbetning eller minimal bearbetning. Icke-fördefinierade språkanalys inkluderar **Asciifolding**, **nyckelordet**, **mönster**, **enkel**, **stoppa**, **Blanksteg**.<br/><br/>[Språkanalys](https://docs.microsoft.com/rest/api/searchservice/language-support) används när du behöver stöd för omfattande språkliga för enskilda språk. Azure Search stöder 35 Lucene språkanalys och 50 Microsoft naturligt språk bearbetning analyzers. |
|[Anpassade analysverktyg](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | En användardefinierad konfiguration av en kombination av befintliga element, som består av en tokenizer (krävs) och valfritt filter (char eller token).|

Du kan anpassa en fördefinierad analyzer som **mönster** eller **stoppa**ska gå att använda alternativ som beskrivs i [fördefinierade Analyzer referens](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable). Några av de fördefinierade analyzers har alternativ som du kan ange. Som med någon anpassning att ge den nya konfigurationen med ett namn som *myPatternAnalyzer* att skilja den från Lucene mönster analyzer.

## <a name="how-to-specify-analyzers"></a>Så här anger du analyzers

1. (för anpassade analyzers) Skapa en **analyzer** avsnitt i indexdefinitionen. Mer information finns i [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) och även [anpassad Analyzers > Skapa](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer).

2. På en [fältet definition](https://docs.microsoft.com/rest/api/searchservice/create-index) i index, ange den **analyzer** egenskapen till namnet på en mål-analyzer (till exempel `"analyzer" = "keyword"`. Giltiga värden är namnet på en fördefinierad analyzer, språk analyzer eller anpassade analyzer som definierats i schemat index.

3. Alternativt kan du i stället för en **analyzer** egenskapen som du kan ange olika analyzers för indexering och fråga med hjälp av den **indexAnalyzer** och **searchAnalyzer'** fält parametrar. 

3. Lägga till en analyzer i en fältdefinition av har en skrivåtgärd i indexet. Om du lägger till en **analyzer** till ett befintligt index Observera följande steg:
 
 | Scenario | Påverkan | Steg |
 |----------|--------|-------|
 | Lägga till ett nytt | Minimal | Om fältet inte finns ännu i schemat, finns det inga fält revision eftersom fältet ännu inte har fysisk närvaro i ditt index. Använd [uppdatera Index](https://docs.microsoft.com/rest/api/searchservice/update-index) och [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) för den här uppgiften.|
 | Lägg till en analyzer till ett befintligt indexerade fält. | Återskapa | Omvända indexet för det här fältet måste återskapas från grunden upp och innehåll för de här fälten måste indexeras. <br/> <br/>För index under active utveckling [ta bort](https://docs.microsoft.com/rest/api/searchservice/delete-index) och [skapa](https://docs.microsoft.com/rest/api/searchservice/create-index) indexet för att hämta den nya fältdefinitionen. <br/> <br/>Du bör skapa ett nytt fält för att ange den omarbetade definitionen och börja använda det för index i produktion. Använd [uppdatera Index](https://docs.microsoft.com/rest/api/searchservice/update-index) och [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) att infoga det nya fältet. Du kan senare som en del av planerad index behandlingen Rensa indexet för att ta bort föråldrade fält. |

## <a name="tips-and-best-practices"></a>Tips och råd

Det här avsnittet innehåller anvisningar om hur du arbetar med analyzers.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>En analyzer för skrivskyddad såvida du inte har särskilda krav

Azure Search kan du ange olika analyzers för indexering och söka via ytterligare `indexAnalyzer` och `searchAnalyzer` fältet parametrar. Om inget anges analyzer anges med den `analyzer` egenskapen används för både indexering och sökning. Om `analyzer` är inget anges används standard Standard Lucene analyzer.

En allmän regel är att använda samma analyzer för indexering och frågar, om specifika krav kräver något annat. Se till att testa noggrant. När texten bearbetning skiljer sig vid sökning och indexering tid, kör du risken för matchningsfel mellan sökord och indexerade villkor när sökning och indexering analyzer konfigurationer inte är justerade.

### <a name="test-during-active-development"></a>Testa under active utvecklingen

Åsidosätta standard analyzer kräver en återskapa indexet. Besluta om möjligt om vilka analyzers ska användas under active utveckling, innan du distribuerar ett index i produktionen.

### <a name="inspect-tokenized-terms"></a>Inspektera principfilerna villkor

Om en sökning misslyckas med att returnera förväntat resultat, är det mest sannolika scenariot token skillnaderna mellan termen indata på frågan och principfilerna termer i indexet. Om token som inte är samma, inte matchar att Materialisera. Om du vill granska tokenizer utdata, bör du använda den [analysera API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) som ett verktyg. Svaret består av token som genererats av en specifik analyzer.

### <a name="compare-english-analyzers"></a>Jämför engelska analyzers

Den [Sök Analyzer Demo](http://alice.unearth.ai/) är en tredje parts demo-appen som visar en sida-vid-sida-jämförelse av standard Lucene analyzer Lucenes engelska analyzer och Microsofts engelska naturligt språk processor. Indexet är oförändrade. den innehåller text från en populär artikel. För varje sökning indata anger du måste visas resultaten från varje analyzer intilliggande rutor, vilket ger dig en uppfattning om hur varje analyzer bearbetar samma sträng. 

## <a name="examples"></a>Exempel

Exemplen nedan visar analyzer definitioner för några vanliga scenarier.

<a name="Example1"></a>
### <a name="example-1-custom-options"></a>Exempel 1: Anpassade alternativ

Det här exemplet illustrerar en analyzer definition med anpassade alternativ. Anpassade alternativ för char-filter, tokenizers och token filter anges separat som namngivna konstruktioner och sedan refereras i analyzer-definition. Fördefinierade element för som-är och bara refereras av namn.

Gå igenom det här exemplet:

* Analyzers är en egenskap i klassen fältet för ett sökbara fält.
* En anpassad analyzer är en del av en Indexdefinition. Det kan lätt anpassas (till exempel hur du anpassar ett alternativ i ett filter) eller anpassade på flera platser.
* I det här fallet anpassade analyzer är ”my_analyzer”, som i sin tur använder en anpassad standard tokenizer ”my_standard_tokenizer” och två token filter: gemener och anpassade asciifolding filter ”my_asciifolding”.
* Den definierar även en anpassad ”map_dash” char-filter för att ersätta alla bindestreck med understreck innan tokenisering (de vanliga tokenizer radbrytningar på dash men inte på understreck).

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
              "map_dash"
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
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
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
### <a name="example-2-override-the-default-analyzer"></a>Exempel 2: Åsidosätta standard analyzer

Standard analyzer är standardinställningen. Anta att du vill ersätta standard med en annan fördefinierade analyzer, till exempel mönster analyzer. Om du inte ange anpassade alternativ, behöver du bara ange namnet i fältdefinitionen.

Elementet ”analyzer” åsidosätter Standard analyzer på grundval av fält. Det finns ingen global åsidosättning. I det här exemplet `text1` använder mönstret analyzer och `text2`, som inte anger en analyzer använder standardvärdet.

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
### <a name="example-3-different-analyzers-for-indexing-and-search-operations"></a>Exempel 3: Olika analyzers för fulltextindexering och åtgärder

API: er innehåller ytterligare indexattribut för att ange olika analyzers för indexering och sökning. Den `searchAnalyzer` och `indexAnalyzer` attribut måste anges som ett par ersätter enda `analyzer` attribut.


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
### <a name="example-4-language-analyzer"></a>Exempel 4: Språk analyzer

Fält som innehåller strängar i olika språk kan använda ett språk analyzer, medan andra fälten behåller du standardvärdet (eller använda vissa andra fördefinierade eller anpassade analyzer). Om du använder ett språk analyzer kan användas för indexering och sökning. Fält som använder ett språk analyzer kan inte ha olika analyzers för indexering och söka.

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

+ Granska våra utförlig förklaring av [hur Fullständig textsökning fungerar i Azure Search](search-lucene-query-architecture.md). Den här artikeln använder exempel för att förklara beteenden som kan verka krånglig på ytan.

+ Försök ytterligare frågesyntaxen från den [Sök dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) exempel avsnittet eller från [enkel frågesyntaxen](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) i Sök explorer i portalen.

+ Lär dig hur du använder [språkspecifika lexikala analyzers](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Konfigurera anpassade analyzers](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) för minimal bearbetning eller särskilda bearbetning av enskilda fält.

+ [Jämför standard och engelska analyzers](http://alice.unearth.ai/) i intilliggande fönster på den här demo-webbplatsen. 

## <a name="see-also"></a>Se också

 [Sök dokument REST-API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Enkel frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

 [Fullständig Lucene frågesyntaxen](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 
 [Hantera sökresultat](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
