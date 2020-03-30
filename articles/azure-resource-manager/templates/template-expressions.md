---
title: Mallsyntax och uttryck
description: Beskriver den deklarativa JSON-syntaxen för Azure Resource Manager-mallar.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 172838fa24709eb60fbcb6a68277f44bbd42f01e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460117"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Syntax och uttryck i Azure Resource Manager-mallar

Den grundläggande syntaxen för mallen är JSON. Du kan dock använda uttryck för att utöka JSON-värdena som är tillgängliga i mallen.  Uttryck börjar och slutar med hakparenteser: `[` respektive `]`. Uttryckets värde utvärderas när mallen distribueras. Ett uttryck kan returnera en sträng, ett heltal, ett booleskt värde, en matris eller ett objekt.

Ett malluttryck får inte överstiga 24 576 tecken.

## <a name="use-functions"></a>Använda funktioner

Azure Resource Manager innehåller [funktioner](template-functions.md) som du kan använda i en mall. I följande exempel visas ett uttryck som använder en funktion i standardvärdet för en parameter:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

I uttrycket anropar syntaxen `resourceGroup()` en av de funktioner som Resource Manager tillhandahåller för användning i en mall. I det här fallet är det [resursgruppsfunktionen.](template-functions-resource.md#resourcegroup) Precis som i JavaScript formateras funktionsanrop som `functionName(arg1,arg2,arg3)`. Syntaxen `.location` hämtar en egenskap från objektet som returneras av den funktionen.

Mallfunktioner och deras parametrar är skiftlägesokänsliga. Resource Manager matchar till exempel **variabler('var1')** och **VARIABLES('VAR1')** som samma. När funktionen utvärderas, om inte funktionen uttryckligen ändrar skiftläge (t.ex. toUpper eller toLower), bevarar funktionen ärendet. Vissa resurstyper kan ha ärendekrav som är åtskilda från hur funktioner utvärderas.

Om du vill skicka ett strängvärde som en parameter till en funktion använder du enstaka citattecken.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

De flesta funktioner fungerar på samma sätt oavsett om de distribueras till en resursgrupp, prenumeration, hanteringsgrupp eller klient. Följande funktioner har begränsningar som baseras på omfattningen:

* [resourceGroup](template-functions-resource.md#resourcegroup) - kan endast användas i distributioner till en resursgrupp.
* [resourceId](template-functions-resource.md#resourceid) - kan användas i alla scope, men de giltiga parametrarna ändras beroende på omfånget.
* [prenumeration](template-functions-resource.md#subscription) - kan endast användas i distributioner till en resursgrupp eller prenumeration.

## <a name="escape-characters"></a>Escape tecken

Om du vill att en bokstavlig sträng ska börja med en vänster hakparentes `[` och sluta med en högerparentes `]`, men inte låta tolka den som ett uttryck, lägger du till en extra hakparentes för att starta strängen med `[[`. Till exempel variabeln:

```json
"demoVar1": "[[test value]"
```

Bestämmer `[test value]`till .

Men om den bokstavliga strängen inte slutar med en hakparentes ska du inte dra ut den första hakparentesen. Till exempel variabeln:

```json
"demoVar2": "[test] value"
```

Bestämmer `[test] value`till .

Om du vill undkomma dubbla citattecken i ett uttryck, till exempel lägga till ett JSON-objekt i mallen, använder du omvänt snedstreck.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

När parametervärden överförs beror användningen av escape-tecken på var parametervärdet anges. Om du anger ett standardvärde i mallen behöver du den extra vänstra hakparentesen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1":{
            "type": "string",
            "defaultValue": "[[test value]"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput": {
            "type": "string",
            "value": "[parameters('demoParam1')]"
        }
    }
}
```

Om du använder standardvärdet returneras `[test value]`mallen .

Men om du skickar in ett parametervärde via kommandoraden tolkas tecknen bokstavligt. Distribuera den tidigare mallen med:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

Returnerar `[[test value]`. Använd i stället:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

Samma formatering gäller när värden passerar från en parameterfil. Karaktärerna tolkas bokstavligt. När följande parameterfil används med föregående `[test value]`mall returneras:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1": {
            "value": "[test value]"
        }
   }
}
```

## <a name="null-values"></a>Null-värden

Om du vill ange att **null** en egenskap ska null ska vara null eller **[json('null')]**. [Json-funktionen](template-functions-array.md#json) returnerar ett tomt `null` objekt när du anger som parameter. I båda fallen behandlar Resource Manager-mallar det som om egenskapen inte finns.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Nästa steg

* Den fullständiga listan över mallfunktioner finns i [Mallfunktionerna](template-functions.md)i Azure Resource Manager .
* Mer information om mallfiler finns [i Förstå strukturen och syntaxen för Azure Resource Manager-mallar](template-syntax.md).
