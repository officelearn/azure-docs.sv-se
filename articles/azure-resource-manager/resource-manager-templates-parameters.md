---
title: Azure Resource Manager parametern mallsektion | Microsoft Docs
description: Beskriver avsnittet parametrar i Azure Resource Manager-mallar med deklarativ JSON-syntax.
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
ms.date: 05/18/2018
ms.author: tomfitz
ms.openlocfilehash: 193e74d94017cf0ca8ec0600c7e5a3dc4b7a6dea
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="parameters-section-of-azure-resource-manager-templates"></a>Parametrarna-avsnittet i Azure Resource Manager-mallar
I avsnittet parametrar i mallen kan du ange vilka värden som du kan ange när du distribuerar resurserna. Dessa värden kan du anpassa distributionen med värden som är anpassade för en viss miljö (t.ex dev, test- och). Du behöver inte ange parametrarna i mallen, men utan parametrar mallen skulle distribuera alltid samma resurser med samma namn, platser och egenskaper.

Du är begränsad till 255 parametrar i en mall. Du kan minska antalet parametrar med hjälp av objekt som innehåller flera egenskaper som visas i den här artikeln.

## <a name="define-and-use-a-parameter"></a>Definiera och använder en parameter

I följande exempel visas en enkel parameterdefinition. Den definierar namnet på parametern och anger att det tar ett strängvärde. Parametern accepterar endast värden som passar dess avsedda användning. Det anger ett standardvärde om inget värde anges under distributionen. Slutligen innehåller parametern en beskrivning av dess användning. 

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

Föregående exempel visade endast några av de egenskaper som du kan använda i parameteravsnittet. De tillgängliga egenskaperna är:

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
| parameterName |Ja |Parameterns namn. Måste vara en giltig JavaScript-identifierare. |
| typ |Ja |Typ av parametervärdet. Tillåtna typer och värden är **sträng**, **securestring**, **int**, **bool**, **objektet**, **secureObject**, och **matris**. |
| Standardvärde |Nej |Standardvärdet för parametern, om inget värde har angetts för parametern. |
| allowedValues |Nej |Matris med tillåtna värden för parametern för att kontrollera att rätt värde har angetts. |
| MinValue |Nej |Det lägsta värdet för int typparametrar det här värdet är inklusiva. |
| MaxValue |Nej |Det maximala värdet för int typparametrar det här värdet är inklusiva. |
| minLength |Nej |Den minsta längden för string, securestring och array typparametrar detta värde är inklusiva. |
| maxLength |Nej |Den maximala längden för string, securestring och array typparametrar detta värde är inklusiva. |
| description |Nej |Beskrivning av den parameter som visas för användarna via portalen. |

## <a name="template-functions-with-parameters"></a>Mallfunktioner med parametrar

Du kan använda de flesta Mallfunktioner när du anger standardvärdet för en parameter. Du kan använda ett annat parametervärde för att skapa ett standardvärde. Följande mall visar hur du använder funktioner i standardvärdet:

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

Du kan inte använda den `reference` funktionen i avsnittet Parametrar. Parametrarna utvärderas före distributionen därför `reference` funktion går inte att hämta runtime-tillståndet för en resurs. 

## <a name="objects-as-parameters"></a>Objekt som parametrar

Det kan vara enklare att organisera relaterade värden genom att skicka dem i som ett objekt. Den här metoden minskar också antalet parametrar i mallen.

Definiera parametern i mallen och ange en JSON-objekt i stället för ett värde under distributionen. 

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

Sedan, referera subegenskaper för parametern med hjälp av punktoperatorn.

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
Följande information kan vara användbart när du arbetar med parametrar:

* Minimera din användning av parametrar. Använd om möjligt en variabel eller ett litteralvärde. Använd parametrarna endast för dessa scenarier:
   
   * Inställningar som du vill använda variationer av enligt miljö (SKU, storlek, kapacitet).
   * Resursnamn som du vill ange för enkel identifiering.
   * Värden som du använder ofta för att utföra andra åtgärder (till exempel en administratörsanvändarnamnet).
   * Hemligheter (till exempel lösenord).
   * Det antal eller matris med-värden ska användas när du skapar flera instanser av en resurstyp.
* Slitbanor användningsfall för parameternamn.
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

* Ange standardvärden för parametrar (förutom för lösenord och SSH-nycklar). Genom att tillhandahålla ett standardvärde, blir parametern valfria under distributionen. Standardvärdet kan vara en tom sträng. 
   
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

* Använd **securestring** för alla lösenord och hemligheter. Om du skickar känsliga data i en JSON-objekt använder du den **secureObject** typen. Mallparametrar med securestring eller secureObject typer går inte att läsa efter resurs distributionen. 
   
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

* Använder en parameter för att ange plats och dela det parametervärdet så mycket som möjligt med resurser som kan förväntas finnas på samma plats. Denna metod minimerar antalet gånger som användare uppmanas att ange platsinformation. Om en resurstyp stöds i endast ett begränsat antal platser, kanske du vill ange en giltig plats direkt i mallen, eller Lägg till en annan platsparametern. När en organisation begränsar tillåtna områden för sina användare den **resourceGroup () .location** uttryck kan förhindra att användare från att kunna distribuera mallen. Till exempel skapar en användare en resursgrupp i en region. En annan användare måste distribuera till resursgruppen men har inte åtkomst till regionen. 
   
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
    
* Undvik att använda en parameter eller variabel för API-version för en resurstyp. Egenskaper för resursen och värdena kan variera efter versionsnummer. IntelliSense i en redigerare kan inte avgöra korrekt schema när API-version anges som en parameter eller variabel. I stället hårdkoda API-version i mallen.
* Undvik att ange namnet på en parameter i mallen som matchar en parameter i kommandot distribution. Hanteraren för filserverresurser löser den här namnkonflikt genom att lägga till username@Domain **från mall** till mallparameter. Till exempel om du lägger till en parameter med namnet **ResourceGroupName** i mallen, den orsakar en konflikt med den **ResourceGroupName** parametern i den [ny AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) cmdlet. Under distributionen kan du uppmanas att ange ett värde för **ResourceGroupNameFromTemplate**.

## <a name="example-templates"></a>Exempel mallar

Mallarna exempel visar några scenarier för att använda parametrar. Distribuera dem för att testa hur parametrar ska hanteras i olika scenarier.

|Mall  |Beskrivning  |
|---------|---------|
|[parametrar med funktioner för standardvärden](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Visar hur du använder Mallfunktioner när du definierar standardvärden för parametrar. Mallen distribuerar inte några resurser. Den skapar parametervärden och returnerar dessa värden. |
|[Parameter-objekt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Visar med hjälp av ett objekt för en parameter. Mallen distribuerar inte några resurser. Den skapar parametervärden och returnerar dessa värden. |

## <a name="next-steps"></a>Nästa steg

* Om du vill visa kompletta mallar för många olika typer av lösningar kan du se [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).
* Att ange parametervärden under distributionen, se [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md). 
* Mer information om de funktioner som du kan använda från i en mall finns [Azure Resource Manager mallen Functions](resource-group-template-functions.md).
* Information om hur du använder en parameter-objekt finns i [använda ett objekt som en parameter i en Azure Resource Manager-mall](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).