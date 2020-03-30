---
title: Öka sökrankningen med hjälp av bedömningsprofiler
titleSuffix: Azure Cognitive Search
description: Öka sökresultaten för sökterrang för Azure Cognitive Search-resultat genom att lägga till poängprofiler.
manager: nitinme
author: Brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/28/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 516637b812afece1966006ce6d894dd1e32e6293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245465"
---
# <a name="add-scoring-profiles-to-an-azure-cognitive-search-index"></a>Lägga till rankningsprofiler i ett Azure Cognitive Search-index

  Poängsättning avser beräkning av en *sökpoäng* för varje objekt som returneras i sökresultaten. Poängen är en indikator för ett objekts relevans i kontexten för den aktuella sökåtgärden. Ju högre poäng, desto mer relevant är objektet. I sökresultaten rangordnas objekt från högt till lågt, baserat på de sökpoäng som beräknas för varje objekt.  

 Azure Cognitive Search använder standardbedömning för att beräkna en första poäng, men du kan anpassa beräkningen via en *bedömningsprofil*. Poängsättningsprofiler ger dig större kontroll över rangordningen av objekt i sökresultaten. Du kanske till exempel vill öka artiklar baserat på deras intäktspotential, befordra nyare artiklar eller kanske öka artiklar som har varit i lager för länge.  

 En bedömningsprofil är en del av indexdefinitionen, som består av viktade fält, funktioner och parametrar.  

 För att ge dig en uppfattning om hur en poängprofil ser ut visar följande exempel en enkel profil med namnet "geo". Den här ökar objekt som har söktermen i **fältet hotelName.** Den använder `distance` också funktionen för att gynna objekt som ligger inom tio kilometer från den aktuella platsen. Om någon söker på termen "inn" och "inn" råkar vara en del av hotellets namn, dokument som innehåller hotell med "inn" inom en 10 KM radie från den aktuella platsen kommer att visas högre i sökresultaten.  


```json
"scoringProfiles": [
  {  
    "name":"geo",
    "text": {  
      "weights": {  
        "hotelName": 5
      }                              
    },
    "functions": [
      {  
        "type": "distance",
        "boost": 5,
        "fieldName": "location",
        "interpolation": "logarithmic",
        "distance": {
          "referencePointParameter": "currentLocation",
          "boostingDistance": 10
        }                        
      }                                      
    ]                     
  }            
]
```  


 Om du vill använda den här bedömningsprofilen formuleras frågan för att ange profilen på frågesträngen. I frågan nedan lägger `scoringProfile=geo` du märke till frågeparametern i begäran.  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2019-05-06 
