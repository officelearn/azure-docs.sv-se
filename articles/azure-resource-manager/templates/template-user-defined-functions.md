---
title: Användardefinierade funktioner i mallar
description: Beskriver hur du definierar och använder användardefinierade funktioner i en Azure Resource Manager mall.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 2c09572a460aa028b23987033d2b77e2aad8a0cd
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943223"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Användardefinierade funktioner i Azure Resource Manager mall

I mallen kan du skapa egna funktioner. Dessa funktioner är tillgängliga för användning i din mall. Användardefinierade funktioner är separata från de [standardmallar](template-functions.md) som är tillgängliga automatiskt i din mall. Skapa egna funktioner när du har komplicerade uttryck som används upprepade gånger i din mall.

I den här artikeln beskrivs hur du lägger till användardefinierade funktioner i Azure Resource Manager-mallen.

## <a name="define-the-function"></a>Definiera funktionen

Dina funktioner kräver ett namn områdes värde för att undvika namngivnings konflikter med Template functions. I följande exempel visas en funktion som returnerar ett unikt namn:

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

I följande exempel visas en mall som innehåller en användardefinierad funktion. Funktionen används för att hämta ett unikt namn för ett lagrings konto. Mallen har en parameter med namnet **storageNamePrefix** som den skickar som en parameter till funktionen.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
 "contentVersion": "1.0.0.0",
 "parameters": {
   "storageNamePrefix": {
     "type": "string",
     "maxLength": 11
   }
 },
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
 "resources": [
   {
     "type": "Microsoft.Storage/storageAccounts",
     "apiVersion": "2019-04-01",
     "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
     "location": "South Central US",
     "sku": {
       "name": "Standard_LRS"
     },
     "kind": "StorageV2",
     "properties": {
       "supportsHttpsTrafficOnly": true
     }
   }
 ]
}
```

## <a name="limitations"></a>Begränsningar

När du definierar en användar funktion finns det vissa begränsningar:

* Funktionen kan inte komma åt variabler.
* Funktionen kan bara använda parametrar som har definierats i funktionen. När du använder funktionen [parametrar](template-functions-deployment.md#parameters) i en användardefinierad funktion är du begränsad till parametrarna för den funktionen.
* Funktionen kan inte anropa andra användardefinierade funktioner.
* Funktionen kan inte använda [referens](template-functions-resource.md#reference) funktionen eller någon av [list](template-functions-resource.md#list) funktionerna.
* Parametrar för funktionen kan inte ha standardvärden.


## <a name="next-steps"></a>Nästa steg

* Mer information om tillgängliga egenskaper för användardefinierade funktioner finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](template-syntax.md).
* En lista över tillgängliga mal funktioner finns i [Azure Resource Manager Template Functions](template-functions.md).
