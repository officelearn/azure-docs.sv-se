---
title: Parameteravsnittet för Azure Resource Manager-mall | Microsoft Docs
description: Beskriver parameters-avsnittet av Azure Resource Manager-mallar med hjälp av deklarativa JSON-syntax.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: tomfitz
ms.openlocfilehash: 83ba1b94413990c0eb8dff42c49d46456a658d5a
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50417777"
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
| minLength |Nej |Den minsta längden för string, securestring och array typparametrar det här värdet är inkluderande. |
| maxLength |Nej |Den maximala längden för string, securestring och array typparametrar det här värdet är inkluderande. |
| beskrivning |Nej |Beskrivning av den parameter som visas för användarna via portalen. |

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

## <a name="recommendations"></a>Rekommendationer
Följande information kan vara till hjälp när du arbetar med parametrar:

* Minimera din användning av parametrar. När det är möjligt använda en variabel eller ett literalvärde. Använda parametrar för endast för dessa scenarier:
   
   * Inställningar som du vill använda varianter av enligt miljö (SKU, storlek, kapacitet).
   * Resursnamn som du vill ange för enkel identifiering.
   * Värden som du använder ofta att utföra andra åtgärder (till exempel ett administratörsanvändarnamn).
   * Hemligheter (till exempel lösenord).
   * Talet eller matris med värden som ska användas när du skapar fler än en instans av en resurstyp.
* Använd kamelnotation för parameternamn.
* Ange en beskrivning av varje parameter i metadata:

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Definiera standardvärden för parametrar (förutom för lösenord och SSH-nycklar). Genom att ange ett standardvärde, blir parametern valfritt under distributionen. Standardvärdet kan vara en tom sträng. 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Använd **securestring** för alla lösenord och hemligheter. Om du skickar känsliga data i JSON-objektet, använder du den **secureObject** typen. Mallparametrar med securestring eller secureObject typer kan inte läsas efter resursdistributionen. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Använd en parameter för att ange plats och dela det parametervärdet som är så mycket som möjligt med resurser som sannolikt kommer att finnas på samma plats. Denna metod minimerar antalet gånger som användare uppmanas att ange platsinformation. Om en resurstyp som stöds i endast ett begränsat antal platser, kanske du vill ange en giltig plats direkt i mallen eller Lägg till en annan plats-parameter. När en organisation begränsar de tillåtna regionerna för dess användare, den **resourceGroup () .location** uttryck kan förhindra att en användare från att distribuera mallen. Exempelvis kan skapar en användare en resursgrupp i en region. En annan användare måste distribuera till resursgruppen men har inte åtkomst till regionen. 
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[parameters('location')]",
         ...
     }
   ]
   ```
    
* Undvik att använda en parameter eller variabel för API-versionen för en resurstyp. Egenskaper för resursen och värdena kan variera efter versionsnummer. IntelliSense i en Kodredigerare kan inte fastställa rätt schema när API-versionen anges som en parameter eller variabel. I stället hårdkoda API-versionen i mallen.
* Undvik att ange ett parameternamn i mallen som matchar en parameter i distributionskommandot. Resource Manager löser den här namnkonflikten genom att lägga till postfixen **från mall** mall-parameter. Exempel: Om du inkluderar en parameter med namnet **ResourceGroupName** i mallen, den är i konflikt med den **ResourceGroupName** parametern i den [New-AzureRmResourceGroupDeployment ](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) cmdlet. Under distributionen kan du uppmanas att ange ett värde för **ResourceGroupNameFromTemplate**.

## <a name="example-templates"></a>Exempel på mallar

Dessa exempel på mallar visar några scenarier för att använda parametrar. Distribuera dem för att testa hur parametrar ska hanteras i olika scenarier.

|Mall  |Beskrivning  |
|---------|---------|
|[parametrar med funktioner för standardvärden](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Visar hur du använder Mallfunktioner när du definierar standardvärden för parametrar. Mallen distribuerar inte några resurser. Den skapar parametervärden och returnerar dessa värden. |
|[Parametern-objekt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Visar hur du använder ett objekt för en parameter. Mallen distribuerar inte några resurser. Den skapar parametervärden och returnerar dessa värden. |

## <a name="next-steps"></a>Nästa steg

* Om du vill visa kompletta mallar för många olika typer av lösningar kan du se [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).
* Att ange parametervärden under distributionen, se [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md). 
* Mer information om de funktioner du kan använda från inom en mall finns i [Azure Resource Manager-Mallfunktioner](resource-group-template-functions.md).
* Information om hur du använder ett parametern-objekt finns i [använda ett objekt som en parameter i en Azure Resource Manager-mall](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).
