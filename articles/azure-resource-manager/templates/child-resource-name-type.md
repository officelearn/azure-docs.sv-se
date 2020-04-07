---
title: Underordnade resurser i mallar
description: Beskriver hur du anger namn och typ för underordnade resurser i en Azure Resource Manager-mall.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 3a69829e674925982c618807f49433a033d8c5f9
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743830"
---
# <a name="set-name-and-type-for-child-resources"></a>Ange namn och typ för underordnade resurser

Underordnade resurser är resurser som bara finns inom ramen för en annan resurs. Ett tillägg [för virtuella datorer](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions) kan till exempel inte finnas utan en [virtuell dator](/azure/templates/microsoft.compute/2019-03-01/virtualmachines). Tilläggsresursen är underordnad den virtuella datorn.

I en Resource Manager-mall kan du ange den underordnade resursen antingen inom den överordnade resursen eller utanför den överordnade resursen. I följande exempel visas den underordnade resurs som ingår i egenskapen resurser för den överordnade resursen.

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

I nästa exempel visas den underordnade resursen utanför den överordnade resursen. Du kan använda den här metoden om den överordnade resursen inte distribueras i samma mall, eller om du vill använda [kopia](copy-resources.md) för att skapa mer än en underordnad resurs.

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

De värden som du anger för resursnamnet och resurstypen varierar beroende på om den underordnade resursen har definierats inom eller utanför den överordnade resursen.

## <a name="within-parent-resource"></a>Inom överordnad resurs

När du definieras inom den överordnade resurstypen formaterar du typ- och namnvärdena som ett enda ord utan snedstreck.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

I följande exempel visas ett virtuellt nätverk och med ett undernät. Observera att undernätet ingår i resursmatrisen för det virtuella nätverket. Namnet är inställt på **Undernät1** och typen är inställd på **undernät**. Den underordnade resursen markeras som beroende av den överordnade resursen eftersom den överordnade resursen måste finnas innan den underordnade resursen kan distribueras.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "location": "[parameters('location')]",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

Den fullständiga resurstypen är fortfarande **Microsoft.Network/virtualNetworks/subnät**. Du anger inte **Microsoft.Network/virtualNetworks/** eftersom det antas från den överordnade resurstypen.

Det underordnade resursnamnet är inställt **på Undernät1,** men det fullständiga namnet innehåller det överordnade namnet. Du anger inte **VNet1** eftersom det antas från den överordnade resursen.

## <a name="outside-parent-resource"></a>Extern överordnad resurs

När du definieras utanför den överordnade resursen formaterar du typen och med snedstreck för att inkludera den överordnade typen och namnet.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

I följande exempel visas ett virtuellt nätverk och ett undernät som båda har definierats på rotnivå. Observera att undernätet inte ingår i resursmatrisen för det virtuella nätverket. Namnet är inställt på **VNet1/Subnet1** och typen är inställd på **Microsoft.Network/virtualNetworks/undernät**. Den underordnade resursen markeras som beroende av den överordnade resursen eftersom den överordnade resursen måste finnas innan den underordnade resursen kan distribueras.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du skapar Azure Resource Manager-mallar finns i [Skapa mallar](template-syntax.md).

* Mer information om resursnamnets format när resursen refereras till finns i [referensfunktionen](template-functions-resource.md#reference).
