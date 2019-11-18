---
title: Öka Sök rankningen med hjälp av bedömnings profiler
titleSuffix: Azure Cognitive Search
description: Höj Sök ranknings poängen för Azure Kognitiv sökning resultat genom att lägga till bedömnings profiler.
manager: nitinme
author: Brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: 60442ab101423d0a91fa35a7a12a0b930417af71
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113616"
---
# <a name="add-scoring-profiles-to-an-azure-cognitive-search-index"></a>Lägga till bedömnings profiler i ett Azure Kognitiv sökning-index

  Poängsättning avser beräkningen av en *Sök Poäng* för varje objekt som returneras i Sök resultaten. Poängen är en indikator för ett objekts relevans i kontexten för den aktuella Sök åtgärden. Ju högre poäng, desto mer relevant är objektet. I Sök resultaten rangordnas objekt rang från hög till låg, baserat på Sök resultaten som beräknas för varje objekt.  

 Azure Kognitiv sökning använder standard poängsättning för att beräkna en första poäng, men du kan anpassa beräkningen genom en *bedömnings profil*. Med bedömnings profiler får du bättre kontroll över rankningen av objekt i Sök resultaten. Du kanske till exempel vill öka objekt baserat på deras intäkts potential, befordra nya objekt eller kanske förbättra objekt som har varit i lager för länge.  

 En bedömnings profil är en del av index definitionen, som består av viktade fält, funktioner och parametrar.  

 I följande exempel visas en enkel profil med namnet "Geo" för att ge dig en uppfattning om vad en bedömnings profil ser ut. Den här en stärker objekt som har Sök termen i fältet **hotelName** . Den använder också funktionen `distance` för att prioritera objekt som ligger inom tio kilo meter på den aktuella platsen. Om någon söker på termen "Inn" och "Inn" ska vara en del av hotell namnet, kommer dokument som innehåller hotell med "Inn" inom en radie på 10 KM på den aktuella platsen att visas högre upp i Sök resultaten.  


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


 Om du vill använda den här bedömnings profilen formuleras din fråga för att ange profilen i frågesträngen. I frågan nedan kan du lägga märke till Frågeparametern `scoringProfile=geo` i begäran.  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2019-05-06 
