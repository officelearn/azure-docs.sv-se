---
title: Data drivna format uttryck i Azure Maps Web SDK | Microsoft Azure Maps
description: 'I den här artikeln får du lära dig mer om hur du använder data drivna format uttryck i Microsoft Azure Maps-webbsdk: n.'
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: c3f5fb2a387db6e672290fcf03d46c476b6211b6
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77657111"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Uttryck för data drivna format (webb-SDK)

Med uttryck kan du tillämpa affärs logik för att formatera alternativ som ser till att egenskaperna som definierats i varje form i en data källa. Uttryck kan filtrera data i en data källa eller ett lager. Uttryck kan bestå av villkorsstyrd logik, t. ex. if-Statements. Och kan användas för att manipulera data med hjälp av: sträng operatorer, logiska operatorer och matematiska operatorer.

Data drivna format minskar mängden kod som behövs för att implementera affärs logik kring formatering. När det används med lager utvärderas uttryck vid åter givnings tiden i en separat tråd. Den här funktionen ger ökad prestanda jämfört med att utvärdera affärs logiken i UI-tråden.

Den här videon ger en översikt över data driven format i Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Uttryck visas som JSON-matriser. Det första elementet i ett uttryck i matrisen är en sträng som anger namnet på uttrycks operatorn. Till exempel "+" eller "Case". Nästa element (om det finns några) är argumenten för uttrycket. Varje argument är antingen ett litteralt värde (en sträng, en siffra, ett booleskt värde eller ett `null`) eller en annan uttrycks mat ris. Följande pseudocode definierar den grundläggande strukturen i ett uttryck. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure Maps Web SDK stöder många typer av uttryck. Uttryck kan användas på egen hand eller i kombination med andra uttryck.

