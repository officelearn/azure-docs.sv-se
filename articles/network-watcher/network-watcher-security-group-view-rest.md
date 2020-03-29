---
title: Analysera nätverkssäkerhet - Security Group View - Azure REST API
titleSuffix: Azure Network Watcher
description: I den här artikeln beskrivs hur du använder PowerShell för att analysera en säkerhet för virtuella datorer med säkerhetsgruppvyn.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: c9c76e9c06d4c45a096cff79dac82bb80ebe25d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840748"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>Analysera säkerheten för den virtuella datorn med säkerhetsgruppvyn med REST API

> [!div class="op_single_selector"]
> - [Powershell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

Säkerhetsgruppvyn returnerar konfigurerade och effektiva nätverkssäkerhetsregler som tillämpas på en virtuell dator. Den här funktionen är användbar för att granska och diagnostisera nätverkssäkerhetsgrupper och regler som är konfigurerade på en virtuell dator för att säkerställa att trafik tillåts eller nekas på rätt sätt. I den här artikeln visar vi hur du hämtar de effektiva och tillämpade säkerhetsreglerna till en virtuell dator med REST API


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Innan du börjar

I det här fallet anropar du API:et för nätverksbevakare för att hämta säkerhetsgruppvyn för en virtuell dator. ARMclient används för att anropa REST API med PowerShell. ARMClient finns på chocolatey på [ARMClient på Chocolatey](https://chocolatey.org/packages/ARMClient)

Det här scenariot förutsätter att du redan har följt stegen i [Skapa en nätverksbevakare](network-watcher-create.md) för att skapa en Network Watcher. Scenariot förutsätter också att en resursgrupp med en giltig virtuell dator finns för att användas.

## <a name="scenario"></a>Scenario

Scenariot som beskrivs i den här artikeln hämtar de effektiva och tillämpade säkerhetsreglerna för en viss virtuell dator.

## <a name="log-in-with-armclient"></a>Logga in med ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Hämta en virtuell dator

Kör följande skript för att returnera en virtuell dator Följande kodbehovsvariabler:

- **subscriptionId** - Prenumerations-ID kan också hämtas med **Cmdlet Get-AzSubscription.**
- **resourceGroupName** - Namnet på en resursgrupp som innehåller virtuella datorer.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Den information som behövs är **id** under typen `Microsoft.Compute/virtualMachines` som svar, vilket visas i följande exempel:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft
.Network/networkInterfaces/{nicName}"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Com
pute/virtualMachines/{vmName}/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute
/virtualMachines/{vmName}",
      "name": "{vmName}"
    }
  ]
}
```

## <a name="get-security-group-view-for-virtual-machine"></a>Hämta säkerhetsgruppsvy för virtuell dator

I följande exempel begär säkerhetsgruppsvyn för en riktad virtuell dator. Resultaten från det här exemplet kan användas för att jämföra med de regler och säkerhet som definieras av originering för att leta efter konfigurationsavdrift.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName

$requestBody = @"
{
    'targetResourceId': '${targetUri}'

}
"@
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/securityGroupView?api-version=2016-12-01" $requestBody -verbose
```

## <a name="view-the-response"></a>Visa svaret

Följande exempel är svaret som returneras från föregående kommando. Resultaten visar alla effektiva och tillämpade säkerhetsregler på den virtuella datorn uppdelad i grupper av **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**och **EffectiveSecurityRules**.

```json

{
  "networkInterfaces": [
    {
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "securityRules": [
            {
              "name": "default-allow-rdp",
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
              "properties": {
                "provisioningState": "Succeeded",
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 1000,
                "direction": "Inbound"
              }
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "name": "AllowVnetInBound",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/",
            "properties": {
              "provisioningState": "Succeeded",
              "description": "Allow inbound traffic from all VMs in VNET",
              "protocol": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "*",
              "sourceAddressPrefix": "VirtualNetwork",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 65000,
              "direction": "Inbound"
            }
          },
          ...
        ],
        "effectiveSecurityRules": [
          {
            "name": "DefaultOutboundDenyAll",
            "protocol": "All",
            "sourcePortRange": "0-65535",
            "destinationPortRange": "0-65535",
            "sourceAddressPrefix": "*",
            "destinationAddressPrefix": "*",
            "access": "Deny",
            "priority": 65500,
            "direction": "Outbound"
          },
          ...
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Besök [Auditing Network Security Groups (NSG) med Network Watcher](network-watcher-security-group-view-powershell.md) om du vill lära dig hur du automatiserar valideringen av nätverkssäkerhetsgrupper.