```  

 Den här frågan söker efter termen "Inn" och skickar den aktuella platsen. Observera att frågan innehåller andra parametrar, t. ex. `scoringParameter`. Frågeparametrar beskrivs i [Sök dokument &#40;Azure-kognitiv sökning&#41;REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).  

 Klicka på [exempel](#bkmk_ex) om du vill granska ett mer detaljerat exempel på en bedömnings profil.  

## <a name="what-is-default-scoring"></a>Vad är standard Poängsättning?  
 Poängsättning beräknar en Sök Poäng för varje objekt i en rangordnad resultat uppsättning. Varje objekt i en Sök Resultat uppsättning tilldelas en Sök Poäng och rangordnas sedan högst till lägsta. Objekt med de högre poängen returneras till programmet. De översta 50 returneras som standard, men du kan använda parametern `$top` för att returnera ett mindre eller större antal objekt (upp till 1000 i ett enda svar).  

Sök poängen beräknas baserat på statistiska egenskaper för data och frågan. Azure Kognitiv sökning hittar dokument som innehåller Sök villkoren i frågesträngen (vissa eller alla, beroende på `searchMode`), som prioriterar dokument som innehåller många instanser av Sök termen. Sök poängen går till ännu högre om termen är sällsynt över data indexet, men vanligt i dokumentet. Grunden för den här metoden för att beräkna relevans kallas [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) eller term frekvens – invertering av dokument frekvens.  

 Förutsatt att det inte finns någon anpassad sortering rangordnas resultatet efter Sök poäng innan de returneras till det anropande programmet. Om $top inte anges returneras 50 objekt med högst Sök resultat.  

 Sök Resultat värden kan upprepas i en resultat uppsättning. Du kan till exempel ha 10 objekt med en poäng på 1,2, 20 objekt med en poäng på 1,0 och 20 objekt med en poäng på 0,5. Om flera träffar har samma Sök poäng, är ordningen för samma poäng objekt inte definierad och är inte stabil. Kör frågan igen så kan du se objektets Skift läge. Om två objekt har samma poäng finns det ingen garanti för att det visas först.  

## <a name="when-to-use-custom-scoring"></a>När du ska använda anpassad Poängsättning  
 Du bör skapa en eller flera bedömnings profiler när standard ranknings beteendet inte är tillräckligt långt för att uppfylla dina affärs mål. Du kan till exempel bestämma att Sök relevans ska prioritera nyligen tillagda objekt. På samma sätt kan du ha ett fält som innehåller vinst marginal, eller något annat fält som visar potentiella intäkter. Det kan vara viktigt att förbättra träffarna som gör det möjligt för ditt företag att välja att använda bedömnings profiler.  

 Relevanta-baserad ordning implementeras också genom bedömnings profiler. Överväg att använda Sök Resultat sidor som du tidigare har använt, så att du kan sortera efter pris, datum, betyg eller relevans. I Azure Kognitiv sökning driver bedömnings profiler alternativet relevans. Definitionen av relevans styrs av dig, predikat på affärs mål och den typ av Sök upplevelse som du vill leverera.  

##  <a name="bkmk_ex"></a>Exempel  
 Som nämnts tidigare implementeras anpassad bedömning via en eller flera bedömnings profiler som definierats i ett index schema.  

 I det här exemplet visas schemat för ett index med två bedömnings profiler (`boostGenre``newAndHighlyRated`). Alla frågor mot detta index som inkluderar profil som en frågeparameter använder profilen för att räkna med resultat uppsättningen.  

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
 Om du vill implementera anpassade bedömnings beteende lägger du till en bedömnings profil i schemat som definierar indexet. Du kan ha upp till 100 bedömnings profiler i ett index (se [tjänst begränsningar](search-limits-quotas-capacity.md)), men du kan bara ange en profil i taget i en specifik fråga.  

 Börja med [mallen](#bkmk_template) som anges i det här avsnittet.  

 Ange ett namn. Bedömnings profiler är valfria, men om du lägger till en måste du ange namnet. Se till att följa namngivnings konventionerna för fält (börjar med en bokstav, undviker specialtecken och reserverade ord). Se [namngivnings &#40;regler Azure&#41; -kognitiv sökning](https://docs.microsoft.com/rest/api/searchservice/naming-rules) för den fullständiga listan.  

 Texten i bedömnings profilen är konstruerad från viktade fält och funktioner.  

|||  
|-|-|  
|**Lekar**|Ange namn/värde-par som tilldelar en relativ vikt till ett fält. I [exemplet](#bkmk_ex)är fälten albumTitle, genre och artistName förstärkta 1,5, 5 respektive 2. Varför ökar genren så mycket högre än de andra? Om sökningen utförs över data som är något homogena (som är fallet med "genre" i `musicstoreindex`) kan du behöva en större varians i de relativa vikterna. I `musicstoreindex`visas till exempel "Rock" som både en genre och i identiskt fraserade Genre beskrivningar. Om du vill att Genre ska uppväga Genre-Beskrivning behöver fältet Genre en mycket högre relativ vikt.|  
|**Funktioner**|Används när ytterligare beräkningar krävs för vissa kontexter. Giltiga värden är `freshness`, `magnitude`, `distance`och `tag`. Varje funktion har parametrar som är unika för den.<br /><br /> -   `freshness` ska användas när du vill öka det nya eller gamla objektet. Den här funktionen kan endast användas med `datetime`-fält (EDM. DataTimeOffset). Observera att attributet `boostingDuration` endast används med funktionen `freshness`.<br />-   `magnitude` ska användas när du vill öka det högsta eller lägsta värdet för ett numeriskt värde. Scenarier som anropar för den här funktionen är ökningar per vinst marginal, högsta pris, lägsta pris eller antal hämtningar. Den här funktionen kan endast användas med Double-och Integer-fält.<br />     För `magnitude`-funktionen kan du ändra intervallet, högt till lågt, om du vill använda det inverterade mönstret (till exempel för att öka priset på lägre pris mer än den högsta pris nivån). Med ett pris intervall från $100 till $1 skulle du ställa in `boostingRangeStart` vid 100 och `boostingRangeEnd` 1 för att öka de lägre pris artiklarna.<br />-   `distance` ska användas när du vill öka med närhet eller geografiska platser. Den här funktionen kan endast användas med `Edm.GeographyPoint` fält.<br />-   `tag` ska användas när du vill öka med taggarna gemensamt mellan dokument och Sök frågor. Den här funktionen kan endast användas med `Edm.String` och `Collection(Edm.String)` fält.<br /><br /> **Regler för att använda funktioner**<br /><br /> Funktions typ (`freshness`, `magnitude`, `distance`) `tag` måste vara gemener.<br /><br /> Funktioner får inte innehålla värden som är null eller tomma. Mer specifikt, om du inkluderar FieldName, måste du ange det till något.<br /><br /> Funktioner kan bara användas för filter bara fält. Mer information om filter bara fält finns i [skapa index &#40;Azure kognitiv sökning REST API&#41; ](https://docs.microsoft.com/rest/api/searchservice/create-index) .<br /><br /> Funktioner kan endast tillämpas på fält som har definierats i fält samlingen för ett index.|  

 När indexet har definierats skapar du indexet genom att ladda upp index schemat, följt av dokument. Se [skapa index &#40;Azure kognitiv sökning REST API&#41; ](https://docs.microsoft.com/rest/api/searchservice/create-index) och [Lägg till, uppdatera eller ta &#40;bort dokument Azure&#41; kognitiv sökning REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) för instruktioner om dessa åtgärder. När indexet har skapats bör du ha en funktionell bedömnings profil som fungerar med dina Sök data.  

##  <a name="bkmk_template"></a>Webbplatsmall  
 I det här avsnittet visas syntax och mall för bedömnings profiler. Mer information om attributen hittar du i avsnittet [index attribut referens](#bkmk_indexref) i nästa avsnitt.  

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

##  <a name="bkmk_indexref"></a>Referens för index attribut  

> [!NOTE]  
>  En poängsättnings funktion kan endast användas för fält som är filter bara.  

|Attribut|Beskrivning|  
|---------------|-----------------|  
|`Name`|Krävs. Detta är namnet på bedömnings profilen. Det följer samma namn konventioner för ett fält. Det måste börja med en bokstav, får inte innehålla punkter, kolon eller @-symboler och får inte börja med frasen "azureSearch" (Skift läges känsligt).|  
|`Text`|Innehåller egenskapen viktningar.|  
|`Weights`|Valfri. Ett namn/värde-par som anger ett fält namn och relativ vikt. Relativ vikt måste vara ett positivt heltal eller ett flytt ALS nummer. Det maximala värdet är Int32. MaxValue.<br /><br /> Du kan ange fält namnet utan motsvarande vikt. Vikter används för att ange vikten av ett fält i förhållande till ett annat.|  
|`Functions`|Valfri. En poängsättnings funktion kan endast användas för fält som är filter bara.|  
|`Type`|Krävs för poängsättnings funktioner. Anger vilken typ av funktion som ska användas. Giltiga värden är storlek, aktualitet, avstånd och tagg. Du kan inkludera fler än en funktion i varje bedömnings profil. Funktions namnet måste vara gemener.|  
|`Boost`|Krävs för poängsättnings funktioner. Ett positivt tal som används som multiplikator för rå score. Det får inte vara lika med 1.|  
|`Fieldname`|Krävs för poängsättnings funktioner. En poängsättnings funktion kan endast användas för fält som ingår i indexets fält samling och som kan filtreras. Dessutom introducerar varje funktions typ ytterligare begränsningar (färskhet används med DATETIME-fält, storlek med heltal eller dubbla fält och avstånd med plats fält). Du kan bara ange ett enskilt fält per funktions definition. Om du till exempel vill använda två gånger i samma profil måste du inkludera två definitions storlek, en för varje fält.|  
|`Interpolation`|Krävs för poängsättnings funktioner. Definierar lutningen för vilken poängen ökar från början av intervallet till slutet av intervallet. Giltiga värden är linjär (standard), konstant, kvadratisk och logaritmisk. Mer information finns i [Ange interpolation](#bkmk_interpolation) .|  
|`magnitude`|Funktionen poängsättnings funktion används för att ändra rangordningar baserat på värde intervallet för ett numeriskt fält. Några av de vanligaste användnings exemplen för detta är:<br /><br /> -   **stjärn klassificeringar:** ändra poängen baserat på värdet i fältet "stjärn klassificering". När två objekt är relevanta kommer objektet med den högre klassificeringen att visas först.<br />-   **marginal:** när två dokument är relevanta kan en åter försäljare vilja öka dokument som har högre marginaler först.<br />-   **Klicka på antal:** för program som spårar Klicka genom åtgärder till produkter eller sidor kan du använda storlek för att öka antalet objekt som tenderar att få ut mesta möjliga trafik.<br />-   **hämtnings antal:** för program som spårar hämtningar kan du använda funktionen omfattning för att öka objekt som har flest hämtningar.|  
|`magnitude` &#124; `boostingRangeStart`|Anger startvärdet för intervallet, vars storlek är resultatet. Värdet måste vara ett heltals värde eller ett flyttal. För stjärn klassificeringar mellan 1 och 4 blir det 1. För marginaler över 50% är detta 50.|  
|`magnitude` &#124; `boostingRangeEnd`|Anger slutvärdet för intervallet, vars storlek är resultatet. Värdet måste vara ett heltals värde eller ett flyttal. För stjärn klassificeringar mellan 1 och 4 är detta 4.|  
|`magnitude` &#124; `constantBoostBeyondRange`|Giltiga värden är true eller false (standard). När värdet är true fortsätter den fullständiga höjningen att gälla för dokument som har ett värde för fältet mål som är högre än det övre slutet av intervallet. Om värdet är false används inte ökningen av den här funktionen för dokument som har ett värde för det målfält som ligger utanför intervallet.|  
|`freshness`|Funktionen för uppdaterings funktionen används för att ändra rangordnings poängen för objekt baserat på värden i `DateTimeOffset` fält. Till exempel kan ett objekt med ett senare datum rangordnas högre än äldre objekt.<br /><br /> Det är också möjligt att rangordna objekt som Kalender händelser med framtida datum, så att objekt som är närmare för närvarande kan rangordnas högre än objekten längre fram i framtiden.<br /><br /> I den aktuella Service Release-versionen kommer en ände av intervallet att åtgärdas till den aktuella tiden. Den andra parten är en tid i det förflutna baserat på `boostingDuration`. Använd ett negativt `boostingDuration`om du vill öka ett antal gånger i framtiden.<br /><br /> Den hastighet med vilken de ökade ändringarna från ett högsta och lägsta intervall bestäms av den interpolering som tillämpas på bedömnings profilen (se bilden nedan). Om du vill ändra förstärknings faktorn väljer du en förstärknings faktor på mindre än 1.|  
|`freshness` &#124; `boostingDuration`|Anger en förfallo period efter vilken förstärkning kommer att stoppas för ett visst dokument. Se [set boostingDuration](#bkmk_boostdur) i följande avsnitt för syntax och exempel.|  
|`distance`|Funktionen för avstånds resultat används för att påverka poängen för dokument baserat på hur nära eller hur långt de är i förhållande till en referens geografisk plats. Referens platsen anges som en del av frågan i en parameter (med alternativet `scoringParameterquery` sträng) som ett Lon, lat-argument.|  
|`distance` &#124; `referencePointParameter`|En parameter som ska skickas i frågor som ska användas som referens plats. `scoringParameter` är en frågeparameter. Se [Sök i &#40;dokument Azure kognitiv sökning&#41; REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) för beskrivningar av frågeparametrar.|  
|`distance` &#124; `boostingDistance`|Ett tal som anger avståndet i kilo meter från referens platsen där förstärknings intervallet slutar.|  
|`tag`|Funktionen för att räkna poängen används för att påverka poängen för dokument baserat på taggar i dokument och Sök frågor. Dokument som har taggar i gemensamma med Sök frågan kommer att öka. Taggarna för Sök frågan anges som en bedömnings parameter i varje sökbegäran (med alternativet `scoringParameterquery` sträng).|  
|`tag` &#124; `tagsParameter`|En parameter som ska skickas i frågor för att ange taggar för en viss begäran. `scoringParameter` är en frågeparameter. Se [Sök i &#40;dokument Azure kognitiv sökning&#41; REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) för beskrivningar av frågeparametrar.|  
|`functionAggregation`|Valfri. Gäller endast när funktioner anges. Giltiga värden är: sum (standard), genomsnitt, lägsta, högsta och firstMatching. En Sök Poäng är ett enskilt värde som beräknas från flera variabler, inklusive flera funktioner. Det här attributet anger hur förstärkningarna av alla funktioner kombineras till en enda sammanställd ökning som sedan tillämpas på grund dokument poängen. Bas poängen baseras på det [TF-IDF-](http://www.tfidf.com/) värde som beräknas från dokumentet och Sök frågan.|  
|`defaultScoringProfile`|Om ingen bedömnings profil anges när du kör en Sök förfrågan används standard bedömnings processen (endast[TF-IDF](http://www.tfidf.com/) ).<br /><br /> Du kan ställa in ett standard bedömnings profil namn här, vilket gör att Azure Kognitiv sökning använda profilen när ingen specifik profil anges i Sök förfrågan.|  

##  <a name="bkmk_interpolation"></a>Ange interpolation  
 Med interpolation kan du ange formen på den lutning som används för poängsättning. Eftersom poängsättningen är hög till låg, minskar lutningen alltid, men interpolation anger kurvan för den nedåtriktade lutningen. Följande interpolation kan användas:  

|||  
|-|-|  
|`Linear`|För objekt som ligger inom max-och min-intervallet görs ökningen som tillämpas på objektet i ett konstant minsknings belopp. Linjär är standard-interpolation för en bedömnings profil.|  
|`Constant`|För objekt som ligger inom start-och Slutintervallet används en konstant förstärkning för ranknings resultatet.|  
|`Quadratic`|I jämförelse med en linjär interpolation som har en konstant minskning av ökningen minskar kvadratisk inlednings vis vid mindre takt och sedan den närmar sig slut intervallet, minskar den med ett mycket högre intervall. Det här alternativet för interpolation tillåts inte i taggnings bedömnings funktioner.|  
|`Logarithmic`|I jämförelse med en linjär interpolation som har en konstant minskning av ökningen minskar logaritmen inlednings vis vid högre takt och när det närmar sig slut intervallet, minskar det med ett mycket mindre intervall. Det här alternativet för interpolation tillåts inte i taggnings bedömnings funktioner.|  

 ![Konstant, linjär, kvadratisk, log10 linjer i grafen](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="bkmk_boostdur"></a>Ange boostingDuration  
 `boostingDuration` är ett attribut för funktionen `freshness`. Du använder den för att ange en utgångs period efter vilken förstärkning kommer att stoppas för ett visst dokument. Om du till exempel vill öka en produkt linje eller ett varumärke för en 10-dagars kampanj period anger du 10-dagars perioden som "P10D" för dessa dokument.  

 `boostingDuration` måste formateras som ett XSD "dayTimeDuration"-värde (en begränsad delmängd av ett varaktighets värde på ISO 8601). Mönstret för detta är: "P [nD] [T [nH] [nM] [nS]]".  

 Följande tabell innehåller flera exempel.  

|Varaktighet|boostingDuration|  
|--------------|----------------------|  
|1 dag|"P1D"|  
|2 dagar och 12 timmar|"P2DT12H"|  
|15 minuter|"PT15M"|  
|30 dagar, 5 timmar, 10 minuter och 6,334 sekunder|"P30DT5H10M6.334S"|  

 Fler exempel finns i [XML-schema: data typer (w3.org webbplats)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration).  

## <a name="see-also"></a>Se även  
   för [Azure KOGNITIV sökning rest](https://docs.microsoft.com/rest/api/searchservice/)  
 [Skapa index &#40;Azure kognitiv sökning REST API&#41; ](https://docs.microsoft.com/rest/api/searchservice/create-index)   
 [Azure Kognitiv sökning .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
