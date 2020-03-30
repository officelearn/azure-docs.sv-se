---
title: Parametrar i mallar
description: Beskriver hur du definierar parametrar i en Azure Resource Manager-mall.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 89c6984c587e8dae59c1825a99d4f8da1c06dafb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122431"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Parametrar i Azure Resource Manager-mallar

I den här artikeln beskrivs hur du definierar och använder parametrar i din Azure Resource Manager-mall. Genom att ange olika värden för parametrar kan du återanvända en mall för olika miljöer.

Resource Manager löser parametervärden innan distributionsåtgärderna startas. Var parametern än används i mallen ersätter Resource Manager den med det lösta värdet.

## <a name="define-parameter"></a>Definiera parameter

I följande exempel visas en enkel parameterdefinition. Den definierar en parameter med namnet **storageSKU**. Parametern är ett strängvärde och accepterar endast värden som är giltiga för den avsedda användningen. Parametern använder ett standardvärde när inget värde anges under distributionen.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }
}
```

## <a name="use-parameter"></a>Använd parameter

I mallen refererar du till parameterns värde med hjälp av [parameterfunktionen.](template-functions-deployment.md#parameters) I följande exempel används parametervärdet för att ange SKU för lagringskontot.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="template-functions"></a>Mallfunktioner

När du anger standardvärdet för en parameter kan du använda de flesta mallfunktioner. Du kan använda ett annat parametervärde för att skapa ett standardvärde. Följande mall visar hur funktioner används i standardvärdet. När inget namn anges för platsen skapas ett unikt strängvärde och läggs till den på **plats**. När inget namn anges för värdplanen tar den värdet för platsen och lägger till **-plan**.

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Du kan inte använda [referensfunktionen](template-functions-resource.md#reference) eller någon av [listfunktionerna](template-functions-resource.md#list) i avsnittet parametrar. Dessa funktioner får körningstillståndet för en resurs och kan inte köras före distributionen när parametrarna har lösts.

## <a name="objects-as-parameters"></a>Objekt som parametrar

Det kan vara enklare att ordna relaterade värden genom att skicka in dem som ett objekt. Den här metoden minskar också antalet parametrar i mallen.

I följande exempel visas en parameter som är ett objekt. Standardvärdet visar de förväntade egenskaperna för objektet.

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Du refererar till objektets egenskaper med hjälp av punktoperatorn.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2015-06-15",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="example-templates"></a>Exempel på mallar

Följande exempel visar scenarier för att använda parametrar.

|Mall  |Beskrivning  |
|---------|---------|
|[parametrar med funktioner för standardvärden](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Visar hur du använder mallfunktioner när du definierar standardvärden för parametrar. Mallen distribuerar inga resurser. Parametervärden och returnerar dessa värden. |
|[parameterobjekt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Demonstrerar med hjälp av ett objekt för en parameter. Mallen distribuerar inga resurser. Parametervärden och returnerar dessa värden. |


## <a name="next-steps"></a>Nästa steg

* Mer information om tillgängliga egenskaper för parametrar finns i [Förstå strukturen och syntaxen för Azure Resource Manager-mallar](template-syntax.md).
* Mer information om hur du skickar in parametervärden som en fil finns i [Skapa Resource Manager-parameterfil](parameter-files.md).
* Rekommendationer om hur du skapar parametrar finns i [Metodtips - parametrar](template-best-practices.md#parameters).
