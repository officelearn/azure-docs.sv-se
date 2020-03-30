---
title: Skapa ui definitionsfunktioner
description: Beskriver de funktioner som ska användas när användargränssnittsdefinitioner för Azure Managed Applications skapas
author: tfitzmac
ms.topic: conceptual
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 6e56c5e528a17d42a75da54158f00857a917645c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248455"
---
# <a name="createuidefinition-functions"></a>Funktioner i CreateUiDefinition
Det här avsnittet innehåller signaturer för alla funktioner som stöds i en CreateUiDefinition.

Om du vill använda en funktion omger du deklarationen med hakparenteser. Ett exempel:

```json
"[function()]"
```

Strängar och andra funktioner kan refereras som parametrar för en funktion, men strängar måste omges av enstaka citattecken. Ett exempel:

```json
"[fn1(fn2(), 'foobar')]"
```

I förekommande fall kan du referera till egenskaper för utdata för en funktion med hjälp av punktoperatorn. Ett exempel:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Referera till funktioner
Dessa funktioner kan användas för att referera till utdata från egenskaperna eller kontexten för en CreateUiDefinition.

### <a name="basics"></a>Grunderna
Returnerar utdatavärdena för ett element som definieras i steget Grunderna.

I följande exempel returneras utdata för elementet som nämns `foo` i steget Grunderna:

```json
"[basics('foo')]"
```

### <a name="steps"></a>steg
Returnerar utdatavärdena för ett element som definieras i det angivna steget. Om du vill hämta utdatavärdena för `basics()` element i steget Grunderna använder du i stället.

I följande exempel returneras utdata för elementet med namnet `bar` i steget `foo`:

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Returnerar den plats som valts i steget Grunderna eller den aktuella kontexten.

Följande exempel kan `"westus"`returneras:

```json
"[location()]"
```

## <a name="string-functions"></a>Strängfunktioner
Dessa funktioner kan endast användas med JSON-strängar.

### <a name="concat"></a>Concat
Sammanfogar en eller flera strängar.

Om till exempel `element1` utdatavärdet `"bar"`för om , `"foobar!"`returnerar det här exemplet strängen:

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>Delsträng
Returnerar delsträngen för den angivna strängen. Delsträngen börjar vid det angivna indexet och har den angivna längden.

Följande exempel `"ftw"`returnerar:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>Ersätta
Returnerar en sträng där alla förekomster av den angivna strängen i den aktuella strängen ersätts med en annan sträng.

Följande exempel `"Everything is awesome!"`returnerar:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>Guid
Genererar en globalt unik sträng (GUID).

Följande exempel kan `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`returneras:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower (TillLågare)
Returnerar en sträng som konverterats till gemener.

Följande exempel `"foobar"`returnerar:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>tillUpper
Returnerar en sträng som konverterats till versaler.

Följande exempel `"FOOBAR"`returnerar:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Insamlingsfunktioner
Dessa funktioner kan användas med samlingar, som JSON-strängar, matriser och objekt.

### <a name="contains"></a>innehåller
Returnerar `true` om en sträng innehåller den angivna delsträngen, en matris innehåller det angivna värdet eller ett objekt innehåller den angivna nyckeln.

#### <a name="example-1-string"></a>Exempel 1: sträng
Följande exempel `true`returnerar:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
`element1` Antag `[1, 2, 3]`returer . Följande exempel `false`returnerar:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>Exempel 3: objekt
Antag `element1` returer:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Följande exempel `true`returnerar:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>length
Returnerar antalet tecken i en sträng, antalet värden i en matris eller antalet nycklar i ett objekt.

#### <a name="example-1-string"></a>Exempel 1: sträng
Följande exempel `6`returnerar:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
`element1` Antag `[1, 2, 3]`returer . Följande exempel `3`returnerar:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exempel 3: objekt
Antag `element1` returer:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Följande exempel `2`returnerar:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>tomt
Returnerar `true` om strängen, matrisen eller objektet är null eller tomt.

#### <a name="example-1-string"></a>Exempel 1: sträng
Följande exempel `true`returnerar:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
`element1` Antag `[1, 2, 3]`returer . Följande exempel `false`returnerar:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exempel 3: objekt
Antag `element1` returer:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Följande exempel `false`returnerar:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Exempel 4: null och odefinierad
`element1` Antag `null` är eller odefinierad. Följande exempel `true`returnerar:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>Första
Returnerar det första tecknet i den angivna strängen. Det första värdet för den angivna matrisen. eller den första nyckeln och värdet för det angivna objektet.

