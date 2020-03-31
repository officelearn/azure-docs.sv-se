---
title: Skapa en indexdefinition och begrepp
titleSuffix: Azure Cognitive Search
description: Introduktion till indextermer och begrepp i Azure Cognitive Search, inklusive komponenter och fysisk struktur.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d2b8b2fecbf85e6590294f1fbd7ff2a4453b9e87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282788"
---
# <a name="create-a-basic-index-in-azure-cognitive-search"></a>Skapa ett grundläggande index i Azure Cognitive Search

I Azure Cognitive Search är ett *index* ett beständigt arkiv med *dokument* och andra konstruktioner som används för filtrerad och fulltextsökning på en Azure Cognitive Search-tjänst. Begreppsmässigt är ett dokument en enda enhet med sökbara data i indexet. En återförsäljare som sysslar med e-handel kan till exempel ha ett dokument för varje artikel som företaget säljer, och en nyhetsorganisation kan ha ett dokument för varje nyhetsartikel osv. Om vi matchar dessa koncept till vanliga motsvarigheter i databasvärlden så kan ett *index* begreppsmässigt liknas vid en *tabell* och *dokument* kan grovt jämföras med *rader* i en tabell.

När du lägger till eller laddar upp ett index skapar Azure Cognitive Search fysiska strukturer baserat på det schema du anger. Om ett fält i indexet till exempel är markerat som sökbart skapas ett inverterat index för det fältet. Senare, när du lägger till eller laddar upp dokument, eller skickar sökfrågor till Azure Cognitive Search, skickar du begäranden till ett visst index i söktjänsten. Inläsning av fält med dokumentvärden kallas *indexering* eller datainmatning.

Du kan skapa ett index i portalen, [REST API](search-create-index-rest-api.md)eller [.NET SDK](search-create-index-dotnet.md).

## <a name="recommended-workflow"></a>Rekommenderat arbetsflöde

Att komma fram till rätt indexdesign uppnås vanligtvis genom flera iterationer. Genom att använda en kombination av verktyg och API:er kan du snabbt slutföra designen.

