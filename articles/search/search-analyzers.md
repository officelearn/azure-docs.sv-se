---
title: Analys verktyg för språk-och text bearbetning
titleSuffix: Azure Cognitive Search
description: Tilldela analyserare till sökbara textfält i ett index om du vill ersätta standard standard-Lucene med anpassade, fördefinierade eller språkspecifika alternativ.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: 2e4a6ab8825982969ffa4654c2418f7a9d168d2e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460710"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Analys verktyg för text bearbetning i Azure Kognitiv sökning

En *Analyzer* är en komponent i den [fullständiga texts öknings motorn](search-lucene-query-architecture.md) som ansvarar för bearbetning av text i frågesträngar och indexerade dokument. Olika analys verktyg ändrar text på olika sätt beroende på scenariot. Språk analys verktyg bearbetar text med språkliga regler för att förbättra Sök kvaliteten, medan andra analyserare utför mer grundläggande uppgifter som att konvertera tecken till gemener, till exempel. 

Språk analys verktyg är de vanligaste och det finns standard språk analys som tilldelats alla sökbara fält i ett Azure Kognitiv sökning-index. Följande språk omvandlingar är typiska under text analys:

+ Icke-grundläggande ord (stoppord) och interpunktion tas bort.
+ Fraser och avstavade ord delas upp i komponent delar.
+ Versaler är lägre – bokstäver.
+ Orden reduceras till rot formulär så att en matchning kan hittas oavsett om det finns flera på flera nivåer.

Språk analys verktyg konverterar text indata till primitiva eller rot formulär som är effektiva för informations lagring och-hämtning. Konvertering sker under indexeringen, när indexet har skapats och sedan igen vid sökningen när indexet läses. Det är mer troligt att du får de Sök resultat du förväntar dig om du använder samma analys för båda åtgärderna.

## <a name="default-analyzer"></a>Standard analys  