#### <a name="example-1-string"></a>Exempel 1: sträng
Följande exempel `"f"`returnerar:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
`element1` Antag `[1, 2, 3]`returer . Följande exempel `1`returnerar:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exempel 3: objekt
Antag `element1` returer:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
Följande exempel `{"key1": "foobar"}`returnerar:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>Senaste
Returnerar det sista tecknet i den angivna strängen, det sista värdet för den angivna matrisen eller den sista nyckeln och värdet för det angivna objektet.

#### <a name="example-1-string"></a>Exempel 1: sträng
Följande exempel `"r"`returnerar:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
`element1` Antag `[1, 2, 3]`returer . Följande exempel `2`returnerar:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exempel 3: objekt
Antag `element1` returer:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Följande exempel `{"key2": "raboof"}`returnerar:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>Ta
Returnerar ett angivet antal sammanhängande tecken från början av strängen, ett angivet antal sammanhängande värden från början av matrisen eller ett angivet antal sammanhängande nycklar och värden från objektets början.

#### <a name="example-1-string"></a>Exempel 1: sträng
Följande exempel `"foo"`returnerar:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
`element1` Antag `[1, 2, 3]`returer . Följande exempel `[1, 2]`returnerar:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Exempel 3: objekt
Antag `element1` returer:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Följande exempel `{"key1": "foobar"}`returnerar:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>hoppa över
Kringgår ett angivet antal element i en samling och returnerar sedan de återstående elementen.

#### <a name="example-1-string"></a>Exempel 1: sträng
Följande exempel `"bar"`returnerar:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
`element1` Antag `[1, 2, 3]`returer . Följande exempel `[3]`returnerar:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Exempel 3: objekt
Antag `element1` returer:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
Följande exempel `{"key2": "raboof"}`returnerar:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Logiska funktioner
Dessa funktioner kan användas i villkor. Vissa funktioner kanske inte stöder alla JSON-datatyper.

### <a name="equals"></a>lika med
Returnerar `true` om båda parametrarna har samma typ och värde. Den här funktionen stöder alla JSON-datatyper.

Följande exempel `true`returnerar:

```json
"[equals(0, 0)]"
```

Följande exempel `true`returnerar:

```json
"[equals('foo', 'foo')]"
```

Följande exempel `false`returnerar:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>mindre än
Returnerar `true` om den första parametern är betydligt mindre än den andra parametern. Den här funktionen stöder endast parametrar av typnummer och sträng.

Följande exempel `true`returnerar:

```json
"[less(1, 2)]"
```

Följande exempel `false`returnerar:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>mindre än eller lika med
Returnerar `true` om den första parametern är mindre än eller lika med den andra parametern. Den här funktionen stöder endast parametrar av typnummer och sträng.

Följande exempel `true`returnerar:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>större än
Returnerar `true` om den första parametern är strikt större än den andra parametern. Den här funktionen stöder endast parametrar av typnummer och sträng.

Följande exempel `false`returnerar:

```json
"[greater(1, 2)]"
```

Följande exempel `true`returnerar:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>större än eller lika med
Returnerar `true` om den första parametern är större än eller lika med den andra parametern. Den här funktionen stöder endast parametrar av typnummer och sträng.

Följande exempel `true`returnerar:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>och
Returnerar `true` om alla `true`parametrar utvärderas till . Den här funktionen stöder endast två eller flera parametrar av typen Boolean.

Följande exempel `true`returnerar:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Följande exempel `false`returnerar:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>eller
Returnerar `true` om minst en av `true`parametrarna utvärderas till . Den här funktionen stöder endast två eller flera parametrar av typen Boolean.

Följande exempel `true`returnerar:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Följande exempel `true`returnerar:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>inte
Returnerar `true` om parametern `false`utvärderas till . Den här funktionen stöder endast parametrar av typen Boolean.

Följande exempel `true`returnerar:

```json
"[not(false)]"
```

Följande exempel `false`returnerar:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>smälter samman
Returnerar värdet för den första parametern som inte är null. Den här funktionen stöder alla JSON-datatyper.

`element1` Antag `element2` och är odefinierade. Följande exempel `"foobar"`returnerar:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Konverteringsfunktioner
Dessa funktioner kan användas för att konvertera värden mellan JSON-datatyper och kodningar.

### <a name="int"></a>int
Konverterar parametern till ett heltal. Den här funktionen stöder parametrar för typnummer och sträng.

Följande exempel `1`returnerar:

```json
"[int('1')]"
```

Följande exempel `2`returnerar:

```json
"[int(2.9)]"
```

### <a name="float"></a>float
Konverterar parametern till en flyttals. Den här funktionen stöder parametrar för typnummer och sträng.

Följande exempel `1.0`returnerar:

```json
"[float('1.0')]"
```

Följande exempel `2.9`returnerar:

```json
"[float(2.9)]"
```

