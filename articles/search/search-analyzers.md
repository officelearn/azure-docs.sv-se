---
title: Analysverktyg för språklig och bearbetning av text - Azure Search
description: Tilldela analysatorer till sökbara fält i ett index att ersätta standard standard Lucene med anpassade, fördefinierade eller språkspecifika varianter.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 868658062a6407dce901b455cc92f95008df798c
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53631954"
---
# <a name="analyzers-for-text-processing-in-azure-search"></a>Analysverktyg för textbearbetning i Azure Search

En *analyzer* är en komponent i [fulltextsökning](search-lucene-query-architecture.md) ansvarar för bearbetningen av text i frågesträngar och indexerade dokumenten. Följande omvandlingar är typiska under analysen:

+ Icke-essential ord (stoppord) och skiljetecken tas bort.
+ Fraser och Avstavade ord är uppdelade i komponenter.
+ Versaler ord är alltid i lägre.
+ Ord minskas till roten formulär så att en matchning finns, oavsett Tempus.

Lingvistiska analysverktyg convert textinmatning i primitiv eller rot formulär som är effektivt för lagring av information och hämtning av filer. Konverteringen sker under indexering, när du har skapat indexet och sedan igen vid sökning när indexet läses. Det är mer sannolikt att hämta sökresultat som du förväntar dig om du använder samma text analyzer för både.

Använder Azure Search i [Standard Lucene analyzer](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) som standard. Du kan åsidosätta standardinställningen på basis av fält i taget. Den här artikeln beskrivs olika alternativen och bästa praxis för anpassad analys. Det ger också exempelkonfigurationer för viktiga scenarier.

## <a name="supported-analyzers"></a>Stöds analysverktyg

I följande lista beskrivs vilka analysverktyg stöds i Azure Search.

