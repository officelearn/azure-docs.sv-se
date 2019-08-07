---
title: Data drivna format uttryck i Azure Maps Web SDK | Microsoft Docs
description: Använda data drivna format uttryck i Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 18d8f2a974fb192578163f71a57d00824ae6b0fa
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839469"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Uttryck för data drivna format (webb-SDK)

Med uttryck kan du tillämpa affärs logik för att formatera alternativ som ser till att egenskaperna som definierats i varje form i en data källa. Uttryck kan också användas för att filtrera data i en data källa eller ett lager. Uttryck kan bestå av villkorlig logik, t. ex. if-statements, och kan även användas för att manipulera data med; sträng, logiska och matematiska operatorer. 

Data drivna format kan minska mängden kod som behövs för att implementera affärs logik kring formatering. När det används med lager utvärderas uttryck vid åter givnings tiden på en separat tråd som ger bättre prestanda jämfört med att utvärdera affärs logiken i UI-tråden.

Följande videoklipp ger en översikt över data driven format i Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Uttryck visas som JSON-matriser. Det första elementet i ett uttryck i matrisen är en sträng som anger namnet på uttrycks operatorn. Till exempel "+" eller "Case". Nästa element (om det finns några) är argumenten för uttrycket. Varje argument är antingen ett litteralt värde (en sträng, en siffra, ett `null`booleskt värde eller ett) eller en annan uttrycks mat ris. Följande pseudocode definierar den grundläggande strukturen ett uttryck. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure Maps Web SDK stöder många typer av som kan användas på egen hand eller i kombination med andra uttryck.

