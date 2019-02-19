---
title: Framtvinga säkerhet med principer på virtuella Linux-datorer i Azure | Microsoft Docs
description: Hur du använder en princip till en Azure Resource Manager Linux-dator
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 06778ab4-f8ff-4eed-ae10-26a276fc3faa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: singhkay
ms.openlocfilehash: 8ad1bf371c5d5dbcbf3657ad69eace2003a8dda9
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342028"
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Tillämpa principer för virtuella Linux-datorer med Azure Resource Manager
Med hjälp av principer kan en organisation tillämpa olika konventioner och regler i hela företaget. Efterlevnad av önskat beteende kan du minimera risken när bidrar till framgång för organisationen. I den här artikeln beskriver vi hur du kan använda Azure Resource Manager-principer för att definiera önskat beteende för virtuella datorer i din organisation.

En introduktion till principer finns i [vad är Azure Policy?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>Tillåtna virtuella datorer
För att säkerställa att virtuella datorer för din organisation är kompatibla med ett program, kan du begränsa de tillåtna operativsystem. I exemplet nedan principen Tillåt endast Ubuntu 14.04.2-LTS virtuella datorer som ska skapas.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Du kan använda jokertecken för att ändra den föregående principen för att tillåta en Ubuntu LTS-avbildning: 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

Information om principfält finns i [princip alias](../../governance/policy/concepts/definition-structure.md#aliases).

## <a name="managed-disks"></a>Hanterade diskar

Använd följande princip för att kräva användning av hanterade diskar:

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>Avbildningar av virtuella datorer

Av säkerhetsskäl kan du kräva att bara godkända anpassade avbildningar har distribuerats i din miljö. Du kan ange antingen den resursgrupp som innehåller de godkända bilderna eller specifikt godkända bilder.

I följande exempel kräver bilder från en godkänd resursgrupp:

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

I följande exempel anger godkända avbildning-ID: N:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Tillägg för virtuell dator

Du kanske vill förbjuda användningen av vissa typer av tillägg. Ett tillägg kan till exempel inte kompatibel med vissa anpassade VM-avbildningar. I följande exempel visas hur du blockerar ett visst tillägg. Den använder utgivaren och typen för att avgöra vilka tillägg som ska blockeras.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="next-steps"></a>Nästa steg
* När du har definierat en regel (som visas i föregående exempel), måste du skapa principdefinitionen och tilldela den till ett omfång. Omfånget kan vara en prenumeration, resursgrupp eller resurs. Om du vill tilldela principer, se [Använd Azure portal för att tilldela och hantera resursprinciper](../../governance/policy/assign-policy-portal.md), [Använd PowerShell för att tilldela principer](../../governance/policy/assign-policy-powershell.md), eller [används Azure CLI för att tilldela principer](../../governance/policy/assign-policy-azurecli.md).
* Läs en introduktion till resursprinciper [vad är Azure Policy?](../../governance/policy/overview.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance).
