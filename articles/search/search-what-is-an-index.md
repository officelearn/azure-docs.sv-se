---
title: Skapa en Indexdefinition och begrepp - Azure Search
description: Introduktion till indexet termer och begrepp i Azure Search, inklusive komponenter och fysiska struktur.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/13/2019
ms.custom: seodec2018
ms.openlocfilehash: 1a07661d97561319b847323981dd2aa8522b0e84
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537600"
---
# <a name="create-a-basic-index-in-azure-search"></a>Skapa ett grundläggande index i Azure Search

I Azure Search, en *index* är ett beständigt Arkiv med *dokument* och andra konstruktioner som används för filtrerad och Fullständig textsökning på en Azure Search-tjänst. Den övergripande är ett dokument en enhet med sökbara data i ditt index. En återförsäljare som sysslar med e-handel kan till exempel ha ett dokument för varje artikel som företaget säljer, och en nyhetsorganisation kan ha ett dokument för varje nyhetsartikel osv. Om vi matchar dessa koncept till vanliga motsvarigheter i databasvärlden så kan ett *index* begreppsmässigt liknas vid en *tabell* och *dokument* kan grovt jämföras med *rader* i en tabell.

När du lägger till eller ladda upp ett index skapas Azure Search fysiska strukturer som baseras på schemat som du anger. Om ett fält i indexet har markerats som sökbara, till exempel skapas ett vägar i inverterad index för det fältet. Senare, när du lägger till eller ladda upp dokument eller skickar sökfrågor till Azure Search skickar du begäranden till ett specifikt index i din söktjänst. Läser in fälten med dokumentvärden kallas *indexering* eller dataintag.

Du kan skapa ett index i portalen [REST API](search-create-index-rest-api.md), eller [.NET SDK](search-create-index-dotnet.md).

## <a name="recommended-workflow"></a>Rekommenderat arbetsflöde

Rätt index-design som kommer uppnås vanligtvis via flera iterationer. Använda en kombination av verktyg och API: er kan hjälpa dig att slutföra utformningen snabbt.

