---
title: Skapa en index definition och-koncept – Azure Search
description: Introduktion till index termer och koncept i Azure Search, inklusive komponent delar och fysisk struktur.
author: HeidiSteen
manager: nitinme
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: seodec2018
ms.openlocfilehash: 0a26cfc578f12044cb5834f202a0fed5d0a30274
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "69647374"
---
# <a name="create-a-basic-index-in-azure-search"></a>Skapa ett grundläggande index i Azure Search

I Azure Search är ett *index* ett beständigt arkiv med *dokument* och andra konstruktioner som används för filtrering och fullständig texts ökning i en Azure Search tjänst. Ett dokument är konceptuellt en enskild enhet med sökbara data i ditt index. En återförsäljare som sysslar med e-handel kan till exempel ha ett dokument för varje artikel som företaget säljer, och en nyhetsorganisation kan ha ett dokument för varje nyhetsartikel osv. Om vi matchar dessa koncept till vanliga motsvarigheter i databasvärlden så kan ett *index* begreppsmässigt liknas vid en *tabell* och *dokument* kan grovt jämföras med *rader* i en tabell.

När du lägger till eller laddar upp ett index skapar Azure Search fysiska strukturer baserat på det schema som du anger. Om ett fält i indexet exempelvis är markerat som sökbar, skapas ett inverterat index för det fältet. När du senare lägger till eller laddar upp dokument eller skickar Sök frågor till Azure Search skickar du begär anden till ett särskilt index i Sök tjänsten. Inläsning av fält med dokument värden kallas *indexering* eller data inmatning.

Du kan skapa ett index i portalen, [REST API](search-create-index-rest-api.md)eller [.NET SDK](search-create-index-dotnet.md).

## <a name="recommended-workflow"></a>Rekommenderat arbets flöde

Att komma till höger index design uppnås normalt genom flera iterationer. Genom att använda en kombination av verktyg och API: er kan du snabbt slutföra din design.

