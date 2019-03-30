---
title: Analysverktyg för språklig och bearbetning av text - Azure Search
description: Tilldela analysatorer till sökbara fält i ett index att ersätta standard standard Lucene med anpassade, fördefinierade eller språkspecifika varianter.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: e3738980206277587ca367339d75da4f3faa643a
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651829"
---
# <a name="analyzers-for-text-processing-in-azure-search"></a>Analysverktyg för textbearbetning i Azure Search

En *analyzer* är en del av den [motorn för fulltextsökning](search-lucene-query-architecture.md) ansvarar för bearbetningen av text i frågesträngar och indexerade dokumenten. Olika analysverktyg ändra text på olika sätt beroende på scenario. Språkanalysverktyg bearbeta text med språkliga regler för att förbättra kvaliteten för sökning, medan andra analysverktyg utför grundläggande uppgifter, t.ex. Konvertera tecken till gemener, till exempel. 

Språkanalysverktyg är den vanligaste och det är standard språkanalysverktyg som tilldelats varje sökbart fält i ett Azure Search-index. Följande språk omvandlingarna är typiska under textanalys:

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

Några fördefinierade analysatorer, till exempel **mönstret** eller **stoppa**, stöd för en begränsad uppsättning konfigurationsalternativ. Om du vill ange de här alternativen du effektivt skapar ett anpassat analysverktyg som består av fördefinierade analysatorn och en av alternativ som beskrivs i [fördefinierade Analyzer referens](index-add-custom-analyzers.md#AnalyzerTable). Som med valfri anpassad konfiguration, ange den nya konfigurationen med ett namn, till exempel *myPatternAnalyzer* att skilja den från Lucene mönstret analysatorn.

## <a name="how-to-specify-analyzers"></a>Så här anger du analysverktyg

1. (för anpassade analysverktyg) Skapa en namngiven **analyzer** avsnittet i indexdefinitionen. Mer information finns i [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) och även [lägga till anpassade analysverktyg](index-add-custom-analyzers.md).

2. På en [fältet definition](https://docs.microsoft.com/rest/api/searchservice/create-index) i indexet, anger du fältets **analyzer** egenskapen till namnet på en mål-analyzer (till exempel `"analyzer" = "keyword"`. Giltiga värden är namnet på en fördefinierad analyzer, språkanalysverktyg eller anpassat analysverktyg definieras även i indexschemat. Planera hur du tilldelar analyzer i utvecklingsfasen index innan indexet har skapats i tjänsten.

3. Du kan också istället för en **analyzer** egenskapen, som du kan ange olika analysverktyg för indexering och fråga med hjälp av den **indexAnalyzer** och **searchAnalyzer** fält parametrar. Du använder olika analysverktyg för förberedelse av data och hämtning av filer om någon av dessa aktiviteter krävs en omvandling krävs inte för den andra.

Tilldela **analyzer** eller **indexAnalyzer** till ett fält som redan har skapats fysiskt tillåts inte. Om det här är oklart, kontrollera i följande tabell för en analys på detaljnivå av vilka åtgärder måste indexet återskapas och varför.
 
 | Scenario | Påverkan | Steg |
 |----------|--------|-------|
 | Lägg till ett nytt fält | minimal | Om fältet inte finns ännu i schemat, finns det inga fält revision eftersom fältet ännu inte har en fysisk närvaro i ditt index. Du kan använda [uppdatera Index](https://docs.microsoft.com/rest/api/searchservice/update-index) att lägga till ett nytt fält till ett befintligt index och [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) att fylla i den.|
 | Lägg till en **analyzer** eller **indexAnalyzer** till ett befintligt indexerade fält. | [rebuild](search-howto-reindex.md) | Vägar i inverterad indexet för det fältet måste återskapas från grunden och innehållet för dessa fält måste indexeras. <br/> <br/>För index utvecklas, [ta bort](https://docs.microsoft.com/rest/api/searchservice/delete-index) och [skapa](https://docs.microsoft.com/rest/api/searchservice/create-index) i index för att hämta den nya fältdefinitionen. <br/> <br/>Du kan fördröja återskapning av en genom att skapa ett nytt fält för att ge den omarbetade definitionen och börja använda det i stället för gamla för index i produktion. Använd [uppdatera Index](https://docs.microsoft.com/rest/api/searchservice/update-index) att lägga till det nya fältet och [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) att fylla i den. Senare, som en del av index för planerat underhåll, kan du rensa i index för att ta bort föråldrade fält. |

## <a name="when-to-add-analyzers"></a>När du ska lägga till analysverktyg

Det är bäst att lägga till och tilldela analysverktyg är under aktivt med utveckling, när släppa och återskapa index är rutin.

Som en Indexdefinition solidifies, du kan lägga till nya analysis-konstruktioner till ett index, men du kommer att behöva skicka den **allowIndexDowntime** flaggan till [uppdatera Index](https://docs.microsoft.com/rest/api/searchservice/update-index) om du vill undvika det här felet:

*”Index uppdatering tillåts inte eftersom det skulle orsaka driftstopp. Ange Frågeparametern 'allowIndexDowntime' till 'true' i begäran om uppdatering index för att lägga till nya analysverktyg, tokenizers, token filter eller tecknet filter till ett befintligt index. Observera att den här åtgärden placeras i ditt index offline för minst ett par sekunder, och din indexering och frågebegäranden misslyckas. Prestanda- och skrivbehörighet tillgängligheten för indexet kan vara försämrad i flera minuter efter att indexet har uppdaterats eller längre för mycket stora index ”.*

Detsamma gäller när du tilldelar en analyzer till ett fält. En analyzer är en del av fältdefinition, så du kan bara lägga till det när fältet har skapats. Om du vill lägga till analysverktyg i befintliga fält måste du [släpp och återskapa](search-howto-reindex.md) index, eller lägga till ett nytt fält med analyzer som du vill.

Enligt vad som anges, ett undantag är den **searchAnalyzer** variant. Tre sätt att ange analysverktyg (**analyzer**, **indexAnalyzer**, **searchAnalyzer**), bara de **searchAnalyzer** attribut kan ändras på ett befintligt fält.

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

<a name="examples"></a>

## <a name="rest-examples"></a>REST-exempel

Exemplen nedan visar analyzer definitioner för några viktiga scenarier.

+ [Anpassat analysverktyg exempel](#Custom-analyzer-example)
+ [Tilldela ett fält till exempel analysverktyg](#Per-field-analyzer-assignment-example)
+ [Blanda analysverktyg för indexering och sökning](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Språk analyzer-exempel](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

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

<a name="Per-field-analyzer-assignment-example"></a>

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

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

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

<a name="Language-analyzer-example"></a>

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

## <a name="c-examples"></a>C#exempel

Om du använder .NET SDK-kodexempel, kan du lägga till de här exemplen för att använda eller konfigurera analysverktyg.

+ [Tilldela en inbyggd analyzer](#Assign-a-language-analyzer)
+ [Konfigurera en analyzer](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Tilldela ett språkanalysverktyg

Alla analyzer som används som – är utan konfiguration, har angetts på en fältdefinition av. Det finns inga krav för att skapa en analyzer-konstruktion. 

Det här exemplet tilldelar Microsoft English och franska analysverktyg beskrivning. Det är ett kodfragment som kommer från en större definition av det hotellindex som skapas med hjälp av klassen hotell i filen hotels.cs för den [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) exemplet.

Anropa [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet), att ange den [AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) typ att tillhandahålla en text analyzer som stöds i Azure Search.

```csharp
    public partial class Hotel
    {
       . . . 

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        [JsonProperty("description")]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("description_fr")]
        public string DescriptionFr { get; set; }

      . . .
    }
```
<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>Definiera ett anpassat analysverktyg

När anpassning eller konfiguration krävs måste du lägga till en analyzer-konstruktion till ett index. När du har definierat du kan lägga till det fältdefinitionen som visas i exemplet ovan.

Skapa en [CustomAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) objekt. Fler exempel finns i [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/src/SDKs/Search/DataPlane/Search.Tests/Tests/CustomAnalyzerTests.cs).

```csharp
{
   var definition = new Index()
   {
         Name = "hotels",
         Fields = FieldBuilder.BuildForType<Hotel>(),
         Analyzers = new[]
            {
               new CustomAnalyzer()
               {
                     Name = "url-analyze",
                     Tokenizer = TokenizerName.UaxUrlEmail,
                     TokenFilters = new[] { TokenFilterName.Lowercase }
               }
            },
   };

   serviceClient.Indexes.Create(definition);
```

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