1. Avgöra om du kan använda en [indexeraren](search-indexer-overview.md#supported-data-sources). Om dina externa data är en av datakällorna som stöds, kan du skapa prototyper och läsa in ett index med hjälp av den [ **dataimport** ](search-import-data-portal.md) guiden.

2. Om du inte kan använda **dataimport**, du kan fortfarande [skapa ett första index i portalen](search-create-index-portal.md), lägga till fält, datatyper, och tilldela attribut med hjälp av kontrollerna på den **Add Index** sidan. Portalen visar vilka attribut som är tillgängliga för olika datatyper. Det här är användbart om du inte har använt design för indexet.

   ![Lägg till indexsidan som visar attribut med datatypen](media/search-create-index-portal/field-attributes.png "Lägg till indexsidan som visar attributen efter datatyp")
  
   När du klickar på **skapa**, alla fysiska strukturer som stöder ditt index skapas i din söktjänst.

3. Hämta den index schema med hjälp av [hämta Index REST API](https://docs.microsoft.com/rest/api/searchservice/get-index) och en webbplats testning verktyg som [Postman](search-fiddler.md). Nu har du en JSON-representation av det index som du skapade i portalen. 

   Du byter till en kodbaserad metod i det här läget. Portalen är inte passar bra för iteration eftersom du inte kan redigera ett index som redan har skapats. Men du kan använda Postman och RESTEN för de återstående aktiviteterna.

4. [Läsa in ditt index med data](search-what-is-data-import.md). Azure Search accepterar JSON-dokument. Du kan använda Postman med JSON-dokument i nyttolasten för begäran för att läsa in dina data programmässigt. Om dina data inte är enkelt uttryckt i JSON, blir det här steget mest arbete beräkningsintensiva.

5. Fråga ditt index, granska resultaten och ytterligare itererar indexschemat tills du börjar du se oväntade resultat. Du kan använda [ **Sökutforskaren** ](search-explorer.md) eller Postman fråga ditt index.

6. Fortsätt att använda kod till att iterera över din design.  

Eftersom fysiska strukturer skapas i tjänsten [släppa och återskapa index](search-howto-reindex.md) är nödvändigt när du gör betydande ändringar av ab befintliga definitionen för fältet. Det innebär att under utvecklingen, bör du planera på ofta behöver. Kan du arbeta med en delmängd av dina data så återskapar gå snabbare. 

Koden i stället för en portal metod som rekommenderas för upprepad konstruktion. Om du förlitar dig på portalen för indexdefinitionen, måste du fylla i indexdefinitionen på varje återskapning. Alternativt kan verktyg som [Postman och REST API](search-fiddler.md) är användbara för proof-of-concept testning när utvecklingsprojekt är fortfarande under tidiga faser. Du kan göra inkrementella ändringar i en Indexdefinition i en begärandetext och skicka begäran till din tjänst att återskapa ett index med en uppdaterade schemat.

## <a name="components-of-an-index"></a>Komponenter i ett index

Grafiskt, består ett Azure Search-index av följande element. 

Den [ *fält samling* ](#fields-collection) är vanligtvis den största delen av ett index, där varje fält med namnet, har angett och ha tillåten beteenden som avgör hur den används. Andra element är [förslagsställare](#suggesters), [poängprofiler](#scoring-profiles), [analysverktyg](#analyzers) med komponenter för anpassning, och [CORS](#cors) alternativ.

```json
{
  "name": (optional on PUT; required on POST) "name_of_index",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [
    {
      "name": "name of suggester",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["field1", "field2", ...]
    }
  ],
  "scoringProfiles": [
    {
      "name": "name of scoring profile",
      "text": (optional, only applies to searchable fields) {
        "weights": {
          "searchable_field_name": relative_weight_value (positive #'s),
          ...
        }
      },
      "functions": (optional) [
        {
          "type": "magnitude | freshness | distance | tag",
          "boost": # (positive number used as multiplier for raw score != 1),
          "fieldName": "...",
          "interpolation": "constant | linear (default) | quadratic | logarithmic",
          "magnitude": {
            "boostingRangeStart": #,
            "boostingRangeEnd": #,
            "constantBoostBeyondRange": true | false (default)
          },
          "freshness": {
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
          },
          "distance": {
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
          },
          "tag": {
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)
          }
        }
      ],
      "functionAggregation": (optional, applies only when functions are specified) 
        "sum (default) | average | minimum | maximum | firstMatching"
    }
  ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) {
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>Fält samling och fältet attribut

När du definierar ett schema måste du ange namnet, typen och attributet för varje fält i ditt index. Fälttypen klassificerar de data som lagras i fältet. Attribut anges för enskilda fält och definierar hur fältet används. Följande tabeller innehåller de typer och attribut som du kan ange.

### <a name="data-types"></a>Datatyper
| Type | Beskrivning |
| --- | --- |
| *Edm.String* |Text som kan tokeniseras för textsökning (radbrytning, ordstamsigenkänning och så vidare). |
| *Collection(Edm.String)* |En lista med strängar som kan tokeniseras för textsökning. Det finns ingen teoretisk övre gräns för antalet objekt i en samling, men den övre gränsen på 16 MB för nyttolasten gäller för samlingar. |
| *Edm.Boolean* |Innehåller sant/falskt-värden. |
| *Edm.Int32* |32-bitars heltalsvärden. |
| *Edm.Int64* |64-bitars heltalsvärden. |
| *Edm.Double* |Numeriska data med dubbel precision. |
| *Edm.DateTimeOffset* |Datum tidsvärden som representeras i OData V4-format (till exempel `yyyy-MM-ddTHH:mm:ss.fffZ` eller `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |En punkt som representerar en geografisk plats i världen. |

Mer detaljerad information om [vilka datatyper som stöds i Azure Search finns här](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types).

### <a name="index-attributes"></a>Indexattribut
| Attribut | Beskrivning |
| --- | --- |
| *Nyckel* |En sträng som innehåller det unika ID:t för varje dokument och som används för att leta upp dokument. Alla index måste ha en nyckel. Endast ett fält kan vara nyckeln och dess typ måste anges till Edm.String. |
| *Hämtningsbar* |Anger om ett fält kan returneras i sökresultat. |
| *Filtrerbar* |Gör att fältet kan användas i filterfrågor. |
| *Sorterbar* |Gör att en fråga kan sortera sökresultat med hjälp av det här fältet. |
| *Fasettbar* |Gör att ett fält kan användas i en struktur för [aspektbaserad navigering](search-faceted-navigation.md) för filtrering av användaren. Oftast fungerar fält med upprepade värden som kan användas för att gruppera flera dokument (till exempel flera dokument som hör till samma varumärkes- eller tjänstkategori) bäst som aspekter. |
| *Sökbar* |Markerar fältet som fulltextsökbart. |

Mer detaljerad information om [indexattributen i Azure Search finns här](https://docs.microsoft.com/rest/api/searchservice/Create-Index).

## <a name="storage-implications"></a>Storage effekter

De attribut som du väljer påverka lagring. Följande skärmbild illustrerar index storage mönster som härrör från olika kombinationer av attribut.

Indexet baseras på den [inbyggda realestate-exemplet](search-get-started-portal.md) datakällan, vilket kan du indexera och fråga i portalen. Även om de index scheman inte visas, kan du hämta de attribut som baseras på Indexnamnet. Till exempel *realestate-sökbara* indexet innehåller den **sökbara** attributet som väljs och inget annat, *realestate-hämtningsbara* indexet innehåller den  **hämtningsbar** attributet som väljs och inget annat och så vidare.

![Index-storlek baserat på val av attributet](./media/search-what-is-an-index/realestate-index-size.png "Index-storlek baserat på val av attribut")

Även om dessa index varianter är artificiella, refererar vi till dem för bred jämförelser av hur attribut påverkar lagring. Gör inställningen **hämtningsbar** öka indexstorleken på? Nej. Gör att lägga till fält till en **förslagsställare** öka indexstorleken på? Ja.

Index som har stöd för filtrering och sortering är proportionellt större än index som stöder bara fulltextsökning. Anledningen är att filtrera och sortera fråga på exakta matchningar så att dokument som lagras intakta. Däremot Sök sökbara fält som har stöd för fulltext- och fuzzy Använd inverterad index, som fylls i med principfilerna villkor som förbrukar mindre utrymme än hela dokument.

> [!Note]
> Lagringsarkitektur anses en implementeringsdetalj för Azure Search och kan ändras utan föregående meddelande. Det finns ingen garanti att aktuella beteendet behålls i framtiden.

## <a name="suggesters"></a>Förslag på alternativ
En förslagsställare är en del av det schema som definierar vilka fält i ett index används för att stödja Komplettera automatiskt eller frågeifyllningsförslag frågor i sökningar. Normalt partiella söksträngar skickas till den [förslag (REST API)](https://docs.microsoft.com/rest/api/searchservice/suggestions) när användaren skriver en sökfråga och API: et returnerar en mängd med föreslagna fraserna. 

Fält som har lagts till i en förslagsställare används för att skapa frågeifyllningsförslag söktermer. Alla sökvillkor skapas under indexering och lagrat separat. Läs mer om hur du skapar en förslagsställare struktur, [lägga till förslagsställare](index-add-suggesters.md).

## <a name="scoring-profiles"></a>Poängprofiler

En [bedömningsprofil](index-add-scoring-profiles.md) är en del av det schema som definierar anpassad bedömning beteenden som låter du påverka vilka objekt som visas högre upp i sökresultaten. Bedömningsprofiler består av fältvikter och funktioner. För att använda dem. anger du en profil med namnet i frågesträngen.

Standard bedömningsprofil körs i bakgrunden att beräkna en sökpoäng för alla objekt i en resultatuppsättning. Du kan använda den interna icke namngivna bedömningsprofil. Du kan också ange **defaultScoringProfile** att använda en anpassad profil som standard, som anropas när en anpassad profil har angetts i frågesträngen.

## <a name="analyzers"></a>Analysverktyg

Analysverktyg elementet anger namnet på språkanalysverktyg för fältet. Läs mer om vilka analysverktyg som är tillgängliga för dig, [att lägga till analysverktyg i ett Azure Search-index](search-analyzers.md). Analysverktyg kan bara användas med sökbara fält. När analysatorn tilldelas till ett fält, kan den inte ändras, såvida inte du återskapa indexet.

## <a name="cors"></a>CORS

JavaScript för klientsidan kan inte anropa alla API: er som standard eftersom webbläsaren förhindrar alla cross-origin-begäranden. Aktivera CORS (Cross-Origin Resource Sharing) om du vill tillåta resursdelning för korsande ursprung frågor till ditt index genom att ange den **corsOptions** attribut. Av säkerhetsskäl endast fråga API: er stöd för CORS. 

Följande alternativ kan anges för CORS:

+ **allowedOrigins** (krävs): Det här är en lista över ursprung som beviljas åtkomst till ditt index. Det innebär att all JavaScript-kod som hämtats från dessa ursprung tillåts fråga ditt index (förutsatt att den ger rätt api-nyckel). Varje ursprung har vanligtvis formatet `protocol://<fully-qualified-domain-name>:<port>` även om `<port>` utelämnas ofta. Se [Cross-origin resource sharing (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) för mer information.

  Om du vill tillåta åtkomst till alla ursprung inkludera `*` som ett enda objekt i den **allowedOrigins** matris. *Detta rekommenderas inte för produktion söktjänster* men det kan ofta vara användbart för utveckling och felsökning.

+ **maxAgeInSeconds** (valfritt): Webbläsare använder värdet för att fastställa varaktigheten (i sekunder) för cache CORS preflight-svar. Detta måste vara ett icke-negativt heltal. Ju större det här värdet är, bättre prestanda, men ju längre tid det tar för CORS principändringar ska börja gälla. Om det inte har angetts används standardlängd på 5 minuter.

## <a name="next-steps"></a>Nästa steg

Du kan fortsätta i portalen för att skapa ditt första index med en förståelse för indexsammansättningen.

> [!div class="nextstepaction"]
> [Lägg till ett index (portal)](search-create-index-portal.md)
