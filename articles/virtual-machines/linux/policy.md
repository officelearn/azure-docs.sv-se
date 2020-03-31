---
title: Framtvinga säkerhet med principer för virtuella Linux-datorer i Azure
description: Så här tillämpar du en princip på en virtuell Virtuell Azure Resource Manager Linux-dator
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 06778ab4-f8ff-4eed-ae10-26a276fc3faa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 08/02/2017
ms.author: kasing
ms.openlocfilehash: 7ab48430ae4d6585c908b53017122096175abac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035322"
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Tillämpa principer på virtuella Linux-datorer med Azure Resource Manager
Genom att använda principer kan en organisation tillämpa olika konventioner och regler i hela företaget. Verkställighet av önskat beteende kan bidra till att minska risken och samtidigt bidra till organisationens framgång. I den här artikeln beskriver vi hur du kan använda Azure Resource Manager-principer för att definiera önskat beteende för organisationens virtuella datorer.

En introduktion till principer finns i [Vad är Azure-princip?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>Tillåtna virtuella datorer
Om du vill vara säkra på att virtuella datorer för din organisation är kompatibla med ett program kan du begränsa de tillåtna operativsystemen. I följande principexempel tillåter du att endast Ubuntu 14.04.2-LTS-virtuella datorer skapas.

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

Använd ett jokertecken för att ändra föregående princip så att en Ubuntu LTS-avbildning kan tillåtas: 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

Information om principfält finns i [Principalias](../../governance/policy/concepts/definition-structure.md#aliases).

## <a name="managed-disks"></a>Hanterade diskar

Om du vill kräva användning av hanterade diskar använder du följande princip:

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

## <a name="images-for-virtual-machines"></a>Avbildningar för virtuella datorer

Av säkerhetsskäl kan du kräva att endast godkända anpassade avbildningar distribueras i din miljö. Du kan ange antingen resursgruppen som innehåller de godkända avbildningarna eller de specifika godkända avbildningarna.

I följande exempel krävs bilder från en godkänd resursgrupp:

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

I följande exempel anges de godkända bild-ID:erna:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Tillägg för virtuella datorer

Du kanske vill förbjuda användning av vissa typer av tillägg. Ett tillägg kanske till exempel inte är kompatibelt med vissa anpassade avbildningar för virtuella datorer. I följande exempel visas hur du blockerar ett visst tillägg. Den använder utgivare och typ för att avgöra vilket tillägg som ska blockeras.

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
* När du har definierat en principregel (som visas i föregående exempel) måste du skapa principdefinitionen och tilldela den till ett scope. Scopet kan vara en prenumeration, resursgrupp eller resurs. Information om hur du tilldelar principer finns i [Använda Azure-portal för att tilldela och hantera resursprinciper](../../governance/policy/assign-policy-portal.md), [Använda PowerShell för att tilldela principer](../../governance/policy/assign-policy-powershell.md)eller Använda Azure CLI för att tilldela [principer](../../governance/policy/assign-policy-azurecli.md).
* En introduktion till resursprinciper finns i [Vad är Azure-princip?](../../governance/policy/overview.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance).
