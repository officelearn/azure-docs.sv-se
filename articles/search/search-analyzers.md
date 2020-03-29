---
title: Analysatorer för språk- och textbehandling
titleSuffix: Azure Cognitive Search
description: Tilldela analysatorer till sökbara textfält i ett index för att ersätta standardstandarden Lucene med anpassade, fördefinierade eller språkspecifika alternativ.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: 2e4a6ab8825982969ffa4654c2418f7a9d168d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460710"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Analysatorer för textbearbetning i Azure Cognitive Search

En *analysator* är en komponent i [den fulltextsökmotor](search-lucene-query-architecture.md) som ansvarar för bearbetning av text i frågesträngar och indexerade dokument. Olika analysatorer manipulerar text på olika sätt beroende på scenariot. Språkanalysatorer bearbetar text med hjälp av språkliga regler för att förbättra sökkvaliteten, medan andra analysatorer utför mer grundläggande uppgifter som att konvertera tecken till gemener, till exempel. 

Språkanalysatorer är de vanligaste och det finns standardspråkanalysator som tilldelats alla sökbara fält i ett Azure Cognitive Search-index. Följande språkomvandlingar är typiska vid textanalys:

+ Icke-väsentliga ord (stopwords) och interpunktion tas bort.
+ Fraser och avstavade ord delas upp i komponenter.
+ Versaler är gemener.
+ Ord reduceras till rotformer så att en matchning kan hittas oavsett tempus.

Språkanalysatorer konverterar en textinmatning till primitiva eller rotformer som är effektiva för informationslagring och hämtning. Konvertering sker under indexeringen, när indexet skapas och sedan igen under sökning när indexet läss. Det är mer troligt att du får de sökresultat du förväntar dig om du använder samma analysator för båda åtgärderna.

## <a name="default-analyzer"></a>Standardanalysator  

