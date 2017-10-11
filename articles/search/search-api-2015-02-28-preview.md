---
title: "Azure Search-tjänsten REST API-Version 2015-02-28-Preview | Microsoft Docs"
description: "Azure Search Service REST API-Version 2015-02-28-Preview innehåller experiment funktioner, till exempel naturliga språkanalys och moreLikeThis sökningar."
services: search
documentationcenter: na
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 3dba3bf8-9c83-42f6-82bc-04727bd11037
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/01/2017
ms.author: brjohnst
ms.openlocfilehash: e6ad5c964bfa8421be2706cb4015980e01a271b7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="azure-search-service-rest-api-version-2015-02-28-preview"></a>Azure Söktjänsts-REST API: Version 2015-02-28-Preview
Den här artikeln är referensdokumentationen för `api-version=2015-02-28-Preview`. Den här förhandsgranskningen utökar den aktuella allmänt tillgängliga versionen [api-version = 2015-02-28](https://msdn.microsoft.com/library/dn798935.aspx), genom att tillhandahålla experiment följande funktioner:

* `moreLikeThis`Frågeparametern i den [Sök dokument](#SearchDocs) API. Den söker efter andra dokument som är relevanta för ett visst dokument.

Några ytterligare delar av den `2015-02-28-Preview` REST API dokumenteras separat. Exempel på dessa är:

* [Bedömningsprofil profiler](search-api-scoring-profiles-2015-02-28-preview.md)
* [Indexerare](search-api-indexers-2015-02-28-preview.md)

Azure Search-tjänsten är tillgänglig i flera versioner. Se [Sök Versionhantering](http://msdn.microsoft.com/library/azure/dn864560.aspx) mer information.

## <a name="apis-in-this-document"></a>API: er i det här dokumentet
API för Azure Search-tjänsten stöder två URL-syntax för API: et: enkelt och OData (se [stöd för OData (Azure Search-API)](http://msdn.microsoft.com/library/azure/dn798932.aspx) information). I följande lista visas enkel syntax.

[Skapa Index](#CreateIndex)

    POST /indexes?api-version=2015-02-28-Preview

[Uppdatera Index](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2015-02-28-Preview

[Hämta Index](#GetIndex)

    GET /indexes/[index name]?api-version=2015-02-28-Preview

[Visar en lista över index](#ListIndexes)

    GET /indexes?api-version=2015-02-28-Preview

[Få indexstatistik](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2015-02-28-Preview

[Testa Analyzer](#TestAnalyzer)

    POST /indexes/[index name]/analyze?api-version=2015-02-28-Preview

[Ta bort ett Index](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2015-02-28-Preview

[Lägga till, ta bort, och uppdatera Data i ett Index](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2015-02-28-Preview

[Sök igenom dokument](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]
    POST /indexes/[index name]/docs/search?api-version=2015-02-28-Preview

[Sökning dokumentet](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[Antal dokument](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2015-02-28-Preview

[Förslag](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]
    POST /indexes/[index name]/docs/suggest?api-version=2015-02-28-Preview

- - -
<a name="IndexOps"></a>

## <a name="index-operations"></a>Indexåtgärder
Du kan skapa och hantera index i Azure Search-tjänsten via enkel HTTP-begäranden (POST, GET, PUT, DELETE) mot en resurs med angivet index. Om du vill skapa ett index efter du först ett JSON-dokument som beskriver indexeringsschema. Schemat definierar fälten för indexet sina datatyper och hur de kan användas (t.ex, i fulltextsökningar, filter, sortering eller faceting). Den definierar även bedömningsprofil profiler, suggesters och andra attribut för att konfigurera beteendet för indexet.

I följande exempel innehåller en illustration av ett schema som används för att söka på hotellinformation med beskrivningsfältet som definierats i två språk. Observera hur attribut styr hur fältet används. Till exempel den `hotelId` används som Dokumentnyckel (`"key": true`) och har exkluderats från fulltextsökningar (`"searchable": false`).

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
      {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
      {"name": "hotelName", "type": "Edm.String"},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ],
     "suggesters": [
      {
       "name": "sg",
       "searchMode": "analyzingInfixMatching",
       "sourceFields": ["hotelName"]
      }
     ]
    }

När indexet har skapats måste du överföra dokument som fyller i indexet. Se [Lägg till eller uppdatera dokument](#AddOrUpdateDocuments) för nästa steg.

En videopresentation indexering i Azure Search, finns det [Channel 9 molnet omfattar avsnitt på Azure Search](http://go.microsoft.com/fwlink/p/?LinkId=511509).

<a name="CreateIndex"></a>

## <a name="create-index"></a>Skapa index
Ett index är primärt ordna och söka dokument i Azure Search liknar hur en tabell ordnar poster i en databas. Varje indexet innehåller en samling dokument att alla motsvara indexeringsschema (fältnamn, datatyper och egenskaper), men index också ange ytterligare konstruktioner (suggesters, bedömningsprofil profiler och alternativ för CORS) som definierar andra Sök beteenden.

Du kan skapa ett nytt index i en Azure Search-tjänst med en HTTP POST eller PUT-begäran. Brödtexten i begäran är en JSON-schema som anger informationen index och konfiguration.

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Du kan också använda PUT och ange Indexnamnet på URI: N. Om indexet inte finns, kommer att skapas.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

Skapa ett index anger strukturen för de dokument som lagras och används i sökningar. Fylla indexet är en separat åtgärd. Det här steget kan du använda en [indexeraren](https://msdn.microsoft.com/library/azure/mt183328.aspx) (tillgängligt för datakällor som stöds) eller en [Lägg till, uppdatera eller ta bort dokument](https://msdn.microsoft.com/library/azure/dn798930.aspx) igen. Omvända indexet genereras när dokumenten är bokförd.

**Obs**: det maximala antalet tillåtna index varierar beroende på prisnivå. Tjänsten gratis kan upp till 3 index. Standard-tjänsten tillåter 50 index per söktjänst. Se [gränser och begränsningar](http://msdn.microsoft.com/library/azure/dn798934.aspx) mer information.

**Förfrågan**

HTTPS krävs för alla tjänstbegäranden. Den **Create Index** begäran kan konstrueras med en POST eller PUT metod. Du måste ange ett indexnamn i begärandetexten tillsammans med schemadefinition index när du använder POST. Med PUT är Indexnamnet en del av URL: en. Om det inte finns index skapas. Om det redan finns uppdateras den till den nya definitionen.

Indexnamnet måste vara gemener, börja med en bokstav eller siffra, har inga snedstreck eller punkter och vara mindre än 128 tecken. När du har startat Indexnamnet med en bokstav eller siffra kan resten av namnet innehålla en bokstav, antal och tankstreck, så länge streck inte är i följd.

Den `api-version` krävs. Se [Sök Versionhantering](http://msdn.microsoft.com/library/azure/dn864560.aspx) för en lista över tillgängliga versioner.

**Huvuden för begäran**

I följande lista beskrivs de obligatoriska och valfria begärandehuvudena.

* `Content-Type`: Krävs. Ställ in på`application/json`
* `api-key`: Krävs. Den `api-key` används för att
* autentisera begäran om att din söktjänst. Det är ett strängvärde som unik för din tjänst. Den **Create Index** begäran måste innehålla en `api-key` huvudet inställt till din administrationsnyckel (i stället för en fråga nyckel).

Du måste också tjänstnamnet att konstruera den begärda Webbadressen. Du kan hämta både tjänstens namn och `api-key` från tjänsten instrumentpanelen i Azure Portal. Se [skapa en Azure Search-tjänst i portalen](search-create-service-portal.md) sidan navigering hjälp.

<a name="RequestData"></a>
**Begäran brödtext Syntax**

Brödtexten i begäran innehåller en schemadefinition av som innehåller en lista över datafält i dokument som kan matas in i detta index, datatyper, attribut, samt en valfri lista med bedömningen profiler som används för att poängsätta matchande dokument när databasfrågan.

Observera att för en POST-begäran måste du ange Indexets namn i begärandetexten.

Det kan bara finnas ett nyckelfält i indexet. Det måste vara ett strängfält. Det här fältet motsvarar den unika identifieraren för varje dokument som lagras i indexet.

De huvudsakliga delarna i ett index är följande:

* `name`
* `fields`som kan matas in i detta index, inklusive namn, datatyp och egenskaper som definierar tillåtna åtgärder på fältet.
* `suggesters`används för automatisk komplettering eller typ-ahead-frågor.
* `scoringProfiles`används för anpassad sökning poäng rangordning. Se [Lägg till bedömningsprofil profiler](https://msdn.microsoft.com/library/azure/dn798928.aspx) mer information.
* `analyzers`, `charFilters`, `tokenizers`, `tokenFilters` används för att definiera hur dina dokument/frågor är uppdelade indexeras/sökbara token. Se [analys i Azure Search](https://aka.ms//azsanalysis) mer information.
* `defaultScoringProfile`används för att skriva över dina standard bedömningen beteenden.
* `corsOptions`att tillåta cross-origin frågor mot ditt index.

Syntaxen för att strukturera nyttolasten i begäran är som följer. Ett exempel på en begäran har angetts mer på i det här avsnittet.

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
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
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
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
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

**Indexattribut**

Följande attribut kan anges när ett index skapas. Mer information om batchbedömning och bedömningen profiler finns [lägga till bedömningen profiler](https://msdn.microsoft.com/library/azure/dn798928.aspx).

`name`-Anger namnet på fältet.

`type`-Anger datatypen för fältet.

`searchable`-Markerar fältet som fulltext-sökning kan. Det innebär att den kommer att göras analys, till exempel radbrytning under indexeringen. Om du ställer in en `searchable` fält till ett värde som ”skiner”, internt den delas upp i enskilda token ”Soligt” och ”dag”. Detta gör att fulltextsökningar för dessa villkor. Fält av typen `Edm.String` eller `Collection(Edm.String)` är `searchable` som standard. Fält av andra typer kan inte vara `searchable`.

* **Obs**: `searchable` fält använda extra utrymme i ditt index eftersom en ytterligare principfilerna version av fältvärdet fulltextsökningar lagras i Azure Search. Om du vill spara utrymme i ditt index och behöver du ett fält som ska ingå i sökningar, anger `searchable` till `false`.

`filterable`-Kan refereras i fältet `$filter` frågor. `filterable`skiljer sig från `searchable` i hur strängar ska hanteras. Fält av typen `Edm.String` eller `Collection(Edm.String)` som är `filterable` inte genomgår radbrytning, så att jämförelser för endast exakt matchar. Till exempel om du ställer in sådant fält `f` till ”skiner” `$filter=f eq 'sunny'` hittar inga matchningar men `$filter=f eq 'sunny day'` kommer. Alla fält är `filterable` som standard.

`sortable`-Som standard sorterar systemet resultat poäng, men i många upplevelser användare vill sortera efter fält i dokument. Fält av typen `Collection(Edm.String)` får inte vara `sortable`. Alla andra fält är `sortable` som standard.

`facetable`-Vanligtvis används i en presentation av sökresultat som innehåller antalet träffar per kategori (till exempel, Sök efter digitalkameror och se träffar efter varumärke, megapixel, av pris, etc.). Det här alternativet kan inte användas med fält av typen `Edm.GeographyPoint`. Alla andra fält är `facetable` som standard.

* **Obs**: fält av typen `Edm.String` som är `filterable`, `sortable`, eller `facetable` kan vara högst 32 KB längd. Detta beror på att dessa fält behandlas som en enda sökterm och den maximala längden på en term i Azure Search är 32KB. Om du behöver lagra mer text än detta i ett enda strängfält behöver du uttryckligen ställa in `filterable`, `sortable`, och `facetable` till `false` i Indexdefinition.
* **Obs**: om ett fält har ingen av de ovanstående attribut `true` (`searchable`, `filterable`, `sortable`, eller`facetable`) fältet effektivt undantagits från inverterad indexet. Det här alternativet är användbart för fält som inte används i frågor, men som behövs i sökresultaten. Förutom dessa fält från indexet förbättrar prestanda.

`key`-Markerar fältet innehåller unika identifierare för dokument i indexet. Exakt ett fält måste väljas som den `key` fältet och det måste vara av typen `Edm.String`. Nyckelfält kan användas för att leta upp dokument direkt via den [sökning API](#LookupAPI).

`retrievable`-Anger om fältet kan returneras i sökresultatet.  Detta är användbart när du vill använda ett fält (till exempel marginal) som ett filter, sortera eller bedömningen mekanism men inte vill att fält som ska visas för slutanvändaren. Det här attributet måste vara `true` för `key` fält.

`analyzer`-Anger namnet på analyzer om du vill använda för fält vid sökning och indexering tid. Tillåtna uppsättning värden finns [Analyzers](https://msdn.microsoft.com/library/mt605304.aspx). Det här alternativet kan användas med `searchable` fält och den kan inte anges tillsammans med antingen `searchAnalyzer` eller `indexAnalyzer`.  När analyzer är valt, kan inte ändras för fältet.

`searchAnalyzer`-Anger namnet på analyzer som används för närvarande sökning för fältet. Tillåtna uppsättning värden finns [Analyzers](https://msdn.microsoft.com/library/mt605304.aspx). Det här alternativet kan användas med `searchable` fält. Det måste anges tillsammans med `indexAnalyzer` och kan inte anges tillsammans med den `analyzer` alternativet. Den här analyzer kan uppdateras på ett befintligt fält.

`indexAnalyzer`-Anger namnet på analyzer som används för närvarande indexering för fältet. Tillåtna uppsättning värden finns [Analyzers](https://msdn.microsoft.com/library/mt605304.aspx). Det här alternativet kan användas med `searchable` fält. Det måste anges tillsammans med `searchAnalyzer` och kan inte anges tillsammans med den `analyzer` alternativet. När analyzer är valt, kan inte ändras för fältet.

`suggesters`-Anger sökläget och fält som är källan till innehållet för förslag. Se [Suggesters](#Suggesters) mer information.

`scoringProfiles`-Definierar anpassade bedömningsprofil beteenden som gör att du kan påverka vilka objekt som visas överst i sökresultaten. Bedömningsprofil profiler består av fältvikter och funktioner. Se [lägga till bedömningen profiler](https://msdn.microsoft.com/library/azure/dn798928.aspx) för mer information om de attribut som används i en bedömningsprofilen.

<!-- This is a standalone topic in MSDN -->
<a name="LanguageSupport"></a>
**Språkstöd**

Sökbara fält genomgå analys som omfattar ofta avstavning text normalisering och filtrera bort villkor. Som standard sökbara fält i Azure Search analyseras med den [Apache Lucene Standard analyzer](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html) som delar upp text i element som följer den[”Unicode-Text segmentering”](http://unicode.org/reports/tr29/) regler. Dessutom konverterar standard analyzer alla tecken till gemener formuläret. Både indexerade dokument och sökvillkoren kan du gå igenom analysen under indexering och frågebearbetning.

Azure Search har stöd för flera olika språk. Varje språk kräver en icke-standard text analyzer som-konton för egenskaperna för ett visst språk. Azure Search finns två typer av analyzers:

* 35 analyzers backas upp av Lucene.
* 50 analyzers backas upp av egna Microsoft naturligt språk bearbetning teknik som används i Office och Bing.

Vissa utvecklare kanske föredrar Lucene mer bekanta, enkla, öppen källkod lösningen. Lucene analyzers är snabbare, men Microsoft analyzers har avancerade funktioner, till exempel lemmatisering, word decompounding (i språk som tyska, danska, nederländska, svenska, norska, estniska, Slutför, ungerska, slovakiska) och entitet recognition (URL: er, e-postmeddelanden, datum, nummer). Om möjligt bör du köra jämförelser av både Microsoft och Lucene analyzers att avgöra vilket som är en bättre anpassning.

***Jämförelse***

Lucene analyzer för engelska utökar standard analyzer. Det tar bort genitiv (avslutande) från ord, gäller härrör enligt [Porter härrör algoritmen](http://tartarus.org/~martin/PorterStemmer/), och tar bort engelska [stoppord](http://en.wikipedia.org/wiki/Stop_words).

Jämförelse utför Microsoft analyzer lemmatisering i stället för härrör. Det innebär att den kan hantera böjda och oregelbundna word formulär förbättras vad resulterar i mer relevant sökresultat (titta på modul 7 av [Azure Search MVA presentation](http://www.microsoftvirtualacademy.com/training-courses/adding-microsoft-azure-search-to-your-websites-and-apps) för mer information).

Indexering med Microsoft analyzers är i genomsnitt två till tre gånger långsammare än motsvarigheterna Lucene beroende på språket. Sökningsprestanda avsevärt påverkas inte för frågor genomsnittlig storlek.

***Konfiguration***

Du kan ange för varje fält i indexdefinitionen den `analyzer` egenskapen till ett analyzer namn som anger vilka språk och leverantör. Samma analyzer tillämpas när indexering och sökning för fältet.
Du kan till exempel ha separata fält för engelska, franska och spanska hotell beskrivningar som finns sida vid sida i samma index. Använd den ['searchFields' Frågeparametern](#SearchQueryParameters) att ange vilket språkspecifika fält om du vill söka mot i dina frågor. Du kan granska frågan exempel som innehåller den `analyzer` egenskap i [Sök dokument](#SearchDocs). 

***Analyzer lista***

Nedan visas en lista över språk som stöds tillsammans med Lucene och Microsoft analyzer namn.

<table style="font-size:12">
    <tr>
        <th>Språk</th>
        <th>Microsoft analyzer namn</th>
        <th>Lucene analyzer namn</th>
    </tr>
    <tr>
        <td>Arabiska</td>
        <td>ar.Microsoft</td>
        <td>ar.lucene</td>        
    </tr>
    <tr>
        <td>Armeniska</td>
        <td></td>
        <td>hy.lucene</td>
      </tr>
    <tr>
        <td>Bangla</td>
        <td>bn.Microsoft</td>
        <td></td>
    </tr>
      <tr>
        <td>Baskiska</td>
        <td></td>
        <td>EU.lucene</td>
    </tr>
      <tr>
         <td>Bulgariska</td>
        <td>BG.Microsoft</td>
        <td>BG.lucene</td>
      </tr>
      <tr>
        <td>Katalanska</td>
        <td>CA.Microsoft</td>
        <td>CA.lucene</td>          
      </tr>
    <tr>
        <td>Kinesiska, förenklad</td>
        <td>zh-Hans.microsoft</td>
        <td>zh-Hans.lucene</td>        
    </tr>
    <tr>
        <td>Kinesiska, traditionell</td>
        <td>zh-Hant.microsoft</td>
        <td>zh-Hant.lucene</td>        
    <tr>
    <tr>
        <td>Kroatiska</td>
        <td>HR.Microsoft</td>
        <td/></td>
    </tr>
    <tr>
        <td>Tjeckiska</td>
        <td>CS.Microsoft</td>
        <td>CS.lucene</td>        
    </tr>    
    <tr>
        <td>Danska</td>
        <td>da.Microsoft</td>
        <td>da.lucene</td>        
    </tr>    
    <tr>
        <td>Nederländska</td>
        <td>NL.Microsoft</td>
        <td>NL.lucene</td>    
    </tr>    
    <tr>
        <td>Svenska</td>        
        <td>en.Microsoft</td>
        <td>en.lucene</td>        
    </tr>
    <tr>
        <td>Estniska</td>
        <td>et.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Finska</td>
        <td>Fi.Microsoft</td>
        <td>Fi.lucene</td>        
    </tr>    
    <tr>
        <td>Franska</td>
        <td>fr.Microsoft</td>
        <td>fr.lucene</td>        
    </tr>
    <tr>
        <td>Galiciska</td>
        <td></td>
        <td>GL.lucene</td>        
      </tr>
    <tr>
        <td>Tyska</td>
        <td>de.Microsoft</td>
        <td>de.lucene</td>        
    </tr>
    <tr>
        <td>Grekiska</td>
        <td>el.Microsoft</td>
        <td>el.lucene</td>        
    </tr>
    <tr>
        <td>Gujarati</td>
        <td>Gu.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Hebreiska</td>
        <td>HE.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Hindi</td>
        <td>Hi.Microsoft</td>
        <td>Hi.lucene</td>        
    </tr>
    <tr>
        <td>Ungerska</td>        
        <td>HU.Microsoft</td>
        <td>HU.lucene</td>
    </tr>
    <tr>
        <td>Isländska</td>
        <td>is.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Indonesiska (Bahasa)</td>
        <td>ID.Microsoft</td>
        <td>ID.lucene</td>        
    </tr>
    <tr>
        <td>Irländska</td>
        <td></td>
          <td>GA.lucene</td>
    </tr>
    <tr>
        <td>Italienska</td>
        <td>IT.Microsoft</td>
        <td>IT.lucene</td>        
    </tr>
    <tr>
        <td>Japanska</td>
        <td>Ja.Microsoft</td>
        <td>Ja.lucene</td>

    </tr>
    <tr>
        <td>Kannada</td>
        <td>KA.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Koreanska</td>
        <td>Ko.Microsoft</td>
        <td>Ko.lucene</td>
    </tr>
    <tr>
        <td>Lettiska</td>        
        <td>LV.Microsoft</td>
        <td>LV.lucene</td>    
    </tr>
    <tr>
        <td>Litauiska</td>
        <td>lt.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Malayalam</td>
        <td>ml.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Malajiska (latinsk)</td>
        <td>MS.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Marathi</td>
        <td>MR.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Norska</td>
        <td>NB.Microsoft</td>
        <td>No.lucene</td>        
    </tr>
      <tr>
        <td>Persiska</td>
        <td></td>
        <td>fa.lucene</td>        
      </tr>
    <tr>
        <td>Polska</td>
        <td>PL.Microsoft</td>
        <td>PL.lucene</td>        
    </tr>
    <tr>
        <td>Portugisiska (Brasilien)</td>
        <td>pt-Br.microsoft</td>
        <td>pt-Br.lucene</td>        
    </tr>
    <tr>
        <td>Portugisiska (Portugal)</td>
        <td>pt-Pt.microsoft</td>        
        <td>pt-Pt.lucene</td>
    </tr>
    <tr>
        <td>Punjabi</td>
        <td>pa.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Rumänska</td>
        <td>ro.Microsoft</td>
        <td>ro.lucene</td>
    </tr>
    <tr>
        <td>Ryska</td>
        <td>RU.Microsoft</td>
        <td>RU.lucene</td>    
    </tr>
    <tr>
        <td>Serbiska (kyrillisk)</td>
        <td>SR-cyrillic.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Serbiska (latinsk)</td>
        <td>SR-latin.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Slovakiska</td>
        <td>Sk.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Slovenska</td>
        <td>Sl.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Spanska</td>
        <td>ES.Microsoft</td>
        <td>ES.lucene</td>
    </tr>
    <tr>
        <td>Svenska</td>
        <td>SV.Microsoft</td>
        <td>SV.lucene</td>
    </tr>

    <tr>
        <td>Tamilska</td>
        <td>ta.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Telugu</td>
        <td>te.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Thai</td>
        <td>TH.Microsoft</td>
        <td>TH.lucene</td>
    </tr>
    <tr>
        <td>Turkiska</td>
        <td>TR.Microsoft</td>
        <td>TR.lucene</td>        
    </tr>
    <tr>
        <td>Ukrainska</td>
        <td>UK.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Urdu</td>
        <td>Your.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Vietnamesiska</td>
        <td>Vi.Microsoft</td>
        <td></td>
    </tr>
    <td colspan="3">Dessutom ger Azure Search språkoberoende analyzer konfigurationer</td>
    <tr>
        <td>Standard-ASCII vikning</td>
        <td>standardasciifolding.lucene</td>
        <td>
        <ul>
            <li>Unicode-text segmentering (Standard Tokenizer)</li>
            <li>Vikning filtret för ASCII - omvandlar Unicode-tecken som inte tillhör uppsättningen först 127 ASCII-tecken till deras motsvarigheter i ASCII. Detta är användbart för att ta bort diakritiska tecken.</li>
        </ul>
        </td>
    </tr>
</table>

Alla analyzers med namn som är försedd med <i>lucene</i> drivs av [Apache Lucene språkanalys](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html). Mer information om ASCII vikning filtret hittar [här](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html).

**Förslag på alternativ**

En `suggester` definierar vilka fält i ett index som används för att stödja automatisk komplettering i sökningar. Vanligtvis partiella söksträngar skickas till den [förslag API](#Suggestions) när användaren skriver en sökfråga och API: N returnerar en uppsättning föreslagna fraser. En förslagsställarens som du definierar i indexet avgör vilka fält som används för att skapa typen ahead sökvillkoren. Se [Suggesters](#Suggesters) konfigurationsinformation.

**Poängprofiler**

En `scoringProfile` definierar anpassade bedömningsprofil beteenden som gör att du kan påverka vilka objekt som visas överst i sökresultaten. Bedömningsprofil profiler består av fältvikter och funktioner. Om du vill använda dem anger du en profil med namnet på frågesträngen.

Standard bedömningen profilen fungerar i bakgrunden att beräkna en sökning poäng för alla objekt i en resultatuppsättning. Du kan använda den interna namnlösa bedömningsprofilen. Du kan också ange `defaultScoringProfile` att använda en anpassad profil som standard, anropas när en anpassad profil inte anges i frågesträngen.

Se [Lägg till bedömningsprofil profiler sökindex (Azure Söktjänsts-REST API)](search-api-scoring-profiles-2015-02-28-preview.md) mer information.

**Alternativ för CORS**

Javascript för klientsidan kan inte anropa alla API: er som standard eftersom webbläsaren hindrar alla cross-origin-begäranden. Aktivera CORS (Cross-Origin Resource Sharing) genom att ange den `corsOptions` attribut för att tillåta cross-origin frågor till ditt index. Observera att endast query API: er stöd CORS av säkerhetsskäl. Följande alternativ kan anges för CORS:

* `allowedOrigins`(krävs): det här är en lista över ursprung som beviljas åtkomst till ditt index. Detta innebär att alla Javascript-kod från dessa ursprung tillåts fråga ditt index (förutsatt att den ger rätt API-nyckel). Varje ursprung har vanligtvis formatet `protocol://fully-qualified-domain-name:port` även om porten utelämnas ofta. Se [i den här artikeln](http://go.microsoft.com/fwlink/?LinkId=330822) för mer information.
  * Om du vill tillåta åtkomst till alla ursprung `*` som ett enskilt objekt i den `allowedOrigins` matris. Observera att **detta rekommenderas inte för produktion search-tjänster.** Det kan dock vara användbar för utveckling eller felsökning.
* `maxAgeInSeconds`(valfritt): webbläsare använder det här värdet för att fastställa varaktigheten (i sekunder) för cache CORS preflight-svar. Detta måste vara ett icke-negativt heltal. Ju större det här värdet är, desto bättre prestanda kommer att men ju längre tid det tar för ändringar av CORS-principer ska gälla. Om den inte är inställd kommer på 5 minuter att används standardvärdet.

<a name="CreateUpdateIndexExample"></a>
**Begäran Body-exempel**

    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String"},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["hotelName"]
        }
      ]
    }

**Svar**

För en lyckad begäran: ”201 skapad”.

Som standard innehåller svarstexten JSON för indexdefinitionen som har skapats. Om den `Prefer` huvudet i begäran är inställd på `return=minimal`svarstexten är tomt och statuskoden lyckas blir ”204 saknar innehåll” i stället för ”201 skapad”. Detta gäller oavsett om PUT eller POST användes för att skapa index.

**Kommentarer**

Det finns för närvarande begränsat stöd för index schema-uppdateringar. Schema-uppdateringar som kräver omindexering, till exempel ändring av fälttyp stöds inte för närvarande. Även om befintliga fält inte kan ändras eller tas bort, kan nya fält läggas till ett befintligt index när som helst. När ett nytt fält läggs alla befintliga dokument i indexet automatiskt att ha ett null-värde för fältet. Inga ytterligare lagringsutrymme används förrän nya dokument har lagts till i indexet.

<a name="Suggesters"></a>

## <a name="suggesters"></a>Förslag på alternativ
Funktionen förslag i Azure Search är en typ i förväg eller automatisk komplettering fråga-funktion som ger en lista över möjliga söktermer som svar på partiella sträng indata som angetts i en sökruta. Har du antagligen märkt frågeförslag när du använder kommersiella sökmotorer: att skriva ”.NET” i Bing genererar en lista över villkor för ”.NET 4.5” ”, .NET Framework 3,5-tums, och så vidare. När du använder tjänsten Search REST-API, kräver implementera förslag i ett anpassat program för Azure Search följande:

* Aktivera förslag genom att lägga till en **förslagsställarens** konstruktionen i ditt index med namnet sökläget och en lista med fält som typ-ahead anropas. Till exempel om du anger ”cityName” som ett fält i datakällan, att skriva en partiell sökning sträng med ”Sea” leder ”Seattle”, ”snäckor” och ”Seatac” (alla tre är faktiska stadsnamn) erbjuds som frågeförslag till användaren.
* Anropa förslag genom att anropa den [förslag API](#Suggestions) i programkoden. Vanligtvis skickas partiella söksträngar till tjänsten när användaren är att skriva en fråga och returnerar en uppsättning föreslagna fraser för detta API.

Den här artikeln förklarar hur du konfigurerar en **förslagsställarens**. Du bör också granska den [förslag API](#Suggestions) information om hur en förslagsställarens används.

**Användning**

`Suggesters`skapas i index och fungerar bäst för att föreslå specifika dokument i stället för Lös ord eller fraser. Fälten bra kandidat är titlar, namn och andra relativt korta fraser som kan identifiera ett objekt. Mindre effektiva är återkommande fält, till exempel kategorier och taggar eller mycket långa fält, till exempel beskrivningar och kommentarer fält.

Som en del av indexdefinitionen, kan du lägga till en enda förslagsställarens till den `suggesters` samling. Egenskaper som definierar en förslagsställarens inkluderar följande:

* `name`: Namnet på förslagsställarens. Du kan använda namnet på förslagsställarens när du anropar den `suggest` API.
* `searchMode`: Den strategi som används för att söka efter möjliga fraser. Det enda läge som stöds för närvarande är `analyzingInfixMatching`, vilket genomför flexibla matchningar av fraser i början eller i mitten av meningarna.
* `sourceFields`: En lista med en eller flera fält som är källan till innehållet för förslag. Endast fält av typen `Edm.String` och `Collection(Edm.String)` kanske källor för förslag. Endast de fält som inte har en anpassad språk analyzer ange kan användas.

**Förslagsställarens-exempel**

En förslagsställarens är en del av indexet. Endast en förslagsställarens kan finnas i den `suggesters` samling i den aktuella versionen, tillsammans med fältsamlingen och `scoringProfiles`.

        {
          "name": "hotels",
          "fields": [
             . . .
           ],
          "suggesters": [
            {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields: ["hotelName", "category"]
            }
          ],
          "scoringProfiles": [
             . . .
          ]
        }

> [!NOTE]
> Om du använder den offentliga förhandsversionen av Azure Search `suggesters` ersätter en äldre boolesk egenskap (`"suggestions": false`) som bara stöds prefix förslag för korta strängar (3-25 tecken). Dess ersättare `suggesters`, stöder infix matchar som söker efter matchande villkoren i början eller i mitten fältet innehåll med bättre tolerans för fel i söksträngar. Från och med allmänt tillgänglig, är detta nu endast implementering av förslag API. Den äldre `suggestions` egenskap som introducerades i `api-version=2014-07-31-Preview` fortsätter att fungera i den här versionen, men fungerar inte i den `2015-02-28` eller senare versioner av Azure Search.
> 
> 

<a name="UpdateIndex"></a>

## <a name="update-index"></a>Uppdatera Index
Du kan uppdatera ett befintligt index i Azure Search med hjälp av en HTTP PUT-begäran. Uppdateringar kan inkludera att lägga till nya fält i det befintliga schemat, ändra alternativ för CORS och ändra bedömningsprofil profiler. Se [lägga till bedömningen profiler](https://msdn.microsoft.com/library/azure/dn798928.aspx) för mer information. Du kan ange namnet på indexet för att uppdatera på URI-begäran:

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Viktigt:** stöd för index schemauppdateringar är begränsad till åtgärder som inte kräver återskapa sökindexet. Schema-uppdateringar som kräver omindexering, till exempel ändring av fälttyp, stöds inte för närvarande. Nya fält läggas när som helst, även om befintliga fält inte kan ändras eller tas bort. Samma gäller `suggesters`. Nya fält kan läggas till i en förslagsställarens när fälten läggs till, men fält kan inte tas bort från `suggesters` och befintliga fält inte kan läggas till `suggesters`.

När du lägger till ett nytt fält till ett index har alla befintliga dokument i indexet automatiskt ett null-värde för fältet. Inga ytterligare lagringsutrymme används förrän nya dokument har lagts till i indexet.

**Förfrågan**

HTTPS krävs för alla tjänstbegäranden. Den **uppdatera Index** begäran har skapats med hjälp av HTTP PUT. Med PUT är Indexnamnet en del av URL: en. Om det inte finns index skapas. Om indexet redan uppdateras den till den nya definitionen.

Indexnamnet måste vara gemener, börja med en bokstav eller siffra, har inga snedstreck eller punkter och vara mindre än 128 tecken. När du har startat Indexnamnet med en bokstav eller siffra kan resten av namnet innehålla en bokstav, antal och tankstreck, så länge streck inte är i följd.

`api-version=[string]`(krävs). Förhandsversionen är `api-version=2015-02-28-Preview`. Se [Sök Versionhantering](http://msdn.microsoft.com/library/azure/dn864560.aspx) information och alternativa versioner.

**Huvuden för begäran**

I följande lista beskrivs de obligatoriska och valfria begärandehuvudena.

* `Content-Type`: Krävs. Ställ in på`application/json`
* `api-key`: Krävs. Den `api-key` används för att autentisera begäran om att din söktjänst. Det är ett strängvärde som unik för din tjänst. Den **uppdatera Index** begäran måste innehålla en `api-key` huvudet inställt till din administrationsnyckel (i stället för en fråga nyckel).

Du måste också tjänstnamnet att konstruera den begärda Webbadressen. Du kan hämta namnet på tjänsten och `api-key` från tjänsten instrumentpanelen i Azure Portal. Se [skapa en Azure Search-tjänst i portalen](search-create-service-portal.md) sidan navigering hjälp.

**Begäran brödtext Syntax**

När du uppdaterar ett befintligt index, inkluderar brödtexten den ursprungliga schemadefinitionen plus nya fält som du lägger till samt ändrade bedömningsprofil profiler, suggesters och alternativ för CORS, eventuella. Om du inte ändrar bedömningsprofil profiler och alternativ för CORS, måste du inkludera original från när indexet har skapats. I allmänhet det bästa mönstret som ska användas för uppdateringar är att hämta indexdefinitionen med GET, ändra och sedan uppdatera med PUT.

Schema-syntax används för att skapa ett index återges här i informationssyfte. Se [Create Index](#CreateIndex) för mer information.

    {
      "name": (optional) "name_of_index",
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
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
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
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
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


**Svar**

För en lyckad begäran ”: 204 saknar innehåll”.

Som standard är svarstexten tom. Men om den `Prefer` huvudet i begäran är inställd på `return=representation`, svarstexten innehåller JSON för indexdefinitionen som har uppdaterats. I det här fallet statuskoden lyckas blir ”200 OK”.

**Uppdaterar indexdefinitionen med anpassade analyzers**

När en analyzer, en tokenizer, ett token filter eller ett char-filter har definierats kan inte ändras. Nya kan läggas till ett befintligt index endast om den `allowIndexDowntime` -flaggan inställd på true i begäran om uppdatering index: 

`PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true`

Observera att den här åtgärden placeras indexet offline för minst ett par sekunder, och din indexering och fråga begäranden att misslyckas. Prestanda- och skrivbehörighet tillgängligheten för indexet kan vara försämrad i flera minuter när indexet uppdateras eller längre för mycket stora index.

<a name="ListIndexes"></a>

## <a name="list-indexes"></a>Lista över index
Den **listan index** åtgärden returnerar en lista över index som för närvarande i Azure Search-tjänsten.

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**Förfrågan**

HTTPS krävs för alla tjänstbegäranden. Den **listan index** begäran kan konstrueras med GET-metoden.

`api-version=[string]`(krävs). Förhandsversionen är `api-version=2015-02-28-Preview`. Se [Sök Versionhantering](http://msdn.microsoft.com/library/azure/dn864560.aspx) information och alternativa versioner.

**Huvuden för begäran**

I följande lista beskrivs de obligatoriska och valfria begärandehuvudena.

* `api-key`: Krävs. Den `api-key` används för att autentisera begäran om att din söktjänst. Det är ett strängvärde som unik för din tjänst. Den **listan index** begäran måste innehålla en `api-key` inställd på en administrationsnyckel (i stället för en fråga nyckel).

Du måste också tjänstnamnet att konstruera den begärda Webbadressen. Du kan hämta namnet på tjänsten och `api-key` från tjänsten instrumentpanelen i Azure Portal. Se [skapa en Azure Search-tjänst i portalen](search-create-service-portal.md) sidan navigering hjälp.

**Begärandetexten**

Ingen.

**Svar**

Statuskod: 200 OK returneras för ett lyckat svar.

Här är ett exempel svarstexten:

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

Observera att du kan filtrera svaret till bara de egenskaper som du är intresserad av. Till exempel om du vill att endast en lista över namn på index använda OData `$select` frågealternativet:

    GET /indexes?api-version=2015-02-28-Preview&$select=name

I det här fallet visas svaret från exemplet ovan på följande sätt:

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

Detta är en användbar teknik för att spara bandbredd om du har många index i din söktjänst.

<a name="GetIndex"></a>

## <a name="get-index"></a>Hämta Index
Den **hämta Index** åtgärden hämtar indexdefinitionen från Azure Search.

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Förfrågan**

HTTPS krävs för tjänstbegäranden. Den **hämta Index** begäran kan konstrueras med GET-metoden.

[Index name] i URI-begäran anger det index som ska returneras från samlingen index.

`api-version=[string]`(krävs). Förhandsversionen är `api-version=2015-02-28-Preview`. Se [Sök Versionhantering](http://msdn.microsoft.com/library/azure/dn864560.aspx) information och alternativa versioner.

**Huvuden för begäran**

I följande lista beskrivs de obligatoriska och valfria begärandehuvudena.

* `api-key`: `api-key` Används för att autentisera begäran om att din söktjänst. Det är ett strängvärde som unik för din tjänst. Den **hämta Index** begäran måste innehålla en `api-key` inställd på en administrationsnyckel (i stället för en fråga nyckel).

Du måste också tjänstnamnet att konstruera den begärda Webbadressen. Du kan hämta namnet på tjänsten och `api-key` från tjänsten instrumentpanelen i Azure Portal. Se [skapa en Azure Search-tjänst i portalen](search-create-service-portal.md) sidan navigering hjälp.

**Begärandetexten**

Ingen.

**Svar**

Statuskod: 200 OK returneras för ett lyckat svar.

Du ser i exemplet JSON i [att skapa och uppdatera ett Index](#CreateUpdateIndexExample) ett exempel på nyttolasten svar.

<a name="DeleteIndex"></a>

## <a name="delete-index"></a>Ta bort indexet
Den **ta bort indexet** åtgärden tar bort ett index och associerade dokument från din Azure Search-tjänst. Du kan hämta Indexnamnet från tjänsten instrumentpanelen i Azure portal och API: et. Se [listan index](#ListIndexes) mer information.

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Förfrågan**

HTTPS krävs för tjänstbegäranden. Den **ta bort indexet** begäran kan konstrueras med DELETE-metoden.

[Index name] i URI-begäran anger det index som ska tas bort från samlingen index.

`api-version=[string]`(krävs). Förhandsversionen är `api-version=2015-02-28-Preview`. Se [Sök Versionhantering](http://msdn.microsoft.com/library/azure/dn864560.aspx) information och alternativa versioner.

**Huvuden för begäran**

I följande lista beskrivs de obligatoriska och valfria begärandehuvudena.

* `api-key`: Krävs. Den `api-key` används för att autentisera begäran om att din söktjänst. Det är ett strängvärde som unik för din tjänst-URL. Den **ta bort indexet** begäran måste innehålla en `api-key` huvudet inställt till din administrationsnyckel (i stället för en fråga nyckel).

Du måste också tjänstnamnet att konstruera den begärda Webbadressen. Du kan hämta namnet på tjänsten och `api-key` från tjänsten instrumentpanelen i Azure Portal. Se [skapa en Azure Search-tjänst i portalen](search-create-service-portal.md) sidan navigering hjälp.

**Begärandetexten**

Ingen.

**Svar**

Statuskod: 204 Nej innehåll returneras för ett lyckat svar.

<a name="GetIndexStats"></a>

## <a name="get-index-statistics"></a>Få indexstatistik
Den **hämta indexstatistik** åtgärd returnerar från Azure Search en dokumentantal för aktuellt index plus lagringskvoten.

    GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

> [!NOTE]
> Statistik om dokumentet antal och lagringsstorlek har samlats in några minuters mellanrum, inte i realtid. Statistik som returneras av denna API kan därför inte återspegla ändringar på grund av de senaste Indexeringsåtgärder.
> 
> 

**Förfrågan**

HTTPS krävs för alla begäranden som tjänster. Den **hämta indexstatistik** begäran kan konstrueras med GET-metoden.

[Index name] i URI-begäran talar om tjänsten för att returnera indexstatistik för det angivna indexet.

`api-version=[string]`(krävs). Förhandsversionen är `api-version=2015-02-28-Preview`. Se [Sök Versionhantering](http://msdn.microsoft.com/library/azure/dn864560.aspx) information och alternativa versioner.

**Huvuden för begäran**

I följande lista beskrivs de obligatoriska och valfria begärandehuvudena.

* `api-key`: `api-key` Används för att autentisera begäran om att din söktjänst. Det är ett strängvärde som unik för din tjänst. Den **hämta indexstatistik** begäran måste innehålla en `api-key` inställd på en administrationsnyckel (i stället för en fråga nyckel).

Du måste också tjänstnamnet att konstruera den begärda Webbadressen. Du kan hämta namnet på tjänsten och `api-key` från tjänsten instrumentpanelen i Azure Portal. Se [skapa en Azure Search-tjänst i portalen](search-create-service-portal.md) sidan navigering hjälp.

**Begärandetexten**

Ingen.

**Svar**

Statuskod: 200 OK returneras för ett lyckat svar.

Svarstexten finns i följande format:

    {
      "documentCount": number,
      "storageSize": number (size of the index in bytes)
    }

<a name="TestAnalyzer"></a>

## <a name="test-analyzer"></a>Testa Analyzer
Den **analysera API** visar hur en analyzer delar upp text i token.

    POST https://[service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Förfrågan**

HTTPS krävs för alla begäranden som tjänster. Den **analysera API** begäran kan konstrueras med POST-metoden.

`api-version=[string]`(krävs). Förhandsversionen är `api-version=2015-02-28-Preview`. Se [Sök Versionhantering](http://msdn.microsoft.com/library/azure/dn864560.aspx) information och alternativa versioner.

**Huvuden för begäran**

I följande lista beskrivs de obligatoriska och valfria begärandehuvudena.

* `api-key`: `api-key` Används för att autentisera begäran om att din söktjänst. Det är ett strängvärde som unik för din tjänst. Den **analysera API** begäran måste innehålla en `api-key` inställd på en administrationsnyckel (i stället för en fråga nyckel).

Du måste också Indexnamnet och tjänstnamn att konstruera den begärda Webbadressen. Du kan hämta namnet på tjänsten och `api-key` från tjänsten instrumentpanelen i Azure Portal. Se [skapa en Azure Search-tjänst i portalen](search-create-service-portal.md) sidan navigering hjälp.

**Begärandetexten**

    {
      "text": "Text to analyze",
      "analyzer": "analyzer_name"
    }

eller

    {
      "text": "Text to analyze",
      "tokenizer": "tokenizer_name",
      "tokenFilters": (optional) [ "token_filter_name" ],
      "charFilters": (optional) [ "char_filter_name" ]
    }

Den `analyzer_name`, `tokenizer_name`, `token_filter_name` och `char_filter_name` måste vara giltiga namn på fördefinierade eller anpassade analyzers, tokenizers, token-filter och char filter för indexet. Mer information om processen för lexikala analys finns [analys i Azure Search](https://aka.ms/azsanalysis).

**Svar**

Statuskod: 200 OK returneras för ett lyckat svar.

Svarstexten finns i följande format:

    {
      "tokens": [
        {
          "token": string (token),
          "startOffset": number (index of the first character of the token),
          "endOffset": number (index of the last character of the token),
          "position": number (position of the token in the input text)
        },
        ...
      ]
    }

**Analysera API-exempel**

**Förfrågan**

    {
      "text": "Text to analyze",
      "analyzer": "standard"
    }

**Svar**

    {
      "tokens": [
        {
          "token": "text",
          "startOffset": 0,
          "endOffset": 4,
          "position": 0
        },
        {
          "token": "to",
          "startOffset": 5,
          "endOffset": 7,
          "position": 1
        },
        {
          "token": "analyze",
          "startOffset": 8,
          "endOffset": 15,
          "position": 2
        }
      ]
    }

- - -
<a name="DocOps"></a>

## <a name="document-operations"></a>Dokumentet åtgärder
I Azure Search index lagras i molnet och fylls i med hjälp av JSON-dokument som du överför till tjänsten. Alla dokument som du överför utgör Kristi Sök data. Dokument innehåller fält, av vilka vissa tokeniserad i söktermer som de överförs. Den `/docs` URL-segment i Azure Search API representerar samling av dokument i ett index. Alla åtgärder som utförs i samlingen som överför sammanslagning, ta bort eller fråga dokument vidta placera i samband med ett index, så URL: er för dessa åtgärder börjar alltid med `/indexes/[index name]/docs` efter ett angivet index.

Programkoden måste antingen generera JSON-dokument för att överföra till Azure Search eller använda en [indexeraren](https://msdn.microsoft.com/library/dn946891.aspx) att läsa in dokument om datakällan är Azure SQL Database eller Azure Cosmos DB. Normalt fylls index i från en enda datamängd som du anger.

Du bör planera ska ha ett dokument för varje objekt som du vill söka efter. En film uthyrning programmet kan ha ett dokument per film, en storefront programmet kan ha ett dokument per SKU, ett online kursprogramvara program kan ha ett dokument per kursen, research företag kan ha ett dokument för varje academic papper i sin databas och så vidare.

Dokument består av ett eller flera fält. Fälten kan innehålla text som är tokeniserad med Azure Search in söktermer som inte tokeniserad eller icke-textvärden som kan användas i filter eller bedömningsprofil profiler. Namn, datatyper och funktioner som stöds för varje fält bestäms av indexeringsschema. Något av fälten i varje indexeringsschema måste anges som ett ID och varje dokument måste ha ett värde för fältet ID som unikt identifierar dokumentet i indexet. Alla andra dokumentfält är valfria och som standard till en null-värde om lämnas Ospecificerad. Observera att null-värden inte plats i sökindexet.

Innan du kan ladda upp dokument, måste du ha skapat indexet på tjänsten. Se [Create Index](#CreateIndex) mer information om det här första steget.

<a name="AddOrUpdateDocuments"></a>

## <a name="add-update-or-delete-documents"></a>Lägga till, uppdatera, eller ta bort dokument
Du kan ladda upp, merge, merge eller överför eller ta bort dokument från ett specificerat index med hjälp av HTTP POST. För många uppdateringar rekommenderas massbearbetning av dokument (1000 dokument per batch) eller ca 16 MB per batch.

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Förfrågan**

HTTPS krävs för alla tjänstbegäranden. Du kan ladda upp, merge, merge eller överför eller ta bort dokument från ett specificerat index med hjälp av HTTP POST.

URI-begäran innehåller [Indexnamnet] anger vilken index om du vill skicka dokument. Du kan bara skicka dokument till ett index i taget.

`api-version=[string]`(krävs). Förhandsversionen är `api-version=2015-02-28-Preview`. Se [Sök Versionhantering](http://msdn.microsoft.com/library/azure/dn864560.aspx) information och alternativa versioner.

**Huvuden för begäran**

I följande lista beskrivs de obligatoriska och valfria begärandehuvudena.

* `Content-Type`: Krävs. Ställ in på`application/json`
* `api-key`: Krävs. Den `api-key` används för att autentisera begäran om att din söktjänst. Det är ett strängvärde som unik för din tjänst. Den **lägga till dokument** begäran måste innehålla en `api-key` huvudet inställt till din administrationsnyckel (i stället för en fråga nyckel).

Du måste också tjänstnamnet att konstruera den begärda Webbadressen. Du kan hämta namnet på tjänsten och `api-key` från tjänsten instrumentpanelen i Azure Portal. Se [skapa en Azure Search-tjänst i portalen](search-create-service-portal.md) sidan navigering hjälp.

**Begärandetexten**

Brödtexten i begäran innehåller ett eller flera dokument indexeras. Dokument identifieras med en unik nyckel. Varje dokument som är associerat med en åtgärd: Överför dokument, mergeOrUpload eller ta bort. Överför förfrågningar måste innehålla dokumentdata som en uppsättning nyckel/värde-par.

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }

> [!NOTE]
> Dokumentet nycklar kan endast innehålla bokstäver, siffror, bindestreck (”-”), understreck (”_”) och likhetstecken (”=”). Mer information finns i [namngivningsregler](https://msdn.microsoft.com/library/azure/dn857353.aspx).
> 
> 

**Dokumentåtgärder**

* `upload`: En åtgärd som överför liknar ”upsert” där dokumentet kommer att infogas om det är nya och uppdaterade/ersättas om den finns. Observera att alla fält ersätts i fallet med uppdateringen.
* `merge`: Sammanslagning uppdaterar ett befintligt dokument med de angivna fälten. Om dokumentet inte finns misslyckas kopplingen. Alla fält som du anger i en sammanfogning ersätter det befintliga fältet i dokumentet. Detta gäller även fält av typen `Collection(Edm.String)`. Om dokumentet innehåller fältet ”taggar” med värdet till exempel `["budget"]` och du utföra en koppling med värdet `["economy", "pool"]` ”taggar” det slutliga värdet för fältet ”taggar” blir `["economy", "pool"]`. Kommer det att **inte** vara `["budget", "economy", "pool"]`.
* `mergeOrUpload`: fungerar som `merge` om ett dokument med den angivna nyckeln finns redan i indexet. Om dokumentet inte finns fungerar som `upload` med ett nytt dokument.
* `delete`: Ta bort tar bort det angivna dokumentet från indexet. Observera att alla fält som du anger i en `delete` åtgärden än nyckelfältet kommer att ignoreras. Om du vill ta bort ett fält från ett dokument, använda `merge` enkelt och i stället ange fältet explicit till `null`.

**Svar**

Statuskoden 200 returneras (OK) för ett lyckat svar, vilket innebär att alla objekt indexerade. Detta anges av den `status` egenskapen anges till true för alla artiklar, såväl som den `statusCode` egenskapsuppsättningen till 201 (för nyligen uppladdade dokument) eller 200 (för sammanfogade eller borttagna dokument):

    {
      "value": [
        {
          "key": "unique_key_of_new_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 201
        },
        {
          "key": "unique_key_of_merged_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        },
        {
          "key": "unique_key_of_deleted_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        }
      ]
    }  

Statuskoden 207 (flera Status) returnerades när minst ett objekt inte har har indexerats. Objekt som inte har indexerats har den `status` fältet inställt på false. Den `errorMessage` och `statusCode` egenskaper kommer att ange orsaken till felet indexering:

    {
      "value": [
        {
          "key": "unique_key_of_document_1",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later.",
          "statusCode": 503
        },
        {
          "key": "unique_key_of_document_2",
          "status": false,
          "errorMessage": "Document not found.",
          "statusCode": 404
        },
        {
          "key": "unique_key_of_document_3",
          "status": false,
          "errorMessage": "Index is temporarily unavailable because it was updated with the 'allowIndexDowntime' flag set to 'true'. Please try again later.",
          "statusCode": 422
        }
      ]
    }  

I följande tabell beskrivs olika per dokument statuskoder som kan returneras i svaret. Observera att vissa anger problem med begäran, medan andra indikerar tillfälliga fel. Denna du kan försöka efter en stund.

<table style="font-size:12">
    <tr>
        <th>Statuskod</th>
        <th>Betydelse</th>
        <th>Återförsökbart</th>
        <th>Anteckningar</th>
    </tr>
    <tr>
        <td>200</td>
        <td>Dokumentet har har ändrats eller tagits bort.</td>
        <td>Saknas</td>
        <td>Ta bort är <a href="https://en.wikipedia.org/wiki/Idempotence">idempotent</a>. Det vill säga även om en Dokumentnyckel inte finns i index, leder försöker en borttagningsåtgärd med nyckeln statuskod 200.</td>
    </tr>
    <tr>
        <td>201</td>
        <td>Dokumentet har skapats.</td>
        <td>Saknas</td>
        <td></td>
    </tr>
    <tr>
        <td>400</td>
        <td>Ett fel uppstod i dokumentet som hindrade den från att indexeras.</td>
        <td>Nej</td>
        <td>Felmeddelande i svaret visar fel med dokumentet.</td>
    </tr>
    <tr>
        <td>404</td>
        <td>Dokumentet kan inte sammanfogas eftersom den angivna nyckeln inte finns i indexet.</td>
        <td>Nej</td>
        <td>Det här felet uppstår inte för överföringar, eftersom de skapar nya dokument och det uppstår inte för borttagningar eftersom de är <a href="https://en.wikipedia.org/wiki/Idempotence">idempotent</a>.</td>
    </tr>
    <tr>
        <td>409</td>
        <td>En versionskonflikt upptäcktes vid försök att indexera ett dokument.</td>
        <td>Ja</td>
        <td>Detta kan inträffa när du försöker att mer än en gång samtidigt indexera samma dokument.</td>
    </tr>
    <tr>
        <td>422</td>
        <td>Indexet är inte tillgänglig för tillfället eftersom den uppdaterades med 'allowIndexDowntime'-flaggan inställd på 'true'.</td>
        <td>Ja</td>
        <td></td>
    </tr>
    <tr>
        <td>503</td>
        <td>Din söktjänst är tillfälligt otillgänglig, möjligen på grund av hög belastning.</td>
        <td>Ja</td>
        <td>Koden ska vänta innan du försöker igen i det här fallet eller riskerar du att förlänga att tjänsten.</td>
    </tr>
</table> 

**Obs**: om din klientkod ofta påträffar ett 207 svar, en möjlig orsak är att systemet är under belastning. Du kan kontrollera detta genom att kontrollera den `statusCode` -egenskapen för 503. Om så är fallet, rekommenderar vi ***begränsning indexering begäranden***. Annars, om indexering trafik inte subside systemet kunde starta avvisar alla begäranden med 503-fel.

Statuskoden 429 anger att du har överskridit kvoten för antalet dokument per index. Du måste skapa ett nytt index eller uppgradera för högre kapacitetsgränser.

**Exempel:**

    {
      "value": [
        {
          "@search.action": "upload",
          "hotelId": "1",
          "baseRate": 199.0,
          "description": "Best hotel in town",
          "description_fr": "Meilleur hôtel en ville",
          "hotelName": "Fancy Stay",
          "category": "Luxury",
          "tags": ["pool", "view", "wifi", "concierge"],
          "parkingIncluded": false,
          "smokingAllowed": false,
          "lastRenovationDate": "2010-06-27T00:00:00Z",
          "rating": 5,
          "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
          "@search.action": "upload",
          "hotelId": "2",
          "baseRate": 79.99,
          "description": "Cheapest hotel in town",
          "description_fr": "Hôtel le moins cher en ville",
          "hotelName": "Roach Motel",
          "category": "Budget",
          "tags": ["motel", "budget"],
          "parkingIncluded": true,
          "smokingAllowed": true,
          "lastRenovationDate": "1982-04-28T00:00:00Z",
          "rating": 1,
          "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
- - -
<a name="SearchDocs"></a>

## <a name="search-documents"></a>Sök igenom dokument
En **Sök** åtgärden utfärdas som en GET eller POST-begäran och anger parametrarna som ger kriterierna för att välja matchande dokument.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**När du ska använda en POST i stället för att hämta**

När du använder HTTP GET för att anropa den **Sök** API, du måste vara medveten om att längden på den begärda Webbadressen inte får överskrida 8 KB. Detta är vanligtvis tillräckligt för de flesta program. Vissa program kan dock ge mycket stora frågor eller OData filteruttryck. För dessa program är med hjälp av HTTP POST ett bättre alternativ eftersom det tillåter större filter och frågor än GET. Med POST är antalet villkor eller satser i en fråga begränsande faktorn inte storleken på raw frågan eftersom begäran storleksgränsen för POST är ungefär 16 MB.

> [!NOTE]
> Även om storleksgränsen för POST-begäran är mycket stora får inte sökningar och filteruttryck vara godtyckligt komplexa. Se [Lucene frågesyntaxen](https://msdn.microsoft.com/library/mt589323.aspx) och [OData-uttryckssyntax](https://msdn.microsoft.com/library/dn798921.aspx) för mer information om begränsningar för sökning fråge- och komplexitet.
> 
> 

**Förfrågan**

HTTPS krävs för tjänstbegäranden. Den **Sök** begäran kan konstrueras med metoderna GET eller POST.

URI-begäran anger vilka index fråga efter alla dokument som matchar parametrarna. Parametrar har angetts i frågesträngen när det gäller GET-begäranden och i begäran brödtext för POST-begäranden.

Ett bra tips när du skapar GET-begäranden, Kom ihåg att [URL-koda](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) särskilda frågeparametrar vid anrop av REST API direkt. För **Sök** åtgärder, detta omfattar:

* `$filter`
* `facet`
* `highlightPreTag`
* `highlightPostTag`
* `search`
* `moreLikeThis`

URL-kodning rekommenderas endast på ovanstående Frågeparametrar. Om du av misstag URL-koda hela frågesträngen (allt efter den?), begäranden bryts.

Dessutom krävs URL-kodning bara vid anrop av REST API: et direkt med GET. Ingen URL-kodning krävs när du anropar **Sök** med POST, eller när du använder den [.NET-klientbibliotek](https://msdn.microsoft.com/library/dn951165.aspx), som hanterar URL-kodning för dig.

<a name="SearchQueryParameters"></a>
**Frågeparametrar**

**Sök** accepterar flera parametrar som tillhandahåller frågevillkor och även ange sökbeteendet. Du anger dessa parametrar i URL: en frågesträng vid anrop av **Sök** via GET och som JSON-egenskaper i frågans brödtext vid anrop av **Sök** via POST. Syntaxen för vissa parametrar skiljer mellan GET och POST. Skillnaderna beskrivs i tillämpliga fall nedan:

`search=[string]`(valfritt) - texten att söka efter. Alla `searchable` fält söks som standard om inte `searchFields` har angetts. När du söker `searchable` fält, söktexten själva tokeniserad så att flera villkor kan avgränsas med blanksteg (till exempel: `search=hello world`). Om du vill matcha alla termen använder `*` (det kan vara användbart för booleskt filterfrågor). Om du utesluter den här parametern har samma effekt som du anger `*`. Se [enkel frågesyntaxen](https://msdn.microsoft.com/library/dn798920.aspx) för närmare information om söksyntax.

* **Obs**: resultatet kan ibland vara konstigt när frågor skickas över `searchable` fält. Tokenizer innehåller logik för att hantera fall som är gemensamma för engelsk text som apostrofer, kommatecken i siffror osv. Till exempel `search=123,456` matchar en enskild term 123,456 i stället för enskilda villkoren 123 och 456, eftersom kommatecken används som avgränsare tusen för stora tal på engelska. Därför bör du använda blanksteg i stället för skiljetecken avgränsa villkoren i den `search` parameter.

`searchMode=any|all`(valfritt, som standard `any`) – oavsett om någon eller samtliga av sökord måste matchas för att kunna räkna dokumentet som en matchning.

`searchFields=[string]`(valfritt) – lista över kommaavgränsade fältnamn att söka efter den angivna texten. Målfält måste vara markerad som `searchable`.

`queryType=simple|full`(valfritt, som standard `simple`) – när inställd på ”enkel” söktext tolkas med hjälp av en enkel frågespråk som möjliggör symboler som, + * och ””. Frågor utvärderas över alla sökbara fält (fält som anges i `searchFields`) i varje dokument som standard. När frågetypen har angetts till `full` söktext tolkas med Lucene frågespråket som tillåter specifika fält och viktat sökningar. Se [enkel frågesyntaxen](https://msdn.microsoft.com/library/dn798920.aspx) och [Lucene frågesyntaxen](https://msdn.microsoft.com/library/mt589323.aspx) för närmare information om Sök-syntax. 

> [!NOTE]
> Intervallet sökning i Lucene frågespråket inte stöds för $filter som ger liknande funktionalitet.
> 
> 

`moreLikeThis=[key]`(valfritt) **Viktigt:** den här funktionen är endast tillgänglig i `2015-02-28-Preview`. Det här alternativet kan inte användas i en fråga som innehåller parametern text search `search=[string]`. Den `moreLikeThis` parametern hittas dokument som liknar det dokument som anges av dokumentnyckeln. När en sökning har begärts med `moreLikeThis`, en lista över söktermer genereras baserat på frekvens och sällsynt termer i källdokument. Dessa villkor används sedan för att begära. Som standard innehållet i alla `searchable` fält anses om `searchFields` används för att begränsa vilka fält som genomsöks.  

`$skip=#`(valfritt) – Antal sökresultat att hoppa över; Får inte vara större än 100 000. Om du vill skanna dokument i följd men kan inte använda `$skip` på grund av den här begränsningen kan du använda `$orderby` på en helt sorterade nyckel och `$filter` med en fråga i stället.

> [!NOTE]
> När du anropar **Sök** med efter den här parametern heter `skip` i stället för `$skip`.
> 
> 

`$top=#`(valfritt) – Antal sökresultat att hämta. Detta kan användas tillsammans med `$skip` att implementera sidindelning på klientsidan i sökresultaten.

> [!NOTE]
> När du anropar **Sök** med efter den här parametern heter `top` i stället för `$top`.
> 
> 

`$count=true|false`(valfritt, som standard `false`) – anger om du vill hämta Totalt antal resultat. Detta är antalet alla dokument som matchar den `search` och `$filter` parametrar, ignorerar `$top` och `$skip`. Om det här värdet anges till `true` kan påverka prestanda. Observera att antalet returnerade en uppskattning.

> [!NOTE]
> När du anropar **Sök** med efter den här parametern heter `count` i stället för `$count`.
> 
> 

`$orderby=[string]`(valfritt) – en lista över CSV-uttryck för att sortera resultaten efter. Varje uttryck kan vara ett fältnamn eller ett anrop till den `geo.distance()` funktion. Varje uttryck kan följas av `asc` anges stigande ordning och `desc` att ange fallande. Standardvärdet är stigande ordning. TIES delas av matchar resultat av dokument. Om ingen `$orderby` anges standardsorteringsordningen fallande dokumentet matchar poäng. Det finns en gräns på 32-satser för `$orderby`.

> [!NOTE]
> När du anropar **Sök** med efter den här parametern heter `orderby` i stället för `$orderby`.
> 
> 

`$select=[string]`(valfritt) – en lista över kommaavgränsade fält för att hämta. Om inget anges ingår alla fält som har markerats som hämtas i schemat. Du kan också explicit begära alla fält genom att ange den här parametern `*`.

> [!NOTE]
> När du anropar **Sök** med efter den här parametern heter `select` i stället för `$select`.
> 
> 

`facet=[string]`(noll eller fler) - aspekten av ett fält. Strängen kan alternativt innehåller parametrar för att anpassa faceting uttryckt i CSV- `name:value` par. Giltiga parametrar finns:

* `count`(max antal aspekten villkor; standardvärdet är 10). Det finns inget maximum men högre värden medföra en motsvarande prestandaförsämring, särskilt om fasetterad fältet innehåller ett stort antal unika villkor.
  * Till exempel: `facet=category,count:5` hämtar upp fem kategorier i aspekten resultat.  
  * **Obs**: om den `count` parameter är mindre än antalet unika villkor, resultaten kan vara felaktig. Det beror på hur faceting frågor är fördelade på shards. Öka `count` vanligtvis ökar exaktheten termen antal, men på en prestandakostnad.
* `sort`(en av `count` att sortera *fallande* efter antal `-count` att sortera *stigande* efter antal `value` att sortera *stigande* efter värde, eller `-value` att sortera *fallande* av värdet)
  * Till exempel: `facet=category,count:3,sort:count` hämtar de tre översta kategorier i aspekten resulterar i fallande ordning efter antalet dokument med varje Ortnamn. Till exempel om de översta tre kategorierna är Budget, översikt och Lyxig, och Budget har 5 träffar, översikt har 6 och Lyxig har 4, blir sedan buckets i ordningen som översikt, Budget, Lyxig.
  * Till exempel: `facet=rating,sort:-value` producerar buckets för alla möjliga betyg i fallande ordning efter värde. Till exempel om klassificeringarna är från 1 till 5, beställs buckets 5, 4, 3, 2, 1, oavsett hur många dokument matchar varje klassificering.
* `values`(pipe-avgränsad numeriskt eller `Edm.DateTimeOffset` värden som anger en dynamisk värdeuppsättningen aspekten post)
  * Till exempel: `facet=baseRate,values:10|20` ger tre buckets: en för grundavgift 0 till men med upp till 10, en för 10, men inte inklusive 20 och en för 20 eller högre.
  * Till exempel: `facet=lastRenovationDate,values:2010-02-01T00:00:00Z` ger två buckets: en för hotell renoverade före februari 2010 och en för hotell renoverade februari 1 2010 eller senare.
* `interval`(heltal-intervall som är större än 0 för siffror eller `minute`, `hour`, `day`, `week`, `month`, `quarter`, `year` för värden för datum-tid)
  * Till exempel: `facet=baseRate,interval:100` producerar buckets baserat på grundavgift intervall i storlek 100. Till exempel om grundpris alla mellan $60 och $600, blir det buckets för 0-100, 200 100, 200 300, 300-400, 400 500 och 500 600.
  * Till exempel: `facet=lastRenovationDate,interval:year` producerar en bucket för varje år när hotell har renoverade.
* `timeoffset`([+-] hh: mm, [+-] hhmm, eller [+-] hh) `timeoffset` är valfritt. Kan endast kombineras med de `interval` alternativet endast när tillämpas på ett fält av typen `Edm.DateTimeOffset`. Värdet anger förskjutning för UTC-tid till kontot för att tid gränser.
  * Till exempel: `facet=lastRenovationDate,interval:day,timeoffset:-01:00` använder dag gränsen som börjar på UTC 01:00:00 (midnatt i tidszon mål)
* **Obs**: `count` och `sort` kan kombineras i samma aspekten specifikationen, men de kan inte kombineras med `interval` eller `values`, och `interval` och `values` kan inte kombineras.
* **Obs**: intervall aspekter på datum och tid beräknas baserat på UTC-tid om `timeoffset` har inte angetts. Exempel: för `facet=lastRenovationDate,interval:day`, gräns startas på 00:00:00 UTC. 

> [!NOTE]
> När du anropar **Sök** med efter den här parametern heter `facets` i stället för `facet`. Du också ange den som en JSON-matris med strängar som där varje sträng är ett separat aspekten uttryck.
> 
> 

`$filter=[string]`(valfritt) – ett strukturerade sökuttryck standard OData-syntax.

> [!NOTE]
> När du anropar **Sök** med efter den här parametern heter `filter` i stället för `$filter`.
> 
> 

`highlight=[string]`(valfritt) – en uppsättning csv-fältnamn som används för träffar markeras. Endast `searchable` fält kan användas för träffar markering.

`highlightPreTag=[string]`(valfritt, som standard `<em>`) – en string-tagg som annat för att träffa visar. Måste anges med `highlightPostTag`.

> [!NOTE]
> När du anropar **Sök** använder GET, reserverade tecken i URL-Adressen måste vara procent-kodat (till exempel % 23 i stället för #).
> 
> 

`highlightPostTag=[string]`(valfritt, som standard `</em>`)-taggen sträng som lägger till för att träffa visar. Måste anges med `highlightPreTag`.

> [!NOTE]
> När du anropar **Sök** använder GET, reserverade tecken i URL-Adressen måste vara procent-kodat (till exempel % 23 i stället för #).
> 
> 

`scoringProfile=[string]`(valfritt) – namnet på bedömningsprofilen att utvärdera matcha resultat för att matcha dokument för att sortera resultaten.

`scoringParameter=[string]`(noll eller fler) – anger värden för varje parameter som definierats i funktionen för bedömningsprofil (till exempel `referencePointParameter`) i formatet `name-value1,value2,...`.

* Till exempel om bedömningsprofilen definierar en funktion med en parameter med namnet ”mylocation” sträng frågealternativet skulle vara `&scoringParameter=mylocation--122.2,44.8`. Första streck skiljer namnet från listan, medan andra streck är en del av det första värdet (longitud i det här exemplet).
* För bedömningsprofil parametrar som taggar för förstärkning som kan innehålla kommatecken, kan du undanta sådana värden i listan med enkla citattecken. Om själva värdena innehåller enkla citattecken, kan du undanta dem av dubblerade.
  * Till exempel om du har en tagg förstärkning parameter med namnet ”mytag” och du vill höja för taggen värden ”Hello, O'Brien” och ”Smith” frågan alternativ för anslutningssträngen skulle vara `&scoringParameter=mytag-'Hello, O''Brien',Smith`. Observera att citattecken är endast för värden med kommatecken.

> [!NOTE]
> När du anropar **Sök** med efter den här parametern heter `scoringParameters` i stället för `scoringParameter`. Du också ange den som en JSON-matris med strängar som där varje sträng är en separat `name-values` par.
> 
> 

`minimumCoverage`(valfritt, standardvärdet är 100)-ett tal mellan 0 och 100 som anger procentandelen av det index som måste förses med en sökfråga för frågan som ska rapporteras som lyckas. Som standard hela indexet måste vara tillgängliga eller `Search` returneras HTTP-statuskoden 503. Om du ställer in `minimumCoverage` och `Search` lyckas den ska returnera HTTP 200 och innehålla en `@search.coverage` värde i svaret som anger procentandelen av det index som ingick i frågan.

> [!NOTE]
> Ange den här parametern till ett värde som är lägre än 100 kan vara användbart för att söka efter tillgänglighet även för tjänster med bara en replik. Dock är inte alla matchande dokument garanterat måste finnas i sökresultaten. Om Sök återkalla är viktigare för ditt program än tillgänglighet, så det är bäst att lämna `minimumCoverage` standardinställningen 100.
> 
> 

`api-version=[string]`(krävs). Förhandsversionen är `api-version=2015-02-28-Preview`. Se [Sök Versionhantering](http://msdn.microsoft.com/library/azure/dn864560.aspx) information och alternativa versioner.

Obs: för den här åtgärden den `api-version` har angetts som en frågeparameter i URL: en oavsett om du anropar **Sök** med GET eller POST.

**Huvuden för begäran**

I följande lista beskrivs de obligatoriska och valfria begärandehuvudena.

* `api-key`: `api-key` Används för att autentisera begäran om att din söktjänst. Det är ett strängvärde som unik för din tjänst-URL. Den **Sök** begäran kan ange en administrationsnyckeln eller Frågenyckeln för `api-key`.

Du måste också tjänstnamnet att konstruera den begärda Webbadressen. Du kan hämta namnet på tjänsten och `api-key` från tjänsten instrumentpanelen i Azure Portal. Se [skapa en Azure Search-tjänst i portalen](search-create-service-portal.md) sidan navigering hjälp.

**Begärandetexten**

För GET: Ingen.

För POST:

    {
      "count": true | false (default),
      "facets": [ "facet_expression_1", "facet_expression_2", ... ],
      "filter": "odata_filter_expression",
      "highlight": "highlight_field_1, highlight_field_2, ...",
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 100),
      "moreLikeThis": "document_key" (mutually exclusive with "search" parameter),
      "orderby": "orderby_expression",
      "scoringParameters": [ "scoring_parameter_1", "scoring_parameter_2", ... ],
      "scoringProfile": "scoring_profile_name",
      "search": "simple_query_expression",
      "searchFields": "field_name_1, field_name_2, ...",
      "searchMode": "any" (default) | "all",
      "select": "field_name_1, field_name_2, ...",
      "skip": # (default 0),
      "top": #
    }

**Fortsättning av svar som partiell sökning**

Ibland kan inte Azure Search returnera alla begärda resultat i ett enda Search-svar. Detta kan inträffa av olika skäl, till exempel när begäranden för många dokument genom att inte ange `$top` eller ange ett värde för `$top` som är för stor. I sådana fall Azure Search innehåller den `@odata.nextLink` kommentar i brödtext för svar, och även `@search.nextPageParameters` om den har en POST-begäran. Du kan använda värdena för dessa anteckningar för att formulera en annan sökbegäran att hämta nästa del av search-svar. Detta kallas en ***fortsättning*** på den ursprungliga sökningen begäran och anteckningarna vanligtvis kallas ***fortsättning token***. Se [exemplet nedan](#SearchResponse) mer information om syntaxen för dessa anteckningar och var de förekommer i brödtext för svar. 

Skälen varför Azure Search kan returnera fortsättning token är implementeringen-specifika och kan ändras. Robust klienter ska alltid vara redo att hantera fall där färre dokument än väntat returneras en fortsättningstoken ingår fortsätta hämtning av dokument. Observera också att du måste använda samma HTTP-metoden som den ursprungliga begäranden ska kunna fortsätta. Till exempel om du har skickat en begäran om att hämta alla fortsättning begäranden som du skickar måste också använda GET (och även för POST).

<a name="SearchResponse"></a>
**Svar**

Statuskod: 200 OK returneras för ett lyckat svar.

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "@search.nextPageParameters": { (request body to fetch the next page of results if not all results could be returned in this response and Search was called with POST)
        "count": ... (value from request body if present),
        "facets": ... (value from request body if present),
        "filter": ... (value from request body if present),
        "highlight": ... (value from request body if present),
        "highlightPreTag": ... (value from request body if present),
        "highlightPostTag": ... (value from request body if present),
        "minimumCoverage": ... (value from request body if present),
        "moreLikeThis": ... (value from request body if present),
        "orderby": ... (value from request body if present),
        "scoringParameters": ... (value from request body if present),
        "scoringProfile": ... (value from request body if present),
        "search": ... (value from request body if present),
        "searchFields": ... (value from request body if present),
        "searchMode": ... (value from request body if present),
        "select": ... (value from request body if present),
        "skip": ... (page size plus value from request body if present),
        "top": ... (value from request body if present minus page size),
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if not all results could be returned in this response; Applies to both GET and POST)
    }

**Exempel:**

Du hittar ytterligare exempel på den [syntaxen för OData-uttryck för Azure Search](https://msdn.microsoft.com/library/azure/dn798921.aspx) sidan.

1)    Sök indexet sorterade i fallande efter datum.

    GET-/indexes/hotels/docs? Sök = * & $orderby = lastRenovationDate desc & api-version = 2015-02-28-Preview

    POST /indexes/hotels/docs/search? api-version 2015-02-28-Preview = {”Sök” ”: *”, ”orderby”: ”lastRenovationDate desc”}

2)    Söka i en fasetterad sökning och hämta facets för kategorier, klassificering, etiketter samt artiklar med baseRate i särskilda intervall:

    GET-/indexes/hotels/docs? Sök = test & aspekten = kategori & aspekten = klassificering & aspekten = taggar & aspekten = baseRate värden: 80 | 150 | 220 & api-version = 2015-02-28-Preview

    POST /indexes/hotels/docs/search? api-version 2015-02-28-Preview = {”Sök”: ”test”, ”facets”: [”kategori”, ”klassificering”, ”-taggar” ”baseRate värden: 80 | 150 | 220”]}

3)    Med hjälp av ett filter, begränsa föregående fasetterad frågeresultaten när användaren klickar på klassificeringen 3 och kategori ”översikt”:

    GET-/indexes/hotels/docs? Sök = test & aspekten = taggar & aspekten = baseRate värden: 80 | 150 | 220 & $filter = klassificering eq 3 och kategori eq 'översikt' & api-version = 2015-02-28-Preview

    POST /indexes/hotels/docs/search? api-version = 2015-02-28-Preview {”Sök”: ”test”, ”facets”: [”taggar” ”, baseRate värden: 80 | 150 | 220”], ”filter”: ”klassificering eq 3 och kategori eq 'Översikt'”}

4) Ange en övre gräns på unikt villkor som returneras i en fråga i en fasetterad sökning. Standardvärdet är 10, men du kan öka eller minska det här värdet med den `count` parameter på den `facet` attribut:

    GET-/indexes/hotels/docs? Sök = test & aspekten = ort, antal: 5 & api-version = 2015-02-28-Preview

    POST /indexes/hotels/docs/search? api-version 2015-02-28-Preview = {”Sök”: ”test”, ”facets”: [”Ort, antal: 5”]}

5)    Söka inom specifika områden. Till exempel ett fält för vissa språk:

    GET-/indexes/hotels/docs? Sök = hôtel & searchFields = description_fr & api-version = 2015-02-28-Preview

    POST /indexes/hotels/docs/search? api-version 2015-02-28-Preview = {”Sök”: ”hôtel”, ”searchFields”: ”description_fr”}

6) Sök Index över flera fält. Du kan till exempel lagra och fråga sökbara fält på flera olika språk, allt i samma index.  Om engelska och franska beskrivningar finnas samtidigt i samma dokument, returnerar någon eller samtliga i resultatet av frågan:

    GET-/indexes/hotels/docs? Sök = hotell & searchFields = beskrivning, description_fr & api-version = 2015-02-28-Preview

    POST /indexes/hotels/docs/search? api-version 2015-02-28-Preview = {”Sök”: ”hotell”, ”searchFields”: ”beskrivning, description_fr”}

Observera att du bara kan fråga ett index i taget. Skapa inte flera index för varje språk om du planerar att fråga en i taget.

7)    Växlingsfil - Get sidan 1 objekt (sidstorlek är 10):

    GET-/indexes/hotels/docs? Sök = * & $skip = 0 & $top = 10 & api-version = 2015-02-28-Preview

    POST /indexes/hotels/docs/search? api-version 2015-02-28-Preview = {”Sök” ”: *”, ”hoppa över”: 0, ”top”: 10}

8)    Växlingsfil - Get sidan 2 i objekt (sidstorlek är 10):

    GET-/indexes/hotels/docs? Sök = * & $skip = 10 & $top = 10 & api-version = 2015-02-28-Preview

    POST /indexes/hotels/docs/search? api-version 2015-02-28-Preview = {”Sök” ”: *”, ”hoppa över”: ”top” 10: 10}

9)    Hämta en specifik uppsättning fält:

    GET-/indexes/hotels/docs? Sök = * & $select = hotelName, beskrivning och api-version = 2015-02-28-Preview

    POST /indexes/hotels/docs/search? api-version 2015-02-28-Preview = {”Sök” ”: *”, ”Välj”: ”hotelName beskrivning”}

10)  Hämta dokument som matchar ett specifikt filter-uttryck:

    GET-/indexes/hotels/docs? $filter = (baseRate ge 60 och baseRate lt 300) eller hotelName eq 'Avancerad förblir' & api-version = 2015-02-28-Preview

    POST /indexes/hotels/docs/search? api-version 2015-02-28-Preview = {”filter” ”: (baseRate ge 60 och baseRate lt 300) eller hotelName eq 'Avancerad stanna'”}

11) Sök index och returnera fragment med träffar viktiga funktioner

    GET-/indexes/hotels/docs? Sök = något & Markera = beskrivning & api-version = 2015-02-28-Preview

    POST /indexes/hotels/docs/search? api-version 2015-02-28-Preview = {”Sök”: ”något”, ”highlight”: ”beskrivning”}

12) Söka och returnera dokument sorteras från längre bort från en referens närmare plats

    GET-/indexes/hotels/docs? Sök = något & $orderby=geo.distance (plats, geography'POINT(-122.12315 47.88121)') & api-version = 2015-02-28-Preview

    POST /indexes/hotels/docs/search? api-version 2015-02-28-Preview = {”Sök”: ”något”, ”orderby” ”: geo.distance (plats, geography'POINT(-122.12315 47.88121)')”}

13) Söka under förutsättning att det finns en bedömningsprofilen som kallas ”geo” med två avståndsresultatfunktioner, en anger en parameter som kallas ”currentLocation” och en definierar en parameter med namnet ”lastLocation”

    GET-/indexes/hotels/docs? Sök = något & scoringProfile = geo & scoringParameter = currentLocation--122.123,44.77233 & scoringParameter = lastLocation--121.499,44.2113 & api-version = 2015-02-28-Preview

    POST /indexes/hotels/docs/search? api-version 2015-02-28-Preview = {”Sök”: ”något”, ”scoringProfile”: ”geo”, ”scoringParameters”: [”currentLocation--122.123,44.77233” ”, lastLocation--121.499,44.2113”]}

14) Hitta dokument i index med [enkel frågesyntaxen](https://msdn.microsoft.com/library/dn798920.aspx). Den här frågan returnerar hotell där sökbara fält innehåller villkoren ”bekvämlighet” och ”plats” men inte ”översikt”:

    GET-/indexes/hotels/docs? Sök = bekvämlighet + plats-översikt & searchMode = all & api-version = 2015-02-28-Preview

    POST /indexes/hotels/docs/search? api-version 2015-02-28-Preview = {”Sök” ”: bekvämlighet + plats-översikt”, ”searchMode”: ”alla”}

Observera användningen av `searchMode=all` ovan. Inklusive den här parametern åsidosätter standardvärdet `searchMode=any`, att se till att som `-motel` innebär ”och inte” i stället för ”eller inte”. Utan `searchMode=all`, får du ”eller inte” som utökar snarare än begränsar sökresultaten och det kan vara krånglig till vissa användare.

15) Hitta dokument i index med [lucene frågesyntaxen](https://msdn.microsoft.com/library/mt589323.aspx). Den här frågan returnerar hotell där kategorifältet innehåller termen ”budget” och alla sökbara fält som innehåller frasen ”nyligen renoverade”. Dokument som innehåller frasen ”nyligen renoverade” rankas högre på grund av termen öka värdet (3)

    GET-/indexes/hotels/docs? Sök = kategori: budget och \"nyligen renoverade\"^ 3 & searchMode = all & api-version = 2015-02-28-Preview & querytype = full

    POST /indexes/hotels/docs/search? api-version 2015-02-28-Preview = {”Sök” ”: kategori: budget och \"nyligen renoverade\"^ 3”, ”queryType”: ”full”, ”searchMode”: ”alla”}

<a name="LookupAPI"></a>

## <a name="lookup-document"></a>Sökning dokumentet
Den **sökning dokumentet** åtgärden hämtar ett dokument från Azure Search. Detta är användbart när en användare klickar på en specifik sökresultat, och du vill söka efter specifik information om dokumentet.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters]
    api-key: [admin or query key]

**Förfrågan**

HTTPS krävs för tjänstbegäranden. Den **sökning dokumentet** begäran kan konstrueras enligt följande.

    GET /indexes/[index name]/docs/key?[query parameters]

Alternativt kan du använda den traditionella OData-syntaxen för nyckelsökningen:

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

URI-begäran innehåller ett [indexnamn] och [], anger vilka dokumentet för att hämta från vilka index. Du kan bara hämta ett dokument åt gången. Använd **Sök** att hämta flera dokument i en enskild begäran.

**Frågeparametrar**

`$select=[string]`(valfritt) – en lista över kommaavgränsade fält för att hämta. Om Ospecificerad eller inställd på `*`, alla fält som har markerats som hämtas i schemat ingår i projektionen.

`api-version=[string]`(krävs). Förhandsversionen är `api-version=2015-02-28-Preview`. Se [Sök Versionhantering](http://msdn.microsoft.com/library/azure/dn864560.aspx) information och alternativa versioner.

Obs: för den här åtgärden på `api-version` har angetts som en frågeparameter.

**Huvuden för begäran**

I följande lista beskrivs de obligatoriska och valfria begärandehuvudena.

* `api-key`: `api-key` Används för att autentisera begäran om att din söktjänst. Det är ett strängvärde som unik för din tjänst-URL. Den **sökning dokumentet** begäran kan ange en administrationsnyckeln eller Frågenyckeln för `api-key`.

Du måste också tjänstnamnet att konstruera den begärda Webbadressen. Du kan hämta namnet på tjänsten och `api-key` från tjänsten instrumentpanelen i Azure Portal. Se [skapa en Azure Search-tjänst i portalen](search-create-service-portal.md) sidan navigering hjälp.

**Begärandetexten**

Ingen.

**Svar**

Statuskod: 200 OK returneras för ett lyckat svar.

    {
      field_name: field_value (fields matching the default or specified projection)
    }

**Exempel**

Sökning dokument har nyckel '2'

    GET /indexes/hotels/docs/2?api-version=2015-02-28-Preview

Dokumentet som har nyckeln ”3” med hjälp av syntaxen för OData-sökning:

    GET /indexes('hotels')/docs('3')?api-version=2015-02-28-Preview

<a name="CountDocs"></a>

## <a name="count-documents"></a>Antal dokument
Den **antalet dokument** åtgärden hämtas en uppräkning av antalet dokument i en sökindex. Den `$count` syntax är en del av OData-protokollet.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin or query key]

**Förfrågan**

HTTPS krävs för tjänstbegäranden. Den **antalet dokument** begäran kan konstrueras med GET-metoden.

[Index name] i URI-begäran talar om tjänsten för att returnera en uppräkning av alla objekt i samlingen docs för det angivna indexet.

`api-version=[string]`(krävs). Förhandsversionen är `api-version=2015-02-28-Preview`. Se [Sök Versionhantering](http://msdn.microsoft.com/library/azure/dn864560.aspx) information och alternativa versioner.

**Huvuden för begäran**

I följande lista beskrivs de obligatoriska och valfria begärandehuvudena.

* `Accept`: Det här värdet måste anges till `text/plain`.
* `api-key`: `api-key` Används för att autentisera begäran om att din söktjänst. Det är ett strängvärde som unik för din tjänst-URL. Den **antalet dokument** begäran kan ange en administrationsnyckeln eller Frågenyckeln för `api-key`.

Du måste också tjänstnamnet att konstruera den begärda Webbadressen. Du kan hämta namnet på tjänsten och `api-key` från tjänsten instrumentpanelen i Azure Portal. Se [skapa en Azure Search-tjänst i portalen](search-create-service-portal.md) sidan navigering hjälp.

**Begärandetexten**

Ingen.

**Svar**

Statuskod: 200 OK returneras för ett lyckat svar.

Svarstexten innehåller värdet för antal som ett heltal som är formaterad med oformaterad text.

<a name="Suggestions"></a>

## <a name="suggestions"></a>Förslag
Den **förslag** åtgärden hämtar förslag baserat på partiell sökning indata. Den används vanligtvis i sökrutorna för att tillhandahålla typ-ahead förslag som användare skriver in söktermer.

Förslag begäranden sträva efter tyder på måldokument så att den föreslagna texten upprepas om flera kandidatdokument matchar samma indata. Du kan använda `$select` att hämta andra dokumentfält (inklusive dokumentnyckeln) så att du kan se vilka dokumentet är källa för varje förslag.

En **förslag** utfärdas igen som en GET eller POST-begäran.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/suggest?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**När du ska använda en POST i stället för att hämta**

När du använder HTTP GET för att anropa den **förslag** API, du måste vara medveten om att längden på den begärda Webbadressen inte får överskrida 8 KB. Detta är vanligtvis tillräckligt för de flesta program. Men genererar vissa program mycket stora frågor, speciellt OData filteruttryck. För dessa program är med hjälp av HTTP POST ett bättre alternativ eftersom det tillåter filter med större än GET. Med POST är antalet satser i ett filter begränsande faktorn inte storleken på raw Filtersträngen eftersom begäran storleksgränsen för POST är ungefär 16 MB.

> [!NOTE]
> Även om storleksgränsen för POST-begäran är väldigt stor får inte filteruttryck vara godtyckligt komplexa. Se [OData-uttryckssyntax](https://msdn.microsoft.com/library/dn798921.aspx) för mer information om begränsningar för filter komplexitet.
> 
> 

**Förfrågan**

HTTPS krävs för tjänstbegäranden. Den **förslag** begäran kan konstrueras med metoderna GET eller POST.

URI-begäran anger namnet på indexet för frågan. Parametrar, till exempel delvis inkommande sökvillkor har angetts i frågesträngen när det gäller GET-begäranden och i begäran brödtext för POST-begäranden.

Ett bra tips när du skapar GET-begäranden, Kom ihåg att [URL-koda](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) särskilda frågeparametrar vid anrop av REST API direkt. För **förslag** åtgärder, detta omfattar:

* `$filter`
* `highlightPreTag`
* `highlightPostTag`
* `search`

URL-kodning rekommenderas endast på ovanstående Frågeparametrar. Om du av misstag URL-koda hela frågesträngen (allt efter den?), begäranden bryts.

Dessutom krävs URL-kodning bara vid anrop av REST API: et direkt med GET. Ingen URL-kodning krävs när du anropar **förslag** med POST, eller när du använder den [.NET-klientbibliotek](https://msdn.microsoft.com/library/dn951165.aspx), som hanterar URL-kodning för dig.

**Frågeparametrar**

**Förslag** accepterar flera parametrar som tillhandahåller frågevillkor och även ange sökbeteendet. Du anger dessa parametrar i URL: en frågesträng vid anrop av **förslag** via GET och som JSON-egenskaper i frågans brödtext vid anrop av **förslag** via POST. Syntaxen för vissa parametrar skiljer mellan GET och POST. Skillnaderna beskrivs i tillämpliga fall nedan:

`search=[string]`-text för sökning att använda att föreslå frågor. Måste vara minst 1 tecken och högst 100 tecken.

`highlightPreTag=[string]`(valfritt) – en sträng tagg som läggs om du vill söka träffar. Måste anges med `highlightPostTag`.

> [!NOTE]
> När du anropar **förslag** använder GET, reserverade tecken i URL-Adressen måste vara procent-kodat (till exempel % 23 i stället för #).
> 
> 

`highlightPostTag=[string]`(valfritt) – en sträng tagg som lägger till om du vill söka träffar. Måste anges med `highlightPreTag`.

> [!NOTE]
> När du anropar **förslag** använder GET, reserverade tecken i URL-Adressen måste vara procent-kodat (till exempel % 23 i stället för #).
> 
> 

`suggesterName=[string]`-namnet på förslagsställarens som anges i den `suggesters` samling som är en del av indexdefinitionen. En `suggester` bestämmer vilka fält som genomsöks för föreslagna sökord. Se [Suggesters](#Suggesters) mer information.

`fuzzy=[boolean]`(valfritt, som standard = false)-om värdet är true detta API hittar förslag även om det finns en saknas eller ersatta tecken i söktexten. Detta ger en bättre upplevelse i vissa fall gäller det vid en prestandakostnad eftersom fuzzy förslag sökningar är långsammare och förbruka mer resurser.

`searchFields=[string]`(valfritt) – lista över kommaavgränsade fältnamn att söka efter den angivna texten. Målfält måste vara aktiverat för förslag.

`$top=#`(valfritt, som standard = 5)-antal förslag för att hämta. Måste vara ett tal mellan 1 och 100.

> [!NOTE]
> När du anropar **förslag** med efter den här parametern heter `top` i stället för `$top`.
> 
> 

`$filter=[string]`(valfritt) - uttryck som filtrerar dokument anses vara förslag.

> [!NOTE]
> När du anropar **förslag** med efter den här parametern heter `filter` i stället för `$filter`.
> 
> 

`$orderby=[string]`(valfritt) – en lista över CSV-uttryck för att sortera resultaten efter. Varje uttryck kan vara ett fältnamn eller ett anrop till den `geo.distance()` funktion. Varje uttryck kan följas av `asc` anges stigande ordning och `desc` att ange fallande. Standardvärdet är stigande ordning. Det finns en gräns på 32-satser för `$orderby`.

> [!NOTE]
> När du anropar **förslag** med efter den här parametern heter `orderby` i stället för `$orderby`.
> 
> 

`$select=[string]`(valfritt) – en lista över kommaavgränsade fält för att hämta. Om inget returneras endast dokument nyckel och förslag text. Du kan uttryckligen begära alla fält genom att ange den här parametern `*`.

> [!NOTE]
> När du anropar **förslag** med efter den här parametern heter `select` i stället för `$select`.
> 
> 

`minimumCoverage`(valfritt, 80 som standard) – ett tal mellan 0 och 100 som anger procentandelen av det index som måste omfattas av en fråga förslag för att frågan ska rapporteras som lyckas. Som standard minst 80% av indexet måste vara tillgängliga eller `Suggest` returneras HTTP-statuskoden 503. Om du ställer in `minimumCoverage` och `Suggest` lyckas den ska returnera HTTP 200 och innehålla en `@search.coverage` värde i svaret som anger procentandelen av det index som ingick i frågan.

> [!NOTE]
> Ange den här parametern till ett värde som är lägre än 100 kan vara användbart för att söka efter tillgänglighet även för tjänster med bara en replik. Inga matchande förslag är dock garanterat måste finnas i resultaten. Om återkallning är viktigare för ditt program än tillgänglighet, så rekommenderas inte att sänka `minimumCoverage` nedan standardvärdet 80.
> 
> 

`api-version=[string]`(krävs). Förhandsversionen är `api-version=2015-02-28-Preview`. Se [Sök Versionhantering](http://msdn.microsoft.com/library/azure/dn864560.aspx) information och alternativa versioner.

Obs: för den här åtgärden den `api-version` har angetts som en frågeparameter i URL: en oavsett om du anropar **förslag** med GET eller POST.

**Huvuden för begäran**

I följande lista beskrivs de obligatoriska och valfria begärandehuvudena

* `api-key`: `api-key` Används för att autentisera begäran om att din söktjänst. Det är ett strängvärde som unik för din tjänst-URL. Den **förslag** begäran kan ange en administrationsnyckeln eller Frågenyckeln som den `api-key`.

Du måste också tjänstnamnet att konstruera den begärda Webbadressen. Du kan hämta namnet på tjänsten och `api-key` från tjänsten instrumentpanelen i Azure Portal. Se [skapa en Azure Search-tjänst i portalen](search-create-service-portal.md) sidan navigering hjälp.

**Begärandetexten**

För GET: Ingen.

För POST:

    {
      "filter": "odata_filter_expression",
      "fuzzy": true | false (default),
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 80),
      "orderby": "orderby_expression",
      "search": "partial_search_input",
      "searchFields": "field_name_1, field_name_2, ...",
      "select": "field_name_1, field_name_2, ...",
      "suggesterName": "suggester_name",
      "top": # (default 5)
    }

**Svar**

Statuskod: 200 OK returneras för ett lyckat svar.

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

Om alternativet projektion används för att hämta de ingår i varje element i matrisen fält:

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

**Exempel**

Hämta 5 förslag där partiell sökning-indata är 'lux'

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&suggesterName=sg&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/suggest?api-version=2015-02-28-Preview
    {
      "search": "lux",
      "top": 5,
      "suggesterName": "sg"
    }
