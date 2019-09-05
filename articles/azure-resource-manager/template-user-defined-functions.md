---
title: Användardefinierade funktioner i Azure Resource Manager mall
description: Beskriver hur du definierar och använder användardefinierade funktioner i en Azure Resource Manager mall.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 71c166b9c596c3c8628f943ae5c7dbebd9c2d51c
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384143"
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
* Funktionen kan inte använda [referens](resource-group-template-functions-resource.md#reference) -eller [list](resource-group-template-functions-resource.md#list) funktionerna.
* Parametrar för funktionen kan inte ha standardvärden.


## <a name="next-steps"></a>Nästa steg

* Mer information om tillgängliga egenskaper för användardefinierade funktioner finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](resource-group-authoring-templates.md).
* En lista över tillgängliga mal funktioner finns i [Azure Resource Manager Template Functions](resource-group-template-functions.md).