1. Avgör om du kan använda en [indexerare](search-indexer-overview.md#supported-data-sources). Om dina externa data är en av de data källor som stöds kan du prototypa och läsa in ett index med hjälp av guiden [**Importera data**](search-import-data-portal.md) .

2. Om du inte kan använda **Importera data**kan du fortfarande [skapa ett första index i portalen](search-create-index-portal.md), lägga till fält, data typer och tilldela attribut med hjälp av kontroller på sidan **Lägg till index** . Portalen visar vilka attribut som är tillgängliga för olika data typer. Detta är användbart om du inte har använt index design tidigare.

   ![Sidan Lägg till index som visar attribut efter datatyp](media/search-create-index-portal/field-attributes.png "Sidan Lägg till index som visar attribut efter datatyp")
  
   När du klickar på **skapa**skapas alla fysiska strukturer som stöder ditt index i din Sök tjänst.

3. Hämta index schema med hjälp av [Get index REST API](https://docs.microsoft.com/rest/api/searchservice/get-index) och ett webb test verktyg som [Postman](search-get-started-postman.md). Nu har du en JSON-representation av indexet som du skapade i portalen. 

   Du växlar till en kod baserad metod i det här läget. Portalen passar inte bra för iteration eftersom du inte kan redigera ett index som redan har skapats. Men du kan använda Postman och REST för återstående uppgifter.

4. [Läs in ditt index med data](search-what-is-data-import.md). Azure Search accepterar JSON-dokument. Om du vill läsa in dina data program mässigt kan du använda Postman med JSON-dokument i nytto lasten för begäran. Om dina data inte är enkla att uttryckas som JSON, är det här steget det mest arbets krävande.

5. Fråga ditt index, granska resultaten och upprepa detta genom att upprepa index schemat tills du börjar se de resultat du förväntar dig. Du kan använda [**Sök Utforskaren**](search-explorer.md) eller Postman för att fråga ditt index.

6. Fortsätt att använda kod för att iterera över din design.  

Eftersom fysiska strukturer skapas i tjänsten är det nödvändigt att [släppa och återskapa index](search-howto-reindex.md) när du gör väsentliga ändringar i en befintlig fält definition. Det innebär att du under utvecklingen bör planera om frekventa återuppbyggnadar. Du kan överväga att arbeta med en delmängd av dina data för att göra återskapandet snabbare. 

Kod i stället för en portal metod rekommenderas för iterativ design. Om du förlitar dig på portalen för index definition måste du fylla i index definitionen för varje återskapning. Som ett alternativ är verktyg som [Postman och REST API](search-get-started-postman.md) användbara för koncept bevis testning när utvecklings projekt fortfarande är i tidiga faser. Du kan göra stegvisa ändringar i en index definition i en begär ande text och sedan skicka begäran till tjänsten för att återskapa ett index med ett uppdaterat schema.

## <a name="components-of-an-index"></a>Komponenter i ett index

Schematiskt är ett Azure Search index består av följande element. 

[*Fält samlingen*](#fields-collection) är vanligt vis den största delen av ett index, där varje fält namnges, skrivs och attributas med tillåtna beteenden som avgör hur det används. Andra element är [förslags ställare](#suggesters), [bedömnings profiler](#scoring-profiles), [analyser](#analyzers) med komponent delar som stöder anpassning, [CORS](#cors) och [krypterings nyckel](#encryption-key) alternativ.

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
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>Fält samling och fält-attribut

När du definierar ett schema måste du ange namnet, typen och attributet för varje fält i ditt index. Fälttypen klassificerar de data som lagras i fältet. Attribut anges för enskilda fält och definierar hur fältet används. Följande tabeller innehåller de typer och attribut som du kan ange.

### <a name="data-types"></a>Datatyper
| Typ | Beskrivning |
| --- | --- |
| *Edm.String* |Text som kan, om du vill, använda en token för full texts ökning (ord brytning, ord och så vidare). |
| *Collection(Edm.String)* |En lista med strängar som kan tokeniseras för textsökning. Det finns ingen teoretisk övre gräns för antalet objekt i en samling, men den övre gränsen på 16 MB för nyttolasten gäller för samlingar. |
| *Edm.Boolean* |Innehåller sant/falskt-värden. |
| *Edm.Int32* |32-bitars heltalsvärden. |
| *Edm.Int64* |64-bitars heltalsvärden. |
| *Edm.Double* |Numeriska data med dubbel precision. |
| *Edm.DateTimeOffset* |Datum/tid-värden som representeras i OData v4-formatet (till exempel `yyyy-MM-ddTHH:mm:ss.fffZ` eller `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |En punkt som representerar en geografisk plats i världen. |

Mer detaljerad information om [vilka datatyper som stöds i Azure Search finns här](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types).

### <a name="index-attributes"></a>Indexera attribut

Exakt ett fält i indexet måste vara det angivna som ett **nyckel** fält som unikt identifierar varje dokument.

Andra attribut avgör hur ett fält används i ett program. Till exempel tilldelas attributet **sökbar** till alla fält som ska ingå i en full texts ökning. 

API: er som du använder för att bygga ett index har varierande standard beteenden. För [REST-API: erna](https://docs.microsoft.com/rest/api/searchservice/Create-Index)är de flesta attribut aktiverade som standard (till exempel **sökbar** och **hämtnings** bar är sanna för sträng fält) och du behöver ofta bara ange dem om du vill inaktivera dem. För .NET SDK är motsatsen sant. På alla egenskaper som du inte uttryckligen anger är standardvärdet att inaktivera motsvarande Sök beteende om du inte uttryckligen aktiverar det.

| Attribut | Beskrivning |
| --- | --- |
| `key` |En sträng som innehåller det unika ID:t för varje dokument och som används för att leta upp dokument. Alla index måste ha en nyckel. Endast ett fält kan vara nyckeln och dess typ måste anges till Edm.String. |
| `retrievable` |Anger om ett fält kan returneras i sökresultat. |
| `filterable` |Gör att fältet kan användas i filterfrågor. |
| `Sortable` |Gör att en fråga kan sortera sökresultat med hjälp av det här fältet. |
| `facetable` |Gör att ett fält kan användas i en struktur för [aspektbaserad navigering](search-faceted-navigation.md) för filtrering av användaren. Oftast fungerar fält med upprepade värden som kan användas för att gruppera flera dokument (till exempel flera dokument som hör till samma varumärkes- eller tjänstkategori) bäst som aspekter. |
| `searchable` |Markerar fältet som fulltextsökbart. |


## <a name="storage-implications"></a>Lagrings konsekvenser

De attribut du väljer påverkar lagringen. Följande skärm bild illustrerar index lagrings mönster som orsakas av olika kombinationer av attribut.

Indexet baseras på den inbyggda exempel data källan för [fastighets fastighets](search-get-started-portal.md) , som du kan indexera och fråga i portalen. Även om index scheman inte visas kan du härleda attributen baserat på index namnet. Till exempel, *realestate-sökbart* index har det **sökbara** attributet markerat och inget annat, *realestate-hämtnings* Bart index har det **hämtnings** bara attributet markerat och inget annat, och så vidare.

![Index storlek baserat på val av attribut](./media/search-what-is-an-index/realestate-index-size.png "Index storlek baserat på val av attribut")

Även om de här varianterna av index är artificiella kan vi referera till dem för att se hur attribut påverkar lagringen. Ställer in den **hämtnings** bara index storleken? Nej. Lägger du till fält till en **förslags** öknings index storlek? Ja.

Index som stöder filtrering och sortering är proportionerligt större än index som stöder bara fullständig texts ökning. Anledningen är att filtrera och sortera frågor om exakta matchningar, så att dokument lagras intakt. Sökbara fält som stöder full text och fuzzy search använder inverterade index, som är ifyllda med token-termer som förbrukar mindre utrymme än hela dokument.

> [!Note]
> Lagrings arkitektur betraktas som en implementerings detalj för Azure Search och kan ändras utan föregående meddelande. Det finns ingen garanti för att det aktuella beteendet är kvar i framtiden.

## <a name="suggesters"></a>Förslag på alternativ
En förslags ställare är en del av schemat som definierar vilka fält i ett index som används för att stödja automatisk fullständig eller typ av frågor i sökningar. Normalt skickas partiella Sök strängar till [förslagen (REST API)](https://docs.microsoft.com/rest/api/searchservice/suggestions) medan användaren skriver en Sök fråga och API: et returnerar en uppsättning föreslagna fraser. 

Fält som läggs till i en förslags lista används för att generera typ kommande Sök villkor. Alla Sök termer skapas vid indexering och lagras separat. Mer information om hur du skapar en förslags struktur finns i [lägga till förslag](index-add-suggesters.md).

## <a name="scoring-profiles"></a>Poängprofiler

En [bedömnings profil](index-add-scoring-profiles.md) är en del av schemat som definierar anpassade bedömnings beteenden som låter dig påverka vilka objekt som visas högre upp i Sök resultaten. Bedömnings profiler består av fält vikter och-funktioner. Om du vill använda dem anger du en profil efter namn i frågesträngen.

En standard bedömnings profil fungerar i bakgrunden för att beräkna en Sök Poäng för varje objekt i en resultat uppsättning. Du kan använda den interna, namnlösa bedömnings profilen. Du kan också ange att **defaultScoringProfile** ska använda en anpassad profil som standard, anropas när en anpassad profil inte anges i frågesträngen.

## <a name="analyzers"></a>Analysverktyg

I elementet analyser anges namnet på den språk analys som ska användas för fältet. Mer information om vilka analys intervall som är tillgängliga finns i [lägga till analyser i ett Azure Search-index](search-analyzers.md). Analys verktyg kan bara användas med sökbara fält. När analysen har tilldelats ett fält kan det inte ändras om du inte bygger om indexet.

## <a name="cors"></a>CORS

Java Script på klient sidan kan inte anropa några API: er som standard eftersom webbläsaren förhindrar alla cross-origin-begäranden. Om du vill tillåta frågor om kors ursprung till ditt index aktiverar du CORS (resurs delning mellan ursprung) genom att ange attributet **corsOptions** . Av säkerhets skäl stöder endast API: er för frågor. 

Följande alternativ kan ställas in för CORS:

+ **allowedOrigins** (obligatoriskt): det här är en lista över ursprung som ska beviljas åtkomst till ditt index. Det innebär att alla JavaScript-koder som hanteras från dessa ursprung kommer att kunna fråga ditt index (förutsatt att det innehåller rätt API-nyckel). Varje ursprung är vanligt vis formuläret `protocol://<fully-qualified-domain-name>:<port>`, även om `<port>` har utelämnats ofta. Se [resurs delning mellan ursprung (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) om du vill ha mer information.

  Om du vill tillåta åtkomst till alla ursprung inkluderar du `*` som ett enskilt objekt i **allowedOrigins** -matrisen. *Detta är inte en rekommenderad metod för produktions Sök tjänster* , men det är ofta användbart för utveckling och fel sökning.

+ **maxAgeInSeconds** (valfritt): webbläsare använder det här värdet för att fastställa varaktigheten (i sekunder) för att cachelagra CORS preflight-svar. Detta måste vara ett icke-negativt heltal. Ju större det här värdet är, desto bättre prestanda blir, men det längre tar för CORS-principens ändringar att börja gälla. Om den inte anges används en standard varaktighet på 5 minuter.

## <a name="encryption-key"></a>Krypterings nyckel

Alla Azure Search-index krypteras som standard med hjälp av Microsoft-hanterade nycklar, och index kan konfigureras för att krypteras med **Kundhanterade nycklar** i Key Vault. Mer information finns i [Hantera krypterings nycklar i Azure Search](search-security-manage-encryption-keys.md).

## <a name="next-steps"></a>Nästa steg

Med förståelse för index komposition kan du fortsätta i portalen för att skapa ditt första index.

> [!div class="nextstepaction"]
> [Lägg till ett index (portal)](search-create-index-portal.md)
