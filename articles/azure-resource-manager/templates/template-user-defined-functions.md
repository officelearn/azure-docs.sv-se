---
title: Användardefinierade funktioner i mallar
description: Beskriver hur du definierar och använder användardefinierade funktioner i en Azure Resource Manager-mall.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 2c09572a460aa028b23987033d2b77e2aad8a0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943223"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Användardefinierade funktioner i Azure Resource Manager-mall

I mallen kan du skapa egna funktioner. Dessa funktioner är tillgängliga för användning i mallen. Användardefinierade funktioner är åtskilda från de [standardmallfunktioner](template-functions.md) som är automatiskt tillgängliga i mallen. Skapa egna funktioner när du har komplicerade uttryck som används upprepade gånger i mallen.

I den här artikeln beskrivs hur du lägger till användardefinierade funktioner i mallen Azure Resource Manager.

## <a name="define-the-function"></a>Definiera funktionen

Dina funktioner kräver ett namnområdesvärde för att undvika att namnge konflikter med mallfunktioner. I följande exempel visas en funktion som returnerar ett unikt namn:

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

I följande exempel visas en mall som innehåller en användardefinierad funktion. Den använder den funktionen för att få ett unikt namn för ett lagringskonto. Mallen har en parameter med namnet **storageNamePrefix** som skickas som en parameter till funktionen.

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

När du definierar en användarfunktion finns det vissa begränsningar:

* Funktionen kan inte komma åt variabler.
* Funktionen kan bara använda parametrar som definieras i funktionen. När du använder [parameterfunktionen](template-functions-deployment.md#parameters) i en användardefinierad funktion är du begränsad till parametrarna för den funktionen.
* Funktionen kan inte anropa andra användardefinierade funktioner.
* Funktionen kan inte använda [referensfunktionen](template-functions-resource.md#reference) eller någon av [listfunktionerna.](template-functions-resource.md#list)
* Parametrar för funktionen kan inte ha standardvärden.


## <a name="next-steps"></a>Nästa steg

* Mer information om tillgängliga egenskaper för användardefinierade funktioner finns i [Förstå strukturen och syntaxen för Azure Resource Manager-mallar](template-syntax.md).
* En lista över tillgängliga mallfunktioner finns i [Mallfunktionerna](template-functions.md)i Azure Resource Manager .
