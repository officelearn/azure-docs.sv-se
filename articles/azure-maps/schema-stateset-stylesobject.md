---
title: StylesObject för dynamiska Azure Maps
description: Referens guide till JSON-schemat och syntaxen för den StylesObject som används för att skapa i dynamiska Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/19/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4284956138002d209ab0934cdd052748ef8aab78
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966283"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>StylesObject schema referens guide för dynamiska kartor

 `StylesObject`Är en `StyleObject` matris som representerar stateset-format. Använd tjänsten Azure Maps Creator [Feature State](/rest/api/maps/featurestate) för att tillämpa dina stateset-format på inhämtade kart data funktioner. När du har skapat dina stateset-format och associerat dem med ingångna kart funktioner kan du använda dem för att skapa dynamiska kartor i inomhus. Mer information om hur du skapar dynamiska kartor finns i [implementera dynamisk formatering för skapare inomhus Maps](indoor-map-dynamic-styling.md).

## <a name="styleobject"></a>StyleObject

A `StyleObject` är antingen en [`BooleanTypeStyleRule`](#booleantypestylerule) eller en [`NumericTypeStyleRule`](#numerictypestylerule) .

I JSON nedan visas ett `BooleanTypeStyleRule` namngivet `occupied` och ett `NumericTypeStyleRule` namngivet `temperature` .

```json
 "styles": [
     {
        "keyname": "occupied",
        "type": "boolean",
        "rules": [
            {
                "true": "#FF0000",
                "false": "#00FF00"
            }
        ]
    },
    {
        "keyname": "temperature",
        "type": "number",
        "rules": [
             {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "maximum": 70,
                "exclusiveMinimum": 30
              },
              "color": "#eba834"
            }
        ]
    }
]
```

## <a name="numerictypestylerule"></a>NumericTypeStyleRule

 En `NumericTypeStyleRule` är en  [`StyleObject`](#styleobject) och består av följande egenskaper:

| Egenskap | Typ | Beskrivning | Krävs |
|-----------|----------|-------------|-------------|
| `keyName` | sträng | *Tillstånd* eller dynamiskt egenskaps namn. En `keyName` ska vara unik i `StyleObject` matrisen.| Ja |
| `type` | sträng | Värdet är "numeric". | Ja |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| En matris med numeriska format intervall med tillhör ande färger. Varje intervall definierar en färg som ska användas när *State* -värdet uppfyller intervallet.| Ja |

### <a name="numberruleobject"></a>NumberRuleObject

En `NumberRuleObject` består av en [`RangeObject`](#rangeobject) och en `color` egenskap. Om *State* -värdet hamnar inom intervallet är färg för visning den färg som anges i `color` egenskapen.

Om du definierar flera överlappande intervall blir den valda färgen den färg som definieras i det första intervallet som är uppfyllt.

I följande JSON-exempel behåller båda intervallen sant när värdet för *tillstånd* är mellan 50-60. Den färg som ska användas beror dock på att `#343deb` det är det första intervallet i listan som är uppfyllt.

```json

    {
        "rules":[
            {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "minimum": 50,
                "maximum": 60
                },
                "color": "#eba834"
            }
        ]
    }
]
```

| Egenskap | Typ | Beskrivning | Krävs |
|-----------|----------|-------------|-------------|
| `range` | [RangeObject](#rangeobject) | [RangeObject](#rangeobject) definierar en uppsättning villkor för logiska intervall, som, om `true` , ändrar visnings färgen för *tillståndet* till den färg som anges i `color` egenskapen. Om `range` inte anges används alltid den färg som definierats i `color` egenskapen.   | Nej |
| `color` | sträng | Färgen som ska användas när tillstånd svärdet hamnar inom intervallet. `color`Egenskapen är en JSON-sträng i något av följande format: <ul><li> Hexadecimala värden i HTML-format </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255, 0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> HSLA ("HSLA (100, 50%, 50%, 1)")</li><li> Fördefinierade HTML-färger, som gult och blått.</li></ul> | Ja |

### <a name="rangeobject"></a>RangeObject

`RangeObject`Definierar ett numeriskt intervall värde för en [`NumberRuleObject`](#numberruleobject) . För att *State* -värdet ska ligga inom intervallet måste alla definierade villkor innehålla True. 

| Egenskap | Typ | Beskrivning | Krävs |
|-----------|----------|-------------|-------------|
| `minimum` | double | Talet x som x ≥ `minimum` .| Nej |
| `maximum` | double | Talet x som x ≤ `maximum` . | Nej |
| `exclusiveMinimum` | double | Talet x som > `exclusiveMinimum` .| Nej |
| `exclusiveMaximum` | double | Talet x som < `exclusiveMaximum` .| Nej |

### <a name="example-of-numerictypestylerule"></a>Exempel på NumericTypeStyleRule

Följande JSON visar ett `NumericTypeStyleRule` *tillstånd* med namnet `temperature` . I det här exemplet [`NumberRuleObject`](#numberruleobject) innehåller två definierade temperatur intervall och deras associerade färg format. Om temperatur intervallet är 50-69 ska visningen använda färgen `#343deb` .  Om temperatur intervallet är 31-70 ska visningen använda färgen `#eba834` .

```json
{
    "keyname": "temperature",
    "type": "number",
    "rules":[
        {
            "range": {
            "minimum": 50,
            "exclusiveMaximum": 70
            },
            "color": "#343deb"
        },
        {
            "range": {
            "maximum": 70,
            "exclusiveMinimum": 30
            },
            "color": "#eba834"
        }
    ]
}
```

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

En `BooleanTypeStyleRule` är en [`StyleObject`](#styleobject) och består av följande egenskaper:

| Egenskap | Typ | Beskrivning | Krävs |
|-----------|----------|-------------|-------------|
| `keyName` | sträng |  *Tillstånd* eller dynamiskt egenskaps namn.  En `keyName` ska vara unik inuti format mat ris.| Ja |
| `type` | sträng |Värdet är "Boolean". | Ja |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)81.1| Ett booleskt par med färger för `true` och `false` *tillstånds* värden.| Ja |

### <a name="booleanruleobject"></a>BooleanRuleObject

En `BooleanRuleObject` definierar färger för `true` och `false` värden.

| Egenskap | Typ | Beskrivning | Krävs |
|-----------|----------|-------------|-------------|
| `true` | sträng | Färgen som ska användas när *State* -värdet är `true` . `color`Egenskapen är en JSON-sträng i något av följande format: <ul><li> Hexadecimala värden i HTML-format </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255, 0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> HSLA ("HSLA (100, 50%, 50%, 1)")</li><li> Fördefinierade HTML-färger, som gult och blått.</li></ul>| Ja |
| `false` | sträng | Färgen som ska användas när *State* -värdet är `false` . | Ja |

### <a name="example-of-booleantypestylerule"></a>Exempel på BooleanTypeStyleRule

Följande JSON visar ett `BooleanTypeStyleRule` *tillstånd* med namnet `occupied` . [`BooleanRuleObject`](#booleanruleobject)Definierar färger för `true` och `false` värden.

```json
{
    "keyname": "occupied",
    "type": "boolean",
    "rules": [
    {
        "true": "#FF0000",
        "false": "#00FF00"
    }
    ]
}
```