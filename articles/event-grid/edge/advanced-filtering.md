---
title: Avancerad filtrering – Azure Event Grid IoT Edge | Microsoft-dokument
description: Avancerad filtrering i händelserutnät på IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d7fdc5074f3c92eea4f236a9b1f7c823b930f391
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72992566"
---
# <a name="advanced-filtering"></a>Avancerad filtrering
Med Event Grid kan du ange filter på valfri egenskap i json-nyttolasten. Dessa filter modelleras `AND` som en uppsättning villkor, `OR` där varje yttre tillstånd har valfria inre förhållanden. För `AND` varje villkor anger du följande värden:

* `OperatorType`- Typen av jämförelse.
* `Key`- Json-sökvägen till den egenskap som filtret ska användas på.
* `Value`- Referensvärdet som filtret körs mot `Values` (eller) - Den uppsättning referensvärden som filtret körs mot.

## <a name="json-syntax"></a>JSON syntax

JSON-syntaxen för ett avancerat filter är följande:

```json
{
    "filter": {
        "advancedFilters": [{
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key1",
                "value": 5
            }, {
                "operatorType": "StringContains",
                "key": "Subject",
                "values": ["container1", "container2"]
            }
        ]
    }
}
```

## <a name="filtering-on-array-values"></a>Filtrering på matrisvärden

Event Grid stöder inte filtrering på en matris med värden idag. Om en inkommande händelse har ett matrisvärde för det avancerade filtrets nyckel misslyckas matchningsåtgärden. Den inkommande händelsen slutar inte matcha med händelseprenumerationen.

## <a name="and-or-not-semantics"></a>OCH-ELLER-INTE semantik

Observera att i json-exemplet `AdvancedFilters` som gavs tidigare är en matris. Tänk på `AdvancedFilter` varje matriselement som ett `AND` villkor.

För operatorer `NumberIn`som stöder flera värden `NumberNotIn` `StringIn`(till exempel , , , `OR` etc.) behandlas varje värde som ett villkor. Så kommer `StringBeginsWith("a", "b", "c")` en matchar alla strängvärde `a` `b` som `c`börjar med antingen eller eller .

> [!CAUTION]
> OPERATORERNA NOT `NumberNotIn` - `StringNotIn` och beter sig som `Values` VILLKOR FÖR varje värde som anges i fältet.
>
> Om du inte gör det blir filtret ett Accept-All-filter och det motverkar filtrningen.

## <a name="floating-point-rounding-behavior"></a>Upprundning av flyttals avrundning

Event Grid `decimal` använder TYPEN .NET för att hantera alla numeriska värden. De talvärden som anges i händelseprenumerationen JSON är inte föremål för flyttalsavrundning.

## <a name="case-sensitivity-of-string-filters"></a>Strängfilters känslighet för skiftläge

Alla strängjämförelser är skiftlägesokänsliga. Det finns inget sätt att ändra detta beteende idag.

## <a name="allowed-advanced-filter-keys"></a>Tillåtna avancerade filternycklar

Egenskapen `Key` kan antingen vara en välkänd egenskap på översta nivån eller vara en json-bana med flera punkter, där varje punkt innebär att du kliver in i ett kapslat json-objekt.

Event Grid har ingen särskild betydelse `$` för tecknet i nyckeln, till skillnad från JSONPath-specifikationen.

### <a name="event-grid-schema"></a>Schema för händelserutnät

För händelser i event grid-schemat:

* ID
* Hjälpavsnitt
* Subjekt
* Eventtype
* DataVersion (DataVersion)
* Data.Prop1
* Data.Prop*Prop2.Prop3.Prop4.Prop5

### <a name="custom-event-schema"></a>Anpassat händelseschema

Det finns ingen begränsning `Key` för det anpassade händelseschemat eftersom Event Grid inte framtvingar något kuvertschema på nyttolasten.

## <a name="numeric-single-value-filter-examples"></a>Numeriska filterexempel med ett värde

* AntalTörretr
* Antal störrethanorEquals
* NumberLessThan
* NumberLessThanOrEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberGreaterThan",
                "key": "Data.Key1",
                "value": 5
            },
            {
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key2",
                "value": *456
            },
            {
                "operatorType": "NumberLessThan",
                "key": "Data.P*P2.P3",
                "value": 1000
            },
            {
                "operatorType": "NumberLessThanOrEquals",
                "key": "Data.P*P2",
                "value": 999
            }
        ]
    }
}
```

## <a name="numeric-range-value-filter-examples"></a>Exempel på numeriskt områdesvärdefilter

* NumberIn (Olikartade)
* Antal InteIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberIn",
                "key": "Data.Key1",
                "values": [1, 10, 100]
            },
            {
                "operatorType": "NumberNotIn",
                "key": "Data.Key2",
                "values": [2, 3, 4.56]
            }
        ]
    }
}
```

## <a name="string-range-value-filter-examples"></a>Exempel på filter för strängområdesvärde

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn (StringIn)
* StringNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "StringContains",
                "key": "Data.Key1",
                "values": ["microsoft", "azure"]
            },
            {
                "operatorType": "StringBeginsWith",
                "key": "Data.Key2",
                "values": ["event", "grid"]
            },
            {
                "operatorType": "StringEndsWith",
                "key": "Data.P3.P4",
                "values": ["jpg", "jpeg", "png"]
            },
            {
                "operatorType": "StringIn",
                "key": "RootKey",
                "values": ["exact", "string", "matches"]
            },
            {
                "operatorType": "StringNotIn",
                "key": "RootKey",
                "values": ["aws", "bridge"]
            }
        ]
    }
}
```

## <a name="boolean-single-value-filter-examples"></a>Booleska filterexempel med ett värde

* BoolEquals (olikartade)

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey1",
                "value": true
            },
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey2",
                "value": false
            }
        ]
    }
}
```
