---
title: Lägg till taggar till digitala dubbla
titleSuffix: Azure Digital Twins
description: Se hur du implementerar taggar på digitala dubbla
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c2620b52c426871b0ec85e3db237be2d373d42f1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458703"
---
# <a name="add-tags-to-digital-twins"></a>Lägg till taggar till digitala dubbla 

Du kan använda begreppet taggar för att ytterligare identifiera och kategorisera digitala dubbla. I synnerhet kanske användarna vill replikera taggar från befintliga system, till exempel [Haystack-Taggar](https://project-haystack.org/doc/TagModel), inom sina Azures digitala dubbla instanser. 

Det här dokumentet beskriver mönster som kan användas för att implementera taggar på digitala dubbla.

Taggar läggs först till som egenskaper i [modellen](concepts-models.md) som beskriver en digital, dubbel. Den egenskapen ställs sedan in på den dubbla när den skapas baserat på modellen. Sedan kan taggarna användas i [frågor](concepts-query-language.md) för att identifiera och filtrera dina dubbla.

## <a name="marker-tags"></a>Markör Taggar 

En **märkpenn-tagg** är en enkel sträng som används för att markera eller kategorisera en digital, t. ex. "blå" eller "röd". Den här strängen är taggens namn och markör taggarna har inget meningsfullt värde – taggen är signifikant, precis som dess förekomst (eller frånvaro). 

### <a name="add-marker-tags-to-model"></a>Lägg till markör Taggar i modell 

Markör taggar är modellerade som en [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) -karta från `string` till `boolean` . Det booleska värdet `mapValue` ignoreras eftersom förekomsten av taggen är allt det är viktigt. 

Här är ett utdrag från en dubbel modell som implementerar en märkpenn-tagg som en egenskap:

```json
{
  "@type": "Property",
  "name": "tags",
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "tagName",
      "schema": "string"
    },
    "mapValue": {
      "name": "tagValue",
      "schema": "boolean"
    }
  }
}
```

### <a name="add-marker-tags-to-digital-twins"></a>Lägg till markör Taggar i digitala dubbla

När `tags` egenskapen är en del av en digital-enhets modell kan du ställa in markören i den digitala dubbla genom att ange värdet för den här egenskapen. 

Här är ett exempel som fyller markören `tags` för tre dubbla:

```csharp
entity-01: "tags": { "red": true, "round": true } 
entity-02: "tags": { "blue": true, "round": true } 
entity-03: "tags": { "red": true, "large": true } 
```

### <a name="query-with-marker-tags"></a>Fråga med markör Taggar

När Taggar har lagts till i digitala dubbla, kan taggarna användas för att filtrera de dubbla i frågorna. 

Här är en fråga för att hämta alla dubbla som har taggats som "Red": 

```sql
SELECT * FROM digitaltwins WHERE is_defined(tags.red) 
```

Du kan också kombinera taggar för mer komplexa frågor. Här är en fråga för att hämta alla dubbla som är rundade och inte röda: 

```sql
SELECT * FROM digitaltwins WHERE NOT is_defined(tags.red) AND is_defined(tags.round) 
```

## <a name="value-tags"></a>Värde koder 

En **Value-tagg** är ett nyckel/värde-par som används för att ge varje tagg ett värde, till exempel `"color": "blue"` eller `"color": "red"` . När en Value-tagg har skapats kan den också användas som en markör-tagg genom att ignorera taggens värde. 

### <a name="add-value-tags-to-model"></a>Lägg till värde Taggar i modellen 

Värde koderna modelleras som en [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) -karta från `string` till `string` . Både `mapKey` och `mapValue` är viktiga. 

Här är ett utdrag från en dubbel modell som implementerar en Value-tagg som en egenskap:

```json
{
  "@type": "Property",
  "name": "tags",
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "tagName",
      "schema": "string"
    },
    "mapValue": {
      "name": "tagValue",
      "schema": "string"
    }
  }
} 
```

### <a name="add-value-tags-to-digital-twins"></a>Lägg till värde Taggar i digitala dubbla

Precis som med markör taggar kan du ange värde tag gen i ett digitalt värde genom att ange värdet för den här `tags` egenskapen från modellen. Om du vill använda en värdekod som en markör etikett kan du ange `tagValue` fältet till det tomma sträng värdet ( `""` ). 

Här är ett exempel som fyller i värdet `tags` för tre dubbla:

```csharp
entity-01: "tags": { "red": "", "size": "large" } 
entity-02: "tags": { "purple": "", "size": "small" } 
entity-03: "tags": { "red": "", "size": "small" } 
```

Observera att `red` och `purple` används som markör Taggar i det här exemplet.

### <a name="query-with-value-tags"></a>Fråga med värde Taggar

Precis som med markör taggar kan du använda värde taggar för att filtrera de dubbla i frågorna. Du kan också använda värde Taggar och markör Taggar tillsammans.

I exemplet ovan `red` används som en märkpenn-tagg. Här är en fråga för att hämta alla dubbla som har taggats som "Red": 

```sql
SELECT * FROM digitaltwins WHERE is_defined(tags.red) 
```

Här är en fråga för att hämta alla entiteter som är små (värde tag gen) och inte rött: 

```sql
SELECT * FROM digitaltwins WHERE NOT is_defined(tags.red) AND tags.size = 'small' 
```

## <a name="next-steps"></a>Nästa steg

Läs mer om att utforma och hantera digitala dubbla modeller:
* [*Anvisningar: Hantera anpassade modeller*](how-to-manage-model.md)

Läs mer om att skicka frågor till den dubbla grafen:
* [*Anvisningar: fråga det dubbla diagrammet*](how-to-query-graph.md)