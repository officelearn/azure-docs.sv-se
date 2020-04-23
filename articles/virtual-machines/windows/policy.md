---
title: Framtvinga säkerhet med principer för virtuella Windows-datorer i Azure
description: Så här tillämpar du en princip på en virtuell Virtuell Azure Resource Manager-dator för Windows
services: virtual-machines-windows
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: 407e5cefe9f7f60c86de6e80133ff1b3f8b9003d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086546"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Tillämpa principer på virtuella Windows-datorer med Azure Resource Manager
Genom att använda principer kan en organisation tillämpa olika konventioner och regler i hela företaget. Verkställighet av önskat beteende kan bidra till att minska risken och samtidigt bidra till organisationens framgång. I den här artikeln beskriver vi hur du kan använda Azure Resource Manager-principer för att definiera önskat beteende för organisationens virtuella datorer.

En introduktion till principer finns i [Vad är Azure-princip?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>Tillåtna virtuella datorer
Om du vill vara säkra på att virtuella datorer för din organisation är kompatibla med ett program kan du begränsa de tillåtna operativsystemen. I följande principexempel tillåter du att endast virtuella virtuella datorer för Windows Server 2012 R2 Datacenter skapas:

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
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "MicrosoftWindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "WindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "2012-R2-Datacenter"
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

Använd ett jokertecken för att ändra föregående princip för att tillåta alla Windows Server Datacenter-avbildningar:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Använd anyOf för att ändra föregående princip för att tillåta alla Windows Server 2012 R2 Datacenter eller högre avbildning:

```json
{
  "anyOf": [
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2012-R2-Datacenter*"
    },
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2016-Datacenter*"
    }
  ]
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


## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid-förmånen

När du har en lokal licens kan du spara licensavgiften på dina virtuella datorer. När du inte har licensen bör du förbjuda alternativet. Följande princip förbjuder användning av Azure Hybrid Use Benefit (AHUB):

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in":[ "Microsoft.Compute/virtualMachines","Microsoft.Compute/VirtualMachineScaleSets"]
            },
            {
                "field": "Microsoft.Compute/licenseType",
                "exists": true
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