### <a name="string"></a>sträng
Konverterar parametern till en sträng. Den här funktionen stöder parametrar för alla JSON-datatyper.

Följande exempel `"1"`returnerar:

```json
"[string(1)]"
```

Följande exempel `"2.9"`returnerar:

```json
"[string(2.9)]"
```

Följande exempel `"[1,2,3]"`returnerar:

```json
"[string([1,2,3])]"
```

Följande exempel `"{"foo":"bar"}"`returnerar:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>bool
Konverterar parametern till en boolesk. Den här funktionen stöder parametrar för typnummer, sträng och boolesk. Liknar booleans i JavaScript, `0` alla `'false'` `true`värden utom eller returnerar .

Följande exempel `true`returnerar:

```json
"[bool(1)]"
```

Följande exempel `false`returnerar:

```json
"[bool(0)]"
```

Följande exempel `true`returnerar:

```json
"[bool(true)]"
```

Följande exempel `true`returnerar:

```json
"[bool('true')]"
```

### <a name="parse"></a>parse
Konverterar parametern till en inbyggd typ. Med andra ord är denna funktion `string()`motsatsen till . Den här funktionen stöder endast parametrar av typsträng.

Följande exempel `1`returnerar:

```json
"[parse('1')]"
```

Följande exempel `true`returnerar:

```json
"[parse('true')]"
```

Följande exempel `[1,2,3]`returnerar:

```json
"[parse('[1,2,3]')]"
```

Följande exempel `{"foo":"bar"}`returnerar:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>kodBase64
Kodar parametern till en bas-64-kodad sträng. Den här funktionen stöder endast parametrar av typsträng.

Följande exempel `"Zm9vYmFy"`returnerar:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>avkodaBase64
Avkodar parametern från en bas-64-kodad sträng. Den här funktionen stöder endast parametrar av typsträng.

Följande exempel `"foobar"`returnerar:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
Kodar parametern till en URL-kodad sträng. Den här funktionen stöder endast parametrar av typsträng.

Följande exempel `"https%3A%2F%2Fportal.azure.com%2F"`returnerar:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>avkodaUriComponent
Avkodar parametern från en URL-kodad sträng. Den här funktionen stöder endast parametrar av typsträng.

Följande exempel `"https://portal.azure.com/"`returnerar:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Matematikfunktioner
### <a name="add"></a>add
Lägger till två tal och returnerar resultatet.

Följande exempel `3`returnerar:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>Sub
Subtraherar det andra talet från det första talet och returnerar resultatet.

Följande exempel `1`returnerar:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul (mul)
Multiplicerar två tal och returnerar resultatet.

Följande exempel `6`returnerar:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Dividerar det första talet med det andra talet och returnerar resultatet. Resultatet är alltid ett heltal.

Följande exempel `2`returnerar:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>Mod
Dividerar det första talet med det andra talet och returnerar resten.

Följande exempel `0`returnerar:

```json
"[mod(6, 3)]"
```

Följande exempel `2`returnerar:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>min
Returnerar den lilla av de två talen.

Följande exempel `1`returnerar:

```json
"[min(1, 2)]"
```

### <a name="max"></a>Max
Returnerar det större av de två talen.

Följande exempel `2`returnerar:

```json
"[max(1, 2)]"
```

### <a name="range"></a>Utbud
Genererar en sekvens av integraltal inom det angivna intervallet.

Följande exempel `[1,2,3]`returnerar:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>Rand
Returnerar ett slumpmässigt integraltal inom det angivna intervallet. Den här funktionen genererar inte kryptografiskt säkra slumptal.

Följande exempel kan `42`returneras:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>Golvet
Returnerar det största heltalet som är mindre än eller lika med det angivna talet.

Följande exempel `3`returnerar:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>Ceil
Returnerar det största heltalet som är större än eller lika med det angivna talet.

Följande exempel `4`returnerar:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Datumfunktioner
### <a name="utcnow"></a>utcNow (olikartade)
Returnerar en sträng i ISO 8601-format för aktuellt datum och tid på den lokala datorn.

Följande exempel kan `"1990-12-31T23:59:59.000Z"`returneras:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
Lägger till ett integrerat antal sekunder i den angivna tidsstämpeln.

Följande exempel `"1991-01-01T00:00:00.000Z"`returnerar:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Lägger till ett integrerat antal minuter i den angivna tidsstämpeln.

Följande exempel `"1991-01-01T00:00:59.000Z"`returnerar:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
Lägger till ett integrerat antal timmar i den angivna tidsstämpeln.

Följande exempel `"1991-01-01T00:59:59.000Z"`returnerar:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till Azure Resource Manager finns i [Översikt över Azure Resource Manager](../management/overview.md).

