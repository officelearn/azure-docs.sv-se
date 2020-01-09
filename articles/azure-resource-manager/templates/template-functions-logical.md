---
title: Template Functions – Logical
description: Beskriver de funktioner som används i en Azure Resource Manager mall för att fastställa logiska värden.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 48c6a05c5a449626c66f9d75c8578b217906d8e0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483965"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Logiska funktioner för Azure Resource Manager mallar

Resource Manager innehåller flera funktioner för att göra jämförelser i dina mallar.

* [och](#and)
* [booleska](#bool)
* [eventuella](#if)
* [Ogiltigt](#not)
* [eller](#or)

## <a name="and"></a>och

`and(arg1, arg2, ...)`

Kontrollerar om alla parameter värden är sanna.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |boolean |Det första värdet för att kontrol lera om är sant. |
| arg2 |Ja |boolean |Det andra värdet för att kontrol lera om är sant. |
| ytterligare argument |Inga |boolean |Ytterligare argument för att kontrol lera om är true. |

### <a name="return-value"></a>Returvärde

Returnerar **True** om alla värden är true; annars **false**.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) visas hur du använder logiska funktioner.

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
| andExampleOutput | Bool | Falskt |
| orExampleOutput | Bool | Sant |
| notExampleOutput | Bool | Falskt |

## <a name="bool"></a>bool

`bool(arg1)`

Konverterar parametern till ett booleskt värde.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |sträng eller heltal |Värdet som ska konverteras till ett booleskt värde. |

### <a name="return-value"></a>Returvärde
Ett booleskt värde för det konverterade värdet.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) visas hur du använder bool med en sträng eller ett heltal.

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
| trueString | Bool | Sant |
| falseString | Bool | Falskt |
| trueInt | Bool | Sant |
| falseInt | Bool | Falskt |

## <a name="if"></a>om

`if(condition, trueValue, falseValue)`

Returnerar ett värde baserat på om ett villkor är sant eller falskt.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| condition |Ja |boolean |Värdet för att kontrol lera om det är sant eller falskt. |
| trueValue |Ja | sträng, heltal, objekt eller matris |Värdet som ska returneras när villkoret är sant. |
| falseValue |Ja | sträng, heltal, objekt eller matris |Värdet som ska returneras när villkoret är falskt. |

### <a name="return-value"></a>Returvärde

Returnerar den andra parametern när den första parametern är **True**; annars returnerar den tredje parametern.

### <a name="remarks"></a>Anmärkningar

När villkoret är **Sant**utvärderas bara det sanna värdet. Om villkoret är **falskt**utvärderas bara det falska värdet. Med funktionen **IF** kan du inkludera uttryck som endast är villkorligt giltiga. Du kan till exempel referera till en resurs som finns under ett villkor, men inte med det andra villkoret. Ett exempel på villkorligt utvärdering av uttryck visas i följande avsnitt.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) visas hur du använder funktionen `if`.

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
| objectOutput | Objekt | {"test": "värde1"} |

I följande [exempel mall](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) visas hur du använder den här funktionen med uttryck som endast är villkorligt giltiga.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "condition": "[not(empty(parameters('logAnalytics')))]",
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
                    "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
                },
                "protectedSettings": {
                    "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
                }
            }
        }
    ],
    "outputs": {
        "mgmtStatus": {
            "type": "string",
            "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
        }
    }
}
```

## <a name="not"></a>inte

`not(arg1)`

Konverterar booleskt värde till motsatt värde.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |boolean |Det värde som ska konverteras. |

### <a name="return-value"></a>Returvärde

Returnerar **True** när parametern är **false**. Returnerar **false** när parametern är **True**.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) visas hur du använder logiska funktioner.

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
| andExampleOutput | Bool | Falskt |
| orExampleOutput | Bool | Sant |
| notExampleOutput | Bool | Falskt |

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) använder **inte** [lika](template-functions-comparison.md#equals)med.

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
| checkNotEquals | Bool | Sant |

## <a name="or"></a>eller

`or(arg1, arg2, ...)`

Kontrollerar om ett parameter värde är sant.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |boolean |Det första värdet för att kontrol lera om är sant. |
| arg2 |Ja |boolean |Det andra värdet för att kontrol lera om är sant. |
| ytterligare argument |Inga |boolean |Ytterligare argument för att kontrol lera om är true. |

### <a name="return-value"></a>Returvärde

Returnerar **Sant** om något värde är sant; annars **false**.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) visas hur du använder logiska funktioner.

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
| andExampleOutput | Bool | Falskt |
| orExampleOutput | Bool | Sant |
| notExampleOutput | Bool | Falskt |

## <a name="next-steps"></a>Nästa steg

* En beskrivning av avsnitt i en Azure Resource Manager-mall finns i [redigera Azure Resource Manager-mallar](template-syntax.md).
* Om du vill slå samman flera mallar, se [med länkade mallar med Azure Resource Manager](linked-templates.md).
* Iterera ett angivet antal gånger när du skapar en typ av resurs, finns i [och skapa flera instanser av resurser i Azure Resource Manager](create-multiple-instances.md).
* Om du vill se hur du distribuerar mallen som du har skapat, se [distribuera ett program med Azure Resource Manager-mall](deploy-powershell.md).

