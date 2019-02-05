---
title: Parameteravsnittet för Azure Resource Manager-mall | Microsoft Docs
description: Beskriver parameters-avsnittet av Azure Resource Manager-mallar med hjälp av deklarativa JSON-syntax.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2019
ms.author: tomfitz
ms.openlocfilehash: dc817302ab39d12ccd1d1a20d4dd72f94352c796
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55695685"
---
# <a name="parameters-section-of-azure-resource-manager-templates"></a>Parameteravsnittet av Azure Resource Manager-mallar
I avsnittet parametrar i mallen kan du ange vilka värden som du kan ange när du distribuerar resurser. Dessa parametervärden kan du anpassa distributionen genom att tillhandahålla värden som är skräddarsydda för en viss miljö (till exempel utveckling, testning och produktion). Du behöver inte ange parametrar i mallen, men utan parametrar mallen distribuerar alltid samma resurser med samma namn, platser och egenskaper.

Du är begränsad till 256 parametrar i en mall. Du kan minska antalet parametrar av objekt som innehåller flera egenskaper som visas i den här artikeln.

## <a name="define-and-use-a-parameter"></a>Definiera och Använd en parameter

I följande exempel visar en enkel parameterdefinition. Den definierar namnet på parametern och anger att det tar ett strängvärde. Parametern accepterar endast värden som passar för att fungera. Den anger ett standardvärde om inget värde anges under distributionen. Slutligen innehåller parametern en beskrivning av dess användning. 

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

I mallen referera du till värdet för parametern med följande syntax:

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

## <a name="available-properties"></a>Tillgängliga egenskaper

I föregående exempel visade endast vissa av egenskaperna som du kan använda i parameteravsnittet. Tillgängliga egenskaper är:

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": "<default-value-of-parameter>",
        "allowedValues": [ "<array-of-allowed-values>" ],
        "minValue": <minimum-value-for-int>,
        "maxValue": <maximum-value-for-int>,
        "minLength": <minimum-length-for-string-or-array>,
        "maxLength": <maximum-length-for-string-or-array-parameters>,
        "metadata": {
            "description": "<description-of-the parameter>" 
        }
    }
}
```

| Elementnamn | Krävs | Beskrivning |
|:--- |:--- |:--- |
| parameterName |Ja |Namnet på parametern. Måste vara en giltig JavaScript-identifierare. |
| typ |Ja |Typ av parametervärdet. Tillåtna typer och värden är **sträng**, **securestring**, **int**, **bool**, **objekt**, **secureObject**, och **matris**. |
| Standardvärde |Nej |Standardvärdet för parametern, om inget värde har angetts för parametern. |
| allowedValues |Nej |Matris med tillåtna värden för parametern för att se till att rätt värde har angetts. |
| minValue |Nej |Det minsta värdet för parametrar av typen int det här värdet är inkluderande. |
| maxValue |Nej |Det maximala värdet för parametrar av typen int det här värdet är inkluderande. |
| minLength |Nej |Den minsta längden för string, säker sträng och matris typparametrar det här värdet är inkluderande. |
| maxLength |Nej |Den maximala längden för string, säker sträng och matris typparametrar det här värdet är inkluderande. |
| beskrivning |Nej |Beskrivning av den parameter som visas för användarna via portalen. Mer information finns i [kommentarer i mallar](resource-group-authoring-templates.md#comments). |

## <a name="template-functions-with-parameters"></a>Mallfunktioner med parametrar

När du anger standardvärdet för en parameter, kan du använda de flesta Mallfunktioner. Du kan använda ett annat parametervärde för att skapa ett standardvärde. Följande mall visar hur du använder funktioner i standardvärdet:

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

Du kan inte använda den `reference` funktionen i parameteravsnittet. Parametrar utvärderas före distributionen så `reference` funktionen kan inte hämta runtime-tillståndet för en resurs. 

## <a name="objects-as-parameters"></a>Objekt som parametrar

Det kan vara enklare att organisera relaterade värden genom att skicka dem i som ett-objekt. Den här metoden minskar också antalet parametrar i mallen.

Definiera parametern i mallen och ange ett JSON-objekt i stället för ett enda värde under distributionen. 

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

Sedan referera subegenskaper för parametern genom att använda punktoperatorn.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
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

Dessa exempel på mallar visar några scenarier för att använda parametrar. Distribuera dem för att testa hur parametrar ska hanteras i olika scenarier.

|Mall  |Beskrivning  |
|---------|---------|
|[parametrar med funktioner för standardvärden](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Visar hur du använder Mallfunktioner när du definierar standardvärden för parametrar. Mallen distribuerar inte några resurser. Den skapar parametervärden och returnerar dessa värden. |
|[Parametern-objekt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Visar hur du använder ett objekt för en parameter. Mallen distribuerar inte några resurser. Den skapar parametervärden och returnerar dessa värden. |

## <a name="next-steps"></a>Nästa steg

* Om du vill visa kompletta mallar för många olika typer av lösningar kan du se [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).
* Att ange parametervärden under distributionen, se [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md). 
* Rekommendationer om hur du skapar mallar finns i [Metodtips för Azure Resource Manager-mall](template-best-practices.md).
* Information om hur du använder ett parametern-objekt finns i [använda ett objekt som en parameter i en Azure Resource Manager-mall](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).
