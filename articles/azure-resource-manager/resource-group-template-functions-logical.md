---
title: Azure Resource Manager Mallfunktioner - logiska | Microsoft Docs
description: Beskriver funktionerna du använder i en Azure Resource Manager-mall för att fastställa logiska värden.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2017
ms.author: tomfitz
ms.openlocfilehash: d8a7ae412fc80dff7bd91c1cdc5d4fcd985e07f4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359075"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Logiska funktioner för Azure Resource Manager-mallar

Resource Manager innehåller flera funktioner för att göra jämförelser i dina mallar.

* [Och](#and)
* [bool](#bool)
* [Om](#if)
* [inte](#not)
* [Eller](#or)

## <a name="and"></a>och
`and(arg1, arg2)`

Kontrollerar om båda parametervärden är true.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |boolesk |Det första värdet för att kontrollera om är true. |
| arg2 |Ja |boolesk |Det andra värdet för att kontrollera om är true. |

### <a name="return-value"></a>Returvärde

Returnerar **SANT** om båda värdena är true, annars **FALSKT**.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) visar hur du använder logiska funktioner.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Utdata från föregående exempel är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| andExampleOutput | bool | False |
| orExampleOutput | bool | True |
| notExampleOutput | bool | False |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

## <a name="bool"></a>bool
`bool(arg1)`

Konverterar parametern till ett booleskt värde.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |sträng eller ett heltal |Värdet som ska konverteras till ett booleskt värde. |

### <a name="return-value"></a>Returvärde
Ett booleskt värde för konverterade värdet.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) visar hur du använder bool med en sträng eller ett heltal.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| trueString | bool | True |
| falseString | bool | False |
| trueInt | bool | True |
| falseInt | bool | False |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/bool.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/bool.json
```

## <a name="if"></a>Om
`if(condition, trueValue, falseValue)`

Returnerar ett värde baserat på om ett villkor är SANT eller FALSKT.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| tillstånd |Ja |boolesk |Värdet för att kontrollera om det är true. |
| trueValue |Ja | sträng, int, objekt eller matris |Värdet som returneras när villkoret är sant. |
| falseValue |Ja | sträng, int, objekt eller matris |Värdet som returneras om villkoret är FALSKT. |

### <a name="return-value"></a>Returvärde

Returnerar andra parameter när den första parametern är **SANT**, annars returnerar tredje parametern.

### <a name="remarks"></a>Kommentarer

Du kan använda den här funktionen för att ange en resursegenskap villkorligt. I följande exempel är inte en fullständig mall, men den visar relevanta delar för att ange villkorligt tillgänglighetsuppsättningen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "availabilitySet": {
            "type": "string",
            "allowedValues": [
                "yes",
                "no"
            ]
        }
    },
    "variables": {
        ...
        "availabilitySetName": "availabilitySet1",
        "availabilitySet": {
            "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
        }
    },
    "resources": [
        {
            "condition": "[equals(parameters('availabilitySet'),'yes')]",
            "type": "Microsoft.Compute/availabilitySets",
            "name": "[variables('availabilitySetName')]",
            ...
        },
        {
            "apiVersion": "2016-03-30",
            "type": "Microsoft.Compute/virtualMachines",
            "properties": {
                "availabilitySet": "[if(equals(parameters('availabilitySet'),'yes'), variables('availabilitySet'), json('null'))]",
                ...
            }
        },
        ...
    ],
    ...
}
```

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) visar hur du använder den `if` funktion.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "yesOutput": {
            "type": "string",
            "value": "[if(equals('a', 'a'), 'yes', 'no')]"
        },
        "noOutput": {
            "type": "string",
            "value": "[if(equals('a', 'b'), 'yes', 'no')]"
        }
    }
}
```

Utdata från föregående exempel är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| yesOutput | Sträng | ja |
| noOutput | Sträng | nej |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/if.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/if.json
```

## <a name="not"></a>inte
`not(arg1)`

Konverterar booleskt värde till motsatt värde.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |boolesk |Värdet som ska konverteras. |

### <a name="return-value"></a>Returvärde

Returnerar **SANT** när parametern är **FALSKT**. Returnerar **FALSKT** när parametern är **SANT**.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) visar hur du använder logiska funktioner.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Utdata från föregående exempel är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| andExampleOutput | bool | False |
| orExampleOutput | bool | True |
| notExampleOutput | bool | False |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) använder **inte** med [är lika med](resource-group-template-functions-comparison.md#equals).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
```

Utdata från föregående exempel är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| checkNotEquals | bool | True |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

## <a name="or"></a>eller
`or(arg1, arg2)`

Kontrollerar om antingen parametervärdet är true.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |boolesk |Det första värdet för att kontrollera om är true. |
| arg2 |Ja |boolesk |Det andra värdet för att kontrollera om är true. |

### <a name="return-value"></a>Returvärde

Returnerar **SANT** om antingen värdet är true, annars **FALSKT**.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) visar hur du använder logiska funktioner.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Utdata från föregående exempel är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| andExampleOutput | bool | False |
| orExampleOutput | bool | True |
| notExampleOutput | bool | False |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

## <a name="next-steps"></a>Nästa steg
* En beskrivning av avsnitt i en Azure Resource Manager-mallen finns [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Om du vill slå samman flera mallar, se [använda länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).
* Iterera ett angivet antal gånger när du skapar en typ av resurs finns [skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md).
* Information om hur du distribuerar mallen som du har skapat finns [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md).

