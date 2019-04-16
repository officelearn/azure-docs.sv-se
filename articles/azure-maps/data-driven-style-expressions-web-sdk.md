---
title: Datadrivna style uttryck i Azure Maps Web SDK | Microsoft Docs
description: Hur du använder en datadriven style uttryck i Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 3b234ca37783fe557baf307f198de9636b06a382
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579503"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Datadrivna Style-uttryck (Web SDK)

Uttryck kan du tillämpa affärslogik till formatalternativ som använder egenskaperna som definierats i varje figur i en datakälla. Uttryck kan även användas för att filtrera data i en datakälla eller ett lager. Uttryck kan bestå av villkorslogik, t.ex. if-uttryck och kan också användas för att manipulera data med; sträng, logiska och matematiska operatorer. 

Datadrivna format kan minska mängden kod som krävs för att implementera affärslogik runt formatering. När den används med lager, utvärderas uttryck vid återgivningstid på en separat tråd som ger bättre prestanda jämfört med utvärdering av affärslogik i UI-tråden.

Följande videoklipp ger en översikt över datadrivna formatering i Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Uttryck representeras som JSON-matriser. Det första elementet i ett uttryck i matrisen är en sträng som anger namnet på operatorn uttryck. Exempel: ”+” eller ”fallet”. Nästa element (om sådan finns) är argumenten uttryck. Varje argument är ett exakt värde (sträng, nummer, boolean, eller `null`), eller ett annat uttryck matris. Följande pseudocode definierar den grundläggande strukturen ett uttryck. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure Maps Web SDK har stöd för många typer av uttryck som kan användas på egen hand eller i kombination med andra uttryck.

