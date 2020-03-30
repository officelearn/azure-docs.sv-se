---
title: Mallfunktioner - jämförelse
description: Beskriver de funktioner som ska användas i en Azure Resource Manager-mall för att jämföra värden.
ms.topic: conceptual
ms.date: 09/05/2017
ms.openlocfilehash: 42009e8543e307f2d3e4643ddaa79f492f9bdfee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156386"
---
# <a name="comparison-functions-for-arm-templates"></a>Jämförelsefunktioner för ARM-mallar

Resource Manager innehåller flera funktioner för att göra jämförelser i dina ARM-mallar (Azure Resource Manager).

* [lika med](#equals)
* [större än](#greater)
* [större än eller lika med](#greaterorequals)
* [mindre än](#less)
* [mindre än eller lika med](#lessorequals)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="equals"></a>lika med
`equals(arg1, arg2)`

Kontrollerar om två värden är lika med varandra.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |int, sträng, array eller objekt |Det första värdet för att kontrollera jämlikhet. |
| arg2 (arg2) |Ja |int, sträng, array eller objekt |Det andra värdet för att kontrollera jämlikhet. |

### <a name="return-value"></a>Returvärde

Returnerar **Sant** om värdena är lika. annars **falskt**.

### <a name="remarks"></a>Anmärkningar

Funktionen lika med används ofta `condition` med elementet för att testa om en resurs distribueras.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

### <a name="example"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) kontrollerar du olika typer av värden för likhet. Alla standardvärden returnerar Sant.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 1
        },
        "firstString": {
            "type": "string",
            "defaultValue": "a"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "firstObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[equals(parameters('firstString'), parameters('secondString'))]"
        },
        "checkArrays": {
            "type": "bool",
            "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
        },
        "checkObjects": {
            "type": "bool",
            "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | True |
| checkArrays | Bool | True |
| checkObjects | Bool | True |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) används [inte](template-functions-logical.md#not) med **lika med**.

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
}
```

Utdata från föregående exempel är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

## <a name="greater"></a>större än
`greater(arg1, arg2)`

Kontrollerar om det första värdet är större än det andra värdet.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |int eller sträng |Det första värdet för den större jämförelsen. |
| arg2 (arg2) |Ja |int eller sträng |Det andra värdet för den större jämförelsen. |

### <a name="return-value"></a>Returvärde

Returnerar **Sant** om det första värdet är större än det andra värdet. annars **falskt**.

### <a name="example"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) kontrolleras om det ena värdet är större än det andra.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greater(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

## <a name="greaterorequals"></a>större än eller lika med
`greaterOrEquals(arg1, arg2)`

Kontrollerar om det första värdet är större än eller lika med det andra värdet.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |int eller sträng |Det första värdet för en större eller lika jämförelse. |
| arg2 (arg2) |Ja |int eller sträng |Det andra värdet för den större eller lika jämförelsen. |

### <a name="return-value"></a>Returvärde

Returnerar **Sant** om det första värdet är större än eller lika med det andra värdet. annars **falskt**.

### <a name="example"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) kontrollerar du om det ena värdet är större än eller lika med det andra.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

## <a name="less"></a>mindre än
`less(arg1, arg2)`

Kontrollerar om det första värdet är mindre än det andra värdet.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |int eller sträng |Det första värdet för mindre jämförelse. |
| arg2 (arg2) |Ja |int eller sträng |Det andra värdet för mindre jämförelse. |

### <a name="return-value"></a>Returvärde

Returnerar **Sant** om det första värdet är mindre än det andra värdet. annars **falskt**.

### <a name="example"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) kontrolleras om det ena värdet är mindre än det andra.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[less(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

## <a name="lessorequals"></a>mindre än eller lika med
`lessOrEquals(arg1, arg2)`

Kontrollerar om det första värdet är mindre än eller lika med det andra värdet.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |int eller sträng |Det första värdet för mindre eller lika med jämförelse. |
| arg2 (arg2) |Ja |int eller sträng |Det andra värdet för mindre eller lika med jämförelse. |

### <a name="return-value"></a>Returvärde

Returnerar **Sant** om det första värdet är mindre än eller lika med det andra värdet. annars **falskt**.

### <a name="example"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) kontrollerar du om det ena värdet är mindre än eller lika med det andra.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

## <a name="next-steps"></a>Nästa steg
* En beskrivning av avsnitten i en Azure Resource Manager-mall finns i [Redigera Azure Resource Manager-mallar](template-syntax.md).
* Information om hur du sammanfogar flera mallar finns [i Använda länkade mallar med Azure Resource Manager](linked-templates.md).
* Information om hur du itererar ett angivet antal gånger när du skapar en typ av resurs finns [i Skapa flera instanser av resurser i Azure Resource Manager](copy-resources.md).
* Information om hur du distribuerar mallen som du har skapat finns i [Distribuera ett program med Azure Resource Manager-mallen](deploy-powershell.md).

