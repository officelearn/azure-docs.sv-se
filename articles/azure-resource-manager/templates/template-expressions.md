---
title: Mallens syntax och uttryck
description: Beskriver deklarativ JSON-syntax för Azure Resource Manager mallar.
ms.topic: conceptual
ms.date: 09/03/2019
ms.openlocfilehash: b7682ba10c30290e5935bc2dd17e2a83852d92f4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484199"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Syntax och uttryck i Azure Resource Manager mallar

Den grundläggande syntaxen för mallen är JSON. Du kan dock använda uttryck för att utöka de JSON-värden som är tillgängliga i mallen.  Uttryck börjar och slutar med hakparenteser: `[` respektive `]`. Uttryckets värde utvärderas när mallen distribueras. Ett uttryck kan returnera en sträng, ett heltal, ett booleskt värde, en matris eller ett objekt.

Ett mall uttryck får inte överstiga 24 576 tecken.

## <a name="use-functions"></a>Använda funktioner

I följande exempel visas ett uttryck i standardvärdet för en parameter:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

I uttrycket anropar syntaxen `resourceGroup()` en av de funktioner som Resource Manager tillhandahåller för användning i en mall. I det här fallet är det [resourceGroup](template-functions-resource.md#resourcegroup) -funktionen. Precis som i Java Script formateras funktions anrop som `functionName(arg1,arg2,arg3)`. Syntaxen `.location` hämtar en egenskap från det objekt som returnerades av funktionen.

Mallens funktioner och deras parametrar är Skift läges känsliga. Resource Manager matchar till exempel **variabler (' var1 ')** och **variabler (' var1 ')** som samma. När den utvärderas, om inte funktionen uttryckligen ändrar Skift läge (t. ex. toUpper eller toLower), bevarar funktionen det fallet. Vissa resurs typer kan ha fall krav som är skilda från hur funktionerna utvärderas.

Om du vill skicka ett sträng värde som en parameter till en funktion använder du enkla citat tecken.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

## <a name="escape-characters"></a>Escape-tecken

Om du vill att en litteral sträng ska börja med en vänsterparentes `[` och sluta med en högerparentes `]`, men inte har den tolkas som ett uttryck, lägger du till en extra parentes för att starta strängen med `[[`. Till exempel variabeln:

```json
"demoVar1": "[[test value]"
```

Matchar `[test value]`.

Om den litterala strängen inte slutar med en hak paren tes, ska du inte undanta den första parentesen. Till exempel variabeln:

```json
"demoVar2": "[test] value"
```

Matchar `[test] value`.

Om du vill undanta dubbla citat tecken i ett uttryck, till exempel lägga till ett JSON-objekt i mallen, använder du omvänt snedstreck.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

## <a name="next-steps"></a>Nästa steg

* En fullständig lista över mall funktioner finns i [Azure Resource Manager Template Functions](template-functions.md).
* Mer information om mallfiler finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](template-syntax.md).
