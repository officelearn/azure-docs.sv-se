---
title: Analysera nätverkssäkerhet med Security Group View - Azure CLI
titleSuffix: Azure Network Watcher
description: I den här artikeln beskrivs hur du använder Azure CLI för att analysera en säkerhet för virtuella datorer med Security Group View.
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
ms.openlocfilehash: 73f1efc512bf031021791da8cc55bc4e7d98a812
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840783"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Analysera säkerheten för den virtuella datorn med security group view med Azure CLI

> [!div class="op_single_selector"]
> - [Powershell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

Säkerhetsgruppvyn returnerar konfigurerade och effektiva nätverkssäkerhetsregler som tillämpas på en virtuell dator. Den här funktionen är användbar för att granska och diagnostisera nätverkssäkerhetsgrupper och regler som är konfigurerade på en virtuell dator för att säkerställa att trafik tillåts eller nekas på rätt sätt. I den här artikeln visar vi hur du hämtar de konfigurerade och effektiva säkerhetsreglerna till en virtuell dator med Azure CLI

Om du vill utföra stegen i den här artikeln måste du [installera Azure-kommandoradsgränssnittet för Mac, Linux och Windows (CLI).](/cli/azure/install-azure-cli)

## <a name="before-you-begin"></a>Innan du börjar

Det här scenariot förutsätter att du redan har följt stegen i [Skapa en nätverksbevakare](network-watcher-create.md) för att skapa en Network Watcher.

## <a name="scenario"></a>Scenario

Scenariot som beskrivs i den här artikeln hämtar de konfigurerade och effektiva säkerhetsreglerna för en viss virtuell dator.

## <a name="get-a-vm"></a>Skaffa en virtuell dator

En virtuell dator krävs `vm list` för att köra cmdleten. Följande kommando visar de virtuella datorerna i en resursgrupp:

```azurecli
az vm list -resource-group resourceGroupName
```

När du känner till den `vm show` virtuella datorn kan du använda cmdlet för att få dess resurs-ID:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Hämta säkerhetsgruppsvyn

Nästa steg är att hämta resultatet för säkerhetsgruppvyn.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Visa resultaten

Följande exempel är ett förkortat svar på de resultat som returneras. Resultaten visar alla effektiva och tillämpade säkerhetsregler på den virtuella datorn uppdelad i grupper av **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**och **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
      "resourceGroup": "{resourceGroupName}",
      "securityRuleAssociations": {
        "defaultSecurityRules": [
          {
            "access": "Allow",
            "description": "Allow inbound traffic from all VMs in VNET",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "*",
            "direction": "Inbound",
            "etag": null,
            "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups//providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/AllowVnetInBound",
            "name": "AllowVnetInBound",
            "priority": 65000,
            "protocol": "*",
            "provisioningState": "Succeeded",
            "resourceGroup": "",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "*"
          }...
        ],
        "effectiveSecurityRules": [
          {
            "access": "Deny",
            "destinationAddressPrefix": "*",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": null,
            "expandedSourceAddressPrefix": null,
            "name": "DefaultOutboundDenyAll",
            "priority": 65500,
            "protocol": "All",
            "sourceAddressPrefix": "*",
            "sourcePortRange": "0-65535"
          },
          {
            "access": "Allow",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "expandedSourceAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "name": "DefaultRule_AllowVnetOutBound",
            "priority": 65000,
            "protocol": "All",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "0-65535"
          },...
        ],
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "resourceGroup": "{resourceGroupName}",
          "securityRules": [
            {
              "access": "Allow",
              "description": null,
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389",
              "direction": "Inbound",
              "etag": "W/\"efb606c1-2d54-475a-ab20-da3f80393577\"",
              "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "name": "default-allow-rdp",
              "priority": 1000,
              "protocol": "TCP",
              "provisioningState": "Succeeded",
              "resourceGroup": "{resourceGroupName}",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*"
            }
          ]
        },
        "subnetAssociation": null
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Besök [Auditing Network Security Groups (NSG) med Network Watcher](network-watcher-nsg-auditing-powershell.md) om du vill lära dig hur du automatiserar valideringen av nätverkssäkerhetsgrupper.

Läs mer om de säkerhetsregler som tillämpas på nätverksresurserna genom att besöka [översikt över säkerhetsgruppvyn](network-watcher-security-group-view-overview.md)
