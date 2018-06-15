---
title: Azure-hanterade program skapa UI definition funktioner | Microsoft Docs
description: Beskriver funktionerna som ska användas när man skapar UI definitioner för hanterade program i Azure
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: a01a59a7e8c9757cb41d328cd26a34fa219f9152
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34304512"
---
# <a name="createuidefinition-functions"></a>CreateUiDefinition funktioner
Det här avsnittet innehåller signaturer för alla funktioner som stöds av en CreateUiDefinition.

Om du vill använda en funktion måste omges av deklaration med hakparenteser. Exempel:

```json
"[function()]"
```

Strängar och andra funktioner kan refereras som parametrar för en funktion, men strängar måste omges av enkla citattecken. Exempel:

```json
"[fn1(fn2(), 'foobar')]"
```

Om tillämpligt, kan du referera egenskaper för utdata för en funktion med hjälp av punktoperatorn. Exempel:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Referensfunktioner
Dessa funktioner kan användas för att referera till utdata från de egenskaper eller kontexten för en CreateUiDefinition.

### <a name="basics"></a>Grunderna
Returnerar utdatavärden för ett element som har definierats i grundläggande steg.

I följande exempel returneras resultatet av element med namnet `foo` i grundläggande steg:

```json
"[basics('foo')]"
```

### <a name="steps"></a>steg
Returnerar utdatavärden för ett element som har definierats i det angivna steget. För att få utdatavärden element i grundläggande steg kan använda `basics()` i stället.

I följande exempel returneras resultatet av element med namnet `bar` i steg med namnet `foo`:

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Returnerar den plats som valts i steget grunderna eller den aktuella kontexten.

I följande exempel kunde returnera `"westus"`:

```json
"[location()]"
```

## <a name="string-functions"></a>Strängfunktioner
Dessa funktioner kan endast användas med JSON-strängar.

### <a name="concat"></a>concat
Sammanfogar en eller flera strängar.

Till exempel om värdet för `element1` om `"bar"`, och sedan det här exemplet returnerar strängen `"foobar!"`:

```json
"[concat('foo', steps('step1').element1), '!']"
```

### <a name="substring"></a>delsträngen
Returnerar delsträngen av den angivna strängen. Delsträngen som börjar med det angivna indexet och har den angivna längden.

I följande exempel returneras `"ftw"`:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>Ersätt
Returnerar en sträng som ersätts alla förekomster av den angivna strängen i den aktuella strängen med en annan sträng.

I följande exempel returneras `"Everything is awesome!"`:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>GUID
Genererar en globalt unik sträng (GUID).

I följande exempel kunde returnera `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Returnerar en sträng som konverterats till gemener.

I följande exempel returneras `"foobar"`:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Returnerar en sträng som är konverterad till versaler.

I följande exempel returneras `"FOOBAR"`:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Samlingen funktioner
Dessa funktioner kan användas med samlingar som JSON-strängar, matriser och -objekt.

### <a name="contains"></a>innehåller
Returnerar `true` om en sträng som innehåller den angivna delsträngen, en matris som innehåller det angivna värdet eller ett objekt som innehåller den angivna nyckeln.

#### <a name="example-1-string"></a>Exempel 1: sträng
I följande exempel returneras `true`:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
Anta att `element1` returnerar `[1, 2, 3]`. I följande exempel returneras `false`:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>Exempel 3: objektet
Anta att `element1` returnerar:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

I följande exempel returneras `true`:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>Längd
Returnerar antalet tecken i en sträng, antalet värden i en matris eller antalet nycklar i ett objekt.

#### <a name="example-1-string"></a>Exempel 1: sträng
I följande exempel returneras `6`:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
Anta att `element1` returnerar `[1, 2, 3]`. I följande exempel returneras `3`:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exempel 3: objektet
Anta att `element1` returnerar:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

I följande exempel returneras `2`:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>tom
Returnerar `true` om den sträng, en matris eller ett objekt är null eller tomt.

#### <a name="example-1-string"></a>Exempel 1: sträng
I följande exempel returneras `true`:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
Anta att `element1` returnerar `[1, 2, 3]`. I följande exempel returneras `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exempel 3: objektet
Anta att `element1` returnerar:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

