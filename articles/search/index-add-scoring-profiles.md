---
title: Lägg till bedömningsprofiler i ett sökindex – Azure Search
description: Öka search rangordning för Azure Search sökresultaten genom att lägga till bedömningsprofiler.
ms.date: 01/31/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: eae7de00294a6a09cb7f942d11ee2391710fc55f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008491"
---
# <a name="add-scoring-profiles-to-an-azure-search-index"></a>Lägg till bedömningsprofiler i en Azure Search-index

  Bedömning refererar till beräkning av en *Sök poäng* för alla objekt som returneras i sökresultaten. Poängen är en indikator för ett objekt relevans i kontexten för den aktuella sökåtgärden. Ju högre poäng, desto viktigare objektet. Objekten är rangordning ordnat från högt till lågt baserat på Sök poängen beräknas för varje objekt i sökresultaten.  

 Azure Search använder standard bedömning för att beräkna en inledande poäng, men du kan anpassa beräkningen via en *bedömningsprofil*. Bedömningsprofiler ger dig större kontroll över rangordning objekt i sökresultaten. Du kanske exempelvis vill öka objekt baserat på deras potentiella intäkter, befordra nyare element eller kanske öka objekt som har varit i lager som är för långt.  

 En bedömningsprofil är en del av den Indexdefinition, består av viktad fält, funktioner och parametrar.  

 För att ge dig en uppfattning om vad en bedömningsprofil ut, visas i följande exempel en enkel profil med namnet ”geo'. Den här ökar objekt som innehåller söktermen i den **hotelName** fält. Den använder också den `distance` funktionen till fördel för objekt som ligger inom tio kilometer för den aktuella platsen. Om någon söker efter termen ”inn' och 'inn' råkar vara en del av namnet hotell, visas dokument som innehåller hotels med inn om du inom en 10 KM radie för den aktuella platsen högre upp i sökresultaten.  