Azure Cognitive Search använder [Apache Lucene Standard-analysatorn (standard lucene)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) som standard, som bryter upp text i element som följer reglerna [för "Unicode-textsegmentering".](https://unicode.org/reports/tr29/) Dessutom konverterar standardanalysatorn alla tecken till sitt gemener. Både indexerade dokument och söktermer går igenom analysen under indexering och frågebearbetning.  

Den används automatiskt i alla sökbara fält. Du kan åsidosätta standardinställningen för fält för fält. Alternativa analysatorer kan vara en [språkanalysator,](index-add-language-analyzers.md) [anpassad analysator](index-add-custom-analyzers.md)eller en fördefinierad analysator från [listan över tillgängliga analysatorer](index-add-custom-analyzers.md#AnalyzerTable).


## <a name="types-of-analyzers"></a>Typer av analysatorer

I följande lista beskrivs vilka analysatorer som är tillgängliga i Azure Cognitive Search.

| Kategori | Beskrivning |
|----------|-------------|
| [Standard Lucene analysator](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Standard. Ingen specifikation eller konfiguration krävs. Den här analysatorn för allmänna ändamål fungerar bra för de flesta språk och scenarier.|
| Fördefinierade analysatorer | Erbjuds som en färdig produkt avsedd att användas som den är. <br/>Det finns två typer: specialiserade och språk. Det som gör dem "fördefinierade" är att du refererar till dem med namn, utan konfiguration eller anpassning. <br/><br/>[Specialiserade (språkagnostiska) analysatorer](index-add-custom-analyzers.md#AnalyzerTable) används när textindata kräver specialiserad bearbetning eller minimal bearbetning. Icke-språk fördefinierade analysatorer inkluderar **Asciifolding**, **Nyckelord**, **Mönster**, **Enkel**, **Stopp**, **Blankspace**.<br/><br/>[Språkanalysatorer](index-add-language-analyzers.md) används när du behöver ett rikt språkligt stöd för enskilda språk. Azure Cognitive Search stöder 35 Lucene-språkanalysatorer och 50 Microsofts analysatorer för bearbetning av naturligt språk. |
|[Anpassade analysverktyg](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Refererar till en användardefinierad konfiguration av en kombination av befintliga element, bestående av en tokenizer (obligatorisk) och valfria filter (tecken eller token).|

Några fördefinierade analysatorer, till exempel **Mönster** eller **Stopp,** stöder en begränsad uppsättning konfigurationsalternativ. Om du vill ange dessa alternativ skapar du effektivt en anpassad analysator som består av den fördefinierade analysatorn och ett av de alternativa alternativen som dokumenteras i [Fördefinierad analysorreferens](index-add-custom-analyzers.md#AnalyzerTable). Som med alla anpassade konfiguration, ge din nya konfiguration med ett namn, till exempel *myPatternAnalyzer* för att skilja den från Lucene Pattern analyzer.

## <a name="how-to-specify-analyzers"></a>Så här anger du analysatorer

1. (endast för anpassade analysatorer) Skapa ett namngivet **analysavsnitt** i indexdefinitionen. Mer information finns i [Skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index) och även Lägga till [anpassade analysatorer](index-add-custom-analyzers.md).

2. På en [fältdefinition](https://docs.microsoft.com/rest/api/searchservice/create-index) i indexet anger du fältets **analysatoregenskap** till namnet `"analyzer" = "keyword"`på en målanalysator (till exempel . Giltiga värden inkluderar namnet på en fördefinierad analysator, språkanalysator eller anpassad analysator som också definieras i indexschemat. Planera att tilldela analysatorn i indexdefinitionsfasen innan indexet skapas i tjänsten.

3. Alternativt kan du i stället för en **analysatoregenskap** ange olika analysatorer för indexering och frågor med hjälp av fältparametrarna **indexAnalyzer** och **searchAnalyzer.** Du skulle använda olika analysatorer för dataförberedelse och hämtning om någon av dessa aktiviteter krävde en specifik omvandling som inte behövs av den andra.

> [!NOTE]
> Det går inte att använda en annan [språkanalysator](index-add-language-analyzers.md) vid indexeringstiden än vid frågetillfället för ett fält. Den funktionen är reserverad för [anpassade analysatorer](index-add-custom-analyzers.md). Om du försöker ställa in egenskaperna **searchAnalyzer** eller **indexAnalyzer** till namnet på en språkanalysator returnerar REST API därför ett felsvar. Du måste använda egenskapen **analyzer** i stället.

Det är inte tillåtet att tilldela **analysator** eller **indexAnalyzer** till ett fält som redan har skapats fysiskt. Om något av detta är oklart, granska följande tabell för en uppdelning av vilka åtgärder som kräver en ombyggnad och varför.
 
 | Scenario | Påverkan | Steg |
 |----------|--------|-------|
 | Lägga till ett nytt fält | Minimal | Om fältet ännu inte finns i schemat finns det ingen fältrevision att göra eftersom fältet ännu inte har någon fysisk närvaro i indexet. Du kan använda [Uppdatera index](https://docs.microsoft.com/rest/api/searchservice/update-index) för att lägga till ett nytt fält i ett befintligt index och [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) för att fylla i det.|
 | Lägg till en **analysator** eller **indexAnalyzer** i ett befintligt indexerat fält. | [Bygga](search-howto-reindex.md) | Det inverterade indexet för det fältet måste återskapas från grunden och innehållet för dessa fält måste indexeras om. <br/> <br/>För index under aktiv utveckling [tar du bort](https://docs.microsoft.com/rest/api/searchservice/delete-index) och [skapar](https://docs.microsoft.com/rest/api/searchservice/create-index) indexet för att hämta den nya fältdefinitionen. <br/> <br/>För produktionsindex kan du skjuta upp en ombyggnad genom att skapa ett nytt fält för att ange den reviderade definitionen och börja använda den i stället för den gamla. Använd [Uppdatera index](https://docs.microsoft.com/rest/api/searchservice/update-index) för att infoga det nya fältet och [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) för att fylla i det. Senare, som en del av planerad indexservice, kan du rensa indexet för att ta bort föråldrade fält. |

## <a name="when-to-add-analyzers"></a>När ska analysatorer läggas till

Den bästa tiden att lägga till och tilldela analysatorer är under aktiv utveckling, när släppa och återskapa index är rutin.

Som en indexdefinition stelnar kan du lägga till nya analyskonstruktioner i ett index, men du måste skicka flaggan **allowIndexDowntime** till [Uppdatera index](https://docs.microsoft.com/rest/api/searchservice/update-index) om du vill undvika det här felet:

*"Indexuppdatering tillåts inte eftersom det skulle orsaka driftstopp. För att lägga till nya analysatorer, tokenizers, tokenfilter eller teckenfilter till ett befintligt index anger du frågeparametern allowIndexDowntime till 'true' i indexuppdateringsbegäran. Observera att den här åtgärden kommer att sätta ditt index offline i minst några sekunder, vilket gör att indexeringen och frågebegäranden misslyckas. Indexets prestanda- och skrivtillgänglighet kan försämras i flera minuter efter att indexet har uppdaterats, eller längre för mycket stora index."*

Detsamma gäller när du tilldelar en analysator till ett fält. En analysator är en integrerad del av fältets definition, så du kan bara lägga till den när fältet skapas. Om du vill lägga till analysatorer i befintliga fält måste du [släppa och återskapa](search-howto-reindex.md) indexet eller lägga till ett nytt fält med den analysator du vill använda.

Som nämnts är ett undantag **searchAnalyzer-varianten.** Av de tre sätten att ange analysatorer (**analysator**, **indexAnalyzer**, **searchAnalyzer**) kan endast **attributet searchAnalyzer** ändras i ett befintligt fält.

## <a name="recommendations-for-working-with-analyzers"></a>Rekommendationer för att arbeta med analysatorer

Detta avsnitt ger råd om hur man arbetar med analysatorer.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>En analysator för läs-skriv om du inte har specifika krav

Med Azure Cognitive Search kan du ange olika analysatorer för indexering och sökning via ytterligare **indexAnalyzer-** och **searchAnalyzer-fältparametrar.** Om det inte anges används **analyzer** analysatoruppsättningen med analysatoregenskapen för både indexering och sökning. Om `analyzer` är ospecificerat används standardanalysatorn Standard Lucene.

En allmän regel är att använda samma analysator för både indexering och frågor, om inte specifika krav kräver något annat. Var noga med att testa noggrant. När textbearbetningen skiljer sig åt vid sök- och indexeringstid riskerar du att inte matcha mellan frågetermer och indexerade termer när konfigurationerna för sök- och indexeringsanalyser inte är justerade.

### <a name="test-during-active-development"></a>Test under aktiv utveckling

Åsidosättning av standardanalysatorn kräver en indexrenaning. Om möjligt, besluta om vilka analysatorer som ska användas under aktiv utveckling, innan du rullar ett index till produktion.

### <a name="inspect-tokenized-terms"></a>Granska tokeniserade termer

Om en sökning misslyckas med att returnera förväntade resultat är det mest sannolika scenariot tokenavvikelser mellan termindata i frågan och tokeniserade termer i indexet. Om token inte är desamma, misslyckas matchningar att materialiseras. Om du vill granska tokenizer-utdata rekommenderar vi att du använder [analys-API:et](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) som ett undersökningsverktyg. Svaret består av token, som genereras av en specifik analysator.

<a name="examples"></a>

## <a name="rest-examples"></a>EXEMPEL PÅ REST

Exemplen nedan visar analysdefinitioner för några viktiga scenarier.

+ [Exempel på anpassad analysator](#Custom-analyzer-example)
+ [Tilldela analysatorer till ett fältexempel](#Per-field-analyzer-assignment-example)
+ [Blanda analysatorer för indexering och sökning](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Exempel på språkanalysator](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Exempel på anpassad analysator

Det här exemplet illustrerar en analysatordefinition med anpassade alternativ. Anpassade alternativ för teckenfilter, tokenizers och tokenfilter anges separat som namngivna konstruktioner och refereras sedan i analysatordefinitionen. Fördefinierade element används som-är och refereras helt enkelt med namn.

Gå igenom detta exempel:

* Analysatorer tillhör fältklassen för ett sökbart fält.
* En anpassad analysator är en del av en indexdefinition. Det kan vara lätt anpassat (till exempel anpassa ett enda alternativ i ett filter) eller anpassas på flera ställen.
* I det här fallet är den anpassade analysatorn "my_analyzer", som i sin tur använder en anpassad standard tokenizer "my_standard_tokenizer" och två tokenfilter: gemener och anpassade asciifolding filter "my_asciifolding".
* Den definierar också 2 anpassade teckenfilter "map_dash" och "remove_whitespace". Den första ersätter alla streck med understreck medan den andra tar bort alla blanksteg. Utrymmen måste VARA UTF-8 kodade i mappningsreglerna. Teckenfiltren används före tokenisering och påverkar de resulterande tokens (standard tokenizer raster på streck och utrymmen men inte på understreck).

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

### <a name="per-field-analyzer-assignment-example"></a>Exempel på tilldelning per fältanalysator

Standardanalysatorn är standard. Anta att du vill ersätta standard med en annan fördefinierad analysator, till exempel mönsteranalysatorn. Om du inte anger anpassade alternativ behöver du bara ange det efter namn i fältdefinitionen.

Elementet "analysator" åsidosätter standardanalysatorn fält för fält. Det finns ingen global åsidosättning. I det `text1` här exemplet använder `text2`mönsteranalysatorn och använder standardinställningen som inte anger en analysator.

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

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Blandning av analysatorer för indexering och sökoperationer

API:erna innehåller ytterligare indexattribut för att ange olika analysatorer för indexering och sökning. **Attributen searchAnalyzer** och **indexAnalyzer** måste anges som ett par som ersätter attributet single **analyzer.**


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

### <a name="language-analyzer-example"></a>Exempel på språkanalysator

Fält som innehåller strängar på olika språk kan använda en språkanalysator, medan andra fält behåller standardinställningen (eller använder någon annan fördefinierad eller anpassad analysator). Om du använder en språkanalysator måste den användas för både indexerings- och sökåtgärder. Fält som använder en språkanalysator kan inte ha olika analysatorer för indexering och sökning.

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

## <a name="c-examples"></a>C# exempel

Om du använder exempel på .NET SDK-kod kan du lägga till dessa exempel för att använda eller konfigurera analysatorer.

+ [Tilldela en inbyggd analysator](#Assign-a-language-analyzer)
+ [Konfigurera en analysator](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Tilldela en språkanalysator

Alla analysatorer som används som de är, utan konfiguration, anges i en fältdefinition. Det finns inget krav på att skapa en analysatorkonstruktion. 

I det här exemplet tilldelas Microsoft English- och French-analysatorer beskrivningsfält. Det är ett utdrag taget från en större definition av hotellindex, skapa med hjälp av Hotel klassen i hotels.cs filen i [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) provet.

Anropa [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet), ange [typ AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) som ger en textanalysator som stöds i Azure Cognitive Search.

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

### <a name="define-a-custom-analyzer"></a>Definiera en anpassad analysator

När anpassning eller konfiguration krävs måste du lägga till en analysatorkonstruktion i ett index. När du har definierat det kan du lägga till den fältdefinitionen som visas i föregående exempel.

Skapa ett [CustomAnalyzer-objekt.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) Fler exempel finns [i CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs).

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

+ Läs vår omfattande förklaring av [hur fulltextsökning fungerar i Azure Cognitive Search](search-lucene-query-architecture.md). Den här artikeln använder exempel för att förklara beteenden som kan verka kontraintuitiva på ytan.

+ Prova ytterligare frågesyntax från exempelavsnittet [Sök dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) eller från [Enkel frågesyntax](query-simple-syntax.md) i Sökutforskaren i portalen.

+ Läs om hur du använder [språkspecifika lexikala analysatorer](index-add-language-analyzers.md).

+ [Konfigurera anpassade analysatorer](index-add-custom-analyzers.md) för antingen minimal bearbetning eller specialiserad bearbetning på enskilda fält.

## <a name="see-also"></a>Se även

 [REST API för dokumentsökning](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Enkel frågesyntax](query-simple-syntax.md) 

 [Fullständig Lucene-frågesyntax](query-lucene-syntax.md) 
 
 [Hantera sökresultat](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