Azure Kognitiv sökning använder [Apache Lucene standard Analyzer (standard-Lucene)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) som standard, vilket innebär att text i element följer reglerna för [Unicode-text segment](https://unicode.org/reports/tr29/) . Dessutom konverterar standard analys alla tecken till gemener. Både indexerade dokument och Sök termer går igenom analysen vid indexering och bearbetning av frågor.  

Den används automatiskt i alla sökbara fält. Du kan åsidosätta standardvärdet för fält-för-fält. Alternativa analys verktyg kan vara [språk analys](index-add-language-analyzers.md), [anpassad analys](index-add-custom-analyzers.md)eller en fördefinierad analys från [listan över tillgängliga analyser](index-add-custom-analyzers.md#AnalyzerTable).


## <a name="types-of-analyzers"></a>Typer av analys verktyg

I följande lista beskrivs vilka analys verktyg som är tillgängliga i Azure Kognitiv sökning.

| Kategori | Beskrivning |
|----------|-------------|
| [Standard Lucene Analyzer](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Standard. Ingen specifikation eller konfiguration krävs. Den här generella analysen fungerar bra för de flesta språk och scenarier.|
| Fördefinierade analys verktyg | Erbjuds som en färdig produkt som är avsedd att användas i befintligt skick. <br/>Det finns två typer: specialiserade och språk. Vad gör dem "fördefinierade" är att du refererar till dem efter namn, utan konfiguration eller anpassning. <br/><br/>[Specialiserade oberoende-analyser (Language-)](index-add-custom-analyzers.md#AnalyzerTable) används när text inmatningar kräver specialiserad bearbetning eller minimal bearbetning. Icke-språkdefinierade analys verktyg omfattar **Asciifolding**, **nyckelord**, **mönster**, **enkel**, **stopp**, **blank steg**.<br/><br/>[Språk analys](index-add-language-analyzers.md) verktyg används när du behöver omfattande språk stöd för enskilda språk. Azure Kognitiv sökning stöder 35 Lucene-språkanalyser och 50 Microsoft Natural Language Processing-analyser. |
|[Anpassade analysverktyg](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Refererar till en användardefinierad konfiguration av en kombination av befintliga element, som består av ett tokenizer (obligatoriskt) och valfria filter (Char eller token).|

Några fördefinierade analyser, till exempel **mönster** eller **stopp**, stöder en begränsad uppsättning konfigurations alternativ. Om du vill ange de här alternativen skapar du en anpassad analys som består av den fördefinierade analysen och ett av de alternativa alternativen som dokumenteras i den [fördefinierade analys referensen](index-add-custom-analyzers.md#AnalyzerTable). Som med valfri anpassad konfiguration kan du ange en ny konfiguration med ett namn, till exempel *myPatternAnalyzer* , för att skilja den från Lucene Pattern Analyzer.

## <a name="how-to-specify-analyzers"></a>Ange analys verktyg

1. (endast för anpassade analys verktyg) Skapa ett namngivet **Analyzer** -avsnitt i index definitionen. Mer information finns i [skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index) och även [lägga till anpassade analys](index-add-custom-analyzers.md)verktyg.

2. På en [fält definition](https://docs.microsoft.com/rest/api/searchservice/create-index) i indexet ställer du in fältets **analys** egenskap till namnet på en mål analys (till exempel `"analyzer" = "keyword"`. Giltiga värden är namn på en fördefinierad analys, språk analys eller anpassad analys som också definieras i index schemat. Planera för att tilldela Analyzer i index definitions fasen innan indexet skapas i tjänsten.

3. Istället för en **analys** egenskap kan du ange olika analys verktyg för indexering och fråga med fält parametrarna **indexAnalyzer** och **searchAnalyzer** . Du använder olika analys verktyg för förberedelse av data och hämtning om någon av dessa aktiviteter kräver en särskild omvandling som inte behövs av den andra.

> [!NOTE]
> Det går inte att använda en annan [språk analys](index-add-language-analyzers.md) vid indexerings tiden än vid frågans tid för ett fält. Den funktionen är reserverad för [anpassade analys](index-add-custom-analyzers.md)verktyg. Av den anledningen, om du försöker ange **searchAnalyzer** -eller **indexAnalyzer** -egenskaperna till namnet på en språk analys, returnerar REST API ett fel svar. Du måste använda **Analyzer** -egenskapen i stället.

Det är inte tillåtet att tilldela **Analyzer** -eller **indexAnalyzer** till ett fält som redan har skapats fysiskt. Om något av detta är oklart kan du läsa följande tabell för en analys av vilka åtgärder som kräver återuppbyggnad och varför.
 
 | Scenario | Påverkan | Steg |
 |----------|--------|-------|
 | Lägg till ett nytt fält | små | Om fältet inte finns än i schemat finns det ingen fält ändring att göra eftersom fältet inte redan har en fysisk närvaro i ditt index. Du kan använda [uppdaterings index](https://docs.microsoft.com/rest/api/searchservice/update-index) för att lägga till ett nytt fält i ett befintligt index och [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) för att fylla det.|
 | Lägg till ett **analys** -eller **indexAnalyzer** i ett befintligt indexerat fält. | [återskapa](search-howto-reindex.md) | Det inverterade indexet för fältet måste återskapas från grunden och innehållet för dessa fält måste omindexeras. <br/> <br/>För index under aktiv utveckling tar du [bort](https://docs.microsoft.com/rest/api/searchservice/delete-index) och [skapar](https://docs.microsoft.com/rest/api/searchservice/create-index) indexet för att hämta den nya fält definitionen. <br/> <br/>För index i produktion kan du skjuta upp en ny version genom att skapa ett nytt fält för att tillhandahålla den ändrade definitionen och börja använda den i stället för den gamla. Använd [Uppdatera index](https://docs.microsoft.com/rest/api/searchservice/update-index) för att lägga till det nya fältet och [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) för att fylla det. Som en del av den planerade indexerings servicen kan du senare rensa indexet för att ta bort föråldrade fält. |

## <a name="when-to-add-analyzers"></a>När du ska lägga till analyser

Den bästa tiden för att lägga till och tilldela analyserare är under aktiv utveckling, när du släpper och återskapar index är rutinmässig.

Som en index definition solidifies kan du lägga till nya analys konstruktioner i ett index, men du måste skicka flaggan **allowIndexDowntime** för att [Uppdatera index](https://docs.microsoft.com/rest/api/searchservice/update-index) om du vill undvika det här felet:

*"Index uppdatering tillåts inte eftersom det skulle orsaka drift stopp. För att lägga till nya analys verktyg, tokenizers, token-filter eller Character-filter i ett befintligt index ställer du in frågeparametern "allowIndexDowntime" till "true" i begäran om index uppdatering. Observera att den här åtgärden kommer att spara indexet offline under minst några sekunder, vilket gör att dina indexerings-och fråge begär Anden Miss söker. Prestanda-och skrivnings tillgänglighet för indexet kan vara försämrade i flera minuter efter att indexet har uppdaterats, eller längre för mycket stora index. "*

Samma gäller när du tilldelar en analys till ett fält. En Analyzer är en integrerad del av fältets definition, så du kan bara lägga till det när fältet skapas. Om du vill lägga till analyser i befintliga fält måste du [släppa och återskapa](search-howto-reindex.md) indexet eller lägga till ett nytt fält med den analys du vill ha.

Som nämnts är ett undantag den **searchAnalyzer** varianten. Det går bara att ändra attributet **searchAnalyzer** i ett befintligt fält av tre sätt att ange analyserare (**Analyzer**, **indexAnalyzer**, **searchAnalyzer**).

## <a name="recommendations-for-working-with-analyzers"></a>Rekommendationer för att arbeta med analyser

Det här avsnittet innehåller råd om hur du arbetar med analyser.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>En analys för Läs-och skriv åtgärder om du inte har särskilda krav

Med Azure Kognitiv sökning kan du ange olika analys verktyg för indexering och sökning via ytterligare **indexAnalyzer** -och **searchAnalyzer** fält-parametrar. Om inget anges används analys uppsättningen med **Analyzer** -egenskapen för både indexering och sökning. Om `analyzer` har angetts används standard standard för Lucene Analyzer.

En allmän regel är att använda samma analys för både indexering och frågor, om inte särskilda krav anger något annat. Se till att testa noggrant. När text bearbetning skiljer sig vid sökning och indexerings tiden, kan du köra risken för matchning mellan sökord och indexerade villkor när konfigurationerna för sökning och indexerings analys inte är justerade.

### <a name="test-during-active-development"></a>Testa under aktiv utveckling

Att åsidosätta standard Analyzer kräver en index återuppbyggnad. Om möjligt bör du bestämma vilka analys verktyg som ska användas under en aktiv utveckling innan du rullar ett index i produktionen.

### <a name="inspect-tokenized-terms"></a>Inspektera token-villkor

Om en sökning inte kan returnera förväntade resultat, är det mest sannolika scenariet token avvikelser mellan term inmatningar i frågan och token-termer i indexet. Om tokens inte är desamma matchar inte materialisera. Om du vill kontrol lera tokenizer-utdata rekommenderar vi att du använder [analysera API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) som ett verktyg för undersökning. Svaret består av tokens, som genereras av en speciell analys.

<a name="examples"></a>

## <a name="rest-examples"></a>REST-exempel

I exemplen nedan visas analys definitioner för några viktiga scenarier.

+ [Exempel på anpassade Analyzer](#Custom-analyzer-example)
+ [Tilldela analyserare till ett fält exempel](#Per-field-analyzer-assignment-example)
+ [Mixa analyser för indexering och sökning](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Exempel på språk analys](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Exempel på anpassade Analyzer

Det här exemplet illustrerar en analys definition med anpassade alternativ. Anpassade alternativ för char filter, tokenizers och token-filter anges separat som namngivna konstruktioner och refereras sedan till i analys definitionen. Fördefinierade element används som de är och refereras bara till efter namn.

Genom det här exemplet:

* Analys verktyg är en egenskap för fält klassen för ett sökbart fält.
* En anpassad analys är en del av en index definition. Det kan vara lätt att anpassa (till exempel anpassa ett enda alternativ i ett filter) eller anpassas på flera platser.
* I det här fallet är den anpassade analysen "my_analyzer", som i sin tur använder en anpassad standard-tokenizer "my_standard_tokenizer" och två token-filter: gement och anpassat asciifolding filter "my_asciifolding".
* Den definierar också två anpassade tecken filter "map_dash" och "remove_whitespace". Den första ersätter alla streck med under streck medan den andra tar bort alla blank steg. Blank stegen måste vara UTF-8-kodade i mappnings reglerna. Char-filtren tillämpas före tokenisering och kommer att påverka de resulterande token (standard tokenizer pauser på streck och blank steg, men inte på under streck).

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

### <a name="per-field-analyzer-assignment-example"></a>Exempel på analys av analys per fält

Standard Analyzer är standard. Anta att du vill ersätta standardvärdet med en annan fördefinierad analys, till exempel Pattern Analyzer. Om du inte anger anpassade alternativ behöver du bara ange den efter namn i fält definitionen.

Elementet "Analyzer" åsidosätter standard analys baserat på fält-för-fält. Det finns ingen global åsidosättning. I det här exemplet använder `text1` Pattern Analyzer och `text2`, som inte anger någon analys, använder standardvärdet.

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

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Mixa analyser för indexerings-och Sök åtgärder

API: erna inkluderar ytterligare index-attribut för att ange olika analyser för indexering och sökning. Attributen **searchAnalyzer** och **indexAnalyzer** måste anges som ett par, vilket ersätter attributet Single **Analyzer** .


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

### <a name="language-analyzer-example"></a>Exempel på språk analys

Fält som innehåller strängar på olika språk kan använda en språk analys, medan andra fält behåller standardvärdet (eller använder någon annan fördefinierad eller anpassad analys). Om du använder en språk analys måste det användas för både indexerings-och Sök åtgärder. Fält som använder en språk analys kan inte ha olika analys verktyg för indexering och sökning.

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

## <a name="c-examples"></a>C#fler

Om du använder kod exempel för .NET SDK kan du lägga till dessa exempel för att använda eller konfigurera analyser.

+ [Tilldela en inbyggd analys](#Assign-a-language-analyzer)
+ [Konfigurera en analys](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Tilldela en språk analys

En analys som används i befintligt skick, utan konfiguration, anges i en fält definition. Det finns inget krav på att skapa en analys konstruktion. 

I det här exemplet tilldelas Microsoft English-och franska-analyser för att beskriva fält. Det är ett kodfragment som tas från en större definition av hotell indexet, vilket skapar med hjälp av hotell klassen i hotels.cs-filen i [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) -exemplet.

Anropa [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet), som anger [AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) -typ som ger stöd för text analys i Azure kognitiv sökning.

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

### <a name="define-a-custom-analyzer"></a>Definiera en anpassad analys

När anpassning eller konfiguration krävs måste du lägga till en analys-konstruktion i ett index. När du har definierat det kan du lägga till den som fält definition som visas i föregående exempel.

Skapa ett [CustomAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) -objekt. Fler exempel finns i [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs).

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

+ Läs vår omfattande förklaring av [hur full texts ökning fungerar i Azure kognitiv sökning](search-lucene-query-architecture.md). I den här artikeln används exempel för att förklara vilka beteenden som kan vara till hjälp för den aktuella ytan.

+ Försök med ytterligare frågesyntax från avsnittet [Sök efter dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) exempel eller från [enkel frågesyntax](query-simple-syntax.md) i Sök Utforskaren i portalen.

+ Lär dig hur du använder [språkspecifika lexikala analyser](index-add-language-analyzers.md).

+ [Konfigurera anpassade analys](index-add-custom-analyzers.md) verktyg för minimal bearbetning eller specialiserad bearbetning på enskilda fält.

## <a name="see-also"></a>Se också

 [REST API för dokumentsökning](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Enkel frågesyntax](query-simple-syntax.md) 

 [Fullständig Lucene-frågesyntax](query-lucene-syntax.md) 
 
 [Hantera sökresultat](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