```  
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


 Om du vill använda den här bedömningsprofilen formulerat frågan om du vill ange profilen i frågesträngen. Lägg märke till i frågan nedan Frågeparametern `scoringProfile=geo` i begäran.  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2017-11-11  
```  

 Den här frågan söker efter termen ”inn' och skickar i den aktuella platsen. Observera att den här frågan innehåller andra parametrar som `scoringParameter`. Frågeparametrar beskrivs i [söka efter dokument &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).  

 Klicka på [exempel](#bkmk_ex) att granska ett mer detaljerat exempel på en bedömningsprofil.  

## <a name="what-is-default-scoring"></a>Vad är standard bedömning?  
 Bedömning beräknar en sökpoäng för varje objekt i en rangordnas sorterad resultatuppsättning. Alla objekt i sökresultaten är tilldelad en sökpoäng, så rangordnas högsta till lägsta. Objekt med högre poäng returneras till programmet. Som standard de översta 50 returneras, men du kan använda den `$top` parametern för att returnera ett mindre eller större antal objekt (upp till 1 000 i ett enda svar).  

Sökpoängen beräknas utifrån statistiska egenskaper för data och frågan. Azure Search söker efter dokument som innehåller sökvillkor i frågesträngen (vissa eller alla, beroende på `searchMode`), prioriterar dokument som innehåller många instanser av söktermen. Sökpoängen går upp ännu högre om termen sällsynta mellan data Kristi, men vanliga i dokumentet. Grunden för den här metoden ska databehandling relevans kallas [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) eller termen frekvens inverterade dokumentet frekvens.  

 Anta att det finns ingen anpassad sortering och är resultat sedan rankade efter sökpoäng innan de skickas tillbaka till det anropande programmet. Om $top inte har angetts, returneras 50 artiklar med högsta sökpoängen.  

 Sök poäng värden kan upprepas i en resultatuppsättning. Du kan till exempel ha 10 objekt med ett värde av 1.2, 20 artiklar med en poäng för 1.0 och 20 objekt med en poäng för 0,5. När flera träffar har samma sökpoängen kan sorteringen av samma poängsatta objekt har inte definierats och är inte stabilt. Kör frågan igen, och du kan se objekt byter läge. Med två objekt med en identisk poäng kan finns det ingen garanti för vilken som visas först.  

## <a name="when-to-use-custom-scoring"></a>När du ska använda anpassad bedömning  
 Du bör skapa en eller flera bedömningsprofiler när standard rangordning beteendet inte går mycket tillräckligt med i uppfylla dina affärsmål. Du kan till exempel bestämma att sökrelevans bör prioriterar nyligen tillagda objekt. På samma sätt kan du ha ett fält som innehåller vinstmarginal eller ett annat fält som indikerar potentiella intäkter. Öka träffar som ger fördelar för företaget kan vara en viktig faktor bestämmer använda bedömningsprofiler.  

 Plats-baserade sorteringen genomförs även via poängprofiler. Överväg att söksidor resultat som du har använt tidigare och som kan du sortera efter pris, datum, klassificering eller relevans. I Azure Search hantera bedömningsprofiler alternativet 'relevans'. Definitionen av relevans styrs av dig, förutsätter affärsmål och typen av sökfunktion som du vill leverera.  

##  <a name="bkmk_ex"></a> Exempel  
 Som tidigare nämnts implementeras anpassad bedömning via en eller flera bedömningsprofiler som definierats i ett indexschema.  

 Det här exemplet visar schemat för ett index med två bedömningsprofiler (`boostGenre`, `newAndHighlyRated`). Alla frågor mot indexet som innehåller antingen profilen eftersom en frågeparameter kommer att använda profilen kan bedöma resultatuppsättningen.  

```  
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
 Lägga till en bedömningsprofil i schemat som definierar indexet för att implementera anpassade bedömnings beteende. Du kan ha upp till 100 poängprofiler i ett index (se [tjänstbegränsningar](search-limits-quotas-capacity.md)), men du kan bara ange en profil på gång i en viss fråga.  

 Börja med den [mall](#bkmk_template) i det här avsnittet.  

 Ange ett namn. Bedömningsprofiler är valfria, men om du lägger till en namn krävs. Se till att följa regler för namngivning av fält (startar med en bokstav, undviker specialtecken och reserverade ord). Se [namngivningsregler &#40;Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/naming-rules) för en fullständig lista.  

 Innehållet i bedömningsprofilen konstrueras utifrån viktad fält och funktioner.  

|||  
|-|-|  
|**Vikterna**|Ange namn / värde-par som tilldelar en relativt viktat till ett fält. I den [exempel](#bkmk_ex), albumTitle och genre artistName fälten är bättre 1.5, 5 och 2 respektive. Varför genre förstärks mycket högre än de andra? Om sökningen utförs över data som är något homogen (som är fallet med 'genre ”i den `musicstoreindex`), du kan behöva en större avvikelse i den relativa vikten. Till exempel i den `musicstoreindex`, 'rock ”visas som båda genre och i identiskt fraserats genre beskrivningar. Om du vill att genre överväger genre beskrivning måste fältet genre en mycket högre relativa vikt.|  
|**Funktioner**|Används när det krävs ytterligare beräkningar för specifika sammanhang. Giltiga värden är `freshness`, `magnitude`, `distance`, och `tag`. Varje funktion har parametrar som är unika för den.<br /><br /> -   `freshness` ska användas när du vill öka som hur nya eller gamla ett objekt. Den här funktionen kan bara användas med `datetime` fält (edm. DataTimeOffset). Obs den `boostingDuration` attributet används endast för den `freshness` funktion.<br />-   `magnitude` ska användas när du vill öka baserat på hur hög eller låg är ett numeriskt värde. Scenarier som anropar för den här funktionen är boosting av vinstmarginal, högsta pris, lägsta pris eller antalet nedladdningar. Den här funktionen kan endast användas med double-värde och heltal.<br />     För den `magnitude` funktion, kan du återställa intervallet, högt till lågt, om du vill att inverterade mönstret (till exempel till boost billigare objekt är mer än högre prisvärda objekt). Få en mängd priser från 100 till $1, anger du `boostingRangeStart` 100 och `boostingRangeEnd` på 1 för att öka billigare-objekt.<br />-   `distance` ska användas när du vill öka genom närhet eller geografisk plats. Den här funktionen kan bara användas med `Edm.GeographyPoint` fält.<br />-   `tag` ska användas när du vill öka efter taggar i vanliga mellan dokument och sökfrågor. Den här funktionen kan bara användas med `Edm.String` och `Collection(Edm.String)` fält.<br /><br /> **Regler för användning av funktioner**<br /><br /> Typen (`freshness`, `magnitude`, `distance`), `tag` måste vara versaler.<br /><br /> Functions kan inte innehålla nullvärden eller tomma värden. Mer specifikt om du inkluderar fieldname, måste du välja något.<br /><br /> Functions kan endast tillämpas på filtrerbara fält. Se [Create Index &#40;Azure Search Service REST API&#41; ](https://docs.microsoft.com/rest/api/searchservice/create-index) för mer information om filtrerbara fält.<br /><br /> Functions kan endast tillämpas på fält som definierats i fältsamlingen av ett index.|  

 När indexet har definierats kan du skapa indexet genom att ladda upp indexschemat, följt av dokument. Se [Create Index &#40;Azure Search Service REST API&#41; ](https://docs.microsoft.com/rest/api/searchservice/create-index) och [lägga till, uppdatera eller ta bort dokument &#40;Azure Search Service REST API&#41; ](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) anvisningar för de här åtgärderna. När du har skapat indexet, bör du ha en fungerande bedömningsprofilen som fungerar med dina sökdata.  

##  <a name="bkmk_template"></a> mall  
 Det här avsnittet visas syntax och mallen för poängprofiler. Referera till [Index attributreferens](#bkmk_indexref) i nästa avsnitt finns beskrivningar av attribut.  

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

##  <a name="bkmk_indexref"></a> Index attributreferens  

> [!NOTE]  
>  En bedömningsfunktion kan endast tillämpas på fält som är filtrerbara.  

|Attribut|Beskrivning|  
|---------------|-----------------|  
|`Name`|Krävs. Det här är namnet på bedömningsprofilen. Det följer samma mönster för ett fält. Det måste börja med en bokstav, får inte innehålla punkter, kolon eller @ symboler, och får inte börja med frasen ”azureSearch” (skiftlägeskänsligt).|  
|`Text`|Innehåller egenskapen vikterna.|  
|`Weights`|Valfri. Ett namn-värde-par som anger ett fältnamn och relativa viktade. Relativa viktade måste vara ett positivt heltal eller flyttal. Det högsta värdet är int32. MaxValue.<br /><br /> Du kan ange fältnamn utan en motsvarande vikt. Vikterna används för att visa ett fält i förhållande till en annan viktig.|  
|`Functions`|Valfri. Observera att en bedömningsfunktion endast kan tillämpas på fält som är filtrerbara.|  
|`Type`|Krävs för bedömningsfunktioner funktioner. Anger vilken typ av funktion som ska användas. Giltiga värden är omfattning, färskhet, avstånd och tagg. Du kan inkludera mer än en funktion i varje bedömningsprofil. Funktionsnamnet måste vara versaler.|  
|`Boost`|Krävs för bedömningsfunktioner funktioner. Ett positivt tal som används som multiplikator för. Det får inte vara lika med 1.|  
|`Fieldname`|Krävs för bedömningsfunktioner funktioner. En bedömningsfunktion kan endast tillämpas på fält som ingår i samlingen fält i indexet och som är filtrerbara. Dessutom kan introducerar varje funktionstyp ytterligare begränsningar (färskhet används med datetime fält, omfattning med heltal eller dubbel fält och avståndet, med Platsfält). Du kan bara ange ett enskilt fält per funktionsdefinitionen. Om du vill använda magnitude två gånger i samma profil, skulle du behöva omfattar två definitioner omfattning, en för varje fält.|  
|`Interpolation`|Krävs för bedömningsfunktioner funktioner. Definierar lutning för vilken poängförstärkningen ökar, från början av intervallet i slutet av intervallet. Giltiga värden är linjär (standard), konstant, andragradsekvation och logaritmisk. Se [ange interpolations](#bkmk_interpolation) mer information.|  
|`magnitude`|Magnitude bedömning funktion används för att ändra rangordningar baserat på värdeintervallet för ett numeriskt fält. Några av de vanligaste användningsexempel på detta är:<br /><br /> -   **Stjärnklassificering:** Ändra bedömning baserat på värdet i fältet ”Star omdöme”. När två objekt är relevant, visas objekt med högre omdömet först.<br />-   **Marginal:** När det gäller två dokument, kanske en återförsäljare vill öka dokument som har högre marginaler först.<br />-   **Klicka på antalet:** För program som spårar klicka dig igenom åtgärder för att produkter eller sidor, kan du använda magnitude boost-objekt som tenderar att få ut det mesta trafik.<br />-   **Ladda ned antalet:** För program att spåra nedladdningar, de magnitude funktionen kan du öka objekt som har mest nedladdningar.|  
|`magnitude` &#124; `boostingRangeStart`|Anger startvärdet för intervallet över vilket omfattning beräknas. Värdet måste vara ett heltal eller flyttal. Detta kan vara 1 för stjärnklassificering mellan 1 och 4. Detta kan vara 50 för marginal över 50%.|  
|`magnitude` &#124; `boostingRangeEnd`|Anger slutvärdet för intervallet över vilket omfattning beräknas. Värdet måste vara ett heltal eller flyttal. Detta kan vara 4 för stjärnklassificering mellan 1 och 4.|  
|`magnitude` &#124; `constantBoostBeyondRange`|Giltiga värden är SANT eller FALSKT (standard). Om värdet är sant, i hela förstärkningen ska tillämpas på dokument som har ett värde för målfältet som är högre än den övre delen av intervallet. Om värdet är FALSKT, tillämpas inte förstärkningen på på dokument med ett värde för målfältet som ligger utanför intervallet.|  
|`freshness`|Färskhet bedömning funktion används för att ändra poängrangordning för objekt baserat på värdena i `DateTimeOffset` fält. Till exempel kan ett objekt med ett datum vara räknas högre än äldre objekt.<br /><br /> Observera att det också möjligt att rangordnas objekt och Kalender-händelser med framtida datum så att objekt närmare aktuella kan tilldelas en högre än objekt ytterligare i framtiden.<br /><br /> I den aktuella versionen av tjänsten korrigeras ena änden av intervallet till den aktuella tiden. Den andra änden är taget tidigare baserat på den `boostingDuration`. För att höja flera gånger i framtiden, använder du en negativ `boostingDuration`.<br /><br /> Den hastighet som den boosting ändras från maximalt och minimalt intervall bestäms av interpolering tillämpas på bedömningsprofilen (se bilden nedan). Välj en boost faktor på mindre än 1 om du vill ändra felkällorna faktor som tillämpas.|  
|`freshness` &#124; `boostingDuration`|Anger en utgångsperiod efter vilken förstärkning tar slut för ett visst dokument. Se [ange boostingDuration](#bkmk_boostdur) i följande avsnitt innehåller syntax och exempel.|  
|`distance`|Avståndet bedömningsfunktion används för att påverka poängsättningen för dokument baserat på hur Stäng eller långt ifrån de är i förhållande till en geografisk referensplats. Referensplatsen ges som en del av frågan i en parameter (med hjälp av den `scoringParameterquery` sträng alternativet) som en celligt lat argumentet.|  
|`distance` &#124; `referencePointParameter`|En parameter som ska överföras i frågor som ska användas som referensplats. `scoringParameter` är en frågeparameter. Se [söka efter dokument &#40;Azure Search Service REST API&#41; ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) beskrivningar av Frågeparametrar.|  
|`distance` &#124; `boostingDistance`|Ett tal som anger avståndet i kilometer, från referensplatsen där förstärkningsintervallet slutar.|  
|`tag`|Taggen bedömning funktion används för att påverka poängsättningen för dokument baserat på taggar i dokument och sökfrågor. Att kommer höja dokument som innehåller taggar är vanlig för sökfrågan. Taggar för sökfrågan tillhandahålls som en arbetsflödesbaserad parameter i varje sökbegäran (med hjälp av den `scoringParameterquery` sträng alternativet).|  
|`tag` &#124; `tagsParameter`|En parameter som ska överföras i frågor för att specificera taggar för en viss begäran. `scoringParameter` är en frågeparameter. Se [söka efter dokument &#40;Azure Search Service REST API&#41; ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) beskrivningar av Frågeparametrar.|  
|`functionAggregation`|Valfri. Gäller endast när funktionerna har angetts. Giltiga värden är: sum (standard), medelvärde, lägsta, högsta och firstMatching. En sökpoäng är enda värde som beräknas från flera variabler, inklusive flera funktioner. Det här attributet anger hur ökar för alla funktioner kombineras till en enskild samling öka som sedan tillämpas på den grundläggande dokument poängen. Den grundläggande poängen baseras på den [tf-idf](http://www.tfidf.com/) värde beräknas från dokumentet och sökfrågan.|  
|`defaultScoringProfile`|När du kör en sökbegäran om ingen bedömningsprofil anges, kommer standardvärdet bedömning används ([tf-idf](http://www.tfidf.com/) endast).<br /><br /> Standard bedömning profilnamn kan anges här kan orsaka Azure Search för att använda profilen när ingen specifik profil anges i sökbegäran.|  

##  <a name="bkmk_interpolation"></a> Ställ in interpolations  
 Interpolations kan du ange formen på lutningen används för bedömning. Eftersom bedömning är högt till lågt, lutningen minskar alltid, men interpolering bestämmer kurvan för nedåtgående lutningen. Du kan använda följande interpolations:  

|||  
|-|-|  
|`Linear`|För objekt som ligger inom intervallet maximalt och minimalt antal görs boost som kopplats till artikeln inom en ständigt minskar. Linjär är standard interpolation för en bedömningsprofil.|  
|`Constant`|Objekt som är i början och slut intervall, tillämpas en konstant förstärkning på rangordnas resultaten.|  
|`Quadratic`|Jämförelse med en linjär interpolation som har en ständigt minskar boost andragradsekvation sänks från början i mindre takt och sedan när det närmar sig slutintervallet minskas med ett mycket högre intervall. Det här alternativet om interpolering tillåts inte i taggen bedömning funktioner.|  
|`Logarithmic`|Jämförelse med en linjär interpolation som har en ständigt minskar boost logaritmisk sänks från början i högre takt och sedan när det närmar sig slutintervallet minskas med ett mycket mindre intervall. Det här alternativet om interpolering tillåts inte i taggen bedömning funktioner.|  

 ![Konstant, linjär, kvadratisk, log10 linjerna i diagrammet](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="bkmk_boostdur"></a> Ställ in boostingDuration  
 `boostingDuration` är ett attribut för den `freshness` funktion. Du kan använda den för att ange en giltighetstid period efter då detta stoppas för ett visst dokument. Om du vill öka en produktserie eller varumärke för en kampanjperiod på 10 dagar, skulle du till exempel ange de 10 dagarna som ”P10D” för dessa dokument.  

 `boostingDuration` måste vara formaterad som ett XSD ”dayTimeDuration”-värde (en begränsad delmängd av en ISO 8601-varaktighetsvärde). Mönstret för det här är: ”P [nD] [T [nH] [nM] [nS]]”.  

 Följande tabell innehåller flera exempel.  

|Varaktighet|boostingDuration|  
|--------------|----------------------|  
|1 dag|"P1D"|  
|2 dagar och 12 timmar|"P2DT12H"|  
|15 minuter|"PT15M"|  
|30 dagar, 5 timmar, 10 minuter och 6.334 sekunder|"P30DT5H10M6.334S"|  

 Fler exempel finns i [XML-Schema: Datatyper (W3.org webbplats)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration).  

## <a name="see-also"></a>Se också  
 [Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Skapa Index &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
 [Azure Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