1. Bestäm om du kan använda en [indexerare](search-indexer-overview.md#supported-data-sources). Om dina externa data är en av de datakällor som stöds kan du prototypera och läsa in ett index med hjälp av guiden [**Importera data.**](search-import-data-portal.md)

2. Om du inte kan använda **Importera data**kan du fortfarande skapa ett första index [i portalen,](search-create-index-portal.md)lägga till fält, datatyper och tilldela attribut med hjälp av kontroller på sidan **Lägg till index.** Portalen visar vilka attribut som är tillgängliga för olika datatyper. Om du inte har någon ny indexdesign kan det vara till hjälp.

   ![Lägga till indexsida som visar attribut efter datatyp](media/search-create-index-portal/field-attributes.png "Lägga till indexsida som visar attribut efter datatyp")
  
   När du klickar på **Skapa**skapas alla fysiska strukturer som stöder indexet i söktjänsten.

3. Ladda ner indexschemat med [Get Index REST API](https://docs.microsoft.com/rest/api/searchservice/get-index) och ett webbtestverktyg som [Postman](search-get-started-postman.md). Du har nu en JSON-representation av indexet som du skapade i portalen. 

   Du byter till en kodbaserad metod just nu. Portalen är inte väl lämpad för iteration eftersom du inte kan redigera ett index som redan har skapats. Men du kan använda Postman och REST för de återstående uppgifterna.

4. [Läs in indexet med data](search-what-is-data-import.md). Azure Cognitive Search accepterar JSON-dokument. Om du vill läsa in dina data programmässigt kan du använda Postman med JSON-dokument i nyttolasten för begäran. Om dina data inte är lätt att uttrycka som JSON, kommer detta steg att vara den mest arbetsintensiva.

5. Fråga ditt index, granska resultat och ytterligare iterera i indexschemat tills du börjar se de resultat du förväntar dig. Du kan använda [**Sökutforskaren**](search-explorer.md) eller Postman för att fråga ditt index.

6. Fortsätt att använda kod för att iterera över din design.  

Eftersom fysiska strukturer skapas i tjänsten är det nödvändigt att [släppa och återskapa index](search-howto-reindex.md) när du gör väsentliga ändringar i en befintlig fältdefinition. Detta innebär att du under utvecklingen bör planera på frekventa ombyggnader. Du kan överväga att arbeta med en delmängd av dina data för att göra ombyggnader gå snabbare. 

Kod, snarare än en portalmetod, rekommenderas för iterativ design. Om du förlitar dig på portalen för indexdefinition måste du fylla i indexdefinitionen för varje ombyggnad. Som ett alternativ är verktyg som [Postman och REST API](search-get-started-postman.md) till hjälp för proof-of-concept testning när utvecklingsprojekt fortfarande är i tidiga faser. Du kan göra inkrementella ändringar i en indexdefinition i en begärandetext och sedan skicka begäran till tjänsten om du vill återskapa ett index med hjälp av ett uppdaterat schema.

## <a name="components-of-an-index"></a>Komponenter i ett index

Schematiskt består ett Azure Cognitive Search-index av följande element. 

[*Fältsamlingen*](#fields-collection) är vanligtvis den största delen av ett index, där varje fält namnges, skrivs och tillskrivs med tillåtna beteenden som avgör hur det används. Andra element inkluderar [förslagsställare,](#suggesters) [bedömningsprofiler,](#scoring-profiles) [analysatorer](#analyzers) med komponenter för att stödja anpassning, [CORS](#cors) och [krypteringsnyckelalternativ.](#encryption-key)

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

## <a name="fields-collection-and-field-attributes"></a>Fältsamling och fältattribut

När du definierar ett schema måste du ange namnet, typen och attributet för varje fält i ditt index. Fälttypen klassificerar de data som lagras i fältet. Attribut anges för enskilda fält och definierar hur fältet används. Följande tabeller innehåller de typer och attribut som du kan ange.

### <a name="data-types"></a>Datatyper
| Typ | Beskrivning |
| --- | --- |
| *Edm.String* |Text som eventuellt kan tokeniseras för fulltextsökning (ordbrytande, avföljd och så vidare). |
| *Collection(Edm.String)* |En lista med strängar som kan tokeniseras för textsökning. Det finns ingen teoretisk övre gräns för antalet objekt i en samling, men den övre gränsen på 16 MB för nyttolasten gäller för samlingar. |
| *Edm.Boolean* |Innehåller sant/falskt-värden. |
| *Edm.Int32* |32-bitars heltalsvärden. |
| *Edm.Int64* |64-bitars heltalsvärden. |
| *Edm.Double* |Numeriska data med dubbel precision. |
| *Edm.DateTimeOffset* |Datumtidsvärden som representeras i OData V4-format (till exempel `yyyy-MM-ddTHH:mm:ss.fffZ` eller `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |En punkt som representerar en geografisk plats i världen. |

Du hittar mer detaljerad information om Azure Cognitive Searchs [datatyper som stöds här](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types).

### <a name="index-attributes"></a>Indexattribut

Exakt ett fält i indexet måste vara det angivna som ett **nyckelfält** som unikt identifierar varje dokument.

Andra attribut avgör hur ett fält används i ett program. Det **sökbara** attributet tilldelas till varje fält som ska inkluderas i en fulltextsökning. 

De API:er som du använder för att skapa ett index har olika standardbeteenden. För [REST-API:erna](https://docs.microsoft.com/rest/api/searchservice/Create-Index)är de flesta attribut aktiverade som standard (till exempel **är sökbara** och **hämtningsbara** som kan hämtas för strängfält) och du behöver ofta bara ange dem om du vill inaktivera dem. För .NET SDK är det tvärtom. På alla egenskaper som du inte uttryckligen anger är standardinställningen att inaktivera motsvarande sökbeteende om du inte specifikt aktiverar det.

| Attribut | Beskrivning |
| --- | --- |
| `key` |En sträng som innehåller det unika ID:t för varje dokument och som används för att leta upp dokument. Alla index måste ha en nyckel. Endast ett fält kan vara nyckeln och dess typ måste anges till Edm.String. |
| `retrievable` |Anger om ett fält kan returneras i sökresultat. |
| `filterable` |Gör att fältet kan användas i filterfrågor. |
| `Sortable` |Gör att en fråga kan sortera sökresultat med hjälp av det här fältet. |
| `facetable` |Gör att ett fält kan användas i en struktur för [aspektbaserad navigering](search-faceted-navigation.md) för filtrering av användaren. Oftast fungerar fält med upprepade värden som kan användas för att gruppera flera dokument (till exempel flera dokument som hör till samma varumärkes- eller tjänstkategori) bäst som aspekter. |
| `searchable` |Markerar fältet som fulltextsökbart. |

## <a name="index-size"></a>Indexstorlek

Storleken på ett index bestäms av storleken på de dokument du laddar upp, plus indexkonfiguration, till exempel om du inkluderar förslagsställare och hur du anger attribut för enskilda fält. Följande skärmbild illustrerar indexlagringsmönster till följd av olika kombinationer av attribut.

Indexet baseras på den inbyggda datakällan [för fastigheter,](search-get-started-portal.md) som du kan indexera och fråga i portalen. Även om indexschemana inte visas kan du härleda attributen baserat på indexnamnet. Till exempel har *realestate-sökbart* index **det sökbara** attributet valt och inget annat, *realestate-hämtningsbart* index har **hämtningsbart** attribut valt och inget annat och så vidare.

![Indexstorlek baserat på attributval](./media/search-what-is-an-index/realestate-index-size.png "Indexstorlek baserat på attributval")

Även om dessa indexvarianter är artificiella, kan vi hänvisa till dem för breda jämförelser av hur attribut påverkar lagring. Ökar inställningen **för hämtningsbara** indexstorlek? Nej. Ökar indexstorleken genom att lägga till fält i en **förslagsförkortare?** Ja.

Index som stöder filter och sortering är proportionellt större än de som stöder bara fulltextsökning. Filtrera och sortera åtgärder söka efter exakta matchningar, vilket kräver närvaro av intakta dokument. Sökbara fält med stöd för fulltext och luddig sökning använder däremot inverterade index, som fylls i med tokeniserade termer som förbrukar mindre utrymme än hela dokument. 

> [!Note]
> Lagringsarkitektur anses vara en implementeringsdetalj för Azure Cognitive Search och kan ändras utan föregående meddelande. Det finns ingen garanti för att det nuvarande beteendet kommer att kvarstå i framtiden.

## <a name="suggesters"></a>Förslag på alternativ
En förslagsställare är ett avsnitt i schemat som definierar vilka fält i ett index som används för att stödja automatisk komplettering eller typ-framåt-frågor vid sökningar. Vanligtvis skickas partiella söksträngar till [REST API (Förslag)](https://docs.microsoft.com/rest/api/searchservice/suggestions) medan användaren skriver en sökfråga och API:et returnerar en uppsättning föreslagna dokument eller fraser. 

Fält som läggs till i en förslagsman används för att skapa söktermer för typen framåt. Alla söktermer skapas under indexeringen och lagras separat. Mer information om hur du skapar en förslagsstruktur finns i [Lägga till förslagsföreare](index-add-suggesters.md).

## <a name="scoring-profiles"></a>Poängprofiler

En [bedömningsprofil](index-add-scoring-profiles.md) är en del av schemat som definierar anpassade bedömningsbeteenden som gör att du kan påverka vilka objekt som visas högre upp i sökresultaten. Poängprofiler består av fältvikter och funktioner. Om du vill använda dem anger du en profil efter namn på frågesträngen.

En standardbedömningsprofil arbetar bakom kulisserna för att beräkna en sökpoäng för varje objekt i en resultatuppsättning. Du kan använda den interna, namnlösa poängprofilen. Du kan också ange **att standardScoringProfile** ska använda en anpassad profil som standard, anropad när en anpassad profil inte anges i frågesträngen.

## <a name="analyzers"></a>Analysverktyg

Analysatorelementet anger namnet på den språkanalysator som ska användas för fältet. Mer information om hur du kan analysera analysatorer finns i Lägga till [analysatorer i ett Azure Cognitive Search-index](search-analyzers.md). Analysatorer kan endast användas med sökbara fält. När analysatorn har tilldelats ett fält kan den inte ändras om du inte återskapar indexet.

## <a name="cors"></a>CORS

JavaScript på klientsidan kan inte anropa api:er som standard eftersom webbläsaren förhindrar alla begäranden mellan ursprung. Om du vill tillåta frågor med flera ursprung i indexet aktiverar du CORS (Resursdelning över flera ursprung) genom att ange attributet **corsOptions.** Av säkerhetsskäl stöder endast fråge-API:er CORS. 

Följande alternativ kan ställas in för CORS:

+ **allowedOrigins** (obligatoriskt): Detta är en lista över ursprung som kommer att beviljas åtkomst till ditt index. Detta innebär att alla JavaScript-kod som serveras från dessa ursprung kommer att tillåtas att fråga ditt index (förutsatt att det ger rätt api-nyckel). Varje ursprung är vanligtvis `protocol://<fully-qualified-domain-name>:<port>` `<port>` av formuläret men utelämnas ofta. Mer information [finns i Samåkningsresursdelning (Wikipedia).](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)

  Om du vill tillåta åtkomst till `*` alla ursprung, inkludera som ett enda objekt i den **tillåtnaOrigins-matrisen.** *Detta rekommenderas inte praxis för produktionssöktjänster* men det är ofta användbart för utveckling och felsökning.

+ **maxAgeInSeconds** (valfritt): Webbläsare använder det här värdet för att bestämma varaktigheten (i sekunder) för att cachelagra CORS preflight-svar. Detta måste vara ett icke-negativt heltal. Ju större detta värde är, desto bättre resultat kommer att bli, men ju längre tid det tar för CORS politiska förändringar att träda i kraft. Om den inte är inställd används en standardlängd på 5 minuter.

## <a name="encryption-key"></a>Krypteringsnyckel

Alla Azure Cognitive Search-index krypteras som standard med hjälp av Microsoft-hanterade nycklar, men index kan konfigureras för att krypteras med **kundhanterade nycklar** i Key Vault. Mer information finns [i Hantera krypteringsnycklar i Azure Cognitive Search](search-security-manage-encryption-keys.md).

## <a name="next-steps"></a>Nästa steg

Med en förståelse för indexsammansättning kan du fortsätta i portalen för att skapa ditt första index.

> [!div class="nextstepaction"]
> [Lägga till ett index (portal)](search-create-index-portal.md)