| Typ av uttryck | Beskrivning |
|---------------------|-------------|
| [Booleska uttryck](#boolean-expressions) | Booleska uttryck ger en uppsättning med booleska operatorer uttryck för att utvärdera booleskt jämförelser. |
| [Uttryck för färg](#color-expressions) | Färg uttryck gör det enklare att skapa och ändra färgvärden. |
| [Villkorsuttryck](#conditional-expressions) | Villkorsuttryck ger logic-åtgärder som påminner om if-uttryck. |
| [Data-uttryck](#data-expressions) | Ger åtkomst till egenskapsdata i en funktion. |
| [Interpolera och steg uttryck](#interpolate-and-step-expressions) | Interpolera och steg uttryck kan användas för att beräkna värden längs en interpolerad kurvan eller -steg-funktion. |
| [Specifika Layer-uttryck](#layer-specific-expressions) | Särskilda uttryck som gäller endast för ett lager. |
| [Matematiska uttryck](#math-expressions) | Innehåller matematiska operatorer för att utföra en datadriven beräkningar inom ramen för uttrycket. |
| [Uttryck för anslutningssträng-operator](#string-operator-expressions) | Uttryck för anslutningssträng operatorn utföra konverteringen åtgärder på strängar, till exempel sammanfoga och konvertera fallet. |
| [Uttryck av typen](#type-expressions) | Uttryck av typen innehåller verktyg för testning och konvertering av olika datatyper, till exempel strängar, siffror och booleska värden. |
| [Variabelbindning uttryck](#variable-binding-expressions) | Variabelbindning uttryck kan resultatet av en beräkning som lagras i en variabel och refererar till någon annanstans i ett uttryck flera gånger utan att behöva beräkna om det lagrade värdet. |
| [Uttryck för zoomning](#zoom-expression) | Hämtar den aktuella zoomningsnivån för kartan på återgivningstid. |

Alla exempel i det här dokumentet använder följande funktion för att visar olika sätt i att olika typer av uttryck kan användas. 

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

## <a name="data-expressions"></a>Data-uttryck

Data-uttryck ger åtkomst till egenskapsdata i en funktion. 

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['at', number, array]` | objekt | Hämtar ett objekt från en matris. |
| `['geometry-type']` | sträng | Hämtar funktionstyp geometri: Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `['get', string]` | värde | Hämtar egenskapsvärdet för från egenskaperna för den aktuella funktionen. Returnerar null om den begärda egenskapen saknas. |
| `['get', string, object]` | värde | Hämtar egenskapsvärdet för från egenskaperna för det angivna objektet. Returnerar null om den begärda egenskapen saknas. |
| `['has', string]` | boolesk | Anger om egenskaperna för en funktion som har den angivna egenskapen. |
| `['has', string, object]` | boolesk | Anger om egenskaperna för objektet har den angivna egenskapen. |
| `['id']` | värde | Hämtar den funktions-ID om den har en. |
| `['length', string | array]` | nummer | Hämtar hur lång en sträng eller en matris. |

**Exempel**

Egenskaperna för en funktion kan nås direkt i ett uttryck med hjälp av en `get` uttryck. I följande exempel används ”zoneColor”-värdet för funktionen för att ange egenskapen färg för ett bubbeldiagram lager. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

Exemplet ovan fungerar bra om alla funktioner på platsen har den `zoneColor` egenskap, men om de inte gör färgen kommer sannolikt att gå ”black”. Ändra återställningsplats färgen, en `case` uttryck kan användas i kombination med den `has` uttryck för att kontrollera om egenskapen finns, och om den inte returnera en återställningsplats färg i stället.

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

Bubbeldiagram och symbolen lager renderas koordinaterna för alla former i en datakälla som standard. Detta kan göras för att fokusera på formhörnen i en polygon eller linje. Den `filter` alternativet skiktets kan användas för att begränsa den geometri typ av de funktioner som det återges med hjälp av en `['geometry-type']` uttryck i ett booleskt uttryck. I följande exempel begränsar ett bubbeldiagram lager så att endast `Point` funktioner återges.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

I följande exempel kan både `Point` och `MultiPoint` funktioner som ska återges. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Översikt över polygoner renderas på samma sätt i rad lager. Om du vill inaktivera det här beteendet i ett linje-lager, lägga till ett filter som endast tillåter `LineString` och `MultiLineString` funktioner.  

## <a name="math-expressions"></a>Matematiska uttryck

Matematiska uttryck ger matematiska operatorer för att utföra en datadriven beräkningar inom ramen för uttrycket.

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['+', number, number, …]` | nummer | Beräknar summan av de angivna siffrorna. |
| `['-', number]` | nummer | Subtraherar 0 med angivet tal. |
| `['-', number, number]` | nummer | Subtraherar första siffror med det andra talet. |
| `['*', number, number, …]` | nummer | Multiplicerar den angivna siffror tillsammans. |
| `['/', number, number]` | nummer | Dividerar den första siffran av det andra talet. |
| `['%', number, number]` | nummer | Beräknar återstoden vid division den första siffran av det andra talet. |
| `['^', number, number]` | nummer | Beräknar värdet för det första värdet upphöjt till potensen av det andra talet. |
| `['abs', number]` | nummer | Beräknar det absoluta värdet för det angivna värdet. |
| `['acos', number]` | nummer | Beräknar arccosinus för det angivna värdet. |
| `['asin', number]` | nummer | Beräknar sinus för det angivna värdet. |
| `['atan', number]` | nummer | Beräknar arctangens för det angivna värdet. |
| `['ceil', number]` | nummer | Avrundar talet uppåt till nästa hela heltal. |
| `['cos', number]` | nummer | Beräknar cos av det angivna värdet. |
| `['e']` | nummer | Returnerar den matematiska konstanten `e`. |
| `['floor', number]` | nummer | Avrundar talet nedåt till föregående hela heltal. |
| `['ln', number]` | nummer | Beräknar den naturliga logaritmen för det angivna värdet. |
| `['ln2']` | nummer | Returnerar den matematiska konstanten `ln(2)`. |
| `['log10', number]` | nummer | Beräknar den bas-10-logaritmen för det angivna värdet. |
| `['log2', number]` | nummer | Beräknar den base två logaritmen för det angivna värdet. |
| `['max', number, number, …]` | nummer | Beräknar det maximala antalet i den angivna uppsättningen med siffror. |
| `['min', number, number, …]` | nummer | Beräknar ett minimum i den angivna uppsättningen med siffror. |
| `['pi']` | nummer | Returnerar den matematiska konstanten `PI`. |
| `['round', number]` | nummer | Avrundar talet till närmaste heltal. Halva avrundas från noll. Till exempel `['round', -1.5]` utvärderas till -2. |
| `['sin', number]` | nummer | Beräknar sinus för det angivna värdet. |
| `['sqrt', number]` | nummer | Beräknar kvadratroten för det angivna värdet. |
| `['tan', number]` | nummer | Beräknar tangens för det angivna värdet. |
## <a name="boolean-expressions"></a>Booleska uttryck

Booleska uttryck ger en uppsättning med booleska operatorer uttryck för att utvärdera booleskt jämförelser.

När du jämför värden skrivs strikt jämförelsen. Värden av olika typer anses alltid lika. Fall där typerna som har visat sig vara olika parsa när är ogiltiga och producerar en parsas. 

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['! ', boolean]` | boolesk | Logisk negation. Returnerar `true` om indata är `false`, och `false` om indata är `true`. |
| `['!= ', value, value]` | boolesk | Returnerar `true` om indatavärdena inte är lika med, `false` annars. |
| `['<', value, value]` | boolesk | Returnerar `true` om första indata är strikt mindre än andra `false` annars. Argumenten krävs för att vara både strängar eller båda numren. |
| `['<=', value, value]` | boolesk | Returnerar `true` om första indata är mindre än eller lika med andra `false` annars. Argumenten krävs för att vara både strängar eller båda numren. |
| `['==', value, value]` | boolesk | Returnerar `true` om indatavärdena är lika med, `false` annars. Argumenten krävs för att vara både strängar eller båda numren. |
| `['>', value, value]` | boolesk | Returnerar `true` om den första indatan är strikt större än andra `false` annars. Argumenten krävs för att vara både strängar eller båda numren. |
| `['>=' value, value]` | boolesk | Returnerar `true` om första indata är större än eller lika med andra `false` annars. Argumenten krävs för att vara både strängar eller båda numren. |
| `['all', boolean, boolean, …]` | boolesk | Returnerar `true` om alla indata är `true`, `false` annars. |
| `['any', boolean, boolean, …]` | boolesk | Returnerar `true` om någon av indata `true`, `false` annars. |

## <a name="conditional-expressions"></a>Villkorliga uttryck

Villkorsuttryck ger logic-åtgärder som påminner om if-uttryck.

Följande uttryck utför villkorslogik åtgärder med indata. Till exempel den `case` uttryck som ger ”if/then/else” logic samtidigt som den `match` uttryck liknar en ”switch-instruktion”. 

### <a name="case-expression"></a>Case-uttryck

En `case` uttrycket är en typ av villkorsuttryck som tillhandahåller if-instruktionen som logic (if/then/else). Den här typen av uttryck hittar en lista över booleskt villkor och returnerar värdet för det första booleska villkoret är sant.

Följande pseudocode definierar strukturen för de `case` uttryck. 

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

I följande exempel vägleder genom olika booleskt villkor tills den hittar en som utvärderas till `true`, och sedan returnerar som associerade värde. Om inga booleska villkoret utvärderas till `true`, en återställningsplats värde returneras. 

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

### <a name="match-expression"></a>Matchar uttrycket

En `match` uttrycket är en typ av villkorsuttryck som tillhandahåller instruktionen exempel logik för switch. Indata kan vara ett uttryck som `['get', 'entityType']` som returnerar en sträng eller en siffra. Varje etikett måste vara ett enda exakt värde eller en matris med teckenvärden, vars värden måste vara alla strängar eller tal. Inmatningen matchar om något av värdena i matrisen matchningen. Varje etikett måste vara unikt. Om Indatatyp inte matchar typ av etiketterna, blir resultatet återställningsplats värdet.

Följande pseudocode definierar strukturen för de `match` uttryck. 

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

I följande exempel tittar på den `entityType` egenskapen för en punkt-funktion i ett bubbeldiagram lager söker efter en matchning. Om en matchning hittas, som anges som returneras värdet eller returneras återställningsplats värdet.

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

I följande exempel används en matris för att visa en uppsättning etiketter som ska alla returnera samma värde. Detta är mycket mer effektivt än lista varje etikett individuellt. I det här fallet, om den `entityType` egenskapen är ”restaurang” eller ”grocery_store”, ”red” färgen ska returneras.

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

### <a name="coalesce-expression"></a>Slå samman uttryck

En `coalesce` uttryck vägleder genom en uppsättning uttryck tills det första icke-null-värdet hämtas och returnerar värdet. 

Följande pseudocode definierar strukturen för de `coalesce` uttryck. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Exempel**

I följande exempel används en `coalesce` uttryck för att ange den `textField` möjlighet att ett lager för symbolen. Om den `title` egenskapen saknas från funktionen eller ange `null`, uttrycket försöker sedan letar du efter den `subtitle` egenskapen om dess saknas eller `null`, den sedan tillbaka till en tom sträng. 

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

## <a name="type-expressions"></a>Uttryck av typen

Uttryck av typen innehåller verktyg för testning och konvertering av olika datatyper, till exempel strängar, siffror och booleska värden.

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | matris \| objekt | Returnerar ett literalvärde matris eller ett objekt. Använd det här uttrycket för att förhindra att en matris eller ett objekt som utvärderas som ett uttryck. Detta är nödvändigt när en matris eller ett objekt som ska returneras av ett uttryck. |
| `['to-boolean', value]` | boolesk | Konverterar indatavärdet till ett booleskt värde. Resultatet är `false` när indata är en tom sträng, `0`, `false`, `null`, eller `NaN`, annars dess `true`. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Konverterar indatavärdet till en färg. Om flera värden har angetts, ska var och en utvärderas i ordning tills den första lyckade konverteringen erhålls. Om ingen av indata som kan konverteras, är uttrycket ett fel. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | nummer | Konverterar indatavärdet till ett tal, om möjligt. Om indata är `null` eller `false`, resultatet är 0. Om indata är `true`, resultatet är 1. Om indata är en sträng konverteras till ett tal med den [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) funktionen av ECMAScript språkspecifikationen. Om flera värden har angetts, ska var och en utvärderas i ordning tills den första lyckade konverteringen erhålls. Om ingen av indata som kan konverteras, är uttrycket ett fel. |
| `['to-string', value]` | sträng | Konverterar indatavärdet till en sträng. Om indata är `null`, resultatet är `""`. Om indata är ett booleskt värde, resultatet är `"true"` eller `"false"`. Om indata är ett tal, konverteras det till en sträng med hjälp av den [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) number funktionen av ECMAScript språkspecifikationen. Om indata är en färg, konverteras det till RGBA CSS-färgsträng `"rgba(r,g,b,a)"`. I annat fall indata konverteras till en sträng med hjälp av den [JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) funktionen av ECMAScript språkspecifikationen. |
| `['typeof', value]` | sträng | Returnerar en sträng som anger vilken typ av det givna värdet. |

> [!TIP]
> Om ett felmeddelande som liknar `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` visas i webbläsarens konsol innebär det att det finns ett uttryck någonstans i din kod som har en matris som inte har en sträng för sitt första värde. Om du vill att uttrycket som returnerar en matris omsluta matrisen med den `literal` uttryck. I följande exempel anger ikonen `offset` möjlighet att en symbol-lager, som måste vara en matris som innehåller två tal med en `match` uttryck för att välja mellan två offset-värden baserat på värdet för den `entityType` egenskapen punktens funktionen.
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

## <a name="color-expressions"></a>Uttryck för färg

Färg uttryck gör det enklare att skapa och ändra färgvärden.

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Skapar ett färgvärde från *red*, *grön*, och *blå* komponenter som måste vara mellan `0` och `255`, och en alpha -komponent`1`. Om någon komponent är utanför intervallet, är uttrycket ett fel. |
| `['rgba', number, number, number, number]` | color | Skapar ett färgvärde från *red*, *grön*, *blå* komponenter som måste vara mellan `0` och `255`, och en alpha-komponent inom ett intervall med `0` och `1`. Om någon komponent är utanför intervallet, är uttrycket ett fel. |
| `['to-rgba']` | \[tal, tal, tal, tal\] | Returnerar en fyra element-matris som innehåller indatafärgen *red*, *grön*, *blå*, och *alpha* komponenter i den ordningen. |

**Exempel**

I följande exempel skapas och RGB-färgvärde som har en *red* värdet för `255`, och *grön* och *blå* värdena som beräknas genom att multiplicera `2.5` av värdet för den `temperature` egenskapen. När temperaturen ändras färgen ändras till olika nyanser av *red*.

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

## <a name="string-operator-expressions"></a>Uttryck för anslutningssträng-operator

Uttryck för anslutningssträng operatorn utföra konverteringen åtgärder på strängar, till exempel sammanfoga och konvertera fallet. 

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['concat', string, string, …]` | sträng | Sammanfogar flera strängar tillsammans. Varje värde måste vara en sträng. Använd den `to-string` anger uttrycket som ska konverteras andra värdetyper till sträng om det behövs. |
| `['downcase', string]` | sträng | Konverterar den angivna strängen till gemener. |
| `['upcase', string]` | sträng | Konverterar den angivna strängen till versaler. |

**Exempel**

I följande exempel konverterar den `temperature` egenskapen punktens funktionen till en sträng och sammanfogar därefter ”° F” i slutet av den.

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

Ovanstående uttryck visas med en PIN-kod på kartan med texten ”64° F” aktiviteter över den som visas i bilden nedan.

<center>

![Exempel-anslutningssträng operatorn uttryck](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>Interpolera och steg uttryck

Interpolera och steg uttryck kan användas för att beräkna värden längs en interpolerad kurvan eller -steg-funktion. Dessa uttryck dra i ett uttryck som returnerar ett numeriskt värde som sin indata, till exempel `['get',  'temperature']`. Indatavärdet utvärderas mot par av inkommande och utgående värden, så kallade ”stoppar”, för att fastställa det värde som passar bäst för funktionen interpolerade kurvan eller steg. Indatavärden för varje steg måste vara ett tal och vara i stigande ordning. Utdatavärden måste vara ett tal, och matris med tal eller en färg.

### <a name="interpolate-expression"></a>Interpolera uttryck

En `interpolate` uttryck kan användas för att beräkna en kontinuerlig, problemfri värdeuppsättning genom interpolering mellan stoppvärdena. En `interpolate` uttryck som returnerar färgvärden ger en färgövertoning i vilka resultat väljer värden från.

Det finns tre typer av interpolering metoder som kan användas i en `interpolate` uttryck:
 
* `['linear']` -Interpolerar linjärt mellan två stoppas.
* `['exponential', base]` -Interpolerar exponentiellt mellan stoppas. Den `base` värdet styr den hastighet med vilken utdata ökar. Högre värden utdataresultatet öka mer mot hög slutet av intervallet. En `base` värde nära 1 producerar utdata som ökar mer linjärt.
* `['cubic-bezier', x1, y1, x2, y2]` -Interpolerar med hjälp av en [m3 bezierkurva](https://developer.mozilla.org/docs/Web/CSS/timing-function) definieras av de angivna kontrollpunkterna.

Här är ett exempel på hur dessa olika typer av interpolations ut. 

| Linjär  | Exponentiell | Cubic Bezier |
|---------|-------------|--------------|
| ![Linjär interpolation graph](media/how-to-expressions/linear-interpolation.png) | ![Exponentiell interpolering graph](media/how-to-expressions/exponential-interpolation.png) | ![M3 Bezier interpolering graph](media/how-to-expressions/bezier-curve-interpolation.png) |

Följande pseudocode definierar strukturen för de `interpolate` uttryck. 

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

I följande exempel används en `linear interpolate` uttryck för att ange den `color` egenskapen för ett bubbeldiagram lager baserat på den `temperature` egenskapen för punkt-funktionen. Om den `temperature` värdet är mindre än 60, ”blue” returneras om mellan 60 och mindre än 70 gult ska returneras om mellan 70 och lägre än 80 ”orange” returneras, om 80 eller större, ”red” kommer att returneras.

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

Följande bild visar hur färgerna som väljs för ovanstående uttryck.
 
<center>

![Interpolera uttryck exempel](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>Steguttryck

En `step` uttryck kan användas för att beräkna diskreta, stegvis resultatvärden genom att utvärdera en [bara konstant funktionen](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) definieras av stoppas. 

Följande pseudocode definierar strukturen för de `step` uttryck. 

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

Steg uttryck returnerar värdet Stoppa precis före indatavärdet eller första indatavärdet om indata är mindre än den första Stopp. 

**Exempel**

I följande exempel används en `step` uttryck för att ange den `color` egenskapen för ett bubbeldiagram lager baserat på den `temperature` egenskapen för punkt-funktionen. Om den `temperature` värdet är mindre än 60, ”blue” returneras om mellan 60 och mindre än 70 ”gul” returneras, om mellan 70 och lägre än 80 ”orange” returneras, om 80 eller större, ”red” kommer att returneras.

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

Följande bild visar hur färgerna som väljs för ovanstående uttryck.
 
<center>

![Steg uttryck exempel](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Specifika Layer-uttryck

Särskilda uttryck som gäller endast för specifika lager.

### <a name="heat-map-density-expression"></a>Termisk karta densitet uttryck

Ett uttryck för den termiska kartan densitet hämtar den termiska kartan densitet värdet för varje bildpunkt i ett lager för den termiska kartan och definieras som `['heatmap-density']`. Det här värdet är ett tal mellan `0` och `1` och används i kombination med en `interpolation` eller `step` uttryck för att definiera färg toningen brukade Färglägg den termiska kartan. Det här uttrycket kan bara användas i den [färg alternativet](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) skiktets termisk karta.

> [!TIP]
> Färgen vid index 0 i ett interpolering uttryck eller en steg-färg standardfärgen definierar färg i området där det finns inga data och kan användas för att definiera en bakgrundsfärg. Många föredrar att ställa in det här värdet för transparent eller en delvis transparent svart. 

**Exempel**

Det här exemplet används ett erbjuds interpolering uttryck för att skapa toningar smooth färg för att återge den termiska kartan. 

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

Förutom att använda en mjuk övertoning färglägga en termisk karta färger kan anges i en uppsättning intervall genom att använda en `step` uttryck. Med hjälp av en `step` uttrycket för färgläggning den termiska kartan bryts densitet visuellt i intervall som mer så liknar en profil eller polärdiagram style karta.  

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

Mer information finns i den [lägga till ett lager för den termiska kartan](map-add-heat-map-layer.md) dokumentation.

### <a name="line-progress-expression"></a>Rad förloppet uttryck

Ett uttryck för rad förloppet hämtar förloppet längs en tonad linje i ett linje-lager och definieras som `['line-progress']`. Det här värdet är ett tal mellan 0 och 1 och används i kombination med en `interpolation` eller `step` uttryck. Det här uttrycket kan bara användas med den [strokeGradient alternativet]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) skiktets rad. 

> [!NOTE]
> Den `strokeGradient` alternativet skiktets rad kräver den `lineMetrics` möjlighet att datakällan anges till `true`.

**Exempel**

I följande exempel används den `['line-progress']` uttryck för att tillämpa toningar färg på linjen för en rad.

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

[Se live exempel](map-add-shape.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Uttryck för fältet format

Formatuttryck för text-fältet kan användas med den `textField` alternativet lager som symbolen `textOptions` egenskapen att tillhandahålla blandat textformatering. Det här uttrycket tillåter en uppsättning inkommande strängar och formateringsalternativ anges. Följande alternativ kan anges för varje Indatasträngen i det här uttrycket.

 * `'font-scale'` -Anger skalningsfaktorn som teckenstorleken. Om det här värdet åsidosätter den `size` egenskapen för den `textOptions` för enskilda sträng.
 * `'text-font'` -Anger ett eller flera teckensnittsfamiljer som ska användas för den här strängen. Om det här värdet åsidosätter den `font` egenskapen för den `textOptions` för enskilda sträng.

Följande pseudocode definierar strukturen för formatuttryck för text-fältet. 

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

I följande exempel formaterar textfältet genom att lägga till en fetstil och skala upp teckenstorleken för den `title` egenskapen för funktionen. Det här exemplet lägger också till den `subtitle` egenskapen för funktionen på en ny rad med en uppskalad ned teckenstorlek.

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

Det här lagret renderas funktionen punkt som visas i bilden nedan:
 
<center>

![Bild av punkt-funktionen med formaterat textfält](media/how-to-expressions/text-field-format-expression.png) </center>

### <a name="number-format-expression"></a>Antalet formatuttryck

Den `number-format` uttryck kan endast användas med den `textField` möjlighet att ett lager för symbolen. Det här uttrycket konverterar det angivna talet till en formaterad sträng. Det här uttrycket omsluter JavaScript- [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) fungerar och har stöd för följande uppsättning alternativ.

 * `locale` -Ange det här alternativet för att konvertera tal till strängar på ett sätt som överensstämmer med det angivna språket. Skicka en [BCP-47 som språktagg](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) i det här alternativet.
 * `currency` – Om du vill konvertera numret till en sträng som representerar en valuta. Möjliga värden är den [ISO 4217 valutakoder](https://en.wikipedia.org/wiki/ISO_4217), till exempel ”USD” för amerikanska dollar, ”EUR” för euro eller ”CNY” för kinesiska RMB.
 * `'min-fraction-digits'` -Anger det minsta antalet decimaler som ska ingå i strängversion för talet.
 * `'max-fraction-digits'` -Anger det maximala antalet decimaler som ska ingå i strängversion för talet.

Följande pseudocode definierar strukturen för formatuttryck för text-fältet. 

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

I följande exempel används en `number-format` uttryck för att ändra hur `revenue` egenskapen för funktionen punkt som återges i den `textField` möjlighet att en symbol layer så att den visas ett värde för amerikanska dollar.

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

Det här lagret renderas funktionen punkt som visas i bilden nedan:

<center>

![Antalet format uttryck exempel](media/how-to-expressions/number-format-expression.png) </center>

## <a name="zoom-expression"></a>Uttryck för zoomning

En `zoom` uttrycket som används för att hämta den aktuella zoomningsnivån för kartan i återgivningstid och definieras som `['zoom']`. Det här uttrycket returnerar ett tal mellan lägsta och högsta zoomning på intervallet för kartan. Med det här uttrycket kan format som ska ändras dynamiskt som zoomningsnivån för kartan har ändrats. Den `zoom` uttryck får endast användas med `interpolate` och `step` uttryck.

**Exempel**

Som standard har radie datapunkter som återges i den termiska kartskiktet en fast pixel radius för alla zoomningsnivåer. Data-mängder tillsammans och termiska kartskiktet ser annorlunda ut som på kartan zoomar. En `zoom` uttryck kan användas för att skala radien för varje zoomningsnivån så att varje datapunkt omfattar samma fysiska kartan. Det gör den termiska kartan lagret titta statiska och mer konsekvent. Varje zoomningsnivån för kartan har dubbelt så många bildpunkter lodrätt och vågrätt som den tidigare zoomningsnivån. Skala radien så att den fördubblar med varje zoomningsnivån skapar en termisk karta som tittar konsekvent på alla zoomningsnivåer. Detta kan åstadkommas med hjälp av den `zoom` uttryck med en `base 2 exponential interpolation` uttryck som visas nedan. 

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

[Se live exempel](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Variabelbindning uttryck

Variabelbindning uttryck lagra resultatet av en beräkning i en variabel så att den kan referera till någon annanstans i ett uttryck flera gånger utan att behöva beräkna om den. Det här är en användbar optimering för uttryck som inbegriper många beräkningar

| Uttryck | Returtyp | Beskrivning |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'let',<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: sträng<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1: alla,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: sträng<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2: alla,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Lagrar ett eller flera värden som variabler för användning av den `var` uttryck i det underordnade uttryck som returnerar resultatet. |
| `['var', name: string]` | valfri | Refererar till en variabel som har skapats med den `let` uttryck. |

**Exempel**

Det här exemplet används ett uttryck som omsättningen i förhållande till temperatur-förhållande och sedan använder en `case` uttryck att utvärdera olika booleska åtgärder i det här värdet. Den `let` uttryck används för att lagra intäkter i förhållande till temperatur förhållande så att den behöver beräknas en gång och `var` uttrycket refererar till den här variabeln så ofta det behövs utan att behöva beräkna om den.

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

Se följande artiklar om fler kodexempel som implementerar uttryck:

> [!div class="nextstepaction"] 
> [Lägg till en symbol-lager](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Lägg till ett bubbeldiagram lager](map-add-bubble-layer.md)

> [!div class="nextstepaction"] 
> [Lägga till former](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Lägg till ett lager för den termiska kartan](map-add-heat-map-layer.md)

Läs mer om alternativ för bildrutsskikt som stöder uttryck:

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