| Typ av uttryck | Beskrivning |
|---------------------|-------------|
| [Mängd uttryck](#aggregate-expression) | Ett uttryck som definierar en beräkning som bearbetas över en uppsättning data och som kan användas med alternativet `clusterProperties` för en `DataSource`. |
| [Booleska uttryck](#boolean-expressions) | Booleska uttryck tillhandahåller en uppsättning booleska operator uttryck för att utvärdera booleska jämförelser. |
| [Färg uttryck](#color-expressions) | Färg uttryck gör det enklare att skapa och ändra färg värden. |
| [Villkors uttryck](#conditional-expressions) | Villkors uttryck tillhandahåller logik åtgärder som liknar IF-Statements. |
| [Data uttryck](#data-expressions) | Ger åtkomst till egenskaps data i en funktion. |
| [Interpolerade och steg uttryck](#interpolate-and-step-expressions) | Interpolerade och steg uttryck kan användas för att beräkna värden längs en interpolerad kurva eller en steg funktion. |
| [Lagerbaserat uttryck](#layer-specific-expressions) | Särskilda uttryck som endast gäller för ett enskilt lager. |
| [Matematiska uttryck](#math-expressions) | Innehåller matematiska operatorer för att utföra data drivna beräkningar i uttrycks ramverket. |
| [Uttryck för sträng operator](#string-operator-expressions) | Uttryck för sträng operatorer utför konverterings åtgärder på strängar som sammanfogning och konvertering av ärendet. |
| [Typ uttryck](#type-expressions) | Typ uttryck innehåller verktyg för att testa och konvertera olika data typer, t. ex. strängar, siffror och booleska värden. |
| [Uttryck för variabel bindning](#variable-binding-expressions) | Variabel bindnings uttryck lagrar resultatet av en beräkning i en variabel och refereras till i ett uttryck flera gånger utan att behöva beräkna om det lagrade värdet. |
| [Uttryck för zoomning](#zoom-expression) | Hämtar den aktuella zoomnings nivån för kartan vid åter givnings tiden. |

I alla exempel i det här dokumentet används följande funktion för att demonstrera olika sätt som de olika typerna av uttryck kan användas på. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
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
| `['at', number, array]` | objekt | Hämtar ett objekt från en matris. |
| `['geometry-type']` | sträng | Hämtar funktionens geometri typ: punkt, MultiPoint, lin Est ring, MultiLineString, polygon, multipolygon. |
| `['get', string]` | värde | Hämtar egenskap svärdet från den aktuella funktionens egenskaper. Returnerar null om den begärda egenskapen saknas. |
| `['get', string, object]` | värde | Hämtar egenskap svärdet från egenskaperna för det angivna objektet. Returnerar null om den begärda egenskapen saknas. |
| `['has', string]` | boolesk | Anger om egenskaperna för en funktion har den angivna egenskapen. |
| `['has', string, object]` | boolesk | Anger om egenskaperna för objektet har den angivna egenskapen. |
| `['id']` | värde | Hämtar funktionens ID om det har en sådan. |
| `['length', string | array]` | nummer | Hämtar längden för en sträng eller en matris. |
| `['in', boolean | string | number, array]` | boolesk | Anger om ett objekt finns i en matris |
| `['in', substring, string]` | boolesk | Anger om en under sträng finns i en sträng |

**Exempel**

Egenskaperna för en funktion kan nås direkt i ett uttryck med hjälp av ett `get` uttryck. I det här exemplet används värdet "zoneColor" för funktionen för att ange färg egenskapen för ett bubbeldiagram. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

Ovanstående exempel fungerar bra om alla punkt funktioner har egenskapen `zoneColor`. Om de inte gör det kommer färgen sannolikt att återgå till "svart". Om du vill ändra återställnings färgen använder du ett `case` uttryck i kombination med uttrycket `has` för att kontrol lera om egenskapen finns. Om egenskapen inte finns returnerar du en återställnings färg.

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

Bubbeldiagram och symbol lager kommer att återge koordinaterna för alla former i en data källa som standard. Det här beteendet kan markera hörnen för en polygon eller en linje. `filter` alternativ för skiktet kan användas för att begränsa geometri typen för de funktioner som återges genom att använda ett `['geometry-type']` uttryck i ett booleskt uttryck. I följande exempel begränsas ett bubbel lager så att endast `Point` funktioner återges.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

I följande exempel kan både `Point`-och `MultiPoint`-funktioner återges. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

På samma sätt kommer konturen för polygoner att återges i linje lager. Om du vill inaktivera det här beteendet i ett linje lager lägger du till ett filter som endast tillåter `LineString` och `MultiLineString` funktioner.  

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

Ett mängd uttryck definierar en beräkning som bearbetas över en uppsättning data och kan användas med alternativet `clusterProperties` för en `DataSource`. Utdata från dessa uttryck måste vara ett tal eller ett booleskt värde. 

Ett agg regerings uttryck tar i tre värden: ett operator värde och ett start värde och ett uttryck för att hämta en egenskap från varje funktion i en data för att tillämpa den sammanställda åtgärden. Det här uttrycket har följande format:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operator: en uttrycks funktion som sedan tillämpas på alla värden som beräknas av `mapExpression` för varje punkt i klustret. Operatorer som stöds: 
    - För tal: `+`, `*`, `max``min`
    - För booleska värden: `all``any`
- initialValue: ett initialt värde där det första beräknade värdet aggregeras mot.
- mapExpression: ett uttryck som tillämpas mot varje punkt i data uppsättningen.

**Exempel**

Om alla funktioner i en data uppsättning har en `revenue`-egenskap, vilket är ett tal. Sedan kan den totala intäkten för alla punkter i ett kluster, som skapas från data uppsättningen, beräknas. Den här beräkningen görs med följande mängd uttryck: `['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Booleska uttryck

Booleska uttryck tillhandahåller en uppsättning booleska operator uttryck för att utvärdera booleska jämförelser.

När värden jämförs skrivs jämförelsen strikt. Värden av olika typer betraktas alltid som likvärdiga. De fall där typerna är kända för olika tidpunkter för parsar betraktas som ogiltiga och genererar ett parsningsfel. 

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['! ', boolean]` | boolesk | Logisk negation. Returnerar `true` om indatatypen är `false`och `false` om indatatypen är `true`. |
| `['!= ', value, value]` | boolesk | Returnerar `true` Om indatavärdena inte är lika, `false` annars. |
| `['<', value, value]` | boolesk | Returnerar `true` om den första indatamängden är strikt mindre än den andra, `false` annars. Argumenten måste vara antingen båda strängarna eller båda talen. |
| `['<=', value, value]` | boolesk | Returnerar `true` om den första indatamängden är mindre än eller lika med den andra, `false` annars. Argumenten måste vara antingen båda strängarna eller båda talen. |
| `['==', value, value]` | boolesk | Returnerar `true` Om indatavärdena är lika, `false` annars. Argumenten måste vara antingen båda strängarna eller båda talen. |
| `['>', value, value]` | boolesk | Returnerar `true` om den första indatamängden är exakt större än den andra, `false` annars. Argumenten måste vara antingen båda strängarna eller båda talen. |
| `['>=' value, value]` | boolesk | Returnerar `true` om den första indatamängden är större än eller lika med den andra, `false` annars. Argumenten måste vara antingen båda strängarna eller båda talen. |
| `['all', boolean, boolean, …]` | boolesk | Returnerar `true` om alla indata är `true``false` annars. |
| `['any', boolean, boolean, …]` | boolesk | Returnerar `true` om några indata är `true``false` annars. |

## <a name="conditional-expressions"></a>Villkorliga uttryck

Villkors uttryck tillhandahåller logik åtgärder som liknar IF-Statements.

Följande uttryck utför villkorliga logik åtgärder på indata. `case` uttrycket ger till exempel logiken "If/and/Else" medan uttrycket `match` är som en "switch-sats". 

### <a name="case-expression"></a>Case-uttryck

Ett `case` uttryck är en typ av villkors uttryck som tillhandahåller "om/sedan/Else"-logik. Den här typen av uttrycks steg i en lista över booleska villkor. Det returnerar utmatning svärdet för det första booleska villkoret för att utvärdera till sant.

Följande pseudocode definierar strukturen för det `case` uttrycket. 

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

I följande exempel går vi igenom olika booleska villkor tills den hittar en som utvärderar till `true`och returnerar sedan det associerade värdet. Om inget booleskt villkor utvärderas till `true`returneras ett återställnings värde. 

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

Ett `match` uttryck är en typ av villkors uttryck som ger switch-instruktion som logik. Inmatade värden kan vara ett uttryck som `['get', 'entityType']` som returnerar en sträng eller ett tal. Varje etikett måste antingen vara ett enstaka litteralt värde eller en matris med litterala värden, vars värden måste vara alla strängar eller alla siffror. Inmatarna matchar om något av värdena i mat ris matchningen. Varje etikett måste vara unik. Om indatatypen inte matchar typen av etiketter, blir resultatet värdet fallback.

Följande pseudocode definierar strukturen för det `match` uttrycket. 

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

Följande exempel tittar på `entityType` egenskapen för en punkt funktion i ett bubbeldiagram söker efter en matchning. Om en matchning hittas returneras det angivna värdet eller returneras återställnings värde.

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

I följande exempel används en matris för att visa en uppsättning etiketter som alla ska returnera samma värde. Den här metoden är mycket mer effektiv än att lista varje etikett individuellt. I detta fall returneras färgen "röd" om `entityType` egenskapen är "restaurang" eller "grocery_store".

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

I följande exempel används ett matchnings uttryck för att utföra en "i matris" eller "matris innehåller" typ filter. I det här fallet filtrerar uttrycket data som har ett ID-värde som finns i en lista över tillåtna ID: n. När du använder uttryck med filter måste resultatet vara ett booleskt värde.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: [
        'match',  

        //Get the property to match.
        ['get', 'id'],  

         //List of values to match.
        [24, 53, 98], 

        //If there is a match, return true.
        true,
    
        //Otherwise return false.
        false
    ]
});
```

### <a name="coalesce-expression"></a>Sammanslagnings uttryck

Ett `coalesce` uttrycks steg genom en uppsättning uttryck tills det första värdet som inte är null hämtas och returnerar värdet. 

Följande pseudocode definierar strukturen för det `coalesce` uttrycket. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Exempel**

I följande exempel används ett `coalesce`-uttryck för att ange alternativet `textField` för ett symbol lager. Om `title`-egenskapen saknas i funktionen eller är inställd på `null`, försöker uttrycket sedan att söka efter `subtitle`-egenskapen, om den saknas eller `null`, så kommer den att återgå till en tom sträng. 

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

I följande exempel används ett `coalesce`-uttryck för att hämta den första tillgängliga bild ikonen som är tillgänglig i kartan Sprite från en lista med angivna avbildnings namn.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>Typ uttryck

Typ uttryck innehåller verktyg för att testa och konvertera olika data typer, t. ex. strängar, siffror och booleska värden.

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | matris \| objekt | Returnerar en literal matris eller ett objekt värde. Använd det här uttrycket för att förhindra att en matris eller ett objekt utvärderas som ett uttryck. Detta är nödvändigt när en matris eller ett objekt måste returneras av ett uttryck. |
| `['image', string]` | sträng | Kontrollerar om ett angivet bild-ID läses in i Maps-avbildningen Sprite. Om så är fallet returneras ID, annars returneras null. |
| `['to-boolean', value]` | boolesk | Konverterar indatavärdet till ett booleskt värde. Resultatet är `false` när indatatypen är en tom sträng, `0`, `false`, `null`eller `NaN`. annars `true`. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Konverterar indatavärdet till en färg. Om flera värden anges utvärderas var och en i ordning tills den första lyckade konverteringen erhålls. Om ingen av indatana kan konverteras, är uttrycket ett fel. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | nummer | Konverterar indatavärdet till ett tal, om möjligt. Om indatatypen är `null` eller `false`är resultatet 0. Om indatatypen är `true`är resultatet 1. Om indatatypen är en sträng konverteras den till ett tal med hjälp av funktionen [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) -sträng i språk specifikationen ECMAScript. Om flera värden anges utvärderas var och en i ordning tills den första lyckade konverteringen erhålls. Om ingen av indatana kan konverteras, är uttrycket ett fel. |
| `['to-string', value]` | sträng | Konverterar indatavärdet till en sträng. Om indatatypen är `null`är resultatet `""`. Om indatatypen är ett booleskt värde är resultatet `"true"` eller `"false"`. Om indatatypen är ett tal, konverteras den till en sträng med hjälp av funktionen [toString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) Number i språk specifikationen ECMAScript. Om indatatypen är en färg konverteras den till CSS RGBA-färgsträng `"rgba(r,g,b,a)"`. Annars konverteras inmatarna till en sträng med hjälp av [JSON. stringify](https://tc39.github.io/ecma262/#sec-json.stringify) -funktionen i språk specifikationen ECMAScript. |
| `['typeof', value]` | sträng | Returnerar en sträng som beskriver typen för det aktuella värdet. |

> [!TIP]
> Om ett fel meddelande som liknar `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` visas i webb läsar konsolen betyder det att det finns ett uttryck någonstans i koden som har en matris som inte har en sträng för det första värdet. Om du vill att uttrycket ska returnera en matris omsluter du matrisen med `literal`-uttrycket. I följande exempel anges ikonen `offset` för ett symbol lager, som måste vara en matris som innehåller två siffror, genom att använda ett `match`-uttryck för att välja mellan två offset-värden baserat på värdet för egenskapen `entityType` för punkt funktionen.
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
>             //If the entity type is 'restaurant', return a different pixel offset. 
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
| `['rgb', number, number, number]` | color | Skapar ett färg värde från *röda*, *gröna*och *blå* komponenter som måste vara mellan `0` och `255`, och en alpha-komponent i `1`. Om någon komponent är utanför intervallet är uttrycket ett fel. |
| `['rgba', number, number, number, number]` | color | Skapar ett färg värde från *röda*, *gröna*, *blå* komponenter som måste vara mellan `0` och `255`, och en alpha-komponent inom en mängd `0` och `1`. Om någon komponent är utanför intervallet är uttrycket ett fel. |
| `['to-rgba']` | \[Number, Number, Number, Number\] | Returnerar en matris med fyra element som innehåller indatamängdens *röda*, *gröna*, *blå*och *alpha* -komponenter i den ordningen. |

**Exempel**

I följande exempel skapas ett RGB-färg värde som har ett *rött* värde av `255`och *gröna* och *blå* värden som beräknas genom att `2.5` multipliceras med värdet för egenskapen `temperature`. När temperaturen ändras ändras färgen till olika nyanser av *rött*.

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
| `['concat', string, string, …]` | sträng | Sammanfogar flera strängar. Varje värde måste vara en sträng. Använd `to-string` typ uttryck för att konvertera andra värde typer till sträng vid behov. |
| `['downcase', string]` | sträng | Konverterar den angivna strängen till gemener. |
| `['upcase', string]` | sträng | Konverterar den angivna strängen till versaler. |

**Exempel**

I följande exempel konverteras punkt funktionens `temperature` egenskap till en sträng och sammanfogas sedan "°F" till slutet av den.

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

exempel på ![sträng operator uttryck](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>Interpolerade och steg uttryck

Interpolerade och steg uttryck kan användas för att beräkna värden längs en interpolerad kurva eller en steg funktion. Dessa uttryck tar i ett uttryck som returnerar ett numeriskt värde som indatatyp, till exempel `['get',  'temperature']`. Indatavärdet utvärderas mot par av indata-och utdataparametrar, för att avgöra vilket värde som passar bäst för funktionen interpolerad kurva eller steg. Värdena för utdata kallas för "stoppa". Indatavärdena för varje stopp måste vara ett tal och vara i stigande ordning. Värdena för utdata måste vara ett tal, en matris med tal eller en färg.

### <a name="interpolate-expression"></a>Interpolerat uttryck

Ett `interpolate` uttryck kan användas för att beräkna en kontinuerlig, mjuk uppsättning värden genom interpolating mellan stopp värden. Ett `interpolate` uttryck som returnerar färg värden skapar en färg toning där resultat värden väljs.

Det finns tre typer av interpolation-metoder som kan användas i ett `interpolate`-uttryck:
 
* `['linear']` – interpolerar linjärt mellan stopp paret.
* `['exponential', base]` – interpolerar exponentiellt mellan stoppen. Värdet `base` anger med vilken hastighet utdata ökar. Högre värden gör att utdata ökar till den övre delen av intervallet. Ett `base` värde nära 1 genererar utdata som ökar linjärt.
* `['cubic-bezier', x1, y1, x2, y2]` – interpolerar med en [kubikmeter](https://developer.mozilla.org/docs/Web/CSS/timing-function) som definieras av de angivna kontroll punkterna.

Här är ett exempel på hur de olika typerna av interpolerar ser ut. 

| Linjär  | Exponentiell | Kubisk Bezier |
|---------|-------------|--------------|
| ![Diagram över linjär interpolation](media/how-to-expressions/linear-interpolation.png) | ![Diagram över exponentiell interpolation](media/how-to-expressions/exponential-interpolation.png) | ![Diagram över kubisk bezierinterpolation](media/how-to-expressions/bezier-curve-interpolation.png) |

Följande pseudocode definierar strukturen för det `interpolate` uttrycket. 

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

I följande exempel används ett `linear interpolate`-uttryck för att ange egenskapen `color` för ett bubbeldiagram baserat på egenskapen `temperature` för punkt funktionen. Om `temperature`-värdet är mindre än 60 returneras "blått". Om det är mellan 60 och mindre än 70 kommer gult att returneras. Om det är mellan 70 och mindre än 80 kommer "orange" att returneras. Om det är 80 eller högre returneras "röd".

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

exempel på ![interpolerat uttryck](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>Steg uttryck

Ett `step` uttryck kan användas för att beräkna diskreta, stegvisa resultat värden genom att utvärdera en [PieceWise-konstant funktion](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) som definieras av stopp. 

Följande pseudocode definierar strukturen för det `step` uttrycket. 

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

I följande exempel används ett `step`-uttryck för att ange egenskapen `color` för ett bubbeldiagram baserat på egenskapen `temperature` för punkt funktionen. Om `temperature`-värdet är mindre än 60 returneras "blått". Om det är mellan 60 och mindre än 70 returneras "gult". Om det är mellan 70 och mindre än 80 kommer "orange" att returneras. Om det är 80 eller högre returneras "röd".

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

exempel på ![steg-uttryck](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Lager-/regionsspecifika uttryck

Särskilda uttryck som endast gäller för specifika lager.

### <a name="heat-map-density-expression"></a>Uttryck för termisk kart täthet

Ett termiskt kart Täthets uttryck hämtar värdet för värme kartan för varje pixel i ett värme kart skikt och definieras som `['heatmap-density']`. Det här värdet är ett tal mellan `0` och `1`. Den används i kombination med ett `interpolation`-eller `step` uttryck för att definiera färg toningen som används för att färga värme kartan. Det här uttrycket kan bara användas i [färg alternativet](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) för värme kart skiktet.

> [!TIP]
> Färgen vid index 0, i ett interpolation-uttryck eller standard färgen för en steg färg definierar färgen för det utrymme där det inte finns några data. Färgen vid index 0 kan användas för att definiera en bakgrunds färg. Många föredrar att ange det här värdet som transparent eller en halv genomskinlig svart.

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

Förutom att använda en mjuk toning för att färga en värme karta kan färger anges inom en uppsättning intervall med hjälp av ett `step` uttryck. Om du använder ett `step`-uttryck för att färgsätta värme kartan, bryts densiteten i områden som liknar en profil-eller radar format karta.  

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

Ett uttryck för linje förlopp hämtar förloppet längs en övertoningsfyllning i ett linje lager och definieras som `['line-progress']`. Värdet är ett tal mellan 0 och 1. Den används i kombination med ett `interpolation`-eller `step`-uttryck. Det här uttrycket kan bara användas med [alternativet strokeGradient]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) för rad skiktet. 

> [!NOTE]
> Alternativet `strokeGradient` för linje lagret kräver att `lineMetrics` alternativet för data källan ska vara inställt på `true`.

**Exempel**

I det här exemplet används uttrycket `['line-progress']` för att tillämpa en färg toning på linjen för en linje.

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

[Se live-exempel](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Format uttryck för text fält

Du kan använda uttryck för textfält format med alternativet `textField` i egenskapen symbol lager `textOptions` för att ange blandat textformatering. Med det här uttrycket kan du ange en uppsättning med indatatyps strängar och formaterings alternativ. Följande alternativ kan anges för varje Indatasträngen i det här uttrycket.

 * `'font-scale'` – anger skalnings faktorn för tecken storleken. Om det här värdet anges åsidosätts egenskapen `size` för `textOptions` för den enskilda strängen.
 * `'text-font'`-anger en eller flera teckensnitts familjer som ska användas för den här strängen. Om det här värdet anges åsidosätts egenskapen `font` för `textOptions` för den enskilda strängen.
 * `'text-color'` – anger en färg som ska användas för en text vid åter givning. 

Följande pseudocode definierar strukturen för text fältets format uttryck. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[],
        'text-color': color
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] ,
        'text-color': color
    },
    …
]
```

**Exempel**

I följande exempel formaterar textfältet genom att lägga till ett fetstilt teckensnitt och skala upp tecken storleken för egenskapen `title` för funktionen. I det här exemplet läggs även egenskapen `subtitle` för funktionen till på en ny rad, med en skalad nedåt tecken storlek och röd färg.

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
            { 
                'font-scale': 0.75, 
                'text-color': 'red' 
            }
        ]
    }
});
```

Det här lagret återger punkt funktionen som visas på bilden nedan:
 
<center>

![avbildning av punkt funktion med formaterat textfält](media/how-to-expressions/text-field-format-expression.png) </center>

### <a name="number-format-expression"></a>Uttryck för tal format

`number-format`-uttrycket kan bara användas med alternativet `textField` för ett symbol lager. Det här uttrycket konverterar det angivna talet till en formaterad sträng. Det här uttrycket radbryter JavaScript-funktionen [number. toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) och har stöd för följande uppsättning alternativ.

 * `locale` – ange det här alternativet för att konvertera siffror till strängar på ett sätt som justeras med det angivna språket. Skicka en [language-tagg för BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) till det här alternativet.
 * `currency`-om du vill konvertera talet till en sträng som representerar en valuta. Möjliga värden är [ISO 4217-valuta koderna](https://en.wikipedia.org/wiki/ISO_4217), till exempel "USD" för amerikanska dollar, "EUR" för euron, eller "CNY" för kinesiska RMB.
 * `'min-fraction-digits'`-anger det minsta antalet decimaler som ska ingå i sträng versionen av talet.
 * `'max-fraction-digits'`-anger det maximala antalet decimaler som ska ingå i sträng versionen av talet.

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

I följande exempel används ett `number-format`-uttryck för att ändra hur egenskapen `revenue` för punkt funktionen återges i `textField` alternativ för ett symbol lager så att det visas ett US-dollar värde.

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

exempel](media/how-to-expressions/number-format-expression.png) ![Number format Expression </center>

## <a name="zoom-expression"></a>Uttryck för zoomning

Ett `zoom` uttryck används för att hämta den aktuella zoomnings nivån för kartan vid åter givnings tid och definieras som `['zoom']`. Det här uttrycket returnerar ett tal mellan kartans minsta och högsta zoomnings nivå intervall. Azure Maps interaktiva kart kontroller för webb-och Android-stöd 25 zoomnings nivåer, numrerade 0 till 24. Med hjälp av uttrycket `zoom` kan format ändras dynamiskt när kart nivån för kartan ändras. `zoom`-uttrycket får bara användas med `interpolate`-och `step`-uttryck.

**Exempel**

Som standard har radien för data punkter som återges i värme kart skiktet ett fast pixel-radie för alla zoomnings nivåer. När kartan är insamlad är data aggarna tillsammans och värme kart lagret annorlunda. Ett `zoom` uttryck kan användas för att skala radien för varje zoomnings nivå så att varje data punkt täcker samma fysiska område i kartan. Det gör att värme kart lagret ser mer statisk och konsekvent. Varje zoomnivå på kartan har två gånger så många bild punkter lodrätt och vågrätt som föregående zoomnings nivå. Om du skalar radien, så att den dubbleras med varje zoomnivå, skapas en värme karta som ser konsekvent ut på alla zoomnings nivåer. Det kan åstadkommas med hjälp av `zoom` uttryck med ett `base 2 exponential interpolation` uttryck som visas nedan. 

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

Variabel bindnings uttryck lagrar resultatet av en beräkning i en variabel. Så att beräknings resultaten kan refereras till någon annan stans i ett uttryck flera gånger. Det är en bra optimering för uttryck som omfattar många beräkningar.

| Uttryck | Returtyp | Beskrivning |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"Let",<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: sträng,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: sträng,<br/>&nbsp;&nbsp;&nbsp;&nbsp;värde2: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;...<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Lagrar ett eller flera värden som variabler som används av `var`-uttrycket i det underordnade uttrycket som returnerar resultatet. |
| `['var', name: string]` | valfri | Refererar till en variabel som skapats med `let`-uttrycket. |

**Exempel**

I det här exemplet används ett uttryck som beräknar intäkterna relativt temperatur förhållandet och använder sedan ett `case`-uttryck för att utvärdera olika booleska åtgärder för det här värdet. `let`-uttrycket används för att lagra intäkterna i förhållande till temperatur förhållandet, så att det bara behöver beräknas en gång. `var` uttrycket refererar till den här variabeln så ofta som det behövs utan att du behöver beräkna om den.

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
> [Lägg till ett linje lager](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygon-lager](map-add-shape.md)

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
