---
title: Användardefinierade funktioner i Azure Resource Manager mall
description: Beskriver hur du definierar och använder användardefinierade funktioner i en Azure Resource Manager mall.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 227ecb57b125264e7d0eba56e634966d677e0f58
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70387288"
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