| Kategori | Beskrivning |
|----------|-------------|
| [Analysverktyget från Lucene](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Standard. Ingen specifikation eller konfiguration krävs. Den här allmänna analyzer utför även för de flesta språk och scenarier.|
| Fördefinierade analysverktyg | Erbjuds en färdig produkt som är avsedd att användas som – är med begränsad anpassning. <br/>Det finns två typer: specialiserade och språk. Vad gör dem ”fördefinierade” är att du referera till dem efter namn, med finns inte någon anpassning. <br/><br/>[Specialiserad (språkoberoende) analysverktyg](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) används när Textinmatningar kräver särskild bearbetning eller minimal bearbetning. Icke-fördefinierade språkanalysverktyg inkluderar **Asciifolding**, **nyckelordet**, **mönstret**, **enkel**, **stoppa**, **Blanksteg**.<br/><br/>[Språkanalysverktyg](https://docs.microsoft.com/rest/api/searchservice/language-support) används när du behöver omfattande språkliga stöd för enskilda språk. Azure Search har stöd för 35 Lucene-språkanalys och 50 analysverktyg för språkbearbetning av Microsoft. |
|[Anpassade analysverktyg](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | En användardefinierad konfiguration av en kombination av befintliga element, som består av en tokenizer (krävs) och valfritt filter (char eller token).|

Du kan anpassa en fördefinierad analyzer, till exempel **mönstret** eller **stoppa**ska gå att använda alternativ som beskrivs i [fördefinierade Analyzer referens](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable). Bara några av de fördefinierade analysverktyg har alternativ som du kan ange. Som med någon anpassning, ange den nya konfigurationen med ett namn, till exempel *myPatternAnalyzer* att skilja den från Lucene mönstret analysatorn.

## <a name="how-to-specify-analyzers"></a>Så här anger du analysverktyg

1. (för anpassade analysverktyg) Skapa en **analyzer** avsnittet i indexdefinitionen. Mer information finns i [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) och även [anpassade analysverktyg > Skapa](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer).

2. På en [fältet definition](https://docs.microsoft.com/rest/api/searchservice/create-index) i indexet, ange den **analyzer** egenskapen till namnet på en mål-analyzer (till exempel `"analyzer" = "keyword"`. Giltiga värden är namnet på en fördefinierad analyzer, språkanalysverktyg eller anpassat analysverktyg definieras även i indexschemat.

3. Du kan också istället för en **analyzer** egenskapen, som du kan ange olika analysverktyg för indexering och fråga med hjälp av den **indexAnalyzer** och **searchAnalyzer'** fält parametrar. 

3. Att lägga till en analyzer i en fältdefinition medför en skrivåtgärd i indexet. Om du lägger till en **analyzer** till ett befintligt index, Tänk på följande:
 
 | Scenario | Påverkan | Steg |
 |----------|--------|-------|
 | Lägg till ett nytt fält | Minimal | Om fältet inte finns ännu i schemat, finns det inga fält revision eftersom fältet ännu inte har en fysisk närvaro i ditt index. Använd [uppdatera Index](https://docs.microsoft.com/rest/api/searchservice/update-index) och [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) för den här uppgiften.|
 | Lägg till en analyzer till ett befintligt indexerade fält. | Återskapa | Vägar i inverterad indexet för det fältet måste återskapas från grunden upp och innehållet för dessa fält måste indexeras. <br/> <br/>För index utvecklas, [ta bort](https://docs.microsoft.com/rest/api/searchservice/delete-index) och [skapa](https://docs.microsoft.com/rest/api/searchservice/create-index) i index för att hämta den nya fältdefinitionen. <br/> <br/>Index i produktion, bör du skapa ett nytt fält för att ge den omarbetade definitionen och börja använda den. Använd [uppdatera Index](https://docs.microsoft.com/rest/api/searchservice/update-index) och [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) att lägga till det nya fältet. Senare, som en del av index för planerat underhåll, kan du rensa i index för att ta bort föråldrade fält. |

## <a name="tips-and-best-practices"></a>Tips och råd

Det här avsnittet ger råd om hur du arbetar med analysverktyg.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>En analyzer för Läs-och om du inte har särskilda krav

Azure Search kan du ange olika analysverktyg för indexering och söka via ytterligare `indexAnalyzer` och `searchAnalyzer` fältet parametrar. Om inget anges analysatorn anges med den `analyzer` egenskapen används för både indexering och sökning. Om `analyzer` är inget anges används standard Standard Lucene analysatorn.

En allmän regel är att använda samma analysatorn för indexering och frågar, såvida inte framgår av specifika krav. Var noga med att testa noggrant. När texten bearbetning skiljer sig på sökning och indexering tid, kör du risken för matchningsfel mellan sökord och indexerade villkor när sökning och indexering analyzer konfigurationer inte är justerade.

### <a name="test-during-active-development"></a>Testa under aktivt med utveckling

Åsidosätta standard analysatorn kräver en återskapning av ett index. Bestäm om möjligt på vilka analysverktyg som ska användas under aktivt med utveckling, innan du distribuerar ett index i produktionen.

### <a name="inspect-tokenized-terms"></a>Inspektera principfilerna villkor

Om en sökning inte returnerar förväntade resultat, är det mest sannolika scenariot token avvikelser mellan termen indata på frågan och principfilerna termer i indexet. Om token som inte är samma, matchar inte Materialisera. Om du vill kontrollera tokenizer utdata, bör du använda den [analysera API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) som ett verktyg. Svaret består av token, som genererats av en specifik analyzer.

### <a name="compare-english-analyzers"></a>Jämför engelska analysverktyg

Den [Sökanalysverktyg](https://alice.unearth.ai/) är en tredjeparts-demonstrationsapp som visar en sida-vid-sida-jämförelse av analysverktyget från Lucene Lucenes engelska analyzer och Microsofts engelska naturligt språk processor. Indexet är oförändrade. den innehåller text från en populära historia. För varje sökinmatning anger du, visas resultaten från varje analyzer i intilliggande fönster, vilket ger dig en uppfattning om hur varje analyzer bearbetar samma sträng. 

## <a name="examples"></a>Exempel

Exemplen nedan visar analyzer definitioner för några viktiga scenarier.

<a name="Example1"></a>
### <a name="example-1-custom-options"></a>Exempel 1: Anpassade alternativ

Det här exemplet illustrerar en analyzer-definition med anpassade alternativ. Anpassade alternativ för char-filter, tokenizers och token filter anges separat som namngivna konstruktioner och referenser till i analyzer-definitionen. Fördefinierade element används som – är och bara refereras till av namn.

Gå igenom det här exemplet:

* Analysverktyg är en egenskap i klassen fält för ett sökbart fält.
* Ett anpassat analysverktyg är en del av en Indexdefinition. Det kan lätt anpassas (till exempel hur du anpassar ett alternativ i ett filter) eller anpassade på flera platser.
* I det här fallet anpassade analysatorn är ”my_analyzer”, som i sin tur använder en anpassad standard tokenizer ”my_standard_tokenizer” och två token filter: gemener och anpassade asciifolding filtret ”my_asciifolding”.
* Den definierar även ett anpassad ”map_dash” char filter om du vill ersätta alla bindestreck med understreck innan tokenisering (de standard tokenizer radbrytningar på dash men inte på understreck).

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
### <a name="example-2-override-the-default-analyzer"></a>Exempel 2: Åsidosätt standard analyzer

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
### <a name="example-3-different-analyzers-for-indexing-and-search-operations"></a>Exempel 3: Olika analysverktyg för indexering och sökning

API: erna innehåller ytterligare indexattribut för att ange olika analysverktyg för indexering och sökning. Den `searchAnalyzer` och `indexAnalyzer` attribut måste anges som par, ersätter enda `analyzer` attribut.


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
### <a name="example-4-language-analyzer"></a>Exempel 4: Språkanalysverktyg

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

+ Prova ytterligare frågesyntax från den [söka efter dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) exemplet eller från [enkla frågesyntaxen](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) i Sökutforskaren i portalen.

+ Lär dig hur du använder [språkspecifika Lexikalisk](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Konfigurera anpassade analysverktyg](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) för minimal bearbetning eller särskild bearbetning för enskilda fält.

+ [Jämför standard- och engelska analysverktyg](https://alice.unearth.ai/) i intilliggande fönster på den här demo-webbplatsen. 

## <a name="see-also"></a>Se också

 [Söka efter dokument REST-API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Enkel frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

 [Fullständig Lucene-frågesyntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 
 [Hantera sökresultat](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
