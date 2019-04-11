---
title: Azure Resource Manager-Mallfunktioner - logiska | Microsoft Docs
description: Beskriver funktionerna du använder i en Azure Resource Manager-mall för att fastställa logiska värden.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/09/2019
ms.author: tomfitz
ms.openlocfilehash: 9065c6bc71a153ae94ddc20d5b41a152094fc111
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2019
ms.locfileid: "59470159"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Logiska funktioner för Azure Resource Manager-mallar

Resource Manager tillhandahåller flera funktioner för att göra jämförelser i dina mallar.

* [och](#and)
* [bool](#bool)
* [if](#if)
* [inte](#not)
* [eller](#or)

## <a name="and"></a>och

`and(arg1, arg2, ...)`

Kontrollerar om alla parametervärden är sant.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |boolesk |Det första värdet för att kontrollera om är sant. |
| arg2 |Ja |boolesk |Det andra värdet att kontrollera om är sant. |
| ytterligare argument |Nej |boolesk |Ytterligare argument för att kontrollera om utvärderas som true. |

### <a name="return-value"></a>Returvärde

Returnerar **SANT** om alla värden är true, i annat fall **FALSKT**.

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
| andExampleOutput | Booleskt | False |
| orExampleOutput | Booleskt | True |
| notExampleOutput | Booleskt | False |

## <a name="bool"></a>bool

`bool(arg1)`

Konverterar parametern till ett booleskt värde.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |sträng eller int |Värdet att konvertera till ett booleskt värde. |

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| trueString | Booleskt | True |
| falseString | Booleskt | False |
| trueInt | Booleskt | True |
| falseInt | Booleskt | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Returnerar ett värde baserat på om ett villkor är SANT eller FALSKT.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| villkor |Ja |boolesk |Värde att kontrollera om det är SANT eller FALSKT. |
| trueValue |Ja | sträng, int, objekt eller matris |Värdet som returneras när villkoret är sant. |
| falseValue |Ja | sträng, int, objekt eller matris |Värdet som returneras om villkoret är FALSKT. |

### <a name="return-value"></a>Returvärde

Returnerar andra parameter när den första parametern är **SANT**, annars returnerar tredje parameter.

### <a name="remarks"></a>Kommentarer

När villkoret är **SANT**, endast true värde ska utvärderas. När villkoret är **FALSKT**, endast false-värde ska utvärderas. Med den **om** -funktion som du kan inkludera uttryck som endast är villkorligt giltiga. Du kan till exempel referera till en resurs som finns under ett villkor, men inte under det andra villkoret. Ett exempel på villkorligt utvärdera uttryck visas i följande avsnitt.

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
        },
        "objectOutput": {
            "type": "object",
            "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
        }
    }
}
```

Utdata från föregående exempel är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| yesOutput | String | ja |
| noOutput | String | nej |
| objectOutput | Objekt | {”test”: ”value1”} |

Följande [exempelmall](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) visar hur du använder den här funktionen med uttryck som endast är villkorligt giltiga.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "logAnalytics": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "condition": "[greaterOrEquals(parameters('logAnalytics'), '0')]",
            "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2017-03-30",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[if(greaterOrEquals(parameters('logAnalytics'), '0'), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
                },
                "protectedSettings": {
                    "workspaceKey": "[if(greaterOrEquals(parameters('logAnalytics'), '0'), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
                }
            }
        }
    ],
    "outputs": {
        "mgmtStatus": {
            "type": "string",
            "value": "[if(greaterOrEquals(parameters('logAnalytics'), '0'), 'Enabled monitoring for VM!', 'Nothing to enable')]"
        }
    }
}
```

## <a name="not"></a>inte

`not(arg1)`

Konverterar booleskt värde till dess motsatt värde.

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
| andExampleOutput | Booleskt | False |
| orExampleOutput | Booleskt | True |
| notExampleOutput | Booleskt | False |

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
| checkNotEquals | Booleskt | True |

## <a name="or"></a>eller

`or(arg1, arg2, ...)`

Kontrollerar om ett parametervärde är sant.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |boolesk |Det första värdet för att kontrollera om är sant. |
| arg2 |Ja |boolesk |Det andra värdet att kontrollera om är sant. |
| ytterligare argument |Nej |boolesk |Ytterligare argument för att kontrollera om utvärderas som true. |

### <a name="return-value"></a>Returvärde

Returnerar **SANT** om något värde är sant, i annat fall **FALSKT**.

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
| andExampleOutput | Booleskt | False |
| orExampleOutput | Booleskt | True |
| notExampleOutput | Booleskt | False |

## <a name="next-steps"></a>Nästa steg

* En beskrivning av avsnitt i en Azure Resource Manager-mall finns i [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Om du vill slå samman flera mallar, se [med länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).
* Iterera ett angivet antal gånger när du skapar en typ av resurs, finns i [och skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md).
* Om du vill se hur du distribuerar mallen som du har skapat, se [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md).