```  

 Den här frågan söker på termen "inn" och passerar på den aktuella platsen. Observera att den här frågan `scoringParameter`innehåller andra parametrar, till exempel . Frågeparametrar beskrivs i [sökdokument &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).  

 Klicka på [Exempel](#bkmk_ex) om du vill granska ett mer detaljerat exempel på en poängprofil.  

## <a name="what-is-default-scoring"></a>Vad är standardbedömning?  
 Poängsättning beräknar en sökpoäng för varje objekt i en rang ordnad resultatuppsättning. Varje objekt i en sökresultatuppsättning tilldelas en sökresultatpoäng och rankas sedan högst till lägst. Objekt med högre poäng returneras till programmet. Som standard returneras de översta 50, men `$top` du kan använda parametern för att returnera ett mindre eller större antal artiklar (upp till 1 000 i ett enda svar).  

Sökresultatet beräknas baserat på statistiska egenskaper för data och frågan. Azure Cognitive Search hittar dokument som innehåller söktermerna i frågesträngen (vissa eller alla, beroende på), `searchMode`som gynnar dokument som innehåller många instanser av söktermen. Sökpoängen ökar ännu högre om termen är sällsynt i dataindexet, men vanligt i dokumentet. Grunden för denna metod för beräkning relevans kallas [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) eller term frekvens-omvänd dokumentfrekvens.  

 Förutsatt att det inte finns någon anpassad sortering rangordnas resultaten sedan efter sökpoäng innan de returneras till det anropande programmet. Om $top inte anges returneras 50 objekt med den högsta sökpoängen.  

 Värden för sökresultat kan upprepas under en resultatuppsättning. Du kan till exempel ha 10 objekt med poängen 1,2, 20 objekt med poängen 1,0 och 20 objekt med poängen 0,5. När flera träffar har samma sökpoäng definieras inte ordningen på samma poängsatta objekt och är inte stabil. Kör frågan igen och du kan se objekt skiftposition. Med tanke på två objekt med en identisk poäng, finns det ingen garanti för vilken som visas först.  

## <a name="when-to-use-custom-scoring"></a>När du ska använda anpassad bedömning  
 Du bör skapa en eller flera bedömningsprofiler när standardrankningsbeteendet inte går tillräckligt långt för att uppfylla dina affärsmål. Du kan till exempel bestämma att sökrelevans ska gynna nyligen tillagda objekt. På samma sätt kan du ha ett fält som innehåller vinstmarginal eller något annat fält som anger intäktspotential. Att öka träffar som ger fördelar för ditt företag kan vara en viktig faktor för att besluta att använda poängprofiler.  

 Relevansbaserad beställning implementeras också genom poängprofiler. Överväg sökresultatsidor som du har använt tidigare och som gör att du kan sortera efter pris, datum, betyg eller relevans. I Azure Cognitive Search driver bedömningsprofiler alternativet "relevans". Definitionen av relevans styrs av dig, bygger på affärsmål och vilken typ av sökupplevelse du vill leverera.  

##  <a name="example"></a><a name="bkmk_ex"></a>Exempel  
 Som nämnts tidigare implementeras anpassad poängsättning via en eller flera bedömningsprofiler som definierats i ett indexschema.  

 I det här exemplet visas schemat för`boostGenre` `newAndHighlyRated`ett index med två bedömningsprofiler ( , ). Alla frågor mot det här indexet som innehåller någon av profilerna som frågeparameter använder profilen för att göra mål i resultatuppsättningen.  

```json
{  
  "name": "musicstoreindex",  
  "fields": [  
    { "name": "key", "type": "Edm.String", "key": true },  
    { "name": "albumTitle", "type": "Edm.String" },  
    { "name": "albumUrl", "type": "Edm.String", "filterable": false },  
    { "name": "genre", "type": "Edm.String" },  
    { "name": "genreDescription", "type": "Edm.String", "filterable": false },  
    { "name": "artistName", "type": "Edm.String" },  
    { "name": "orderableOnline", "type": "Edm.Boolean" },  
    { "name": "rating", "type": "Edm.Int32" },  
    { "name": "tags", "type": "Collection(Edm.String)" },  
    { "name": "price", "type": "Edm.Double", "filterable": false },  
    { "name": "margin", "type": "Edm.Int32", "retrievable": false },  
    { "name": "inventory", "type": "Edm.Int32" },  
    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "boostGenre",  
      "text": {  
        "weights": {  
          "albumTitle": 1.5,  
          "genre": 5,  
          "artistName": 2  
        }  
      }  
    },  
    {  
      "name": "newAndHighlyRated",  
      "functions": [  
        {  
          "type": "freshness",  
          "fieldName": "lastUpdated",  
          "boost": 10,  
          "interpolation": "quadratic",  
          "freshness": {  
            "boostingDuration": "P365D"  
          }  
        },  
        {
          "type": "magnitude",  
          "fieldName": "rating",  
          "boost": 10,  
          "interpolation": "linear",  
          "magnitude": {  
            "boostingRangeStart": 1,  
            "boostingRangeEnd": 5,  
            "constantBoostBeyondRange": false  
          }  
        }  
      ]  
    }  
  ],  
  "suggesters": [  
    {  
      "name": "sg",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": [ "albumTitle", "artistName" ]  
    }  
  ]   
}  
```  

## <a name="workflow"></a>Arbetsflöde  
 Om du vill implementera anpassat bedömningsbeteende lägger du till en bedömningsprofil i schemat som definierar indexet. Du kan ha upp till 100 bedömningsprofiler inom ett index (se [Tjänstgränser),](search-limits-quotas-capacity.md)men du kan bara ange en profil i taget i en viss fråga.  

 Börja med [mallen](#bkmk_template) som finns i det här avsnittet.  

 Ange ett namn. Poängsättningsprofiler är valfria, men om du lägger till en krävs namnet. Var noga med att följa namngivningskonventionerna för fält (börjar med en bokstav, undviker specialtecken och reserverade ord). Se [Namngivningsregler &#40;Azure Cognitive Search&#41;](https://docs.microsoft.com/rest/api/searchservice/naming-rules) för hela listan.  

 Kroppen i poängprofilen är konstruerad utifrån viktade fält och funktioner.  

|||  
|-|-|  
|**Vikter**|Ange namnvärdespar som tilldelar en relativ vikt till ett fält. I fälten [Exempel](#bkmk_ex), albumTitle, genre och artistName marknadsförs 1,5, 5 respektive 2. Varför förstärks genren så mycket högre än de andra? Om sökningen utförs över data som är något homogen (vilket `musicstoreindex`är fallet med "genre" i ), kan du behöva en större varians i de relativa vikterna. Till exempel i `musicstoreindex`" rock' visas som både en genre och i identiskt formulerade genre beskrivningar. Om du vill att genren ska väga tyngre än genrebeskrivning, kommer genrefältet behöver en mycket högre relativ vikt.|  
|**Funktioner**|Används när ytterligare beräkningar krävs för specifika kontexter. Giltiga värden `freshness` `magnitude`är `distance`, `tag`, och . Varje funktion har parametrar som är unika för den.<br /><br /> -   `freshness`ska användas när du vill öka med hur nytt eller gammalt ett objekt är. Den här funktionen kan `datetime` endast användas med fält (edm. DataTimeOffset). Observera `boostingDuration` att attributet endast `freshness` används med funktionen.<br />-   `magnitude`bör användas när du vill öka baserat på hur högt eller lågt ett numeriskt värde är. Scenarier som kräver den här funktionen inkluderar öka med vinstmarginal, högsta pris, lägsta pris eller ett antal nedladdningar. Den här funktionen kan endast användas med dubbel- och heltalsfält.<br />     För `magnitude` funktionen kan du vända intervallet, högt till lågt, om du vill ha det omvända mönstret (till exempel för att öka billigare objekt mer än dyrare objekt). Med tanke på en rad priser från $ 100 till $ 1, skulle du ställa in `boostingRangeStart` på 100 och `boostingRangeEnd` 1 för att öka billigare objekt.<br />-   `distance`användas när du vill öka genom närhet eller geografisk plats. Den här funktionen kan `Edm.GeographyPoint` bara användas med fält.<br />-   `tag`bör användas när du vill öka med taggar gemensamt mellan dokument och sökfrågor. Den här funktionen kan `Edm.String` `Collection(Edm.String)` bara användas med och fält.<br /><br /> **Regler för användning av funktioner**<br /><br /> Funktionstypen `magnitude` `distance`( `tag` `freshness`, , ) måste vara gemener.<br /><br /> Funktioner kan inte innehålla null- eller tomma värden. Om du inkluderar fältnamn måste du ställa in det på något.<br /><br /> Funktioner kan endast användas på filterbara fält. Se [Skapa index &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index) för mer information om filterbara fält.<br /><br /> Funktioner kan endast användas på fält som definieras i fältsamlingen för ett index.|  

 När indexet har definierats skapar du indexet genom att ladda upp indexschemat, följt av dokument. Se [Skapa index &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index) och Lägga [till, uppdatera eller ta bort dokument &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) för instruktioner om dessa åtgärder. När indexet har skapats bör du ha en funktionell bedömningsprofil som fungerar med dina sökdata.  

##  <a name="template"></a><a name="bkmk_template"></a>Mall  
 I det här avsnittet visas syntaxen och mallen för bedömningsprofiler. Se [Indexattributreferens](#bkmk_indexref) i nästa avsnitt för beskrivningar av attributen.  

```  
. . .   
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
        }  

        // ( - or -)  

        "freshness": {
          "boostingDuration": "..." (value representing timespan over which boosting occurs)   
        }  

        // ( - or -)  

        "distance": {
          "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)   
          "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)   
        }   

        // ( - or -)  

        "tag": {
          "tagsParameter":  "..."(parameter to be passed in queries to specify a list of tags to compare against target field)   
        }
      }
    ],   
    "functionAggregation": (optional, applies only when functions are specified) "sum (default) | average | minimum | maximum | firstMatching"   
  }   
],   
"defaultScoringProfile": (optional) "...",   
. . .  
```  

##  <a name="index-attributes-reference"></a><a name="bkmk_indexref"></a>Referens för indexattribut  

> [!NOTE]  
>  En bedömningsfunktion kan bara användas på fält som är filterbara.  

|Attribut|Beskrivning|  
|---------------|-----------------|  
|`name`|Krävs. Det här är namnet på poängprofilen. Den följer samma namngivningskonventioner för ett fält. Det måste börja med en bokstav, kan inte innehålla punkter, kolon eller @ symboler, och kan inte börja med frasen "azureSearch" (skiftlägeskänslig).|  
|`text`|Innehåller egenskapen vikter.|  
|`weights`|Valfri. Innehåller namnvärdespar som var och en anger ett fältnamn och en relativ vikt. Relativ vikt måste vara ett positivt heltal eller flyttalsnummer.<br /><br /> Vikter används för att ange vikten av ett sökbart fält i förhållande till ett annat.|  
|`functions`|Valfri. En bedömningsfunktion kan bara användas på fält som är filterbara.|  
|`type`|Krävs för poängfunktioner. Anger vilken typ av funktion som ska användas. Giltiga värden inkluderar magnitud, friskhet, avstånd och tagg. Du kan inkludera mer än en funktion i varje poängprofil. Funktionsnamnet måste vara gemen.|  
|`boost`|Krävs för poängfunktioner. Ett positivt tal som används som multiplikator för råpoäng. Det kan inte vara lika med 1.|  
|`fieldname`|Krävs för poängfunktioner. En bedömningsfunktion kan bara användas på fält som ingår i fältsamlingen i indexet och som är filterbara. Dessutom introducerar varje funktionstyp ytterligare begränsningar (färskhet används med datumfält, storlek med heltal eller dubbla fält och avstånd med platsfält). Du kan bara ange ett enskilt fält per funktionsdefinition. Om du till exempel vill använda magnitud två gånger i samma profil måste du inkludera två definitioner magnitud, en för varje fält.|  
|`interpolation`|Krävs för poängfunktioner. Definierar lutningen för vilken poängökningen ökar från början av intervallet till slutet av intervallet. Giltiga värden är linjär (standard), Konstant, Kvadratisk och Logaritmisk. Mer information [finns i Ange interpolationer.](#bkmk_interpolation)|  
|`magnitude`|Funktionen magnitudbedömning används för att ändra rangordningar baserat på värdeintervallet för ett numeriskt fält. Några av de vanligaste användningsexempelna på detta är:<br /><br /> -   **Stjärnor:** Ändra poängsättningen baserat på värdet i fältet "Stjärnklassificering". När två objekt är relevanta visas objektet med den högre klassificeringen först.<br />-   **Marginal:** När två dokument är relevanta kan en återförsäljare vilja marknadsföra dokument som har högre marginaler först.<br />-   **Klicka räknas:** För program som spårar klickning till åtgärder till produkter eller sidor kan du använda magnitud för att öka objekt som tenderar att få mest trafik.<br />-   **Nedladdning räknas:** För program som spårar nedladdningar kan du med funktionen Magnitud öka objekt som har flest nedladdningar.|  
|`magnitude`&#124;`boostingRangeStart`|Anger startvärdet för det område över vilket magnituden är poängsättad. Värdet måste vara ett heltal eller flyttalsnummer. För stjärnbetyg på 1 till 4, skulle detta vara 1. För marginaler över 50%, skulle detta vara 50.|  
|`magnitude`&#124;`boostingRangeEnd`|Anger slutvärdet för det område över vilket magnituden är poängsatt. Värdet måste vara ett heltal eller flyttalsnummer. För stjärnbetyg på 1 till 4, skulle detta vara 4.|  
|`magnitude`&#124;`constantBoostBeyondRange`|Giltiga värden är sanna eller falska (standard). När värdet är true fortsätter den fullständiga förstärkandet att gälla för dokument som har ett värde för målfältet som är högre än den övre delen av intervallet. Om den här funktionen är falsk tillämpas inte förstärkningen av den här funktionen på dokument som har ett värde för målfältet som ligger utanför intervallet.|  
|`freshness`|Bedömningsfunktionen för färskhet används för att ändra `DateTimeOffset` rangordningspoäng för artiklar baserat på värden i fält. Ett objekt med ett nyare datum kan till exempel rangordnas högre än äldre objekt.<br /><br /> Det är också möjligt att rangordna objekt som kalenderhändelser med framtida datum så att objekt närmare nutiden kan rangordnas högre än objekt längre i framtiden.<br /><br /> I den aktuella tjänstutgåvan kommer en ände av intervallet att vara fast på den aktuella tiden. Den andra änden är en tid `boostingDuration`i det förflutna baserat på . Om du vill öka en rad gånger `boostingDuration`i framtiden använder du en negativ .<br /><br /> Den hastighet med vilken de öka ändringarna från ett högsta och lägsta intervall bestäms av interpolationen som tillämpas på poängprofilen (se figuren nedan). Om du vill vända på den ökande faktorn väljer du en boostfaktor på mindre än 1.|  
|`freshness`&#124;`boostingDuration`|Anger en förfalloperiod efter vilken förstärkningen stoppas för ett visst dokument. Se [Ange boostDuration](#bkmk_boostdur) i följande avsnitt för syntax och exempel.|  
|`distance`|Avståndsbedömningsfunktionen används för att påverka poängen för dokument baserat på hur nära eller långt de är i förhållande till en referensgesänk. Referensplatsen anges som en del av frågan i `scoringParameterquery` en parameter (med strängalternativet) som ett lon,lat-argument.|  
|`distance`&#124;`referencePointParameter`|En parameter som ska skickas i frågor som ska användas som referensplats. `scoringParameter`är en frågeparameter. Se [Sökdokument &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) för beskrivningar av frågeparametrar.|  
|`distance`&#124;`boostingDistance`|Ett tal som anger avståndet i kilometer från referensplatsen där det ökande området slutar.|  
|`tag`|Taggbedömningsfunktionen används för att påverka poängen för dokument baserat på taggar i dokument och sökfrågor. Dokument som har taggar som är gemensamma med sökfrågan kommer att marknadsföras. Taggarna för sökfrågan anges som en bedömningsparameter `scoringParameterquery` i varje sökbegäran (med strängalternativet).|  
|`tag`&#124;`tagsParameter`|En parameter som ska skickas i frågor för att ange taggar för en viss begäran. `scoringParameter`är en frågeparameter. Se [Sökdokument &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) för beskrivningar av frågeparametrar.|  
|`functionAggregation`|Valfri. Gäller endast när funktioner anges. Giltiga värden inkluderar: summa (standard), medelvärde, minimum, maximum och firstMatching. En sökpoäng är ett värde som beräknas från flera variabler, inklusive flera funktioner. Det här attributet anger hur ökningarna för alla funktioner kombineras till en enda sammanlagd boost som sedan tillämpas på basdokumentpoängen. Baspoängen baseras på [tf-idf-värdet](http://www.tfidf.com/) som beräknas från dokumentet och sökfrågan.|  
|`defaultScoringProfile`|När du kör en sökbegäran, om ingen poängprofil anges, används standardbedömning (endast[tf-idf).](http://www.tfidf.com/)<br /><br /> Ett standardbedömningsprofilnamn kan anges här, vilket gör att Azure Cognitive Search använder den profilen när ingen specifik profil anges i sökbegäran.|  

##  <a name="set-interpolations"></a><a name="bkmk_interpolation"></a>Ange interpolationer  
 Interpolationer kan du ställa in formen på lutningen som används för poängsättning. Eftersom poängsättningen är hög till låg minskar lutningen alltid, men interpolationen bestämmer kurvan för den nedåtgående lutningen. Följande interpolationer kan användas:  

|||  
|-|-|  
|`linear`|För artiklar som ligger inom intervallet max och min kommer den boost som tillämpas på artikeln att göras i ett ständigt minskande belopp. Linjär är standardinterpolering för en poängprofil.|  
|`constant`|För objekt som ligger inom start- och slutintervallet tillämpas en konstant boost på rangordningsresultaten.|  
|`quadratic`|I jämförelse med en linjär interpolation som har en ständigt minskande uppsving, kommer Kvadratiska inledningsvis minska i mindre takt och sedan när den närmar sig slutområdet, minskar det med ett mycket högre intervall. Det här interpolationsalternativet är inte tillåtet i taggbedömningsfunktioner.|  
|`logarithmic`|I jämförelse med en linjär interpolation som har en ständigt minskande ökning, logaritmiska kommer inledningsvis att minska i högre takt och sedan när den närmar sig slutområdet, minskar det med ett mycket mindre intervall. Det här interpolationsalternativet är inte tillåtet i taggbedömningsfunktioner.|  

 ![Konstant, linjär, kvadratisk, log10 linjer på diagram](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="set-boostingduration"></a><a name="bkmk_boostdur"></a>Ange boostDuration  
 `boostingDuration`är ett attribut `freshness` för funktionen. Du använder den för att ange en förfalloperiod efter vilken öka kommer att stoppas för ett visst dokument. Om du till exempel vill marknadsföra en produktlinje eller ett varumärke för en 10-dagars kampanjperiod anger du 10-dagarsperioden som "P10D" för dessa dokument.  

 `boostingDuration`måste formateras som ett XSD -värde för "dayTimeDuration" (en begränsad delmängd av ett varaktighetsvärde för ISO 8601). Mönstret för detta är: "P[nD][T[nH][nM][nS]]".  

 Följande tabell innehåller flera exempel.  

|Varaktighet|ökaDuration|  
|--------------|----------------------|  
|1 dag|"P1D"|  
|2 dagar och 12 timmar|"P2DT12H"|  
|15 minuter|"PT15M"|  
|30 dagar, 5 timmar, 10 minuter och 6,334 sekunder|"P30DT5H10M6.334s"|  

 Fler exempel finns i [XML-schema: Datatyper (W3.org webbplats)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration).  

## <a name="see-also"></a>Se även  
 [AZURE Kognitiv sökning REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Skapa index &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
 [Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
