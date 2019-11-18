---
title: Användardefinierade funktioner i mallar
description: Beskriver hur du definierar och använder användardefinierade funktioner i en Azure Resource Manager mall.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 94fdf80ffc309645a4bc10109a5e8dd4450731e2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143702"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Användardefinierade funktioner i Azure Resource Manager mall

I mallen kan du skapa egna funktioner. Dessa funktioner är tillgängliga för användning i din mall. Användardefinierade funktioner är separata från de [standardmallar](resource-group-template-functions.md) som är tillgängliga automatiskt i din mall. Skapa egna funktioner när du har komplicerade uttryck som används upprepade gånger i din mall.

I den här artikeln beskrivs hur du lägger till användardefinierade funktioner i Azure Resource Manager-mallen.

## <a name="define-the-function"></a>Definiera funktionen

Dina funktioner kräver ett namn områdes värde för att undvika namngivnings konflikter med Template functions. I följande exempel visas en funktion som returnerar ett lagrings konto namn:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>Använd funktionen

I följande exempel visas hur du anropar din funktion.

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="limitations"></a>Begränsningar

När du definierar en användar funktion finns det vissa begränsningar:

* Funktionen kan inte komma åt variabler.
* Funktionen kan bara använda parametrar som har definierats i funktionen. När du använder funktionen [parametrar](resource-group-template-functions-deployment.md#parameters) i en användardefinierad funktion är du begränsad till parametrarna för den funktionen.
* Funktionen kan inte anropa andra användardefinierade funktioner.
* Funktionen kan inte använda [referens](resource-group-template-functions-resource.md#reference) funktionen eller någon av [list](resource-group-template-functions-resource.md#list) funktionerna.
* Parametrar för funktionen kan inte ha standardvärden.


## <a name="next-steps"></a>Nästa steg

* Mer information om tillgängliga egenskaper för användardefinierade funktioner finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](resource-group-authoring-templates.md).
* En lista över tillgängliga mal funktioner finns i [Azure Resource Manager Template Functions](resource-group-template-functions.md).
