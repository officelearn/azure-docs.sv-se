---
title: Datadrivna formatuttryck i Azure Maps Web SDK | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om hur du använder datadrivna formatuttryck i Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 3f15033095b02dd35c2d8d7bda60ca184df64c9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475027"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Datadrivna formatuttryck (Web SDK)

Med uttryck kan du använda affärslogik på formateringsalternativ som observerar de egenskaper som definierats i varje form i en datakälla. Uttryck kan filtrera data i en datakälla eller ett lager. Uttryck kan bestå av villkorsstyrd logik, till exempel om-satser. Och de kan användas för att manipulera data med hjälp av: strängoperatorer, logiska operatorer och matematiska operatorer.

Datadrivna format minskar mängden kod som behövs för att implementera affärslogik kring styling. När uttryck används med lager utvärderas de vid återgivningstiden på en separat tråd. Den här funktionen ger ökad prestanda jämfört med att utvärdera affärslogik på gränssnittstråden.

Den här videon innehåller en översikt över datadriven formatering i Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Uttryck representeras som JSON-matriser. Det första elementet i ett uttryck i matrisen är en sträng som anger namnet på uttrycksoperatorn. Till exempel "+" eller "case". Nästa element (om sådana finns) är argumenten till uttrycket. Varje argument är antingen ett litteralt värde (en `null`sträng, ett tal, booleskt eller ) eller ) eller en annan uttrycksmatris. Följande pseudokod definierar den grundläggande strukturen för ett uttryck. 

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
| [Aggregerat uttryck](#aggregate-expression) | Ett uttryck som definierar en beräkning som bearbetas över en `clusterProperties` uppsättning data `DataSource`och kan användas med alternativet en . |
| [Booleska uttryck](#boolean-expressions) | Booleska uttryck ger en uppsättning booleska operatorer uttryck för att utvärdera booleska jämförelser. |
| [Färguttryck](#color-expressions) | Färguttryck gör det enklare att skapa och manipulera färgvärden. |
| [Villkorliga uttryck](#conditional-expressions) | Villkorsstyrda uttryck ger logiska åtgärder som är som om-satser. |
| [Datauttryck](#data-expressions) | Ger åtkomst till egenskapsdata i en funktion. |
| [Interpolera och stega uttryck](#interpolate-and-step-expressions) | Interpolera och steguttryck kan användas för att beräkna värden längs en interpolerad kurva eller stegfunktion. |
| [Lagerspecifika uttryck](#layer-specific-expressions) | Särskilda uttryck som endast är tillämpliga på ett enda lager. |
| [Matematiska uttryck](#math-expressions) | Tillhandahåller matematiska operatorer för att utföra datadrivna beräkningar inom uttrycksramen. |
| [Uttryck för strängoperator](#string-operator-expressions) | Strängoperatoruttryck utför konverteringsåtgärder på strängar som att sammanfoga och konvertera ärendet. |
| [Skriva uttryck](#type-expressions) | Typuttryck innehåller verktyg för att testa och konvertera olika datatyper som strängar, tal och booleska värden. |
| [Variabla bindningsuttryck](#variable-binding-expressions) | Variabla bindningsuttryck lagrar resultatet av en beräkning i en variabel och refereras någon annanstans i ett uttryck flera gånger utan att behöva beräkna om det lagrade värdet. |
| [Zooma uttryck](#zoom-expression) | Hämtar den aktuella zoomnivån på kartan vid renderingstillfället. |

Alla exempel i det här dokumentet använder följande funktion för att visa olika sätt på vilka olika typer av uttryck kan användas. 

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

## <a name="data-expressions"></a>Datauttryck

Datauttryck ger åtkomst till egenskapsdata i en funktion. 

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['at', number, array]` | objekt | Hämtar ett objekt från en matris. |
| `['geometry-type']` | sträng | Hämtar funktionens geometrityp: Punkt, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `['get', string]` | värde | Hämtar egenskapsvärdet från den aktuella funktionens egenskaper. Returnerar null om den begärda egenskapen saknas. |
| `['get', string, object]` | värde | Hämtar egenskapsvärdet från egenskaperna för det angivna objektet. Returnerar null om den begärda egenskapen saknas. |
| `['has', string]` | boolean | Avgör om egenskaperna för en funktion har den angivna egenskapen. |
| `['has', string, object]` | boolean | Bestämmer om objektets egenskaper har den angivna egenskapen. |
| `['id']` | värde | Hämtar funktionens ID om det har ett. |
| `['length', string | array]` | nummer | Hämtar längden på en sträng eller en matris. |
| `['in', boolean | string | number, array]` | boolean | Avgör om det finns ett objekt i en matris |
| `['in', substring, string]` | boolean | Avgör om det finns en delsträng i en sträng |

**Exempel**

Egenskaper för en funktion kan nås direkt i `get` ett uttryck med hjälp av ett uttryck. I det här exemplet används värdet "zoneColor" för funktionen för att ange färgegenskapen för ett bubbellager. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

Ovanstående exempel fungerar bra, om alla punktfunktioner har egenskapen. `zoneColor` Om de inte gör det, kommer färgen sannolikt reserv till "svart". Om du vill ändra reservfärgen använder du ett `case` uttryck i kombination med uttrycket `has` för att kontrollera om egenskapen finns. Om egenskapen inte finns returnerar du en reservfärg.

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

Bubbel- och symbollager återger som standard koordinaterna för alla former i en datakälla. Detta kan markera hörnen för en polygon eller en rad. Alternativet `filter` för lagret kan användas för att begränsa geometritypen för de `['geometry-type']` funktioner som återges, med hjälp av ett uttryck i ett booleskt uttryck. I följande exempel begränsas ett `Point` bubbellager så att endast funktioner återges.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

I följande exempel `Point` `MultiPoint` kan både funktioner och funktioner återges. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

På samma sätt återges dispositionen för polygoner i linjelager. Om du vill inaktivera det här beteendet `LineString` i `MultiLineString` ett linjelager lägger du till ett filter som bara tillåter och innehåller funktioner.  

## <a name="math-expressions"></a>Matematiska uttryck

Matematiska uttryck ger matematiska operatorer för att utföra datadrivna beräkningar inom uttrycksramen.

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['+', number, number, …]` | nummer | Beräknar summan av de angivna talen. |
| `['-', number]` | nummer | Subtraherar 0 med det angivna talet. |
| `['-', number, number]` | nummer | Subtraherar de första siffrorna med det andra talet. |
| `['*', number, number, …]` | nummer | Multiplicerar de angivna talen tillsammans. |
| `['/', number, number]` | nummer | Dividerar det första talet med det andra talet. |
| `['%', number, number]` | nummer | Beräknar resten när det första talet divideras med det andra talet. |
| `['^', number, number]` | nummer | Beräknar värdet för det första värdet som höjs till kraften i det andra talet. |
| `['abs', number]` | nummer | Beräknar det absoluta värdet för det angivna talet. |
| `['acos', number]` | nummer | Beräknar arcussinet för det angivna talet. |
| `['asin', number]` | nummer | Beräknar arcsinus för det angivna talet. |
| `['atan', number]` | nummer | Beräknar arcustangenten för det angivna talet. |
| `['ceil', number]` | nummer | Avrundar numret uppåt till nästa heltal. |
| `['cos', number]` | nummer | Beräknar cos för det angivna numret. |
| `['e']` | nummer | Returnerar den `e`matematiska konstanten . |
| `['floor', number]` | nummer | Avrundar talet nedåt till föregående heltal. |
| `['ln', number]` | nummer | Beräknar den naturliga logaritmen för det angivna talet. |
| `['ln2']` | nummer | Returnerar den `ln(2)`matematiska konstanten . |
| `['log10', number]` | nummer | Beräknar bas-tio logaritmen för det angivna talet. |
| `['log2', number]` | nummer | Beräknar bas-två logaritmen för det angivna talet. |
| `['max', number, number, …]` | nummer | Beräknar det maximala talet i den angivna uppsättningen tal. |
| `['min', number, number, …]` | nummer | Beräknar det minsta talet i den angivna uppsättningen tal. |
| `['pi']` | nummer | Returnerar den `PI`matematiska konstanten . |
| `['round', number]` | nummer | Avrundar numret till närmaste heltal. Halvvägs värden avrundas bort från noll. Utvärderar `['round', -1.5]` till exempel till -2. |
| `['sin', number]` | nummer | Beräknar sinus för det angivna numret. |
| `['sqrt', number]` | nummer | Beräknar kvadratroten för det angivna talet. |
| `['tan', number]` | nummer | Beräknar tangenten för det angivna talet. |

## <a name="aggregate-expression"></a>Aggregerat uttryck

Ett aggregerat uttryck definierar en beräkning som bearbetas över `clusterProperties` en uppsättning `DataSource`data och kan användas med alternativet en . Utdata för dessa uttryck måste vara ett tal eller en boolesk. 

Ett aggregerat uttryck tar in tre värden: ett operatörsvärde och ett ursprungligt värde och ett uttryck för att hämta en egenskap från varje funktion i en data som ska användas på den aggregerade åtgärden. Det här uttrycket har följande format:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operator: En uttrycksfunktion som sedan används mot `mapExpression` alla värden som beräknas av för varje punkt i klustret. Operatorer som stöds: 
    - För `+`siffror: `*` `max`, ,`min`
    - För booleans: `all`,`any`
- initialvalue:Ett initialt värde som det första beräknade värdet aggregeras mot.
- mapExpression: Ett uttryck som tillämpas mot varje punkt i datauppsättningen.

**Exempel**

Om alla funktioner i en `revenue` datauppsättning har en egenskap, vilket är ett tal. Sedan kan den totala intäkten för alla punkter i ett kluster, som skapas från datauppsättningen, beräknas. Den här beräkningen görs med följande mängduttryck:`['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Booleska uttryck

Booleska uttryck ger en uppsättning booleska operatorer uttryck för att utvärdera booleska jämförelser.

När du jämför värden skrivs jämförelsen strikt. Värden av olika slag anses alltid vara ojämlika. Fall där typerna är kända för att vara olika vid tolkningstid anses ogiltiga och kommer att ge ett tolkningsfel. 

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | Logisk negation. Returnerar `true` om `false`indata `false` är , `true`och om indata är . |
| `['!= ', value, value]` | boolean | Returnerar `true` om indatavärdena `false` inte är lika, annars. |
| `['<', value, value]` | boolean | Returnerar `true` om den första indata `false` är betydligt mindre än den andra, annars. Argumenten måste vara antingen både strängar eller båda talen. |
| `['<=', value, value]` | boolean | Returnerar `true` om den första indata är `false` mindre än eller lika med den andra, annars. Argumenten måste vara antingen både strängar eller båda talen. |
| `['==', value, value]` | boolean | Returnerar `true` om indatavärdena är lika, `false` annars. Argumenten måste vara antingen både strängar eller båda talen. |
| `['>', value, value]` | boolean | Returnerar `true` om den första indata `false` är strikt större än den andra, annars. Argumenten måste vara antingen både strängar eller båda talen. |
| `['>=' value, value]` | boolean | Returnerar `true` om den första indata är `false` större än eller lika med den andra, annars. Argumenten måste vara antingen både strängar eller båda talen. |
| `['all', boolean, boolean, …]` | boolean | Returnerar `true` om alla `true`indata är , `false` annars. |
| `['any', boolean, boolean, …]` | boolean | Returnerar `true` om någon av `true` `false` ingångarna är , annars. |

## <a name="conditional-expressions"></a>Villkorliga uttryck

Villkorsstyrda uttryck ger logiska åtgärder som är som om-satser.

Följande uttryck utför villkorsstyrda logikåtgärder på indata. Uttrycket ger `case` till exempel logiken "om/då/annat" medan uttrycket `match` är som en "switch-statement". 

### <a name="case-expression"></a>Uttryck för ärende

Ett `case` uttryck är en typ av villkorsuttryck som ger logiken "om/då/annat". Den här typen av uttryck går igenom en lista med booleska villkor. Det returnerar utdatavärdet för det första booleska villkoret som ska utvärderas till true.

Följande pseudokod definierar uttryckets `case` struktur. 

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

Följande exempel går igenom olika booleska villkor tills den hittar ett som utvärderas till `true`och returnerar sedan det associerade värdet. Om inget booleskt `true`villkor utvärderas till returneras ett återgångsvärde. 

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

### <a name="match-expression"></a>Matcha uttryck

Ett `match` uttryck är en typ av villkorsuttryck som ger switch-satsliknande logik. Indata kan vara ett `['get', 'entityType']` uttryck som det returnerar en sträng eller ett tal. Varje etikett måste vara antingen ett enda litteralt värde eller en matris med litterala värden, vars värden måste vara alla strängar eller alla tal. Indata matchar om något av värdena i matrisen matchar. Varje etikett måste vara unik. Om indatatypen inte matchar typen av etiketter blir resultatet reservvärdet.

Följande pseudokod definierar uttryckets `match` struktur. 

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

I följande exempel `entityType` visas egenskapen för en point-funktion i ett bubbellager som söker efter en matchning. Om den hittar en matchning returneras det angivna värdet eller returnerar reservvärdet.

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

I följande exempel används en matris för att lista en uppsättning etiketter som alla ska returnera samma värde. Detta tillvägagångssätt är mycket effektivare än att lista varje etikett individuellt. I detta fall, `entityType` om fastigheten är "restaurang" eller "grocery_store", kommer färgen "röd" att returneras.

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

I följande exempel används ett matchningsuttryck för att utföra ett "i matris" eller "matris innehåller" typfilter. I det här fallet filtrerar uttrycket data som har ett ID-värde som finns i en lista över tillåtna ID:er. När du använder uttryck med filter måste resultatet vara ett booleskt värde.

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

### <a name="coalesce-expression"></a>Coalesce uttryck

Ett `coalesce` uttryck går genom en uppsättning uttryck tills det första icke-null-värdet erhålls och returnerar det värdet. 

Följande pseudokod definierar uttryckets `coalesce` struktur. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Exempel**

I följande exempel `coalesce` används ett `textField` uttryck för att ange alternativet för ett symbollager. Om `title` egenskapen saknas i funktionen `null`eller är inställd på `subtitle` , kommer uttrycket `null`att försöka leta efter egenskapen, om den saknas eller , kommer den sedan att falla tillbaka till en tom sträng. 

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

I följande exempel `coalesce` används ett uttryck för att hämta den första tillgängliga bildikonen som finns i kartsprit från en lista med angivna bildnamn.

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

## <a name="type-expressions"></a>Skriva uttryck

Typuttryck innehåller verktyg för att testa och konvertera olika datatyper som strängar, tal och booleska värden.

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | matrisobjekt \| | Returnerar en litteral matris eller ett objektvärde. Använd det här uttrycket om du vill förhindra att en matris eller ett objekt utvärderas som ett uttryck. Detta är nödvändigt när en matris eller ett objekt måste returneras av ett uttryck. |
| `['image', string]` | sträng | Kontrollerar om ett angivet bild-ID läses in i kartbilden. I så fall returneras ID:et, annars returneras null. |
| `['to-boolean', value]` | boolean | Konverterar indatavärdet till en boolesk. Resultatet blir `false` när indata är `0`en `false` `null`tom `NaN`sträng, , , eller ; annars `true`dess . |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Konverterar indatavärdet till en färg. Om flera värden anges utvärderas var och en i ordning tills den första lyckade konverteringen erhålls. Om ingen av indata kan konverteras är uttrycket ett fel. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | nummer | Konverterar indatavärdet till ett tal, om möjligt. Om indata `null` `false`är eller blir resultatet 0. Om ingången `true`är , är resultatet 1. Om indata är en sträng konverteras den till ett tal med funktionen [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) i ECMAScript-språkspecifikationen. Om flera värden anges utvärderas var och en i ordning tills den första lyckade konverteringen erhålls. Om ingen av indata kan konverteras är uttrycket ett fel. |
| `['to-string', value]` | sträng | Konverterar indatavärdet till en sträng. Om indata `null`är , `""`blir resultatet . Om ingången är boolesk `"true"` blir `"false"`resultatet eller . Om indata är ett tal konverteras den till en sträng med funktionen [ToString-nummer](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) i ECMAScript-språkspecifikationen. Om indata är en färg konverteras den till CSS `"rgba(r,g,b,a)"`RGBA-färgsträng . Annars konverteras indata till en sträng med funktionen [JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) i ECMAScript Language Specification. |
| `['typeof', value]` | sträng | Returnerar en sträng som beskriver typen av det angivna värdet. |

> [!TIP]
> Om ett felmeddelande `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` som liknar visas i webbläsarkonsolen betyder det att det finns ett uttryck någonstans i koden som har en matris som inte har en sträng för sitt första värde. Om du vill att uttrycket ska returnera `literal` en matris radbryts matrisen med uttrycket. I följande exempel `offset` anges ikonalternativet för ett symbollager, som måste `match` vara en matris som innehåller två tal, genom att använda ett uttryck för att välja mellan två förskjutningsvärden baserat på värdet för egenskapen `entityType` för punktfunktionen.
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

## <a name="color-expressions"></a>Färguttryck

Färguttryck gör det enklare att skapa och manipulera färgvärden.

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Skapar ett färgvärde från *röda,* *gröna*och *blå* komponenter som måste variera mellan `0` och `255`, och en alfakomponent i `1`. Om någon komponent ligger utanför intervallet är uttrycket ett fel. |
| `['rgba', number, number, number, number]` | color | Skapar ett färgvärde från *röda,* *gröna,* *blå* `255`komponenter som måste variera mellan `0` `0` `1`och , och en alfakomponent inom ett intervall av och . Om någon komponent ligger utanför intervallet är uttrycket ett fel. |
| `['to-rgba']` | \[tal, tal, tal, tal\] | Returnerar en matris med fyra element som innehåller indatafärgens *röda,* *gröna,* *blå*och *alfakomponenter* i den ordningen. |

**Exempel**

I följande exempel skapas ett RGB-färgvärde `255`som har ett *rött* värde på och *gröna* `temperature` och *blå* värden som beräknas genom att `2.5` multiplicera med egenskapens värde. När temperaturen ändras, kommer färgen att ändras till olika nyanser av *rött*.

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

## <a name="string-operator-expressions"></a>Uttryck för strängoperator

Strängoperatoruttryck utför konverteringsåtgärder på strängar som att sammanfoga och konvertera ärendet. 

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['concat', string, string, …]` | sträng | Sammanfogar flera strängar tillsammans. Varje värde måste vara en sträng. Använd `to-string` typuttrycket för att konvertera andra värdetyper till sträng om det behövs. |
| `['downcase', string]` | sträng | Konverterar den angivna strängen till gemener. |
| `['upcase', string]` | sträng | Konverterar den angivna strängen till versaler. |

**Exempel**

I följande exempel `temperature` konverteras egenskapen för punktfunktionen till en sträng och sammanfogas sedan "°F" till slutet av den.

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

Ovanstående uttryck återger en stift på kartan med texten "64 °F" överlagrad ovanpå den som visas i bilden nedan.

<center>

![Exempel på](media/how-to-expressions/string-operator-expression.png) strängoperatoruttryck</center>

## <a name="interpolate-and-step-expressions"></a>Interpolera och stega uttryck

Interpolera och steguttryck kan användas för att beräkna värden längs en interpolerad kurva eller stegfunktion. Dessa uttryck tar i ett uttryck som returnerar ett `['get',  'temperature']`numeriskt värde som indata, till exempel . Indatavärdet utvärderas mot par av in- och utdatavärden för att bestämma det värde som bäst passar den interpolerade kurvan eller stegfunktionen. Utdatavärdena kallas "stopp". Indatavärdena för varje stopp måste vara ett tal och vara i stigande ordning. Utdatavärdena måste vara ett tal och en matris med tal eller en färg.

### <a name="interpolate-expression"></a>Interpolera uttryck

Ett `interpolate` uttryck kan användas för att beräkna en kontinuerlig, jämn uppsättning värden genom att interpolera mellan stoppvärden. Ett `interpolate` uttryck som returnerar färgvärden ger en färgövertoning där resultatvärden väljs från.

Det finns tre typer av interpolationsmetoder `interpolate` som kan användas i ett uttryck:
 
* `['linear']`- Interpolerar linjärt mellan stoppparet.
* `['exponential', base]`- Interpolerar exponentiellt mellan hållplatserna. Värdet `base` styr den hastighet med vilken utdata ökar. Högre värden gör att produktionen ökar mer mot den övre delen av intervallet. Ett `base` värde nära 1 ger en utdata som ökar mer linjärt.
* `['cubic-bezier', x1, y1, x2, y2]`- Interpolerar med hjälp av en [kubisk Bezier-kurva](https://developer.mozilla.org/docs/Web/CSS/timing-function) som definieras av de angivna kontrollpunkterna.

Här är ett exempel på hur dessa olika typer av interpolationer ser ut. 

| Linjär  | Exponentiell | Cubic Bezier |
|---------|-------------|--------------|
| ![Linjär interpolationsdiagram](media/how-to-expressions/linear-interpolation.png) | ![Exponentiell interpolation diagram](media/how-to-expressions/exponential-interpolation.png) | ![Kubisk Bezier interpolation diagram](media/how-to-expressions/bezier-curve-interpolation.png) |

Följande pseudokod definierar uttryckets `interpolate` struktur. 

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

I följande exempel `linear interpolate` används ett `color` uttryck för att ange `temperature` egenskapen för ett bubbellager baserat på egenskapen för punktfunktionen. Om `temperature` värdet är mindre än 60 returneras "blå". Om det är mellan 60 och mindre än 70, kommer gult att returneras. Om det är mellan 70 och mindre än 80, "orange" kommer att returneras. Om det är 80 eller högre, "röd" kommer att returneras.

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

Följande bild visar hur färgerna väljs för ovanstående uttryck.
 
<center>

![Interpolera uttrycksexempel](media/how-to-expressions/interpolate-expression-example.png)</center>

### <a name="step-expression"></a>Steguttryck

Ett `step` uttryck kan användas för att beräkna diskreta, stegiga resultatvärden genom att utvärdera en [bitvis konstant funktion](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) som definieras av stopp. 

Följande pseudokod definierar uttryckets `step` struktur. 

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

Steguttryck returnerar utdatavärdet för stoppet precis före indatavärdet, eller det första indatavärdet om indata är mindre än det första stoppet. 

**Exempel**

I följande exempel `step` används ett `color` uttryck för att ange `temperature` egenskapen för ett bubbellager baserat på egenskapen för punktfunktionen. Om `temperature` värdet är mindre än 60 returneras "blå". Om det är mellan 60 och mindre än 70, "gul" kommer att returneras. Om det är mellan 70 och mindre än 80, "orange" kommer att returneras. Om det är 80 eller högre, "röd" kommer att returneras.

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

Följande bild visar hur färgerna väljs för ovanstående uttryck.
 
<center>

![Exempel på steguttryck](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Lagerspecifika uttryck

Särskilda uttryck som bara gäller för specifika lager.

### <a name="heat-map-density-expression"></a>Uttryck för värmekarttäthet

Ett värmekarttäthetsuttryck hämtar värmekarttäthetsvärdet för varje pixel `['heatmap-density']`i ett värmekartlager och definieras som . Det här värdet `0` är `1`ett tal mellan och . Den används i kombination `interpolation` med `step` ett eller uttryck för att definiera färggradienten som används för att färglägga värmekartan. Detta uttryck kan endast användas i [färgalternativet](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) för värmekartlagret.

> [!TIP]
> Färgen på index 0, i ett interpolationsuttryck eller standardfärgen för en stegfärg, definierar färgen på det område där det inte finns några data. Färgen på index 0 kan användas för att definiera en bakgrundsfärg. Många föredrar att ställa in det här värdet till transparent eller halvtransparent svart.

**Exempel**

I det här exemplet används ett liner-interpolationsuttryck för att skapa en jämn färgövertoning för rendering av värmekartan. 

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

Förutom att använda en jämn övertoning för att färglägga en värmekarta kan `step` färger anges inom en uppsättning intervall med hjälp av ett uttryck. Om `step` du använder ett uttryck för att färglägga värmekartan bryts densiteten visuellt upp i områden som liknar en kontur- eller radarstilskarta.  

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

Mer information finns i dokumentationen [Lägg till ett värmekartlager.](map-add-heat-map-layer.md)

### <a name="line-progress-expression"></a>Uttryck för radförlopp

Ett radförloppsuttryck hämtar förloppet längs en övertoningslinje i ett linjelager och definieras som `['line-progress']`. Det här värdet är ett tal mellan 0 och 1. Den används i kombination `interpolation` med `step` ett eller ett uttryck. Det här uttrycket kan bara användas med [alternativet linjeGradient]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) i linjelagret. 

> [!NOTE]
> Alternativet `strokeGradient` för linjelagret `lineMetrics` kräver att alternativet för datakällan ställs in på `true`.

**Exempel**

I det `['line-progress']` här exemplet används uttrycket för att använda en färgövertoning på linjens linje.

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

[Se levande exempel](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Uttryck för textfältsformat

Textfältsformatuttrycket kan användas `textField` med alternativet `textOptions` egenskapen symbollager för att ge blandad textformatering. Med det här uttrycket kan en uppsättning indatasträngar och formateringsalternativ anges. Följande alternativ kan anges för varje indatasträng i det här uttrycket.

 * `'font-scale'`- Anger skalningsfaktorn för teckenstorleken. Om det anges åsidosätter `size` det här `textOptions` värdet egenskapen för den enskilda strängen.
 * `'text-font'`- Anger en eller flera teckensnittsfamiljer som ska användas för den här strängen. Om det anges åsidosätter `font` det här `textOptions` värdet egenskapen för den enskilda strängen.
 * `'text-color'`- Anger en färg som ska tillämpas på en text när den återges. 

Följande pseudokod definierar strukturen för textfältsformatuttrycket. 

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

I följande exempel formateras textfältet genom att lägga till ett `title` fetstilt teckensnitt och teckenstorleken för funktionens egenskap skalas upp. I det här `subtitle` exemplet läggs också funktionens egenskap till på en nyrad, med en nedskalad teckenstorlek och färgad röd.

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

Det här lagret återger punktfunktionen enligt bilden nedan:
 
<center>

![Bild av funktionen Punkt med](media/how-to-expressions/text-field-format-expression.png) formaterat textfält</center>

### <a name="number-format-expression"></a>Talformat uttryck

Uttrycket `number-format` kan bara användas `textField` med alternativet ett symbollager. Det här uttrycket konverterar det angivna talet till en formaterad sträng. Det här uttrycket radbryts i [JavaScript:s number.toLocalString-funktion](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) och stöder följande uppsättning alternativ.

 * `locale`- Ange det här alternativet för att konvertera tal till strängar på ett sätt som justeras mot det angivna språket. Skicka en [BCP 47-språktagg](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) till det här alternativet.
 * `currency`- Om du vill konvertera talet till en sträng som representerar en valuta. Möjliga värden är [ISO 4217 valutakoder](https://en.wikipedia.org/wiki/ISO_4217), till exempel "USD" för den amerikanska dollarn, "EUR" för euron, eller "CNY" för den kinesiska RMB.
 * `'min-fraction-digits'`- Anger det minsta antalet decimaler som ska inkluderas i strängversionen av talet.
 * `'max-fraction-digits'`- Anger det maximala antalet decimaler som ska inkluderas i strängversionen av talet.

Följande pseudokod definierar strukturen för textfältsformatuttrycket. 

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

I följande exempel `number-format` används ett `revenue` uttryck för att ändra hur `textField` egenskapen för punktfunktionen återges i alternativet för ett symbollager så att det ser ut som ett us-dollarvärde.

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

Det här lagret återger punktfunktionen enligt bilden nedan:

<center>

![Exempel på](media/how-to-expressions/number-format-expression.png) talformatsuttryck</center>

### <a name="image-expression"></a>Bilduttryck

Ett bilduttryck kan `image` användas `textField` med alternativen för ett `fillPattern` symbollager och alternativet för polygonlagret. Det här uttrycket kontrollerar att den begärda bilden finns `null`i formatet och returnerar antingen det lösta bildnamnet eller , beroende på om bilden för närvarande är i formatet eller inte. Den här valideringsprocessen är synkron och kräver att bilden har lagts till i formatet innan den begärs i bildargumentet.

**Exempel**

I följande exempel `image` används ett uttryck för att lägga till en ikon infogad med text i ett symbollager. 

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));

    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

Det här lagret återger textfältet i symbollagret enligt bilden nedan:

<center>

![Exempel på](media/how-to-expressions/image-expression.png) bilduttryck</center>

## <a name="zoom-expression"></a>Zooma uttryck

Ett `zoom` uttryck används för att hämta den aktuella zoomnivån `['zoom']`på kartan vid renderingstillfället och definieras som . Det här uttrycket returnerar ett tal mellan kartans minsta och högsta zoomnivå. Azure Maps interaktiva kartkontroller för webb- och Android-stöd för 25 zoomnivåer, numrerade 0 till 24. Med `zoom` hjälp av uttrycket kan formaten ändras dynamiskt när kartans zoomnivå ändras. Uttrycket `zoom` får endast `interpolate` användas `step` med och uttryck.

**Exempel**

Som standard har raderna för datapunkter som återges i värmekartlagret en fast pixelradie för alla zoomnivåer. När kartan zoomas samman sammanställs data och värmekartlagret ser annorlunda ut. Ett `zoom` uttryck kan användas för att skala radien för varje zoomnivå så att varje datapunkt täcker samma fysiska område på kartan. Det kommer att göra värmekartan lagret ser mer statisk och konsekvent. Varje zoomnivå på kartan har dubbelt så många pixlar vertikalt och horisontellt som föregående zoomnivå. Skala radien, så att den fördubblas med varje zoomnivå, skapar en värmekarta som ser konsekvent ut på alla zoomnivåer. Det kan åstadkommas `zoom` med hjälp `base 2 exponential interpolation` av uttrycket med ett uttryck som visas nedan. 

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

[Se levande exempel](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Variabla bindningsuttryck

Variabla bindningsuttryck lagrar resultatet av en beräkning i en variabel. Så att beräkningsresultaten kan refereras någon annanstans i ett uttryck flera gånger. Det är en användbar optimering för uttryck som involverar många beräkningar.

| Uttryck | Returtyp | Beskrivning |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"låt",<br/>&nbsp;&nbsp;&nbsp;&nbsp;namn1: sträng,<br/>&nbsp;&nbsp;&nbsp;&nbsp;Värde1: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;namn2: sträng,<br/>&nbsp;&nbsp;&nbsp;&nbsp;värde2: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;barnUttryck<br/>\] | | Lagrar ett eller flera värden som `var` variabler för användning av uttrycket i det underordnade uttrycket som returnerar resultatet. |
| `['var', name: string]` | valfri | Refererar till en variabel `let` som skapades med uttrycket. |

**Exempel**

I det här exemplet används ett uttryck som beräknar `case` intäkten i förhållande till temperatur och sedan använder ett uttryck för att utvärdera olika booleska operationer på det här värdet. Uttrycket `let` används för att lagra intäkten i förhållande till temperatur, så att den bara behöver beräknas en gång. Uttrycket `var` refererar till den här variabeln så ofta som behövs utan att behöva räkna om den.

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

Se följande artiklar för fler kodexempel som implementerar uttryck:

> [!div class="nextstepaction"] 
> [Lägga till ett symbolskikt](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Lägga till ett bubbelskikt](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett linjeskikt](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Lägga till ett heatmapskikt](map-add-heat-map-layer.md)

Läs mer om de lageralternativ som stöder uttryck:

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