| Typ av uttryck | Beskrivning |
|---------------------|-------------|
| [Mängd uttryck](#aggregate-expression) | Ett uttryck som definierar en beräkning som bearbetas över en uppsättning data och som kan användas med `clusterProperties` alternativet i en. `DataSource` |
| [Booleska uttryck](#boolean-expressions) | Booleska uttryck tillhandahåller en uppsättning booleska operator uttryck för att utvärdera booleska jämförelser. |
| [Färg uttryck](#color-expressions) | Färg uttryck gör det enklare att skapa och ändra färg värden. |
| [Villkors uttryck](#conditional-expressions) | Villkors uttryck tillhandahåller logik åtgärder som liknar IF-Statements. |
| [Data uttryck](#data-expressions) | Ger åtkomst till egenskaps data i en funktion. |
| [Interpolerade och steg uttryck](#interpolate-and-step-expressions) | Interpolerade och steg uttryck kan användas för att beräkna värden längs en interpolerad kurva eller en steg funktion. |
| [Lagerbaserat uttryck](#layer-specific-expressions) | Särskilda uttryck som endast gäller för ett enskilt lager. |
| [Matematiska uttryck](#math-expressions) | Innehåller matematiska operatorer för att utföra data drivna beräkningar i uttrycks ramverket. |
| [Uttryck för sträng operator](#string-operator-expressions) | Uttryck för sträng operatorer utför konverterings åtgärder på strängar som sammanfogning och konvertering av ärendet. |
| [Typ uttryck](#type-expressions) | Typ uttryck innehåller verktyg för att testa och konvertera olika data typer, t. ex. strängar, siffror och booleska värden. |
| [Uttryck för variabel bindning](#variable-binding-expressions) | Uttryck för variabel bindning gör att resultatet av en beräkning lagras i en variabel och refereras till någon annan stans i ett uttryck med flera gånger utan att behöva beräkna om det lagrade värdet. |
| [Uttryck för zoomning](#zoom-expression) | Hämtar den aktuella zoomnings nivån för kartan vid åter givnings tiden. |

I alla exempel i det här dokumentet används följande funktion för att demonstrera olika sätt i att de olika typerna av uttryck kan användas. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {     
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>Data uttryck

Data uttryck ger till gång till egenskaps data i en funktion. 

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['at', number, array]` | object | Hämtar ett objekt från en matris. |
| `['geometry-type']` | sträng | Hämtar funktionens geometri typ: Punkt, MultiPoint, lin Est ring, MultiLineString, polygon, multipolygon. |
| `['get', string]` | value | Hämtar egenskap svärdet från den aktuella funktionens egenskaper. Returnerar null om den begärda egenskapen saknas. |
| `['get', string, object]` | value | Hämtar egenskap svärdet från egenskaperna för det angivna objektet. Returnerar null om den begärda egenskapen saknas. |
| `['has', string]` | boolean | Anger om egenskaperna för en funktion har den angivna egenskapen. |
| `['has', string, object]` | boolean | Anger om egenskaperna för objektet har den angivna egenskapen. |
| `['id']` | value | Hämtar funktionens ID om det har en sådan. |
| `['length', string | array]` | nummer | Hämtar längden för en sträng eller matris. |

**Exempel**

Egenskaperna för en funktion kan nås direkt i ett uttryck med hjälp av ett `get` uttryck. I följande exempel används värdet "zoneColor" för funktionen för att ange färg egenskapen för ett bubbeldiagram. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

Exemplet ovan fungerar bra om alla punkt funktioner har `zoneColor` egenskapen, men om de inte gör det kommer färgen förmodligen att återgå till "svart". Om du vill ändra återställnings `case` färgen kan ett uttryck användas i kombination `has` med uttrycket för att kontrol lera om egenskapen finns och om den inte returnerar en återställnings färg i stället.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

Bubbeldiagram och symbol lager kommer att återge koordinaterna för alla former i en data källa som standard. Detta kan göras för att markera hörnen på en polygon eller linje. Alternativet för skiktet kan användas för att begränsa geometri typen för de funktioner som återges med hjälp av ett `['geometry-type']` uttryck i ett booleskt uttryck. `filter` I följande exempel begränsas ett bubbel lager så att `Point` endast funktioner återges.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

I följande exempel kommer både `Point` och `MultiPoint` funktionerna att återges. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

På samma sätt kommer konturen för polygoner att återges i linje lager. Om du vill inaktivera det här beteendet i ett linje lager lägger du till `LineString` ett `MultiLineString` filter som endast tillåter och-funktioner.  

## <a name="math-expressions"></a>Matematiska uttryck

Matematiska uttryck tillhandahåller matematiska operatorer för att utföra data drivna beräkningar i uttrycks ramverket.

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['+', number, number, …]` | nummer | Beräknar summan av de angivna talen. |
| `['-', number]` | nummer | Subtraherar 0 efter det angivna talet. |
| `['-', number, number]` | nummer | Subtraherar de första talen med det andra talet. |
| `['*', number, number, …]` | nummer | Multiplicerar de angivna talen tillsammans. |
| `['/', number, number]` | nummer | Dividerar det första talet med det andra talet. |
| `['%', number, number]` | nummer | Beräknar resten när det första talet divideras med det andra talet. |
| `['^', number, number]` | nummer | Beräknar värdet för det första värdet upphöjt till det andra värdets potens. |
| `['abs', number]` | nummer | Beräknar det absoluta värdet för det angivna talet. |
| `['acos', number]` | nummer | Beräknar arcus cosinus för det angivna talet. |
| `['asin', number]` | nummer | Beräknar arcus sinus för det angivna talet. |
| `['atan', number]` | nummer | Beräknar arcus tangens för det angivna talet. |
| `['ceil', number]` | nummer | Avrundar talet uppåt till närmaste heltal. |
| `['cos', number]` | nummer | Beräknar cos för det angivna talet. |
| `['e']` | nummer | Returnerar den matematiska konstanten `e`. |
| `['floor', number]` | nummer | Avrundar talet nedåt till föregående heltal. |
| `['ln', number]` | nummer | Beräknar den naturliga logaritmen för det angivna talet. |
| `['ln2']` | nummer | Returnerar den matematiska konstanten `ln(2)`. |
| `['log10', number]` | nummer | Beräknar basen 10-logaritmen för det angivna talet. |
| `['log2', number]` | nummer | Beräknar basen – två logaritmen för det angivna talet. |
| `['max', number, number, …]` | nummer | Beräknar det maximala antalet i den angivna uppsättningen tal. |
| `['min', number, number, …]` | nummer | Beräknar det lägsta antalet i den angivna uppsättningen tal. |
| `['pi']` | nummer | Returnerar den matematiska konstanten `PI`. |
| `['round', number]` | nummer | Avrundar talet till närmaste heltal. Värdena för halvvägs avrundas från noll. Till exempel `['round', -1.5]` utvärderas till-2. |
| `['sin', number]` | nummer | Beräknar sinus för det angivna talet. |
| `['sqrt', number]` | nummer | Beräknar kvadratroten ur det angivna talet. |
| `['tan', number]` | nummer | Beräknar tangens för det angivna talet. |

## <a name="aggregate-expression"></a>Mängd uttryck

Ett mängd uttryck definierar en beräkning som bearbetas över en uppsättning data och kan användas med `clusterProperties` alternativet för en. `DataSource` Utdata från dessa uttryck måste vara en siffra eller ett booleskt värde. 

Ett agg regerings uttryck tar i tre värden. ett operator värde och ett start värde och ett uttryck för att hämta en egenskap från varje funktion i en data för att tillämpa den sammanställda åtgärden. Det här uttrycket har följande format:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- Operator En uttrycks funktion som sedan tillämpas på alla värden som beräknas av `mapExpression` för varje punkt i klustret. Operatorer som stöds; 
    - För siffror: `+`, `*`, `max`,`min`
    - För booleska värden: `all`,`any`
- initialValue: Ett initialt värde där det första beräknade värdet aggregeras mot.
- mapExpression: Ett uttryck som tillämpas mot varje punkt i data uppsättningen.

**Exempel**

Om alla funktioner i en data uppsättning har en `revenue` egenskap som är ett tal. Den totala intäkten för alla punkter i ett kluster som skapats från data uppsättningen kan beräknas med följande mängd uttryck:`['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Booleska uttryck

Booleska uttryck tillhandahåller en uppsättning booleska operator uttryck för att utvärdera booleska jämförelser.

När värden jämförs skrivs jämförelsen strikt. Värden av olika typer betraktas alltid som likvärdiga. De fall där typerna är kända för olika tidpunkter för parsar betraktas som ogiltiga och genererar ett parsningsfel. 

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | Logisk negation. Returnerar `true` om indatatypen `false`är och `false` om indatatypen `true`är. |
| `['!= ', value, value]` | boolean | Returnerar `true` Om indatavärdena inte är lika, `false` annars. |
| `['<', value, value]` | boolean | Returnerar `true` om den första indatamängden är strikt mindre än den `false` andra, annars. Argumenten måste vara antingen båda strängarna eller båda talen. |
| `['<=', value, value]` | boolean | Returnerar `true` om den första indatamängden är mindre än eller lika med den `false` andra, annars. Argumenten måste vara antingen båda strängarna eller båda talen. |
| `['==', value, value]` | boolean | Returnerar `true` Om indatavärdena är identiska, `false` annars. Argumenten måste vara antingen båda strängarna eller båda talen. |
| `['>', value, value]` | boolean | Returnerar `true` om den första indatamängden är exakt större än den `false` andra, annars. Argumenten måste vara antingen båda strängarna eller båda talen. |
| `['>=' value, value]` | boolean | Returnerar `true` om den första indatamängden är större än eller lika med den `false` andra, annars. Argumenten måste vara antingen båda strängarna eller båda talen. |
| `['all', boolean, boolean, …]` | boolean | Returnerar `true` om alla indata är `true`, `false` annars. |
| `['any', boolean, boolean, …]` | boolean | Returnerar `true` om någon av indatan `true`är `false` , annars. |

## <a name="conditional-expressions"></a>Villkorliga uttryck

Villkors uttryck tillhandahåller logik åtgärder som liknar IF-Statements.

Följande uttryck utför villkorliga logik åtgärder på indata. `case` Uttrycket innehåller exempelvis logiken "if/then/Else" `match` medan uttrycket liknar en "switch-sats". 

### <a name="case-expression"></a>Case-uttryck

Ett `case` uttryck är en typ av villkors uttryck som ger IF-Statement som Logic (om/sedan/Else). Den här typen av uttrycks steg genom en lista över booleska villkor och returnerar värdet output för det första booleska villkoret som är sant.

Följande pseudocode definierar strukturen för `case` uttrycket. 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**Exempel**

I följande exempel går vi igenom olika booleska villkor tills den hittar en som utvärderas `true`till och returnerar sedan det associerade värdet. Om inget booleskt villkor utvärderas till `true`returneras ett återställnings värde. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Matchnings uttryck

Ett `match` uttryck är en typ av villkors uttryck som ger switch-instruktion som logik. Inmatade värden kan vara ett uttryck `['get', 'entityType']` som returnerar en sträng eller ett tal. Varje etikett måste antingen vara ett enstaka litteralt värde eller en matris med litterala värden, vars värden måste vara alla strängar eller alla siffror. Inmatarna matchar om något av värdena i mat ris matchningen. Varje etikett måste vara unik. Om indatatypen inte matchar typen av etiketter, blir resultatet värdet fallback.

Följande pseudocode definierar strukturen för `match` uttrycket. 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**Exempel**

Följande exempel tittar på `entityType` egenskapen för en punkt funktion i ett Bubble-lager söker efter en matchning. Om en matchning hittas returneras det angivna värdet eller returneras återställnings värde.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

I följande exempel används en matris för att visa en uppsättning etiketter som alla ska returnera samma värde. Det här är mycket mer effektivt än att lista varje etikett individuellt. I detta fall returneras färgen " `entityType` Red", om egenskapen är "restaurang" eller "grocery_store".

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

### <a name="coalesce-expression"></a>Sammanslagnings uttryck

Ett `coalesce` uttrycks steg genom en uppsättning uttryck tills det första värdet som inte är null hämtas och returnerar värdet. 

Följande pseudocode definierar strukturen för `coalesce` uttrycket. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Exempel**

I följande exempel används ett `coalesce` uttryck för att `textField` ange alternativet för ett symbol lager. Om egenskapen saknas i funktionen eller om den är inställd på `null`, försöker uttrycket `subtitle` sedan att söka efter egenskapen, om den saknas eller `null`, kommer den att återgå till en tom sträng. `title` 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

## <a name="type-expressions"></a>Typ uttryck

Typ uttryck innehåller verktyg för att testa och konvertera olika data typer, t. ex. strängar, siffror och booleska värden.

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | mat \| ris objekt | Returnerar en literal matris eller ett objekt värde. Använd det här uttrycket för att förhindra att en matris eller ett objekt utvärderas som ett uttryck. Detta är nödvändigt när en matris eller ett objekt måste returneras av ett uttryck. |
| `['to-boolean', value]` | boolean | Konverterar indatavärdet till ett booleskt värde. Resultatet är `false` när indatatypen är en tom sträng `null` `false`, `0`,, eller `NaN`; i övrigt `true`. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | färg | Konverterar indatavärdet till en färg. Om flera värden anges utvärderas var och en i ordning tills den första lyckade konverteringen erhålls. Om ingen av indatana kan konverteras, är uttrycket ett fel. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | nummer | Konverterar indatavärdet till ett tal, om möjligt. Om indatatypen `null` är `false`eller, är resultatet 0. Om indatatypen `true`är är resultatet 1. Om indatatypen är en sträng konverteras den till ett tal med hjälp av funktionen [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) -sträng i språk specifikationen ECMAScript. Om flera värden anges utvärderas var och en i ordning tills den första lyckade konverteringen erhålls. Om ingen av indatana kan konverteras, är uttrycket ett fel. |
| `['to-string', value]` | sträng | Konverterar indatavärdet till en sträng. Om indatatypen `null`är är `""`resultatet. Om indatatypen är ett booleskt värde är `"true"` resultatet eller. `"false"` Om indatatypen är ett tal, konverteras den till en sträng med hjälp av funktionen [toString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) Number i språk specifikationen ECMAScript. Om indatatypen är en färg konverteras den till CSS RGBA-färg `"rgba(r,g,b,a)"`sträng. Annars konverteras inmatarna till en sträng med hjälp av [JSON. stringify](https://tc39.github.io/ecma262/#sec-json.stringify) -funktionen i språk specifikationen ECMAScript. |
| `['typeof', value]` | sträng | Returnerar en sträng som beskriver typen för det aktuella värdet. |

> [!TIP]
> Om ett fel meddelande liknar `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` det som visas i webb läsar konsolen betyder det att det finns ett uttryck någonstans i koden som har en matris som inte har en sträng för det första värdet. Om du vill att uttrycket ska returnera en matris omsluter du matrisen `literal` med uttrycket. I följande exempel anges ikon `offset` alternativet för ett symbol lager, som måste vara en matris som innehåller två siffror, genom att använda ett `match` uttryck för att välja mellan två offset-värden baserat `entityType` på värdet för punktens egenskap zoomfunktionen.
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If there is no title, try getting the subtitle. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Färg uttryck

Färg uttryck gör det enklare att skapa och ändra färg värden.

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | färg | Skapar ett färg värde från *röda*, *gröna*och *blå* komponenter som måste vara mellan `0` och `255`och en alpha-komponent i `1`. Om någon komponent är utanför intervallet är uttrycket ett fel. |
| `['rgba', number, number, number, number]` | färg | Skapar ett färg värde från *röda*, *gröna*, *blå* komponenter som måste vara mellan `0` och `255`och en alpha-komponent inom ett intervall av `0` och `1`. Om någon komponent är utanför intervallet är uttrycket ett fel. |
| `['to-rgba']` | \[Number, Number, Number, Number\] | Returnerar en matris med fyra element som innehåller indatamängdens *röda*, *gröna*, *blå*och *alpha* -komponenter i den ordningen. |

**Exempel**

I följande exempel skapas och RGB-färgvärdet som har ett rött `255`värde av och de *gröna* och *blå* värdena som beräknas `2.5` `temperature` genom att multipliceras med egenskapens värde. När temperaturen ändras ändras färgen till olika nyanser av *rött*.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>Uttryck för sträng operator

Uttryck för sträng operatorer utför konverterings åtgärder på strängar som sammanfogning och konvertering av ärendet. 

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['concat', string, string, …]` | sträng | Sammanfogar flera strängar. Varje värde måste vara en sträng. `to-string` Använd typ uttrycket för att konvertera andra värde typer till sträng vid behov. |
| `['downcase', string]` | sträng | Konverterar den angivna strängen till gemener. |
| `['upcase', string]` | sträng | Konverterar den angivna strängen till versaler. |

**Exempel**

I följande exempel konverteras `temperature` egenskapen för punkt-funktionen till en sträng och sedan sammanfogas "°F" till slutet av den.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

Uttrycket ovan återger en PIN-kod på kartan med texten "64 °F" som står ovanpå den som visas på bilden nedan.

<center>

![Uttrycks exempel](media/how-to-expressions/string-operator-expression.png) för sträng operator</center>

## <a name="interpolate-and-step-expressions"></a>Interpolerade och steg uttryck

Interpolerade och steg uttryck kan användas för att beräkna värden längs en interpolerad kurva eller en steg funktion. Dessa uttryck tar i ett uttryck som returnerar ett numeriskt värde som indatatyp, till `['get',  'temperature']`exempel. Indatavärdet utvärderas mot par av indata-och utdatakolumner, som kallas "stoppa", för att fastställa det värde som passar bäst för funktionen interpolerad kurva eller funktion. Indatavärdena för varje stopp måste vara ett tal och vara i stigande ordning. Värdena för utdata måste vara ett tal, en matris med tal eller en färg.

### <a name="interpolate-expression"></a>Interpolerat uttryck

Ett `interpolate` uttryck kan användas för att beräkna en kontinuerlig, mjuk uppsättning värden genom interpolating mellan stopp värden. Ett `interpolate` uttryck som returnerar färg värden skapar en färg toning där resultat värden väljs.

Det finns tre typer av interpolation-metoder som kan användas i `interpolate` ett uttryck:
 
* `['linear']`– Interpolerar linjärt mellan stopp paret.
* `['exponential', base]`– Interpolerar exponentiellt mellan stoppen. `base` Värdet styr den hastighet som utdata ökar. Högre värden gör att utdata ökar till den övre delen av intervallet. Ett `base` värde nära 1 ger utdata som ökar linjärt.
* `['cubic-bezier', x1, y1, x2, y2]`– Interpolerar med en [kubikmeter-kurva](https://developer.mozilla.org/docs/Web/CSS/timing-function) som definieras av de angivna kontroll punkterna.

Här är ett exempel på hur de olika typerna av interpolerar ser ut. 

| Linjär  | Exponentiell | Kubisk Bezier |
|---------|-------------|--------------|
| ![Diagram över linjär interpolation](media/how-to-expressions/linear-interpolation.png) | ![Diagram över exponentiell interpolation](media/how-to-expressions/exponential-interpolation.png) | ![Diagram över kubisk bezierinterpolation](media/how-to-expressions/bezier-curve-interpolation.png) |

Följande pseudocode definierar strukturen för `interpolate` uttrycket. 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**Exempel**

I följande exempel används ett `linear interpolate` uttryck för att `color` ange egenskapen för ett `temperature` bubbel-lager baserat på egenskapen för punkt funktionen. `temperature` Om värdet är mindre än 60 kommer "blå" att returneras, om mellan 60 och mindre än 70, kommer gult att returneras, om mellan 70 och mindre än 80, returneras "orange", om 80 eller senare, "röd" kommer att returneras.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

Följande bild visar hur färgerna väljs för uttrycket ovan.
 
<center>

![Exempel](media/how-to-expressions/interpolate-expression-example.png) på interpolerat uttryck</center>

### <a name="step-expression"></a>Steg uttryck

Ett `step` uttryck kan användas för att beräkna diskreta, stegvisa resultat värden genom att utvärdera en [PieceWise-konstant funktion](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) som definieras av stopp. 

Följande pseudocode definierar strukturen för `step` uttrycket. 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

Steg uttryck returnerar utdata för stoppet precis före indatavärdet eller det första indatavärdet om indata är mindre än det första steget. 

**Exempel**

I följande exempel används ett `step` uttryck för att `color` ange egenskapen för ett `temperature` bubbel-lager baserat på egenskapen för punkt funktionen. `temperature` Om värdet är mindre än 60 returneras "blått", om mellan 60 och mindre än 70, returneras "gult", om mellan 70 och mindre än 80, returneras "orange", om 80 eller senare, "röd" kommer att returneras.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

Följande bild visar hur färgerna väljs för uttrycket ovan.
 
<center>

![Exempel på steg uttryck](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Lagerbaserat uttryck

Särskilda uttryck som endast gäller för specifika lager.

### <a name="heat-map-density-expression"></a>Uttryck för termisk kart täthet

Ett termiskt kart Täthets uttryck hämtar värdet för värme kartan för varje pixel i ett värme kart skikt och definieras som `['heatmap-density']`. Det här värdet är ett tal `0` mellan `1` och och används i kombination med ett `interpolation` or `step` -uttryck för att definiera färg toningen som används för att färga värme kartan. Det här uttrycket kan bara användas i [färg alternativet](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) för värme kart skiktet.

> [!TIP]
> Färgen vid index 0 i ett interpolation-uttryck eller standard färgen för en steg färg definierar ytans färg där det inte finns några data och som kan användas för att definiera en bakgrunds färg. Många föredrar att ange det här värdet som transparent eller en halv genomskinlig svart. 

**Exempel**

I det här exemplet används ett liner-interpolation-uttryck för att skapa en mjuk färg toning för rendering av värme kartan. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

Förutom att använda en mjuk toning för att färga en värme karta kan färger anges inom en uppsättning intervall med hjälp av ett `step` uttryck. Om du `step` använder ett uttryck för att färgsätta värme kartan bryts densiteten visuellt i intervall som påminner om en profil eller radar stil karta.  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

Mer information finns i dokumentationen för att [lägga till en termisk kart skikt](map-add-heat-map-layer.md) .

### <a name="line-progress-expression"></a>Uttryck för linje förlopp

Ett uttryck för linje förlopp hämtar förloppet längs en övertoningsfyllning i ett linje lager och definieras som `['line-progress']`. Det här värdet är ett tal mellan 0 och 1 och används i kombination med ett `interpolation` eller `step` -uttryck. Det här uttrycket kan bara användas med [alternativet strokeGradient]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) för rad skiktet. 

> [!NOTE]
> Alternativet för linje skiktet `lineMetrics` kräver att du anger alternativet för den data källa som ska anges `true`till. `strokeGradient`

**Exempel**

I följande exempel används `['line-progress']` uttrycket för att tillämpa en färg toning på linjen för en linje.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[Se live-exempel](map-add-shape.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Format uttryck för text fält

Format uttrycket för textfält kan användas med `textField` alternativet för egenskapen symbol lager `textOptions` för att ge blandad textformatering. Med det här uttrycket kan du ange en uppsättning med indatatyps strängar och formaterings alternativ. Följande alternativ kan anges för varje Indatasträngen i det här uttrycket.

 * `'font-scale'`– Anger skalnings faktorn för tecken storleken. Om det här värdet anges åsidosätts `size` egenskapen `textOptions` för för den enskilda strängen.
 * `'text-font'`-Anger en eller flera teckensnitts familjer som ska användas för den här strängen. Om det här värdet anges åsidosätts `font` egenskapen `textOptions` för för den enskilda strängen.

Följande pseudocode definierar strukturen för text fältets format uttryck. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    …
]
```

**Exempel**

I följande exempel formateras textfältet genom att lägga till ett fetstilt teckensnitt och skala upp tecken storleken `title` för egenskapen i funktionen. I det här exemplet läggs `subtitle` även egenskapen för funktionen till på en ny rad med en skalad nedåt tecken storlek.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 'font-scale': 0.75 }
        ]
    }
});
```

Det här lagret återger punkt funktionen som visas på bilden nedan:
 
<center>

![Bild av punkt funktion med formaterat textfält](media/how-to-expressions/text-field-format-expression.png)</center>

### <a name="number-format-expression"></a>Uttryck för tal format

Uttrycket kan bara användas `textField` med ett symbol skikts alternativ. `number-format` Det här uttrycket konverterar det angivna talet till en formaterad sträng. Det här uttrycket radbryter JavaScript-funktionen [number. toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) och har stöd för följande uppsättning alternativ.

 * `locale`– Ange det här alternativet för att konvertera siffror till strängar på ett sätt som justeras med det angivna språket. Skicka en [language-tagg för BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) till det här alternativet.
 * `currency`– Om du vill konvertera talet till en sträng som representerar en valuta. Möjliga värden är [ISO 4217-valuta koderna](https://en.wikipedia.org/wiki/ISO_4217), till exempel "USD" för amerikanska dollar, "EUR" för euron, eller "CNY" för kinesiska RMB.
 * `'min-fraction-digits'`– Anger det minsta antalet decimaler som ska ingå i sträng versionen av talet.
 * `'max-fraction-digits'`-Anger det maximala antalet decimaler som ska ingå i sträng versionen av talet.

Följande pseudocode definierar strukturen för text fältets format uttryck. 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**Exempel**

I följande exempel används ett `number-format` uttryck för att ändra `revenue` hur egenskapen för punkt funktionen återges i `textField` alternativet för ett symbol lager så att det visas som ett US-dollar värde.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency’: 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Det här lagret återger punkt funktionen som visas på bilden nedan:

<center>

![Uttrycks exempel](media/how-to-expressions/number-format-expression.png) för tal format</center>

## <a name="zoom-expression"></a>Uttryck för zoomning

Ett `zoom` uttryck används för att hämta den aktuella zoomnings nivån för kartan vid åter givnings tiden och definieras som `['zoom']`. Det här uttrycket returnerar ett tal mellan kartans minsta och högsta zoomnings nivå intervall. Genom att använda det här uttrycket kan du ändra formaten dynamiskt när kart nivån för kartan ändras. Uttrycket får bara användas med `interpolate` och `step` -uttryck. `zoom`

**Exempel**

Som standard har radien för data punkter som återges i värme kart skiktet ett fast pixel-radie för alla zoomnings nivåer. När kartan zoomas samman samlas data samman och termisk kart skiktet ser annorlunda ut. Ett `zoom` uttryck kan användas för att skala radien för varje zoomnings nivå så att varje data punkt täcker samma fysiska område i kartan. Det gör att värme kart lagret ser mer statiskt och konsekvent. Varje zoomnivå på kartan har två gånger så många bild punkter lodrätt och vågrätt som föregående zoomnings nivå. Om du skalar radien så att den dubbleras med varje zoomnivå skapas en värme karta som ser konsekvent ut på alla zoomnings nivåer. Detta kan åstadkommas med hjälp `zoom` av uttrycket med ett `base 2 exponential interpolation` uttryck som visas nedan. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 10,000 pixels.
        19, 10000
    ]
};
```

[Se live-exempel](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Uttryck för variabel bindning

Variabel bindnings uttryck lagrar resultatet av en beräkning i en variabel så att den kan refereras till någon annan stans i ett uttryck, utan att behöva beräkna om den. Detta är en användbar optimering för uttryck som omfattar många beräkningar

| Uttryck | Returtyp | Beskrivning |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"Let",<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: sträng,<br/>&nbsp;&nbsp;&nbsp;&nbsp;värde1: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: sträng,<br/>&nbsp;&nbsp;&nbsp;&nbsp;värde2: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Lagrar ett eller flera värden som variabler som används av `var` uttrycket i det underordnade uttrycket som returnerar resultatet. |
| `['var', name: string]` | valfri | Refererar till `let` en variabel som skapats med uttrycket. |

**Exempel**

I det här exemplet används ett uttryck som beräknar intäkterna relativt temperatur förhållandet och använder sedan ett `case` uttryck för att utvärdera olika booleska åtgärder för det här värdet. Uttrycket används för att lagra intäkterna relativt till temperatur förhållandet så att det bara behöver beräknas en gång `var` och uttrycket refererar till den här variabeln så ofta som det behövs utan att behöva beräkna om den. `let`

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>Nästa steg

I följande artiklar finns fler kod exempel som implementerar uttryck:

> [!div class="nextstepaction"] 
> [Lägg till ett symbol lager](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Lägg till ett bubbel-lager](map-add-bubble-layer.md)

> [!div class="nextstepaction"] 
> [Lägg till former](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Lägg till ett värme kart skikt](map-add-heat-map-layer.md)

Läs mer om de lager alternativ som stöder uttryck:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