I följande exempel returneras `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Exempel 4: null och odefinierad
Anta att `element1` är `null` eller odefinierad. I följande exempel returneras `true`:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>första
Returnerar det första tecknet i den angivna strängen; första värde för den angivna matrisen; eller den första nyckel och värde för det angivna objektet.

#### <a name="example-1-string"></a>Exempel 1: sträng
I följande exempel returneras `"f"`:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
Anta att `element1` returnerar `[1, 2, 3]`. I följande exempel returneras `1`:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exempel 3: objektet
Anta att `element1` returnerar:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
I följande exempel returneras `{"key1": "foobar"}`:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>senaste
Returnerar det sista tecknet i den angivna strängen, det senaste värdet för den angivna matrisen eller den senaste nyckeln och värdet för det angivna objektet.

#### <a name="example-1-string"></a>Exempel 1: sträng
I följande exempel returneras `"r"`:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
Anta att `element1` returnerar `[1, 2, 3]`. I följande exempel returneras `2`:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exempel 3: objektet
Anta att `element1` returnerar:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

I följande exempel returneras `{"key2": "raboof"}`:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>ta
Returnerar ett angivet antal sammanhängande tecken från början av strängen, ett angivet antal sammanhängande värden från början av matrisen eller ett angivet antal sammanhängande nycklar och värden från början av objektet.

#### <a name="example-1-string"></a>Exempel 1: sträng
I följande exempel returneras `"foo"`:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
Anta att `element1` returnerar `[1, 2, 3]`. I följande exempel returneras `[1, 2]`:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Exempel 3: objektet
Anta att `element1` returnerar:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

I följande exempel returneras `{"key1": "foobar"}`:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>Hoppa över
Kringgår ett angivet antal element i en mängd och returnerar sedan de återstående element.

#### <a name="example-1-string"></a>Exempel 1: sträng
I följande exempel returneras `"bar"`:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
Anta att `element1` returnerar `[1, 2, 3]`. I följande exempel returneras `[3]`:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Exempel 3: objektet
Anta att `element1` returnerar:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
I följande exempel returneras `{"key2": "raboof"}`:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Logiska funktioner
Dessa funktioner kan användas i villkorlig sats. Vissa funktioner kanske inte stöder alla JSON-datatyper.

### <a name="equals"></a>lika med
Returnerar `true` om båda parametrarna har samma typ och värde. Den här funktionen stöder alla JSON-datatyper.

I följande exempel returneras `true`:

```json
"[equals(0, 0)]"
```

I följande exempel returneras `true`:

```json
"[equals('foo', 'foo')]"
```

I följande exempel returneras `false`:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>mindre
Returnerar `true` om den första parametern är strikt mindre än den andra parametern. Den här funktionen har stöd för parametrarna endast för nummer och strängen.

I följande exempel returneras `true`:

```json
"[less(1, 2)]"
```

I följande exempel returneras `false`:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Returnerar `true` om den första parametern är mindre än eller lika med den andra parametern. Den här funktionen har stöd för parametrarna endast för nummer och strängen.

I följande exempel returneras `true`:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>större
Returnerar `true` om den första parametern är strikt större än den andra parametern. Den här funktionen har stöd för parametrarna endast för nummer och strängen.

I följande exempel returneras `false`:

```json
"[greater(1, 2)]"
```

I följande exempel returneras `true`:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Returnerar `true` om den första parametern är större än eller lika med den andra parametern. Den här funktionen har stöd för parametrarna endast för nummer och strängen.

I följande exempel returneras `true`:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>och
Returnerar `true` om alla parametrar som utvärderas till `true`. Den här funktionen har stöd för två eller flera parametrar av typen Boolean.

I följande exempel returneras `true`:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

I följande exempel returneras `false`:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>eller
Returnerar `true` om minst en av parametrarna evalueras till `true`. Den här funktionen har stöd för två eller flera parametrar av typen Boolean.

I följande exempel returneras `true`:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

I följande exempel returneras `true`:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>inte
Returnerar `true` om parametern evalueras till `false`. Den här funktionen har stöd för parametrar av typen Boolean.

I följande exempel returneras `true`:

```json
"[not(false)]"
```

I följande exempel returneras `false`:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>Slå samman
Returnerar värdet för den första icke-null-parametern. Den här funktionen stöder alla JSON-datatyper.

Anta att `element1` och `element2` är odefinierad. I följande exempel returneras `"foobar"`:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Konverteringsfunktioner
Dessa funktioner kan användas för att konvertera värden mellan JSON-datatyper och kodningar.

### <a name="int"></a>int
Konverterar parametern till ett heltal. Den här funktionen har stöd för parametrar av typen nummer och strängen.

I följande exempel returneras `1`:

```json
"[int('1')]"
```

I följande exempel returneras `2`:

```json
"[int(2.9)]"
```

### <a name="float"></a>flyt
Konverterar parametern till ett flyttal. Den här funktionen har stöd för parametrar av typen nummer och strängen.

I följande exempel returneras `1.0`:

```json
"[float('1.0')]"
```

I följande exempel returneras `2.9`:

```json
"[float(2.9)]"
```

### <a name="string"></a>sträng
Konverterar parametern till en sträng. Den här funktionen stöder alla datatyper i JSON-parametrar.

I följande exempel returneras `"1"`:

```json
"[string(1)]"
```

I följande exempel returneras `"2.9"`:

```json
"[string(2.9)]"
```

I följande exempel returneras `"[1,2,3]"`:

```json
"[string([1,2,3])]"
```

I följande exempel returneras `"{"foo":"bar"}"`:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>bool
Konverterar parametern till ett booleskt värde. Den här funktionen har stöd för parametrar av typen antal, sträng och booleskt värde. Något värde liknar booleska värden i JavaScript, utom `0` eller `'false'` returnerar `true`.

I följande exempel returneras `true`:

```json
"[bool(1)]"
```

I följande exempel returneras `false`:

```json
"[bool(0)]"
```

I följande exempel returneras `true`:

```json
"[bool(true)]"
```

I följande exempel returneras `true`:

```json
"[bool('true')]"
```

### <a name="parse"></a>parsa
Konverterar parametern till en inbyggd typ. I den här funktionen är med andra ord inversen av `string()`. Den här funktionen stöder endast av typen string-parametrar.

I följande exempel returneras `1`:

```json
"[parse('1')]"
```

I följande exempel returneras `true`:

```json
"[parse('true')]"
```

I följande exempel returneras `[1,2,3]`:

```json
"[parse('[1,2,3]')]"
```

I följande exempel returneras `{"foo":"bar"}`:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
Kodar parametern för en Base64-kodad sträng. Den här funktionen stöder endast av typen string-parametrar.

I följande exempel returneras `"Zm9vYmFy"`:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
Avkodar parametern från en Base64-kodad sträng. Den här funktionen stöder endast av typen string-parametrar.

I följande exempel returneras `"foobar"`:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
Kodar parametern till en URL-kodad sträng. Den här funktionen stöder endast av typen string-parametrar.

I följande exempel returneras `"https%3A%2F%2Fportal.azure.com%2F"`:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
Avkodar parametern från en URL-kodad sträng. Den här funktionen stöder endast av typen string-parametrar.

I följande exempel returneras `"https://portal.azure.com/"`:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Matematikfunktioner
### <a name="add"></a>lägg till
Adderar två tal och returnerar resultatet.

I följande exempel returneras `3`:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>Sub
Subtraherar andra tal från det första och returnerar resultatet.

I följande exempel returneras `1`:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
Multiplicerar två tal och returnerar resultatet.

I följande exempel returneras `6`:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Delar det första talet av den andra siffran, och returnerar resultatet. Resultatet är alltid ett heltal.

I följande exempel returneras `2`:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>MOD
Delar det första talet av den andra siffran, och returnerar resten.

I följande exempel returneras `0`:

```json
"[mod(6, 3)]"
```

I följande exempel returneras `2`:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>min.
Returnerar små av de två talen.

I följande exempel returneras `1`:

```json
"[min(1, 2)]"
```

### <a name="max"></a>max
Returnerar högre av de två talen.

I följande exempel returneras `2`:

```json
"[max(1, 2)]"
```

### <a name="range"></a>intervallet
Genererar en sekvens med integrerad som ligger inom det angivna intervallet.

I följande exempel returneras `[1,2,3]`:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>SLUMP
Returnerar ett slumptal integrerad inom det angivna intervallet. Den här funktionen genererar inte kryptografiskt säker slumptal.

I följande exempel kunde returnera `42`:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>våning
Returnerar det största heltalet mindre än eller lika med det angivna värdet.

I följande exempel returneras `3`:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
Returnerar det största heltalet större än eller lika med det angivna värdet.

I följande exempel returneras `4`:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Datumfunktioner
### <a name="utcnow"></a>utcNow
Returnerar en sträng i ISO 8601-format för den aktuella datum och tid på den lokala datorn.

I följande exempel kunde returnera `"1990-12-31T23:59:59.000Z"`:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>Lägg_till_sekunder
Lägger till ett heltal sekunder till den angivna tidsstämpeln.

I följande exempel returneras `"1991-01-01T00:00:00.000Z"`:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Lägger till ett heltal minuter till den angivna tidsstämpeln.

I följande exempel returneras `"1991-01-01T00:00:59.000Z"`:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
Lägger till ett heltal timmar till den angivna tidsstämpeln.

I följande exempel returneras `"1991-01-01T00:59:59.000Z"`:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till Azure Resource Manager finns [översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

