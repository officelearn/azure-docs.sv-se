---
title: Variabler i mallar
description: Beskriver hur du definierar variabler i en Azure Resource Manager-mall.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: cf135959d30702ea58b7a1d4fdd82625a39245d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483822"
---
# <a name="variables-in-azure-resource-manager-template"></a>Variabler i Azure Resource Manager-mall

I den här artikeln beskrivs hur du definierar och använder variabler i din Azure Resource Manager-mall. Du använder variabler för att förenkla mallen. I stället för att upprepa komplicerade uttryck i hela mallen definierar du en variabel som innehåller det komplicerade uttrycket. Sedan refererar du till den variabeln efter behov i hela mallen.

Resource Manager matchar variabler innan distributionsåtgärderna startas. Var variabeln än används i mallen ersätter Resource Manager den med det lösta värdet.

## <a name="define-variable"></a>Definiera variabel

I följande exempel visas en variabel definition. Det skapar ett strängvärde för ett lagringskontonamn. Den använder flera mallfunktioner för att få ett parametervärde och sammanfogar det till en unik sträng.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Du kan inte använda [referensfunktionen](template-functions-resource.md#reference) eller någon av [listfunktionerna](template-functions-resource.md#list) i variabelavsnittet. Dessa funktioner får körningstillståndet för en resurs och kan inte köras före distributionen när variabler har lösts.

## <a name="use-variable"></a>Använd variabel

I mallen refererar du till parameterns värde med hjälp av [variabelfunktionen.](template-functions-deployment.md#variables) I följande exempel visas hur du använder variabeln för en resursegenskap.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="configuration-variables"></a>Konfigurationsvariabler

Du kan definiera variabler som innehåller relaterade värden för att konfigurera en miljö. Du definierar variabeln som ett objekt med värdena. I följande exempel visas ett objekt som innehåller värden för två miljöer - **testa** och **prod**.

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

I parametrar skapar du ett värde som anger vilka konfigurationsvärden som ska användas.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

Om du vill hämta inställningar för den angivna miljön använder du variabeln och parametern tillsammans.

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>Exempel på mallar

Följande exempel visar scenarier för att använda variabler.

|Mall  |Beskrivning  |
|---------|---------|
| [variabla definitioner](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Visar de olika typerna av variabler. Mallen distribuerar inga resurser. De konstruerar variabla värden och returnerar dessa värden. |
| [konfigurationsvariabel](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Visar användningen av en variabel som definierar konfigurationsvärden. Mallen distribuerar inga resurser. De konstruerar variabla värden och returnerar dessa värden. |
| [nätverkssäkerhetsregler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) och [parameterfil](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Konstruerar en matris i rätt format för att tilldela säkerhetsregler till en nätverkssäkerhetsgrupp. |

## <a name="next-steps"></a>Nästa steg

* Mer information om tillgängliga egenskaper för variabler finns [i Förstå strukturen och syntaxen för Azure Resource Manager-mallar](template-syntax.md).
* Rekommendationer om hur du skapar variabler finns i [Metodtips - variabler](template-best-practices.md#variables).
