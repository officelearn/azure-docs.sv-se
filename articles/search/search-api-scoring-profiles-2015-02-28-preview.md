---
title: "Bedömningen profiler (Azure Search REST API-Version 2015-02-28-Preview) | Microsoft Docs"
description: "Azure Search är en söktjänst för värdbaserade moln som har stöd för inställning av rangordnas resultat baserat på användardefinierade bedömningsprofil profiler."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: bfd62649-13d7-40b3-a8fa-85521a15084d
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.author: heidist
ms.date: 10/27/2016
ms.openlocfilehash: a67637d149a84313270c03d21acf8a9c1870be05
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="scoring-profiles-azure-search-rest-api-version-2015-02-28-preview"></a>Bedömningsprofil profiler (Azure Search REST API-Version 2015-02-28-Preview)
> [!NOTE]
> Den här artikeln beskriver bedömningsprofil profiler i den [2015-02-28-Preview](search-api-2015-02-28-preview.md). Det finns för närvarande ingen skillnad mellan de `2016-09-01` version dokumenteras i [MSDN](http://msdn.microsoft.com/library/azure/mt183328.aspx) och `2015-02-28-Preview` version beskrivs här, men vi erbjuder ändå det här dokumentet för att kunna tillhandahålla dokumentet täckning i hela API: et.
>
>

## <a name="overview"></a>Översikt
Bedömningen refererar till beräkning av en sökning poäng för varje objekt som returneras i sökresultaten. Poängen är en indikator på ett objekt av betydelse i samband med den aktuella sökåtgärden. Ju högre poäng, mer relevant objektet. Objekt är RANG sorterade från högt till lågt utifrån Sök poängsättningen beräknas för varje objekt i sökresultaten.

Azure Search använder standard bedömningen för att beräkna ett inledande poäng, men du kan anpassa beräkningen via en bedömningsprofilen. Bedömningsprofil profiler ger dig större kontroll över rangordningen för objekt i sökresultaten. Du kanske vill höja objekt baserat på deras potentiella intäkter, befordra nyare element eller kanske öka objekt som har gjorts i lagret är för långt.

En bedömningsprofilen är en del av indexdefinitionen består av fält, funktioner och parametrar.

För att ge dig en uppfattning om hur en bedömningsprofilen ser ut, visas i följande exempel en enkel profil med namnet 'geo'. Den här förstärker objekt som innehåller söktermen i den `hotelName` fältet. Dessutom används den `distance` funktionen att acceptera objekt som ligger inom tio kilometer för den aktuella platsen. Om någon söker på termen ”inn' och 'inn' händer vara en del av namnet på hotell, visas dokument som innehåller hotell med 'inn' högre upp i sökresultatet.

    "scoringProfiles": [
      {
        "name": "geo",
        "text": {
          "weights": { "hotelName": 5 }
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

Om du vill använda den här bedömningsprofilen formulerade frågan om du vill ange vilken profil för frågesträngen. Lägg märke till i frågan nedan Frågeparametern, `scoringProfile=geo` i begäran.

    GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2015-02-28-Preview

Den här frågan söker efter termen ”inn' och överför på den aktuella platsen. Observera att den här frågan innehåller andra parametrar som `scoringParameter`. Frågeparametrar beskrivs i [Sök dokument (Azure Search-API)](search-api-2015-02-28-preview.md#SearchDocs).

Klicka på [exempel](#example) att granska en mer ingående exempel på en bedömningsprofilen.

## <a name="what-is-default-scoring"></a>Vad är standard bedömningen?
Bedömningen beräknar Sök poängen för varje objekt i en rank beställda resultatuppsättning. Alla objekt i sökresultaten är tilldelade en sökning poäng sedan rangordnas högsta till lägsta. Objekt med högre resultat returneras till programmet. Övre 50 returneras som standard, men du kan använda den `$top` parametern för att returnera ett större eller mindre antal objekt (upp till 1 000 stycken i ett enda svar).

Som standard beräknas en sökning poäng utifrån statistiska egenskaper för data och frågan. Azure Search hittar dokument som innehåller sökvillkoren i frågesträngen (vissa eller alla, beroende på `searchMode`), prioriterar dokument som innehåller många instanser av söktermen. Sök poäng går upp ännu större om termen sällsynta mellan data Kristi, men vanliga i dokumentet. Basen för den här metoden för databehandling relevans kallas TF IDF eller (termen frekvens inversen dokumentet frekvens).

Under förutsättning att det finns ingen anpassad sortering,-resultat rangordnas sedan Sök poäng innan de skickas tillbaka till det anropande programmet. Om `$top` har inte angetts 50 artiklar med högsta sökningen resultat returneras.

Sök poäng värden kan upprepas i en resultatuppsättning. Du kan till exempel ha 10 artiklar med en poäng för 1.2, 20 artiklar med en poäng 1.0 och 20 artiklar med en poäng för 0,5. När flera träffar har samma sökning poäng, sorteringen av samma poängsatta objekt har inte definierats och är inte stabilt. Kör frågan igen, och du kan se objekt SKIFT position. Två objekt med en identisk poäng är, det ingen garanti vilken visas först.

## <a name="when-to-use-custom-scoring"></a>När du ska använda anpassade bedömningen
Du bör skapa en eller flera bedömningsprofil profiler när standard rangordning beteendet inte är tillräckligt långt i uppfyller dina affärsmål. Du kan till exempel bestämma att sökningens relevans ska ge företräde åt nyligen tillagda objekt. På samma sätt kan du ha ett fält som innehåller vinst eller ett annat fält som indikerar potentiella intäkter. Förstärkning träffar som ger fördelar för företaget kan vara en viktig faktor för att bestämma att använda profiler för bedömningsprofil.

Relevans-baserade ordning genomförs även via bedömningen profiler. Överväg att sökningen resultatsidor som du har använt tidigare med vilka du kan sortera efter pris, datum, klassificering eller betydelse. I Azure Search enheten bedömningsprofil profiler alternativet 'relevans'. Definitionen av relevanta styrs av du förutsätter affärsmål och vilken typ av sökinställningar som du vill leverera.

<a name="example"></a>

## <a name="example"></a>Exempel
Som nämndes, implementeras anpassade bedömningen via bedömningen profiler som definierats i ett indexeringsschema.

Det här exemplet visar schemat för ett index med två bedömningsprofil profiler (`boostGenre`, `newAndHighlyRated`). Alla frågor mot detta index som innehåller antingen profil som en frågeparameter använder profilen för att poängsätta resultatmängden.

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
          "sourceFields": ["albumTitle", "artistName"]
        }
      ]
    }


## <a name="workflow"></a>Arbetsflöde
För att implementera anpassade bedömningsprofil beteende, att lägga till en bedömningsprofilen det schema som definierar indexet. Du kan ha upp till 16 bedömningen profiler i ett index (se [Tjänstbegränsningarna](search-limits-quotas-capacity.md)), men du kan bara ange en profil för närvarande i en given fråga.

Börja med den [mallen](#bkmk_template) i det här avsnittet.

Ange ett namn. Bedömningsprofil profiler är valfria, men om du lägger till en namn krävs. Se till att följa namnkonventionerna för fält (startar med en bokstav, undviker specialtecken och reserverade ord). Se [namnregler](http://msdn.microsoft.com/library/azure/dn857353.aspx) för mer information.

Innehållet i bedömningsprofilen konstrueras utifrån viktat fält och funktioner.

### <a name="weights"></a>Vikter
Den `weights` egenskapen för en bedömningsprofilen anger namn / värde-par som tilldelar relativa ett fält. I den [exempel](#example), albumTitle och genre artistName fälten är ökat 1.5 5 och 2, respektive. Varför genre förstärks så mycket högre än de andra? Om sökningen genomförs över data som är något homogen (vilket är fallet med 'genre' i den `musicstoreindex`), måste du kanske en större variationen i den relativa vikten. Till exempel i den `musicstoreindex`, 'Berg ”visas som båda genre och i identiskt fraserats genre beskrivningar. Om du vill för uppväger genre beskrivning måste fältet genre en mycket högre relativa vikt.

### <a name="functions"></a>Funktioner
Funktioner som används när det krävs ytterligare beräkningar för specifika sammanhang. Giltig funktionstyper är `freshness`, `magnitude`, `distance` och `tag`. Varje funktion har parametrar som är unika för den.

* `freshness`ska användas när du vill öka som hur nya eller gamla ett objekt. Den här funktionen kan bara användas med datetime-fält (`Edm.DataTimeOffset`). Observera den `boostingDuration` attributet används endast för funktionen dokumentens.
* `magnitude`ska användas när du vill öka baserat på hur hög eller låg är ett numeriskt värde. Scenarier som anropar för den här funktionen är förstärkning av vinst, högsta pris, lägsta pris eller antalet hämtningar. Du kan ändra intervallet, högt till lågt, om du vill att inverterade mönstret (till exempel till förstärkningen lägre priser objekt mer än högre prisvärda objekt). Få olika priser mellan 100 till $1, skulle du ange `boostingRangeStart` 100 och `boostingRangeEnd` på 1 för att öka lägre pris-objekt. Den här funktionen kan endast användas med dubbla och heltal.
* `distance`ska användas när du vill öka genom närhet eller geografisk plats. Den här funktionen kan bara användas med `Edm.GeographyPoint` fält.
* `tag`ska användas när du vill öka efter taggar mellan dokument och sökningar. Den här funktionen kan bara användas med `Edm.String` och `Collection(Edm.String)` fält.

#### <a name="rules-for-using-functions"></a>Regler för med hjälp av funktioner
* Funktionstyp (dokumentens, omfattning, avstånd, taggen) måste vara versaler.
* Funktioner kan inte innehålla null eller tomma värden. Om du inkluderar fältnamn, har du mer specifikt angetts till något används.
* Funktioner kan endast användas för att filtrera fält. Se [Create Index](search-api-2015-02-28-preview.md#CreateIndex) för mer information om filtrera fält.
* Funktioner kan endast tillämpas på fält som har definierats i fältsamlingen för ett index.

När indexet har definierats, skapa indexet genom att överföra indexeringsschema, följt av dokument. Se [Create Index](search-api-2015-02-28-preview.md#CreateIndex) och [Lägg till eller uppdatera dokument](search-api-2015-02-28-preview.md#AddOrUpdateDocuments) anvisningar för dessa åtgärder. När du har skapat indexet, bör du ha en fungerande bedömningsprofilen som fungerar med din sökning-data.

<a name="bkmk_template"></a>

## <a name="template"></a>Mall
Det här avsnittet visas syntax och mallen för resultatfunktioner profiler. Referera till [Index attributreferensen](#bkmk_indexref) i nästa avsnitt beskrivningar av attribut.

    ...
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

            // (- or -)

            "freshness": {
              "boostingDuration": "..." (value representing timespan over which boosting occurs)
            }

            // (- or -)

            "distance": {
              "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
              "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
            }

            // (- or -)

            "tag": {
              "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field)
            }
          }
        ],
        "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
      }
    ],
    "defaultScoringProfile": (optional) "...",
    ...

<a name="bkmk_indexref"></a>

## <a name="scoring-profile-property-reference"></a>Referens för bedömningsprofil profil
> [!NOTE]
> En bedömningsprofil funktion kan endast tillämpas på fält som är filtrera.
>
>

| Egenskap | Beskrivning |
| --- | --- |
| `name` |Krävs. Detta är namnet på bedömningsprofilen. Det följer samma mönster för ett fält. Den måste börja med en bokstav, får inte innehålla punkter, kolon eller @ symboler, och kan inte börja med frasen ”azureSearch” (skiftlägeskänsligt). |
| `text` |Innehåller egenskapen vikter. |
| `weights` |Valfri. Ett namn-värde-par som anger ett fältnamn och relativa viktade. Relativa viktade måste vara ett positivt heltal eller flyttal. Du kan ange fältnamn utan en motsvarande vikt. Vikten används för att visa ett fält i förhållande till en annan viktig. |
| `functions` |Valfri. Observera att en bedömningsprofil funktion kan endast användas för fält som är filtrera. |
| `type` |Krävs för resultatfunktioner funktioner. Anger vilken typ av funktion som ska användas. Giltiga värden är `magnitude`, `freshness`, `distance` och `tag`. Du kan ta mer än en funktion i varje bedömningsprofilen. Namnet på funktionen måste vara versaler. |
| `boost` |Krävs för resultatfunktioner funktioner. Ett positivt tal används som multiplikator för råvärde. Det går inte att vara lika med 1. |
| `fieldName` |Krävs för resultatfunktioner funktioner. En bedömningsprofil funktion kan endast tillämpas på fält som är en del av fältet-samlingen i indexet och som är filtrera. Dessutom kan introducerar varje funktionstyp ytterligare begränsningar (dokumentens används med datetime-fält omfattning med heltal eller dubbel fält, avstånd med plats och tagga med sträng eller samling strängfält). Du kan bara ange ett enda fält per funktionsdefinitionen. Om du vill använda omfattning två gånger i samma profil, skulle du behöva innehåller två definitioner omfattning, ett för varje fält. |
| `interpolation` |Krävs för resultatfunktioner funktioner. Anger lutning för vilken resultatförstärkningen ökar, från början av intervallet i slutet av intervallet. Giltiga värden är `linear` (standard), `constant`, `quadratic`, och `logarithmic`. Se [ange interpolations](#bkmk_interpolation) mer information. |
| `magnitude` |Omfattning bedömningen funktionen används för att ändra rangordningar baserat på värdeintervallet för ett numeriskt fält. Några av de vanligaste användningsexempel på detta är:<ul><li>Om du hämtar stjärnklassificeringar: Alter bedömningen baserat på värdet i fältet ”Star klassificeringen”. När två objekt är relevanta visas objekt med högre klassificeringen först.</li><li>Marginal: När två dokument är relevant, en återförsäljare kanske vill höja dokument som har högre marginaler först.</li><li>Klicka på antalet: program som spårar Klicka på genom att utföra åtgärder på produkter eller sidor, kan du använda omfattning förstärkningen objekt som tenderar att få ut mesta möjliga trafik.</li><li>Hämta antal: för program att spåra nedladdningar, de omfattning funktionen kan du öka objekt som har mest hämtningar.</li></ul> |
| `magnitude:boostingRangeStart` |Anger startvärdet för intervallet över vilket omfattning beräknas. Värdet måste vara ett heltal eller ett flyttal. För stjärnklassificering mellan 1 och 4 skulle värdet vara 1. För en marginal över 50% skulle värdet vara 50. |
| `magnitude:boostingRangeEnd` |Anger slutvärdet för intervallet över vilket omfattning beräknas. Värdet måste vara ett heltal eller ett flyttal. För stjärnklassificering mellan 1 och 4 skulle värdet vara 4. |
| `magnitude:constantBoostBeyondRange` |Giltiga värden är SANT eller FALSKT (standard). Om värdet är true, i hela förstärkningen ska användas för dokument som har ett värde för målfältet som är högre än den övre delen av intervallet. Om värdet är false kommer inte förstärkningen på tillämpas på dokument som har ett värde för målfältet som ligger utanför intervallet. |
| `freshness` |Uppdateringen bedömningen funktionen används för att ändra poängrangordning för objekt baserat på värden i DateTimeOffset-fälten. Till exempel kan ett objekt med ett senare datum tilldelas en högre än äldre objekt. (Observera att det är också möjligt att rank saker som kalenderhändelser med framtida datum så att objekt närmare aktuella kan tilldelas en högre än objekt ytterligare i framtiden.) I den aktuella versionen av tjänsten korrigeras en intervallslut till aktuell tid. Den andra änden är en gång tidigare baserat på den `boostingDuration`. För att öka flera gånger i framtiden för att använda en negativ `boostingDuration`. Frekvens som den förstärkning ändras från en högsta och lägsta intervallet bestäms av interpolerade tillämpas på bedömningsprofilen (se figuren nedan). Välj en förstärkningen faktor på mindre än 1 för att omvända den faktor som tillämpas. |
| `freshness:boostingDuration` |Anger en utgångsperiod efter vilken förstärkning tar slut för ett visst dokument. Se [ange boostingDuration](#bkmk_boostdur) i följande avsnitt för syntax och exempel. |
| `distance` |Avståndet mellan bedömningsprofil funktionen används för att påverka poängsättningen för dokument baserat på hur Stäng eller långt ifrån de är i förhållande till en geografisk referensplats. Referens för platsen anges som en del av frågan i en parameter (med hjälp av den `scoringParameter` Frågeparametern) som en celligt lat argumentet. |
| `distance:referencePointParameter` |En parameter som ska överföras i frågor som ska användas som referensplats. scoringParameter är en frågeparameter. Se [Sök dokument](search-api-2015-02-28-preview.md#SearchDocs) beskrivningar av Frågeparametrar. |
| `distance:boostingDistance` |Ett tal som anger avståndet, i kilometer, från referensplatsen där förstärkningsintervallet slutar. |
| `tag` |Taggen bedömningen funktionen används för att påverka poängsättningen för dokument baserat på taggar i dokument och sökningar. Att kommer ökat dokument som innehåller taggar gemensamt med frågan. Taggar för frågan har angetts som en bedömningsprofil parameter i varje sökbegäran (med hjälp av den `scoringParameter` Frågeparametern). |
| `tag:tagsParameter` |En parameter som används i frågor för att specificera taggar för en viss begäran. `scoringParameter`är en frågeparameter. Se [Sök dokument](search-api-2015-02-28-preview.md#SearchDocs) beskrivningar av Frågeparametrar. |
| `functionAggregation` |Valfri. Gäller endast när funktioner har angetts. Giltiga värden är: `sum` (standard), `average`, `minimum`, `maximum`, och `firstMatching`. En sökning poäng är ett värde som beräknats från flera variabler, inklusive flera funktioner. Detta attribut anger hur ökar av alla funktioner som kombineras i en enda sammanställd förstärkningen tillämpas sedan på grundläggande dokumentet poäng. -Resultat baserat på tf idf-värde som beräknats från dokumentet och sökfrågan. |
| `defaultScoringProfile` |När du kör en sökbegäran om inga bedömningsprofilen anges, är standard bedömningen används (tf-idf endast). Standard bedömningen profilnamn kan anges här orsakar Azure-sökning för att använda den här profilen när ingen specifik profil anges i sökbegäran. |

<a name="bkmk_interpolation"></a>

## <a name="set-interpolations"></a>Ange interpolations
Interpolations kan du definiera lutning för vilken resultatförstärkningen ökar, från början av intervallet i slutet av intervallet. Du kan använda följande interpolations:

* `Linear`: För objekt som ligger inom intervallet max och min utförs förstärkningen används för alternativet inom en ständigt minskar. Linjär är standard interpolerade för en bedömningsprofilen.
* `Constant`: För objekt som är i början och avslutar intervall för tillämpas en konstant förstärkning rank resultaten.
* `Quadratic`: I jämfört till en linjär interpolerade som har en ständigt minskande förstärkningen kommer först att minska andragradsekvation i mindre takt och sedan eftersom den närmar sig slutintervallet minskas med ett mycket högre intervall. Det här alternativet om interpolerade tillåts inte i taggen bedömningen funktioner.
* `Logarithmic`: Logaritmisk minskar ursprungligen i högre takt i jämfört till en linjär interpolerade som har en ständigt minskande förstärkningen, och sedan eftersom den närmar sig slutintervallet minskas med ett mycket mindre intervall. Det här alternativet om interpolerade tillåts inte i taggen bedömningen funktioner.

<a name="Figure1"></a> ![][1]

<a name="bkmk_boostdur"></a>

## <a name="set-boostingduration"></a>Ange boostingDuration
`boostingDuration`är ett attribut för funktionen dokumentens. Du kan använda den för att ange en giltighetstid period efter vilken förstärkning tar slut för ett visst dokument. Till exempel för att höja en produktserie eller varumärken under 10 dagar erbjudanden, anger du de 10 dagarna som ”P10D” för dessa dokument. Eller för att förbättra kommande händelser under de kommande veckorna ange ”-P7D”.

`boostingDuration`måste formateras som ett XSD ”daytimeduration” XSD-värde (en begränsad delmängd av ett ISO 8601-varaktighetsvärde). Mönstret för detta är: `[-]P[nD][T[nH][nM][nS]]`.

Följande tabell innehåller flera exempel.

| Varaktighet | boostingDuration |
| --- | --- |
| 1 dag |”P1D” |
| 2 dagar och 12 timmar |”P2DT12H” |
| 15 minuter |”PT15M” |
| 30 dagar, 5 timmar, 10 minuter och 6.334 sekunder |”P30DT5H10M6.334S” |

Fler exempel finns [XML-schemat: datatyper (W3.org webbplats)](http://www.w3.org/TR/xmlschema11-2/).

**Se även**
[Azure Söktjänsts-REST API](http://msdn.microsoft.com/library/azure/dn798935.aspx) på MSDN <br/>
[Skapa Index (Azure Search API)](http://msdn.microsoft.com/library/azure/dn798941.aspx) på MSDN<br/>
[Lägga till en bedömningsprofilen i en sökindex](http://msdn.microsoft.com/library/azure/dn798928.aspx) på MSDN<br/>

<!--Image references-->
[1]: ./media/search-api-scoring-profiles-2015-02-28-Preview/scoring_interpolations.png
